import { describe, it, expect, vi, beforeEach, afterEach } from 'vitest'
import { StorageService } from '../StorageService'
import type { ApiDefinition, RecoveryApiDefinition } from '../StorageService'

// Mock de localStorage
const localStorageMock = {
  getItem: vi.fn(),
  setItem: vi.fn(),
  removeItem: vi.fn(),
  clear: vi.fn(),
}

Object.defineProperty(window, 'localStorage', {
  value: localStorageMock,
  writable: true
})

describe('StorageService', () => {
  let storageService: StorageService

  beforeEach(() => {
    storageService = new StorageService()
    vi.clearAllMocks()
  })

  afterEach(() => {
    vi.clearAllMocks()
  })

  describe('exists', () => {
    it('debe retornar true cuando existe una API guardada', () => {
      localStorageMock.getItem.mockReturnValue('{"spec": {"test": "data"}}')
      
      const result = storageService.exists()
      
      expect(result).toBe(true)
      expect(localStorageMock.getItem).toHaveBeenCalledWith('apicurito-api-definition')
    })

    it('debe retornar false cuando no existe una API guardada', () => {
      localStorageMock.getItem.mockReturnValue(null)
      
      const result = storageService.exists()
      
      expect(result).toBe(false)
    })

    it('debe retornar false cuando hay un error', () => {
      localStorageMock.getItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      const result = storageService.exists()
      
      expect(result).toBe(false)
    })
  })

  describe('save', () => {
    it('debe guardar una API correctamente', () => {
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' },
        name: 'Test API'
      }
      
      storageService.save(apiDefinition)
      
      expect(localStorageMock.setItem).toHaveBeenCalledWith(
        'apicurito-api-definition',
        expect.stringContaining('"test":"data"')
      )
    })

    it('debe agregar modifiedOn al guardar', () => {
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' }
      }
      
      storageService.save(apiDefinition)
      
      const savedData = JSON.parse(localStorageMock.setItem.mock.calls[0][1])
      expect(savedData.modifiedOn).toBeDefined()
    })

    it('debe lanzar error cuando falla el guardado', () => {
      localStorageMock.setItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' }
      }
      
      expect(() => storageService.save(apiDefinition)).toThrow('Failed to save API definition')
    })
  })

  describe('load', () => {
    it('debe cargar una API correctamente', () => {
      const mockData = {
        spec: { test: 'data' },
        name: 'Test API',
        modifiedOn: '2023-01-01T00:00:00.000Z'
      }
      localStorageMock.getItem.mockReturnValue(JSON.stringify(mockData))
      
      const result = storageService.load()
      
      expect(result).toEqual({
        spec: { test: 'data' },
        name: 'Test API',
        modifiedOn: expect.any(Date)
      })
    })

    it('debe retornar null cuando no hay datos', () => {
      localStorageMock.getItem.mockReturnValue(null)
      
      const result = storageService.load()
      
      expect(result).toBeNull()
    })

    it('debe retornar null cuando hay error de parsing', () => {
      localStorageMock.getItem.mockReturnValue('invalid json')
      
      const result = storageService.load()
      
      expect(result).toBeNull()
    })

    it('debe manejar datos sin modifiedOn', () => {
      const mockData = {
        spec: { test: 'data' }
      }
      localStorageMock.getItem.mockReturnValue(JSON.stringify(mockData))
      
      const result = storageService.load()
      
      expect(result?.modifiedOn).toBeInstanceOf(Date)
    })
  })

  describe('recover', () => {
    it('debe recuperar una API correctamente', () => {
      const mockData = {
        spec: { test: 'data' },
        name: 'Test API'
      }
      localStorageMock.getItem.mockReturnValue(JSON.stringify(mockData))
      
      const result = storageService.recover()
      
      expect(result).toEqual({
        spec: { test: 'data' },
        name: 'Test API',
        modifiedOn: expect.any(Date)
      })
    })

    it('debe lanzar error cuando no hay API para recuperar', () => {
      localStorageMock.getItem.mockReturnValue(null)
      
      expect(() => storageService.recover()).toThrow('No API definition found to recover')
    })
  })

  describe('clear', () => {
    it('debe limpiar la API guardada', () => {
      storageService.clear()
      
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('apicurito-api-definition')
    })

    it('debe manejar errores al limpiar', () => {
      localStorageMock.removeItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      // No debe lanzar error
      expect(() => storageService.clear()).not.toThrow()
    })
  })

  describe('saveForRecovery', () => {
    it('debe guardar una copia de recuperación', () => {
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' },
        name: 'Test API'
      }
      
      storageService.saveForRecovery(apiDefinition)
      
      expect(localStorageMock.setItem).toHaveBeenCalledWith(
        'apicurito-recovery',
        expect.stringContaining('"test":"data"')
      )
    })

    it('debe agregar savedForRecovery al guardar', () => {
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' }
      }
      
      storageService.saveForRecovery(apiDefinition)
      
      const savedData = JSON.parse(localStorageMock.setItem.mock.calls[0][1])
      expect(savedData.savedForRecovery).toBeDefined()
    })

    it('debe manejar errores al guardar recuperación', () => {
      localStorageMock.setItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      const apiDefinition: ApiDefinition = {
        spec: { test: 'data' }
      }
      
      // No debe lanzar error
      expect(() => storageService.saveForRecovery(apiDefinition)).not.toThrow()
    })
  })

  describe('hasRecoverableApi', () => {
    it('debe retornar true cuando existe una API de recuperación', () => {
      localStorageMock.getItem.mockReturnValue('{"spec": {"test": "data"}}')
      
      const result = storageService.hasRecoverableApi()
      
      expect(result).toBe(true)
      expect(localStorageMock.getItem).toHaveBeenCalledWith('apicurito-recovery')
    })

    it('debe retornar false cuando no existe una API de recuperación', () => {
      localStorageMock.getItem.mockReturnValue(null)
      
      const result = storageService.hasRecoverableApi()
      
      expect(result).toBe(false)
    })

    it('debe retornar false cuando hay un error', () => {
      localStorageMock.getItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      const result = storageService.hasRecoverableApi()
      
      expect(result).toBe(false)
    })
  })

  describe('loadRecovery', () => {
    it('debe cargar una API de recuperación correctamente', () => {
      const mockData: RecoveryApiDefinition = {
        spec: { test: 'data' },
        name: 'Test API',
        savedForRecovery: new Date('2023-01-01T00:00:00.000Z')
      }
      localStorageMock.getItem.mockReturnValue(JSON.stringify(mockData))
      
      const result = storageService.loadRecovery()
      
      expect(result).toEqual({
        spec: { test: 'data' },
        name: 'Test API',
        savedForRecovery: expect.any(Date)
      })
    })

    it('debe retornar null cuando no hay datos de recuperación', () => {
      localStorageMock.getItem.mockReturnValue(null)
      
      const result = storageService.loadRecovery()
      
      expect(result).toBeNull()
    })

    it('debe retornar null cuando hay error de parsing', () => {
      localStorageMock.getItem.mockReturnValue('invalid json')
      
      const result = storageService.loadRecovery()
      
      expect(result).toBeNull()
    })

    it('debe manejar datos sin savedForRecovery', () => {
      const mockData: ApiDefinition = {
        spec: { test: 'data' }
      }
      localStorageMock.getItem.mockReturnValue(JSON.stringify(mockData))
      
      const result = storageService.loadRecovery()
      
      expect(result?.savedForRecovery).toBeInstanceOf(Date)
    })
  })

  describe('clearRecovery', () => {
    it('debe limpiar la API de recuperación', () => {
      storageService.clearRecovery()
      
      expect(localStorageMock.removeItem).toHaveBeenCalledWith('apicurito-recovery')
    })

    it('debe manejar errores al limpiar recuperación', () => {
      localStorageMock.removeItem.mockImplementation(() => {
        throw new Error('Storage error')
      })
      
      // No debe lanzar error
      expect(() => storageService.clearRecovery()).not.toThrow()
    })
  })
})
