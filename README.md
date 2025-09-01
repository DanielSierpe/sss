/// <reference types="vitest" />
import federation from '@originjs/vite-plugin-federation';
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vitest/config';
import type { IncomingMessage, ServerResponse } from 'http';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(),
    federation({
      name: 'app',
      remotes: {
        remoteApp: '/remoteApp/assets/remoteEntry.js',
      },
      shared: ['react', 'react-dom'],
    }),
  ],
  build: {
    target: 'esnext',
    rollupOptions: {
      output: {
        manualChunks: {
          react: ['react', 'react-dom'],
        },
      },
    },
  },
  server: {
    proxy: {
      '/remoteApp': {
        target: `http://localhost:4174`,
        changeOrigin: true,
        rewrite: (path: string) => path.replace(/^\/remoteApp/, ''),
        secure: false,
      },
      '/oauth/token': {
        target: process.env.VITE_JWT_ENDPOINT || 'https://ssm.hcloud.cl.bsch',
        changeOrigin: true,
        secure: false, 
        rewrite: () => '/oauth/token', 
        configure: (proxy: any) => {
          proxy.on('error', (err: Error) => {
            console.error('Error en proxy OAuth:', err);
          });
          proxy.on('proxyReq', (proxyReq: any, req: IncomingMessage) => {
            console.log('Proxy interceptando petición OAuth:', req.method, req.url);
            console.log('URL destino:', proxyReq.path);
            console.log('Host destino:', proxyReq.getHeader('host'));
          });
          proxy.on('proxyRes', (proxyRes: ServerResponse, req: IncomingMessage) => {
            console.log('Respuesta del proxy OAuth:', proxyRes.statusCode, req.url);
          });
        },
        agent: false,
      },
      '/api/oauth/token': {
        target: process.env.VITE_REFRESH_ENDPOINT || 'https://ssm.dcloud.cl.bsch',
        changeOrigin: true,
        secure: false, 
        rewrite: () => '/oauth/token', 
        configure: (proxy: any) => {
          proxy.on('error', (err: Error) => {
            console.error('Error en proxy API:', err);
          });
          proxy.on('proxyReq', (_proxyReq: any, req: IncomingMessage) => {
            console.log('Enviando petición API:', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes: ServerResponse, req: IncomingMessage) => {
            console.log('Respuesta API:', proxyRes.statusCode, req.url);
          });
        },
        agent: false, 
      },
    },
  },
  preview: {
    proxy: {
      '/remoteApp': {
        target: `http://localhost:4174`,
        changeOrigin: true,
        rewrite: (path: string) => path.replace(/^\/remoteApp/, ''),
        secure: false,
      },
      '/oauth/token': {
        target: process.env.VITE_JWT_ENDPOINT || 'https://ssm.hcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        rewrite: () => '/oauth/token',
        configure: (proxy: any) => {
          proxy.on('error', (err: Error) => {
            console.error('Error en proxy OAuth (preview):', err);
          });
          proxy.on('proxyReq', (_proxyReq: any, req: IncomingMessage) => {
            console.log('Enviando petición OAuth (preview):', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes: ServerResponse, req: IncomingMessage) => {
            console.log('Respuesta OAuth (preview):', proxyRes.statusCode, req.url);
          });
        },
        agent: false,
      },
      '/api/oauth/token': {
        target: process.env.VITE_REFRESH_ENDPOINT || 'https://ssm.dcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        rewrite: () => '/oauth/token',
        configure: (proxy: any) => {
          proxy.on('error', (err: Error) => {
            console.error('Error en proxy API (preview):', err);
          });
          proxy.on('proxyReq', (_proxyReq: any, req: IncomingMessage) => {
            console.log('Enviando petición API (preview):', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes: ServerResponse, req: IncomingMessage) => {
            console.log('Respuesta API:', proxyRes.statusCode, req.url);
          });
        },
        agent: false,
      },
    },
  },
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      reporter: ['clover', 'lcov', 'text', 'json', 'html'],
      all: true,
      include: ['src'],
      exclude: [
        'src/main.tsx',
        'src/vite-env.d.ts',
        'src/__mocks__/**/*',
        'src/__fixtures__/**/*',
        'src/**/index.ts',
        'src/App.tsx',
        'src/global.d.ts',
        'src/test/setup.ts',
        'src/setupTests.ts',
      ],
      thresholds: {
        statements: 0,
        branches: 0,
        functions: 0,
        lines: 0,
      },
    },
    restoreMocks: true,
  }
})
