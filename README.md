import React from 'react';
import './Generador.css';

const Generador: React.FC = () => {
  return (
    <div className="generador-content">
      <div className="generador-inner">
        <h2>Validación de Estructura</h2>
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
