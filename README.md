# Actividad Práctica – AEE

## Desafío de Consultoría "Gobernanza Digital"

* **Unidad:** UD7. Sistemas de Gestión Empresarial  
* **Contexto:** RA7 (CE: a, c, f, i)  
* **Alumnos:** Pablo Márquez, Adrián López, Joaquin Madrid, Elena Sáez

# 1\. Contexto de la actividad

La empresa sevillana **"Aceites del Aljarafe S.L."** opera actualmente con el "Efecto Silo": usan hojas de cálculo para el inventario, una base de datos Access antigua para clientes y facturan a mano. Han contratado a nuestro equipo de consultoría para migrar a un sistema profesional.

# 2\. Misión del Equipo (Grupos de 3 personas)

La tarea es entregar una **Propuesta Técnica de Implantación** que cubra los siguientes tres bloques fundamentales:

## Bloque A: Análisis de Mercado y Selección (CE a, c)

Debemos elegir entre **Odoo (SaaS o Community)**, **SAP S/4HANA** o **Zoho One**.

1. Justificar la elección basándote en el perfil de la empresa (25 empleados, presupuesto ajustado, necesidad de personalización en el etiquetado).  
2. **Cálculo de TCO:** Realizar una estimación a 3 años.  
   * Coste de licencias/suscripción.  
   * Coste de implantación (vuestras horas de desarrollo: estima 100h a 40€/h).  
   * Coste operativo (Hosting en Google Cloud, AWS, Huawei Cloud o similar).

Una vez analizado el número de empleados, nos encontramos antes una pyme (pequeña empresa, de 10 a 50 empleados), por lo que  tenemos un capital bastante más limitado. Para podernos ajustarnos a este presupuesto, tenemos que descartar la opción de SAP, ya que es la más costosa de todas ellas pese a que es la más potente, pero debido a su alto costo solo se emplea en empresas multinacionales o sociedades anónimas de gran poder adquisitivo ( su coste  va de 1500€-3000€ por usuario al año más un coste de implementación que puede ir desde los 50000€ a los 5000000€), además de que la curva de aprendizaje es la más alta de todas ellas. 

