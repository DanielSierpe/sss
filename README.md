suario autenticado: false                                                                                                                                                                                               
                                                                                                                                                                                                                         
 ✓ src/App.test.tsx (5)
 ✓ src/pages/__tests__/DisenoOpenAPI.test.tsx (60) 1790ms
 ✓ src/pages/__tests__/EditorJSLT.test.tsx (17) 2035ms
 ✓ src/pages/__tests__/EditorVisual.test.tsx (34) 2238ms
 ✓ src/pages/__tests__/EditorXSLT.test.tsx (18) 1750ms
 ❯ src/pages/__tests__/Generador.test.tsx (14) 405ms
   ❯ Generador (14) 404ms
     ✓ debería renderizar correctamente
     ✓ debería permitir escribir en el campo de búsqueda
     ✓ debería habilitar el botón generar cuando hay texto en el campo
     ✓ debería mostrar error cuando el componente no existe
     ✓ debería habilitar el botón generar cuando se escribe en el campo
     ✓ debería ejecutar el script y mostrar el modal al hacer clic en generar
     × debería iniciar el polling después de ejecutar el script exitosamente
     × debería mostrar el botón de descarga cuando el estado es completed
     × debería descargar el proyecto cuando se hace clic en el botón de descarga
     × debería cerrar el modal y detener el polling al hacer clic en cerrar
     × debería manejar errores en la ejecución del script
     × debería manejar errores en la descarga del proyecto
     × debería mostrar el estado correcto según el status de la aplicación
     × debería limpiar el polling al desmontar el componente
 ✓ src/hooks/__tests__/useAuth.test.ts (7)
 ✓ src/contexts/__tests__/AuthContext.test.tsx (7)
 ✓ src/services/__tests__/ApiDefinitionFileService.test.ts (28)
 ✓ src/services/__tests__/AuthService.test.ts (12)
 ✓ src/services/__tests__/EditorService.test.ts (42)
 ✓ src/services/__tests__/ExecutorService.test.ts (5)
 ✓ src/services/__tests__/HttpService.test.ts (14)
 ✓ src/services/__tests__/StorageService.test.ts (26)

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 8 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería iniciar el polling después de ejecutar el script exitosamente
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:195:30
    193|     });
    194|
    195|     const appOption = screen.getByText('app1');
       |                              ^
    196|
    197|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería mostrar el botón de descarga cuando el estado es completed
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:233:30
    231|     });
    232|
    233|     const appOption = screen.getByText('app1');
       |                              ^
    234|
    235|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería descargar el proyecto cuando se hace clic en el botón de descarga
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:271:30
    269|     });
    270|
    271|     const appOption = screen.getByText('app1');
       |                              ^
    272|
    273|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería cerrar el modal y detener el polling al hacer clic en cerrar
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:308:30
    306|     });
    307|
    308|     const appOption = screen.getByText('app1');
       |                              ^
    309|
    310|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería manejar errores en la ejecución del script
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:342:30
    340|     });
    341|
    342|     const appOption = screen.getByText('app1');
       |                              ^
    343|
    344|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[5/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería manejar errores en la descarga del proyecto
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:376:30
    374|     });
    375|
    376|     const appOption = screen.getByText('app1');
       |                              ^
    377|
    378|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[6/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería mostrar el estado correcto según el status de la aplicación
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:420:30
    418|     });
    419|
    420|     const appOption = screen.getByText('app1');
       |                              ^
    421|
    422|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[7/8]⎯

 FAIL  src/pages/__tests__/Generador.test.tsx > Generador > debería limpiar el polling al desmontar el componente
TestingLibraryElementError: Unable to find an element with the text: app1. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ src/pages/__tests__/Generador.test.tsx:451:30
    449|     });
    450|
    451|     const appOption = screen.getByText('app1');
       |                              ^
    452|
    453|     await act(async () => {

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[8/8]⎯

 Test Files  1 failed | 13 passed (14)
      Tests  8 failed | 281 passed (289)
   Start at  17:23:07
   Duration  31.07s (transform 1.41s, setup 6.95s, collect 6.73s, tests 8.89s, environment 49.26s, prepare 5.55s)
