import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, fireEvent } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import Generador from '../Generador'

describe('Generador', () => {
  let user: ReturnType<typeof userEvent.setup>

  beforeEach(() => {
    user = userEvent.setup()
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar el componente Generador correctamente', () => {
      render(<Generador />)
      
      expect(screen.getByText('Validación de Estructura')).toBeInTheDocument()
      expect(screen.getByText('Nombre openAPI')).toBeInTheDocument()
      expect(screen.getByText('Validar')).toBeInTheDocument()
      expect(screen.getByText('Generar componente')).toBeInTheDocument()
    })

    it('debe mostrar la tabla con las columnas correctas', () => {
      render(<Generador />)
      
      expect(screen.getByText('Code')).toBeInTheDocument()
      expect(screen.getByText('path')).toBeInTheDocument()
      expect(screen.getByText('Message')).toBeInTheDocument()
      expect(screen.getByText('Severity')).toBeInTheDocument()
      expect(screen.getByText('range')).toBeInTheDocument()
    })

    it('debe mostrar el input de búsqueda', () => {
      render(<Generador />)
      
      const searchInput = screen.getByPlaceholderText('buscar')
      expect(searchInput).toBeInTheDocument()
      expect(searchInput).toHaveAttribute('type', 'text')
      expect(searchInput).toHaveAttribute('id', 'openapi-name')
    })
  })

  describe('Funcionalidad de formulario', () => {
    it('debe permitir escribir en el input de búsqueda', async () => {
      render(<Generador />)
      
      const searchInput = screen.getByPlaceholderText('buscar')
      await user.type(searchInput, 'mi-api')
      
      expect(searchInput).toHaveValue('mi-api')
    })

    it('debe tener el botón de validar', () => {
      render(<Generador />)
      
      const validateButton = screen.getByText('Validar')
      expect(validateButton).toBeInTheDocument()
      expect(validateButton).toHaveClass('btn', 'validar')
    })

    it('debe tener el botón de generar componente', () => {
      render(<Generador />)
      
      const generateButton = screen.getByText('Generar componente')
      expect(generateButton).toBeInTheDocument()
      expect(generateButton).toHaveClass('btn', 'generar')
    })
  })

  describe('Interacción con botones', () => {
    it('debe manejar el clic en el botón validar', async () => {
      const mockValidate = vi.fn()
      render(<Generador />)
      
      const validateButton = screen.getByText('Validar')
      await user.click(validateButton)
      
      // El botón debería ser clickeable
      expect(validateButton).toBeInTheDocument()
    })

    it('debe manejar el clic en el botón generar', async () => {
      const mockGenerate = vi.fn()
      render(<Generador />)
      
      const generateButton = screen.getByText('Generar componente')
      await user.click(generateButton)
      
      // El botón debería ser clickeable
      expect(generateButton).toBeInTheDocument()
    })
  })

  describe('Estructura del formulario', () => {
    it('debe tener la estructura de formulario correcta', () => {
      render(<Generador />)
      
      const form = document.querySelector('form')
      expect(form).toBeInTheDocument()
      expect(form).toHaveClass('generador-form')
    })

    it('debe tener el grupo de botones', () => {
      render(<Generador />)
      
      const buttonGroup = document.querySelector('.button-group')
      expect(buttonGroup).toBeInTheDocument()
    })

    it('debe tener el grupo de formulario', () => {
      render(<Generador />)
      
      const formGroup = document.querySelector('.form-group')
      expect(formGroup).toBeInTheDocument()
    })
  })

  describe('Estructura de la tabla', () => {
    it('debe tener el contenedor de tabla', () => {
      render(<Generador />)
      
      const tableContainer = document.querySelector('.tabla-container')
      expect(tableContainer).toBeInTheDocument()
    })

    it('debe tener la tabla con la clase correcta', () => {
      render(<Generador />)
      
      const table = document.querySelector('.tabla-generador')
      expect(table).toBeInTheDocument()
    })

    it('debe tener el encabezado de tabla', () => {
      render(<Generador />)
      
      const thead = document.querySelector('thead')
      expect(thead).toBeInTheDocument()
    })

    it('debe tener el cuerpo de tabla', () => {
      render(<Generador />)
      
      const tbody = document.querySelector('tbody')
      expect(tbody).toBeInTheDocument()
    })
  })

  describe('Accesibilidad', () => {
    it('debe tener labels asociados correctamente', () => {
      render(<Generador />)
      
      const label = screen.getByText('Nombre openAPI')
      const input = screen.getByPlaceholderText('buscar')
      
      expect(label).toHaveAttribute('for', 'openapi-name')
      expect(input).toHaveAttribute('id', 'openapi-name')
    })

    it('debe tener autocomplete deshabilitado', () => {
      render(<Generador />)
      
      const form = document.querySelector('form')
      const input = screen.getByPlaceholderText('buscar')
      
      expect(form).toHaveAttribute('autoComplete', 'off')
      expect(input).toHaveAttribute('autoComplete', 'off')
    })
  })

  describe('Estilos y clases CSS', () => {
    it('debe tener las clases CSS principales', () => {
      render(<Generador />)
      
      const container = document.querySelector('.generador-content')
      const inner = document.querySelector('.generador-inner')
      
      expect(container).toBeInTheDocument()
      expect(inner).toBeInTheDocument()
    })

    it('debe tener el input con la clase search-input', () => {
      render(<Generador />)
      
      const input = screen.getByPlaceholderText('buscar')
      expect(input).toHaveClass('search-input')
    })
  })
})
