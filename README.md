import ConfigService from './ConfigService';

interface TokenResponse {
  access_token: string;
  token_type: string;
  expires_in: number;
  refresh_token?: string;
}

class AuthService {
  private redirectUri: string | undefined = import.meta.env.VITE_REDIRECT_URI;
  private clientId: string | undefined = import.meta.env.VITE_CLIENT_ID;

  constructor() {
    console.log('AuthService: Constructor iniciado');
    console.log('AuthService: Valores iniciales desde import.meta.env:');
    console.log('  - VITE_REDIRECT_URI:', this.redirectUri);
    console.log('  - VITE_CLIENT_ID:', this.clientId);
    
    // Cargar valores también desde param.json si existe
    ConfigService.get('VITE_REDIRECT_URI', this.redirectUri).then(v => {
      this.redirectUri = v;
      console.log('AuthService: VITE_REDIRECT_URI actualizado a:', v);
    });
    ConfigService.get('VITE_CLIENT_ID', this.clientId).then(v => {
      this.clientId = v;
      console.log('AuthService: VITE_CLIENT_ID actualizado a:', v);
    });
  }

  /**
   * Obtiene el token JWT usando el código de autorización
   * Usa el proxy configurado en Vite para evitar problemas de CORS y certificados
   */
  async getJWTToken(code: string): Promise<TokenResponse> {
    console.log('AuthService: Obteniendo token JWT con código:', code);
    console.log('AuthService: redirectUri actual:', this.redirectUri);
    
    // Esperar a que ConfigService termine de cargar si es necesario
    if (!this.redirectUri) {
      console.log('AuthService: redirectUri no disponible, esperando carga de ConfigService...');
      this.redirectUri = await ConfigService.get('VITE_REDIRECT_URI', import.meta.env.VITE_REDIRECT_URI);
      this.clientId = await ConfigService.get('VITE_CLIENT_ID', import.meta.env.VITE_CLIENT_ID);
      console.log('AuthService: Valores actualizados desde ConfigService:', {
        redirectUri: this.redirectUri,
        clientId: this.clientId
      });
    }
    
    if (!this.redirectUri) {
      console.error('AuthService: VITE_REDIRECT_URI no está configurado');
      throw new Error('Configuración de OAuth incompleta. Verifique VITE_REDIRECT_URI.');
    }

  
    const params = new URLSearchParams({
      grant_type: 'authorization_code',
      code: code,
      redirect_uri: this.redirectUri
    });

    try {
      const endpoint = await ConfigService.get('VITE_JWT_ENDPOINT', import.meta.env.VITE_JWT_ENDPOINT);
      if (!endpoint) {
        console.error('AuthService: VITE_JWT_ENDPOINT no está configurado');
        throw new Error('Configuración de OAuth incompleta. Verifique VITE_JWT_ENDPOINT.');
      }
      console.log('AuthService: Endpoint OAuth:', endpoint);
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
      console.log('Estructura del token recibido:', JSON.stringify(tokenData, null, 2));
      
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al obtener el token JWT:', error);
      throw error;
    }
  }

  async refreshToken(): Promise<TokenResponse | null> {
    // Esperar a que ConfigService termine de cargar si es necesario
    if (!this.clientId) {
      console.log('AuthService: clientId no disponible, esperando carga de ConfigService...');
      this.clientId = await ConfigService.get('VITE_CLIENT_ID', import.meta.env.VITE_CLIENT_ID);
      console.log('AuthService: clientId actualizado desde ConfigService:', this.clientId);
    }
    
    if (!this.clientId) {
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
      client_id: this.clientId,
      refresh_token: refreshToken
    });

    try {
      const headers: Record<string, string> = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'true-client-ip': '0.0.0.0',
        'oauth_type': 'iam',
        'Authorization': 'Basic ' + btoa('webtools:webtools')
      };

      const endpoint = await ConfigService.get('VITE_REFRESH_ENDPOINT', import.meta.env.VITE_REFRESH_ENDPOINT);
      if (!endpoint) {
        console.warn('VITE_REFRESH_ENDPOINT no configurado');
        this.clearTokens();
        return null;
      }
      console.log('Endpoint refresh:', endpoint);
      console.log('Parámetros refresh:', bodyParams.toString());

      const response = await fetch(endpoint, {
        method: 'POST',
        headers,
        body: bodyParams.toString(),
        mode: 'cors',
        credentials: 'include'
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
    // Validar que los campos requeridos existan
    if (!tokenData.access_token) {
      console.error('Token no contiene access_token');
      return;
    }

    // Usar 'Bearer' como token_type por defecto si no viene en la respuesta
    const tokenType = tokenData.token_type || 'Bearer';
    console.log('Token type:', tokenType);

    localStorage.setItem('access_token', tokenData.access_token);
    localStorage.setItem('token_type', tokenType);
    
    
    if (tokenData.expires_in !== undefined && tokenData.expires_in !== null) {
      localStorage.setItem('token_expires_in', tokenData.expires_in.toString());
      
     
      const expiresAt = new Date().getTime() + (tokenData.expires_in * 1000);
      localStorage.setItem('token_expires_at', expiresAt.toString());
      console.log('Fecha de expiración calculada:', new Date(expiresAt).toISOString());
    } else {
      console.warn('Token no contiene expires_in, no se puede calcular expiración');
   
      const defaultExpiresIn = 3600; 
      localStorage.setItem('token_expires_in', defaultExpiresIn.toString());
      const expiresAt = new Date().getTime() + (defaultExpiresIn * 1000);
      localStorage.setItem('token_expires_at', expiresAt.toString());
      console.log('Usando expiración por defecto (1 hora)');
    }
    

    if (tokenData.refresh_token) {
      localStorage.setItem('refresh_token', tokenData.refresh_token);
      console.log('Refresh token guardado');
    } else {
      console.log('No hay refresh token disponible');
    }
    
    console.log('Token guardado en localStorage exitosamente');
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
    const currentToken = this.getStoredToken();
    
    // Si no hay token guardado, no se puede hacer nada
    if (!currentToken) {
      console.warn('No hay token de acceso guardado');
      return null;
    }

    // Si el token no está expirado, devolverlo
    if (!this.isTokenExpired()) {
      return currentToken;
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
