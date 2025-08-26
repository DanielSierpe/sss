import { describe, it, expect, beforeEach, vi } from 'vitest'
import { render, screen} from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import Generador from '../Generador'
import { AuthProvider } from '../../contexts/AuthContext'

// Mock del contexto de autenticación
vi.mock('../../contexts/AuthContext', async () => {
  const actual = await vi.importActual('../../contexts/AuthContext')
  return {
    ...actual,
    useAuthContext: () => ({
      isAuthenticated: false,
      isLoading: false,
      error: null,
      token: null,
      handleAuthCode: vi.fn(),
      logout: vi.fn(),
    }),
  }
})

// Wrapper para envolver componentes con AuthProvider
const renderWithAuth = (component: React.ReactElement) => {
  return render(
    <AuthProvider>
      {component}
    </AuthProvider>
  )
}

describe('Generador', () => {
  let user: ReturnType<typeof userEvent.setup>

  beforeEach(() => {
    user = userEvent.setup()
  })

  describe('Renderizado inicial', () => {
    it('debe renderizar el componente Generador correctamente', () => {
      renderWithAuth(<Generador />)
      
      expect(screen.getByText('Validación de Estructura')).toBeInTheDocument()
      expect(screen.getByText('Nombre openAPI')).toBeInTheDocument()
      expect(screen.getByText('Validar')).toBeInTheDocument()
      expect(screen.getByText('Generar componente')).toBeInTheDocument()
    })

    it('debe mostrar la tabla con las columnas correctas', () => {
      renderWithAuth(<Generador />)
      
      expect(screen.getByText('Code')).toBeInTheDocument()
      expect(screen.getByText('path')).toBeInTheDocument()
      expect(screen.getByText('Message')).toBeInTheDocument()
      expect(screen.getByText('Severity')).toBeInTheDocument()
      expect(screen.getByText('range')).toBeInTheDocument()
    })

    it('debe mostrar el input de búsqueda', () => {
      renderWithAuth(<Generador />)
      
      const searchInput = screen.getByPlaceholderText('buscar')
      expect(searchInput).toBeInTheDocument()
      expect(searchInput).toHaveAttribute('type', 'text')
      expect(searchInput).toHaveAttribute('id', 'openapi-name')
    })
  })

  describe('Funcionalidad de formulario', () => {
    it('debe permitir escribir en el input de búsqueda', async () => {
      renderWithAuth(<Generador />)
      
      const searchInput = screen.getByPlaceholderText('buscar')
      await user.type(searchInput, 'mi-api')
      
      expect(searchInput).toHaveValue('mi-api')
    })

    it('debe tener el botón de validar', () => {
      renderWithAuth(<Generador />)
      
      const validateButton = screen.getByText('Validar')
      expect(validateButton).toBeInTheDocument()
      expect(validateButton).toHaveClass('btn', 'validar')
    })

    it('debe tener el botón de generar componente', () => {
      renderWithAuth(<Generador />)
      
      const generateButton = screen.getByText('Generar componente')
      expect(generateButton).toBeInTheDocument()
      expect(generateButton).toHaveClass('btn', 'generar')
    })
  })

  describe('Interacción con botones', () => {
    it('debe manejar el clic en el botón validar', async () => {
      renderWithAuth(<Generador />)
      
      const validateButton = screen.getByText('Validar')
      await user.click(validateButton)
      
      // El botón debería ser clickeable
      expect(validateButton).toBeInTheDocument()
    })

    it('debe manejar el clic en el botón generar', async () => {
      renderWithAuth(<Generador />)
      
      const generateButton = screen.getByText('Generar componente')
      await user.click(generateButton)
      
      // El botón debería ser clickeable
      expect(generateButton).toBeInTheDocument()
    })
  })

  describe('Estructura del formulario', () => {
    it('debe tener la estructura de formulario correcta', () => {
      renderWithAuth(<Generador />)
      
      const form = document.querySelector('form')
      expect(form).toBeInTheDocument()
      expect(form).toHaveClass('generador-form')
    })

    it('debe tener el grupo de botones', () => {
      renderWithAuth(<Generador />)
      
      const buttonGroup = document.querySelector('.button-group')
      expect(buttonGroup).toBeInTheDocument()
    })

    it('debe tener el grupo de formulario', () => {
      renderWithAuth(<Generador />)
      
      const formGroup = document.querySelector('.form-group')
      expect(formGroup).toBeInTheDocument()
    })
  })

  describe('Estructura de la tabla', () => {
    it('debe tener el contenedor de tabla', () => {
      renderWithAuth(<Generador />)
      
      const tableContainer = document.querySelector('.tabla-container')
      expect(tableContainer).toBeInTheDocument()
    })

    it('debe tener la tabla con la clase correcta', () => {
      renderWithAuth(<Generador />)
      
      const table = document.querySelector('.tabla-generador')
      expect(table).toBeInTheDocument()
    })

    it('debe tener el encabezado de tabla', () => {
      renderWithAuth(<Generador />)
      
      const thead = document.querySelector('thead')
      expect(thead).toBeInTheDocument()
    })

    it('debe tener el cuerpo de tabla', () => {
      renderWithAuth(<Generador />)
      
      const tbody = document.querySelector('tbody')
      expect(tbody).toBeInTheDocument()
    })
  })

  describe('Accesibilidad', () => {
    it('debe tener labels asociados correctamente', () => {
      renderWithAuth(<Generador />)
      
      const label = screen.getByText('Nombre openAPI')
      const input = screen.getByPlaceholderText('buscar')
      
      expect(label).toHaveAttribute('for', 'openapi-name')
      expect(input).toHaveAttribute('id', 'openapi-name')
    })

    it('debe tener autocomplete deshabilitado', () => {
      renderWithAuth(<Generador />)
      
      const form = document.querySelector('form')
      const input = screen.getByPlaceholderText('buscar')
      
      expect(form).toHaveAttribute('autoComplete', 'off')
      expect(input).toHaveAttribute('autoComplete', 'off')
    })
  })

  describe('Estilos y clases CSS', () => {
    it('debe tener las clases CSS principales', () => {
      renderWithAuth(<Generador />)
      
      const container = document.querySelector('.generador-content')
      const inner = document.querySelector('.generador-inner')
      
      expect(container).toBeInTheDocument()
      expect(inner).toBeInTheDocument()
    })

    it('debe tener el input con la clase search-input', () => {
      renderWithAuth(<Generador />)
      
      const input = screen.getByPlaceholderText('buscar')
      expect(input).toHaveClass('search-input')
    })
  })
})
