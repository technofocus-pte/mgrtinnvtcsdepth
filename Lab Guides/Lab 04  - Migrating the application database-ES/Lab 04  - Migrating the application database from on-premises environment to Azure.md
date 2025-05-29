# Laboratorio 04 – Migrar el database de la aplicación desde on-premises environment a Azure

## Obectivo

En este laboratorio, utilicemos la metodología Cloud Adoption Framework
Adopt para migrar los on-premises Databases con el Azure Database
Migration Service para migrar el SQL Database. Azure Database Migration
Service es una herramienta que le ayuda a simplificar, guiar y
automatizar la migración de sus datos a Azure. Migre sus datos, esquema
y objetos desde múltiples fuentes a la nube a escala.

![A diagram of a cloud server Description automatically generated with
medium confidence](./media/image1.jpg)

Un diagrama del servidor de la nube Descripción generada automáticamente
con confianza media

> **Ojo**: inicie sus VMs si los ha parado después del laboratorio
> anterior.

## Ejercicio 1 – Migrar el Microsoft SQL database a Azure SQL Database

### Tarea 1: Registre el Microsoft.DataMigration resource provider

Previo al uso de Azure Database Migration Service, se debe registrar el
resource provider **Microsoft.DataMigration** en la suscripción final.

1.  Abra el Azure Cloud Shell al navegar a `https://shell.azure.com`.
    Inicie sesión con sus Azure subscription credentials si se le pide,
    seleccione una sesión **PowerShell**, y acepte cualquier prompt.

