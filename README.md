Run pnpm coverage

> chl-dss-lowcodeportal@0.0.1-BETA coverage /runner/_work/chl-dss-lowcodeportal/chl-dss-lowcodeportal
> vitest run --coverage


 RUN  v2.1.9 /runner/_work/chl-dss-lowcodeportal/chl-dss-lowcodeportal
      Coverage enabled with v8

 ✓ src/App.test.tsx (6 tests) 219ms

 Test Files  1 passed (1)
      Tests  6 passed (6)
   Start at  11:44:50
   Duration  15.62s (transform 376ms, setup 238ms, collect 629ms, tests 219ms, environment 1.24s, prepare 127ms)

 % Coverage report from v8
-------------------|---------|----------|---------|---------|-------------------
File               | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s 
-------------------|---------|----------|---------|---------|-------------------
All files          |   14.27 |      100 |   12.72 |   14.27 |                   
 src               |     100 |      100 |     100 |     100 |                   
  App.test.tsx     |     100 |      100 |     100 |     100 |                   
  ...arations.d.ts |       0 |        0 |       0 |       0 |                   
 src/components    |     100 |      100 |     100 |     100 |                   
  Header.tsx       |     100 |      100 |     100 |     100 |                   
  Sidebar.tsx      |     100 |      100 |     100 |     100 |                   
 src/pages         |    8.03 |      100 |      20 |    8.03 |                   
  Dashboard.tsx    |     100 |      100 |     100 |     100 |                   
  ...noOpenAPI.tsx |   10.59 |      100 |       0 |   10.59 | 32-281            
  EditorJSLT.tsx   |   12.09 |      100 |       0 |   12.09 | 84-365            
  EditorVisual.tsx |    1.38 |      100 |       0 |    1.38 | 13-510            
  Generador.tsx    |    8.51 |      100 |       0 |    8.51 | 6-50              
 src/services      |   14.76 |      100 |    6.38 |   14.76 |                   
  ...ileService.ts |    7.36 |      100 |       0 |    7.36 | ...93-194,198-203 
  EditorService.ts |   20.85 |      100 |    6.89 |   20.85 | ...51-252,255-272 
  ...ageService.ts |      14 |      100 |      10 |      14 | ...93-107,110-115 
-------------------|---------|----------|---------|---------|-------------------
ERROR: Coverage for lines (14.27%) does not meet global threshold (65%)
ERROR: Coverage for functions (12.72%) does not meet global threshold (60%)
ERROR: Coverage for statements (14.27%) does not meet global threshold (65%)
 ELIFECYCLE  Command failed with exit code 1.
Error: Process completed with exit code 1.
