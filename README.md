import { describe, it, expect, vi, beforeEach } from 'vitest'
import { EditorService } from '../EditorService'

describe('EditorService', () => {
  let editorService: EditorService

  beforeEach(() => {
    editorService = new EditorService()
  })

  describe('constructor', () => {
    it('debe inicializar con estado por defecto', () => {
      const state = editorService.getState()
      
      expect(state.currentApi).toBeNull()
      expect(state.currentView).toBe('design')
      expect(state.isDirty).toBe(false)
      expect(state.lastSaved).toBeNull()
      expect(state.errors).toEqual([])
      expect(state.warnings).toEqual([])
    })
  })

  describe('initialize', () => {
    it('debe inicializar el editor con una nueva API', () => {
      const api = { test: 'data' }
      
      editorService.initialize(api)
      
      const state = editorService.getState()
      expect(state.currentApi).toEqual(api)
      expect(state.isDirty).toBe(false)
      expect(state.lastSaved).toBeInstanceOf(Date)
      expect(state.errors).toEqual([])
      expect(state.warnings).toEqual([])
    })

    it('debe limpiar el historial al inicializar', () => {
      const api = { test: 'data' }
      
      // Ejecutar algunos comandos primero
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.executeCommand({ type: 'update', path: 'test.path', value: 'newValue' })
      
      // Verificar que hay historial
      expect(editorService.canUndo()).toBe(true)
      
      // Inicializar
      editorService.initialize(api)
      
      // Verificar que se limpió el historial
      expect(editorService.canUndo()).toBe(false)
    })
  })

  describe('getState', () => {
    it('debe retornar una copia del estado', () => {
      const state1 = editorService.getState()
      const state2 = editorService.getState()
      
      expect(state1).not.toBe(state2) // Debe ser una copia, no la misma referencia
      expect(state1).toEqual(state2)
    })
  })

  describe('setView', () => {
    it('debe cambiar la vista a design', () => {
      editorService.setView('design')
      
      expect(editorService.getCurrentView()).toBe('design')
    })

    it('debe cambiar la vista a source', () => {
      editorService.setView('source')
      
      expect(editorService.getCurrentView()).toBe('source')
    })
  })

  describe('getCurrentView', () => {
    it('debe retornar la vista actual', () => {
      expect(editorService.getCurrentView()).toBe('design')
      
      editorService.setView('source')
      expect(editorService.getCurrentView()).toBe('source')
    })
  })

  describe('getCurrentApi', () => {
    it('debe retornar la API actual', () => {
      const api = { test: 'data' }
      editorService.initialize(api)
      
      expect(editorService.getCurrentApi()).toEqual(api)
    })
  })

  describe('isDirty', () => {
    it('debe retornar false inicialmente', () => {
      expect(editorService.isDirty()).toBe(false)
    })

    it('debe retornar true después de ejecutar un comando', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      expect(editorService.isDirty()).toBe(true)
    })
  })

  describe('markAsSaved', () => {
    it('debe marcar la API como guardada', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      expect(editorService.isDirty()).toBe(true)
      
      editorService.markAsSaved()
      
      expect(editorService.isDirty()).toBe(false)
      expect(editorService.getState().lastSaved).toBeInstanceOf(Date)
    })
  })

  describe('executeCommand', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe ejecutar comando de creación', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      expect(editorService.getValueAtPath('test.path')).toBe('value')
    })

    it('debe ejecutar comando de actualización', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'oldValue' })
      editorService.executeCommand({ type: 'update', path: 'test.path', value: 'newValue' })
      
      expect(editorService.getValueAtPath('test.path')).toBe('newValue')
    })

    it('debe ejecutar comando de eliminación', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.executeCommand({ type: 'delete', path: 'test.path' })
      
      expect(editorService.getValueAtPath('test.path')).toBeUndefined()
    })

    it('debe marcar como modificado después de ejecutar comando', () => {
      expect(editorService.isDirty()).toBe(false)
      
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      expect(editorService.isDirty()).toBe(true)
    })

    it('debe agregar comando al historial', () => {
      expect(editorService.canUndo()).toBe(false)
      
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      expect(editorService.canUndo()).toBe(true)
    })

    it('debe manejar errores en la ejecución', () => {
      // Simular un error en la ejecución
      const originalSetValueAtPath = (editorService as any).setValueAtPath
      ;(editorService as any).setValueAtPath = vi.fn().mockImplementation(() => {
        throw new Error('Test error')
      })
      
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      const errors = editorService.getErrors()
      expect(errors).toContain('Failed to execute command: Test error')
      
      // Restaurar método original
      ;(editorService as any).setValueAtPath = originalSetValueAtPath
    })
  })

  describe('getValueAtPath', () => {
    beforeEach(() => {
      editorService.initialize({
        level1: {
          level2: {
            level3: 'value'
          }
        }
      })
    })

    it('debe obtener valor en ruta simple', () => {
      const value = editorService.getValueAtPath('level1')
      
      expect(value).toEqual({
        level2: {
          level3: 'value'
        }
      })
    })

    it('debe obtener valor en ruta anidada', () => {
      const value = editorService.getValueAtPath('level1.level2.level3')
      
      expect(value).toBe('value')
    })

    it('debe retornar undefined para ruta inexistente', () => {
      const value = editorService.getValueAtPath('nonexistent.path')
      
      expect(value).toBeUndefined()
    })

    it('debe retornar undefined para ruta parcialmente inexistente', () => {
      const value = editorService.getValueAtPath('level1.nonexistent.path')
      
      expect(value).toBeUndefined()
    })
  })

  describe('undo', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe deshacer el último comando', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      expect(editorService.getValueAtPath('test.path')).toBe('value')
      
      const result = editorService.undo()
      
      expect(result).toBe(true)
      expect(editorService.getValueAtPath('test.path')).toBeUndefined()
    })

    it('debe retornar false cuando no hay comandos para deshacer', () => {
      const result = editorService.undo()
      
      expect(result).toBe(false)
    })

    it('debe marcar como modificado después de deshacer', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.markAsSaved()
      expect(editorService.isDirty()).toBe(false)
      
      editorService.undo()
      
      expect(editorService.isDirty()).toBe(true)
    })
  })

  describe('redo', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe rehacer el siguiente comando', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.undo()
      expect(editorService.getValueAtPath('test.path')).toBeUndefined()
      
      const result = editorService.redo()
      
      expect(result).toBe(true)
      expect(editorService.getValueAtPath('test.path')).toBe('value')
    })

    it('debe retornar false cuando no hay comandos para rehacer', () => {
      const result = editorService.redo()
      
      expect(result).toBe(false)
    })

    it('debe marcar como modificado después de rehacer', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.undo()
      editorService.markAsSaved()
      expect(editorService.isDirty()).toBe(false)
      
      editorService.redo()
      
      expect(editorService.isDirty()).toBe(true)
    })
  })

  describe('clearHistory', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe limpiar el historial de comandos', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      expect(editorService.canUndo()).toBe(true)
      
      editorService.clearHistory()
      
      expect(editorService.canUndo()).toBe(false)
      expect(editorService.canRedo()).toBe(false)
    })
  })

  describe('canUndo', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe retornar false inicialmente', () => {
      expect(editorService.canUndo()).toBe(false)
    })

    it('debe retornar true después de ejecutar un comando', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      
      expect(editorService.canUndo()).toBe(true)
    })

    it('debe retornar false después de deshacer todos los comandos', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.undo()
      
      expect(editorService.canUndo()).toBe(false)
    })
  })

  describe('canRedo', () => {
    beforeEach(() => {
      editorService.initialize({})
    })

    it('debe retornar false inicialmente', () => {
      expect(editorService.canRedo()).toBe(false)
    })

    it('debe retornar true después de deshacer un comando', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.undo()
      
      expect(editorService.canRedo()).toBe(true)
    })

    it('debe retornar false después de rehacer todos los comandos', () => {
      editorService.executeCommand({ type: 'create', path: 'test.path', value: 'value' })
      editorService.undo()
      editorService.redo()
      
      expect(editorService.canRedo()).toBe(false)
    })
  })

  describe('addError', () => {
    it('debe agregar un error', () => {
      editorService.addError('Test error')
      
      const errors = editorService.getErrors()
      expect(errors).toContain('Test error')
    })
  })

  describe('addWarning', () => {
    it('debe agregar una advertencia', () => {
      editorService.addWarning('Test warning')
      
      const warnings = editorService.getWarnings()
      expect(warnings).toContain('Test warning')
    })
  })

  describe('clearMessages', () => {
    it('debe limpiar todos los errores y advertencias', () => {
      editorService.addError('Test error')
      editorService.addWarning('Test warning')
      
      editorService.clearMessages()
      
      expect(editorService.getErrors()).toEqual([])
      expect(editorService.getWarnings()).toEqual([])
    })
  })

  describe('getErrors', () => {
    it('debe retornar una copia de los errores', () => {
      editorService.addError('Test error')
      
      const errors1 = editorService.getErrors()
      const errors2 = editorService.getErrors()
      
      expect(errors1).not.toBe(errors2) // Debe ser una copia
      expect(errors1).toEqual(errors2)
    })
  })

  describe('getWarnings', () => {
    it('debe retornar una copia de las advertencias', () => {
      editorService.addWarning('Test warning')
      
      const warnings1 = editorService.getWarnings()
      const warnings2 = editorService.getWarnings()
      
      expect(warnings1).not.toBe(warnings2) // Debe ser una copia
      expect(warnings1).toEqual(warnings2)
    })
  })

  describe('exportAsJson', () => {
    it('debe exportar la API como JSON', () => {
      const api = { test: 'data', number: 123 }
      editorService.initialize(api)
      
      const result = editorService.exportAsJson()
      
      expect(result).toBe('{\n  "test": "data",\n  "number": 123\n}')
    })
  })

  describe('exportAsYaml', () => {
    it('debe exportar la API como YAML', () => {
      const api = { test: 'data', number: 123 }
      editorService.initialize(api)
      
      const result = editorService.exportAsYaml()
      
      // Verificar que el resultado contiene el contenido esperado
      expect(result).toContain('test:')
      expect(result).toContain('data')
      expect(result).toContain('number:')
      expect(result).toContain('123')
    })
  })

  describe('límite del historial', () => {
    beforeEach(() => {
      editorService = new EditorService()
      editorService.initialize({})
    })

    it('debe limitar el tamaño del historial', () => {
      // Ejecutar más comandos que el límite (50)
      for (let i = 0; i < 55; i++) {
        editorService.executeCommand({ 
          type: 'create', 
          path: `test.path.${i}`, 
          value: `value${i}` 
        })
      }
      
      // Verificar que solo se pueden deshacer hasta el límite
      let undoCount = 0
      while (editorService.canUndo()) {
        editorService.undo()
        undoCount++
      }
      
      expect(undoCount).toBeLessThanOrEqual(50)
    })
  })
})
