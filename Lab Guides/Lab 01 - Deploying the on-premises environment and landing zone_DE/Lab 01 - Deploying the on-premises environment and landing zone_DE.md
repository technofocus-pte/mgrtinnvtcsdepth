# Lab 01 – Bereitstellen und Überprüfen der on-premises Umgebung und Zielzone

## Ziele

Im Lab arbeiten wir mit der **on-premises Umgebung,** einschließlich der
folgenden

- Eine Azure-VM mit geschachtelten Hyper-V, mit 4 geschachtelten VMs

- **2** Ressourcengruppen mit insgesamt **17** Ressourcen, die in den
  kommenden Übungen benötigt werden.

- Eine **SmartHotel-Anwendung**, die auf geschachtelten VMs in Hyper-V
  auf dem SmartHotelHost ausgeführt wird

- VNet-Peering ist nach der Migration erforderlich.

- Azure SQL-Datenbank usw.

&nbsp;

- ![](./media/image1.jpg)

In **SmartHotelHostRG** wird ein virtueller Maschine erstellt, auf dem
geschachteltes Hyper-V mit 4 geschachtelten VMs ausgeführt wird. Dies
stellt die " on-premises" Umgebung dar, die Sie während dieser Übung
bewerten und migrieren werden.

Die **SmartHotel-Anwendung** umfasst 4 VMs, die in Hyper-V gehostet
werden:

- **Database tier** : Gehostet auf der smarthotelSQL1-VM, auf der
  Windows Server 2016 und SQL Server 2017 ausgeführt werden.

- **Application tier** : Gehostet auf der smarthotelweb2-VM, auf der
  Windows Server 2012R2 ausgeführt wird.

- **Web tier**  Gehostet auf der VM smarthotelweb1, auf der Windows
  Server 2012R2 ausgeführt wird.

- **Webproxy** Gehostet auf der UbuntuWAF-VM, auf der Nginx unter Ubuntu
  18.04 LTS ausgeführt wird.

Der Einfachheit halber, gibt es in keiner der Ebenen Redundanz.

Die andere Ressourcengruppe mit dem Namen SmartHotelRG besteht aus

Zum Bewerten der Hyper-V-Umgebung verwenden Sie **Azure Migrate: Server
Assessment**. Dazu gehört die Bereitstellung der **Azure Migrate
Appliance**  auf dem Hyper-V-Host, um Informationen über die Umgebung zu
sammeln. Für eine tiefergehende Analyse werden der **Microsoft
Monitoring Agent** und der **Dependency-Agent** auf den VMs installiert,
wodurch die **Azure Migrate dependency visualization** aktiviert wird.

Die **SQL Server database** wird bewertet, indem der **Microsoft Data
Migration Assistant (DMA)** auf dem Hyper-V-Host installiert und zum
Sammeln von Informationen über die Datenbank verwendet wird. **Schema
migration** und **data migration** werden dann mit dem **Azure Database
Migration Service (DMS)** abgeschlossen.

**The application, web, und web proxy tiers**  **werden** mithilfe von
**Azure Migrate: Server Migration** zu **Azure-VMs** migriert. Sie
führen Sie durch die Schritte zum Erstellen der Azure-Umgebung, zum
Replizieren von Daten in Azure, zum Anpassen der VM-Einstellungen und
zum Ausführen eines Failovers, um die Anwendung zu Azure zu migrieren.

**Hinweis**: Nach der Migration kann die Anwendung so modernisiert
werden, dass **Azure Application Gateway** anstelle der **Ubuntu
Nginx-VM** verwendet wird und **Azure App Service** zum Hosten sowohl
der **web tier** als auch der **application tiers** verwendet wird.
Diese Optimierungen liegen außerhalb des Geltungsbereichs dieses Labs,
das sich nur auf eine Lift & Shift-Migration zu Azure-VMs konzentriert.

![Ein Diagramm eines Servers Beschreibung wird automatisch
generiert](./media/image2.jpg)

Ein Diagramm eines Servers Beschreibung wird automatisch generiert

> **Hinweis:** Eine Vorlage wurde verwendet, um die on-premises Umgebung
> während des Starts zu generieren, daher dauerte die Bereitstellung
> etwa 7-10 Minuten. Sobald die Bereitstellung der Vorlage abgeschlossen
> ist, werden mehrere zusätzliche Skripte ausgeführt, um die Lab
> umgebung zu booten. **Erlauben Sie den Skripten mindestens 1 Stunde ab
> dem Beginn der Vorlagenbereitstellung, um ausgeführt zu werden.**
>
> **Warten Sie während der Einrichtung der On-Premise-Umgebung 30 bis 40
> Minuten und fahren Sie dann mit Aufgabe 1 fort.**

