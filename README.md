import React, { useState, useEffect, useRef } from 'react';
import { editorService } from '../services/EditorService';
import { storageService } from '../services/StorageService';
import type { ApiDefinition } from '../services/StorageService';
import './EditorVisual.css';
interface EditorVisualProps {
  initialApi?: any;
  onBack?: () => void;
}
const EditorVisual: React.FC<EditorVisualProps> = ({ initialApi, onBack }) => {
  const [currentView, setCurrentView] = useState<'design' | 'source'>('design');
  const [isDirty, setIsDirty] = useState(false);
  const [errors, setErrors] = useState<string[]>([]);
  const [warnings, setWarnings] = useState<string[]>([]);
  const [sourceCode, setSourceCode] = useState('');
  const [showSaveDialog, setShowSaveDialog] = useState(false);
  const [showExportDialog, setShowExportDialog] = useState(false);
  const [exportFormat, setExportFormat] = useState<'json' | 'yaml'>('json');
  const [exportContent, setExportContent] = useState('');
  const [expandedPaths, setExpandedPaths] = useState<Set<string>>(new Set());
  const [expandedComponents, setExpandedComponents] = useState<Set<string>>(new Set());
  
  const sourceEditorRef = useRef<HTMLTextAreaElement>(null);
  // Inicializar el editor cuando se monta el componente
  useEffect(() => {
    if (initialApi) {
      editorService.initialize(initialApi);
      updateSourceCode();
    }
  }, [initialApi]);
  // Actualizar el estado del editor
  useEffect(() => {
    const updateEditorState = () => {
      const state = editorService.getState();
      setIsDirty(state.isDirty);
      setErrors(state.errors);
      setWarnings(state.warnings);
    };
    // Actualizar estado cada segundo
    const interval = setInterval(updateEditorState, 1000);
    return () => clearInterval(interval);
  }, []);
  const updateSourceCode = () => {
    const api = editorService.getCurrentApi();
    if (api) {
      setSourceCode(JSON.stringify(api, null, 2));
    }
  };
  const handleViewChange = (view: 'design' | 'source') => {
    setCurrentView(view);
    editorService.setView(view);
  };
  const handleSave = () => {
    try {
      const api = editorService.getCurrentApi();
      if (api) {
        const apiDefinition: ApiDefinition = {
          spec: api,
          name: 'API Sin Título',
          createdOn: new Date()
        };
        
        storageService.save(apiDefinition);
        editorService.markAsSaved();
        setShowSaveDialog(false);
      }
    } catch (error) {
      console.error('Error saving API:', error);
      setErrors(['Error al guardar la API']);
    }
  };
  const handleExport = () => {
    try {
      let content = '';
      if (exportFormat === 'json') {
        content = editorService.exportAsJson();
      } else {
        content = editorService.exportAsYaml();
      }
      
      setExportContent(content);
      setShowExportDialog(true);
    } catch (error) {
      console.error('Error exporting API:', error);
      setErrors(['Error al exportar la API']);
    }
  };
  const handleDownload = () => {
    try {
      const content = exportContent;
      const fileName = `api-${new Date().toISOString().split('T')[0]}.${exportFormat}`;
      const blob = new Blob([content], { type: exportFormat === 'json' ? 'application/json' : 'text/yaml' });
      const url = URL.createObjectURL(blob);
      
      const link = document.createElement('a');
      link.href = url;
      link.download = fileName;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      URL.revokeObjectURL(url);
      
      alert('¡Archivo descargado exitosamente!');
    } catch (error) {
      console.error('Error downloading file:', error);
      setErrors(['Error al descargar el archivo']);
    }
  };
  const handleUndo = () => {
    if (editorService.undo()) {
      updateSourceCode();
    }
  };
  const handleRedo = () => {
    if (editorService.redo()) {
      updateSourceCode();
    }
  };
  const handleSourceCodeChange = (event: React.ChangeEvent<HTMLTextAreaElement>) => {
    setSourceCode(event.target.value);
    try {
      const parsedApi = JSON.parse(event.target.value);
      editorService.initialize(parsedApi);
    } catch (error) {
      // Error de parsing, no actualizar el editor
    }
  };
  const handleSourceCodeBlur = () => {
    try {
      const parsedApi = JSON.parse(sourceCode);
      editorService.initialize(parsedApi);
      setErrors([]);
    } catch (error) {
      setErrors(['Formato JSON inválido']);
    }
  };
  const handleCreatePath = () => {
    const pathName = prompt('Ingresa el nombre del path (ej: /usuarios):');
    if (pathName) {
      editorService.executeCommand({
        type: 'create',
        path: `paths.${pathName}`,
        value: {
          get: {
            summary: 'Obtener datos',
            responses: {
              '200': {
                description: 'Respuesta exitosa'
              }
            }
          }
        }
      });
      updateSourceCode();
    }
  };
  const handleCreateComponent = () => {
    const componentName = prompt('Ingresa el nombre del componente:');
    if (componentName) {
      editorService.executeCommand({
        type: 'create',
        path: `components.schemas.${componentName}`,
        value: {
          type: 'object',
          properties: {}
        }
      });
      updateSourceCode();
    }
  };
  const togglePathExpansion = (path: string) => {
    const newExpandedPaths = new Set(expandedPaths);
    if (newExpandedPaths.has(path)) {
      newExpandedPaths.delete(path);
    } else {
      newExpandedPaths.add(path);
    }
    setExpandedPaths(newExpandedPaths);
  };
  const toggleComponentExpansion = (component: string) => {
    const newExpandedComponents = new Set(expandedComponents);
    if (newExpandedComponents.has(component)) {
      newExpandedComponents.delete(component);
    } else {
      newExpandedComponents.add(component);
    }
    setExpandedComponents(newExpandedComponents);
  };
  const getPathContent = (path: string) => {
    const pathData = editorService.getValueAtPath(`paths.${path}`);
    return JSON.stringify(pathData, null, 2);
  };
  const getComponentContent = (component: string) => {
    const componentData = editorService.getValueAtPath(`components.schemas.${component}`);
    return JSON.stringify(componentData, null, 2);
  };
  const handleBack = () => {
    if (onBack) {
      onBack();
    }
  };
  return (
    <div className="editor-visual-content">
      <div className="editor-visual-inner">
        {/* Header del editor */}
        <div className="editor-header">
          <div className="editor-title">
            <button className="btn btn-back" onClick={handleBack}>
              ← Volver
            </button>
            <h2>Editor de API</h2>
            {isDirty && <span className="dirty-indicator">*</span>}
          </div>
          
          <div className="editor-actions">
            <button 
              className="btn btn-secondary" 
              onClick={handleUndo}
              disabled={!editorService.canUndo()}
              title="Deshacer"
            >
              Deshacer
            </button>
            <button 
              className="btn btn-secondary" 
              onClick={handleRedo}
              disabled={!editorService.canRedo()}
              title="Rehacer"
            >
              Rehacer
            </button>
            <button className="btn btn-primary" onClick={() => setShowSaveDialog(true)}>
              Guardar
            </button>
            <button className="btn btn-secondary" onClick={handleExport}>
              Exportar
            </button>
          </div>
        </div>
        {/* Pestañas */}
        <div className="editor-tabs">
          <button
            className={`tab ${currentView === 'design' ? 'active' : ''}`}
            onClick={() => handleViewChange('design')}
          >
            Diseño
          </button>
          <button
            className={`tab ${currentView === 'source' ? 'active' : ''}`}
            onClick={() => handleViewChange('source')}
          >
            Código Fuente
          </button>
        </div>
        {/* Contenido principal */}
        <div className="editor-main-content">
          {currentView === 'design' ? (
            <div className="design-view">
              <div className="design-toolbar">
                <button className="btn btn-sm btn-primary" onClick={handleCreatePath}>
                  Agregar Path
                </button>
                <button className="btn btn-sm btn-primary" onClick={handleCreateComponent}>
                  Agregar Componente
                </button>
              </div>
              
              <div className="design-content">
                <div className="api-info-section">
                  <h3>Información de la API</h3>
                  <div className="field">
                    <label>Título:</label>
                    <input 
                      type="text" 
                      value={editorService.getValueAtPath('info.title') || ''}
                      onChange={(e) => {
                        editorService.executeCommand({
                          type: 'update',
                          path: 'info.title',
                          value: e.target.value,
                          oldValue: editorService.getValueAtPath('info.title')
                        });
                        updateSourceCode();
                      }}
                    />
                  </div>
                  <div className="field">
                    <label>Versión:</label>
                    <input 
                      type="text" 
                      value={editorService.getValueAtPath('info.version') || ''}
                      onChange={(e) => {
                        editorService.executeCommand({
                          type: 'update',
                          path: 'info.version',
                          value: e.target.value,
                          oldValue: editorService.getValueAtPath('info.version')
                        });
                        updateSourceCode();
                      }}
                    />
                  </div>
                </div>
                <div className="paths-section">
                  <h3>Rutas (Paths)</h3>
                  <div className="paths-list">
                    {editorService.getValueAtPath('paths') ? 
                      Object.keys(editorService.getValueAtPath('paths')).map(path => (
                        <div key={path} className="path-item">
                          <div className="path-header" onClick={() => togglePathExpansion(path)}>
                            <span className="path-name">{path}</span>
                            <span className="expand-icon">
                              {expandedPaths.has(path) ? '▼' : '▶'}
                            </span>
                          </div>
                          {expandedPaths.has(path) && (
                            <div className="path-content">
                              <pre className="json-content">
                                {getPathContent(path)}
                              </pre>
                            </div>
                          )}
                          <button 
                            className="btn btn-sm btn-danger"
                            onClick={() => {
                              editorService.executeCommand({
                                type: 'delete',
                                path: `paths.${path}`
                              });
                              updateSourceCode();
                            }}
                          >
                            Eliminar
                          </button>
                        </div>
                      )) : 
                      <p>No hay rutas definidas</p>
                    }
                  </div>
                </div>
                <div className="components-section">
                  <h3>Componentes</h3>
                  <div className="components-list">
                    {editorService.getValueAtPath('components.schemas') ? 
                      Object.keys(editorService.getValueAtPath('components.schemas')).map(schema => (
                        <div key={schema} className="component-item">
                          <div className="component-header" onClick={() => toggleComponentExpansion(schema)}>
                            <span className="component-name">{schema}</span>
                            <span className="expand-icon">
                              {expandedComponents.has(schema) ? '▼' : '▶'}
                            </span>
                          </div>
                          {expandedComponents.has(schema) && (
                            <div className="component-content">
                              <pre className="json-content">
                                {getComponentContent(schema)}
                              </pre>
                            </div>
                          )}
                          <button 
                            className="btn btn-sm btn-danger"
                            onClick={() => {
                              editorService.executeCommand({
                                type: 'delete',
                                path: `components.schemas.${schema}`
                              });
                              updateSourceCode();
                            }}
                          >
                            Eliminar
                          </button>
                        </div>
                      )) : 
                      <p>No hay componentes definidos</p>
                    }
                  </div>
                </div>
              </div>
            </div>
          ) : (
            <div className="source-view">
              <textarea
                ref={sourceEditorRef}
                className="source-editor"
                value={sourceCode}
                onChange={handleSourceCodeChange}
                onBlur={handleSourceCodeBlur}
                placeholder="Ingresa tu especificación OpenAPI aquí..."
              />
            </div>
          )}
        </div>
        {/* Mensajes de error y advertencia */}
        {(errors.length > 0 || warnings.length > 0) && (
          <div className="editor-messages">
            {errors.map((error, index) => (
              <div key={index} className="message error">
                <span className="icon">⚠️</span>
                <span>{error}</span>
              </div>
            ))}
            {warnings.map((warning, index) => (
              <div key={index} className="message warning">
                <span className="icon">ℹ️</span>
                <span>{warning}</span>
              </div>
            ))}
          </div>
        )}
        {/* Diálogo de guardar */}
        {showSaveDialog && (
          <div className="modal-overlay">
            <div className="modal">
              <h3>Guardar API</h3>
              <p>¿Estás seguro de que quieres guardar la API actual?</p>
              <div className="modal-actions">
                <button className="btn btn-primary" onClick={handleSave}>
                  Guardar
                </button>
                <button className="btn btn-secondary" onClick={() => setShowSaveDialog(false)}>
                  Cancelar
                </button>
              </div>
            </div>
          </div>
        )}
        {/* Diálogo de exportar */}
        {showExportDialog && (
          <div className="modal-overlay">
            <div className="modal">
              <h3>Exportar API</h3>
              <div className="export-options">
                <label>
                  <input
                    type="radio"
                    value="json"
                    checked={exportFormat === 'json'}
                    onChange={(e) => setExportFormat(e.target.value as 'json' | 'yaml')}
                  />
                  JSON
                </label>
                <label>
                  <input
                    type="radio"
                    value="yaml"
                    checked={exportFormat === 'yaml'}
                    onChange={(e) => setExportFormat(e.target.value as 'json' | 'yaml')}
                  />
                  YAML
                </label>
              </div>
              <textarea
                className="export-content"
                value={exportContent}
                readOnly
                rows={10}
              />
              <div className="modal-actions">
                <button 
                  className="btn btn-primary"
                  onClick={() => {
                    navigator.clipboard.writeText(exportContent);
                    alert('¡Copiado al portapapeles!');
                  }}
                >
                  Copiar al Portapapeles
                </button>
                <button className="btn btn-secondary" onClick={handleDownload}>
                  Descargar
                </button>
                <button className="btn btn-secondary" onClick={() => setShowExportDialog(false)}>
                  Cerrar
                </button>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};
export default EditorVisual; 
