❯ src/services/__tests__/ExecutorService.test.ts (20)
   ❯ ExecutorService (20)
     ❯ executeScript (6)
       ✓ debería manejar errores de autenticación
       ✓ debería manejar errores de red
       × debería ejecutar script exitosamente
       × debería reintentar con barra final en caso de error 405
       ✓ debería manejar errores HTTP del servidor
       ✓ debería manejar errores desconocidos
     ❯ getAppStatus (6)
       ✓ debería retornar null cuando no hay token
       ✓ debería retornar null para aplicaciones no encontradas
       ✓ debería manejar errores HTTP del servidor
       ✓ debería manejar errores de red
       × debería obtener el estado exitosamente
       ✓ debería incluir logs en la respuesta del status
     ❯ downloadGeneratedProject (8)
       ✓ debería manejar errores de autenticación
       ✓ debería manejar errores HTTP del servidor
       ✓ debería manejar errores cuando no hay base64 en la respuesta
       × debería manejar base64 vacío o inválido
       × debería descargar el proyecto exitosamente
       ✓ debería manejar errores en el procesamiento de base64
       ✓ debería manejar errores de red
       ✓ debería manejar errores desconocidos
 ✓ src/services/__tests__/HttpService.test.ts (14)
 ✓ src/services/__tests__/StorageService.test.ts (26)

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 5 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/services/__tests__/ExecutorService.test.ts > ExecutorService > executeScript > debería ejecutar script exitosamente
AssertionError: expected "spy" to be called with arguments: [ …(2) ]

Received:

  1st spy call:

  Array [
-   "/executor/v1/execute?app-name=test-app",
-   ObjectContaining {
-     "headers": ObjectContaining {
+   "https://platform.dcloud.cl.bsch/executor/v1/execute?app-name=test-app",
+   Object {
+     "headers": Object {
        "Authorization": "Bearer mock-token",
+       "Content-Type": "application/json",
      },
      "method": "POST",
    },
  ]


Number of calls: 1

 ❯ src/services/__tests__/ExecutorService.test.ts:101:25
     99|       expect(result.success).toBe(true);
    100|       expect(result.message).toBe('Componente test-app iniciado correctamente');
    101|       expect(mockFetch).toHaveBeenCalledWith(
       |                         ^
    102|         '/executor/v1/execute?app-name=test-app',
    103|         expect.objectContaining({

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/5]⎯

 FAIL  src/services/__tests__/ExecutorService.test.ts > ExecutorService > executeScript > debería reintentar con barra final en caso de error 405
AssertionError: expected 2nd "spy" call to have been called with [ …(2) ]

- Expected
+ Received

  Array [
-   "/executor/v1/execute?app-name=test-app/",
+   "https://platform.dcloud.cl.bsch/executor/v1/execute?app-name=test-app/",
    ObjectContaining {
      "headers": ObjectContaining {
        "Authorization": "Bearer mock-token",
      },
      "method": "POST",
    },
  ]

 ❯ src/services/__tests__/ExecutorService.test.ts:133:25
    131|       expect(result.success).toBe(true);
    132|       expect(mockFetch).toHaveBeenCalledTimes(2);
    133|       expect(mockFetch).toHaveBeenNthCalledWith(2,
       |                         ^
    134|         '/executor/v1/execute?app-name=test-app/',
    135|         expect.objectContaining({

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/5]⎯

 FAIL  src/services/__tests__/ExecutorService.test.ts > ExecutorService > getAppStatus > debería obtener el estado exitosamente
AssertionError: expected "spy" to be called with arguments: [ Array(2) ]

Received:

  1st spy call:

  Array [
-   "/executor/v1/status/test-app",
-   ObjectContaining {
-     "headers": ObjectContaining {
+   "https://platform.dcloud.cl.bsch/executor/v1/status/test-app",
+   Object {
+     "headers": Object {
        "Authorization": "Bearer mock-token",
+       "Content-Type": "application/json",
      },
      "method": "GET",
    },
  ]


Number of calls: 1

 ❯ src/services/__tests__/ExecutorService.test.ts:249:25
    247|
    248|       expect(result).toEqual(mockStatus);
    249|       expect(mockFetch).toHaveBeenCalledWith(
       |                         ^
    250|         '/executor/v1/status/test-app',
    251|         expect.objectContaining({

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/5]⎯

 FAIL  src/services/__tests__/ExecutorService.test.ts > ExecutorService > downloadGeneratedProject > debería manejar base64 vacío o inválido
AssertionError: expected 'No se encontró el contenido del proye…' to be 'El contenido base64 está vacío o es i…' // Object.is equality

Expected: "El contenido base64 está vacío o es inválido"
Received: "No se encontró el contenido del proyecto en la respuesta"

 ❯ src/services/__tests__/ExecutorService.test.ts:356:30
    354|
    355|       expect(result.success).toBe(false);
    356|       expect(result.message).toBe('El contenido base64 está vacío o es inválido');
       |                              ^
    357|     });
    358|

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/5]⎯

 FAIL  src/services/__tests__/ExecutorService.test.ts > ExecutorService > downloadGeneratedProject > debería descargar el proyecto exitosamente
AssertionError: expected false to be true // Object.is equality

- Expected
+ Received

- true
+ false

 ❯ src/services/__tests__/ExecutorService.test.ts:384:30
    382|       const result = await ExecutorService.downloadGeneratedProject('test-app');
    383|
    384|       expect(result.success).toBe(true);
       |                              ^
    385|       expect(result.message).toBe('Proyecto test-app descargado exitosamente como test-app-generated-project.tar.gz');
    386|

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[5/5]⎯

 Test Files  1 failed | 13 passed (14)
      Tests  5 failed | 299 passed (304)
   Start at  18:03:35
   Duration  33.18s (transform 2.46s, setup 6.74s, collect 7.70s, tests 8.05s, environment 49.56s, prepare 6.64s)
