import AuthService from './AuthService';

export interface AppStatus {
  app: string;
  startTime: string;
  version: string;
  status: 'STARTING' | 'RUNNING' | 'COMPLETE' | 'STOPPED' | 'ERROR';
  logs?: ExecutionLog[];
}

export interface ExecutionLog {
  timestamp: string;
  level: 'INFO' | 'WARN' | 'ERROR' | 'DEBUG';
  message: string;
}

export class ExecutorService {
  private static readonly BASE_URL = import.meta.env.VITE_EXECUTOR_BASE_URL;
  private static readonly EXECUTE_ENDPOINT = import.meta.env.VITE_EXECUTOR_EXECUTE_ENDPOINT;
  private static readonly STATUS_ENDPOINT = import.meta.env.VITE_EXECUTOR_STATUS_ENDPOINT;
  private static readonly GENERATED_PROJECT_ENDPOINT = import.meta.env.VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT;

  /**
   * Valida que las variables de entorno estén configuradas
   */
  private static validateConfig(): void {
    const requiredVars = [
      'VITE_EXECUTOR_BASE_URL',
      'VITE_EXECUTOR_EXECUTE_ENDPOINT',
      'VITE_EXECUTOR_STATUS_ENDPOINT',
      'VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT'
    ];

    const missingVars = requiredVars.filter(varName => !import.meta.env[varName]);
    
    if (missingVars.length > 0) {
      throw new Error(`Variables de entorno faltantes: ${missingVars.join(', ')}`);
    }
  }

  /**
   * Ejecuta un script para generar un componente
   */
  static async executeScript(appName: string): Promise<{ success: boolean; message: string }> {
    try {
      const token = await AuthService.getValidToken();
      if (!token) {
        return { success: false, message: 'No hay token de autenticación disponible' };
      }

      this.validateConfig();

      const url = `${this.BASE_URL}${this.EXECUTE_ENDPOINT}?app-name=${encodeURIComponent(appName)}`;
      
      console.log('Ejecutando script para:', appName);
      console.log('URL:', url);

      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
      });

      if (!response.ok) {
        const errorText = await response.text();
        throw new Error(`Error ${response.status}: ${errorText}`);
      }

      const result = await response.json();
      console.log('Script ejecutado exitosamente:', result);
      
      return {
        success: true,
        message: `Componente ${appName} iniciado correctamente`
      };
    } catch (error) {
      console.error('Error ejecutando script:', error);
      return {
        success: false,
        message: error instanceof Error ? error.message : 'Error desconocido'
      };
    }
  }

  /**
   * Obtiene el estado de una aplicación
   */
  static async getAppStatus(appName: string): Promise<AppStatus | null> {
    try {
      const token = await AuthService.getValidToken();
      if (!token) {
        return null;
      }

      this.validateConfig();

      const url = `${this.BASE_URL}${this.STATUS_ENDPOINT}/${encodeURIComponent(appName)}`;
      
      console.log('Obteniendo estado de la app:', appName);
      console.log('URL:', url);

      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
      });

      if (!response.ok) {
        if (response.status === 404) {
          return null; // App no encontrada
        }
        throw new Error(`Error ${response.status}: ${response.statusText}`);
      }

      const status: AppStatus = await response.json();
      return status;
    } catch (error) {
      console.error('Error obteniendo estado de la app:', error);
      return null;
    }
  }


  /**
   * Descarga el proyecto generado como ZIP
   */
  static async downloadGeneratedProject(appName: string): Promise<{ success: boolean; message: string }> {
    try {
      const token = await AuthService.getValidToken();
      if (!token) {
        return { success: false, message: 'No hay token de autenticación disponible' };
      }

      this.validateConfig();

      const url = `${this.BASE_URL}${this.GENERATED_PROJECT_ENDPOINT}?app=${encodeURIComponent(appName)}`;
      
      console.log('Descargando proyecto generado para:', appName);
      console.log('URL:', url);

      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
      });

      if (!response.ok) {
        throw new Error(`Error ${response.status}: ${response.statusText}`);
      }

      const result = await response.json();
      
      // Asumimos que la respuesta contiene el base64 del ZIP
      if (result.base64 || result.data) {
        const base64Data = result.base64 || result.data;
        const fileName = `${appName}-generated-project.zip`;
        
        // Convertir base64 a blob y descargar
        const byteCharacters = atob(base64Data);
        const byteNumbers = new Array(byteCharacters.length);
        for (let i = 0; i < byteCharacters.length; i++) {
          byteNumbers[i] = byteCharacters.charCodeAt(i);
        }
        const byteArray = new Uint8Array(byteNumbers);
        const blob = new Blob([byteArray], { type: 'application/zip' });
        
        // Crear enlace de descarga
        const url = window.URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.download = fileName;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        window.URL.revokeObjectURL(url);
        
        return {
          success: true,
          message: `Proyecto ${appName} descargado exitosamente`
        };
      } else {
        throw new Error('No se encontró el contenido del proyecto en la respuesta');
      }
    } catch (error) {
      console.error('Error descargando proyecto:', error);
      return {
        success: false,
        message: error instanceof Error ? error.message : 'Error desconocido'
      };
    }
  }

  
  static async searchApps(query: string): Promise<string[]> {
    const availableApps = [
      'chl-dss-fraudlocal'
    ];
    if (!query.trim()) {
      return availableApps;
    }
    return availableApps.filter(app => 
      app.toLowerCase().includes(query.toLowerCase())
    );
  }
}
