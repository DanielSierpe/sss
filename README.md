<html xmlns:th="http://www.thymeleaf.org" lang="es"> 
	<head> 			
		<meta http-equiv='Content-Type' content='text/html; charset=UTF-8'/>
		<link rel="shortcut icon" type="image/jpg" th:href="@{/static/favicon.ico}"/>
		<style>			
			* {
				margin: 0;
				padding: 0;
				box-sizing: border-box;
			}

			body {
				background: linear-gradient(135deg, #ec0000 0%, #cc0000 100%);
				font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
				min-height: 100vh;
				display: flex;
				align-items: center;
				justify-content: center;
				position: relative;
				overflow: hidden;
			}

			body::before {
				content: '';
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				background-image: 
					radial-gradient(circle at 20% 80%, rgba(255,255,255,0.1) 0%, transparent 50%),
					radial-gradient(circle at 80% 20%, rgba(255,255,255,0.1) 0%, transparent 50%),
					radial-gradient(circle at 40% 40%, rgba(255,255,255,0.05) 0%, transparent 50%);
				animation: float 20s ease-in-out infinite;
			}

			@keyframes float {
				0%, 100% { transform: translateY(0px) rotate(0deg); }
				50% { transform: translateY(-20px) rotate(180deg); }
			}

			.page {
				width: 100%;
				height: 100%;
				padding: 0;
				margin: 0;
				display: flex;
				align-items: center;
				justify-content: center;
			}

			.white-box {
				background: rgba(255, 255, 255, 0.95);
				backdrop-filter: blur(10px);
				border-radius: 20px;
				box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
				padding: 40px;
				width: 400px;
				position: relative;
				z-index: 1;
				transform: translateY(0);
				transition: all 0.3s ease;
				border: 1px solid rgba(255, 255, 255, 0.2);
				font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
			}

			.white-box:hover {
				transform: translateY(-5px);
				box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
			}

			h1 { 		
				font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif !important;
				font-weight: 300 !important;
				font-size: 24px;
				color: #333;
				margin-bottom: 10px;
				text-align: center;
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 0.3s;
			}

			@keyframes fadeInUp {
				from {
					opacity: 0;
					transform: translateY(20px);
				}
				to {
					opacity: 1;
					transform: translateY(0);
				}
			}

			.logo-container {
				text-align: center;
				margin-bottom: 20px;
			}

			.logo-container svg {
				width: 120px;
				height: auto;
				filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.1));
				transition: all 0.3s ease;
				animation: logoFloat 3s ease-in-out infinite;
			}

			.logo-container svg:hover {
				transform: scale(1.05) rotate(2deg);
				filter: drop-shadow(0 6px 12px rgba(0, 0, 0, 0.2));
			}

			@keyframes logoFloat {
				0%, 100% { transform: translateY(0px); }
				50% { transform: translateY(-5px); }
			}

			.title {
				font-size: 28px;
				color: #333;
				margin-bottom: 8px;
				font-weight: 300;
				text-align: center;
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 0.3s;
			}

			.subtitle {
				font-size: 16px;
				color: #666;
				margin-bottom: 30px;
				text-align: center;
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 0.5s;
			}

			.inputWrapper {
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 0.5s;
			}

			.inputContainer {
				margin-bottom: 25px;
				position: relative;
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 0.7s;
			}

			.inputContainer:nth-child(2) {
				animation-delay: 0.9s;
			}

			.inputContainer:nth-child(3) {
				animation-delay: 1.1s;
			}

			.inputContainer input {
				width: 100%;
				padding: 15px 20px;
				border: 2px solid #e1e5e9;
				border-radius: 12px;
				font-size: 16px;
				background: rgba(255, 255, 255, 0.8);
				transition: all 0.3s ease;
				outline: none;
			}

			.inputContainer input:focus {
				border-color: #ec0000;
				background: white;
				box-shadow: 0 0 0 3px rgba(236, 0, 0, 0.1);
			}

			.inputContainer input::placeholder {
				color: #999;
				font-style: italic;
			}

			button {
				width: 100%;
				padding: 15px;
				background: linear-gradient(135deg, #ec0000 0%, #cc0000 100%);
				color: white;
				border: none;
				border-radius: 12px;
				font-size: 16px;
				font-weight: 600;
				cursor: pointer;
				transition: all 0.3s ease;
				position: relative;
				overflow: hidden;
				opacity: 0;
				animation: fadeInUp 0.8s ease forwards 1.3s;
				font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
			}

			button::before {
				content: '';
				position: absolute;
				top: 0;
				left: -100%;
				width: 100%;
				height: 100%;
				background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
				transition: left 0.5s;
			}

			button:hover::before {
				left: 100%;
			}

			button:hover {
				transform: translateY(-2px);
				box-shadow: 0 10px 20px rgba(236, 0, 0, 0.3);
			}

			button:active {
				transform: translateY(0);
			}

			/* Responsive */
			@media (max-width: 480px) {
				.white-box {
					width: 90%;
					padding: 30px 20px;
				}
				
				h1 svg {
					width: 100px;
				}
			}

			/* Efectos de entrada para los campos */
			.inputContainer {
				transition: all 0.3s ease;
			}

			.inputContainer:focus-within {
				transform: translateY(-1px);
			}

			/* Efecto de typing */
			.inputContainer input:not(:placeholder-shown) {
				border-color: #ec0000;
			}

			/* Mensajes de alerta */
			.alert {
				margin-bottom: 20px;
				padding: 12px 16px;
				border-radius: 10px;
				font-size: 14px;
				line-height: 1.4;
			}

			.alert.error {
				background-color: #fde8e8;
				border: 1px solid #f8b4b4;
				color: #8a1f1f;
			}

			.alert.info {
				background-color: #e8f1fd;
				border: 1px solid #b4d0f8;
				color: #1f3c8a;
			}
		</style>
		
	</head>  			 			
	<body class='login'>  
		<div class='page'>  					
			<div id='box' class='white-box'>  						
				<form method='POST' enctype='application/x-www-form-urlencoded' th:action="@{/login}">						
					<div class="logo-container">
						<svg xmlns="http://www.w3.org/2000/svg" width="106" height="36" viewBox="0 0 106 36" fill="none"><title id="gluon-logo">Gluon</title><path d="M25.2042 20.6594C25.2836 20.8494 25.3581 21.0394 25.4327 21.2317C25.4543 21.287 25.5168 21.3159 25.5721 21.2942C26.6398 20.9047 27.5968 20.3805 28.323 19.6736C29.3329 18.6733 29.3209 17.1896 28.4288 16.1605C26.1204 13.5371 21.3064 12.8758 18.0001 12.8494C17.8871 12.8494 17.7693 12.8518 17.6539 12.8542C18.6518 11.5389 19.7723 10.3318 21.0852 9.36758C24.2087 7.1698 24.442 10.2933 24.0741 12.7316C24.0669 12.7845 24.0981 12.835 24.151 12.8494C24.353 12.9071 24.555 12.9696 24.7546 13.0345C24.8147 13.0538 24.8796 13.0177 24.894 12.9576C25.0407 12.3276 25.1537 11.6856 25.2187 11.0339C25.5289 7.51846 23.1796 6.26087 20.2268 7.98255C18.1011 9.25456 16.4083 11.058 14.9896 13.0466C14.1625 13.15 13.3184 13.3015 12.4937 13.5106H12.4889C12.0753 12.3637 11.705 11.1926 11.5439 10.0336C11.4453 9.2762 11.3852 8.51876 11.6473 7.84549C12.1498 6.71774 13.7873 7.66995 14.5472 8.14606C15.2517 8.62937 15.9154 9.1704 16.5382 9.75471C16.5815 9.79559 16.6464 9.79319 16.6873 9.75231C16.846 9.59361 17.0071 9.43731 17.1706 9.28823C17.2139 9.24735 17.2163 9.18002 17.1754 9.13433C16.0356 7.89598 14.8021 6.8524 13.518 6.26809C12.3686 5.7463 10.9812 5.9579 10.3368 7.15297C9.4495 8.93475 9.79575 11.5629 10.5893 14.131C9.40381 14.6192 8.3458 15.278 7.57153 16.1581C6.67462 17.1896 6.67222 18.6757 7.67733 19.6712C8.31454 20.2915 9.12969 20.7724 10.0434 21.1452C10.1035 21.1692 10.1733 21.1355 10.1901 21.073C10.2454 20.8686 10.3055 20.657 10.3704 20.443C10.3849 20.3925 10.3632 20.3396 10.3151 20.3156C8.41072 19.3489 6.86218 17.7643 9.63705 16.3048C10.118 16.0643 10.6157 15.8623 11.1255 15.6892C11.4381 16.5188 11.7819 17.3219 12.1282 18.0625C10.9163 20.8157 10.0194 24.0619 10.7287 26.6901C11.1712 28.2026 12.5273 28.9648 14.0326 28.4695C14.9944 28.1304 15.9178 27.5173 16.7882 26.7502C16.8339 26.7093 16.8363 26.6396 16.7955 26.5939C16.644 26.4256 16.4949 26.2573 16.3458 26.0865C16.3097 26.0433 16.2448 26.036 16.1991 26.0697C14.2899 27.498 11.7747 28.532 12.0969 24.9275C12.2677 23.3694 12.7221 21.8545 13.3257 20.3901C13.3641 20.4574 13.4026 20.5248 13.4387 20.5897C15.2421 23.7397 19.6208 29.7631 23.5234 29.9988C24.882 30.0421 25.7597 28.936 25.8823 27.6856C26.2093 24.2591 24.0452 19.7313 22.3019 16.538C22.2586 16.4586 22.1817 16.4033 22.0927 16.3841C21.8883 16.3432 21.6815 16.3048 21.4748 16.2711C20.323 16.0811 19.1519 16.0042 17.9929 15.9825C17.7573 15.9825 17.5192 15.9874 17.2836 15.9946C17.2018 15.997 17.1513 16.0859 17.1922 16.1581L17.8559 17.3724C17.8751 17.406 17.9112 17.4277 17.9521 17.4277C18.0122 17.4277 18.0723 17.4277 18.1348 17.4253C19.4814 17.3531 21.4892 17.3507 21.9653 17.3844C22.023 17.3892 22.0735 17.4229 22.1023 17.4734C23.4874 20.14 24.5454 22.8764 24.7738 25.8581C24.7955 26.6877 24.8195 28.0174 24.0597 28.4598C22.5905 28.9095 20.1907 26.2284 19.2601 25.1488C17.7909 23.4054 16.5045 21.4818 15.3503 19.4884C15.0521 18.9714 14.7636 18.4472 14.4847 17.9182C14.7492 17.406 15.0257 16.9011 15.3046 16.4033C15.622 15.8695 15.949 15.3357 16.2905 14.8091C16.8628 14.7779 17.4351 14.761 18.0001 14.7538C20.8351 14.7947 23.8216 15.04 26.3632 16.3048C29.3473 17.8725 27.3347 19.5846 25.2595 20.52C25.2066 20.544 25.1826 20.6041 25.2042 20.6594Z" fill="url(#paint0_linear_1761_70915)"></path><path d="M49.104 19.32V24.68C48.6773 24.8613 48.1707 25 47.584 25.096C47.008 25.2027 46.4267 25.256 45.84 25.256C44.7093 25.256 43.7387 25.0213 42.928 24.552C42.128 24.072 41.52 23.4 41.104 22.536C40.6987 21.6613 40.496 20.632 40.496 19.448C40.496 18.2533 40.704 17.224 41.12 16.36C41.5467 15.496 42.1813 14.8293 43.024 14.36C43.8773 13.8907 44.928 13.656 46.176 13.656C47.2853 13.656 48.2453 13.8 49.056 14.088C49.0133 14.6107 48.9013 15.2133 48.72 15.896C48.3147 15.7467 47.904 15.6347 47.488 15.56C47.0827 15.4853 46.6453 15.448 46.176 15.448C45.024 15.448 44.1493 15.7947 43.552 16.488C42.9547 17.1707 42.656 18.152 42.656 19.432C42.656 20.7227 42.9387 21.72 43.504 22.424C44.0693 23.1173 44.8907 23.464 45.968 23.464C46.32 23.464 46.672 23.432 47.024 23.368V19.304L49.104 19.32ZM54.3047 14.056C54.5714 13.9813 54.8967 13.9173 55.2807 13.864C55.6754 13.8107 56.0434 13.784 56.3847 13.784V23.192H60.3528C60.3528 23.448 60.3368 23.752 60.3047 24.104C60.2728 24.4453 60.2301 24.744 60.1768 25H54.3047V14.056ZM68.7075 25.272C67.8435 25.272 67.0968 25.1173 66.4675 24.808C65.8382 24.488 65.3528 24.0347 65.0115 23.448C64.6702 22.8613 64.4995 22.1733 64.4995 21.384V14.056C64.7768 13.9707 65.1075 13.9067 65.4915 13.864C65.8755 13.8107 66.2382 13.784 66.5795 13.784V21.32C66.5795 22.0347 66.7662 22.5733 67.1395 22.936C67.5128 23.2987 68.0408 23.48 68.7235 23.48C69.3955 23.48 69.9182 23.304 70.2915 22.952C70.6755 22.5893 70.8675 22.04 70.8675 21.304V14.056C71.1342 13.9813 71.4595 13.9173 71.8435 13.864C72.2382 13.8107 72.6062 13.784 72.9475 13.784V21.384C72.9475 22.1733 72.7715 22.8613 72.4195 23.448C72.0782 24.0347 71.5875 24.488 70.9475 24.808C70.3075 25.1173 69.5608 25.272 68.7075 25.272ZM82.5571 25.272C81.4798 25.272 80.5678 25.0373 79.8211 24.568C79.0745 24.0987 78.5091 23.432 78.1251 22.568C77.7411 21.6933 77.5491 20.6587 77.5491 19.464C77.5491 18.2587 77.7411 17.224 78.1251 16.36C78.5198 15.4853 79.0905 14.8187 79.8371 14.36C80.5838 13.8907 81.4851 13.656 82.5411 13.656C83.6078 13.656 84.5145 13.8907 85.2611 14.36C86.0078 14.8293 86.5731 15.5013 86.9571 16.376C87.3411 17.24 87.5331 18.2693 87.5331 19.464C87.5331 20.68 87.3358 21.7253 86.9411 22.6C86.5571 23.464 85.9918 24.1253 85.2451 24.584C84.4985 25.0427 83.6025 25.272 82.5571 25.272ZM82.5571 23.48C84.4451 23.48 85.3891 22.1413 85.3891 19.464C85.3891 18.152 85.1491 17.1547 84.6691 16.472C84.1891 15.7893 83.4798 15.448 82.5411 15.448C81.6025 15.448 80.8931 15.7893 80.4131 16.472C79.9331 17.144 79.6931 18.1413 79.6931 19.464C79.6931 20.776 79.9331 21.7733 80.4131 22.456C80.9038 23.1387 81.6185 23.48 82.5571 23.48ZM98.7419 14.056C99.0192 13.9707 99.3392 13.9067 99.7019 13.864C100.065 13.8107 100.427 13.784 100.79 13.784V25H98.9339L94.9979 18.632C94.6779 18.088 94.4432 17.6613 94.2939 17.352C94.3152 18.1093 94.3259 19.3147 94.3259 20.968V25H92.3579V14.056C92.6565 13.9707 92.9765 13.9067 93.3179 13.864C93.6699 13.8107 94.0005 13.784 94.3099 13.784L97.9419 19.736C98.1979 20.1627 98.4965 20.7173 98.8379 21.4C98.7739 20.12 98.7419 18.76 98.7419 17.32V14.056Z" fill="#222222"></path><defs><linearGradient id="paint0_linear_1761_70915" x1="16.0981" y1="27.9212" x2="19.7627" y2="8.56926" gradientUnits="userSpaceOnUse"><stop stop-color="#D9285A"></stop><stop offset="1" stop-color="#F6A019"></stop></linearGradient></defs></svg>    
					</div>
					<h1 class="title">Bienvenido</h1>
					<p class="subtitle">Ingresa tus credenciales para continuar</p>
					<div class='alert error' th:if="${error} or ${param.error}">Usuario o contraseña incorrectos.</div>
					<div class='alert info' th:if="${logout} or ${param.logout}">Has cerrado sesión correctamente.</div>
					<div class='inputWrapper'>
						<div class='inputContainer'>
						<input type='text' name='username' id='username' required='required' placeholder="RUT Funcionarios (Ej: 122748426)"/>
						</div>
					
						<div class='inputContainer'>
							<input type='password' name='password' id='password' required='required' placeholder="Clave de acceso Intranet"/>
						</div>
						<input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>
						<button id='login_button' type='submit' name='login' value='true'>Ingresar</button>							
			        </div>
				</form>
		  </div>
		</div> 
	</body>
</html>
