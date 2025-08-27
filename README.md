import { describe, it, expect, beforeEach, vi } from 'vitest';
import HttpService from '../HttpService';
import AuthService from '../AuthService';

// Mock del AuthService
vi.mock('../AuthService');

// Mock de fetch
vi.stubGlobal('fetch', vi.fn());

// Mock de window.location
Object.defineProperty(window, 'location', {
  value: {
    href: 'https://dss-login-webtools-ui-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/',
  },
  writable: true,
});

describe('HttpService', () => {
  beforeEach(() => {
    vi.clearAllMocks();
    // Mock de getValidToken que ahora es asíncrono
    (AuthService.getValidToken as any).mockResolvedValue('test-token');
    (AuthService.clearTokens as any).mockImplementation(() => {});
  });

  describe('request', () => {
    it('debería hacer una petición GET exitosa', async () => {
      const mockResponse = { data: 'test' };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.request('/test-endpoint', { method: 'GET' });

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/test-endpoint');
      expect(fetchCall[1].method).toBe('GET');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(result).toEqual(mockResponse);
    });

    it('debería hacer una petición POST con body', async () => {
      const mockResponse = { success: true };
      const requestBody = { name: 'test' };
      
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.request('/test-endpoint', {
        method: 'POST',
        body: requestBody,
      });

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/test-endpoint');
      expect(fetchCall[1].method).toBe('POST');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(fetchCall[1].body).toBe(JSON.stringify(requestBody));
      expect(result).toEqual(mockResponse);
    });

    it('debería hacer una petición sin autenticación', async () => {
      const mockResponse = { public: true };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.request('/public-endpoint', {
        method: 'GET',
        includeAuth: false,
      });

      expect(fetch).toHaveBeenCalledWith('/public-endpoint', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      expect(result).toEqual(mockResponse);
    });

    it('debería manejar respuestas vacías', async () => {
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => '',
      });

      const result = await HttpService.request('/empty-endpoint');

      expect(result).toBeNull();
    });

    it('debería redirigir al login si recibe 401', async () => {
      (fetch as any).mockResolvedValueOnce({
        ok: false,
        status: 401,
        statusText: 'Unauthorized',
      });

      await expect(HttpService.request('/protected-endpoint')).rejects.toThrow('Token expirado y no se pudo renovar. Redirigiendo al login...');
      expect(AuthService.clearTokens).toHaveBeenCalled();
    });

    it('debería manejar otros errores HTTP', async () => {
      (fetch as any).mockResolvedValueOnce({
        ok: false,
        status: 500,
        statusText: 'Internal Server Error',
      });

      await expect(HttpService.request('/error-endpoint')).rejects.toThrow('HTTP error! status: 500 - Internal Server Error');
    });

    it('debería manejar errores de red', async () => {
      (fetch as any).mockRejectedValueOnce(new Error('Network error'));

      await expect(HttpService.request('/network-error')).rejects.toThrow('Network error');
    });
  });

  describe('get', () => {
    it('debería hacer una petición GET', async () => {
      const mockResponse = { data: 'get-test' };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.get('/get-endpoint');

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/get-endpoint');
      expect(fetchCall[1].method).toBe('GET');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(result).toEqual(mockResponse);
    });
  });

  describe('post', () => {
    it('debería hacer una petición POST', async () => {
      const mockResponse = { success: true };
      const requestBody = { name: 'post-test' };
      
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.post('/post-endpoint', requestBody);

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/post-endpoint');
      expect(fetchCall[1].method).toBe('POST');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(fetchCall[1].body).toBe(JSON.stringify(requestBody));
      expect(result).toEqual(mockResponse);
    });
  });

  describe('put', () => {
    it('debería hacer una petición PUT', async () => {
      const mockResponse = { updated: true };
      const requestBody = { name: 'put-test' };
      
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.put('/put-endpoint', requestBody);

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/put-endpoint');
      expect(fetchCall[1].method).toBe('PUT');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(fetchCall[1].body).toBe(JSON.stringify(requestBody));
      expect(result).toEqual(mockResponse);
    });
  });

  describe('delete', () => {
    it('debería hacer una petición DELETE', async () => {
      const mockResponse = { deleted: true };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.delete('/delete-endpoint');

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/delete-endpoint');
      expect(fetchCall[1].method).toBe('DELETE');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(result).toEqual(mockResponse);
    });
  });

  describe('patch', () => {
    it('debería hacer una petición PATCH', async () => {
      const mockResponse = { patched: true };
      const requestBody = { name: 'patch-test' };
      
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.patch('/patch-endpoint', requestBody);

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/patch-endpoint');
      expect(fetchCall[1].method).toBe('PATCH');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(fetchCall[1].body).toBe(JSON.stringify(requestBody));
      expect(result).toEqual(mockResponse);
    });
  });

  describe('requestWithoutAuth', () => {
    it('debería hacer una petición sin autenticación', async () => {
      const mockResponse = { public: true };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const result = await HttpService.requestWithoutAuth('/public-endpoint');

      expect(fetch).toHaveBeenCalledWith('/public-endpoint', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      expect(result).toEqual(mockResponse);
    });
  });

  describe('manejo de headers personalizados', () => {
    it('debería incluir headers personalizados', async () => {
      const mockResponse = { custom: true };
      (fetch as any).mockResolvedValueOnce({
        ok: true,
        text: async () => JSON.stringify(mockResponse),
      });

      const customHeaders = {
        'X-Custom-Header': 'custom-value',
        'Accept': 'application/xml',
      };

      await HttpService.request('/custom-endpoint', {
        method: 'GET',
        headers: customHeaders,
      });

      const fetchCall = (fetch as any).mock.calls[0];
      expect(fetchCall[0]).toBe('/custom-endpoint');
      expect(fetchCall[1].method).toBe('GET');
      expect(fetchCall[1].headers['Content-Type']).toBe('application/json');
      expect(fetchCall[1].headers['Authorization']).toBe('Bearer test-token');
      expect(fetchCall[1].headers['X-Custom-Header']).toBe('custom-value');
      expect(fetchCall[1].headers['Accept']).toBe('application/xml');
    });
  });
});
