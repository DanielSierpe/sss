export type AppConfig = Record<string, string>;

class ConfigService {
  private static configCache: AppConfig | null = null;
  private static loadingPromise: Promise<AppConfig> | null = null;

  static async load(): Promise<AppConfig> {
    if (this.configCache) return this.configCache;
    if (this.loadingPromise) return this.loadingPromise;

    this.loadingPromise = fetch('/param.json')
      .then(async (res) => {
        if (!res.ok) throw new Error(`No se pudo cargar param.json: ${res.status}`);
        const cfg = (await res.json()) as AppConfig;
        this.configCache = cfg;
        return cfg;
      })
      .catch((err) => {
        console.warn('Fallo al cargar param.json, usando import.meta.env como fallback', err);
        const env: AppConfig = {} as AppConfig;
        Object.keys(import.meta.env || {}).forEach((k) => {
          const v = (import.meta.env as unknown as Record<string, string>)[k];
          if (typeof v === 'string') env[k] = v;
        });
        this.configCache = env;
        return env;
      })
      .finally(() => {
        this.loadingPromise = null;
      });

    return this.loadingPromise;
  }

  static async get(key: string, defaultValue?: string): Promise<string | undefined> {
    const cfg = await this.load();
    return cfg[key] ?? defaultValue;
  }

  static clearCache(): void {
    this.configCache = null;
    this.loadingPromise = null;
  }
}

export default ConfigService;

