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
});
