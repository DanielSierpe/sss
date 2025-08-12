import React from 'react';
import './Header.css';


const Header: React.FC = () => {
    return (
        <header className="app-header">
        <img src= "/logo_santander_new.svg" alt="Logo" className="logo" />
        <span className="header-title"></span>
        </header>

    );
};

export default Header;




import React from 'react';
import { Link } from 'react-router-dom';
import './Sidebar.css';

const Sidebar: React.FC = () => {
  return (
    <aside className="sidebar">
      <div className="sidebar-logo-title">     
            <img src="/logo.png" alt="Gluon Logo" className="gluon-logo" />
        <span className="gluon-title">Gluon</span>
      </div>
      <div className="sidebar-title">MENÚ</div>
      <nav>
        <ul>
          <li><Link to="/diseno-openapi">Diseño OpenAPI</Link></li>
          <li><Link to="/generador">Generador</Link></li>
          <li><Link to="/editor-jslt">Editor JSLT</Link></li>
          <li><Link to="#">Editor XSLT</Link></li>
          <li><Link to="#">Editor HTML</Link></li>
        </ul>
      </nav>
    </aside>
  );
};




 ❯ src/App.test.tsx (2)
   ❯ App (2)
     × renders correct text
     × renders correct number of LogoLink components

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 2 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/App.test.tsx > App > renders correct text
TestingLibraryElementError: Unable to find an element with the text: /Click on the Vite and React logos to learn more/i. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.

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
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:76:38
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:52:17
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/App.test.tsx:7:32
      5|   test('renders correct text', () => {
      6|     render(<App />);
      7|     const textElement = screen.getByText(/Click on the Vite and React logos to learn more/i);
       |                                ^
      8|     expect(textElement).toBeInTheDocument();
      9|   });

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/2]⎯

 FAIL  src/App.test.tsx > App > renders correct number of LogoLink components
AssertionError: expected 5 to be 2 // Object.is equality

- Expected
+ Received

- 2
+ 5

 ❯ src/App.test.tsx:14:30
     12|     render(<App />);
     13|     const logoLinks = screen.getAllByRole('link');
     14|     expect(logoLinks.length).toBe(2);
       |                              ^
     15|   });
     16| });

export default Sidebar; 



