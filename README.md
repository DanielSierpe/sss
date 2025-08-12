import React from 'react';
import './Header.css';


const Header: React.FC = () => {
    return (
        <header className="app-header">
        <img src= "/logo_santander_new.svg" alt="Logo" className="logo" />
        <span className="header-title"></span>
        </header>

    );
};

export default Header;




import React from 'react';
import { Link } from 'react-router-dom';
import './Sidebar.css';

const Sidebar: React.FC = () => {
  return (
    <aside className="sidebar">
      <div className="sidebar-logo-title">     
            <img src="/logo.png" alt="Gluon Logo" className="gluon-logo" />
        <span className="gluon-title">Gluon</span>
      </div>
      <div className="sidebar-title">MENÚ</div>
      <nav>
        <ul>
          <li><Link to="/diseno-openapi">Diseño OpenAPI</Link></li>
          <li><Link to="/generador">Generador</Link></li>
          <li><Link to="/editor-jslt">Editor JSLT</Link></li>
          <li><Link to="#">Editor XSLT</Link></li>
          <li><Link to="#">Editor HTML</Link></li>
        </ul>
      </nav>
    </aside>
  );
};

export default Sidebar; 



