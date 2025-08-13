.diseno-openapi-content {
  flex: 1;
  padding: 0;
  background: #fff;
  min-height: 100%;
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  color: #333;
}

.diseno-openapi-inner {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  min-height: calc(100vh - 64px);
  padding-top: 0;
}

/* Cuando contiene el editor, no centrar */
.diseno-openapi-inner.editor-mode {
  align-items: stretch;
  justify-content: flex-start;
}

/* Icons */
.pficon {
  font-family: 'PatternFlyIcons-webfont';
  font-style: normal;
  font-weight: normal;
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  -webkit-font-smoothing: antialiased;
}

.pficon-add-circle-o:before {
  content: "\e60d";
}

.pficon-warning-triangle-o:before {
  content: "\e60e";
}

.pficon-error-circle-o:before {
  content: "\e60f";
}

/* Blank Slate PatternFly */
.blank-slate-pf {
  text-align: center;
  padding: 80px 40px;
  background: #fff;
  border: 2px solid #e0e0e0;
  border-radius: 20px;
  margin: 0 auto;
  max-width: 700px;
  width: 100%;
  transition: all 0.4s ease;
  box-shadow: 0 25px 60px rgba(0, 0, 0, 0.15), 0 10px 30px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.blank-slate-pf.dragging {
  border: 3px dashed #e60000;
  background-color: #fff;
  box-shadow: 0 30px 80px rgba(230, 0, 0, 0.25), 0 15px 40px rgba(230, 0, 0, 0.15);
  transform: scale(1.02) translate(-50%, -50%);
}

.blank-slate-pf-icon {
  margin-bottom: 30px;
}

.blank-slate-pf-icon .pficon {
  font-size: 80px;
  color: #e60000;
  animation: float 3s ease-in-out infinite;
}


.blank-slate-pf h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 20px;
  color: #333;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.blank-slate-pf p {
  font-size: 1.1rem;
  color: #666;
  margin-bottom: 15px;
  line-height: 1.6;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.blank-slate-pf a {
  color: #e60000;
  text-decoration: none;
  font-weight: 600;
  transition: color 0.3s ease;
}

.blank-slate-pf a:hover {
  color: #cc0000;
  text-decoration: underline;
}

/* Main Action */
.blank-slate-pf-main-action {
  margin-top: 40px;
}

.btn-group {
  display: inline-block;
  position: relative;
  vertical-align: middle;
}

.btn {
  display: inline-block;
  padding: 12px 24px;
  margin-bottom: 0;
  font-size: 1rem;
  font-weight: 600;
  line-height: 1.42857143;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  cursor: pointer;
  border: 2px solid transparent;
  border-radius: 12px;
  text-decoration: none;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.btn-lg {
  padding: 16px 32px;
  font-size: 1.1rem;
  line-height: 1.3333333;
  border-radius: 15px;
}

.btn-primary {
  color: #fff;
  background: linear-gradient(135deg, #e60000 0%, #cc0000 100%);
  border-color: #e60000;
  box-shadow: 0 4px 15px rgba(230, 0, 0, 0.3);
}

.btn-primary:hover {
  background: linear-gradient(135deg, #cc0000 0%, #b30000 100%);
  border-color: #cc0000;
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(230, 0, 0, 0.4);
}

.btn-default {
  color: #333;
  background: #fff;
  border-color: #ddd;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.btn-default:hover {
  background: #f8f9fa;
  border-color: #e60000;
  color: #e60000;
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

/* Recovery Notice */
.recovery-notice {
  margin-top: 30px;
}

.alert {
  padding: 20px;
  margin-bottom: 20px;
  border: none;
  border-radius: 12px;
  font-size: 1rem;
  line-height: 1.5;
}

.alert-warning {
  color: #856404;
  background: linear-gradient(135deg, #fff3cd 0%, #ffeaa7 100%);
  border-left: 4px solid #ffc107;
}

.alert-danger {
  color: #721c24;
  background: linear-gradient(135deg, #f8d7da 0%, #f5c6cb 100%);
  border-left: 4px solid #dc3545;
}

.alert-link {
  font-weight: bold;
  color: #e60000;
  cursor: pointer;
  text-decoration: none;
}

.alert-link:hover {
  text-decoration: underline;
}

/* DND Notification */
.dnd-notification {
  margin-top: 30px;
  font-size: 1rem;
  color: #666;
  font-style: italic;
  padding: 15px;
  background: #f8f9fa;
  border-radius: 8px;
  border: 2px dashed #ccc;
}

/* Responsive */
@media (max-width: 768px) {
  .blank-slate-pf {
    margin: 20px;
    padding: 60px 30px;
  }

  .blank-slate-pf h1 {
    font-size: 2rem;
  }

  .blank-slate-pf p {
    font-size: 1rem;
  }

  .btn-group {
    display: block;
    margin-bottom: 15px;
  }

  .btn {
    display: block;
    width: 100%;
    margin-bottom: 15px;
  }

  .btn-lg {
    padding: 14px 24px;
    font-size: 1rem;
  }
}

@media (max-width: 480px) {
  .blank-slate-pf {
    margin: 10px;
    padding: 40px 20px;
  }

  .blank-slate-pf h1 {
    font-size: 1.8rem;
  }

  .blank-slate-pf-icon .pficon {
    font-size: 60px;
  }
} 
