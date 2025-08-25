.editor-xslt-container {
  width: 100%;
  height: 100%;
  background: #fff;
  color: #333;
  display: flex;
  flex-direction: column;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
}

.editor-xslt-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 32px;
  background: #f8f9fa;
  border-bottom: 1px solid #e0e0e0;
}

.editor-xslt-header h1 {
  margin: 0;
  font-size: 1.5rem;
  color: #333;
  font-weight: 600;
}

.editor-xslt-actions {
  display: flex;
  gap: 12px;
}

.editor-xslt-content {
  flex: 1;
  display: flex;
  height: calc(100vh - 64px - 80px);
  padding: 20px;
  gap: 20px;
}

.editor-xslt-left {
  width: 50%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.editor-xslt-right {
  width: 50%;
  display: flex;
  flex-direction: column;
}

.editor-xslt-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  overflow: hidden;
}

.section-header {
  padding: 16px 20px;
  background: #f8f9fa;
  border-bottom: 1px solid #e0e0e0;
}

.section-header h3 {
  margin: 0;
  font-size: 1.1rem;
  color: #333;
  font-weight: 600;
}

.editor-textarea {
  flex: 1;
  background: #fff;
  color: #333;
  border: none;
  padding: 20px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.5;
  resize: none;
  outline: none;
  white-space: pre-wrap;
  word-wrap: break-word;
  overflow-y: auto;
}

.editor-textarea::placeholder {
  color: #999;
  font-style: italic;
}

.editor-textarea:focus {
  background: #fff;
}

.xml-input {
  border-top: 1px solid #e0e0e0;
}

.xslt-transform {
  border-top: 1px solid #e0e0e0;
}

.xml-output {
  border-top: 1px solid #e0e0e0;
  background: #f8f9fa;
  color: #333;
}

/* Botones */
.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.btn-primary {
  background: #e60000;
  color: #fff;
  border: 1px solid #e60000;
}

.btn-primary:hover {
  background: #cc0000;
  border-color: #cc0000;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(230, 0, 0, 0.3);
}

.btn-secondary {
  background: #6c757d;
  color: #fff;
  border: 1px solid #6c757d;
}

.btn-secondary:hover {
  background: #5a6268;
  border-color: #545b62;
}

.btn-danger {
  background: #dc3545;
  color: #fff;
  border: 1px solid #dc3545;
}

.btn-danger:hover {
  background: #c82333;
  border-color: #c82333;
}

.btn-sm {
  padding: 6px 12px;
  font-size: 0.8rem;
}

/* Mensajes de error */
.editor-xslt-error {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 32px;
  background: rgba(220, 53, 69, 0.1);
  border-top: 1px solid rgba(220, 53, 69, 0.3);
  color: #dc3545;
  font-size: 0.9rem;
}

.error-icon {
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
}

.modal {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  padding: 32px;
  min-width: 600px;
  max-width: 800px;
  max-height: 80vh;
  overflow-y: auto;
  color: #333;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
}

.modal h3 {
  margin: 0 0 24px 0;
  color: #333;
  font-size: 1.4rem;
  font-weight: 600;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  margin-top: 24px;
  gap: 12px;
}

/* Grid de ejemplos */
.examples-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 24px;
}

.example-card {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 20px;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.example-card:hover {
  border-color: #e60000;
  background: #f8f9fa;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.example-card h4 {
  margin: 0 0 12px 0;
  color: #333;
  font-size: 1.1rem;
  font-weight: 600;
}

.example-card p {
  margin: 0 0 16px 0;
  color: #666;
  font-size: 0.9rem;
  line-height: 1.4;
}

/* Transformaciones guardadas */
.saved-transforms {
  position: fixed;
  top: 100px;
  right: 20px;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 20px;
  min-width: 250px;
  max-width: 300px;
  z-index: 100;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.saved-transforms h4 {
  margin: 0 0 16px 0;
  color: #333;
  font-size: 1rem;
  font-weight: 600;
}

.saved-transforms-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.saved-transform-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background: #f8f9fa;
  border-radius: 6px;
  border: 1px solid #e0e0e0;
}

.transform-name {
  color: #333;
  font-size: 0.9rem;
  font-weight: 500;
}

.transform-actions {
  display: flex;
  gap: 8px;
}

/* Responsive */
@media (max-width: 768px) {
  .editor-xslt-content {
    flex-direction: column;
    height: auto;
  }
  
  .editor-xslt-left,
  .editor-xslt-right {
    width: 100%;
  }
  
  .editor-xslt-header {
    flex-direction: column;
    gap: 16px;
    align-items: flex-start;
  }
  
  .editor-xslt-actions {
    width: 100%;
    justify-content: space-between;
  }
}

/* Scrollbar personalizado */
.editor-textarea::-webkit-scrollbar {
  width: 8px;
}

.editor-textarea::-webkit-scrollbar-track {
  background: #f0f0f0;
}

.editor-textarea::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 4px;
}

.editor-textarea::-webkit-scrollbar-thumb:hover {
  background: #999;
} 
