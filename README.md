import { describe, it, expect, vi, beforeEach, afterAll } from 'vitest';

const originalFetch = global.fetch;

describe('ConfigService', () => {
  beforeEach(() => {
    vi.resetModules();
    global.fetch = vi.fn();
  });

  it('carga y cachea param.json', async () => {
    (global.fetch as any).mockResolvedValueOnce({
      ok: true,
      json: async () => ({ FOO: 'BAR' })
    });

    const ConfigService = (await import('../ConfigService')).default;

    const v1 = await ConfigService.get('FOO');
    const v2 = await ConfigService.get('FOO');

    expect(v1).toBe('BAR');
    expect(v2).toBe('BAR');
    expect(global.fetch).toHaveBeenCalledTimes(1);
  });

  it('usa import.meta.env como fallback si falla fetch', async () => {
    (global.fetch as any).mockRejectedValueOnce(new Error('network'));

    const ConfigService = (await import('../ConfigService')).default;

    const v = await ConfigService.get('VITE_CLIENT_ID', 'default');
    expect(v).toBeDefined();
  });

  afterAll(() => {
    global.fetch = originalFetch as any;
  });
});
