import AuthService from './AuthService';
import ConfigService from './ConfigService';

export interface AppStatus {
  app: string;
  startTime: string;
  version: string;
  status: 'STARTING' | 'RUNNING' | 'COMPLETED' | 'STOPPED' | 'ERROR';
  logs?: ExecutionLog[];
}

export interface ExecutionLog {
  timestamp: string;
  level: 'INFO' | 'WARN' | 'ERROR' | 'DEBUG';
  message: string;
}

export class ExecutorService {
  private static async getConfigValues() {
    const baseUrl = await ConfigService.get('VITE_EXECUTOR_BASE_URL', import.meta.env.VITE_EXECUTOR_BASE_URL);
    const executeEndpoint = await ConfigService.get('VITE_EXECUTOR_EXECUTE_ENDPOINT', import.meta.env.VITE_EXECUTOR_EXECUTE_ENDPOINT);
    const statusEndpoint = await ConfigService.get('VITE_EXECUTOR_STATUS_ENDPOINT', import.meta.env.VITE_EXECUTOR_STATUS_ENDPOINT);
    const generatedEndpoint = await ConfigService.get('VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT', import.meta.env.VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT);
    return { baseUrl, executeEndpoint, statusEndpoint, generatedEndpoint };
  }

  /**
   * Valida que las variables de entorno estén configuradas
   */
  private static async validateConfig(): Promise<void> {
    const { baseUrl, executeEndpoint, statusEndpoint, generatedEndpoint } = await this.getConfigValues();
    const missing: string[] = [];
    if (!baseUrl) missing.push('VITE_EXECUTOR_BASE_URL');
    if (!executeEndpoint) missing.push('VITE_EXECUTOR_EXECUTE_ENDPOINT');
    if (!statusEndpoint) missing.push('VITE_EXECUTOR_STATUS_ENDPOINT');
    if (!generatedEndpoint) missing.push('VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT');
    if (missing.length > 0) {
      throw new Error(`Variables de configuración faltantes: ${missing.join(', ')}`);
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

      await this.validateConfig();
      const { baseUrl, executeEndpoint } = await this.getConfigValues();
      const url = `${baseUrl}${executeEndpoint}?app-name=${encodeURIComponent(appName)}`;
      
      console.log('Ejecutando script para:', appName);
      console.log('URL:', url);

      let response = await fetch(url, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
      });

      // Si el servidor devuelve 405 (p. ej. por diferencia con la barra final), reintentar
      if (response.status === 405) {
        const urlWithSlash = url.endsWith('/') ? url : `${url}/`;
        console.warn('Recibido 405. Reintentando con barra final:', urlWithSlash);
        response = await fetch(urlWithSlash, {
          method: 'POST',
          headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
          },
        });
      }

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

      await this.validateConfig();
      const { baseUrl, statusEndpoint } = await this.getConfigValues();
      const url = `${baseUrl}${statusEndpoint}/${encodeURIComponent(appName)}`;
      
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
   * Descarga el proyecto generado como TAR.GZ
   */
  static async downloadGeneratedProject(appName: string): Promise<{ success: boolean; message: string }> {
    try {
      const token = await AuthService.getValidToken();
      if (!token) {
        return { success: false, message: 'No hay token de autenticación disponible' };
      }

      await this.validateConfig();
      const { baseUrl, generatedEndpoint } = await this.getConfigValues();
      const url = `${baseUrl}${generatedEndpoint}?app=${encodeURIComponent(appName)}`;
      
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
      console.log('Respuesta del servidor:', result);
      
      // Buscar el base64 en diferentes campos posibles
      const base64Data = result.base64 || result.data || result.content || result.file;
      
      if (!base64Data) {
        console.error('No se encontró base64 en la respuesta:', result);
        throw new Error('No se encontró el contenido del proyecto en la respuesta');
      }

      // Validar que el base64 no esté vacío
      if (typeof base64Data !== 'string' || base64Data.trim() === '') {
        throw new Error('El contenido base64 está vacío o es inválido');
      }

      const fileName = `${appName}-generated-project.tar.gz`;
      
      try {
        // Convertir base64 a bytes de manera más robusta
        const binaryString = atob(base64Data);
        const bytes = new Uint8Array(binaryString.length);
        
        for (let i = 0; i < binaryString.length; i++) {
          bytes[i] = binaryString.charCodeAt(i);
        }
        
        // Crear blob como TAR.GZ
        const blob = new Blob([bytes], { type: 'application/gzip' });
        
        console.log('Blob creado:', {
          size: blob.size,
          type: blob.type
        });
        
        // Crear enlace de descarga
        const downloadUrl = window.URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = downloadUrl;
        link.download = fileName;
        link.style.display = 'none';
        
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        
        // Limpiar URL después de un breve delay
        setTimeout(() => {
          window.URL.revokeObjectURL(downloadUrl);
        }, 100);
        
        return {
          success: true,
          message: `Proyecto ${appName} descargado exitosamente como ${fileName}`
        };
        
      } catch (base64Error) {
        console.error('Error procesando base64:', base64Error);
        throw new Error('Error al procesar el contenido base64 del archivo');
      }
      
    } catch (error) {
      console.error('Error descargando proyecto:', error);
      return {
        success: false,
        message: error instanceof Error ? error.message : 'Error desconocido'
      };
    }
  }

}