Si tuviéramos que decantarnos por una de ellas la que más se adecuaba a nuestras necesidades sería odoo Community, debido a que cubre las necesidades básicas de cualquier empresa como serian: control de inventario, área de ventas y su parte contable, esta última si añadimos módulos adicionales de la comunidad de forma gratuita (, nos permitirá poder utilizarlo para presentarlos a hacienda directamente. Incluido todo esto también tiene la capacidad de cumplir con la especificación del etiquetado que nos marca nuestro cliente, ya que al ser de código abierto tenemos libertad para modificar y adaptar este etiquetado a nuestras necesidades.

La mayor desventaja de este sistema es que no cuenta con soporte técnico, la única opción de resolver problemas de esta índole es mediante foros, pero por suerte tiene una comunidad muy activa. También es cierto que no tiene actualizaciones de forma habitual, lo que puede provocar que haya vulnerabilidades visibles y sea más fácil acceder a nuestra información empresarial.

2\.

Resumiendo como hemos comentado previamente el coste de Odoo Community, el coste es gratuito. Pero más a largo plazo a 3 años cambiaríamos a Odoo SaaS ya que tiene mayor seguridad y el coste no es mucho más caro.

## 

## 

## Bloque B: Diseño de Seguridad RBAC (CE f)

Diseña la matriz de permisos para los siguientes roles, asegurando el **Principio de Mínimo Privilegio**:

* **Administrador:** Acceso total.  
* **Comercial:** Solo ve sus clientes y presupuestos (Record Rules).  
* **Operario de Almacén:** Solo ve stock y albaranes de entrada/salida.  
* **Contable:** Puede mirar facturas pero no puede modificar el stock.

| Rol/Permisos | Clientes | Presupuestos | Stock | Docs entrada y salida | Facturas |
| :---- | ----- | ----- | ----- | ----- | ----- |
| **Admin** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Comercial** | ✅RC | ✅RC |  |  |  |
| **Operario de almacén** |  |  | ✅ | ✅ |  |
| **Contable** |  |  |  |  | ✅ |

El **administrador** tiene acceso a todos los permisos, ya que es el principal actor en un sistema de gestión empresarial. 

El **comercial** solo ve sus clientes y presupuestos según las **Reglas de Registro (Record Rules)**, las cuales hacen más estrictos los permisos de acceso a dichos grupos. 

El **operario de almacén** solo tiene acceso a los documentos de entrada y salida, como por ejemplo el control de recepción, entrega de pedidos…

Y el **contable** únicamente puede leer las facturas, ya que su función es llevar las cuentas de los gastos y producción de la empresa, por lo que no debe modificar el stock.

## 

## Bloque C: Documentación de Explotación (CE i)

Siguiendo la norma **ISO/IEC 26514**, redacta un breve **Manual de Despliegue** para que el responsable de IT de la empresa pueda levantar el sistema en caso de caída. Debe incluir:

1. El fragmento de *docker-compose.yml* necesario.  
2. El comando para realizar un backup de la base de datos PostgreSQL.

**Manual de Despliegue:**

En primer lugar, el manual de despliegue contendrá el **docker-compose.yml** necesario en caso de que el sistema se caiga. Por último, es necesario proporcionar el comando para realizar un backup de la base de datos.

1. Archivo docker-compose.yml

 


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

# 3\. Entregable y Evaluación

Subirás un README a un repositorio Github específico con la propuesta técnica. Lo importante aquí es la **precisión técnica**:

* ¿Es coherente el TCO con la realidad de una PYME?

\- Si el TCO es sumamente coherente y beneficioso para una PYME, ya que les permite tomar decisiones de inversion mas inteligentes al considerar mas costes asociados a un activo y no solo el precio inicial .

* ¿La matriz RBAC evita que el comercial vea los costes de producción?

\- Si la matriz RBAC evita que personal no autorizado,como el comercial visualice informacion sensible como los costes de produccion. Este sistema permite restringir el acceso basandose en el principio de menor privilegio o en la necesidad de reconocer

* ¿El comando de backup es sintácticamente correcto?

\- Si.el comando backup es sumamente correcto,aunque tambien se suele decir wbadmin en el cmd de Windows y en Ubuntu rsync.

# 5\. Rúbrica de Evaluación

| Criterio de Evaluación | Sobresaliente (10-9) | Notable(8-7) | Aprobado(6-5) | Insuficiente(4-0) | Peso |
| ----- | ----- | ----- | ----- | ----- | :---: |
| **Análisis y Selección de ERP (CE a, c)** | Selecciona el sistema ideal justificando con precisión técnica y estratégica según las necesidades de la PYME. | Selecciona el sistema adecuado con una justificación clara pero con pocos detalles técnicos. | La selección es aceptable pero la justificación es genérica o poco adaptada al caso. | No justifica la selección o elige un sistema claramente inadecuado para el escenario. | 18% |
| **Cálculo de TCO (CE c)** | Realiza un cálculo quirúrgico a 3 años incluyendo licencias, horas de desarrollo y hosting con datos reales. | Calcula el TCO incluyendo los bloques principales, aunque con pequeñas imprecisiones en los costes operativos. | El cálculo es muy básico y omite costes indirectos importantes (mantenimiento o backups). | No realiza el cálculo de TCO o los datos son totalmente incoherentes con el mercado. | 18% |
| **Diseño RBAC y Seguridad (CE f)** | Define una matriz de permisos perfecta que cumple estrictamente el principio de mínimo privilegio y usa Record Rules. | Define la matriz correctamente para todos los roles, aunque la restricción del comercial es mejorable. | La matriz es funcional pero asigna permisos excesivos a roles que no los requieren. | No define roles o la matriz permite acceso total a personal no autorizado (riesgo de seguridad). | 18% |
| **Doc. de Explotación e ISO 26514 (CE i)** | Documentación profesional con *docker-compose.yml* y comandos de backup 100% funcionales y sintácticamente correctos. | Documentación clara siguiendo la norma ISO, con comandos funcionales pero con errores leves de formato. | Incluye la documentación mínima pero los comandos requieren ajustes para ser funcionales. | Documentación inexistente, desorganizada o con comandos que generan errores críticos. | 18% |
| **Organización del repositorio Github** **(Competencia Transversal)** | Organización profesional, README con imágenes y bien “estilizado” | Organización profesional, README con imágenes | Organización profesional y README | Sin organización aunque tenga README | 14% |
| **Presentación y Pitch** **(Competencia Transversal)** | Defiende la propuesta con seguridad, lenguaje técnico preciso y convence al "cliente" de la viabilidad. | Presentación clara y profesional, respondiendo bien a la mayoría de las dudas técnicas. | Exposición correcta pero con dificultades para defender los puntos financieros (TCO). | No es capaz de explicar la propuesta o utiliza un lenguaje poco profesional. | 14% |
| **Uso de agentes IA** | Incluyendo Google, Bing, etc. “Modo IA” |  |  |  | **\-100%** |

### **Observaciones:**

* **CE a y c:** Se ha valorado especialmente que no solo elijas Odoo por ser el que usamos en clase, sino que demuestres conocer las alternativas.  
* **CE f:** Lo importante aquí es que el comercial no pueda "asomarse" a la contabilidad.  
* **CE i:** Un comando de backup mal escrito es un suspenso directo en este bloque, fíjate que la integridad del dato no admite errores de sintaxis.  
* **Evaluación de competencias**. Una vez hayas leído toda la actividad y sepas qué vas a hacer, levántate y desde tu puesto dile al profesor que has comprendido la actividad, los nombres de los integrantes del grupo y el profesor te dará el “Ok“ para comenzar. Si no lo haces, el profesor evaluará qué has hecho y si comienzas de nuevo o continúas.