- ![A screenshot of a computer Description automatically
  generated](./media/image2.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

2.  En la ventana **Get started** seleccione **Mount storage account** y
    luego seleccione la suscripción adecuada y haga clic en el botón
    **Apply**.

- ![alt text](./media/image3.png)

  alt text

3.  En la ventana **Mount storage account** seleccione **We will create
    a storage account for you** y haga clic en el botón **Next**.

- ![alt text](./media/image4.png)

  alt text

4.  Espere a que se complete la implementación.

5.  Ejecute el siguiente command para registrar
    el **Microsoft.DataMigration** resource provider:

- `Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration`

  > **Ojo**: Esto puede llevar varios minutos. Puede seguir con la
  > siguiente tarea sin esperar a que se complete la inscripción. No
  > usará ningún resource provider hasta la tarea 3.

  ![A screenshot of a computer Description automatically
  generated](./media/image5.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

6.  Puede averiguar el estado al ejecutar:

- `Get-AzResourceProvider -ProviderNamespace Microsoft.DataMigration | Select-Object ProviderNamespace, RegistrationState, ResourceTypes`

  ![A screenshot of a computer Description automatically
  generated](./media/image6.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

Ha completado esta tarea. No cierre ninguna ventana y siga con la
próxima tarea.

**Resumen de la tarea**

En esta tarea, ha registrado el **Microsoft.DataMigration** resource
provider con su suscripción. Esto habilita a la suscripción para usar el
Azure Database Migration Service.

### Tarea 2: Cree el Database Migration Service

En esta tarea, creará un Azure Database Migration Service resource. El
**Microsoft.DataMigration resource** provider gestiona este recurso que
registró en la tarea 1.

> **Ojo**: El Azure Database Migrate Service (DMS) requiere un network
> access a su on-premises database para que recupere los datos que
> transferir. Para lograr este acceso, se implementa el DMS en un Azure
> VNet. Entonces es responsible para conectar este VNet de forma segura
> a su database, por ejemplo, al usar un Site-to-Site VPN o una conexión
> ExpressRoute.

En este laboratorio, se simula un entorno ‘on-premises’ por un Hyper-V
host ejecutando en un Azure VM. Se implementa este VM al
‘smarthotelvnet’ VNet. Se implementará DMS a un VNet separado llamado
‘DMSVnet’. Para simular la conexión local, se han asomado estos dos
VNet.

1.  Navegue a **Azure portal**. En la casilla de global search,
    introduzca `SmartHotelHost` y seleccione el VM **SmartHotelHost**.

- ![](./media/image7.png)

2.  Seleccione **Connect**, elija **Connect** desde el drop-down.

- ![A screenshot of a computer Description automatically
  generated](./media/image8.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

3.  Seleccione **Download RDP File**.

- ![A screenshot of a computer Description automatically
  generated](./media/image9.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

4.  Haga clic en el botón **Keep** para la notificación y haga clic
    en **Open file** para conectar.

- ![A screenshot of a computer Description automatically
  generated](./media/image10.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

5.  **Conecte** a la máquina virtual con el username `demouser` y
    password `demo!pass123`

6.  Inicie **Chrome** desde el desktop shortcut.

7.  Navegue al Azure
    portal `https://portal.azure.com` busque `Azure database migration`,
    y luego seleccione **Azure Database Migration Services** desde el
    drop-down list.

- ![A screenshot of a computer Description automatically
  generated](./media/image11.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

8.  En el **Azure Database Migration Services** blade, seleccione
    + **Create**.

- ![A screenshot of a computer Description automatically
  generated](./media/image12.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

9.  Revise los detalles en la página **Select migration scenario and
    Database Migration Service** y haga clic en el botón **Select**

- ![A screenshot of a computer Description automatically
  generated](./media/image13.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

10. En la página Create Data Migration Service, pestaña Basics,
    proporcione los siguientes detalles.

    - Subscription – **Depth-@lab.CloudSubscription.Id**

    - Resource group: **SmartHotelRG**

    - Location – **West US**

    - Name: `SmartHotelDBMigration`

    - Haga clic en **Review + create**

- ![A screenshot of a data migration service Description automatically
  generated](./media/image14.png)

  Una captura de pantalla de un servicio de migración de datos
  Descripción generada automáticamente

11. En la pestaña **Review + create**, haga clic en el botón **Create**.

- ![A screenshot of a computer Description automatically
  generated](./media/image15.png)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

12. Se debe completar la implementación en unos segundos, haga clic en
    el botón **Go to resource**.

- ![A screenshot of a computer Description automatically
  generated](./media/image16.png)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

13. Seleccione **Integration runtime** en Settings, y haga clic
    en **Configure integration runtime.**

- ![A screenshot of a computer Description automatically
  generated](./media/image17.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

14. Haga clic en el enlace **Download and install the integration
    runtime** descargue el runtime en el **SmartHotelHost** VM

- ![A screenshot of a computer Description automatically
  generated](./media/image18.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

15. Haga clic en el **Download**

- ![A screenshot of a computer Description automatically
  generated](./media/image19.png)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

16. Elija la versión más reciente y haga clic en **Download**

- ![A screenshot of a computer Description automatically
  generated](./media/image20.png)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

17. Una vez descargado, instale el Integration runtime con las opciones
    predeterminadas

- ![A screenshot of a computer Description automatically
  generated](./media/image21.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

18. Se debe iniciar el **Microsoft Integration runtime Configuration
    manager** al hacer clic en el botón **Finish**.

19. Desde el Azure Portal, la pestaña **Configure integration
    runtime** copie el valor **Key 1**

- ![A screenshot of a computer Description automatically
  generated](./media/image22.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

20. Volviendo a **Microsoft Integration runtime Configuration
    manager** pegue el key copiado y haga clic en el botón **Register**.

- ![A screenshot of a computer Description automatically
  generated](./media/image23.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

21. Haga clic en el botón **Finish**

- ![A screenshot of a computer Description automatically
  generated](./media/image24.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

  ![A yellow rectangle with black text Description automatically
  generated](./media/image25.jpg)

  Un rectángulo amarillo con texto negro Descripción generada
  automáticamente

22. Una vez que se complete la inscripción, haga clic en el
    botón **Launch Configuration Manager**.

- ![A screenshot of a computer Description automatically
  generated](./media/image26.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

23. Revise los detalles en el **Microsoft Integration runtime
    Configuration manager**

- ![A screenshot of a computer Description automatically
  generated](./media/image27.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

24. Cambie al Azure portal y haga clic en Ok en la pestaña **Configure
    integration runtime**.

25. Se debe actualizar el Status a Online para el **Integration
    runtime**

- ![A screenshot of a computer Description automatically
  generated](./media/image28.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

### Tarea 3: Migre el On-premises SQL Database a Azure SQL Database

1.  Continuando en la página Azure Database Migration service,
    seleccione Overview y haga clic en el botón **New Migration** en la
    pestaña Getting started.

- ![A screenshot of a computer Description automatically
  generated](./media/image29.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

2.  En la página Select new migration scenario, proporcione los
    siguientes detalles

    - Source server type – **SQL Server**

    - Target server type – **Azure SQL Database**

- ![A screenshot of a computer Description automatically
  generated](./media/image30.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

3.  Haga clic en el botón **Select**

4.  En la página Azure SQL Database Offline Migration Wizard,
    proporcione los siguientes detalles en la pestaña **Connect to
    source SQL Server**.

    - Source server name: `192.168.0.6`

    - Authentication type: **SQL Authentication**

    - Username: `sa`

    - Password: `demo!pass123`

    - Connection properties – **habilite ambas casillas**

- ![A screenshot of a login Description automatically
  generated](./media/image31.jpg)

  Una captura de pantalla de una descripción de inicio de sesión
  generada automáticamente

5.  Haga clic en **Next: Select database for migration \>\>**

6.  En la pestaña **Select database for migration**, seleccione el
    SmartHotel.Registration database y haga clic en **Next: Connect to
    the target Azure SQL Database \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image32.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

7.  En la pestaña **Connect to the target Azure SQL Database** se debe
    poblar toda la información, puede revisar la información y luego
    proporcione la contraseña – `demo!pass123` y haga clic en **Next:
    Map source and target databases \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image33.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

8.  En la pestaña **Map source and target databases**, desde el
    drop-down de Target database seleccione **smarthoteldb** y haga clic
    en el **Next: Select database tables to migrate \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image34.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

9.  En la pestaña **Select database tables to migrate**, haga clic en el
    drop-down **SmartHotel.Registration tables selected 2/2** y asegure
    que \[dbo\].\[Bookings\] es la única tabla seleccionada y haga clic
    en **Next: Database migration summary \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image35.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

10. En la pestaña **Database migration summary**, revise los detalles y
    haga clic en el botón **Start migration**.

- ![A screenshot of a computer Description automatically
  generated](./media/image36.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

11. El Migration status se puede ver en la pestaña **Migration**

- ![A screenshot of a computer Description automatically
  generated](./media/image37.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

  > **Ojo: la migración tarda unos 10 minutos**

  ![A screenshot of a computer Description automatically
  generated](./media/image38.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

12. Haga clic en el botón **Refresh** un par de veces, hasta que el
    Migration status cambie a **Succeeded**.

- ![A screenshot of a computer Description automatically
  generated](./media/image39.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

13. Haga clic en el Source name, **192.168.0.6**

- ![A screenshot of a computer Description automatically
  generated](./media/image40.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

14. Revise los detalles de la migración

- ![A screenshot of a computer Description automatically
  generated](./media/image41.jpg)

  Una captura de pantalla de una computadora Descripción generada
  automáticamente

15. Hemos migrado de forma exitosa el On-premises SQL Database a Azure
    SQL Database.

### Resumen

En este laboratorio, hemos trabajado con Azure Database Migration
service e instale el integration runtime requerido en
el **SmartHotelHost** VM para poder migrar el on-premises database a
Azure SQL Database con el Database Migration Service (**DMS**).

![A screenshot of a computer Description automatically
generated](./media/image41.jpg)

Una captura de pantalla de una computadora Descripción generada
automáticamente
