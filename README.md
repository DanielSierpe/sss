.generador-content {
  flex: 1;
  padding: 40px 48px;
  background: #fff;
  min-height: 100%;
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
}

.generador-inner {
  width: 100%;
  display: flex;
  flex-direction: column;
  /* Elimina align-items */
}

.generador-content h2 {
  font-size: 2rem; /* Más grande si quieres */
  color: #e60000;
  margin-bottom: 32px;
  font-weight: 700;
}

.generador-form {
  width: 100%;
  display: flex;
  align-items: flex-end;
  gap: 32px;
  margin-bottom: 36px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-group label {
  font-size: 1rem;
  color: #888;
  font-weight: 500;
}

.search-input {
  width: 320px;
  padding: 12px 16px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 1rem;
  background: #fafafa;
  color: #000;
  outline: none;
  transition: border 0.2s;
}

.search-input:focus {
  border: 1.5px solid #e60000;
}

.search-input::placeholder {
  color: #666;
  opacity: 1;
}

.button-group {
  display: flex;
  gap: 18px;
}

.btn {
  padding: 12px 28px;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, box-shadow 0.2s;
  box-shadow: 0 2px 8px 0 #e6000022;
}

.btn.validar {
  background: #e60000;
  color: #fff;
  border: 2px solid #e60000;
}

.btn.generar {
  background: #fff;
  color: #e60000;
  border: 2px solid #e60000;
}

.btn.validar:hover, .btn.generar:hover {
  box-shadow: 0 4px 16px 0 #e6000033;
  opacity: 0.95;
}

.tabla-container {
  width: 100%;
  margin-top: 32px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 16px 0 #0001;
  overflow-x: auto;
}

.tabla-generador {
  width: 100%;
  border-collapse: collapse;
  background: #fff;
}

.tabla-generador th, .tabla-generador td {
  padding: 14px 12px;
  text-align: left;
  font-size: 1rem;
}

.tabla-generador th {
  color: #a10000;
  font-weight: 700;
  border-bottom: 2px solid #e0e0e0;
  background: #fff;
}

.tabla-generador td {
  color: #333;
  border-bottom: 1px solid #f0f0f0;
}

/* Estilos para la búsqueda con dropdown */
.search-container {
  position: relative;
}

.search-wrapper {
  position: relative;
  display: inline-block;
  width: 100%;
}

.search-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-top: none;
  border-radius: 0 0 8px 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  max-height: 200px;
  overflow-y: auto;
  z-index: 1000;
}

.dropdown-item {
  padding: 12px 16px;
  cursor: pointer;
  border-bottom: 1px solid #f0f0f0;
  transition: background-color 0.2s;
}

.dropdown-item:hover {
  background-color: #f8f9fa;
}

.dropdown-item:last-child {
  border-bottom: none;
}

.selected-app {
  margin-top: 8px;
  padding: 8px 12px;
  background: #e8f5e8;
  border: 1px solid #4caf50;
  border-radius: 6px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.selected-label {
  font-size: 0.9rem;
  color: #2e7d32;
  font-weight: 500;
}

.selected-value {
  font-size: 0.9rem;
  color: #1b5e20;
  font-weight: 600;
  background: #fff;
  padding: 2px 8px;
  border-radius: 4px;
}

/* Estilos para botones habilitados/deshabilitados */
.btn.generar.disabled {
  background: #f5f5f5;
  color: #999;
  border-color: #ddd;
  cursor: not-allowed;
  box-shadow: none;
}

.btn.generar.enabled {
  background: #fff;
  color: #e60000;
  border: 2px solid #e60000;
  cursor: pointer;
}

.btn.generar.enabled:hover {
  box-shadow: 0 4px 16px 0 #e6000033;
  opacity: 0.95;
}

/* Estilos para el modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  padding: 20px;
}

.log-modal {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  max-width: 800px;
  width: 100%;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  border-bottom: 1px solid #e0e0e0;
  background: #f8f9fa;
  border-radius: 12px 12px 0 0;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 12px;
}

.modal-header h3 {
  margin: 0;
  color: #e60000;
  font-size: 1.25rem;
  font-weight: 600;
}

.close-button {
  background: none;
  border: none;
  font-size: 24px;
  color: #666;
  cursor: pointer;
  padding: 0;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.close-button:hover {
  background-color: #e0e0e0;
}

.modal-content {
  padding: 24px;
  flex: 1;
  overflow-y: auto;
}

.execution-status {
  margin-bottom: 24px;
}

.status-message {
  font-size: 1.1rem;
  font-weight: 500;
  color: #333;
  margin-bottom: 16px;
  padding: 12px 16px;
  background: #f8f9fa;
  border-radius: 8px;
  border-left: 4px solid #e60000;
}

.app-status {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
  margin-top: 16px;
}

.status-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 12px;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
}

.status-label {
  font-size: 0.9rem;
  color: #666;
  font-weight: 500;
}

.status-value {
  font-size: 1rem;
  font-weight: 600;
  color: #333;
}

.status-starting {
  color: #ff9800;
}

.status-running {
  color: #4caf50;
}

.status-stopped {
  color: #666;
}

.status-error {
  color: #f44336;
}

.status-complete {
  color: #4caf50;
  font-weight: 700;
}

/* Estilos para logs */
.logs-section {
  margin-top: 24px;
}

.logs-section h4 {
  margin: 0 0 16px 0;
  color: #333;
  font-size: 1.1rem;
  font-weight: 600;
}

.logs-container {
  background: #1e1e1e;
  border-radius: 8px;
  padding: 16px;
  max-height: 300px;
  overflow-y: auto;
  font-family: 'Courier New', monospace;
}

.log-entry {
  display: flex;
  gap: 12px;
  margin-bottom: 8px;
  font-size: 0.9rem;
  line-height: 1.4;
}

.log-timestamp {
  color: #888;
  min-width: 80px;
}

.log-level {
  min-width: 60px;
  font-weight: 600;
}

.log-info {
  color: #4caf50;
}

.log-warn {
  color: #ff9800;
}

.log-error {
  color: #f44336;
}

.log-debug {
  color: #2196f3;
}

.log-message {
  color: #fff;
  flex: 1;
}

/* Loading indicator */
.loading-indicator {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 16px;
  padding: 16px;
  background: #f8f9fa;
  border-radius: 8px;
  justify-content: center;
}

.spinner {
  width: 20px;
  height: 20px;
  border: 2px solid #e0e0e0;
  border-top: 2px solid #ff9800;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.modal-footer {
  padding: 20px 24px;
  border-top: 1px solid #e0e0e0;
  display: flex;
  justify-content: flex-end;
  background: #f8f9fa;
  border-radius: 0 0 12px 12px;
}

.btn-secondary {
  background: #6c757d;
  color: #fff;
  border: 2px solid #6c757d;
}

.btn-secondary:hover {
  background: #5a6268;
  border-color: #5a6268;
}

.btn-toggle-logs {
  background: #17a2b8;
  color: #fff;
  border: 2px solid #17a2b8;
  font-size: 0.9rem;
  padding: 8px 16px;
}

.btn-toggle-logs:hover {
  background: #138496;
  border-color: #138496;
}

.btn-download {
  background: #28a745;
  color: #fff;
  border: 2px solid #28a745;
  margin-right: 12px;
}

.btn-download:hover {
  background: #218838;
  border-color: #218838;
}

.btn-download:disabled {
  background: #6c757d;
  border-color: #6c757d;
  cursor: not-allowed;
  opacity: 0.6;
} 
