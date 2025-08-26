import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { AuthProvider } from './contexts/AuthContext';
import Header from './components/Header';
import Sidebar from './components/Sidebar';
import Dashboard from './pages/Dashboard';
import Generador from './pages/Generador';
import DisenoOpenAPI from './pages/DisenoOpenAPI';
import EditorJSLT from './pages/EditorJSLT';
import EditorXSLT from './pages/EditorXSLT';

import './App.css';

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <div className="app-container">
          <Header />
          <div className="main-layout">
            <Sidebar />
            <main className="main-content">
                          <Routes>
              <Route path="/" element={<Dashboard />} />
              <Route path="/generador" element={<Generador />} />
              <Route path="/diseno-openapi" element={<DisenoOpenAPI />} />
              <Route path="/editor-jslt" element={<EditorJSLT />} />
              <Route path="/editor-xslt" element={<EditorXSLT />} />
            </Routes>
            </main>
          </div>
        </div>
      </BrowserRouter>
    </AuthProvider>
  );
}
