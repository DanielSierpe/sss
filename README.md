 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería mostrar logs cuando están disponibles en el status
TestingLibraryElementError: Unable to find an element with the text: Ver Logs. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

Ignored nodes: comments, script, style
<body>
  <div>
    <div
      class="generador-content"
    >
      <div
        class="generador-inner"
      >
        <h2>
          Validación de Estructura
        </h2>
        <div
          class="auth-info"
          style="background: rgb(248, 249, 250); padding: 16px; border-radius: 8px; margin-bottom: 24px; border: 1px solid rgb(233, 236, 239);"
        >
          <h3
            style="margin: 0px 0px 12px 0px; color: rgb(73, 80, 87);"
          >
            Estado de Autenticación
          </h3>
          <div
            style="display: flex; flex-direction: column; gap: 8px;"
          >
            <div>
              <strong>
                Autenticado:
              </strong>

              Sí
            </div>
            <div>
              <strong>
                Cargando:
              </strong>

              No
            </div>
            <div>
              <strong>
                Token:
              </strong>

              mock-jwt-token...
            </div>
          </div>
        </div>
        <form
          autocomplete="off"
          class="generador-form"
        >
          <div
            class="form-group search-container"
          >
            <label
              for="openapi-name"
            >
              Nombre openAPI
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Buscar aplicación..."
                type="text"
                value="app1"
              />
            </div>
            <div
              class="selected-app"
            >
              <span
                class="selected-label"
              >
                Aplicación seleccionada:
              </span>
              <span
                class="selected-value"
              >
                app1
              </span>
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn validar"
              type="button"
            >
              Validar
            </button>
            <button
              class="btn generar enabled"
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
        <div
          class="tabla-container"
        >
          <table
            class="tabla-generador"
          >
            <thead>
              <tr>
                <th>
                  Code
                </th>
                <th>
                  path
                </th>
                <th>
                  Message
                </th>
                <th>
                  Severity
                </th>
                <th>
                  range
                </th>
              </tr>
            </thead>
            <tbody />
          </table>
        </div>
      </div>
      <div
        class="modal-overlay"
      >
        <div
          class="log-modal"
        >
          <div
            class="modal-header"
          >
            <h3>
              Generación de Componente:
              app1
            </h3>
            <div
              class="header-actions"
            >
              <button
                class="close-button"
              >
                ×
              </button>
            </div>
          </div>
          <div
            class="modal-content"
          >
            <div
              class="execution-status"
            >
              <div
                class="status-message"
              >
                Componente en ejecución...
              </div>
              <div
                class="app-status"
              >
                <div
                  class="status-item"
                >
                  <span
                    class="status-label"
                  >
                    Estado:
                  </span>
                  <span
                    class="status-value status-running"
                  >
                    running
                  </span>
                </div>
                <div
                  class="status-item"
                >
                  <span
                    class="status-label"
                  >
                    Versión:
                  </span>
                  <span
                    class="status-value"
                  >
                    1.0.0
                  </span>
                </div>
                <div
                  class="status-item"
                >
                  <span
                    class="status-label"
                  >
                    Inicio:
                  </span>
                  <span
                    class="status-value"
                  >
                    31/12/2023, 21:00:00
                  </span>
                </div>
              </div>
            </div>
            <div
              class="loading-indicator"
            >
              <div
                class="spinner"
              />
              <span>
                Monitoreando estado...
              </span>
            </div>
          </div>
          <div
            class="modal-footer"
          >
            <button
              class="btn btn-secondary"
            >
              Cerrar
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:260:37
    258|     });
    259|
    260|     const toggleLogsButton = screen.getByText('Ver Logs');
       |                                     ^
    261|
    262|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯

 Test Files  1 failed | 13 passed (14)
      Tests  1 failed | 294 passed (295)
   Start at  16:11:51
   Duration  29.94s (transform 2.48s, setup 7.02s, collect 8.23s, tests 9.74s, environment 44.61s, prepare 5.58s)
