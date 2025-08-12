
import { render, screen } from '@testing-library/react';
import App from './App';

describe('App', () => {
  test('renders Dashboard title', () => {
    render(<App />);
    const dashboardTitle = screen.getByText(/Dashboard/i);
    expect(dashboardTitle).toBeInTheDocument();
  });

  test('renders Gluon title in sidebar', () => {
    render(<App />);
    const gluonTitle = screen.getByText(/Gluon/i);
    expect(gluonTitle).toBeInTheDocument();
  });

  test('renders MENÚ label in sidebar', () => {
    render(<App />);
    const menuLabel = screen.getByText(/MENÚ/i);
    expect(menuLabel).toBeInTheDocument();
  });

  test('renders correct number of sidebar links', () => {
    render(<App />);
    const sidebarLinks = screen.getAllByRole('link');
    expect(sidebarLinks.length).toBe(5); // según el HTML actual
  });

  test('renders Santander logo image', () => {
    render(<App />);
    // Busca el alt exacto para evitar conflicto con "Gluon Logo"
    const santanderLogo = screen.getByAltText('Logo');
    expect(santanderLogo).toBeInTheDocument();
  });

  test('renders Gluon logo image', () => {
    render(<App />);
    const gluonLogo = screen.getByAltText(/Gluon Logo/i);
    expect(gluonLogo).toBeInTheDocument();
  });
});
