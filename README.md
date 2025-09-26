export type AppConfig = Record<string, string>;

class ConfigService {
  private static configCache: AppConfig | null = null;
  private static loadingPromise: Promise<AppConfig> | null = null;

  static async load(): Promise<AppConfig> {
    if (this.configCache) {
      console.log('ConfigService: Usando configuración cacheada desde params.json');
      return this.configCache;
    }
    if (this.loadingPromise) {
      console.log('ConfigService: Esperando carga en progreso...');
      return this.loadingPromise;
    }

    console.log('ConfigService: Iniciando carga de params.json desde configmap...');
    this.loadingPromise = fetch('/params.json')
      .then(async (res) => {
        console.log('ConfigService: Respuesta de params.json:', res.status, res.statusText);
        if (!res.ok) throw new Error(`No se pudo cargar params.json: ${res.status}`);
        const cfg = (await res.json()) as AppConfig;
        console.log('ConfigService: Configuración cargada desde params.json (configmap):', cfg);
        this.configCache = cfg;
        return cfg;
      })
      .catch((err) => {
        console.warn('ConfigService: Fallo al cargar params.json, usando import.meta.env como fallback', err);
        const env: AppConfig = {} as AppConfig;
        Object.keys(import.meta.env || {}).forEach((k) => {
          const v = (import.meta.env as unknown as Record<string, string>)[k];
          if (typeof v === 'string') env[k] = v;
        });
        console.log('ConfigService: Usando configuración desde import.meta.env:', env);
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
    const value = cfg[key] ?? defaultValue;
    const source = cfg[key] ? 'params.json (configmap)' : 'import.meta.env (fallback)';
    console.log(`ConfigService: ${key} = "${value}" (fuente: ${source})`);
    return value;
  }

  static clearCache(): void {
    this.configCache = null;
    this.loadingPromise = null;
  }
}

export default ConfigService;

