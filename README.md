import React, { useState, useRef } from 'react';
import { storageService } from '../services/StorageService';
import { apiDefinitionFileService, ApiDefinitionFileService } from '../services/ApiDefinitionFileService';
import type { ApiDefinition } from '../services/StorageService';
import EditorVisual from './EditorVisual';
import './DisenoOpenAPI.css';

// Templates para APIs vacías
const EMPTY_API_30 = JSON.stringify({
  openapi: "3.0.2",
  info: {
    title: "API Sin Título",
    version: "1.0.0"
  },
  paths: {}
}, null, 2);

const EMPTY_API_20 = JSON.stringify({
  swagger: "2.0",
  info: {
    title: "API Sin Título",
    version: "1.0.0"
  },
  paths: {}
}, null, 2);

interface DisenoOpenAPIProps {
  onOpen?: (api: any) => void;
}

const DisenoOpenAPI: React.FC<DisenoOpenAPIProps> = ({}) => {
  const [dragging, setDragging] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [currentApi, setCurrentApi] = useState<any>(null);
  const [showEditor, setShowEditor] = useState(false);
  const fileInputRef = useRef<HTMLInputElement>(null);

  const hasRecoverableApi = (): boolean => {
    return storageService.hasRecoverableApi();
  };

  const recoverApi = (): void => {
    console.info("[DisenoOpenAPI] Recovering an API definition that was in-progress");
    try {
      const recoveredApi = storageService.loadRecovery();
      if (recoveredApi) {
        setCurrentApi(recoveredApi.spec);
        setShowEditor(true);
        // Limpiar la recuperación después de usarla
        storageService.clearRecovery();
      }
    } catch (error) {
      console.error('Error recovering API:', error);
      setError('Error al recuperar la API');
    }
  };

  const createNewApi = (version: string = "3.0.2"): void => {
    setError(null);
    let api: any;
    
    if (version === "2.0") {
      api = JSON.parse(EMPTY_API_20);
    } else {
      api = JSON.parse(EMPTY_API_30);
    }
    
    // Validar la nueva API
    const validation = apiDefinitionFileService.validateOpenApiSpec(api);
    if (!validation || !validation.isValid) {
      const errorMessage = validation?.errors?.join(', ') || 'Error de validación desconocido';
      setError(`Especificación de API inválida: ${errorMessage}`);
      return;
    }
    
    // Guardar la nueva API
    const apiDefinition: ApiDefinition = {
      spec: api,
      name: "API Sin Título",
      createdOn: new Date()
    };
    
    try {
      storageService.save(apiDefinition);
      storageService.saveForRecovery(apiDefinition);
      
      setCurrentApi(api);
      setShowEditor(true);
    } catch (error) {
      console.error('Error saving new API:', error);
      setError('Error al crear nueva API');
    }
  };

  const openExistingApi = (): void => {
    setError(null);
    if (fileInputRef.current) {
      fileInputRef.current.click();
    }
  };

  const onFileOpened = (event: React.ChangeEvent<HTMLInputElement>): void => {
    setError(null);
    const file = event.target.files?.[0];
    
    if (!file) return;

    const fileFormat = ApiDefinitionFileService.getFileFormat(file);
    if (!fileFormat) {
      setError("Solo se admiten archivos JSON y YAML.");
      return;
    }

    loadFile(file);
  };

  const loadFile = async (file: File): Promise<void> => {
    setError(null);
    
    try {
      // Usar el nuevo servicio para cargar el archivo
      const spec = await apiDefinitionFileService.load(file);
      
      // Validar la especificación OpenAPI
      const validation = apiDefinitionFileService.validateOpenApiSpec(spec);
      if (!validation || !validation.isValid) {
        const errorMessage = validation?.errors?.join(', ') || 'Error de validación desconocido';
        setError(`Especificación OpenAPI inválida: ${errorMessage}`);
        return;
      }
      
      // Guardar la API cargada
      const apiDefinition: ApiDefinition = {
        spec: spec,
        name: file.name,
        createdOn: new Date()
      };
      
      storageService.save(apiDefinition);
      storageService.saveForRecovery(apiDefinition);
      
      setCurrentApi(spec);
      setShowEditor(true);
    } catch (e) {
      console.log(e);
      setError(e instanceof Error ? e.message : 'Error al cargar archivo');
    }
  };

  const onDragOver = (event: React.DragEvent): void => {
    if (!dragging) {
      setDragging(true);
    }
    event.preventDefault();
  };

  const onDrop = async (event: React.DragEvent): Promise<void> => {
    setDragging(false);
    event.preventDefault();

    const items = event.dataTransfer.items;
    if (!items || items.length < 1) {
      return;
    }

    const item = items[0];
    const file = item.getAsFile();

    if (file) {
      await loadFile(file);
    } else {
      setError("Solo se admiten archivos.");
    }
  };

  const onDragEnd = (): void => {
    setDragging(false);
  };

  const handleBackToWelcome = () => {
    setShowEditor(false);
    setCurrentApi(null);
    setError(null);
  };

  // Si estamos en el editor, mostrar el EditorVisual
  if (showEditor && currentApi) {
    return (
      <div className="diseno-openapi-content">
        <div className="diseno-openapi-inner editor-mode">
          <EditorVisual initialApi={currentApi} onBack={handleBackToWelcome} />
        </div>
      </div>
    );
  }

  // Pantalla de bienvenida
  return (
    <div className="diseno-openapi-content">
      <div className="diseno-openapi-inner">
        <div 
          className={`blank-slate-pf ${dragging ? 'dragging' : ''}`}
          onDragOver={onDragOver}
          onDrop={onDrop}
          onDragEnd={onDragEnd}
          onDragExit={onDragEnd}
          onDragLeave={onDragEnd}
        >
          <div className="blank-slate-pf-icon">
            <span className="pficon pficon-add-circle-o"></span>
          </div>
          
          <h1>Diseño de OpenAPI</h1>
          
          <p>
            Crea y edita especificaciones OpenAPI de manera visual e intuitiva. 
          </p>
          
          <p>
            Comienza creando una nueva API o carga una existente para empezar a trabajar.
          </p>
          
          <div className="blank-slate-pf-main-action">
            <div className="btn-group">
              <button 
                type="button" 
                className="btn btn-lg btn-primary" 
                onClick={() => createNewApi()}
              >
                Crear Nueva API
              </button>
            </div>
            
            <span>&nbsp;</span>
            
            <button 
              className="btn btn-default btn-lg btn-load" 
              onClick={openExistingApi}
            >
              Cargar API Existente
            </button>

            {hasRecoverableApi() && (
              <div className="recovery-notice">
                <div className="alert alert-warning">
                  <span className="pficon pficon-warning-triangle-o"></span>
                  <strong>¡API en Progreso Detectada!</strong>
                  <span>&nbsp;</span>
                  <span>Parece que tienes una API sin terminar.</span>
                  <span>&nbsp;</span>
                  <a onClick={recoverApi} className="alert-link">Recuperar mi API</a>
                </div>
              </div>
            )}

            <div className="dnd-notification">
              O arrastra y suelta un archivo sobre la página...
            </div>
            
            {error && (
              <div>
                <div className="alert alert-danger">
                  <span className="pficon pficon-error-circle-o"></span>
                  <strong>¡Error Detectado!</strong>
                  <span>&nbsp;</span>
                  <span>{error}</span>
                </div>
              </div>
            )}
          </div>
          
          <div className="hidden">
            <input 
              ref={fileInputRef}
              type="file" 
              id="load-file" 
              name="load-file" 
              onChange={onFileOpened}
              accept=".json,.yaml,.yml"
            />
          </div>
        </div>
      </div>
    </div>
  );
};

export default DisenoOpenAPI; 
