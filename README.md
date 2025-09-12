import { describe, it, expect, vi, beforeEach } from 'vitest';

// Mock de AuthService
vi.mock('../AuthService', () => ({
  default: {
    getValidToken: vi.fn()
  }
}));

// Mock de import.meta.env usando vi.stubEnv
vi.stubEnv('VITE_EXECUTOR_BASE_URL', 'https://platform.dcloud.cl.bsch/executor/v1');
vi.stubEnv('VITE_EXECUTOR_EXECUTE_ENDPOINT', '/execute');
vi.stubEnv('VITE_EXECUTOR_STATUS_ENDPOINT', '/status');
vi.stubEnv('VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT', '/generated-project');

import { ExecutorService } from '../ExecutorService';

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
  click: vi.fn(),
  style: { display: 'none' }
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

    it('debería ejecutar script exitosamente', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({ success: true })
      });

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(true);
      expect(result.message).toBe('Componente test-app iniciado correctamente');
      expect(mockFetch).toHaveBeenCalledWith(
        'https://platform.dcloud.cl.bsch/executor/v1/execute?app-name=test-app',
        expect.objectContaining({
          method: 'POST',
          headers: expect.objectContaining({
            'Authorization': 'Bearer mock-token',
            'Content-Type': 'application/json'
          })
        })
      );
    });

    it('debería reintentar con barra final en caso de error 405', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      // Primera llamada devuelve 405
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 405
      });
      
      // Segunda llamada (con barra final) es exitosa
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({ success: true })
      });

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(true);
      expect(mockFetch).toHaveBeenCalledTimes(2);
      expect(mockFetch).toHaveBeenNthCalledWith(2,
        'https://platform.dcloud.cl.bsch/executor/v1/execute?app-name=test-app/',
        expect.objectContaining({
          method: 'POST',
          headers: expect.objectContaining({
            'Authorization': 'Bearer mock-token',
            'Content-Type': 'application/json'
          })
        })
      );
    });

    it('debería manejar errores HTTP del servidor', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 500,
        text: async () => 'Internal Server Error'
      });

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Error 500: Internal Server Error');
    });

    it('debería manejar errores desconocidos', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockRejectedValueOnce('Unknown error');

      const result = await ExecutorService.executeScript('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Error desconocido');
    });
  });

  describe('getAppStatus', () => {
    it('debería retornar null cuando no hay token', async () => {
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(null);

      const result = await ExecutorService.getAppStatus('test-app');

      expect(result).toBeNull();
      expect(mockFetch).not.toHaveBeenCalled();
    });

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

    it('debería manejar errores HTTP del servidor', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 500,
        statusText: 'Internal Server Error'
      });

      const result = await ExecutorService.getAppStatus('test-app');

      expect(result).toBeNull();
    });

    it('debería manejar errores de red', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockRejectedValueOnce(new Error('Network error'));

      const result = await ExecutorService.getAppStatus('test-app');

      expect(result).toBeNull();
    });

    it('debería obtener el estado exitosamente', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      const mockStatus = {
        app: 'test-app',
        startTime: '2024-01-01T00:00:00Z',
        version: '1.0.0',
        status: 'RUNNING'
      };

      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => mockStatus
      });

      const result = await ExecutorService.getAppStatus('test-app');

      expect(result).toEqual(mockStatus);
      expect(mockFetch).toHaveBeenCalledWith(
        'https://platform.dcloud.cl.bsch/executor/v1/status/test-app',
        expect.objectContaining({
          method: 'GET',
          headers: expect.objectContaining({
            'Authorization': 'Bearer mock-token',
            'Content-Type': 'application/json'
          })
        })
      );
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
    it('debería manejar errores de autenticación', async () => {
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(null);

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('No hay token de autenticación disponible');
      expect(mockFetch).not.toHaveBeenCalled();
    });

    it('debería manejar errores HTTP del servidor', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: false,
        status: 500,
        statusText: 'Internal Server Error'
      });

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Error 500: Internal Server Error');
    });

    it('debería manejar errores cuando no hay base64 en la respuesta', async () => {
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

    it('debería manejar base64 vacío o inválido', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({ base64: '   ' }) 
      });

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('El contenido base64 está vacío o es inválido');
    });

    it('debería descargar el proyecto exitosamente', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      const mockBase64 = 'dGVzdCBkYXRh'; // "test data" en base64
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({ base64: mockBase64 })
      });

      // Mock de atob para simular la conversión
      vi.mocked(global.atob).mockReturnValue('test data');
      
      // Mock de createObjectURL
      mockCreateObjectURL.mockReturnValue('blob:mock-url');
      
      // Mock de setTimeout para ejecutar inmediatamente
      vi.spyOn(global, 'setTimeout').mockImplementation((fn) => {
        fn();
        return 1 as any;
      });

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      // Verificar que se hizo la llamada HTTP correcta
      expect(mockFetch).toHaveBeenCalledWith(
        'https://platform.dcloud.cl.bsch/executor/v1/generated-project?app=test-app',
        expect.objectContaining({
          method: 'GET',
          headers: expect.objectContaining({
            'Authorization': 'Bearer mock-token'
          })
        })
      );

      // Verificar que el resultado es exitoso
      expect(result.success).toBe(true);
      expect(result.message).toContain('Proyecto test-app descargado exitosamente');
    });

    it('debería manejar errores en el procesamiento de base64', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      const mockBase64 = 'invalid-base64';
      mockFetch.mockResolvedValueOnce({
        ok: true,
        json: async () => ({ base64: mockBase64 })
      });

      // Mock de atob para que lance un error
      vi.mocked(global.atob).mockImplementation(() => {
        throw new Error('Invalid base64');
      });

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Error al procesar el contenido base64 del archivo');
    });

    it('debería manejar errores de red', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockRejectedValueOnce(new Error('Network error'));

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Network error');
    });

    it('debería manejar errores desconocidos', async () => {
      const mockToken = 'mock-token';
      const AuthService = await import('../AuthService');
      vi.mocked(AuthService.default.getValidToken).mockResolvedValue(mockToken);
      
      mockFetch.mockRejectedValueOnce('Unknown error');

      const result = await ExecutorService.downloadGeneratedProject('test-app');

      expect(result.success).toBe(false);
      expect(result.message).toBe('Error desconocido');
    });
  });
});
