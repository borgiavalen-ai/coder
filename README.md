# Concesionaria IA - Ecosistema de Automatización con IA

## Entrega Final - Coderhouse

Este proyecto presenta un ecosistema de automatización con inteligencia artificial para una concesionaria de vehículos.

El sistema recibe leads desde un formulario de Airtable, los procesa con un agente de IA en n8n, consulta el stock disponible, actualiza el estado del lead en Airtable y envía una notificación interna por Gmail para revisión humana antes de contactar al cliente final.

---

## Caso de uso

La automatización gestiona leads comerciales de una concesionaria en tres escenarios principales:

1. **Compra:** una persona quiere comprar un vehículo.
2. **Permuta:** una persona quiere entregar su vehículo como parte de pago y comprar otro.
3. **Venta:** una persona quiere venderle su vehículo a la concesionaria.

Ejemplo de lead:

> Hola, busco un Golf GTI 2018. Tengo un Gol Power 2007 base 1.6 para entregar.

La IA debe detectar que se trata de una permuta, identificar datos faltantes y generar una respuesta sugerida para revisión humana.

---

## Herramientas utilizadas

- **Airtable:** base de datos dinámica y memoria operativa del sistema.
- **Airtable Form:** canal de entrada de leads.
- **n8n:** orquestador del flujo.
- **AI Agent:** motor de razonamiento.
- **OpenRouter:** proveedor del modelo de lenguaje.
- **Gmail:** canal interno de notificación para aprobación humana.
- **GitHub:** repositorio de documentación, JSON y evidencias.

---

## Base de datos Airtable

La base de datos funciona como el cerebro del sistema. Contiene tablas relacionadas para gestionar clientes, leads, stock, vehículos ofrecidos, propuestas y errores.

### Link modo lectura

[Abrir base de datos en Airtable](https://airtable.com/invite/l?inviteId=invPM1WBdbnwubNUp&inviteToken=45578b5f99eb52b9f4ee3585e58dfcd5ab0c9b47526e61a61830f082bd786fdf&utm_medium=email&utm_source=product_team&utm_content=transactional-alerts)

### Formulario de ingreso de leads

[Abrir formulario de Airtable](https://airtable.com/app0hPWaSmILUkCcP/shraHM3TKK80BIqpK)

---

## Tablas principales

### Clientes

Contiene los datos generales de las personas que contactan a la concesionaria.

Campos principales:

- Nombre
- WhatsApp
- Email
- Localidad
- Presupuesto habitual
- Estado cliente

---

### Consultas Leads

Es la tabla principal del flujo. Registra cada consulta recibida desde el formulario y guarda el análisis realizado por la IA.

Campos utilizados por el flujo:

- Lead
- Nombre
- WhatsApp
- Mensaje original
- Canal de entrada
- Presupuesto máximo
- Moneda
- Tipo de vehículo
- Transmisión preferida
- Forma de pago
- Tiene vehículo para permutar/vender
- Vehículo ofrecido
- Estado
- Datos faltantes
- Respuesta IA
- Fecha Trigger

Estados utilizados:

- Pendiente lectura IA
- Procesado por IA
- Datos incompletos
- Pendiente aprobación humana

---

### Vehículos Stock

Contiene el stock disponible de la concesionaria.  
El agente IA consulta esta tabla como herramienta cuando el lead corresponde a compra o permuta.

Campos principales:

- Vehículo
- Marca
- Modelo
- Año
- Precio
- Kilometraje
- Tipo de vehículo
- Estado stock
- Disponible
- Descripción comercial

Vista utilizada:

- Stock disponible

---

### Vehículos Ofrecidos

Tabla preparada para registrar vehículos ofrecidos por clientes en escenarios de permuta o venta.

En el flujo actual, la IA analiza el vehículo ofrecido desde el lead y guarda los datos faltantes en la tabla Consultas Leads.

---

### Propuestas

Tabla preparada para una etapa posterior del proceso comercial.

En el flujo actual, la propuesta no se crea como registro independiente. La respuesta sugerida queda guardada en el campo **Respuesta IA** de la tabla Consultas Leads y se envía por Gmail para revisión humana.

---

### Errores Logs

Tabla destinada a registrar errores del flujo.

El workflow principal tiene configurado un **Error Workflow externo** en n8n. Para evidenciarlo, se incluye la captura de configuración y, si corresponde, el JSON del flujo de error handling.

---

## Arquitectura real del flujo n8n

El flujo exportado en el archivo JSON funciona de la siguiente manera:

```text
Airtable Trigger
        ↓
AI Agent
        ↓
OpenRouter Chat Model
        ↓
Airtable Tool - Buscar stock disponible
        ↓
Simple Memory
        ↓
Update record en Airtable
        ↓
Gmail - Notificación interna
