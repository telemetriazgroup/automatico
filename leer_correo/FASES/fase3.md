
Fase 3 -> Configurar Bot de Telegram

    9*Crear el bot con @BotFather

        Paso	Acción
        1	Abre Telegram → busca @BotFather
        2	Escribe /newbot
        3	Nombre del bot: Monitor Gmail Bot
        4	Username: tu_monitor_gmail_bot (debe terminar en _bot)
        5	Copia el TOKEN que te da (formato: 12345:AABBcc...)
    Guarda el token en un lugar seguro. Es como la contraseña del bot.


    10*Obtener tu chat ID

    Primero envía cualquier mensaje a tu bot en Telegram. Luego:

    Abre este URL en el navegador (reemplaza TOKEN)
        https://api.telegram.org/botTU_TOKEN/getUpdates
    Busca en el JSON el campo:

        "chat":{"id": 123456789, ...}
    Ese número es tu Chat ID. Guárdalo.

    Si el JSON está vacío, envía un mensaje al bot primero y vuelve a cargar el URL.

    11*Conectar Telegram en n8n
        
        Paso	Acción
        1	En n8n: "Settings" → "Credentials" → "New"
        2	Buscar y seleccionar Telegram API
        3	Pegar el token del bot en el campo "Access Token"
        4	Guardar con nombre Telegram Bot Monitor