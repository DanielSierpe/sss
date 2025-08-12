❯ src/App.test.tsx (6)
   ❯ App (6)
     ✓ renders Dashboard title
     ✓ renders Gluon title in sidebar
     ✓ renders MENÚ label in sidebar
     ✓ renders correct number of sidebar links
     × renders Santander logo image
     ✓ renders Gluon logo image

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/App.test.tsx > App > renders Santander logo image
TestingLibraryElementError: Found multiple elements with the alt text: /Logo/i

Here are the matching elements:

Ignored nodes: comments, script, style
<img
  alt="Logo"
  class="logo"
  src="/logo_santander_new.svg"
/>

Ignored nodes: comments, script, style
<img
  alt="Gluon Logo"
  class="gluon-logo"
  src="/logo.png"
/>

(If this is intentional, then use the `*AllBy*` variant of the query (like `queryAllByText`, `getAllByText`, or `findAllByText`)).

Ignored nodes: comments, script, style
<body>
  <div>
    <div
      class="app-container"
    >
      <header
        class="app-header"
      >
        <img
          alt="Logo"
          class="logo"
          src="/logo_santander_new.svg"
        />
        <span
          class="header-title"
        />
      </header>
      <div
        class="main-layout"
      >
        <aside
          class="sidebar"
        >
          <div
            class="sidebar-logo-title"
          >
            <img
              alt="Gluon Logo"
              class="gluon-logo"
              src="/logo.png"
            />
            <span
              class="gluon-title"
            >
              Gluon
            </span>
          </div>
          <div
            class="sidebar-title"
          >
            MENÚ
          </div>
          <nav>
            <ul>
              <li>
                <a
                  data-discover="true"
                  href="/diseno-openapi"
                >
                  Diseño OpenAPI
                </a>
              </li>
              <li>
                <a
                  data-discover="true"
                  href="/generador"
                >
                  Generador
                </a>
              </li>
              <li>
                <a
                  data-discover="true"
                  href="/editor-jslt"
                >
                  Editor JSLT
                </a>
              </li>
              <li>
                <a
                  data-discover="true"
                  href="/"
                >
                  Editor XSLT
                </a>
              </li>
              <li>
                <a
                  data-discover="true"
                  href="/"
                >
                  Editor HTML
                </a>
              </li>
            </ul>
          </nav>
        </aside>
        <main
          class="main-content"
        >
          <div
            class="app-cpmtainer"
          >
            <div
              class="main-layout"
            >
              <main
                class="dashboard-content"
              >
                <h2>
                  Dashboard
                </h2>
                <p />
              </main>
            </div>
          </div>
        </main>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ getElementError node_modules/@testing-library/dom/dist/query-helpers.js:20:35
 ❯ getMultipleElementsFoundError node_modules/@testing-library/dom/dist/query-helpers.js:23:10
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:55:13
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/App.test.tsx:31:34
     29|   test('renders Santander logo image', () => {
     30|     render(<App />);
     31|     const santanderLogo = screen.getByAltText(/Logo/i);
       |                                  ^
     32|     expect(santanderLogo).toBeInTheDocument();
     33|   });

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯

 Test Files  1 failed (1)
      Tests  1 failed | 5 passed (6)
   Start at  11:29:26
   Duration  1.13s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit
