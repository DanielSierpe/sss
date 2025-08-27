import AuthService from './AuthService';

interface RequestOptions {
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';
  headers?: Record<string, string>;
  body?: any;
  includeAuth?: boolean;
}

class HttpService {
  private baseURL: string = '';

  constructor(baseURL?: string) {
    this.baseURL = baseURL || '';
  }

  /**
   * Realiza una petición HTTP con autenticación automática
   */
  async request<T>(endpoint: string, options: RequestOptions = {}): Promise<T> {
    const {
      method = 'GET',
      headers = {},
      body,
      includeAuth = true
    } = options;

    const url = `${this.baseURL}${endpoint}`;
    
    const requestHeaders: Record<string, string> = {
      'Content-Type': 'application/json',
      ...headers
    };

    // Agregar token de autenticación si está disponible y se requiere
    if (includeAuth) {
      const validToken = await AuthService.getValidToken();
      if (validToken) {
        const tokenType = localStorage.getItem('token_type') || 'Bearer';
        requestHeaders['Authorization'] = `${tokenType} ${validToken}`;
      }
    }

    const config: RequestInit = {
      method,
      headers: requestHeaders,
    };

    if (body && method !== 'GET') {
      config.body = typeof body === 'string' ? body : JSON.stringify(body);
    }

    try {
      const response = await fetch(url, config);

      // Si el token expiró, intentar renovarlo automáticamente
      if (response.status === 401 && includeAuth) {
        console.log('Token expirado, intentando renovar...');
        const refreshedToken = await AuthService.refreshToken();
        
        if (refreshedToken) {
          // Reintentar la petición con el nuevo token
          const newTokenType = localStorage.getItem('token_type') || 'Bearer';
          requestHeaders['Authorization'] = `${newTokenType} ${refreshedToken.access_token}`;
          
          const retryResponse = await fetch(url, {
            ...config,
            headers: requestHeaders
          });
          
          if (retryResponse.ok) {
            const text = await retryResponse.text();
            return text ? JSON.parse(text) as T : null as T;
          }
        }
        
        // Si no se pudo renovar, limpiar tokens y redirigir al login
        AuthService.clearTokens();
        const loginUrl = import.meta.env.VITE_LOGIN_URL || 'https://dss-login-webtools-ui-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/';
        window.location.href = loginUrl;
        throw new Error('Token expirado y no se pudo renovar. Redirigiendo al login...');
      }

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status} - ${response.statusText}`);
      }

      // Si la respuesta es vacía, retornar null
      const text = await response.text();
      if (!text) {
        return null as T;
      }

      return JSON.parse(text) as T;
    } catch (error) {
      console.error('Error en petición HTTP:', error);
      throw error;
    }
  }

  /**
   * Método GET
   */
  async get<T>(endpoint: string, options?: Omit<RequestOptions, 'method'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, method: 'GET' });
  }

  /**
   * Método POST
   */
  async post<T>(endpoint: string, body?: any, options?: Omit<RequestOptions, 'method' | 'body'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, method: 'POST', body });
  }

  /**
   * Método PUT
   */
  async put<T>(endpoint: string, body?: any, options?: Omit<RequestOptions, 'method' | 'body'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, method: 'PUT', body });
  }

  /**
   * Método DELETE
   */
  async delete<T>(endpoint: string, options?: Omit<RequestOptions, 'method'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, method: 'DELETE' });
  }

  /**
   * Método PATCH
   */
  async patch<T>(endpoint: string, body?: any, options?: Omit<RequestOptions, 'method' | 'body'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, method: 'PATCH', body });
  }

  /**
   * Petición sin autenticación (para login, etc.)
   */
  async requestWithoutAuth<T>(endpoint: string, options?: Omit<RequestOptions, 'includeAuth'>): Promise<T> {
    return this.request<T>(endpoint, { ...options, includeAuth: false });
  }
}

export default new HttpService();
