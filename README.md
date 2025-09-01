interface TokenResponse {
  access_token: string;
  token_type: string;
  expires_in: number;
  refresh_token?: string;
}

class AuthService {
  private readonly REDIRECT_URI = import.meta.env.VITE_REDIRECT_URI;
  private readonly CLIENT_ID = import.meta.env.VITE_CLIENT_ID;

  constructor() {
    console.log('AuthService constructor - Variables de entorno:');
    console.log('VITE_REDIRECT_URI:', import.meta.env.VITE_REDIRECT_URI);
    console.log('VITE_CLIENT_ID:', import.meta.env.VITE_CLIENT_ID);
  }

  /**
   * Obtiene el token JWT usando el código de autorización
   * Usa el proxy configurado en Vite para evitar problemas de CORS y certificados
   */
  async getJWTToken(code: string): Promise<TokenResponse> {
    console.log('Obteniendo token JWT con código:', code);
    
    if (!this.REDIRECT_URI) {
      throw new Error('Configuración de OAuth incompleta. Verifique VITE_REDIRECT_URI.');
    }

    const params = new URLSearchParams({
      grant_type: 'authorization_code',
      code: code,
      redirect_uri: this.REDIRECT_URI
    });

    // Usar la ruta del proxy en lugar de la URL completa
    const proxyUrl = `/oauth/token?${params.toString()}`;
    console.log('URL del proxy para OAuth:', proxyUrl);

    try {
      // Cambiar a GET temporalmente para probar
      const response = await fetch(proxyUrl, {
        method: 'GET', // Cambiado de POST a GET para probar
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
          'true-client-ip': '0.0.0.0',
          'oauth_type': 'iam',
          'Authorization': 'Basic ' + btoa('webtools:webtools')
        },
        // Configuración para manejar certificados SSL
        mode: 'cors',
        credentials: 'include'
      });

      console.log('Respuesta del servidor:', response.status, response.statusText);

      if (!response.ok) {
        const errorText = await response.text();
        console.error('Error en la respuesta:', errorText);
        throw new Error(`Error al obtener token: ${response.status} ${response.statusText}`);
      }

      const tokenData: TokenResponse = await response.json();
      console.log('Token obtenido exitosamente');
      
      // Guardar el token en localStorage
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al obtener el token JWT:', error);
      throw error;
    }
  }

  /**
   * Renueva el token usando el refresh token
   * Usa el proxy configurado en Vite para evitar problemas de CORS y certificados
   */
  async refreshToken(): Promise<TokenResponse | null> {
    console.log('Intentando renovar token...');
    
    if (!this.CLIENT_ID) {
      console.error('Configuración de refresh token incompleta. Verifique VITE_CLIENT_ID.');
      return null;
    }

    const refreshToken = localStorage.getItem('refresh_token');
    if (!refreshToken) {
      console.warn('No hay refresh token disponible');
      return null;
    }

    const params = new URLSearchParams({
      grant_type: 'refresh_token',
      client_id: this.CLIENT_ID,
      refresh_token: refreshToken
    });

    try {
      const currentToken = this.getStoredToken();
      const headers: Record<string, string> = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'oauth_type': 'iam'
      };

      // Agregar el token actual como Bearer si existe
      if (currentToken) {
        headers['Authorization'] = `Bearer ${currentToken}`;
      }

      // Usar la ruta del proxy en lugar de la URL completa
      const proxyUrl = `/api/oauth/token?${params.toString()}`;
      console.log('URL del proxy para refresh:', proxyUrl);

      const response = await fetch(proxyUrl, {
        method: 'POST',
        headers,
        // Configuración para manejar certificados SSL
        mode: 'cors',
        credentials: 'include'
      });

      console.log('Respuesta del servidor (refresh):', response.status, response.statusText);

      if (!response.ok) {
        const errorText = await response.text();
        console.error('Error al renovar token:', errorText);
        // Si falla el refresh, limpiar tokens
        this.clearTokens();
        return null;
      }

      const tokenData: TokenResponse = await response.json();
      console.log('Token renovado exitosamente');
      
      // Guardar el nuevo token
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al renovar el token:', error);
      this.clearTokens();
      return null;
    }
  }

  /**
   * Guarda el token en localStorage
   */
  private saveToken(tokenData: TokenResponse): void {
    localStorage.setItem('access_token', tokenData.access_token);
    localStorage.setItem('token_type', tokenData.token_type);
    localStorage.setItem('token_expires_in', tokenData.expires_in.toString());
    
    if (tokenData.refresh_token) {
      localStorage.setItem('refresh_token', tokenData.refresh_token);
    }
    
    // Guardar la fecha de expiración
    const expiresAt = new Date().getTime() + (tokenData.expires_in * 1000);
    localStorage.setItem('token_expires_at', expiresAt.toString());
    
    console.log('Token guardado en localStorage');
  }

  /**
   * Obtiene el token almacenado
   */
  getStoredToken(): string | null {
    return localStorage.getItem('access_token');
  }

  /**
   * Verifica si el token está expirado
   */
  isTokenExpired(): boolean {
    const expiresAt = localStorage.getItem('token_expires_at');
    if (!expiresAt) return true;
    
    const isExpired = new Date().getTime() > parseInt(expiresAt);
    if (isExpired) {
      console.log('Token expirado');
    }
    return isExpired;
  }

  /**
   * Obtiene el token con el tipo para usar en headers
   */
  getAuthHeader(): string | null {
    const token = this.getStoredToken();
    const tokenType = localStorage.getItem('token_type') || 'Bearer';
    
    if (!token || this.isTokenExpired()) {
      return null;
    }
    
    return `${tokenType} ${token}`;
  }

  /**
   * Obtiene el token con renovación automática si es necesario
   */
  async getValidToken(): Promise<string | null> {
    if (!this.isTokenExpired()) {
      return this.getStoredToken();
    }

    console.log('Token expirado, intentando renovar...');
    // Token expirado, intentar renovar
    const refreshedToken = await this.refreshToken();
    return refreshedToken ? refreshedToken.access_token : null;
  }

  /**
   * Limpia todos los tokens almacenados
   */
  clearTokens(): void {
    localStorage.removeItem('access_token');
    localStorage.removeItem('token_type');
    localStorage.removeItem('token_expires_in');
    localStorage.removeItem('token_expires_at');
    localStorage.removeItem('refresh_token');
    console.log('Tokens limpiados del localStorage');
  }

  /**
   * Verifica si el usuario está autenticado
   */
  isAuthenticated(): boolean {
    const token = this.getStoredToken();
    const authenticated = token !== null && !this.isTokenExpired();
    console.log('Usuario autenticado:', authenticated);
    return authenticated;
  }

}

export default new AuthService();
