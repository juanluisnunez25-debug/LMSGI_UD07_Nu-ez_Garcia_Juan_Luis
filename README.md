# LMSGI_UD07_Nu-ez_Garcia_Juan_Luis

1. Cómo lo he hecho

- Entregable 1 – Plantilla QWeb report_invoice_willmantech.xml

Este fue el más entretenido. La idea era crear una plantilla de factura para Odoo usando el motor QWeb que es HTML con directivas especiales.

Lo que hice fue partir de la estructura de una factura normal y añadir la lógica dinámica encima,  lo más importante:

- Con t-foreach="doc.invoice_line_ids recorro todas las líneas de la factura automáticamente, sin hardcodear nada.
- Con t-if controlo si mostrar o no la columna de descuento según si alguna línea tiene ese campo relleno.
- Los datos como el número de factura o el total los muestro con t-field en lugar de escribirlos a mano.

Al principio me lié un poco con la condición del descuento, pero al final quedó bien.

- Entregable 2a – Exportación JSON invoice_export.json

Para este fichero lo que pedía era representar una factura como si la hubiéramos extraído del ERP para enviarla a otro sistema .

Un array con una factura de ejemplo que tiene los datos del cliente anidados dentro y las líneas de producto también anidadas. Los campos que puse son los básicos que tendría cualquier factura real: nombre del cliente, email, productos, precios y totales.

- Entregable 2b – Factura UBL invoice_ubl.xml

Este fue el que más investigación requirió porque el estándar UBL tiene una estructura bastante concreta que hay que respetar.

Lo fundamental era incluir los namespaces correctos:
- cac para los componentes agregados (como dirección, parte, línea de factura)
- cbc para los componentes básicos (campos sueltos como nombre, importe, fecha)

También hay que meter el CustomizationID con el identificador europeo EN16931 compatible con la red PEPPOL, que es la red que usan los países europeos para intercambiar facturas electrónicas entre administraciones y empresas.

Usé los mismos datos que en el JSON para que todo sea coherente entre los dos ficheros.

- Entregable 3 – Manual de explotación manual_explotacion_willmantech.md

Para el manual seguí la estructura que marca el estándar ISO/IEC/IEEE 26514:2022, que básicamente dice cómo tiene que estar escrita la documentación técnica de software para que sea útil y completa.

Las secciones que inclui son las que pedía la actividad:

1. Introducción y arquitectura: explico cómo está montado el sistema con Docker Compose y qué módulos tiene Odoo activados.
2. Instalación: paso a paso desde cero, con el docker-compose.yml completo y las variables de entorno.
3. Seguridad: tabla de roles, cómo crear usuarios y contraseñas.
4. Backup y restauración: comandos exactos para hacer el dump de PostgreSQL y cómo restaurarlo.
5. Flujo de facturación: cómo crear una factura desde la interfaz y cómo funciona el pipeline QWeb -> HTML -> wkhtmltopdf -> PDF.

Lo que más me costó fue la parte del pipeline de renderizado, pero cuando lo entiendes y lo vas sabiendo hacer tiene bastante sentido: la plantilla XML no genera el PDF directamente, sino que primero se convierte a HTML y luego wkhtmltopdf lo imprime como si fuera un navegador.

- Tecnologías y estándares usados

- QWeb – Motor de plantillas de Odoo
- UBL 2.1 / EN16931 – Estándar europeo de factura electrónica
- PEPPOL – Red de intercambio de documentos electrónicos
- Docker / Docker Compose – Despliegue del entorno
- PostgreSQL 15 – Base de datos del ERP
- ISO/IEC/IEEE 26514:2022 – Estándar para documentación de software
