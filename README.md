let response = await fetch(url, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'application/json',
        },
      });

      // Si el servidor devuelve 405 (p. ej. por diferencia con la barra final), reintentar
      if (response.status === 405) {
        const urlWithSlash = url.endsWith('/') ? url : `${url}/`;
        console.warn('Recibido 405. Reintentando con barra final:', urlWithSlash);
        response = await fetch(urlWithSlash, {
          method: 'POST',
          headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
          },
        });
      }
