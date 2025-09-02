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
      redirect_uri: this.REDIRECT_URI,
      client_id: this.CLIENT_ID
    });

    // Probar diferentes rutas del proxy con POST
    const proxyUrls = [
      `/oauth/token`,           // Ruta original
      `/api/oauth/token`,      // Con prefijo api
      `/oauth`,                // Sin /token
      `/auth/token`            // Con prefijo auth
    ];

    for (let i = 0; i < proxyUrls.length; i++) {
      const proxyUrl = proxyUrls[i];
      console.log(`Intentando ruta ${i + 1}: ${proxyUrl}`);
      
      try {
        // Probar POST con parámetros en query string
        let response = await fetch(`${proxyUrl}?${params.toString()}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
            'true-client-ip': '0.0.0.0',
            'oauth_type': 'iam',
            'Authorization': 'Basic ' + btoa('webtools:webtools')
          },
          mode: 'cors',
          credentials: 'include'
        });

        if (response.ok) {
          console.log(`Éxito con POST en ruta ${i + 1}: ${proxyUrl}`);
          const data = await response.json();
          return data as TokenResponse;
        }

        console.log(`Ruta ${i + 1} falló. Status: ${response.status}`);
        
        // Si es 405, probar con body en POST
        if (response.status === 405) {
          console.log(`Intentando POST con body en ruta ${i + 1}`);
          response = await fetch(proxyUrl, {
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

          if (response.ok) {
            console.log(`Éxito con POST + body en ruta ${i + 1}: ${proxyUrl}`);
            const data = await response.json();
            return data as TokenResponse;
          }
        }

      } catch (error) {
        console.log(`Error en ruta ${i + 1}:`, error);
        continue;
      }
    }

    // Si todas las rutas fallan, probar directamente al backend
    console.log('Todas las rutas del proxy fallaron, probando directamente al backend');
    
    try {
      const directUrl = `${import.meta.env.VITE_JWT_ENDPOINT}`;
      console.log('Intentando directamente:', directUrl);
      
      const response = await fetch(directUrl, {
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

      if (response.ok) {
        const data = await response.json();
        return data as TokenResponse;
      }

      throw new Error(`Error al obtener token: ${response.status} ${response.statusText}`);
    } catch (error) {
      console.error('Error final al obtener token:', error);
      throw new Error(`Error al obtener token: ${error instanceof Error ? error.message : 'Error desconocido'}`);
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

      const proxyUrl = `/api/oauth/token`;
      console.log('URL del proxy para refresh:', proxyUrl);

      const response = await fetch(proxyUrl, {
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
