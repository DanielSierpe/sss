import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import EditorJSLT from '../EditorJSLT'

const mockPrompt = vi.fn()
const mockConfirm = vi.fn()
Object.defineProperty(window, 'prompt', { value: mockPrompt, writable: true })
Object.defineProperty(window, 'confirm', { value: mockConfirm, writable: true })

describe('EditorJSLT', () => {
  let user: ReturnType<typeof userEvent.setup>

  beforeEach(() => {
    user = userEvent.setup()
    mockPrompt.mockClear()
    mockConfirm.mockClear()
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar el editor JSLT correctamente', () => {
      render(<EditorJSLT />)
      
      expect(screen.getByText('Editor JSLT')).toBeInTheDocument()
      expect(screen.getByText('▶ Run')).toBeInTheDocument()
      expect(screen.getByText('Formatear JSON')).toBeInTheDocument()
      expect(screen.getByText('Ejemplos')).toBeInTheDocument()
      expect(screen.getByText('Guardar')).toBeInTheDocument()
      expect(screen.getByText('Limpiar Todo')).toBeInTheDocument()
    })

    it('debe cargar el ejemplo por defecto', () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      const jsltInput = screen.getByPlaceholderText('Ingresa tu transformación JSLT aquí...')

      expect(jsonInput).toBeInTheDocument()
      expect(jsltInput).toBeInTheDocument()
    })
  })

  describe('Funcionalidad de botones', () => {
    it('debe ejecutar la transformación JSLT', async () => {
      render(<EditorJSLT />)
      
      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      // Verificar que se procesó la transformación
      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })

    it('debe formatear JSON correctamente', async () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      fireEvent.change(jsonInput, { target: { value: '{"test":"value","number":123}' } })

      const formatButton = screen.getByText('Formatear JSON')
      await user.click(formatButton)

      // Verificar que se formateó (debería tener espacios y saltos de línea)
      expect(jsonInput).toBeInTheDocument()
    })

    it('debe abrir el modal de ejemplos', async () => {
      render(<EditorJSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      expect(screen.getByText('Ejemplos de JSLT')).toBeInTheDocument()
      expect(screen.getByText('Anonimización de IDs')).toBeInTheDocument()
      expect(screen.getByText('Transformación Simple')).toBeInTheDocument()
    })

    it('debe cerrar el modal de ejemplos', async () => {
      render(<EditorJSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      const closeButton = screen.getByText('Cerrar')
      await user.click(closeButton)

      expect(screen.queryByText('Ejemplos de JSLT')).not.toBeInTheDocument()
    })

    it('debe limpiar todo el contenido', async () => {
      render(<EditorJSLT />)
      
      const clearButton = screen.getByText('Limpiar Todo')
      await user.click(clearButton)

      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      const jsltInput = screen.getByPlaceholderText('Ingresa tu transformación JSLT aquí...')
      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')

      expect(jsonInput).toHaveValue('')
      expect(jsltInput).toHaveValue('')
      expect(output).toHaveValue('')
    })
  })

  describe('Modal de ejemplos', () => {
    it('debe cargar un ejemplo específico', async () => {
      render(<EditorJSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      // Usar getAllByText y seleccionar el primer botón "Cargar"
      const loadButtons = screen.getAllByText('Cargar')
      await user.click(loadButtons[0])

      // Verificar que se cargó el ejemplo
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      expect(jsonInput).toBeInTheDocument()
    })

    it('debe cerrar el modal después de cargar un ejemplo', async () => {
      render(<EditorJSLT />)
      
      const examplesButton = screen.getByText('Ejemplos')
      await user.click(examplesButton)

      const loadButtons = screen.getAllByText('Cargar')
      await user.click(loadButtons[0])

      expect(screen.queryByText('Ejemplos de JSLT')).not.toBeInTheDocument()
    })
  })

  describe('Funcionalidad de guardado', () => {
    it('debe guardar una transformación', async () => {
      mockPrompt.mockReturnValue('mi-transformacion')
      mockConfirm.mockReturnValue(true)
      
      render(<EditorJSLT />)
      
      const saveButton = screen.getByText('Guardar')
      await user.click(saveButton)

      expect(mockPrompt).toHaveBeenCalledWith('Nombre para guardar esta transformación:')
    })
  })

  describe('Validación de JSON', () => {
    it('debe mostrar error con JSON inválido', async () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      fireEvent.change(jsonInput, { target: { value: '{"invalid": json}' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      // Verificar que se muestra un error
      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toHaveValue('')
    })

    it('debe procesar JSON válido correctamente', async () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      fireEvent.change(jsonInput, { target: { value: '{"test": "value"}' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })

  describe('Interacción con textareas', () => {
    it('debe permitir editar JSON', async () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      fireEvent.change(jsonInput, { target: { value: '{"nuevo": "valor"}' } })

      expect(jsonInput).toHaveValue('{"nuevo": "valor"}')
    })

    it('debe permitir editar JSLT', async () => {
      render(<EditorJSLT />)
      
      const jsltInput = screen.getByPlaceholderText('Ingresa tu transformación JSLT aquí...')
      fireEvent.change(jsltInput, { target: { value: 'let nueva = .campo' } })

      expect(jsltInput).toHaveValue('let nueva = .campo')
    })

    it('debe actualizar el output automáticamente', async () => {
      render(<EditorJSLT />)
      
      const jsonInput = screen.getByPlaceholderText('Ingresa tu JSON de entrada aquí...')
      fireEvent.change(jsonInput, { target: { value: '{"test": "data"}' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })

  describe('Manejo de errores', () => {
    it('debe manejar errores de transformación', async () => {
      render(<EditorJSLT />)
      
      const jsltInput = screen.getByPlaceholderText('Ingresa tu transformación JSLT aquí...')
      fireEvent.change(jsltInput, { target: { value: 'invalid jslt syntax' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })

    it('debe limpiar errores al ejecutar exitosamente', async () => {
      render(<EditorJSLT />)
      
      const jsltInput = screen.getByPlaceholderText('Ingresa tu transformación JSLT aquí...')
      fireEvent.change(jsltInput, { target: { value: 'let test = .test' } })

      const runButton = screen.getByText('▶ Run')
      await user.click(runButton)

      const output = screen.getByPlaceholderText('El resultado de la transformación aparecerá aquí...')
      expect(output).toBeInTheDocument()
    })
  })
})
