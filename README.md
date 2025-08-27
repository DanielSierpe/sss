import React from 'react';
import { useAuthContext } from '../contexts/AuthContext';
import './Header.css';

const Header: React.FC = () => {
  const { isAuthenticated, isLoading, error, logout } = useAuthContext();

  const handleLogout = () => {
    logout();
    const loginUrl = import.meta.env.VITE_LOGIN_URL || 'https://dss-login-webtools-ui-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/';
    
    window.location.href = loginUrl;
  };

  return (
    <header className="app-header">
      <img src="/santander.png" alt="Logo" className="logo" />
      <span className="header-title"></span>
      
             {/* Estado de autenticación */}
       <div className="auth-status">
         {isLoading && <span className="auth-loading">Autenticando...</span>}
         {error && <span className="auth-error">Error: {error}</span>}
         {isAuthenticated && <span className="auth-success">Autenticado</span>}
         {!isAuthenticated && !isLoading && <span className="auth-pending">No autenticado</span>}
       </div>
      
      <button className="logout-btn" onClick={handleLogout}>
        <svg className="logout-icon" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M9 21H5C4.46957 21 3.96086 20.7893 3.58579 20.4142C3.21071 20.0391 3 19.5304 3 19V5C3 4.46957 3.21071 3.96086 3.58579 3.58579C3.96086 3.21071 4.46957 3 5 3H9" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
          <path d="M16 17L21 12L16 7" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
          <path d="M21 12H9" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
        </svg>
        Log Out
      </button>
    </header>
  );
};

export default Header; 
