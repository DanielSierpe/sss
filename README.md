import { render, screen, fireEvent, act } from '@testing-library/react';
import { vi, describe, it, expect, beforeEach, afterEach } from 'vitest';
import Generador from '../Generador';
import { useAuthContext } from '../../contexts/AuthContext';
import { ExecutorService } from '../../services/ExecutorService';
import type { AppStatus } from '../../services/ExecutorService';

// Mock del contexto de autenticación
vi.mock('../../contexts/AuthContext', () => ({
  useAuthContext: vi.fn()
}));

// Mock del ExecutorService
vi.mock('../../services/ExecutorService', () => ({
  ExecutorService: {
    searchApps: vi.fn(),
    executeScript: vi.fn(),
    getAppStatus: vi.fn(),
    downloadGeneratedProject: vi.fn()
  }
}));

// Mock de timers
vi.useFakeTimers();

describe('Generador', () => {
  const mockUseAuthContext = vi.mocked(useAuthContext);
  const mockExecutorService = vi.mocked(ExecutorService);

  const mockApps = ['app1', 'app2', 'app3', 'test-app'];
  const mockToken = 'mock-jwt-token';

  const mockAppStatus: AppStatus = {
    app: 'test-app',
    startTime: '2024-01-01T00:00:00Z',
    version: '1.0.0',
    status: 'running',
    logs: [
      {
        timestamp: '2024-01-01T00:00:00Z',
        level: 'INFO',
        message: 'Aplicación iniciada'
      },
      {
        timestamp: '2024-01-01T00:01:00Z',
        level: 'INFO',
        message: 'Procesando componente'
      }
    ]
  };

  beforeEach(() => {
    // Configurar mock del contexto de autenticación
    mockUseAuthContext.mockReturnValue({
      isAuthenticated: true,
      token: mockToken,
      isLoading: false,
      error: null,
      handleAuthCode: vi.fn(),
      logout: vi.fn()
    });

    // Configurar mocks del ExecutorService
    mockExecutorService.searchApps.mockResolvedValue(mockApps);
    mockExecutorService.executeScript.mockResolvedValue({ success: true, message: 'Script ejecutado' });
    mockExecutorService.getAppStatus.mockResolvedValue(mockAppStatus);
    mockExecutorService.downloadGeneratedProject.mockResolvedValue({ success: true, message: 'Proyecto test-app descargado exitosamente como test-app-generated-project.tar.gz' });

    // Limpiar timers
    vi.clearAllTimers();
  });

  afterEach(() => {
    vi.clearAllMocks();
    vi.runOnlyPendingTimers();
  });

  it('debería renderizar correctamente', async () => {
    await act(async () => {
      render(<Generador />);
    });

    expect(screen.getByText('Validación de Estructura')).toBeInTheDocument();
    expect(screen.getByText('Estado de Autenticación')).toBeInTheDocument();
    expect(screen.getByText('Sí')).toBeInTheDocument();
    expect(screen.getByPlaceholderText('Buscar aplicación...')).toBeInTheDocument();
    expect(screen.getByText('Validar')).toBeInTheDocument();
    expect(screen.getByText('Generar componente')).toBeInTheDocument();
  });

  it('debería cargar aplicaciones al montar el componente', async () => {
    await act(async () => {
      render(<Generador />);
    });

    expect(mockExecutorService.searchApps).toHaveBeenCalledWith('');
  });

  it('debería filtrar aplicaciones al escribir en el campo de búsqueda', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.change(searchInput, { target: { value: 'app1' } });
    });

    expect(screen.getByText('app1')).toBeInTheDocument();
    expect(screen.queryByText('app2')).not.toBeInTheDocument();
  });

  it('debería mostrar dropdown al hacer focus en el campo de búsqueda', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    expect(screen.getByText('app1')).toBeInTheDocument();
    expect(screen.getByText('app2')).toBeInTheDocument();
  });

  it('debería seleccionar una aplicación al hacer clic en el dropdown', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    expect(screen.getByText('Aplicación seleccionada:')).toBeInTheDocument();
    expect(screen.getByText('app1')).toBeInTheDocument();
  });

  it('debería habilitar el botón generar cuando se selecciona una aplicación', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    const generateButton = screen.getByText('Generar componente');
    

    expect(generateButton).toBeDisabled();

    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });


    expect(generateButton).not.toBeDisabled();
  });

  it('debería ejecutar el script y mostrar el modal al hacer clic en generar', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

    expect(mockExecutorService.executeScript).toHaveBeenCalledWith('app1');
    expect(screen.getByText('Generación de Componente: app1')).toBeInTheDocument();
    expect(screen.getByText('Componente iniciado correctamente. Monitoreando estado...')).toBeInTheDocument();
  });

  it('debería iniciar el polling después de ejecutar el script exitosamente', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    expect(mockExecutorService.getAppStatus).toHaveBeenCalledWith('app1');
  });

  it('debería mostrar logs cuando están disponibles en el status', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    const toggleLogsButton = screen.getByText('Ver Logs');
    
    await act(async () => {
      fireEvent.click(toggleLogsButton);
    });

    expect(screen.getByText('Logs de Ejecución')).toBeInTheDocument();
    expect(screen.getByText('Aplicación iniciada')).toBeInTheDocument();
    expect(screen.getByText('Procesando componente')).toBeInTheDocument();
  });

  it('debería mostrar el botón de descarga cuando el estado es completed', async () => {
    const completeStatus: AppStatus = {
      ...mockAppStatus,
      status: 'completed'
    };

    mockExecutorService.getAppStatus.mockResolvedValue(completeStatus);

    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });


    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    expect(screen.getByText('Descargar Proyecto')).toBeInTheDocument();
  });

  it('debería descargar el proyecto cuando se hace clic en el botón de descarga', async () => {
    const completeStatus: AppStatus = {
      ...mockAppStatus,
      status: 'completed'
    };

    mockExecutorService.getAppStatus.mockResolvedValue(completeStatus);

    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

  
    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    const downloadButton = screen.getByText('Descargar Proyecto');
    
    await act(async () => {
      fireEvent.click(downloadButton);
    });

    expect(mockExecutorService.downloadGeneratedProject).toHaveBeenCalledWith('app1');
  });

  it('debería cerrar el modal y detener el polling al hacer clic en cerrar', async () => {
    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

    const closeButton = screen.getByText('Cerrar');
    
    await act(async () => {
      fireEvent.click(closeButton);
    });

    expect(screen.queryByText('Generación de Componente: app1')).not.toBeInTheDocument();
  });

  it('debería manejar errores en la ejecución del script', async () => {
    mockExecutorService.executeScript.mockRejectedValue(new Error('Error de ejecución'));

    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

    expect(screen.getByText('Error: Error de ejecución')).toBeInTheDocument();
  });

  it('debería manejar errores en la descarga del proyecto', async () => {
    const completeStatus: AppStatus = {
      ...mockAppStatus,
      status: 'completed'
    };

    mockExecutorService.getAppStatus.mockResolvedValue(completeStatus);
    mockExecutorService.downloadGeneratedProject.mockRejectedValue(new Error('Error de descarga'));

    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });

 
    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    const downloadButton = screen.getByText('Descargar Proyecto');
    
    await act(async () => {
      fireEvent.click(downloadButton);
    });

    expect(screen.getByText('Error en descarga: Error de descarga')).toBeInTheDocument();
  });

  it('debería mostrar el estado correcto según el status de la aplicación', async () => {
    const errorStatus: AppStatus = {
      ...mockAppStatus,
      status: 'error'
    };

    mockExecutorService.getAppStatus.mockResolvedValue(errorStatus);

    await act(async () => {
      render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });


    await act(async () => {
      vi.advanceTimersByTime(2000);
    });

    expect(screen.getByText('Error en la generación del componente')).toBeInTheDocument();
  });

  it('debería limpiar el polling al desmontar el componente', async () => {
    const { unmount } = await act(async () => {
      return render(<Generador />);
    });

    const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
    
    await act(async () => {
      fireEvent.focus(searchInput);
    });

    const appOption = screen.getByText('app1');
    
    await act(async () => {
      fireEvent.click(appOption);
    });

    const generateButton = screen.getByText('Generar componente');
    
    await act(async () => {
      fireEvent.click(generateButton);
    });


    unmount();

    await act(async () => {
      vi.advanceTimersByTime(2000);
    });


    expect(mockExecutorService.getAppStatus).toHaveBeenCalledTimes(0);
  });
});
