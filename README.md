import { describe, it, expect, beforeEach, vi } from 'vitest';

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
const mockFetch = vi.fn();
vi.stubGlobal('fetch', mockFetch);

// Mock de btoa para Basic Auth
global.btoa = vi.fn((str: string) => Buffer.from(str).toString('base64'));

// Mock de ConfigService para valores de param.json (usar vi.hoisted para evitar TDZ)
const hoistedCfg = vi.hoisted(() => ({
  VITE_JWT_ENDPOINT: 'https://ssm.dcloud.cl.bsch/oauth/token',
  VITE_REFRESH_ENDPOINT: 'https://ssm.dcloud.cl.bsch/oauth/token',
  VITE_REDIRECT_URI: 'https://webtools-portal.dcloud.cl.bsch/',
  VITE_CLIENT_ID: 'webtools'
} as Record<string, string>));
vi.mock('../ConfigService', () => ({
  default: {
    get: vi.fn(async (key: string, def?: string) => hoistedCfg[key] ?? def)
  }
}));

// Importar AuthService después de configurar los mocks
import AuthService from '../AuthService';

describe('AuthService', () => {
  beforeEach(() => {
    vi.clearAllMocks();
    mockFetch.mockReset();
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
      const consoleSpy = vi.spyOn(console, 'log');
      AuthService.clearTokens();
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('access_token');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_type');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_expires_in');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('token_expires_at');
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('refresh_token');
      expect(consoleSpy).toHaveBeenCalledWith('Tokens limpiados del localStorage');
      consoleSpy.mockRestore();
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

  describe('getAuthHeader', () => {
    it('debería retornar el header de autorización con token válido', () => {
      const futureTime = new Date().getTime() + 1000000;
      localStorageMock.getItem
        .mockReturnValueOnce('test-token')
        .mockReturnValueOnce('Bearer')
        .mockReturnValueOnce(futureTime.toString());
      const result = AuthService.getAuthHeader();
      expect(result).toBe('Bearer test-token');
    });

    it('debería retornar null si no hay token', () => {
      localStorageMock.getItem.mockReturnValue(null);
      const result = AuthService.getAuthHeader();
      expect(result).toBeNull();
    });
  });

  describe('getJWTToken (directo a backend)', () => {
    it('debería llamar al endpoint con POST y x-www-form-urlencoded sin client_id', async () => {
      const responseBody = {
        access_token: 'acc',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      await AuthService.getJWTToken('code123');

      // Verificar URL y body mediante la primera llamada
      expect(mockFetch).toHaveBeenCalledTimes(1);
      const [calledUrl, calledOptions] = (mockFetch.mock.calls[0] ?? []) as [string, any];
      expect(calledUrl).toBe(hoistedCfg.VITE_JWT_ENDPOINT);
      expect(calledOptions).toEqual(expect.objectContaining({
        method: 'POST',
        headers: expect.objectContaining({
          'Content-Type': 'application/x-www-form-urlencoded',
          'oauth_type': 'iam',
          'true-client-ip': '0.0.0.0',
          Authorization: expect.stringContaining('Basic '),
        }),
        credentials: 'include',
        mode: 'cors',
      }));
      const bodySent = String(calledOptions?.body ?? '');
      expect(bodySent).toContain('grant_type=authorization_code');
      expect(bodySent).toContain('code=code123');
      expect(bodySent).toContain('redirect_uri=');
      expect(bodySent).not.toContain('client_id=');

      // Verificar guardado
      expect(localStorageMock.setItem).toHaveBeenCalledWith('access_token', 'acc');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('token_type', 'Bearer');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('token_expires_in', '3600');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('refresh_token', 'ref');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('token_expires_at', expect.any(String));
    });

    it('debería mostrar logs de configuración', async () => {
      const consoleSpy = vi.spyOn(console, 'log');
      const responseBody = {
        access_token: 'acc',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      await AuthService.getJWTToken('code123');

      expect(consoleSpy).toHaveBeenCalledWith('AuthService: Obteniendo token JWT con código:', 'code123');
      expect(consoleSpy).toHaveBeenCalledWith('AuthService: Endpoint OAuth:', hoistedCfg.VITE_JWT_ENDPOINT);
      consoleSpy.mockRestore();
    });

    it('debería usar valores existentes cuando ya están disponibles', async () => {
      const consoleSpy = vi.spyOn(console, 'log');
      const responseBody = {
        access_token: 'acc',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      // Simular que AuthService ya tiene los valores (caso normal)
      (AuthService as any).redirectUri = hoistedCfg.VITE_REDIRECT_URI;
      (AuthService as any).clientId = hoistedCfg.VITE_CLIENT_ID;

      await AuthService.getJWTToken('code123');

      // No debería llamar a ConfigService.get() porque ya tiene los valores
      expect(consoleSpy).not.toHaveBeenCalledWith('AuthService: redirectUri no disponible, esperando carga de ConfigService...');
      consoleSpy.mockRestore();
    });

    it('debería esperar a ConfigService cuando redirectUri no está disponible', async () => {
      const consoleSpy = vi.spyOn(console, 'log');
      const responseBody = {
        access_token: 'acc',
        token_type: 'Bearer',
        expires_in: 3600,
        refresh_token: 'ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      // Simular que AuthService no tiene los valores
      (AuthService as any).redirectUri = undefined;
      (AuthService as any).clientId = undefined;

      // Mock ConfigService para simular que redirectUri no está disponible inicialmente
      const ConfigService = await import('../ConfigService');
      vi.mocked(ConfigService.default.get)
        .mockResolvedValueOnce(hoistedCfg.VITE_REDIRECT_URI) // redirectUri
        .mockResolvedValueOnce(hoistedCfg.VITE_CLIENT_ID) // clientId
        .mockResolvedValueOnce(hoistedCfg.VITE_JWT_ENDPOINT); // JWT endpoint

      await AuthService.getJWTToken('code123');

      expect(consoleSpy).toHaveBeenCalledWith('AuthService: redirectUri no disponible, esperando carga de ConfigService...');
      expect(consoleSpy).toHaveBeenCalledWith('AuthService: Valores actualizados desde ConfigService:', {
        redirectUri: hoistedCfg.VITE_REDIRECT_URI,
        clientId: hoistedCfg.VITE_CLIENT_ID
      });
      consoleSpy.mockRestore();
    });


    it('debería manejar respuesta no OK', async () => {
      mockFetch.mockResolvedValue({
        ok: false,
        status: 400,
        statusText: 'Bad Request',
        text: vi.fn().mockResolvedValue('bad'),
      } as any);

      await expect(AuthService.getJWTToken('badcode')).rejects.toThrow('Error al obtener token: 400 Bad Request');
    });
  });

  describe('refreshToken (directo a backend)', () => {
    it('debería llamar al endpoint con POST y guardar token', async () => {
      localStorageMock.getItem
        .mockReturnValueOnce('ref') 
        .mockReturnValueOnce('acc'); 

      const responseBody = {
        access_token: 'new-acc',
        token_type: 'Bearer',
        expires_in: 1800,
        refresh_token: 'new-ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      const result = await AuthService.refreshToken();
      expect(result?.access_token).toBe('new-acc');
      expect(mockFetch).toHaveBeenCalledWith(
        hoistedCfg.VITE_REFRESH_ENDPOINT,
        expect.objectContaining({
          method: 'POST',
          headers: expect.objectContaining({ 'Content-Type': 'application/x-www-form-urlencoded' }),
          body: expect.stringContaining('grant_type=refresh_token'),
        })
      );
    });

    it('debería esperar a ConfigService cuando clientId no está disponible', async () => {
      const consoleSpy = vi.spyOn(console, 'log');
      const responseBody = {
        access_token: 'new-acc',
        token_type: 'Bearer',
        expires_in: 1800,
        refresh_token: 'new-ref',
      };
      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      localStorageMock.getItem.mockReturnValue('ref');

      // Mock ConfigService para simular que clientId no está disponible inicialmente
      const ConfigService = await import('../ConfigService');
      vi.mocked(ConfigService.default.get)
        .mockResolvedValueOnce(hoistedCfg.VITE_CLIENT_ID) // clientId
        .mockResolvedValueOnce(hoistedCfg.VITE_REFRESH_ENDPOINT); // refresh endpoint

      const result = await AuthService.refreshToken();

      expect(result?.access_token).toBe('new-acc');
      expect(consoleSpy).toHaveBeenCalledWith('AuthService: clientId no disponible, esperando carga de ConfigService...');
      expect(consoleSpy).toHaveBeenCalledWith('AuthService: clientId actualizado desde ConfigService:', hoistedCfg.VITE_CLIENT_ID);
      consoleSpy.mockRestore();
    });

    it('debería manejar error cuando VITE_REFRESH_ENDPOINT no está configurado', async () => {
      const consoleSpy = vi.spyOn(console, 'warn');
      const ConfigService = await import('../ConfigService');
      vi.mocked(ConfigService.default.get).mockResolvedValue(undefined);

      localStorageMock.getItem.mockReturnValue('ref');

      const result = await AuthService.refreshToken();
      
      expect(result).toBeNull();
      expect(consoleSpy).toHaveBeenCalledWith('VITE_REFRESH_ENDPOINT no configurado');
      consoleSpy.mockRestore();
    });

    it('debería usar expiración por defecto si expires_in no viene', async () => {
      localStorageMock.getItem
        .mockReturnValueOnce('ref') 
        .mockReturnValueOnce('acc');

      const responseBody = {
        access_token: 'na',
        token_type: 'Bearer',

      } as any;

      mockFetch.mockResolvedValue({
        ok: true,
        status: 200,
        statusText: 'OK',
        json: vi.fn().mockResolvedValue(responseBody),
      } as any);

      await AuthService.refreshToken();

      
      expect(localStorageMock.setItem).toHaveBeenCalledWith('token_expires_in', '3600');
      expect(localStorageMock.setItem).toHaveBeenCalledWith('token_expires_at', expect.any(String));
    });
  });
});
