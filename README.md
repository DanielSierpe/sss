# Variables de entorno para autenticación OAuth
# Copia este archivo como .env y configura los valores reales para tu entorno

# Endpoint para obtener el token JWT (auth_code)
VITE_JWT_ENDPOINT=https://ssm.hcloud.cl.bsch/oauth/token

# Endpoint para renovar el token (refresh_token)
VITE_REFRESH_ENDPOINT=https://ssm.dcloud.cl.bsch/oauth/token

# URI de redirección después de la autenticación
VITE_REDIRECT_URI=https://chl-dss-lowcodeportal-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/

# URL del portal de login
VITE_LOGIN_URL=https://dss-login-webtools-ui-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/

# ID del cliente OAuth
VITE_CLIENT_ID=webtools
