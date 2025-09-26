import { describe, it, expect, vi, beforeEach, afterAll } from 'vitest';

const originalFetch = global.fetch;
const originalConsoleLog = console.log;
const originalConsoleWarn = console.warn;

describe('ConfigService', () => {
  beforeEach(() => {
    vi.resetModules();
    global.fetch = vi.fn();
    console.log = vi.fn();
    console.warn = vi.fn();
  });

  afterAll(() => {
    global.fetch = originalFetch as any;
    console.log = originalConsoleLog;
    console.warn = originalConsoleWarn;
  });

  it('carga y cachea param.json exitosamente', async () => {
    const mockConfig = { FOO: 'BAR', VITE_CLIENT_ID: 'test-client' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    const v1 = await ConfigService.get('FOO');
    const v2 = await ConfigService.get('FOO');

    expect(v1).toBe('BAR');
    expect(v2).toBe('BAR');
    expect(global.fetch).toHaveBeenCalledTimes(1);
    expect(console.log).toHaveBeenCalledWith('ConfigService: Iniciando carga de params.json desde configmap...');
    expect(console.log).toHaveBeenCalledWith('ConfigService: Configuración cargada desde params.json (configmap):', mockConfig);
  });

  it('usa caché en llamadas posteriores', async () => {
    const mockConfig = { FOO: 'BAR' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    // Primera llamada
    await ConfigService.get('FOO');
    // Segunda llamada (debería usar caché)
    await ConfigService.get('FOO');

    expect(global.fetch).toHaveBeenCalledTimes(1);
    expect(console.log).toHaveBeenCalledWith('ConfigService: Usando configuración cacheada desde params.json');
  });

  it('usa import.meta.env como fallback si falla fetch', async () => {
    (global.fetch as any).mockRejectedValueOnce(new Error('network error'));

    const ConfigService = (await import('../ConfigService')).default;

    const v = await ConfigService.get('VITE_CLIENT_ID', 'default-value');
    expect(v).toBe('default-value');
    expect(console.warn).toHaveBeenCalledWith('ConfigService: Fallo al cargar params.json, usando import.meta.env como fallback', expect.any(Error));
  });

  it('maneja respuesta HTTP no exitosa', async () => {
    (global.fetch as any).mockResolvedValueOnce({
      ok: false,
      status: 404,
      statusText: 'Not Found'
    });

    const ConfigService = (await import('../ConfigService')).default;

    const v = await ConfigService.get('VITE_CLIENT_ID', 'fallback');
    expect(v).toBe('fallback');
    expect(console.log).toHaveBeenCalledWith('ConfigService: Respuesta de params.json:', 404, 'Not Found');
  });

  it('indica fuente correcta en logs', async () => {
    const mockConfig = { VITE_CLIENT_ID: 'from-configmap' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    await ConfigService.get('VITE_CLIENT_ID');
    expect(console.log).toHaveBeenCalledWith('ConfigService: VITE_CLIENT_ID = "from-configmap" (fuente: params.json (configmap))');
  });

  it('indica fallback en logs cuando no encuentra clave', async () => {
    const mockConfig = { OTHER_KEY: 'value' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    await ConfigService.get('VITE_CLIENT_ID', 'fallback-value');
    expect(console.log).toHaveBeenCalledWith('ConfigService: VITE_CLIENT_ID = "fallback-value" (fuente: import.meta.env (fallback))');
  });

  it('limpia caché correctamente', async () => {
    const mockConfig = { FOO: 'BAR' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    await ConfigService.get('FOO');
    ConfigService.clearCache();
    
    // Nueva llamada debería hacer fetch nuevamente
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });
    
    await ConfigService.get('FOO');
    expect(global.fetch).toHaveBeenCalledTimes(2);
  });

  it('maneja múltiples llamadas concurrentes', async () => {
    const mockConfig = { FOO: 'BAR' };
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => mockConfig
    });

    const ConfigService = (await import('../ConfigService')).default;

    // Hacer múltiples llamadas concurrentes
    const promises = [
      ConfigService.get('FOO'),
      ConfigService.get('FOO'),
      ConfigService.get('FOO')
    ];

    const results = await Promise.all(promises);
    
    expect(results).toEqual(['BAR', 'BAR', 'BAR']);
    expect(global.fetch).toHaveBeenCalledTimes(1);
    expect(console.log).toHaveBeenCalledWith('ConfigService: Esperando carga en progreso...');
  });
});