### Aufgabe 1: Überprüfung von on-premises Umgebungen 

1.  Öffnen Sie auf Ihrer Lab-VM einen Browser, und navigieren Sie zu
    `https://portal.azure.com`  Anmeldung mit den **Office
    365-Mandantenanmeldeinformationen** auf der **Registerkarte
    Home/Resources** der Lab-Benutzeroberfläche.

2.  Wählen Sie auf der Startseite **Resource group** aus.

- ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image3.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

3.  Wählen Sie **SmartHotelHostRG** aus.

- ![Grafische Benutzeroberfläche, Text, Anwendung Beschreibung wird
  automatisch generiert](./media/image4.png)

  Grafische Benutzeroberfläche, Text, Anwendung Beschreibung wird
  automatisch generiert

4.  Wählen Sie **SmartHotelHost** VM aus, die von der Vorlage im
    vorherigen Modul bereitgestellt wurde.

- ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image5.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

5.  Notieren Sie sich **public IP address**.

- ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image6.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

6.  Öffnen Sie eine Browser Registerkarte, und navigieren Sie zu
    **Public IP of the SmartHotelHostVM** (im vorherigen Schritt
    notiert). Die SmartHotel-Anwendung, die auf geschachtelten VMs in
    Hyper-V auf dem SmartHotelHost ausgeführt wird, sollte angezeigt
    werden. (Die Anwendung macht nicht viel: Sie können die Seite
    aktualisieren, um die Liste der Gäste anzuzeigen, oder auswählen
    **'CheckIn'** oder **'CheckOut'**, um ihren Status umzuschalten.)

- ![Grafische Benutzeroberfläche, Anwendung Beschreibung wird
  automatisch generiert](./media/image7.png)

  Grafische Benutzeroberfläche, Anwendung Beschreibung wird automatisch
  generiert

  > **Hinweis:** Wenn die **SmartHotel-Anwendung** **nicht** angezeigt
  > wird, warten Sie 10 Minuten und versuchen Sie es erneut. Es dauert
  > **mindestens 1 Stunde** ab dem Start der Vorlagenbereitstellung. Sie
  > können auch die CPU-, Netzwerk- und Datenträgeraktivitätsebenen für
  > die **SmartHotelHost-VM** im Azure-Portal überprüfen, um
  > festzustellen, ob die Bereitstellung noch aktiv ist.

Sie haben diese Aufgabe abgeschlossen. Schließen Sie diese Registerkarte
nicht, um mit der nächsten Aufgabe fortzufahren.

### Aufgabe 2: Überprüfen Sie die Umgebung der Zielzone

1.  Wechseln Sie zurück zur Registerkarte **SmartHotelHost** VM, und
    wählen Sie **Home** aus.

- ![Grafische Benutzeroberfläche, Anwendung, E-Mail Beschreibung wird
  automatisch generiert](./media/image8.png)

  Grafische Benutzeroberfläche, Anwendung, E-Mail Beschreibung wird
  automatisch generiert

2.  Wählen Sie **Resource Groups** aus.

- ![Grafische Benutzeroberfläche, Anwendung Beschreibung wird
  automatisch generiert](./media/image9.png)

  Grafische Benutzeroberfläche, Anwendung Beschreibung wird automatisch
  generiert

3.  Wählen Sie **SmartHotelRG** resource group aus.

- ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image10.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

4.  Beachten Sie, dass das **virtuelle Netzwerk**, die
    **Bastion-Ressource**, **Application Gateway**, **SQL Server** und
    die **Datenbank** verfügbar sind.

- ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image11.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

  ![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert](./media/image12.png)

  Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
  wird automatisch generiert

### Zusammenfassung

Am Ende des Labs sollten wir die ARM-Vorlage erfolgreich bereitgestellt
und die on-premises **Smart Hotel Application** überprüft haben, die
betriebsbereit sein sollte. Die **Azure Landing zone resource** sollte
bereitgestellt werden, die aus einem virtuellen Netzwerk, Azure Bastion,
Application Gateway und einem Azure SQL Server mit einer Azure
SQL-Datenbank besteht.

**Smart Hotel Application**

![Grafische Benutzeroberfläche, Anwendung Beschreibung wird automatisch
generiert](./media/image13.png)

Grafische Benutzeroberfläche, Anwendung Beschreibung wird automatisch
generiert

**Azure Landing zone** in der **SmartHotelRG**

![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
wird automatisch generiert](./media/image11.png)

Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung wird
automatisch generiert

![Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung
wird automatisch generiert](./media/image12.png)

Grafische Benutzeroberfläche, Text, Anwendung, E-Mail Beschreibung wird
automatisch generiert
