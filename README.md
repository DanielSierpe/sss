import yaml from 'js-yaml';

export interface FileFormat {
  type: 'json' | 'yaml' | 'yml';
  extension: string;
}

export class ApiDefinitionFileService {

  /**
   * Detecta el formato del archivo basado en su extensión
   */
  public static getFileFormat(file: File): FileFormat | null {
    const extension = file.name.split('.').pop()?.toLowerCase();

    if (!extension) {
      return null;
    }

    switch (extension) {
      case 'json':
        return { type: 'json', extension: 'json' };
      case 'yaml':
      case 'yml':
        return { type: 'yaml', extension };
      default:
        return null;
    }
  }

  /**
   * Carga un archivo y retorna su contenido parseado
   */
  public async load(file: File): Promise<any> {
    const fileFormat = ApiDefinitionFileService.getFileFormat(file);

    if (!fileFormat) {
      throw new Error('Formato de archivo no soportado. Solo se admiten archivos JSON y YAML.');
    }

    try {
      const content = await this.readFileContent(file);
      return this.parseContent(content, fileFormat.type);
    } catch (error) {
      console.error('Error loading file:', error);
      throw new Error(`Error al cargar archivo: ${error instanceof Error ? error.message : 'Error desconocido'}`);
    }
  }

  /**
   * Lee el contenido del archivo como texto
   */
  private async readFileContent(file: File): Promise<string> {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();

      reader.onload = (event) => {
        const content = event.target?.result as string;
        if (content) {
          resolve(content);
        } else {
          reject(new Error('Error al leer el contenido del archivo'));
        }
      };

      reader.onerror = () => {
        reject(new Error('Error al leer el archivo'));
      };

      reader.readAsText(file);
    });
  }

  /**
   * Parsea el contenido según el formato usando js-yaml para YAML
   */
  private parseContent(content: string, format: 'json' | 'yaml' | 'yml'): any {
    try {
      if (format === 'json') {
        return JSON.parse(content);
      } else {
        // Usar js-yaml para parsear YAML
        return yaml.load(content);
      }
    } catch (error) {
      const formatName = format === 'json' ? 'JSON' : 'YAML';
      throw new Error(`Error al parsear contenido ${formatName}: ${error instanceof Error ? error.message : 'Formato inválido'}`);
    }
  }

  /**
   * Detecta el tipo de archivo basado en su contenido
   */
  private detectFileType(spec: any): { type: string; description: string } {
    if (!spec || typeof spec !== 'object') {
      return { type: 'unknown', description: 'Estructura de archivo inválida' };
    }

    const keys = Object.keys(spec);
    
    // Detectar OpenAPI 2.0
    if (spec.swagger) {
      return { type: 'openapi-2.0', description: 'Especificación OpenAPI 2.0 (Swagger)' };
    }
    
    // Detectar OpenAPI 3.x
    if (spec.openapi) {
      return { type: 'openapi-3.x', description: `Especificación OpenAPI ${spec.openapi}` };
    }

    // Detectar otros tipos comunes
    if (keys.includes('paths') && keys.includes('info')) {
      return { type: 'api-like', description: 'Estructura similar a API pero falta el campo swagger/openapi' };
    }

    if (keys.includes('components') && keys.includes('paths')) {
      return { type: 'api-like', description: 'Estructura similar a API pero falta el campo swagger/openapi' };
    }

    // Detectar respuestas HTTP
    if (keys.includes('status') || keys.includes('code') || keys.includes('message')) {
      return { type: 'http-response', description: 'Objeto de respuesta HTTP' };
    }

    // Detectar arrays (podrían ser listas de APIs)
    if (Array.isArray(spec)) {
      return { type: 'array', description: 'Array de objetos (no es una especificación de API única)' };
    }

    // Detectar objetos con claves numéricas (podrían ser códigos de estado)
    const numericKeys = keys.filter(key => !isNaN(Number(key)));
    if (numericKeys.length > 0 && numericKeys.length === keys.length) {
      return { type: 'status-codes', description: 'Objeto con claves numéricas (posiblemente códigos de estado HTTP)' };
    }

    return { type: 'unknown', description: `Formato desconocido con claves: ${keys.join(', ')}` };
  }

  /**
   * Valida si el contenido es una especificación OpenAPI válida
   */
  public validateOpenApiSpec(spec: any): { isValid: boolean; errors: string[] } {
    const errors: string[] = [];

    // Validaciones básicas
    if (!spec) {
      errors.push('La especificación está vacía');
      return { isValid: false, errors };
    }

    if (typeof spec !== 'object') {
      errors.push('La especificación debe ser un objeto JSON');
      return { isValid: false, errors };
    }

    // Detectar el tipo de archivo
    const fileType = this.detectFileType(spec);

    // Verificar si es OpenAPI 2.0 (Swagger)
    if (spec.swagger) {
      if (spec.swagger !== '2.0') {
        errors.push('OpenAPI 2.0: la versión de swagger debe ser "2.0"');
      }
      if (!spec.info) {
        errors.push('OpenAPI 2.0: Falta la sección info');
      }
      if (!spec.paths) {
        errors.push('OpenAPI 2.0: Falta la sección paths');
      }
      if (spec.info && typeof spec.info !== 'object') {
        errors.push('OpenAPI 2.0: info debe ser un objeto');
      }
      if (spec.paths && typeof spec.paths !== 'object') {
        errors.push('OpenAPI 2.0: paths debe ser un objeto');
      }
    }
    // Verificar si es OpenAPI 3.x
    else if (spec.openapi) {
      const version = spec.openapi;
      if (!version.startsWith('3.')) {
        errors.push(`OpenAPI 3.x: la versión openapi debe comenzar con "3.", se obtuvo "${version}"`);
      }
      if (!spec.info) {
        errors.push('OpenAPI 3.x: Falta la sección info');
      }
      if (!spec.paths) {
        errors.push('OpenAPI 3.x: Falta la sección paths');
      }
      if (spec.info && typeof spec.info !== 'object') {
        errors.push('OpenAPI 3.x: info debe ser un objeto');
      }
      if (spec.paths && typeof spec.paths !== 'object') {
        errors.push('OpenAPI 3.x: paths debe ser un objeto');
      }
    } else {
      // El archivo no es OpenAPI válido
      errors.push(`Este archivo parece ser un ${fileType.description}`);
      errors.push('Los archivos OpenAPI deben tener "swagger" (para 2.0) u "openapi" (para 3.x) como campo raíz');
      
      // Dar sugerencias específicas según el tipo detectado
      if (fileType.type === 'http-response') {
        errors.push('Esto parece ser una respuesta HTTP. Por favor proporciona un archivo de especificación OpenAPI.');
      } else if (fileType.type === 'status-codes') {
        errors.push('Esto parece ser un mapeo de códigos de estado. Por favor proporciona un archivo de especificación OpenAPI.');
      } else if (fileType.type === 'array') {
        errors.push('Esto es un array. Por favor proporciona un objeto de especificación OpenAPI único.');
      } else if (fileType.type === 'api-like') {
        errors.push('Este archivo tiene estructura similar a API pero falta el campo requerido "swagger" u "openapi".');
      }
    }

    return {
      isValid: errors.length === 0,
      errors
    };
  }

  /**
   * Convierte una especificación a formato JSON
   */
  public toJson(spec: any): string {
    return JSON.stringify(spec, null, 2);
  }

  /**
   * Convierte una especificación a formato YAML usando js-yaml
   */
  public toYaml(spec: any): string {
    return yaml.dump(spec, {
      indent: 2,
      lineWidth: -1,
      noRefs: true
    });
  }
}

export const apiDefinitionFileService = new ApiDefinitionFileService(); 
