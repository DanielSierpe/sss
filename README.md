⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/services/__tests__/AuthService.test.ts > AuthService > refreshToken (directo a backend) > debería esperar a ConfigService cuando clientId no está disponible
AssertionError: expected "log" to be called with arguments: [ Array(1) ]

Received:

  1st log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Endpoint refresh:",
+   "webtools",
  ]

  2nd log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Parámetros refresh:",
+   "grant_type=refresh_token&client_id=webtools&refresh_token=ref",
  ]

  3rd log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Respuesta del servidor (refresh):",
+   200,
+   "OK",
  ]

  4th log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Token renovado exitosamente",
  ]

  5th log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Token type:",
+   "Bearer",
  ]

  6th log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Fecha de expiración calculada:",
+   "2025-09-26T21:22:37.306Z",
  ]

  7th log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Refresh token guardado",
  ]

  8th log call:

  Array [
-   "AuthService: clientId no disponible, esperando carga de ConfigService...",
+   "Token guardado en localStorage exitosamente",
  ]


Number of calls: 8

 ❯ src/services/__tests__/AuthService.test.ts:314:26
    312| 
    313|       expect(result?.access_token).toBe('new-acc');
    314|       expect(consoleSpy).toHaveBeenCalledWith('AuthService: clientId no disponible, esperando carga de ConfigService...');
       |                          ^
    315|       expect(consoleSpy).toHaveBeenCalledWith('AuthService: clientId actualizado desde ConfigService:', hoistedCfg.VITE_CLIENT_ID);
    316|       consoleSpy.mockRestore();

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯

 Test Files  1 failed | 14 passed (15)
      Tests  1 failed | 316 passed (317)
   Start at  17:52:23
   Duration  31.71s (transform 1.99s, setup 7.82s, collect 7.32s, tests 7.01s, environment 50.52s, prepare 5.82s)
