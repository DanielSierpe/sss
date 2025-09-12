
 ✓ src/App.test.tsx (5) 389ms
 ✓ src/contexts/__tests__/AuthContext.test.tsx (7)
 ✓ src/hooks/__tests__/useAuth.test.ts (7)
 ✓ src/pages/__tests__/DisenoOpenAPI.test.tsx (60) 1802ms
 ✓ src/pages/__tests__/EditorJSLT.test.tsx (17) 1543ms
 ✓ src/pages/__tests__/EditorVisual.test.tsx (34) 2483ms
 ✓ src/pages/__tests__/EditorXSLT.test.tsx (18) 1423ms
 ❯ src/pages/__tests__/Generador.test.tsx (14) 770ms
   ❯ Generador (14) 768ms
     ✓ debería renderizar correctamente
     ✓ debería permitir escribir en el campo de búsqueda
     ✓ debería habilitar el botón generar cuando hay texto en el campo
     ✓ debería mostrar error cuando el componente no existe
     ✓ debería habilitar el botón generar cuando se escribe en el campo
     ✓ debería ejecutar el script y mostrar el modal al hacer clic en generar
     × debería iniciar el polling después de ejecutar el script exitosamente
     × debería mostrar el botón de descarga cuando el estado es completed
     × debería descargar el proyecto cuando se hace clic en el botón de descarga
     × debería cerrar el modal y detener el polling al hacer clic en cerrar 354ms
     × debería manejar errores en la ejecución del script
     × debería manejar errores en la descarga del proyecto
     × debería mostrar el estado correcto según el status de la aplicación
     × debería limpiar el polling al desmontar el componente
 ✓ src/services/__tests__/ApiDefinitionFileService.test.ts (28)
 ✓ src/services/__tests__/AuthService.test.ts (12)
 ✓ src/services/__tests__/EditorService.test.ts (42)
 ✓ src/services/__tests__/ExecutorService.test.ts (5)
 ✓ src/services/__tests__/HttpService.test.ts (14)
 ✓ src/services/__tests__/StorageService.test.ts (26)

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 8 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería iniciar el polling después de ejecutar el script exitosamente
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:189:32
    187|     });
    188|
    189|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    190|
    191|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería mostrar el botón de descarga cuando el estado es completed
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:227:32
    225|     });
    226|
    227|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    228|
    229|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería descargar el proyecto cuando se hace clic en el botón de descarga
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:265:32
    263|     });
    264|
    265|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    266|
    267|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería cerrar el modal y detener el polling al hacer clic en cerrar
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:302:32
    300|     });
    301|
    302|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    303|
    304|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería manejar errores en la ejecución del script
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:336:32
    334|     });
    335|
    336|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    337|
    338|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[5/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería manejar errores en la descarga del proyecto
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:370:32
    368|     });
    369|
    370|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    371|
    372|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[6/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería mostrar el estado correcto según el status de la aplicación
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:414:32
    412|     });
    413|
    414|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    415|
    416|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[7/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería limpiar el polling al desmontar el componente
TestingLibraryElementError: Unable to find an element with the placeholder text of: Buscar aplicación...

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
          Generación de Componentes
        </h2>
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
              Nombre del componente
            </label>
            <div
              class="search-wrapper"
            >
              <input
                autocomplete="off"
                class="search-input"
                id="openapi-name"
                name="openapi-name"
                placeholder="Ingresa el nombre del componente..."
                type="text"
                value=""
              />
            </div>
          </div>
          <div
            class="button-group"
          >
            <button
              class="btn generar disabled"
              disabled=""
              type="button"
            >
              Generar componente
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/pages/__tests__/Generador.test.tsx:445:32
    443|     });
    444|
    445|     const searchInput = screen.getByPlaceholderText('Buscar aplicación...');
       |                                ^
    446|
    447|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[8/8]⎯

 Test Files  1 failed | 13 passed (14)
      Tests  8 failed | 281 passed (289)
   Start at  17:18:30
   Duration  35.66s (transform 2.37s, setup 7.62s, collect 9.31s, tests 8.85s, environment 52.35s, prepare 7.90s)
