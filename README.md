# Endpoint para obtener el token JWT (auth_code)
VITE_JWT_ENDPOINT=https://ssm.dcloud.cl.bsch/oauth/token
 
# Endpoint para renovar el token (refresh_token)
VITE_REFRESH_ENDPOINT=https://ssm.dcloud.cl.bsch/oauth/token
 
# URI de redirección después de la autenticación
VITE_REDIRECT_URI=https://webtools-portal.dcloud.cl.bsch/
 
# URL del portal de login atraves de haproxy
VITE_LOGIN_URL=https://webtools.dcloud.cl.bsch/
 
# ID del cliente OAuth
VITE_CLIENT_ID=webtools
 

# URL base del executor
VITE_EXECUTOR_BASE_URL=http://platform.dcloud.cl.bsch/executor/v1

# Endpoints del executor
VITE_EXECUTOR_EXECUTE_ENDPOINT=/execute
VITE_EXECUTOR_STATUS_ENDPOINT=/status
VITE_EXECUTOR_LOGS_ENDPOINT=/logs
VITE_EXECUTOR_GENERATED_PROJECT_ENDPOINT=/generated-project
