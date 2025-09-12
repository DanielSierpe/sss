import React, { useState, useEffect, useRef } from 'react';
import { ExecutorService } from '../services/ExecutorService';
import type { AppStatus } from '../services/ExecutorService';
import './Generador.css';

const Generador: React.FC = () => {

  
  // Estados para la búsqueda y selección
  const [searchQuery, setSearchQuery] = useState('');
  const [availableApps, setAvailableApps] = useState<string[]>([]);
  const [filteredApps, setFilteredApps] = useState<string[]>([]);
  const [selectedApp, setSelectedApp] = useState<string>('');
  const [showDropdown, setShowDropdown] = useState(false);
  const [isSearching, setIsSearching] = useState(false);
  
  // Estados para el modal de logs
  const [showLogModal, setShowLogModal] = useState(false);
  const [appStatus, setAppStatus] = useState<AppStatus | null>(null);
  const [isExecuting, setIsExecuting] = useState(false);
  const [executionMessage, setExecutionMessage] = useState('');
  const [isDownloading, setIsDownloading] = useState(false);
  
  // Referencias
  const searchInputRef = useRef<HTMLInputElement>(null);
  const dropdownRef = useRef<HTMLDivElement>(null);
  const logModalRef = useRef<HTMLDivElement>(null);
  
  // Polling para actualizar el estado
  const [pollingInterval, setPollingInterval] = useState<NodeJS.Timeout | null>(null);

  // Cargar aplicaciones disponibles al montar el componente
  useEffect(() => {
    loadAvailableApps();
  }, []);

  // Filtrar aplicaciones cuando cambia la búsqueda
  useEffect(() => {
    if (searchQuery.trim()) {
      const filtered = availableApps.filter(app => 
        app.toLowerCase().includes(searchQuery.toLowerCase())
      );
      setFilteredApps(filtered);
    } else {
      setFilteredApps(availableApps);
    }
  }, [searchQuery, availableApps]);

  // Limpiar polling al desmontar
  useEffect(() => {
    return () => {
      if (pollingInterval) {
        clearInterval(pollingInterval);
      }
    };
  }, [pollingInterval]);

  // Cerrar dropdown al hacer clic fuera
  useEffect(() => {
    const handleClickOutside = (event: MouseEvent) => {
      if (dropdownRef.current && !dropdownRef.current.contains(event.target as Node)) {
        setShowDropdown(false);
      }
      if (logModalRef.current && !logModalRef.current.contains(event.target as Node)) {
        setShowLogModal(false);
        stopPolling();
      }
    };

    document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, []);

  const loadAvailableApps = async () => {
    try {
      setIsSearching(true);
      const apps = await ExecutorService.searchApps('');
      setAvailableApps(apps);
      setFilteredApps(apps);
    } catch (error) {
      console.error('Error cargando aplicaciones:', error);
    } finally {
      setIsSearching(false);
    }
  };

  const handleSearchChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setSearchQuery(value);
    setShowDropdown(true);
    
    if (value !== selectedApp) {
      setSelectedApp('');
    }
  };

  const handleAppSelect = (app: string) => {
    setSelectedApp(app);
    setSearchQuery(app);
    setShowDropdown(false);
  };

  const handleGenerateComponent = async () => {
    if (!selectedApp) return;

    try {
      setIsExecuting(true);
      setExecutionMessage('Iniciando generación del componente...');
      setShowLogModal(true);

      // Ejecutar el script
      const result = await ExecutorService.executeScript(selectedApp);
      
      if (result.success) {
        setExecutionMessage('Componente iniciado correctamente. Monitoreando estado...');
        startPolling();
      } else {
        setExecutionMessage(`Error: ${result.message}`);
        setIsExecuting(false);
      }
    } catch (error) {
      console.error('Error generando componente:', error);
      setExecutionMessage(`Error: ${error instanceof Error ? error.message : 'Error desconocido'}`);
      setIsExecuting(false);
    }
  };

  const startPolling = () => {
    console.log('Iniciando polling...');
    const interval = setInterval(async () => {
      try {
        const status = await ExecutorService.getAppStatus(selectedApp);
        if (status) {
          setAppStatus(status);
          



          if (status.status === 'completed') {
            console.log('Status completed detectado:', status);
            setExecutionMessage('Componente generado exitosamente y listo para descarga');
            setIsExecuting(false);
            setAppStatus(status);
            stopPolling();
            return; 
          } else if (status.status === 'running') {
            setExecutionMessage('Componente en ejecución...');
          } else if (status.status === 'error') {
            setExecutionMessage('Error en la generación del componente');
            setIsExecuting(false);
            setAppStatus(status);
            stopPolling();
            return; 
          }
        }
      } catch (error) {
        console.error('Error en polling:', error);
      }
    }, 2000); 

    setPollingInterval(interval);
  };

  const stopPolling = () => {
    if (pollingInterval) {
      console.log('Deteniendo polling...');
      clearInterval(pollingInterval);
      setPollingInterval(null);
    }
  };

  const handleDownload = async () => {
    if (!selectedApp) return;

    try {
      setIsDownloading(true);
      const result = await ExecutorService.downloadGeneratedProject(selectedApp);
      
      if (result.success) {
        setExecutionMessage('Proyecto descargado exitosamente');
      } else {
        setExecutionMessage(`Error en descarga: ${result.message}`);
      }
    } catch (error) {
      console.error('Error descargando proyecto:', error);
      setExecutionMessage(`Error en descarga: ${error instanceof Error ? error.message : 'Error desconocido'}`);
    } finally {
      setIsDownloading(false);
    }
  };

  const closeLogModal = () => {
    setShowLogModal(false);
    stopPolling();
    setIsExecuting(false);
    setExecutionMessage('');
    setAppStatus(null);
    setIsDownloading(false);
  };

  return (
    <div className="generador-content">
      <div className="generador-inner">
        <h2>Generación de Componentes</h2>
        

         {/* <div className="auth-info" style={{ 
           background: '#f8f9fa', 
           padding: '16px', 
           borderRadius: '8px', 
           marginBottom: '24px',
           border: '1px solid #e9ecef'
         }}>
           <h3 style={{ margin: '0 0 12px 0', color: '#495057' }}>Estado de Autenticación</h3>
           <div style={{ display: 'flex', flexDirection: 'column', gap: '8px' }}>
             <div><strong>Autenticado:</strong> {isAuthenticated ? 'Sí' : 'No'}</div>
             <div><strong>Cargando:</strong> {isLoading ? 'Sí' : 'No'}</div>
             <div><strong>Token:</strong> {token ? `${token.substring(0, 20)}...` : 'No disponible'}</div>
           </div>
         </div> */}
        
        <form className="generador-form" autoComplete="off">
          <div className="form-group search-container">
            <label htmlFor="openapi-name">Nombre openAPI</label>
            <div className="search-wrapper" ref={dropdownRef}>
              <input
                ref={searchInputRef}
                type="text"
                id="openapi-name"
                name="openapi-name"
                placeholder={isSearching ? "Cargando aplicaciones..." : "Buscar aplicación..."}
                className="search-input"
                value={searchQuery}
                onChange={handleSearchChange}
                onFocus={() => setShowDropdown(true)}
                autoComplete="off"
                disabled={isSearching}
              />
              {showDropdown && filteredApps.length > 0 && (
                <div className="search-dropdown">
                  {filteredApps.map((app, index) => (
                    <div
                      key={index}
                      className="dropdown-item"
                      onClick={() => handleAppSelect(app)}
                    >
                      {app}
                    </div>
                  ))}
                </div>
              )}
            </div>
            {selectedApp && (
              <div className="selected-app">
                <span className="selected-label">Aplicación seleccionada:</span>
                <span className="selected-value">{selectedApp}</span>
              </div>
            )}
          </div>
          <div className="button-group">
            {/* <button type="button" className="btn validar">Validar</button> */}
            <button 
              type="button" 
              className={`btn generar ${selectedApp ? 'enabled' : 'disabled'}`}
              onClick={handleGenerateComponent}
              disabled={!selectedApp}
            >
              Generar componente
            </button>
          </div>
        </form>
        {/* <div className="tabla-container">
          <table className="tabla-generador">
            <thead>
              <tr>
                <th>Code</th>
                <th>path</th>
                <th>Message</th>
                <th>Severity</th>
                <th>range</th>
              </tr>
            </thead>
            <tbody>

            </tbody>
          </table>
        </div> */}
      </div>

      {/* Modal de Logs */}
      {showLogModal && (() => {
        console.log('Renderizando modal, appStatus:', appStatus, 'showLogModal:', showLogModal);
        return true;
      })() && (
        <div className="modal-overlay">
          <div className="log-modal" ref={logModalRef}>
            <div className="modal-header">
              <h3>Generación de Componente: {selectedApp}</h3>
              <div className="header-actions">
                <button className="close-button" onClick={closeLogModal}>×</button>
              </div>
            </div>
            
            <div className="modal-content">
              <div className="execution-status">
                <div className="status-message">{executionMessage}</div>
                {appStatus && (
                  <div className="app-status">
                    <div className="status-item">
                      <span className="status-label">Estado:</span>
                      <span className={`status-value status-${appStatus.status?.toLowerCase() || 'unknown'}`}>
                        {appStatus.status || 'Desconocido'}
                      </span>
                    </div>
                    <div className="status-item">
                      <span className="status-label">Versión:</span>
                      <span className="status-value">{appStatus.version}</span>
                    </div>
                    <div className="status-item">
                      <span className="status-label">Inicio:</span>
                      <span className="status-value">
                        {new Date(appStatus.startTime).toLocaleString()}
                      </span>
                    </div>
                  </div>
                )}
              </div>


              {isExecuting && appStatus?.status !== 'completed' && (
                <div className="loading-indicator">
                  <div className="spinner"></div>
                  <span>Monitoreando estado...</span>
                </div>
              )}
            </div>

            <div className="modal-footer">
              {appStatus?.status === 'completed' && !isExecuting && (
                <button 
                  className="btn btn-download" 
                  onClick={() => {
                    console.log('Botón descarga clickeado, appStatus:', appStatus);
                    handleDownload();
                  }}
                  disabled={isDownloading}
                >
                  {isDownloading ? 'Descargando...' : 'Descargar Proyecto'}
                </button>
              )}
              <button className="btn btn-secondary" onClick={closeLogModal}>
                Cerrar
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default Generador; 
