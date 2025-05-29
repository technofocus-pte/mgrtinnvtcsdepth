# Laboratorio 01 – Implementar y verificar el entorno local y landing zone

## Obectivo

En el laboratorio, trabajemos con el **entorno local** (**on-premises
environment**) incluidos los siguientes

- Un Azure VM ejecutando nested Hyper-V, con 4 nested VMs

- **2** Resources groups con un total de **17** recursos que se
  requieren en los próximos ejercicios.

- Un **SmartHotel application**, lo que se ejecuta en nested VMs dentro
  de Hyper-V en el SmartHotelHost

- VNet Peering requerido después de la migración.

- Azure SQL database et cetera.

&nbsp;

- ![](./media/image1.jpg)

En **SmartHotelHostRG**, una máquina virtual ejecutando nested Hyper-V,
se crea con 4 nested VMs. Esto representa el entorno ‘on-premises’ lo
que va a evaluar y migrar en este laboratorio.

El **SmartHotel** application contiene 4 VMs alojados en Hyper-V:

- **Database tier** Alojado en el smarthotelSQL1 VM, lo que ejecuta en
  el Windows Server 2016 y el SQL Server 2017.

- **Application tier** Alojado en el smarthotelweb2 VM, lo que ejecuta
  en el Windows Server 2012R2.

- **Web tier** Alojado en el smarthotelweb1 VM, lo que ejecuta en el
  Windows Server 2012R2.

- **Web proxy** Alojado en el UbuntuWAF VM, lo que ejecuta en el Nginx
  en Ubuntu 18.04 LTS.

Para simplificar, no hay redundancia en cualquiera de estos tiers.

El otro grupo de recursos llamado SmartHotelRG contiene

Para evaluar el Hyper-V environment, usará **Azure Migrate: Server
Assessment**. Esto incluye implementar el **Azure Migrate appliance** en
el Hyper-V host para obtener información sobre el environment. Para un
análisis más profundo, se instalará **Microsoft Monitoring
Agent** y **Dependency Agent** en el VMs, habilitando el **Azure Migrate
dependency visualization**.

Se evaluará el **SQL Server database** al instalar el **Microsoft Data
Migration Assistant (DMA)** en el Hyper-V host, y usarlo para obtener
información sobre el database. Se completará **schema
migration** y **data migration** con el **Azure Database Migration
Service (DMS)**.

**Los tiers application, web, y web proxy** se migrarán al **Azure
VMs** mediante **Azure Migrate: Server Migration**. Echará un vistazo a
los pasos de construir el Azure environment, replicar los datos a Azure,
personalizar las configuraciones VM, y realizar un failover para migrar
la aplicación a Azure.

**Ojo:** Después de la migración, se puede modernizar la aplicación para
usar **Azure Application Gateway** en lugar de **Ubuntu Nginx VM**, y
para usar **Azure App Service** para alojar **web tier** y **application
tiers**. Estas optimizaciones no se pueden hacer en este laboratorio, lo
que se enfoca solo en la migración ‘lift and shift’ a Azure VMs.

![A diagram of a server Description automatically
generated](./media/image2.jpg)

Un diagrama de un server Descripción generado de forma automática

> **Ojo:** se usó un template para generar el On-premise environment
> mediante el launch, por eso el launch tardó alrededor de 7-10 minutos
> para implementarse. Una vez se complete el template deployment, se
> ejecutan varios scripts adicionales para arrancar el entorno del
> laboratorio. **Deje pasar al menos una hora desde el inicio del
> template deployment para que se ejecuten los scripts.**
>
> **Mientras se configura el On-premise environment espere por unos
> 30-40 minutos y proceda con Tarea 1.**

### Tarea 1: Verifique el on-premises environment

1.  En su Lab VM abra un navegador y navegue
    a `https://portal.azure.com`  con sus **Office 365 Tenant
    Credentials** desde el **Home/Resources tab** del interfaz del
    laboratorio.

2.  Seleccione `Resource group` en la página de inicio.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image3.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

3.  Seleccione **SmartHotelHostRG**.

- ![Graphical user interface, text, application Description
  automatically generated](./media/image4.png)

  Interfaz gráfica de usuario, texto, descripción de la aplicación
  generada automáticamente

4.  Seleccione **SmartHotelHost** VM que se implementó por el template
    en el módulo anterior.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image5.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

5.  Tome nota del **public IP address**.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image6.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

6.  Abra una pestaña del navegador y navegue a **Public IP of the
    SmartHotelHostVM** (notado en el paso anterior). Debe ver la
    aplicación **SmartHotel**, la que se ejecuta en nested VMs dentro de
    Hyper-V en el SmartHotelHost. (la aplicación no hace mucho: puede
    actualizar la página para ver la lista de guests o
    seleccione **‘CheckIn’** o **‘CheckOut’** para cambiar su estado.)

- ![Graphical user interface, application Description automatically
  generated](./media/image7.png)

  Interfaz gráfica de usuario, aplicación Descripción generada
  automáticamente

  > **Ojo:** Si **no** se ve **SmartHotel application**, espere unos 10
  > minutos e intentar de nuevo. Lleva **al menos una hora** desde el
  > inicio del template deployment. También puede averiguar el CPU,
  > network y disk activity levels para **SmartHotelHost** VM en el
  > Azure portal, para ver si sigue activo el aprovisionamiento.

Ha completado esta tarea. No cierre esta pestaña para seguir con la
siguiente tarea.

### Tarea 2: Verifique el landing zone environment

1.  Cambie a la pestaña **SmartHotelHost** VM y seleccione **Home**.

- ![Graphical user interface, application, email Description
  automatically generated](./media/image8.png)

  Interfaz gráfica de usuario, aplicación, email Descripción generada
  automáticamente

2.  Seleccione el **Resource Groups** service.

- ![Graphical user interface, application Description automatically
  generated](./media/image9.png)

  Interfaz gráfica de usuario, aplicación Descripción generada
  automáticamente

3.  Seleccione el **SmartHotelRG** resource group.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image10.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

4.  Note que están disponibles los **Virtual Network**, **Bastion
    resource**, **Application Gateway**, **SQL Server** y, **Database**.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image11.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

  ![Graphical user interface, text, application, email Description
  automatically generated](./media/image12.png)

  Interfaz gráfica de usuario, texto, aplicación, email Descripción
  generada automáticamente

### Resumen

Al final del laboratorio, deberíamos haber implementado correctamente la
plantilla de ARM y haber verificado el On-premises **Smart Hotel
Application** que debería estar en funcionamiento. Se debe
implementar **Azure Landing zone resource** que contiene Virtual
Network, Azure Bastion, Application Gateway, y un Azure SQL Server con
un Azure SQL Database.

**Smart Hotel Application**

![Graphical user interface, application Description automatically
generated](./media/image13.png)

Interfaz gráfica de usuario, aplicación Descripción generada
automáticamente

**Azure Landing zone** resource en el **SmartHotelRG**

![Graphical user interface, text, application, email Description
automatically generated](./media/image11.png)

Interfaz gráfica de usuario, texto, aplicación, email Descripción
generada automáticamente

![Graphical user interface, text, application, email Description
automatically generated](./media/image12.png)

Interfaz gráfica de usuario, texto, aplicación, email Descripción
generada automáticamente
