/// <reference types="vitest" />
import federation from '@originjs/vite-plugin-federation';
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vitest/config';

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
        rewrite: (path) => path.replace(/^\/remoteApp/, ''),
        secure: false,
      },
      '/oauth': {
        target: process.env.VITE_JWT_ENDPOINT || 'https://ssm.hcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        configure: (proxy) => {
          proxy.on('error', (err) => {
            console.log('proxy error', err);
          });
          proxy.on('proxyReq', (_, req) => {
            console.log('Sending Request to the Target:', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes, req) => {
            console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
          });
        },
      },
      '/api': {
        target: process.env.VITE_REFRESH_ENDPOINT || 'https://ssm.dcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        configure: (proxy) => {
          proxy.on('error', (err) => {
            console.log('proxy error', err);
          });
          proxy.on('proxyReq', (_, req) => {
            console.log('Sending Request to the Target:', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes, req) => {
            console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
          });
        },
      },
    }
  },
  preview: {
    proxy: {
      '/remoteApp': {
        target: `http://localhost:4174`,
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/remoteApp/, ''),
        secure: false,
      },
      '/oauth': {
        target: process.env.VITE_JWT_ENDPOINT || 'https://ssm.hcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        configure: (proxy) => {
          proxy.on('error', (err) => {
            console.log('proxy error', err);
          });
          proxy.on('proxyReq', (_, req) => {
            console.log('Sending Request to the Target:', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes, req) => {
            console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
          });
        },
      },
      '/api': {
        target: process.env.VITE_REFRESH_ENDPOINT || 'https://ssm.dcloud.cl.bsch',
        changeOrigin: true,
        secure: false,
        configure: (proxy) => {
          proxy.on('error', (err) => {
            console.log('proxy error', err);
          });
          proxy.on('proxyReq', (_, req) => {
            console.log('Sending Request to the Target:', req.method, req.url);
          });
          proxy.on('proxyRes', (proxyRes, req) => {
            console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
          });
        },
      },
    }
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
