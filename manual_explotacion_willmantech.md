Manual de Explotación – WillmanTech S.L.

1. Introducción y Arquitectura

WillmanTech S.L. utiliza un ERP basado en Odoo 16 desplegado en contenedores Docker. El sistema cubre los módulos de Ventas, Facturación y CRM.

La topología de despliegue usa Docker Compose con dos servicios principales:


- odoo: contenedor con la aplicación web y el motor de plantillas QWeb.
- db: contenedor PostgreSQL que almacena todos los datos del ERP.

- Guía de Instalación y Reinstalación

- Requisitos previos

- Docker Engine 24+
- Docker Compose v2
- 4 GB de RAM mínimo

Pasos para levantar el entorno desde cero

1. Clonar o copiar los ficheros del proyecto

2. Crear el fichero .env con las variables de entorno necesarias

3. Definir el docker-compose.yml

4. Levantar los contenedores:

docker compose up -d

5. Acceder al ERP y crear la base de datos desde el asistente inicial.

- Reinstalación limpia

Para borrar todos los datos y empezar de cero:

docker compose down -v
docker compose up -d

El flag -v elimina los volúmenes y todos los datos almacenados, hacer backup antes.

3. Seguridad y Control de Acceso


- Crear y asignar roles

1. Ir a Ajustes -> Usuarios y Empresas -> Usuarios.
2. Crear nuevo usuario e indicar nombre y email.
3. En la pestaña Permisos de acceso, asignar el nivel correspondiente en cada módulo.
4. Guardar y enviar invitación por correo.

- Políticas de contraseñas

- Longitud mínima: 10 caracteres.
- Debe contener mayúsculas, minúsculas y números.
- Caducidad recomendada: 90 días.
- Activar autenticación en dos pasos desde Ajustes → Usuarios → Autenticación de dos factores.

- Contraseña maestra

La contraseña maestra ODOO_ADMIN_PASSWD protege el gestor de bases de datos en /web/database/manager. Debe mantenerse en secreto y no coincidir con ninguna contraseña de usuario.

4. Procedimiento de Backup y Restauración

Backup de la base de datos

Ejecutar el siguiente comando para volcar la base de datos desde el contenedor:

docker exec -t <nombre_contenedor_db> pg_dump -U odoo willmantech_db > backup_$(date +%F).sql


Para incluir también los ficheros adjuntos y datos del filestore de Odoo:

docker cp <nombre_contenedor_odoo>:/var/lib/odoo ./backup_filestore_$(date +%F)

Restauración

1. Restaurar la base de datos:

cat backup_2025-05-15.sql | docker exec -i <nombre_contenedor_db> psql -U odoo -d willmantech_db

2. Restaurar el filestore:

docker cp ./backup_filestore_2025-05-15 <nombre_contenedor_odoo>:/var/lib/odoo

3. Reiniciar el contenedor Odoo:

docker compose restart odoo

- Recomendaciones

- Automatizar el backup diario con cron.
- Guardar las copias en un almacenamiento externo (NAS o nube).
- Verificar la integridad del backup al menos una vez al mes haciendo una restauración de prueba.

5. Flujo Operativo de Facturación e Informes

Crear una factura desde la interfaz

1. Acceder al módulo Facturación -> Clientes -> Facturas.
2. Hacer clic en Nuevo.
3. Seleccionar el cliente en el campo Cliente.
4. Añadir líneas de producto con cantidad y precio unitario en la pestaña Líneas de factura.
5. Revisar impuestos y totales en la parte inferior.
6. Hacer clic en Confirmar para pasar la factura a estado Publicada.

- Generar el informe PDF

Una vez confirmada la factura:

1. Hacer clic en el botón **Imprimir → Facturas**.
2. El sistema lanza el pipeline de renderizado internamente.

- Pipeline de renderizado: HTML → wkhtmltopdf → PDF

En resumen: la plantilla XML define la estructura visual, QWeb la rellena con datos reales del ERP, y wkhtmltopdf convierte ese resultado en el PDF final que ve el usuario.
