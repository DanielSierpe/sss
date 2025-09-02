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

    try {
      const endpoint = import.meta.env.VITE_JWT_ENDPOINT;
      console.log('Endpoint OAuth:', endpoint);
      console.log('Parámetros:', params.toString());
      
      const response = await fetch(endpoint, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
          'true-client-ip': '0.0.0.0',
          'oauth_type': 'iam',
          'Authorization': 'Basic ' + btoa('webtools:webtools')
        },
        body: params.toString(),
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
      
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al obtener el token JWT:', error);
      throw error;
    }
  }

  async refreshToken(): Promise<TokenResponse | null> {
    if (!this.CLIENT_ID) {
      console.warn('No hay client_id configurado');
      return null;
    }

    const refreshToken = localStorage.getItem('refresh_token');
    if (!refreshToken) {
      console.warn('No hay refresh token disponible');
      return null;
    }

    const bodyParams = new URLSearchParams({
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

      if (currentToken) {
        headers['Authorization'] = `Bearer ${currentToken}`;
      }

      const endpoint = import.meta.env.VITE_REFRESH_ENDPOINT;
      console.log('Endpoint refresh:', endpoint);
      console.log('Parámetros refresh:', bodyParams.toString());

      const response = await fetch(endpoint, {
        method: 'POST',
        headers,
        credentials: 'include',
        body: bodyParams.toString(),
      });

      console.log('Respuesta del servidor (refresh):', response.status, response.statusText);

      if (!response.ok) {
        const errorText = await response.text();
        console.error('Error al renovar token:', errorText);
        this.clearTokens();
        return null;
      }

      const tokenData: TokenResponse = await response.json();
      console.log('Token renovado exitosamente');
      
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al renovar el token:', error);
      this.clearTokens();
      return null;
    }
  }

  private saveToken(tokenData: TokenResponse): void {
    localStorage.setItem('access_token', tokenData.access_token);
    localStorage.setItem('token_type', tokenData.token_type);
    localStorage.setItem('token_expires_in', tokenData.expires_in.toString());
    
    if (tokenData.refresh_token) {
      localStorage.setItem('refresh_token', tokenData.refresh_token);
    }
    
    const expiresAt = new Date().getTime() + (tokenData.expires_in * 1000);
    localStorage.setItem('token_expires_at', expiresAt.toString());
    
    console.log('Token guardado en localStorage');
  }

  getStoredToken(): string | null {
    return localStorage.getItem('access_token');
  }

  isTokenExpired(): boolean {
    const expiresAt = localStorage.getItem('token_expires_at');
    if (!expiresAt) return true;
    
    const isExpired = new Date().getTime() > parseInt(expiresAt);
    if (isExpired) {
      console.log('Token expirado');
    }
    return isExpired;
  }

  getAuthHeader(): string | null {
    const token = this.getStoredToken();
    const tokenType = localStorage.getItem('token_type') || 'Bearer';
    
    if (!token || this.isTokenExpired()) {
      return null;
    }
    
    return `${tokenType} ${token}`;
  }

  async getValidToken(): Promise<string | null> {
    if (!this.isTokenExpired()) {
      return this.getStoredToken();
    }

    console.log('Token expirado, intentando renovar...');
    const refreshedToken = await this.refreshToken();
    return refreshedToken ? refreshedToken.access_token : null;
  }

  clearTokens(): void {
    localStorage.removeItem('access_token');
    localStorage.removeItem('token_type');
    localStorage.removeItem('token_expires_in');
    localStorage.removeItem('token_expires_at');
    localStorage.removeItem('refresh_token');
    console.log('Tokens limpiados del localStorage');
  }

  isAuthenticated(): boolean {
    const token = this.getStoredToken();
    const authenticated = token !== null && !this.isTokenExpired();
    console.log('Usuario autenticado:', authenticated);
    return authenticated;
  }

}

export default new AuthService();
