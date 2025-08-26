import { describe, it, expect, vi, beforeEach } from 'vitest';
import React from 'react';
import { render, screen } from '@testing-library/react';
import { AuthProvider, useAuthContext } from '../AuthContext';

// Mock del hook useAuth
const mockUseAuth = vi.fn();

vi.mock('../../hooks/useAuth', () => ({
  useAuth: () => mockUseAuth(),
}));

// Componente de prueba para usar el contexto
const TestComponent = () => {
  const auth = useAuthContext();
  return (
    <div>
      <span data-testid="isAuthenticated">{auth.isAuthenticated.toString()}</span>
      <span data-testid="isLoading">{auth.isLoading.toString()}</span>
      <span data-testid="error">{auth.error || 'no-error'}</span>
      <span data-testid="token">{auth.token || 'no-token'}</span>
      <button data-testid="logout" onClick={auth.logout}>Logout</button>
    </div>
  );
};

describe('AuthContext', () => {
  const mockAuthData = {
    isAuthenticated: false,
    isLoading: false,
    error: null,
    token: null,
    handleAuthCode: vi.fn(),
    logout: vi.fn(),
  };

  beforeEach(() => {
    vi.clearAllMocks();
    mockUseAuth.mockReturnValue(mockAuthData);
  });

  describe('AuthProvider', () => {
    it('debería renderizar children correctamente', () => {
      render(
        <AuthProvider>
          <div data-testid="test-child">Test Child</div>
        </AuthProvider>
      );

      expect(screen.getByTestId('test-child')).toBeInTheDocument();
    });

    it('debería proporcionar el contexto de autenticación', () => {
      render(
        <AuthProvider>
          <TestComponent />
        </AuthProvider>
      );

      expect(screen.getByTestId('isAuthenticated')).toHaveTextContent('false');
      expect(screen.getByTestId('isLoading')).toHaveTextContent('false');
      expect(screen.getByTestId('error')).toHaveTextContent('no-error');
      expect(screen.getByTestId('token')).toHaveTextContent('no-token');
    });

    it('debería actualizar el contexto cuando cambia el estado de autenticación', () => {
      const authenticatedData = {
        ...mockAuthData,
        isAuthenticated: true,
        token: 'test-token',
      };

      mockUseAuth.mockReturnValue(authenticatedData);

      render(
        <AuthProvider>
          <TestComponent />
        </AuthProvider>
      );

      expect(screen.getByTestId('isAuthenticated')).toHaveTextContent('true');
      expect(screen.getByTestId('token')).toHaveTextContent('test-token');
    });

    it('debería mostrar estado de carga', () => {
      const loadingData = {
        ...mockAuthData,
        isLoading: true,
      };

      mockUseAuth.mockReturnValue(loadingData);

      render(
        <AuthProvider>
          <TestComponent />
        </AuthProvider>
      );

      expect(screen.getByTestId('isLoading')).toHaveTextContent('true');
    });

    it('debería mostrar errores', () => {
      const errorData = {
        ...mockAuthData,
        error: 'Error de autenticación',
      };

      mockUseAuth.mockReturnValue(errorData);

      render(
        <AuthProvider>
          <TestComponent />
        </AuthProvider>
      );

      expect(screen.getByTestId('error')).toHaveTextContent('Error de autenticación');
    });
  });

  describe('useAuthContext', () => {
    it('debería lanzar error si se usa fuera del AuthProvider', () => {
      // Suprimir el error de console.error para esta prueba
      const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {});

      expect(() => {
        render(<TestComponent />);
      }).toThrow('useAuthContext debe ser usado dentro de un AuthProvider');

      consoleSpy.mockRestore();
    });

    it('debería proporcionar acceso a las funciones de autenticación', () => {
      const mockLogout = vi.fn();
      const authDataWithLogout = {
        ...mockAuthData,
        logout: mockLogout,
      };

      mockUseAuth.mockReturnValue(authDataWithLogout);

      render(
        <AuthProvider>
          <TestComponent />
        </AuthProvider>
      );

      const logoutButton = screen.getByTestId('logout');
      logoutButton.click();

      expect(mockLogout).toHaveBeenCalled();
    });
  });
});
