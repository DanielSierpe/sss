
          const isValidStatus = status.status && 
                               status.startTime && 
                               status.startTime !== 'Invalid Date' &&
                               status.startTime !== 'Invalid date' &&
                               status.startTime !== 'null' &&
                               status.startTime !== '';
          
          if (!isValidStatus) {
            console.log('Status inválido detectado:', status);
            if (pollingAttempts >= maxAttempts) {
              setExecutionMessage('Error: componente no existe o no se puede encontrar');
              setIsExecuting(false);
              clearInterval(interval);
              setPollingInterval(null);
              console.log('Polling detenido por status inválido');
              return;
            }
            return; 
          }
