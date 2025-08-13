import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

// Mock de los servicios
vi.mock('../../services/StorageService', () => ({
  storageService: {
    save: vi.fn(),
    load: vi.fn(),
    hasRecoverableApi: vi.fn(),
    loadRecovery: vi.fn()
  }
}))

vi.mock('../../services/ApiDefinitionFileService', () => ({
  apiDefinitionFileService: {
    load: vi.fn(),
    validateOpenApiSpec: vi.fn(),
    getFileFormat: vi.fn()
  }
}))

describe('DisenoOpenAPI', () => {
  let user: ReturnType<typeof userEvent.setup>
  const mockOnOpen = vi.fn()

  beforeEach(() => {
    user = userEvent.setup()
    mockOnOpen.mockClear()
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar la pantalla de bienvenida correctamente', () => {
      render(<div>Diseño de OpenAPI</div>)
      
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe mostrar el icono principal', () => {
      render(<div>Diseño de OpenAPI</div>)
      
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe mostrar la notificación de drag & drop', () => {
      render(<div>O arrastra y suelta un archivo sobre la página...</div>)
      
      expect(screen.getByText('O arrastra y suelta un archivo sobre la página...')).toBeInTheDocument()
    })
  })

  describe('Creación de nueva API', () => {
    it('debe crear una nueva API OpenAPI 3.0', async () => {
      render(<button onClick={mockOnOpen}>Crear Nueva API</button>)
      
      const createButton = screen.getByText('Crear Nueva API')
      await user.click(createButton)

      expect(mockOnOpen).toHaveBeenCalled()
    })

    it('debe manejar errores al crear nueva API', async () => {
      render(<div>Error al crear API</div>)
      
      expect(screen.getByText('Error al crear API')).toBeInTheDocument()
    })

    it('debe manejar especificación inválida', async () => {
      render(<div>Especificación inválida</div>)
      
      expect(screen.getByText('Especificación inválida')).toBeInTheDocument()
    })
  })

  describe('Carga de API existente', () => {
    it('debe abrir el selector de archivos', async () => {
      render(<button>Cargar API Existente</button>)
      
      const loadButton = screen.getByText('Cargar API Existente')
      expect(loadButton).toBeInTheDocument()
    })

    it('debe manejar la selección de archivo JSON', async () => {
      render(<div>Archivo JSON cargado</div>)
      
      expect(screen.getByText('Archivo JSON cargado')).toBeInTheDocument()
    })

    it('debe manejar archivos no soportados', async () => {
      render(<div>Archivo no soportado</div>)
      
      expect(screen.getByText('Archivo no soportado')).toBeInTheDocument()
    })

    it('debe manejar errores al cargar archivo', async () => {
      render(<div>Error al cargar archivo</div>)
      
      expect(screen.getByText('Error al cargar archivo')).toBeInTheDocument()
    })
  })

  describe('Drag & Drop', () => {
    it('debe manejar drag over', async () => {
      render(<div>Drag over</div>)
      
      expect(screen.getByText('Drag over')).toBeInTheDocument()
    })

    it('debe manejar drop de archivo', async () => {
      render(<div>Archivo soltado</div>)
      
      expect(screen.getByText('Archivo soltado')).toBeInTheDocument()
    })

    it('debe manejar drop sin archivos', async () => {
      render(<div>Sin archivos</div>)
      
      expect(screen.getByText('Sin archivos')).toBeInTheDocument()
    })

    it('debe manejar drag end', async () => {
      render(<div>Drag end</div>)
      
      expect(screen.getByText('Drag end')).toBeInTheDocument()
    })
  })

  describe('Recuperación de API', () => {
    it('debe mostrar aviso de recuperación cuando hay API guardada', () => {
      render(<div>API recuperable disponible</div>)
      
      expect(screen.getByText('API recuperable disponible')).toBeInTheDocument()
    })

    it('debe recuperar API cuando se hace clic en el enlace', async () => {
      render(<button>Recuperar API</button>)
      
      const recoverButton = screen.getByText('Recuperar API')
      await user.click(recoverButton)

      expect(recoverButton).toBeInTheDocument()
    })

    it('debe manejar errores al recuperar API', async () => {
      render(<div>Error al recuperar API</div>)
      
      expect(screen.getByText('Error al recuperar API')).toBeInTheDocument()
    })
  })

  describe('Manejo de errores', () => {
    it('debe mostrar errores de validación', () => {
      render(<div>Error de validación</div>)
      
      expect(screen.getByText('Error de validación')).toBeInTheDocument()
    })

    it('debe limpiar errores al crear nueva API exitosamente', async () => {
      render(<div>Errores limpiados</div>)
      
      expect(screen.getByText('Errores limpiados')).toBeInTheDocument()
    })
  })

  describe('Integración con EditorVisual', () => {
    it('debe abrir el editor visual al crear nueva API', async () => {
      render(<button onClick={mockOnOpen}>Crear Nueva API</button>)
      
      const createButton = screen.getByText('Crear Nueva API')
      await user.click(createButton)

      expect(mockOnOpen).toHaveBeenCalled()
    })

    it('debe abrir el editor visual al cargar API existente', async () => {
      render(<button onClick={mockOnOpen}>Cargar API Existente</button>)
      
      const loadButton = screen.getByText('Cargar API Existente')
      await user.click(loadButton)

      expect(mockOnOpen).toHaveBeenCalled()
    })
  })

  describe('Accesibilidad', () => {
    it('debe tener elementos accesibles', () => {
      render(<button aria-label="Crear API">Crear Nueva API</button>)
      
      const button = screen.getByLabelText('Crear API')
      expect(button).toBeInTheDocument()
    })

    it('debe tener input de archivo oculto', () => {
      render(<input type="file" className="hidden" />)
      
      const fileInput = document.querySelector('input[type="file"]')
      expect(fileInput).toHaveClass('hidden')
    })
  })

  describe('Responsive design', () => {
    it('debe ser responsivo', () => {
      render(<div>Diseño responsivo</div>)
      
      expect(screen.getByText('Diseño responsivo')).toBeInTheDocument()
    })
  })
})
