interface TokenResponse {
  access_token: string;
  token_type: string;
  expires_in: number;
  refresh_token?: string;
}

class AuthService {
  private readonly JWT_ENDPOINT = 'https://ssm.hcloud.cl.bsch/oauth/token';
  private readonly REDIRECT_URI = 'https://chl-dss-lowcodeportal-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/';

  /**
   * Obtiene el token JWT usando el código de autorización
   */
  async getJWTToken(code: string): Promise<TokenResponse> {
    const params = new URLSearchParams({
      grant_type: 'authorization_code',
      code: code,
      redirect_uri: this.REDIRECT_URI
    });

    try {
      const response = await fetch(`${this.JWT_ENDPOINT}?${params.toString()}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
        },
      });

      if (!response.ok) {
        throw new Error(`Error al obtener token: ${response.status} ${response.statusText}`);
      }

      const tokenData: TokenResponse = await response.json();
      
      // Guardar el token en localStorage
      this.saveToken(tokenData);
      
      return tokenData;
    } catch (error) {
      console.error('Error al obtener el token JWT:', error);
      throw error;
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
    
    return new Date().getTime() > parseInt(expiresAt);
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
   * Limpia todos los tokens almacenados
   */
  clearTokens(): void {
    localStorage.removeItem('access_token');
    localStorage.removeItem('token_type');
    localStorage.removeItem('token_expires_in');
    localStorage.removeItem('token_expires_at');
    localStorage.removeItem('refresh_token');
  }

  /**
   * Verifica si el usuario está autenticado
   */
  isAuthenticated(): boolean {
    const token = this.getStoredToken();
    return token !== null && !this.isTokenExpired();
  }
}

export default new AuthService();
