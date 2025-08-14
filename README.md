export interface ApiDefinition {
  spec: any;
  name?: string;
  createdOn?: Date;
  modifiedOn?: Date;
}

export interface RecoveryApiDefinition extends ApiDefinition {
  savedForRecovery?: Date;
}

export class StorageService {
  private readonly STORAGE_KEY = 'apicurito-api-definition';
  private readonly RECOVERY_KEY = 'apicurito-recovery';

  /**
   * Verifica si existe una API guardada
   */
  public exists(): boolean {
    try {
      const stored = localStorage.getItem(this.STORAGE_KEY);
      return stored !== null && stored !== undefined;
    } catch (error) {
      console.error('Error checking storage:', error);
      return false;
    }
  }

  /**
   * Guarda una API en localStorage
   */
  public save(apiDefinition: ApiDefinition): void {
    try {
      const dataToStore = {
        ...apiDefinition,
        modifiedOn: new Date()
      };
      localStorage.setItem(this.STORAGE_KEY, JSON.stringify(dataToStore));
      console.info('[StorageService] API saved successfully');
    } catch (error) {
      console.error('Error saving API:', error);
      throw new Error('Failed to save API definition');
    }
  }

  /**
   * Carga una API desde localStorage
   */
  public load(): ApiDefinition | null {
    try {
      const stored = localStorage.getItem(this.STORAGE_KEY);
      if (!stored) {
        return null;
      }
      
      const parsed = JSON.parse(stored);
      return {
        ...parsed,
        modifiedOn: parsed.modifiedOn ? new Date(parsed.modifiedOn) : new Date()
      };
    } catch (error) {
      console.error('Error loading API:', error);
      return null;
    }
  }

  /**
   * Recupera una API (alias de load para mantener compatibilidad)
   */
  public recover(): ApiDefinition {
    const api = this.load();
    if (!api) {
      throw new Error('No API definition found to recover');
    }
    return api;
  }

  /**
   * Elimina la API guardada
   */
  public clear(): void {
    try {
      localStorage.removeItem(this.STORAGE_KEY);
      console.info('[StorageService] API cleared successfully');
    } catch (error) {
      console.error('Error clearing API:', error);
    }
  }

  /**
   * Guarda una copia de recuperación
   */
  public saveForRecovery(apiDefinition: ApiDefinition): void {
    try {
      const recoveryData = {
        ...apiDefinition,
        savedForRecovery: new Date()
      };
      localStorage.setItem(this.RECOVERY_KEY, JSON.stringify(recoveryData));
    } catch (error) {
      console.error('Error saving for recovery:', error);
    }
  }

  /**
   * Verifica si hay una API para recuperar
   */
  public hasRecoverableApi(): boolean {
    try {
      const stored = localStorage.getItem(this.RECOVERY_KEY);
      return stored !== null && stored !== undefined;
    } catch (error) {
      return false;
    }
  }

  /**
   * Carga la API de recuperación
   */
  public loadRecovery(): RecoveryApiDefinition | null {
    try {
      const stored = localStorage.getItem(this.RECOVERY_KEY);
      if (!stored) {
        return null;
      }
      
      const parsed = JSON.parse(stored);
      return {
        ...parsed,
        savedForRecovery: parsed.savedForRecovery ? new Date(parsed.savedForRecovery) : new Date()
      };
    } catch (error) {
      console.error('Error loading recovery API:', error);
      return null;
    }
  }

  /**
   * Limpia la API de recuperación
   */
  public clearRecovery(): void {
    try {
      localStorage.removeItem(this.RECOVERY_KEY);
    } catch (error) {
      console.error('Error clearing recovery API:', error);
    }
  }
}

// Instancia singleton
export const storageService = new StorageService(); 
