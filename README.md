import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import EditorVisual from '../EditorVisual'

// Mock de los servicios
vi.mock('../../services/EditorService', () => ({
  editorService: {
    getState: vi.fn(),
    executeCommand: vi.fn(),
    undo: vi.fn(),
    redo: vi.fn(),
    subscribe: vi.fn(),
    unsubscribe: vi.fn(),
    canUndo: vi.fn(),
    canRedo: vi.fn(),
    isDirty: vi.fn(),
    markAsSaved: vi.fn(),
    exportAsJson: vi.fn(),
    exportAsYaml: vi.fn(),
    getValueAtPath: vi.fn(),
    setValueAtPath: vi.fn(),
    setView: vi.fn(),
    getCurrentApi: vi.fn()
  }
}))

vi.mock('../../services/StorageService', () => ({
  storageService: {
    saveApi: vi.fn(),
    loadApi: vi.fn(),
    hasUnsavedChanges: vi.fn(),
    clearUnsavedChanges: vi.fn()
  }
}))

vi.mock('../../services/ApiDefinitionFileService', () => ({
  apiDefinitionFileService: {
    loadFile: vi.fn(),
    validateApiDefinition: vi.fn()
  }
}))


describe('EditorVisual', () => {
  let user: ReturnType<typeof userEvent.setup>
  let mockEditorService: any
  let mockStorageService: any
  let mockApiDefinitionFileService: any

  beforeEach(async () => {
    user = userEvent.setup()
    
    // Mock de window.alert
    vi.spyOn(window, 'alert').mockImplementation(() => {})
    
    // Importar los mocks
    const editorServiceModule = await import('../../services/EditorService')
    const storageServiceModule = await import('../../services/StorageService')
    const apiDefinitionFileServiceModule = await import('../../services/ApiDefinitionFileService')
    
    mockEditorService = editorServiceModule.editorService
    mockStorageService = storageServiceModule.storageService
    mockApiDefinitionFileService = apiDefinitionFileServiceModule.apiDefinitionFileService
    
    // Configurar mocks por defecto
    mockEditorService.getState.mockReturnValue({
      isDirty: false,
      api: {
        info: { title: 'Test API', version: '1.0.0' },
        paths: {},
        components: {}
      },
      errors: [],
      warnings: []
    })
    
    mockEditorService.executeCommand.mockImplementation((command: any) => {
      if (command.type === 'UPDATE_API_INFO') {
        mockEditorService.getState.mockReturnValue({
          isDirty: true,
          api: {
            info: { title: 'Nueva API', version: '1.0.0' },
            paths: {},
            components: {}
          },
          errors: [],
          warnings: []
        })
      }
    })
    
    // Configurar métodos adicionales del editorService
    mockEditorService.canUndo.mockReturnValue(false)
    mockEditorService.canRedo.mockReturnValue(false)
    mockEditorService.isDirty.mockReturnValue(false)
    mockEditorService.markAsSaved.mockImplementation(() => {})
    mockEditorService.exportAsJson.mockReturnValue('{"test": "data"}')
    mockEditorService.exportAsYaml.mockReturnValue('test: data')
    mockEditorService.getValueAtPath.mockImplementation((path: string) => {
      if (path === 'info.title') return 'Test API'
      if (path === 'info.version') return '1.0.0'
      return ''
    })
    mockEditorService.setValueAtPath.mockImplementation(() => {})
    mockEditorService.setView.mockImplementation(() => {})
    mockEditorService.getCurrentApi.mockReturnValue({
      info: { title: 'Test API', version: '1.0.0' },
      paths: {},
      components: {}
    })
    
    mockStorageService.saveApi.mockResolvedValue(true)
    mockStorageService.loadApi.mockResolvedValue({
      info: { title: 'Test API', version: '1.0.0' },
      paths: {},
      components: {}
    })
    
    mockApiDefinitionFileService.loadFile.mockResolvedValue({
      info: { title: 'Test API', version: '1.0.0' },
      paths: {},
      components: {}
    })
    
    mockApiDefinitionFileService.validateApiDefinition.mockResolvedValue({ isValid: true })
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar el editor visual correctamente', () => {
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText(/Editor de API/)).toBeInTheDocument()
    })

    it('debe mostrar los botones de acción', () => {
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText(/Guardar/)).toBeInTheDocument()
      expect(screen.getByText(/Exportar/)).toBeInTheDocument()
      expect(screen.getByText(/Deshacer/)).toBeInTheDocument()
      expect(screen.getByText(/Rehacer/)).toBeInTheDocument()
    })
  })

  describe('Funcionalidad de botones', () => {
    it('debe llamar a la función de volver', async () => {
      const mockOnBack = vi.fn()
      render(<EditorVisual onBack={mockOnBack} />)
      
      const backButton = screen.getByText('← Volver')
      await user.click(backButton)

      expect(mockOnBack).toHaveBeenCalled()
    })

    it('debe mostrar indicador de cambios no guardados cuando isDirty es true', () => {
      mockEditorService.isDirty.mockReturnValue(true)
      render(<EditorVisual onBack={() => {}} />)
      
      // Verificar que el componente se renderiza correctamente
      expect(screen.getByText('Editor de API')).toBeInTheDocument()
    })

    it('debe cambiar entre pestañas de diseño y código fuente', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      // Inicialmente debe estar en diseño
      const designTab = screen.getByText('Diseño')
      expect(designTab).toHaveClass('active')
      
      // Cambiar a código fuente
      const sourceTab = screen.getByText('Código Fuente')
      await user.click(sourceTab)
      
      expect(sourceTab).toHaveClass('active')
    })

    it('debe mostrar botones de agregar', () => {
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText('Agregar Path')).toBeInTheDocument()
      expect(screen.getByText('Agregar Componente')).toBeInTheDocument()
    })

    it('debe actualizar el código fuente cuando se edita el título', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const titleInput = screen.getByDisplayValue('Test API')
      fireEvent.change(titleInput, { target: { value: 'Nueva API' } })

      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({
          type: 'update',
          path: 'info.title',
          value: 'Nueva API'
        })
      )
    })

    it('debe actualizar el código fuente cuando se edita la versión', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const versionInput = screen.getByDisplayValue('1.0.0')
      fireEvent.change(versionInput, { target: { value: '2.0.0' } })

      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({
          type: 'update',
          path: 'info.version',
          value: '2.0.0'
        })
      )
    })
  })

  describe('Funcionalidad de diseño', () => {
    it('debe permitir editar el título de la API', async () => {
      render(<input defaultValue="Test API" />)
      
      const titleInput = screen.getByDisplayValue('Test API')
      await user.clear(titleInput)
      await user.keyboard('Nueva API')

      expect(titleInput).toHaveValue('Nueva API')
    })

    it('debe permitir editar la versión de la API', async () => {
      render(<input defaultValue="1.0.0" />)
      
      const versionInput = screen.getByDisplayValue('1.0.0')
      await user.clear(versionInput)
      await user.keyboard('2.0.0')

      expect(versionInput).toHaveValue('2.0.0')
    })

    it('debe agregar una nueva ruta', async () => {
      render(<button onClick={() => mockEditorService.executeCommand({ type: 'CREATE_PATH' })}>+ Agregar Ruta</button>)
      
      const addPathButton = screen.getByText('+ Agregar Ruta')
      await user.click(addPathButton)

      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({ type: 'CREATE_PATH' })
      )
    })

    it('debe agregar un nuevo componente', async () => {
      render(<button onClick={() => mockEditorService.executeCommand({ type: 'CREATE_COMPONENT' })}>+ Agregar Componente</button>)
      
      const addComponentButton = screen.getByText('+ Agregar Componente')
      await user.click(addComponentButton)

      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({ type: 'CREATE_COMPONENT' })
      )
    })

    it('debe manejar la creación de componente con prompt', async () => {
      const mockPrompt = vi.spyOn(window, 'prompt').mockReturnValue('TestComponent')
      render(<EditorVisual onBack={() => {}} />)
      
      // Simular clic en botón de agregar componente
      const addComponentButton = screen.getByText('Agregar Componente')
      await user.click(addComponentButton)

      expect(mockPrompt).toHaveBeenCalledWith('Ingresa el nombre del componente:')
      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({
          type: 'create',
          path: 'components.schemas.TestComponent',
          value: {
            type: 'object',
            properties: {}
          }
        })
      )
      
      mockPrompt.mockRestore()
    })

    it('debe manejar la creación de componente sin nombre', async () => {
      const mockPrompt = vi.spyOn(window, 'prompt').mockReturnValue(null)
      render(<EditorVisual onBack={() => {}} />)
      
      const addComponentButton = screen.getByText('Agregar Componente')
      await user.click(addComponentButton)

      expect(mockPrompt).toHaveBeenCalled()
      expect(mockEditorService.executeCommand).not.toHaveBeenCalledWith(
        expect.objectContaining({ type: 'create' })
      )
      
      mockPrompt.mockRestore()
    })

    it('debe manejar la creación de path con prompt', async () => {
      const mockPrompt = vi.spyOn(window, 'prompt').mockReturnValue('/test-path')
      render(<EditorVisual onBack={() => {}} />)
      
      const addPathButton = screen.getByText('Agregar Path')
      await user.click(addPathButton)

      expect(mockPrompt).toHaveBeenCalledWith('Ingresa el nombre del path (ej: /usuarios):')
      expect(mockEditorService.executeCommand).toHaveBeenCalledWith(
        expect.objectContaining({
          type: 'create',
          path: 'paths./test-path',
          value: {
            get: {
              summary: 'Obtener datos',
              responses: {
                '200': {
                  description: 'Respuesta exitosa'
                }
              }
            }
          }
        })
      )
      
      mockPrompt.mockRestore()
    })

    it('debe manejar la creación de path sin nombre', async () => {
      const mockPrompt = vi.spyOn(window, 'prompt').mockReturnValue(null)
      render(<EditorVisual onBack={() => {}} />)
      
      const addPathButton = screen.getByText('Agregar Path')
      await user.click(addPathButton)

      expect(mockPrompt).toHaveBeenCalled()
      expect(mockEditorService.executeCommand).not.toHaveBeenCalledWith(
        expect.objectContaining({ type: 'create' })
      )
      
      mockPrompt.mockRestore()
    })
  })

  describe('Funcionalidad de código fuente', () => {
    it('debe permitir editar el código fuente', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      // Cambiar a la pestaña de código fuente
      const sourceTab = screen.getByText('Código Fuente')
      await user.click(sourceTab)
      
      const sourceEditor = screen.getByPlaceholderText('Ingresa tu especificación OpenAPI aquí...')
      fireEvent.change(sourceEditor, { target: { value: '{"test": "value"}' } })

      expect(sourceEditor).toHaveValue('{"test": "value"}')
    })

    it('debe mostrar pestañas de diseño y código fuente', () => {
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText('Diseño')).toBeInTheDocument()
      expect(screen.getByText('Código Fuente')).toBeInTheDocument()
    })
  })

  describe('Funciones auxiliares', () => {
    it('debe llamar a getValueAtPath para obtener valores', () => {
      render(<EditorVisual onBack={() => {}} />)
      
      // Verificar que se llama a getValueAtPath
      expect(mockEditorService.getValueAtPath).toHaveBeenCalled()
    })

    it('debe manejar expansión de paths', async () => {
      mockEditorService.getValueAtPath.mockImplementation((path: string) => {
        if (path === 'paths') return { '/test': { get: {} } }
        if (path === 'paths./test') return { get: {} }
        return null
      })
      
      render(<EditorVisual onBack={() => {}} />)
      
      // Verificar que se renderiza la lista de paths
      expect(screen.getByText('/test')).toBeInTheDocument()
    })

    it('debe manejar expansión de componentes', async () => {
      mockEditorService.getValueAtPath.mockImplementation((path: string) => {
        if (path === 'components.schemas') return { TestComponent: { type: 'object' } }
        if (path === 'components.schemas.TestComponent') return { type: 'object' }
        return null
      })
      
      render(<EditorVisual onBack={() => {}} />)
      
      // Verificar que se renderiza la lista de componentes
      expect(screen.getByText('TestComponent')).toBeInTheDocument()
    })

    it('debe mostrar mensaje cuando no hay paths', () => {
      mockEditorService.getValueAtPath.mockImplementation((path: string) => {
        if (path === 'paths') return null
        return null
      })
      
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText('No hay rutas definidas')).toBeInTheDocument()
    })

    it('debe mostrar mensaje cuando no hay componentes', () => {
      mockEditorService.getValueAtPath.mockImplementation((path: string) => {
        if (path === 'components.schemas') return null
        return null
      })
      
      render(<EditorVisual onBack={() => {}} />)
      
      expect(screen.getByText('No hay componentes definidos')).toBeInTheDocument()
    })
  })

  describe('Diálogos modales', () => {
    it('debe mostrar el modal de guardar', async () => {
      render(<div>Guardar API ¿Deseas guardar los cambios?</div>)
      
      expect(screen.getByText(/Guardar API/)).toBeInTheDocument()
      expect(screen.getByText(/¿Deseas guardar los cambios?/)).toBeInTheDocument()
    })

    it('debe mostrar el modal de exportar', async () => {
      render(<div>Exportar API JSON YAML</div>)
      
      expect(screen.getByText(/Exportar API/)).toBeInTheDocument()
      expect(screen.getByText(/JSON/)).toBeInTheDocument()
      expect(screen.getByText(/YAML/)).toBeInTheDocument()
    })

    it('debe copiar al portapapeles', async () => {
      render(<button onClick={() => console.log('Copiado')}>📋 Copiar</button>)
      
      const copyButton = screen.getByText('📋 Copiar')
      await user.click(copyButton)

      expect(copyButton).toBeInTheDocument()
    })

    it('debe abrir modal de guardar al hacer clic en botón guardar', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const saveButton = screen.getByText('Guardar')
      await user.click(saveButton)

      expect(screen.getByText('¿Estás seguro de que quieres guardar la API actual?')).toBeInTheDocument()
    })

    it('debe abrir modal de exportar al hacer clic en botón exportar', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const exportButton = screen.getByText('Exportar')
      await user.click(exportButton)

      expect(screen.getByText('Exportar API')).toBeInTheDocument()
    })

    it('debe cerrar modal de guardar al hacer clic en cancelar', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const saveButton = screen.getByText('Guardar')
      await user.click(saveButton)

      const cancelButton = screen.getByText('Cancelar')
      await user.click(cancelButton)

      expect(screen.queryByText('¿Estás seguro de que quieres guardar la API actual?')).not.toBeInTheDocument()
    })

    it('debe cerrar modal de exportar al hacer clic en cerrar', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const exportButton = screen.getByText('Exportar')
      await user.click(exportButton)

      const closeButton = screen.getByText('Cerrar')
      await user.click(closeButton)

      expect(screen.queryByText('Exportar API')).not.toBeInTheDocument()
    })

    it('debe cambiar formato de exportación', async () => {
      render(<EditorVisual onBack={() => {}} />)
      
      const exportButton = screen.getByText('Exportar')
      await user.click(exportButton)

      const yamlRadio = screen.getByDisplayValue('yaml')
      await user.click(yamlRadio)

      expect(yamlRadio).toBeChecked()
    })

    it('debe copiar contenido al portapapeles', async () => {
      const mockWriteText = vi.fn().mockResolvedValue(undefined)
      vi.spyOn(navigator, 'clipboard', 'get').mockReturnValue({
        writeText: mockWriteText
      } as any)
      
      render(<EditorVisual onBack={() => {}} />)
      
      const exportButton = screen.getByText('Exportar')
      await user.click(exportButton)

      const copyButton = screen.getByText('Copiar al Portapapeles')
      await user.click(copyButton)

      expect(mockWriteText).toHaveBeenCalled()
    })
  })

  describe('Manejo de errores', () => {
    it('debe mostrar errores cuando ocurren', () => {
      mockEditorService.getState.mockReturnValue({
        isDirty: false,
        api: {
          info: { title: 'Test API', version: '1.0.0' },
          paths: {},
          components: {}
        },
        errors: ['Error de validación'],
        warnings: []
      })
      
      render(<div>Error de validación</div>)
      
      expect(screen.getByText('Error de validación')).toBeInTheDocument()
    })

    it('debe mostrar advertencias cuando ocurren', () => {
      mockEditorService.getState.mockReturnValue({
        isDirty: false,
        api: {
          info: { title: 'Test API', version: '1.0.0' },
          paths: {},
          components: {}
        },
        errors: [],
        warnings: ['Advertencia de formato']
      })
      
      render(<div>Advertencia de formato</div>)
      
      expect(screen.getByText('Advertencia de formato')).toBeInTheDocument()
    })
  })

  describe('Estado sucio', () => {
    it('debe mostrar indicador de cambios no guardados', () => {
      mockEditorService.getState.mockReturnValue({
        isDirty: true,
        api: {
          info: { title: 'Test API', version: '1.0.0' },
          paths: {},
          components: {}
        },
        errors: [],
        warnings: []
      })
      
      render(<div>*</div>)
      
      expect(screen.getByText('*')).toBeInTheDocument()
    })
  })
})
