Fase 2 :Obtener acceso API a Gmail (OAuth2)

    5*Crear proyecto en GoogleCloud Console

        Entra a console.cloud.google.com

            Paso	Acción
            1	Clic en "Seleccionar proyecto" → "Nuevo proyecto"
            2	Nombre: n8n-gmail-monitor → Crear
            3	Menú izq → "APIs y servicios" → "Biblioteca"
            4	Buscar "Gmail API" → Habilitar
    
    6*Configurar pantalla de consentimiento OAuth

        Paso	Acción
        1	"APIs y servicios" → "Pantalla de consentimiento de OAuth"
        2	Tipo de usuario: Externo → Crear
        3	Nombre de app: n8n Monitor
        4	Email soporte: tu@email.com
        5	Sección "Permisos" → Agregar: gmail.readonly
        6	Sección "Usuarios de prueba" → Agregar tu email
        7	Guardar y continuar


    7*Crear credenciales OAuth2

        Paso	Acción
        1	"Credenciales" → "Crear credenciales" → "ID cliente OAuth"
        2	Tipo: Aplicación web
        3	Nombre: n8n-client
        4	URI de redireccionamiento autorizado:
        http://TU_IP:5678/rest/oauth2-credential/callback
    Clic en Crear. Guarda el Client ID y Client Secret.

    8*Vincular Gmail en n8n

        Paso	Acción
        1	En n8n: "Settings" → "Credentials" → "New Credential"
        2	Buscar y seleccionar Gmail OAuth2
        3	Pegar tu Client ID y Client Secret de Google
        4	Clic en "Connect my account"
        5	Se abrirá Google → autorizar con tu cuenta
        6	Guardar la credencial
    Si Google muestra "app no verificada", clic en "Avanzado" → "Ir a n8n (no seguro)" — esto es normal en desarrollo.