import { describe, it, expect, vi, beforeEach } from 'vitest';
import { ExecutorService } from '../ExecutorService';

// Mock de AuthService
vi.mock('../AuthService', () => ({
  default: {
    getValidToken: vi.fn()
  }
}));

// Mock de import.meta.env
vi.mock('import.meta', () => ({
  env: {
    VITE_EXECUTOR_BASE_URL: 'http://platform.dcloud.cl.bsch/executor/v1',
    VITE_EXECUTOR_EXECUTE_ENDPOINT: '/execute',
    VITE_EXECUTOR_STATUS_ENDPOINT: '/status',
    VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT: '/generated-project'
  }
}));

// Mock de fetch
const mockFetch = vi.fn();
global.fetch = mockFetch;

// Mock de atob y btoa
global.atob = vi.fn();
global.btoa = vi.fn();

// Mock de window.URL
const mockCreateObjectURL = vi.fn();
const mockRevokeObjectURL = vi.fn();
Object.defineProperty(window, 'URL', {
  value: {
    createObjectURL: mockCreateObjectURL,
    revokeObjectURL: mockRevokeObjectURL
  }
});

// Mock de document.createElement
const mockLink = {
  href: '',
  download: '',
  click: vi.fn()
};
const mockCreateElement = vi.fn(() => mockLink);
Object.defineProperty(document, 'createElement', {
  value: mockCreateElement
});

// Mock de document.body
const mockBody = {
  appendChild: vi.fn(),
  removeChild: vi.fn()
};
Object.defineProperty(document, 'body', {
  value: mockBody
});

describe('ExecutorService', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  describe('searchApps', () => {
    it('debería retornar todas las aplicaciones cuando no hay query', async () => {
      const result = await ExecutorService.searchApps('');

      expect(result).toEqual([
        'chl-dss-fraudlocal'
      ]);
    });

    it('debería filtrar aplicaciones por query', async () => {
      const result = await ExecutorService.searchApps('fraud');

      expect(result).toEqual([
        'chl-dss-fraudlocal'
      ]);
    });

    it('debería ser case insensitive', async () => {
      const result = await ExecutorService.searchApps('FRAUD');

      expect(result).toEqual([
        'chl-dss-fraudlocal'
      ]);
    });

    it('debería retornar array vacío para query sin coincidencias', async () => {
      const result = await ExecutorService.searchApps('nonexistent');

      expect(result).toEqual([]);
    });
  });

  describe('executeScript', () => {
    it('debería manejar errores de autenticación', async () => {
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(null);

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('No hay token de autenticación disponible');
      expect(mockFetch).not.toHaveBeenCalled();
    });

    it('debería manejar errores de red', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockRejectedValueOnce(new Error('Network error'));

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Network error');
    });
  });

  describe('getAppStatus', () => {
    it('debería retornar null para aplicaciones no encontradas', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 404
      });

      const result = await ExecutorService.getAppStatus('non-existent-app');

      expect(result).toBeNull();
    });

    it('debería incluir logs en la respuesta del status', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      const mockStatusWithLogs = {
        app: 'test-app',
        startTime: '2024-01-01T00:00:00Z',
        version: '1.0.0',
        status: 'RUNNING',
        logs: [
          {
            timestamp: '2024-01-01T00:00:00Z',
            level: 'INFO',
            message: 'Aplicación iniciada'
          },
          {
            timestamp: '2024-01-01T00:01:00Z',
            level: 'INFO',
            message: 'Procesando componente'
          }
        ]
      };

      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => mockStatusWithLogs
      });

      const result = await ExecutorService.getAppStatus('test-app');

      expect(result).toEqual(mockStatusWithLogs);
      expect(result?.logs).toHaveLength(2);
      expect(result?.logs?.[0].message).toBe('Aplicación iniciada');
    });
  });


  describe('downloadGeneratedProject', () => {
    it('debería manejar errores en la descarga', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({}) // Sin base64
      });

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('No se encontró el contenido del proyecto en la respuesta');
    });
  });
});
