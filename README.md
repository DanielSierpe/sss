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
