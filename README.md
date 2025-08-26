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
      const authHeader = AuthService.getAuthHeader();
      if (authHeader) {
        requestHeaders['Authorization'] = authHeader;
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

      // Si el token expiró, intentar renovarlo o redirigir al login
      if (response.status === 401) {
        AuthService.clearTokens();
        window.location.href = 'https://dss-login-webtools-ui-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/';
        throw new Error('Token expirado. Redirigiendo al login...');
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
