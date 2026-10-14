[README.md](https://github.com/user-attachments/files/33459247/README.md)
# Checkpoint 4 — Asistente de Atención al Cliente | Growthive Agency

**Autor:** Sanders Sánchez  
**Plataforma:** n8n  
**Entregable:** `checkpoint4_sanders_sanchez.json`

## Descripción

Workflow de atención al cliente construido en n8n. Un agente de IA responde consultas generales sobre los servicios de Growthive Agency, consulta el estado de pedidos en Google Sheets y deriva solicitudes sensibles o que requieren atención humana por Gmail.

## Arquitectura

```text
When chat message received
          |
       AI Agent
          |-- Groq Chat Model
          |-- Simple Memory
          |-- Consulta (Google Sheets: Get Row(s))
          `-- Escalar consulta (Gmail: Send Message)
```

## Herramientas y comportamiento

| Componente | Función |
| --- | --- |
| Chat Trigger | Recibe las consultas del usuario. |
| AI Agent + Groq | Identifica la intención y decide cuándo utilizar herramientas. |
| Simple Memory | Mantiene el contexto dentro de la sesión. |
| Google Sheets (`Consulta`) | Busca un pedido por número y permite cotejar el nombre del cliente antes de informar el estado. |
| Gmail (`Escalar consulta`) | Notifica al equipo cuando hay reclamos, reembolsos, asuntos legales, datos sensibles o solicitudes de atención humana. |

**Reglas de seguridad:** no inventar estados de pedidos; no compartir información si el nombre proporcionado no coincide con el registro; no confirmar un escalamiento hasta que Gmail responda correctamente. La coincidencia entre nombre y pedido es una validación básica, no una autenticación robusta.

## Credenciales

La actividad fue adaptada siguiendo las indicaciones del profesor: se utiliza **Google Sheets como CRM** y **Gmail para el escalamiento**. 


## Pruebas funcionales

1. **Consulta autorizada:** pedido `0001`, cliente `Juan`. El agente utilizó Google Sheets y respondió con el estado registrado: `En preparación`.
2. **Consulta no autorizada:** se comprobó el rechazo de solicitudes con nombre que no coincide con el registro, sin revelar el estado del pedido.
3. **Escalamiento humano:** una petición de reembolso y reclamación activó Gmail; la herramienta devolvió un identificador de mensaje y el agente confirmó la derivación.

Estas pruebas corresponden al entorno de desarrollo; para reproducirlas se requiere una hoja de pedidos con datos de prueba apropiados.

## Importar en n8n

1. Descargar `checkpoint4_sanders_sanchez.json` desde este repositorio.
2. En n8n, crear un workflow e importar el archivo JSON mediante la opción de importar desde archivo.
3. Seleccionar credenciales propias de **Groq**, **Google Sheets** y **Gmail** en los nodos correspondientes.
4. En `Consulta`, seleccionar el documento y la hoja que contienen las columnas `id`, `pedido`, `cliente` y `estado` (y, si están disponibles, `createdAt` y `updatedAt`). Mantener `pedido` como texto para preservar ceros iniciales.
5. Configurar la dirección de destino autorizada en `Escalar consulta`.
6. Ejecutar pruebas manuales de consulta válida, consulta no autorizada y escalamiento antes de publicar el workflow.

**Nota:** las credenciales y permisos reales no se exportan con el JSON. La importación estructural debe comprobarse en la instalación de destino; esta guía no implica que el archivo haya sido validado en otra instancia.

## Archivos del repositorio

```text
checkpoint4-growthive/
├── README.md
├── checkpoint4_sanders_sanchez.json
└── images/
    └── credentials.png
```

## Privacidad

Antes de publicar el repositorio, revisar el JSON exportado y cualquier captura para eliminar claves, tokens, direcciones privadas, datos reales de clientes y otra información sensible. Utilizar exclusivamente datos ficticios en las pruebas públicas.
