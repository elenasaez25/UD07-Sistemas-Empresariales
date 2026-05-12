# Actividad Práctica – AEE

## Desafío de Consultoría "Gobernanza Digital"

* **Unidad:** UD7. Sistemas de Gestión Empresarial  
* **Contexto:** RA7 (CE: a, c, f, i)  
* **Alumnos:** Pablo Márquez, Adrián López, Joaquin Madrid, Elena Sáez

# 1\. Contexto de la actividad

La empresa sevillana **"Aceites del Aljarafe S.L."** opera actualmente con el "Efecto Silo": usan hojas de cálculo para el inventario, una base de datos Access antigua para clientes y facturan a mano. Han contratado a nuestro equipo de consultoría para migrar a un sistema profesional.

# 2\. Misión del Equipo

La tarea es entregar una **Propuesta Técnica de Implantación** que cubra los siguientes tres bloques fundamentales:

## Bloque A: Análisis de Mercado y Selección (CE a, c)

Debemos elegir entre **Odoo (SaaS o Community)**, **SAP S/4HANA** o **Zoho One**.

<img width="865" height="744" alt="Captura de pantalla 2026-05-12 095518" src="https://github.com/user-attachments/assets/468f4bf5-c054-4d75-9ece-96bcb9e0b7a9" />


Una vez analizado el número de empleados, nos encontramos antes una pyme (pequeña empresa, de 10 a 50 empleados), por lo que  tenemos un capital bastante más limitado. Para podernos ajustarnos a este presupuesto, tenemos que descartar la opción de SAP, ya que es la más costosa de todas ellas pese a que es la más potente, pero debido a su alto costo solo se emplea en empresas multinacionales o sociedades anónimas de gran poder adquisitivo ( su coste  va de 1500€-3000€ por usuario al año más un coste de implementación que puede ir desde los 50000€ a los 5000000€), además de que la curva de aprendizaje es la más alta de todas ellas. 

Si tuviéramos que decantarnos por una de ellas la que más se adecuaba a nuestras necesidades sería odoo Community, debido a que cubre las necesidades básicas de cualquier empresa como serian: control de inventario, área de ventas y su parte contable, esta última si añadimos módulos adicionales de la comunidad de forma gratuita (, nos permitirá poder utilizarlo para presentarlos a hacienda directamente. Incluido todo esto también tiene la capacidad de cumplir con la especificación del etiquetado que nos marca nuestro cliente, ya que al ser de código abierto tenemos libertad para modificar y adaptar este etiquetado a nuestras necesidades.

La mayor desventaja de este sistema es que no cuenta con soporte técnico, la única opción de resolver problemas de esta índole es mediante foros, pero por suerte tiene una comunidad muy activa. También es cierto que no tiene actualizaciones de forma habitual, lo que puede provocar que haya vulnerabilidades visibles y sea más fácil acceder a nuestra información empresarial.

2\.

Resumiendo como hemos comentado previamente el coste de Odoo Community, el coste es gratuito. Pero más a largo plazo a 3 años cambiaríamos a Odoo SaaS ya que tiene mayor seguridad y el coste no es mucho más caro, debido a que pensamos que nuestra empresa va a tener una evolución  creciente y vamos a tener más trabajadores y más clientes futuros. En caso de que esto no mejorase en gran medida continuaremos con Odoo Community.

## 

## 

## Bloque B: Diseño de Seguridad RBAC (CE f)

| Rol/Permisos | Clientes | Presupuestos | Stock | Docs entrada y salida | Facturas |
| :---- | ----- | ----- | ----- | ----- | ----- |
| **Admin** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Comercial** | ✅ | ✅ |  |  |  |
| **Operario de almacén** |  |  | ✅ | ✅ |  |
| **Contable** |  |  |  |  | ✅ |

El **administrador** tiene acceso a todos los permisos, ya que es el principal actor en un sistema de gestión empresarial. 

El **comercial** solo ve sus clientes y presupuestos según las **Reglas de Registro (Record Rules)**, las cuales hacen más estrictos los permisos de acceso a dichos grupos. 

El **operario de almacén** solo tiene acceso a los documentos de entrada y salida, como por ejemplo el control de recepción, entrega de pedidos…

Y el **contable** únicamente puede leer las facturas, ya que su función es llevar las cuentas de los gastos y producción de la empresa, por lo que no debe modificar el stock.

## 

## Bloque C: Manual de Despliegue:

En primer lugar, el manual de despliegue contendrá el **docker-compose.yml** necesario en caso de que el sistema se caiga. Por último, es necesario proporcionar el comando para realizar un backup de la base de datos.

1. Archivo `docker-compose.yml`

services:

  odoo:

    image: odoo:latest //Cómo vamos a elegir odoo, seleccionamos la versión más reciente.

    container\_name: odoo

    restart: unless-stopped

    depends\_on:

      \- db

    ports:

      \- "8200:8069" //Estos puertos cambiarían según las preferencias de la empresa.

    environment:

//USER y PASSWORD por defecto, hasta que la empresa nos proporcione los datos que quieran poner.

      \- HOST=db

      \- USER=odoo 

      \- PASSWORD=odoo

    volumes:

      \- odoo-data:/var/lib/odoo

      \- ./config:/etc/odoo

      \- ./addons:/mnt/extra-addons

    command: odoo \-d odoo \--db\_host=db \--db\_user=odoo \--db\_password=odoo

  db:

    image: postgres:16.0 //La imágen de la base de datos será postgres, también siendo una de las más recientes.

    container\_name: db

    environment: //USER y PASSWORD de la base de datos también por defecto, hasta que la empresa nos proporcione los datos que quieran poner.

      \- POSTGRES\_DB=odoo

      \- POSTGRES\_USER=odoo

      \- POSTGRES\_PASSWORD=odoo

      \- PGDATA=/var/lib/postgresql/data/pgdata

    volumes: //Directorios donde se guardará la base de datos

      \- db-data:/var/lib/postgresql/data

volumes:

  odoo-data:

  db-data:

2. Comando para realizar un backup de la base de datos PostgreSQL [\[1\]](https://platzi.com/blog/como-generar-una-backup-de-postgresql-y-como-restaurarla/)

   ***pg\_dump \-U usuario \-W \-h host basename \> basename.sql***

	\-U → Se refiere al usuario

	\-W → Parámetro que solicita la contraseña (password) del usuario

	basename → Es el último parámetro que indica el nombre de la base de datos

\> basename.sql → Indica que la salida del comando **pg\_dump** la guarde en un archivo llamado “basename.sql”. Se puede modificar al gusto.

# 3\. Conclusión

* ¿Es coherente el TCO con la realidad de una PYME?

Si el TCO es sumamente coherente y beneficioso para una PYME, ya que les permite tomar decisiones de inversion mas inteligentes al considerar mas costes asociados a un activo y no solo el precio inicial .

* ¿La matriz RBAC evita que el comercial vea los costes de producción?

Si la matriz RBAC evita que personal no autorizado,como el comercial visualice informacion sensible como los costes de produccion. Este sistema permite restringir el acceso basandose en el principio de menor privilegio o en la necesidad de reconocer

* ¿El comando de backup es sintácticamente correcto?

Si.el comando backup es sumamente correcto,aunque tambien se suele decir wbadmin en el cmd de Windows y en Ubuntu rsync.
