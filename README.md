.editor-visual-content {
  flex: 1;
  padding: 0;
  background: #fff;
  height: calc(100vh - 64px);
  width: 100%;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  color: #333;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
}

.editor-visual-inner {
  width: 100%;
  height: calc(100vh - 64px);
  display: flex;
  flex-direction: column;
  min-height: calc(100vh - 64px);
}

/* Header del editor */
.editor-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 32px;
  background: #fff;
  border-bottom: 1px solid #e0e0e0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.editor-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.btn-back {
  background: none;
  border: none;
  color: #e60000;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  padding: 8px 12px;
  border-radius: 6px;
  transition: all 0.3s ease;
  text-decoration: none;
}

.btn-back:hover {
  background: rgba(230, 0, 0, 0.1);
  transform: translateX(-2px);
}

.editor-title h2 {
  margin: 0;
  font-size: 1.4rem;
  color: #333;
  font-weight: 600;
}

.dirty-indicator {
  color: #e60000;
  font-weight: bold;
  font-size: 1.4rem;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

.editor-actions {
  display: flex;
  gap: 12px;
}

/* Pestañas */
.editor-tabs {
  display: flex;
  background: #f8f9fa;
  border-bottom: 1px solid #e0e0e0;
  padding: 0 32px;
}

.tab {
  padding: 16px 32px;
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 500;
  transition: all 0.3s ease;
  position: relative;
}

.tab.active {
  background: #fff;
  color: #e60000;
  border-bottom: 3px solid #e60000;
}

.tab:hover {
  background: #f0f0f0;
  color: #e60000;
}

.tab.active:hover {
  background: #fff;
}

/* Contenido principal */
.editor-main-content {
  flex: 1;
  padding: 32px;
  overflow-y: auto;
  background: #fff;
  display: flex;
  flex-direction: column;
  min-height: 0;
  height: calc(100vh - 64px - 120px); /* Resta header y tabs */
  max-height: calc(100vh - 64px - 120px);
}

/* Vista de diseño */
.design-view {
  height: 100%;
}

.design-toolbar {
  margin-bottom: 32px;
  display: flex;
  gap: 16px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
}

.design-content {
  display: flex;
  flex-direction: column;
  gap: 32px;
}

.api-info-section,
.paths-section,
.components-section {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.api-info-section h3,
.paths-section h3,
.components-section h3 {
  margin: 0 0 20px 0;
  color: #333;
  font-size: 1.2rem;
  font-weight: 600;
  border-bottom: 2px solid #e60000;
  padding-bottom: 8px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 20px;
}

.field label {
  font-size: 0.95rem;
  color: #666;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.field input {
  padding: 12px 16px;
  background: #fff;
  border: 2px solid #e0e0e0;
  border-radius: 6px;
  color: #333;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.field input:focus {
  outline: none;
  border-color: #e60000;
  box-shadow: 0 0 0 3px rgba(230, 0, 0, 0.1);
}

.paths-list,
.components-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.path-item,
.component-item {
  display: flex;
  flex-direction: column;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  transition: all 0.3s ease;
  overflow: hidden;
}

.path-item:hover,
.component-item:hover {
  border-color: #e60000;
  background: #f8f9fa;
}

.path-header,
.component-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  cursor: pointer;
  transition: background 0.3s ease;
}

.path-header:hover,
.component-header:hover {
  background: #f0f0f0;
}

.path-name,
.component-name {
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 1rem;
  color: #333;
  font-weight: 500;
}

.expand-icon {
  color: #e60000;
  font-size: 1.2rem;
  font-weight: bold;
  transition: transform 0.3s ease;
}

.path-content,
.component-content {
  padding: 16px 20px;
  background: #f8f9fa;
  border-top: 1px solid #e0e0e0;
  animation: slideDown 0.3s ease;
}

@keyframes slideDown {
  from {
    opacity: 0;
    max-height: 0;
  }
  to {
    opacity: 1;
    max-height: 500px;
  }
}

.json-content {
  background: #f8f9fa;
  color: #333;
  padding: 16px;
  border-radius: 6px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.85rem;
  line-height: 1.4;
  overflow-x: auto;
  white-space: pre-wrap;
  word-wrap: break-word;
  border: 1px solid #e0e0e0;
  margin: 0;
}

/* Botones */
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  background: #e60000;
  color: #fff;
  border: 2px solid #e60000;
}

