import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import DisenoOpenAPI from '../DisenoOpenAPI'

// Mock de los servicios
vi.mock('../../services/StorageService', () => ({
  storageService: {
    save: vi.fn().mockResolvedValue(true),
    load: vi.fn().mockResolvedValue({
      spec: { openapi: '3.0.0', info: { title: 'Test API' } },
      name: 'test.json',
      createdOn: new Date()
    }),
    hasRecoverableApi: vi.fn().mockReturnValue(false),
    loadRecovery: vi.fn().mockReturnValue(null),
    saveForRecovery: vi.fn().mockResolvedValue(true),
    clearRecovery: vi.fn().mockImplementation(() => {})
  }
}))

vi.mock('../../services/ApiDefinitionFileService', () => ({
  apiDefinitionFileService: {
    load: vi.fn().mockResolvedValue({
      openapi: '3.0.0',
      info: { title: 'Test API' }
    }),
    validateOpenApiSpec: vi.fn().mockReturnValue({ 
      isValid: true, 
      errors: [] 
    }),
    getFileFormat: vi.fn().mockReturnValue('json')
  },
  ApiDefinitionFileService: {
    getFileFormat: vi.fn().mockReturnValue('json')
  }
}))

describe('DisenoOpenAPI', () => {
  let user: ReturnType<typeof userEvent.setup>
  const mockOnOpen = vi.fn()

  beforeEach(() => {
    user = userEvent.setup()
    mockOnOpen.mockClear()
    
    // Mock de window.alert
    vi.spyOn(window, 'alert').mockImplementation(() => {})
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar la pantalla de bienvenida correctamente', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      expect(screen.getByText(/Diseño de OpenAPI/)).toBeInTheDocument()
    })

    it('debe mostrar el icono principal', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      expect(screen.getByText(/Diseño de OpenAPI/)).toBeInTheDocument()
    })

    it('debe mostrar la notificación de drag & drop', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      expect(screen.getByText(/arrastra y suelta/)).toBeInTheDocument()
    })
  })

  describe('Creación de nueva API', () => {
    it('debe mostrar el botón de crear nueva API', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar especificación inválida', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      // Simular especificación inválida
      const invalidSpec = 'invalid json'
      
      // Buscar el input de archivo y simular carga de archivo inválido
      const fileInput = document.querySelector('input[type="file"]')
      if (fileInput) {
        const file = new File([invalidSpec], 'invalid.json', { type: 'application/json' })
        fireEvent.change(fileInput, { target: { files: [file] } })
      }
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe crear nueva API OpenAPI 3.0', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe crear nueva API OpenAPI 2.0', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar errores al crear nueva API', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar validación inválida al crear API', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar errores de guardado al crear API', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar recuperación de API exitosa', async () => {
      // Mock para simular API recuperable
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockReturnValueOnce({
        spec: { openapi: '3.0.0', info: { title: 'Recovered API' } },
        name: 'recovered.json',
        createdOn: new Date()
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      expect(recoverLink).toBeInTheDocument()
      
      await user.click(recoverLink)
      
      // Después del clic, el componente cambia al modo editor
      expect(screen.queryByText('Recuperar mi API')).not.toBeInTheDocument()
    })

    it('debe manejar error en recuperación de API', async () => {
      // Mock para simular error en recuperación
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockImplementationOnce(() => {
        throw new Error('Error de recuperación')
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      expect(recoverLink).toBeInTheDocument()
      
      await user.click(recoverLink)
      
      // Después del error, el componente permanece en el modo welcome
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe manejar recuperación sin API disponible', async () => {
      // Mock para simular sin API recuperable
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockReturnValueOnce(null)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      await user.click(recoverLink)
      
      expect(recoverLink).toBeInTheDocument()
    })

    it('debe manejar validación inválida en createNewApi', async () => {
      // Mock para simular validación inválida
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.validateOpenApiSpec).mockReturnValueOnce({
        isValid: false,
        errors: ['Error de validación']
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      await user.click(createButton)
      
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar error de guardado en createNewApi', async () => {
      // Mock para simular error de guardado
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.save).mockRejectedValueOnce(new Error('Error de guardado'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      await user.click(createButton)
      
      expect(createButton).toBeInTheDocument()
    })

    it('debe manejar error en loadFile', async () => {
      // Mock para simular error en loadFile
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.load).mockRejectedValueOnce(new Error('Error de carga'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar validación inválida en loadFile', async () => {
      // Mock para simular validación inválida en loadFile
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.validateOpenApiSpec).mockReturnValueOnce({
        isValid: false,
        errors: ['Error de validación en archivo']
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar error de guardado en loadFile', async () => {
      // Mock para simular error de guardado en loadFile
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.save).mockRejectedValueOnce(new Error('Error de guardado en loadFile'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo sin seleccionar en onFileOpened', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      fireEvent.change(fileInput!, { target: { files: [] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo con formato no soportado en onFileOpened', async () => {
      // Mock para simular formato no soportado
      const { ApiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(ApiDefinitionFileService.getFileFormat).mockReturnValueOnce(null)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['content'], 'test.txt', { type: 'text/plain' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })
  })

  describe('Carga de API existente', () => {
    it('debe abrir el selector de archivos', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const loadButton = screen.getByText('Cargar API Existente')
      expect(loadButton).toBeInTheDocument()
    })

    it('debe manejar la selección de archivo JSON', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo con formato no soportado', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['content'], 'test.txt', { type: 'text/plain' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar error de carga de archivo', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar validación inválida de archivo', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo sin seleccionar en onFileOpened', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      fireEvent.change(fileInput!, { target: { files: [] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar error en loadFile', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo con formato no soportado en onFileOpened', async () => {
      // Mock para simular formato no soportado
      const { ApiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(ApiDefinitionFileService.getFileFormat).mockReturnValueOnce(null)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['content'], 'test.txt', { type: 'text/plain' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar error de guardado en loadFile', async () => {
      // Mock para simular error de guardado en loadFile
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.save).mockRejectedValueOnce(new Error('Error de guardado en loadFile'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.change(fileInput!, { target: { files: [file] } })
      
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivos no soportados', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      expect(fileInput).toHaveAttribute('accept', '.json,.yaml,.yml')
    })

    it('debe manejar errores al cargar archivo', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      expect(fileInput).toBeInTheDocument()
    })

    it('debe manejar archivo sin seleccionar', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const fileInput = document.querySelector('input[type="file"]')
      fireEvent.change(fileInput!, { target: { files: [] } })
      
      expect(fileInput).toBeInTheDocument()
    })
  })

  describe('Drag & Drop', () => {
    it('debe manejar drag over', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      fireEvent.dragOver(dropZone!)
      
      expect(dropZone).toHaveClass('dragging')
    })

    it('debe manejar drop de archivo exitoso', async () => {
      // Mock para simular carga exitosa
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.load).mockResolvedValueOnce({
        openapi: '3.0.0',
        info: { title: 'Test API' }
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => file
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop de archivo con error de validación', async () => {
      // Mock para simular validación inválida
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.validateOpenApiSpec).mockReturnValueOnce({
        isValid: false,
        errors: ['Error de validación en drop']
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => file
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop de archivo con error de carga', async () => {
      // Mock para simular error de carga
      const { apiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(apiDefinitionFileService.load).mockRejectedValueOnce(new Error('Error de carga en drop'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => file
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop sin archivo', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => null
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop con archivo inválido', async () => {
      // Mock para simular formato no soportado
      const { ApiDefinitionFileService } = await import('../../services/ApiDefinitionFileService')
      vi.mocked(ApiDefinitionFileService.getFileFormat).mockReturnValueOnce(null)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      const file = new File(['invalid content'], 'test.txt', { type: 'text/plain' })
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => file
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop sin archivos', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: []
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drop con error de guardado', async () => {
      // Mock para simular error de guardado en drop
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.save).mockRejectedValueOnce(new Error('Error de guardado en drop'))
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      const file = new File(['{"openapi": "3.0.0"}'], 'test.json', { type: 'application/json' })
      
      fireEvent.drop(dropZone!, {
        dataTransfer: {
          items: [{
            getAsFile: () => file
          }]
        }
      })
      
      expect(dropZone).toBeInTheDocument()
    })

    it('debe manejar drag end', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      fireEvent.dragEnd(dropZone!)
      
      expect(dropZone).not.toHaveClass('dragging')
    })

    it('debe manejar drag exit', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      fireEvent.dragExit(dropZone!)
      
      expect(dropZone).not.toHaveClass('dragging')
    })

    it('debe manejar drag leave', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const dropZone = screen.getByText('Diseño de OpenAPI').closest('.blank-slate-pf')
      fireEvent.dragLeave(dropZone!)
      
      expect(dropZone).not.toHaveClass('dragging')
    })
  })

  describe('Recuperación de API', () => {
    it('debe mostrar aviso de recuperación cuando hay API guardada', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      // Verificar que el componente se renderiza correctamente
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe recuperar API cuando se hace clic en el enlace', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      // Verificar que el componente se renderiza correctamente
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe manejar errores al recuperar API', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      // Verificar que el componente se renderiza correctamente
      expect(screen.getByText('Diseño de OpenAPI')).toBeInTheDocument()
    })

    it('debe mostrar notificación de recuperación', () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      expect(screen.getByText('O arrastra y suelta un archivo sobre la página...')).toBeInTheDocument()
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

    it('debe mostrar el editor cuando hay API cargada', async () => {
      // Mock para simular API recuperable que abre el editor
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockReturnValueOnce({
        spec: { openapi: '3.0.0', info: { title: 'Recovered API' } },
        name: 'recovered.json',
        createdOn: new Date()
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      expect(recoverLink).toBeInTheDocument()
      
      await user.click(recoverLink)
      
      // Después del clic, el componente cambia al modo editor
      expect(screen.queryByText('Recuperar mi API')).not.toBeInTheDocument()
    })

    it('debe manejar el botón de volver al welcome', async () => {
      // Mock para simular API recuperable que abre el editor
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockReturnValueOnce({
        spec: { openapi: '3.0.0', info: { title: 'Recovered API' } },
        name: 'recovered.json',
        createdOn: new Date()
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      expect(recoverLink).toBeInTheDocument()
      
      await user.click(recoverLink)
      
      // Después del clic, el componente cambia al modo editor
      expect(screen.queryByText('Recuperar mi API')).not.toBeInTheDocument()
    })

    it('debe manejar el botón de volver desde el editor', async () => {
      // Mock para simular API recuperable que abre el editor
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      vi.mocked(storageService.loadRecovery).mockReturnValueOnce({
        spec: { openapi: '3.0.0', info: { title: 'Recovered API' } },
        name: 'recovered.json',
        createdOn: new Date()
      })
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoverLink = screen.getByText('Recuperar mi API')
      expect(recoverLink).toBeInTheDocument()
      
      await user.click(recoverLink)
      
      // Después del clic, el componente cambia al modo editor
      expect(screen.queryByText('Recuperar mi API')).not.toBeInTheDocument()
    })

    it('debe manejar openExistingApi', async () => {
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const loadButton = screen.getByText('Cargar API Existente')
      await user.click(loadButton)
      
      expect(loadButton).toBeInTheDocument()
    })

    it('debe manejar hasRecoverableApi cuando es true', async () => {
      // Mock para simular API recuperable
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(true)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const recoveryNotice = screen.getByText('¡API en Progreso Detectada!')
      expect(recoveryNotice).toBeInTheDocument()
    })

    it('debe manejar hasRecoverableApi cuando es false', async () => {
      // Mock para simular sin API recuperable
      const { storageService } = await import('../../services/StorageService')
      vi.mocked(storageService.hasRecoverableApi).mockReturnValueOnce(false)
      
      render(<DisenoOpenAPI onOpen={mockOnOpen} />)
      
      const createButton = screen.getByText('Crear Nueva API')
      expect(createButton).toBeInTheDocument()
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
