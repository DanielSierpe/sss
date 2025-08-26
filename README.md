import React from 'react';
import { useAuthContext } from '../contexts/AuthContext';
import './Generador.css';

const Generador: React.FC = () => {
  const { isAuthenticated, token, isLoading } = useAuthContext();

  return (
    <div className="generador-content">
      <div className="generador-inner">
        <h2>Validación de Estructura</h2>
        
                 {/* Información de autenticación */}
         <div className="auth-info" style={{ 
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
         </div>
        
        <form className="generador-form" autoComplete="off">
          <div className="form-group">
            <label htmlFor="openapi-name">Nombre openAPI</label>
            <input
              type="text"
              id="openapi-name"
              name="openapi-name"
              placeholder="buscar"
              className="search-input"
              autoComplete="off"
            />
          </div>
          <div className="button-group">
            <button type="button" className="btn validar">Validar</button>
            <button type="button" className="btn generar">Generar componente</button>
          </div>
        </form>
        <div className="tabla-container">
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
              {/* Los datos se llenarán dinámicamente desde la API */}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
};

export default Generador; 
