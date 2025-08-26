import { useEffect, useState } from 'react';
import AuthService from '../services/AuthService';

interface UseAuthReturn {
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
  token: string | null;
  handleAuthCode: (code: string) => Promise<void>;
  logout: () => void;
}

export const useAuth = (): UseAuthReturn => {
  const [isAuthenticated, setIsAuthenticated] = useState<boolean>(false);
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);
  const [token, setToken] = useState<string | null>(null);

  // Verificar autenticación al cargar
  useEffect(() => {
    const checkAuth = () => {
      const isAuth = AuthService.isAuthenticated();
      const storedToken = AuthService.getStoredToken();
      
      setIsAuthenticated(isAuth);
      setToken(storedToken);
    };

    checkAuth();
  }, []);

  // Detectar código de autorización en la URL
  useEffect(() => {
    const detectAuthCode = async () => {
      try {
        const url = new URL(window.location.href);
        const code = url.searchParams.get('code');
        
        if (code) {
          console.log('Código de autorización detectado:', code);
          await handleAuthCode(code);
          
          // Limpiar la URL después de procesar el código
          const newUrl = new URL(window.location.href);
          newUrl.searchParams.delete('code');
          window.history.replaceState({}, document.title, newUrl.pathname + newUrl.search);
        }
      } catch (error) {
        console.error('Error al detectar código de autorización:', error);
        setError('Error al procesar la autenticación');
      }
    };

    detectAuthCode();
  }, []);

  const handleAuthCode = async (code: string): Promise<void> => {
    setIsLoading(true);
    setError(null);
    
    try {
      const tokenData = await AuthService.getJWTToken(code);
      setIsAuthenticated(true);
      setToken(tokenData.access_token);
      console.log('Token obtenido exitosamente');
    } catch (error) {
      console.error('Error al obtener token:', error);
      setError(error instanceof Error ? error.message : 'Error al obtener el token');
      setIsAuthenticated(false);
      setToken(null);
    } finally {
      setIsLoading(false);
    }
  };

  const logout = (): void => {
    AuthService.clearTokens();
    setIsAuthenticated(false);
    setToken(null);
    setError(null);
  };

  return {
    isAuthenticated,
    isLoading,
    error,
    token,
    handleAuthCode,
    logout
  };
};
