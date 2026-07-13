# Concesionaria IA - Ecosistema de Automatización con IA

## Entrega Final - Coderhouse

Este proyecto presenta un ecosistema de automatización con inteligencia artificial para una concesionaria de vehículos.  
El sistema permite recibir leads comerciales desde un formulario, interpretar el mensaje con IA, clasificar la consulta y dejar la respuesta pendiente de aprobación humana antes de contactar al cliente final.

---

## Caso de uso

El proceso automatizado gestiona tres escenarios comerciales:

1. **Compra:** el cliente quiere comprar un vehículo.
2. **Permuta:** el cliente quiere entregar su vehículo como parte de pago y comprar otro.
3. **Venta:** el usuario quiere venderle su vehículo a la concesionaria como potencial nuevo stock.

Ejemplo de mensaje recibido:

> Hola, busco un Golf GTI 2018. Tengo un Gol Power 2007 base 1.6 para entregar.

La IA analiza el mensaje, detecta que se trata de una permuta, identifica datos faltantes y genera una respuesta sugerida para revisión humana.

---

## Herramientas utilizadas

- Airtable: base de datos dinámica y memoria del sistema.
- Airtable Form: canal de entrada de leads.
- n8n: orquestador del flujo.
- OpenRouter: proveedor del modelo de IA.
- AI Agent: motor de razonamiento.
- Gmail: notificación de aprobación humana.
- GitHub: repositorio de documentación y evidencias.

---

## Base de datos Airtable

La base de datos funciona como el cerebro del sistema. Contiene tablas relacionadas para evitar datos aislados y mantener trazabilidad del proceso comercial.

### Link modo lectura

[Abrir base de datos en Airtable](https://airtable.com/invite/l?inviteId=invPM1WBdbnwubNUp&inviteToken=45578b5f99eb52b9f4ee3585e58dfcd5ab0c9b47526e61a61830f082bd786fdf&utm_medium=email&utm_source=product_team&utm_content=transactional-alerts)

### Formulario de ingreso de leads

[Abrir formulario de Airtable](https://airtable.com/app0hPWaSmILUkCcP/shraHM3TKK80BIqpK)

---

## Tablas principales

### Clientes

Guarda la información de las personas que contactan a la concesionaria.

Campos principales:

- Nombre
- WhatsApp
- Email
- Localidad
- Presupuesto habitual
- Estado cliente

Estados posibles:

- Nuevo
- Interesado
- En seguimiento
- Activo
- Compró
- No interesado

---

### Consultas Leads

Registra cada consulta comercial recibida desde el formulario.

Campos principales:

- Lead
- Cliente
- Nombre
- WhatsApp
- Mensaje original
- Canal de entrada
- Escenario
- Tipo de búsqueda
- Presupuesto máximo
- Moneda
- Tipo de vehículo
- Vehículo ofrecido
- Datos faltantes
- Estado
- Respuesta IA

Estados principales:

- Pendiente lectura IA
- Procesado por IA
- Datos incompletos
- Pendiente aprobación humana

---

### Vehículos Stock

Contiene los vehículos disponibles de la concesionaria.

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

---

### Vehículos Ofrecidos

Registra los autos ofrecidos por usuarios en escenarios de permuta o venta.

Campos principales:

- Lead relacionado
- Cliente
- Marca
- Modelo
- Año
- Kilometraje
- Precio pretendido
- Estado evaluación

---

### Propuestas

Guarda las respuestas o propuestas generadas por IA antes de ser aprobadas por un humano.

Estados posibles:

- Borrador IA
- Pendiente aprobación
- Aprobada
- Rechazada
- Enviada

---

### Errores Logs

Registra errores técnicos del flujo para garantizar resiliencia.

Campos principales:

- Error
- Fecha
- Nodo del error
- Mensaje de error
- Estado error
- Acción tomada

---

## Arquitectura del sistema

```text
Formulario Airtable
        ↓
Airtable - Consultas Leads
        ↓
n8n Schedule Trigger
        ↓
Search records - Leads pendientes IA
        ↓
AI Agent
        ↓
OpenRouter Chat Model
        ↓
Tool Airtable - Consulta de stock disponible
        ↓
Memory por WhatsApp
        ↓
Update record en Airtable
        ↓
Gmail - Notificación de aprobación humana
        ↓
Humano revisa y aprueba
