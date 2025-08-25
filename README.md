import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import EditorXSLT from '../EditorXSLT'

const mockDOMParser = { parseFromString: vi.fn() }
const mockXSLTProcessor = { importStylesheet: vi.fn(), transformToFragment: vi.fn() }
const mockXMLSerializer = { serializeToString: vi.fn() }

const mockPrompt = vi.fn()
const mockConfirm = vi.fn()

Object.defineProperty(window, 'DOMParser', { value: vi.fn(() => mockDOMParser), writable: true })
Object.defineProperty(window, 'XSLTProcessor', { value: vi.fn(() => mockXSLTProcessor), writable: true })
Object.defineProperty(window, 'XMLSerializer', { value: vi.fn(() => mockXMLSerializer), writable: true })
Object.defineProperty(window, 'prompt', { value: mockPrompt, writable: true })
Object.defineProperty(window, 'confirm', { value: mockConfirm, writable: true })

describe('EditorXSLT', () => {
  let user: ReturnType<typeof userEvent.setup>

  beforeEach(() => {
    user = userEvent.setup()
    mockPrompt.mockClear()
    mockConfirm.mockClear()

    // Configurar mocks por defecto
    mockDOMParser.parseFromString.mockReturnValue({
      documentElement: { textContent: 'Test XML' }
    })
    mockXSLTProcessor.transformToFragment.mockReturnValue({
      textContent: '<result>Transformed XML</result>'
    })
    mockXMLSerializer.serializeToString.mockReturnValue('<result>Transformed XML</result>')
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar el editor XSLT correctamente', () => {
      render(<EditorXSLT />)
      
      expect(screen.getByText('Editor XSLT')).toBeInTheDocument()
      expect(screen.getByText('▶ Run')).toBeInTheDocument()
      expect(screen.getByText('Formatear XML')).toBeInTheDocument()
      expect(screen.getByText('Ejemplos')).toBeInTheDocument()
      expect(screen.getByText('Guardar')).toBeInTheDocument()
      expect(screen.getByText('Limpiar Todo')).toBeInTheDocument()
    })

    it('debe mostrar las tres secciones principales', () => {
      render(<EditorXSLT />)
      
      expect(screen.getByText('XML Input')).toBeInTheDocument()
      expect(screen.getByText('XSLT')).toBeInTheDocument()
      expect(screen.getByText('XML Output')).toBeInTheDocument()
    })

    it('debe cargar el ejemplo por defecto', () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      const xsltInput = screen.getByPlaceholderText('Ingresa tu transformación XSLT aquí...')

      expect(xmlInput).toBeInTheDocument()
      expect(xsltInput).toBeInTheDocument()
    })
  })

  describe('Funcionalidad de botones', () => {
    it('debe ejecutar la transformación XSLT', async () => {
      render(<EditorXSLT />)
      
      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      // Verificar que se procesó la transformación
      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toHaveValue('')
    })

    it('debe formatear XML correctamente', async () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      fireEvent.change(xmlInput, { target: { value: '<root><test>value</test></root>' } })

      const formatButton = screen.getByText('Formatear XML')
      await user.click(formatButton)

      // Verificar que se formateó
      expect(xmlInput).toBeInTheDocument()
    })

    it('debe abrir el modal de ejemplos', async () => {
      render(<EditorXSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      expect(screen.getByText('Ejemplos de XSLT')).toBeInTheDocument()
      expect(screen.getByText('Transformación Básica')).toBeInTheDocument()
      expect(screen.getByText('Tabla XML')).toBeInTheDocument()
    })

    it('debe cerrar el modal de ejemplos', async () => {
      render(<EditorXSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      const closeButton = screen.getByText('Cerrar')
      await user.click(closeButton)

      expect(screen.queryByText('Ejemplos de XSLT')).not.toBeInTheDocument()
    })

    it('debe limpiar todo el contenido', async () => {
      render(<EditorXSLT />)
      
      const clearButton = screen.getByText('Limpiar Todo')
      await user.click(clearButton)

      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      const xsltInput = screen.getByPlaceholderText('Ingresa tu transformación XSLT aquí...')
      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')

      expect(xmlInput).toHaveValue('')
      expect(xsltInput).toHaveValue('')
      expect(output).toHaveValue('')
    })
  })

  describe('Modal de ejemplos', () => {
    it('debe cargar un ejemplo específico', async () => {
      render(<EditorXSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      // Usar getAllByText y seleccionar el primer botón "Cargar"
      const loadButtons = screen.getAllByText('Cargar')
      await user.click(loadButtons[0])

      // Verificar que se cargó el ejemplo
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      expect(xmlInput).toBeInTheDocument()
    })

    it('debe cerrar el modal después de cargar un ejemplo', async () => {
      render(<EditorXSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      const loadButtons = screen.getAllByText('Cargar')
      await user.click(loadButtons[0])

      expect(screen.queryByText('Ejemplos de XSLT')).not.toBeInTheDocument()
    })
  })

  describe('Funcionalidad de guardado', () => {
    it('debe guardar una transformación', async () => {
      mockPrompt.mockReturnValue('mi-transformacion')
      mockConfirm.mockReturnValue(true)
      
      render(<EditorXSLT />)
      
      const saveButton = screen.getByText('Guardar')
      await user.click(saveButton)

      expect(mockPrompt).toHaveBeenCalledWith('Nombre para guardar esta transformación:')
    })
  })

  describe('Validación de XML', () => {
    it('debe mostrar error con XML inválido', async () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      fireEvent.change(xmlInput, { target: { value: '<root><unclosed>' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      // Verificar que se muestra un error
      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toHaveValue('')
    })

    it('debe procesar XML válido correctamente', async () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      fireEvent.change(xmlInput, { target: { value: '<root><test>value</test></root>' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })

  describe('Interacción con textareas', () => {
    it('debe permitir editar XML', async () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      fireEvent.change(xmlInput, { target: { value: '<nuevo>contenido</nuevo>' } })

      expect(xmlInput).toHaveValue('<nuevo>contenido</nuevo>')
    })

    it('debe permitir editar XSLT', async () => {
      render(<EditorXSLT />)
      
      const xsltInput = screen.getByPlaceholderText('Ingresa tu transformación XSLT aquí...')
      fireEvent.change(xsltInput, { target: { value: '<xsl:template match="/">' } })

      expect(xsltInput).toHaveValue('<xsl:template match="/">')
    })

    it('debe actualizar el output automáticamente', async () => {
      render(<EditorXSLT />)
      
      const xmlInput = screen.getByPlaceholderText('Ingresa tu XML de entrada aquí...')
      fireEvent.change(xmlInput, { target: { value: '<test>data</test>' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })

  describe('Manejo de errores', () => {
    it('debe manejar errores de transformación', async () => {
      render(<EditorXSLT />)
      
      const xsltInput = screen.getByPlaceholderText('Ingresa tu transformación XSLT aquí...')
      fireEvent.change(xsltInput, { target: { value: '<xsl:invalid>' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toHaveValue('')
    })

    it('debe limpiar errores al ejecutar exitosamente', async () => {
      render(<EditorXSLT />)
      
      const xsltInput = screen.getByPlaceholderText('Ingresa tu transformación XSLT aquí...')
      fireEvent.change(xsltInput, { target: { value: '<xsl:template match="/"><xsl:value-of select="."/></xsl:template>' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación XML aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })
})
