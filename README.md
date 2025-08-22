import React, { useState } from 'react';
import './EditorXSLT.css';

// Ejemplos predefinidos de XSLT
const XSLT_EXAMPLES = {
  'transformacion-basica': {
    name: 'Transformación Básica',
    description: 'Convierte XML a XML con estructura simplificada',
    xml: `<?xml version="1.0" encoding="UTF-8"?>
<libros>
  <libro>
    <titulo>El Señor de los Anillos</titulo>
    <autor>J.R.R. Tolkien</autor>
    <precio>25.99</precio>
  </libro>
  <libro>
    <titulo>1984</titulo>
    <autor>George Orwell</autor>
    <precio>15.50</precio>
  </libro>
  <libro>
    <titulo>Don Quijote</titulo>
    <autor>Miguel de Cervantes</autor>
    <precio>20.00</precio>
  </libro>
</libros>`,
    xslt: `<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <biblioteca>
      <xsl:for-each select="libros/libro">
        <libro>
          <titulo><xsl:value-of select="titulo"/></titulo>
          <autor><xsl:value-of select="autor"/></autor>
          <precio><xsl:value-of select="precio"/></precio>
        </libro>
      </xsl:for-each>
    </biblioteca>
  </xsl:template>
</xsl:stylesheet>`
  },
  'tabla-xml': {
    name: 'Tabla XML',
    description: 'Convierte XML a estructura XML tabular',
    xml: `<?xml version="1.0" encoding="UTF-8"?>
<empleados>
  <empleado>
    <id>001</id>
    <nombre>Juan Pérez</nombre>
    <departamento>IT</departamento>
    <salario>50000</salario>
  </empleado>
  <empleado>
    <id>002</id>
    <nombre>María García</nombre>
    <departamento>RRHH</departamento>
    <salario>45000</salario>
  </empleado>
  <empleado>
    <id>003</id>
    <nombre>Carlos López</nombre>
    <departamento>Ventas</departamento>
    <salario>55000</salario>
  </empleado>
</empleados>`,
    xslt: `<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <empleados-resumen>
      <xsl:for-each select="empleados/empleado">
        <empleado>
          <id><xsl:value-of select="id"/></id>
          <nombre><xsl:value-of select="nombre"/></nombre>
          <departamento><xsl:value-of select="departamento"/></departamento>
          <salario><xsl:value-of select="salario"/></salario>
        </empleado>
      </xsl:for-each>
    </empleados-resumen>
  </xsl:template>
</xsl:stylesheet>`
  },
  'filtrado-condicional': {
    name: 'Filtrado Condicional',
    description: 'Filtra elementos XML basado en condiciones',
    xml: `<?xml version="1.0" encoding="UTF-8"?>
<productos>
  <producto categoria="electronica">
    <nombre>Laptop</nombre>
    <precio>1200</precio>
    <stock>15</stock>
  </producto>
  <producto categoria="ropa">
    <nombre>Camisa</nombre>
    <precio>50</precio>
    <stock>100</stock>
  </producto>
  <producto categoria="electronica">
    <nombre>Smartphone</nombre>
    <precio>800</precio>
    <stock>25</stock>
  </producto>
  <producto categoria="hogar">
    <nombre>Lámpara</nombre>
    <precio>80</precio>
    <stock>30</stock>
  </producto>
</productos>`,
    xslt: `<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/">
    <productos-filtrados>
      <electronica>
        <xsl:for-each select="productos/producto[@categoria='electronica']">
          <producto>
            <nombre><xsl:value-of select="nombre"/></nombre>
            <precio><xsl:value-of select="precio"/></precio>
            <stock><xsl:value-of select="stock"/></stock>
          </producto>
        </xsl:for-each>
      </electronica>
      <ropa>
        <xsl:for-each select="productos/producto[@categoria='ropa']">
          <producto>
            <nombre><xsl:value-of select="nombre"/></nombre>
            <precio><xsl:value-of select="precio"/></precio>
            <stock><xsl:value-of select="stock"/></stock>
          </producto>
        </xsl:for-each>
      </ropa>
      <hogar>
        <xsl:for-each select="productos/producto[@categoria='hogar']">
          <producto>
            <nombre><xsl:value-of select="nombre"/></nombre>
            <precio><xsl:value-of select="precio"/></precio>
            <stock><xsl:value-of select="stock"/></stock>
          </producto>
        </xsl:for-each>
      </hogar>
    </productos-filtrados>
  </xsl:template>
</xsl:stylesheet>`
  }
};