.btn-primary:hover:not(:disabled) {
  background: #cc0000;
  border-color: #cc0000;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(230, 0, 0, 0.3);
}

.btn-secondary {
  background: #6c757d;
  color: #fff;
  border: 2px solid #6c757d;
}

.btn-secondary:hover:not(:disabled) {
  background: #5a6268;
  border-color: #545b62;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.btn-danger {
  background: #dc3545;
  color: #fff;
  border: 2px solid #dc3545;
}

.btn-danger:hover {
  background: #c82333;
  border-color: #c82333;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(220, 53, 69, 0.3);
}

.btn-sm {
  padding: 8px 16px;
  font-size: 0.8rem;
}

.path-item .btn,
.component-item .btn {
  margin: 16px 20px;
  align-self: flex-end;
}

/* Vista de código fuente */
.source-view {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  flex: 1;
  min-height: 0;
}

.source-editor {
  width: 100%;
  height: 100%;
  background: #fff;
  color: #333;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  padding: 20px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.95rem;
  line-height: 1.6;
  resize: none;
  outline: none;
  transition: all 0.3s ease;
  white-space: pre-wrap;
  overflow-x: hidden;
  word-wrap: break-word;
  overflow-y: auto;
  flex: 1;
  min-height: 0;
  box-sizing: border-box;
}

.source-editor::placeholder {
  color: #999;
  font-style: italic;
}

.source-editor:focus {
  border-color: #e60000;
  box-shadow: 0 0 0 3px rgba(230, 0, 0, 0.1);
}

/* Mensajes */
.editor-messages {
  padding: 20px 32px;
  background: #f8f9fa;
  border-top: 1px solid #e0e0e0;
}

.message {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 0;
  font-size: 0.95rem;
  border-radius: 6px;
  padding: 12px 16px;
  margin-bottom: 8px;
}

.message.error {
  color: #dc3545;
  background: rgba(220, 53, 69, 0.1);
  border: 1px solid rgba(220, 53, 69, 0.3);
}

.message.warning {
  color: #ffc107;
  background: rgba(255, 193, 7, 0.1);
  border: 1px solid rgba(255, 193, 7, 0.3);
}

.message .icon {
  font-size: 1.2rem;
}

/* Modales */
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
  z-index: 1000;
  backdrop-filter: blur(4px);
}

.modal {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  padding: 32px;
  min-width: 500px;
  max-width: 700px;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
}

.modal h3 {
  margin: 0 0 20px 0;
  color: #333;
  font-size: 1.4rem;
  font-weight: 600;
}

.modal p {
  margin: 0 0 20px 0;
  color: #666;
  font-size: 1rem;
  line-height: 1.5;
}

.modal-actions {
  display: flex;
  gap: 12px;
  justify-content: flex-end;
  margin-top: 24px;
  flex-wrap: wrap;
}

.modal-actions .btn {
  min-width: 120px;
}

.export-options {
  display: flex;
  gap: 24px;
  margin-bottom: 20px;
  padding: 16px;
  background: #f8f9fa;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
}

.export-options label {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  font-size: 1rem;
  cursor: pointer;
  padding: 8px 12px;
  border-radius: 6px;
  transition: all 0.3s ease;
}

.export-options label:hover {
  background: #e9ecef;
  color: #333;
}

.export-content {
  width: 100%;
  background: #f8f9fa;
  color: #333;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  padding: 16px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.4;
  resize: vertical;
  min-height: 200px;
}

.export-content:focus {
  outline: none;
  border-color: #e60000;
}

/* Responsive */
@media (max-width: 768px) {
  .editor-header {
    flex-direction: column;
    gap: 16px;
    align-items: flex-start;
    padding: 16px 20px;
  }
  
  .editor-actions {
    width: 100%;
    justify-content: space-between;
  }
  
  .editor-main-content {
    padding: 20px;
  }
  
  .design-toolbar {
    flex-direction: column;
    gap: 12px;
  }
  
  .modal {
    margin: 20px;
    min-width: auto;
    padding: 24px;
  }
  
  .export-options {
    flex-direction: column;
    gap: 12px;
  }
} 
