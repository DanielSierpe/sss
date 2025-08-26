.app-header {
  background: #e60000;
  color: #fff;
  padding: 24px 32px;
  display: flex;
  align-items: center;
  font-size: 2rem;
  font-weight: bold;
  letter-spacing: 1px;
  gap: 16px; 
  justify-content: space-between; 
}

.logo {
  height: 40px;
  margin-right: 16px;
}

.header-title {
  font-size: 2.2rem;
  font-weight: 700;
  flex: 1; 
}

.logout-btn {
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-radius: 8px;
  padding: 10px 20px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.logout-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  border-color: rgba(255, 255, 255, 0.5);
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.logout-btn:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.logout-icon {
  width: 18px;
  height: 18px;
  transition: transform 0.3s ease;
}

.logout-btn:hover .logout-icon {
  transform: translateX(2px);
}

/* Estilos para el estado de autenticación */
.auth-status {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 14px;
  font-weight: 500;
}

.auth-loading {
  color: #ffd700;
  display: flex;
  align-items: center;
  gap: 6px;
}

.auth-loading::before {
  content: '';
  width: 12px;
  height: 12px;
  border: 2px solid #ffd700;
  border-top: 2px solid transparent;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.auth-error {
  color: #ff6b6b;
  background: rgba(255, 107, 107, 0.1);
  padding: 4px 8px;
  border-radius: 4px;
  border: 1px solid rgba(255, 107, 107, 0.3);
}

.auth-success {
  color: #51cf66;
  background: rgba(81, 207, 102, 0.1);
  padding: 4px 8px;
  border-radius: 4px;
  border: 1px solid rgba(81, 207, 102, 0.3);
  display: flex;
  align-items: center;
  gap: 4px;
}

.auth-pending {
  color: #ffd43b;
  background: rgba(255, 212, 59, 0.1);
  padding: 4px 8px;
  border-radius: 4px;
  border: 1px solid rgba(255, 212, 59, 0.3);
} 