const EditorXSLT: React.FC = () => {
  const [xmlInput, setXmlInput] = useState(XSLT_EXAMPLES['transformacion-basica'].xml);
  const [xsltTransform, setXsltTransform] = useState(XSLT_EXAMPLES['transformacion-basica'].xslt);
  const [htmlOutput, setHtmlOutput] = useState('');
  const [error, setError] = useState<string | null>(null);
  const [showExamples, setShowExamples] = useState(false);
  const [savedTransforms, setSavedTransforms] = useState<{[key: string]: any}>({});
  const [currentTransformName, setCurrentTransformName] = useState('');

  // Función para validar XML
  const isValidXML = (str: string): boolean => {
    try {
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(str, 'text/xml');
      return xmlDoc.getElementsByTagName('parsererror').length === 0;
    } catch (e) {
      return false;
    }
  };

  // Función para formatear XML
  const formatXML = (xml: string): string => {
    try {
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(xml, 'text/xml');
      const serializer = new XMLSerializer();
      return serializer.serializeToString(xmlDoc);
    } catch (e) {
      return xml;
    }
  };

  // Función para procesar la transformación XSLT
  const processTransformation = () => {
    setError(null);
    
    // Validar XML de entrada
    if (!isValidXML(xmlInput)) {
      setError('XML de entrada inválido');
      return;
    }

    try {
      // Crear parser y procesador XSLT
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(xmlInput, 'text/xml');
      const xsltDoc = parser.parseFromString(xsltTransform, 'text/xml');
      
      // Crear procesador XSLT
      const processor = new XSLTProcessor();
      processor.importStylesheet(xsltDoc);
      
      // Procesar la transformación
      const result = processor.transformToFragment(xmlDoc, document);
      
      if (result) {
        const serializer = new XMLSerializer();
        const xmlString = serializer.serializeToString(result);
        setHtmlOutput(xmlString);
      } else {
        setError('Error en la transformación XSLT');
      }
    } catch (e) {
      setError(`Error en la transformación XSLT: ${e instanceof Error ? e.message : 'Error desconocido'}`);
    }
  };

  const handleXmlInputChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
    setXmlInput(e.target.value);
  };

  const handleXsltChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
    setXsltTransform(e.target.value);
  };

  const handleFormatXML = () => {
    setXmlInput(formatXML(xmlInput));
  };

  const handleClearAll = () => {
    setXmlInput('');
    setXsltTransform('');
    setHtmlOutput('');
    setError(null);
  };

  const handleLoadExample = (exampleKey: string) => {
    const example = XSLT_EXAMPLES[exampleKey as keyof typeof XSLT_EXAMPLES];
    if (example) {
      setXmlInput(example.xml);
      setXsltTransform(example.xslt);
      setCurrentTransformName(example.name);
    }
  };

  const handleSaveTransform = () => {
    const name = prompt('Nombre para guardar esta transformación:');
    if (name && name.trim()) {
      const newTransform = {
        name: name.trim(),
        xslt: xsltTransform,
        xml: xmlInput,
        timestamp: new Date().toISOString()
      };
      setSavedTransforms(prev => ({
        ...prev,
        [name.trim()]: newTransform
      }));
      setCurrentTransformName(name.trim());
    }
  };

  const handleLoadSavedTransform = (name: string) => {
    const transform = savedTransforms[name];
    if (transform) {
      setXmlInput(transform.xml);
      setXsltTransform(transform.xslt);
      setCurrentTransformName(transform.name);
    }
  };

  const handleDeleteTransform = (name: string) => {
    if (confirm(`¿Eliminar la transformación "${name}"?`)) {
      const newSaved = { ...savedTransforms };
      delete newSaved[name];
      setSavedTransforms(newSaved);
      if (currentTransformName === name) {
        setCurrentTransformName('');
      }
    }
  };

  return (
    <div className="editor-xslt-container">
      <div className="editor-xslt-header">
        <h1>Editor XSLT</h1>
        <div className="editor-xslt-actions">
          <button className="btn btn-primary" onClick={processTransformation}>
            ▶ Run
          </button>
          <button className="btn btn-secondary" onClick={handleFormatXML}>
            Formatear XML
          </button>
          <button className="btn btn-secondary" onClick={() => setShowExamples(true)}>
            Ejemplos
          </button>
          <button className="btn btn-secondary" onClick={handleSaveTransform}>
            Guardar
          </button>
          <button className="btn btn-danger" onClick={handleClearAll}>
            Limpiar Todo
          </button>
        </div>
      </div>

      <div className="editor-xslt-content">
        {/* Sección izquierda */}
        <div className="editor-xslt-left">
          {/* XML Input */}
          <div className="editor-xslt-section">
            <div className="section-header">
              <h3>XML Input</h3>
            </div>
            <textarea
              className="editor-textarea xml-input"
              value={xmlInput}
              onChange={handleXmlInputChange}
              placeholder="Ingresa tu XML de entrada aquí..."
            />
          </div>

          {/* XSLT Transform */}
          <div className="editor-xslt-section">
            <div className="section-header">
              <h3>XSLT</h3>
            </div>
            <textarea
              className="editor-textarea xslt-transform"
              value={xsltTransform}
              onChange={handleXsltChange}
              placeholder="Ingresa tu transformación XSLT aquí..."
            />
          </div>
        </div>

        {/* Sección derecha */}
        <div className="editor-xslt-right">
          {/* XML Output */}
          <div className="editor-xslt-section">
            <div className="section-header">
              <h3>XML Output</h3>
            </div>
            <textarea
              className="editor-textarea xml-output"
              value={htmlOutput}
              readOnly
              placeholder="El resultado de la transformación XML aparecerá aquí..."
            />
          </div>
        </div>
      </div>

      {/* Mensajes de error */}
      {error && (
        <div className="editor-xslt-error">
          <span className="error-icon">⚠️</span>
          <span>{error}</span>
        </div>
      )}

      {/* Modal de Ejemplos */}
      {showExamples && (
        <div className="modal-overlay" onClick={() => setShowExamples(false)}>
          <div className="modal" onClick={(e) => e.stopPropagation()}>
            <h3>Ejemplos de XSLT</h3>
            <div className="examples-grid">
              {Object.entries(XSLT_EXAMPLES).map(([key, example]) => (
                <div key={key} className="example-card">
                  <h4>{example.name}</h4>
                  <p>{example.description}</p>
                  <button 
                    className="btn btn-primary" 
                    onClick={() => {
                      handleLoadExample(key);
                      setShowExamples(false);
                    }}
                  >
                    Cargar
                  </button>
                </div>
              ))}
            </div>
            <div className="modal-actions">
              <button className="btn btn-secondary" onClick={() => setShowExamples(false)}>
                Cerrar
              </button>
            </div>
          </div>
        </div>
      )}

      {/* Modal de Transformaciones Guardadas */}
      {Object.keys(savedTransforms).length > 0 && (
        <div className="saved-transforms">
          <h4>Transformaciones Guardadas</h4>
          <div className="saved-transforms-list">
            {Object.entries(savedTransforms).map(([name, transform]) => (
              <div key={name} className="saved-transform-item">
                <span className="transform-name">{transform.name}</span>
                <div className="transform-actions">
                  <button 
                    className="btn btn-sm btn-secondary"
                    onClick={() => handleLoadSavedTransform(name)}
                  >
                    Cargar
                  </button>
                  <button 
                    className="btn btn-sm btn-danger"
                    onClick={() => handleDeleteTransform(name)}
                  >
                    Eliminar
                  </button>
                </div>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
};

export default EditorXSLT; 
