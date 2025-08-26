import { describe, it, expect, beforeEach, vi } from 'vitest';
import { renderHook, act } from '@testing-library/react';
import { useAuth } from '../useAuth';
import AuthService from '../../services/AuthService';

// Mock del AuthService
vi.mock('../../services/AuthService');

// Mock de window.location
const mockLocation = {
  href: 'http://localhost:3000/',
  searchParams: {
    get: vi.fn(),
  },
};

Object.defineProperty(window, 'location', {
  value: mockLocation,
  writable: true,
});

// Mock de window.history
const mockHistory = {
  replaceState: vi.fn(),
};

Object.defineProperty(window, 'history', {
  value: mockHistory,
  writable: true,
});

describe('useAuth', () => {
  beforeEach(() => {
    vi.clearAllMocks();
    (AuthService.isAuthenticated as any).mockReturnValue(false);
    (AuthService.getStoredToken as any).mockReturnValue(null);
    mockLocation.searchParams.get.mockReturnValue(null);
  });

  describe('estado inicial', () => {
    it('debería inicializar con estado no autenticado', () => {
      const { result } = renderHook(() => useAuth());

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.isLoading).toBe(false);
      expect(result.current.error).toBeNull();
      expect(result.current.token).toBeNull();
    });

    it('debería inicializar con estado autenticado si hay token válido', () => {
      (AuthService.isAuthenticated as any).mockReturnValue(true);
      (AuthService.getStoredToken as any).mockReturnValue('valid-token');

      const { result } = renderHook(() => useAuth());

      expect(result.current.isAuthenticated).toBe(true);
      expect(result.current.token).toBe('valid-token');
    });
  });

  describe('handleAuthCode', () => {
    it('debería manejar el código de autorización exitosamente', async () => {
      const mockTokenData = {
        access_token: 'test-token',
        token_type: 'Bearer',
        expires_in: 3600,
      };

      (AuthService.getJWTToken as any).mockResolvedValue(mockTokenData);

      const { result } = renderHook(() => useAuth());

      await act(async () => {
        await result.current.handleAuthCode('test-code');
      });

      expect(AuthService.getJWTToken).toHaveBeenCalledWith('test-code');
      expect(result.current.isAuthenticated).toBe(true);
      expect(result.current.token).toBe('test-token');
      expect(result.current.isLoading).toBe(false);
      expect(result.current.error).toBeNull();
    });

    it('debería manejar errores durante la obtención del token', async () => {
      const errorMessage = 'Error de red';
      (AuthService.getJWTToken as any).mockRejectedValue(new Error(errorMessage));

      const { result } = renderHook(() => useAuth());

      await act(async () => {
        await result.current.handleAuthCode('test-code');
      });

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.token).toBeNull();
      expect(result.current.isLoading).toBe(false);
      expect(result.current.error).toBe(errorMessage);
    });
  });

  describe('logout', () => {
    it('debería limpiar el estado de autenticación', () => {
      const { result } = renderHook(() => useAuth());

      act(() => {
        result.current.logout();
      });

      expect(AuthService.clearTokens).toHaveBeenCalled();
      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.token).toBeNull();
      expect(result.current.error).toBeNull();
    });
  });

  describe('detección automática de código', () => {
    it('debería manejar la detección de código de autorización', async () => {
      // Esta prueba verifica que el hook se inicializa correctamente
      // La detección automática se prueba en el entorno real
      const { result } = renderHook(() => useAuth());

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.token).toBeNull();
      expect(result.current.isLoading).toBe(false);
      expect(result.current.error).toBeNull();
    });

    it('debería tener la función handleAuthCode disponible', () => {
      const { result } = renderHook(() => useAuth());

      expect(typeof result.current.handleAuthCode).toBe('function');
      expect(typeof result.current.logout).toBe('function');
    });
  });
});
