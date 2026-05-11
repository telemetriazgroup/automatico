Fase 4 -> Construir el Workflow en n8n

    12*Nodo 1 : Gmail Trigger (revisar correos cada minuto)
        Campo	Valor
        Tipo	Gmail Trigger
        Credencial	Tu Gmail OAuth2
        Evento	Message Received
        Poll every	1 minute
        Filters	is:unread (opcional)

    13*Nodo 2: Code - Contador y validacion 

        Agrega un nodo tipo Code (JavaScript). Este nodo lleva el contador de correos y valida las palabras clave:

            const keywords = ['telemetria', 'telemetría', 'Luis', 'Eusebio'];
            const subject = $json.subject || '';
            const body = $json.text || $json.snippet || '';
            const from = $json.from || '';
            const fullText = (subject + ' ' + body + ' ' + from).toLowerCase();

            const matched = keywords.filter(kw =>
            fullText.includes(kw.toLowerCase())
            );

            return [{
            json: {
                ...$json,
                _matched: matched.length > 0,
                _matchedKeywords: matched,
                _subject: subject,
                _from: from,
                _bodyPreview: body.substring(0, 800),
                _date: new Date().toLocaleString('es-PE',
                {timeZone:'America/Lima'})
            }
            }];

    14* Nodo 3:IF -¿Contiene palabras clave ?

        Campo	Valor
        Tipo	IF Node
        Condición	{{ $json._matched }}
        Operación	is equal to true
        La rama TRUE continúa al resumen con IA. La rama FALSE termina (correo ignorado).

    
    15*Nodo 4:HTTP Request - Resumen con Calude AI

        Campo	Valor
        Method	POST
        URL	https://api.anthropic.com/v1/messages
        Header: x-api-key	TU_API_KEY_ANTHROPIC
        Header: anthropic-version	2023-06-01
        Header: content-type	application/json

        Body JSON:
        {
        "model": "claude-sonnet-4-20250514",
        "max_tokens": 400,
        "messages": [{
            "role": "user",
            "content": "Resume en 3 puntos clave (máximo 2 líneas cada uno) este correo en español. Sé directo y profesional.\n\nAsunto: {{$json._subject}}\n\nCuerpo:\n{{$json._bodyPreview}}"
        }]
        }
    16*Nodo 5:Telegram - Envia alerta

        Campo	Valor
        Tipo	Telegram
        Credencial	Telegram Bot Monitor
        Operación	Send Message
        Chat ID	TU_CHAT_ID
        Parse Mode	Markdown

        Texto del mensaje:
            🚨 *ALERTA — Correo referenciado*

            📧 *De:* {{ $('Code').item.json._from }}
            📌 *Asunto:* {{ $('Code').item.json._subject }}
            🔍 *Palabras detectadas:* {{ $('Code').item.json._matchedKeywords.join(', ') }}
            🕐 *Hora:* {{ $('Code').item.json._date }}

            📝 *Resumen IA:*
            {{ $json.content[0].text }}

            ─────────────────────
            📄 *Extracto del correo:*
            {{ $('Code').item.json._bodyPreview.substring(0,500) }}

    17 Node 6 (opcional): reporte estadistico diario

        Agrega un segundo workflow con un nodo Schedule Trigger (todos los días a las 8pm) conectado a un nodo Code que lea de una variable estática, y envíe por Telegram:

        // En el Code del workflow principal,
        // al final del nodo validación, acumula en
        // variables estáticas de n8n:

        const stats = $getWorkflowStaticData('global');
        if (!stats.total) stats.total = 0;
        if (!stats.matched) stats.matched = 0;

        stats.total += 1;
        if ($json._matched) stats.matched += 1;

        Mensaje de reporte diario en Telegram:
            📊 *Reporte diario — Monitor Gmail*

            📬 Correos analizados: {{ stats.total }}
            🔍 Correos referenciados: {{ stats.matched }}
            📅 Fecha: {{ new Date().toLocaleDateString('es-PE') }}
        El reporte te da el total de correos analizados y cuántos tuvieron las palabras clave del día.
