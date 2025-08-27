import { describe, it, expect, beforeEach, vi } from 'vitest';
import AuthService from '../AuthService';

// Mock de localStorage
const localStorageMock = {
  getItem: vi.fn(),
  setItem: vi.fn(),
  removeItem: vi.fn(),
  clear: vi.fn(),
};

Object.defineProperty(window, 'localStorage', {
  value: localStorageMock,
});

// Mock de fetch
vi.stubGlobal('fetch', vi.fn());

describe('AuthService', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  describe('getStoredToken', () => {
    it('debería retornar el token almacenado', () => {
      localStorageMock.getItem.mockReturnValue('stored-token');
      
      const result = AuthService.getStoredToken();
      
      expect(localStorageMock.getItem).toHaveBeenCalledWith('access_token');
      expect(result).toBe('stored-token');
    });

    it('debería retornar null si no hay token', () => {
      localStorageMock.getItem.mockReturnValue(null);
      
      const result = AuthService.getStoredToken();
      
      expect(result).toBeNull();
    });
  });

  describe('clearTokens', () => {
    it('debería limpiar todos los tokens del localStorage', () => {
      AuthService.clearTokens();
      
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('access_token');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_type');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_expires_in');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_expires_at');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('refresh_token');
    });
  });

  describe('isTokenExpired', () => {
    it('debería retornar true si no hay fecha de expiración', () => {
      localStorageMock.getItem.mockReturnValue(null);
      
      const result = AuthService.isTokenExpired();
      
      expect(result).toBe(true);
    });

    it('debería retornar true si el token está expirado', () => {
      const pastTime = new Date().getTime() - 1000;
      localStorageMock.getItem.mockReturnValue(pastTime.toString());
      
      const result = AuthService.isTokenExpired();
      
      expect(result).toBe(true);
    });

    it('debería retornar false si el token no está expirado', () => {
      const futureTime = new Date().getTime() + 1000000;
      localStorageMock.getItem.mockReturnValue(futureTime.toString());
      
      const result = AuthService.isTokenExpired();
      
      expect(result).toBe(false);
    });
  });

  describe('isAuthenticated', () => {
    it('debería retornar true si hay token válido', () => {
      const futureTime = new Date().getTime() + 1000000;
      localStorageMock.getItem
        .mockReturnValueOnce('test-token')
        .mockReturnValueOnce(futureTime.toString());
      
      const result = AuthService.isAuthenticated();
      
      expect(result).toBe(true);
    });

    it('debería retornar false si no hay token', () => {
      localStorageMock.getItem.mockReturnValue(null);
      
      const result = AuthService.isAuthenticated();
      
      expect(result).toBe(false);
    });
  });

  describe('getJWTToken', () => {
    it('debería obtener el token JWT correctamente', async () => {
      const mockResponse = {
        access_token: 'new-access-token',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'new-refresh-token'
      };

      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: vi.fn().mockResolvedValue(mockResponse)
      } as any);

      const result = await AuthService.getJWTToken('test-code');

      expect(mockFetch).toHaveBeenCalledWith(
        expect.stringContaining('grant_type=authorization_code'),
        expect.objectContaining({
          method: 'POST',
          headers: expect.objectContaining({
            'Content-Type': 'application/x-www-form-urlencoded',
            'true-client-ip': '0.0.0.0',
            'oauth_type': 'iam'
          })
        })
      );
      expect(result).toEqual(mockResponse);
      expect(localStorageMock.setItem).toHaveBeenCalledWith('access_token', 'new-access-token');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('refresh_token', 'new-refresh-token');
    });

    it('debería manejar errores en la obtención del token', async () => {
      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 400,
        statusText: 'Bad Request'
      } as any);

      await expect(AuthService.getJWTToken('invalid-code')).rejects.toThrow('Error al obtener token: 400 Bad Request');
    });
  });

  describe('refreshToken', () => {
    it('debería renovar el token correctamente', async () => {
      const mockResponse = {
        access_token: 'refreshed-access-token',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'new-refresh-token'
      };

      // Configurar el mock para que devuelva valores específicos en cada llamada
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'access_token') return 'old-access-token';
        if (key === 'refresh_token') return 'old-refresh-token';
        return null;
      });

      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: vi.fn().mockResolvedValue(mockResponse)
      } as any);

      const result = await AuthService.refreshToken();

      expect(mockFetch).toHaveBeenCalledWith(
        expect.stringContaining('grant_type=refresh_token'),
        expect.objectContaining({
          method: 'POST',
          headers: expect.objectContaining({
            'Content-Type': 'application/x-www-form-urlencoded',
            'oauth_type': 'iam',
            'authorization': 'Bearer old-access-token'
          })
        })
      );
      expect(result).toEqual(mockResponse);
    });

    it('debería retornar null si no hay refresh token', async () => {
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'refresh_token') return null;
        return null;
      });

      const result = await AuthService.refreshToken();

      expect(result).toBeNull();
    });

    it('debería limpiar tokens si falla la renovación', async () => {
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'refresh_token') return 'old-refresh-token';
        return null;
      });

      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 401,
        statusText: 'Unauthorized'
      } as any);

      const result = await AuthService.refreshToken();

      expect(result).toBeNull();
      expect(localStorageMock.removeItem).toHaveBeenCalled();
    });
  });

  describe('getValidToken', () => {
    it('debería retornar el token actual si no está expirado', async () => {
      const futureTime = new Date().getTime() + 1000000;
      
      // Configurar el mock para que devuelva valores específicos en cada llamada
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'access_token') return 'valid-token';
        if (key === 'token_expires_at') return futureTime.toString();
        return null;
      });

      const result = await AuthService.getValidToken();

      expect(result).toBe('valid-token');
    });

    it('debería renovar el token si está expirado', async () => {
      const pastTime = new Date().getTime() - 1000;
      const mockResponse = {
        access_token: 'refreshed-token',
        token_type: 'Bearer',
        expires_in: 3600
      };

      // Configurar el mock para que devuelva valores específicos en cada llamada
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'access_token') return 'expired-token';
        if (key === 'token_expires_at') return pastTime.toString();
        if (key === 'refresh_token') return 'refresh-token';
        return null;
      });

      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: vi.fn().mockResolvedValue(mockResponse)
      } as any);

      const result = await AuthService.getValidToken();

      expect(result).toBe('refreshed-token');
    });

    it('debería retornar null si no se puede renovar el token expirado', async () => {
      const pastTime = new Date().getTime() - 1000;
      
      // Configurar el mock para que devuelva valores específicos en cada llamada
      localStorageMock.getItem.mockImplementation((key: string) => {
        if (key === 'access_token') return 'expired-token';
        if (key === 'token_expires_at') return pastTime.toString();
        if (key === 'refresh_token') return 'refresh-token';
        return null;
      });

      const mockFetch = vi.mocked(fetch);
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 401
      } as any);

      const result = await AuthService.getValidToken();

      expect(result).toBeNull();
    });
  });
});
