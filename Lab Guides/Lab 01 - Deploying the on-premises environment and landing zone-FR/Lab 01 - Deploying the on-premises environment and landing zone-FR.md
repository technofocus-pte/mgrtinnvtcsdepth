# Atelier 01 - Déploiement et vérification de l'environnement local et de la zone d'atterrissage

## Objectif

Dans l’Atelier, nous travaillerions avec l' **environnement local**, y
compris les éléments ci-dessous

- Une machine virtuelle Azure executant nested Hyper-V, avec 4 nested
  VMs

- **2** groupes de ressources avec un total de **17** ressources qui
  seraient nécessaires pour les exercices à venir.

- Une **application SmartHotel**, qui s'exécute sur des nested VMs dans
  Hyper-V sur SmartHotelHost

- Peering VNet requis après la migration.

- Azure SQL database, etc.

&nbsp;

- ![](./media/image1.jpg)

Dans **SmartHotelHostRG,** une machine virtuelle exécutant nested
Hyper-V, avec nested VMs est créée. Il s'agit de l'environnement ’ local
’ que vous allez évaluer et migrer au cours de cet atelier.

L'application **SmartHotel** comprend 4 VM hébergées dans Hyper-V :

- **Database tier**  **H**ébergé sur la machine virtuelle
  smarthotelSQL1, qui exécute Windows Server 2016 et SQL Server 2017.

- **Application tier**  Hébergé sur la machine virtuelle smarthotelweb2,
  qui exécute Windows Server 2012R2.

- **Web tier**  **H**ébergé sur la machine virtuelle smarthotelweb1, qui
  exécute Windows Server 2012R2.

- **Web proxy**  hébergé sur la machine virtuelle UbuntuWAF, qui exécute
  Nginx sur Ubuntu 18.04 LTS.

Pour simplifier, il n'y a pas de redondance dans l'un des niveaux.

L'autre groupe de ressources nommé SmartHotelRG comprend

Pour évaluer l'environnement Hyper-V, vous allez utiliser **Azure
Migrate: Server Assessment** Cela inclut le déploiement de **Azure
Migrate appliance**  sur Hyper-V host pour collecter des informations
sur l'environnement. Pour une analyse plus approfondie, **Microsoft
Monitoring Agent** et **Dependency Agent**  seront installés sur les
machines virtuelles, ce qui permettra de **Azure Migrate dependency
visualization**.

Le **SQL Server database**  est évaluée en installant le **Microsoft
Data Migration Assistant (DMA)** sur Hyper-V host et en l'utilisant pour
collecter des informations sur la base de données. **Schema
migration** et **data migration**  sera ensuite effectuée à l'aide d’
**Azure Database Migration Service (DMS).**

**The application, web, and web proxy tiers**  seront migrées vers
**Azure VMs**  à l'aide de **Azure Migrate: Server Migration**. Vous
allez passer en revue les étapes de création de l'environnement Azure,
de réplication des données sur Azure, de personnalisation des paramètres
de machine virtuelle et d'exécution d'un basculement pour migrer
l'application vers Azure.

**Remarque :** Après la migration, l'application peut être modernisée
pour utiliser **Azure Application Gateway** au lieu de le **Ubuntu Nginx
VM**, et pour utiliser **Azure App Service** pour héberger à la fois le
**web tier** et **application tiers** Ces optimisations sont hors du
champ d'application de cet atelier, qui se concentre uniquement sur une
migration ‘lift and shift’ vers des machines virtuelles Azure.

![Un schéma d'un serveur Description générée
automatiquement](./media/image2.jpg)

Un schéma d'un serveur Description générée automatiquement

> **Remarque :** Un modèle a été utilisé pour générer l'environnement
> local lors du lancement, le déploiement du lancement a donc pris
> environ 7 à 10 minutes. Une fois le déploiement du modèle terminé,
> plusieurs scripts supplémentaires sont exécutés pour bootstrap
> l'environnement de L’atelier. **Prévoyez au moins 1 heure à partir du
> début du déploiement du modèle pour que les scripts s'exécutent.**
>
> **Pendant la configuration de l'environnement locale, attendez 30 à 40
> minutes, puis passez à la tâche 1.**

### Tâche 1 : Vérifier l'environnement local

1.  Sur votre Lab VM ouvrez un navigateur et accédez à
    `https://portal.azure.com` connectez vous à l'aide des **Office 365
    Tenant Credential** à partir de **Home/Resources tab** de
    l'interface d’Ateleir.

2.  Sélectionnez **Resource group**  sur la page d'accueil.

- ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image3.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

3.  Sélectionnez **SmartHotelHostRG.**

- ![Interface utilisateur graphique, texte, application Description
  générée automatiquement](./media/image4.png)

  Interface utilisateur graphique, texte, application Description
  générée automatiquement

4.  Sélectionnez la VM **SmartHotelHost** qui a été déployée par le
    modèle dans le module précédent.

- ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image5.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

5.  Notez l'**adresse IP publique**.

- ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image6.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

6.  Ouvrez un onglet de navigateur et accédez à **Public IP of the
    SmartHotelHostVM**  (indiquée à l'étape précédente). Vous devez voir
    l' application **SmartHotel**, qui s'exécute sur des nested VMs dans
    Hyper-V sur SmartHotelHost. (L'application ne fait pas grand-chose :
    vous pouvez rafraîchir la page pour voir la liste des invités ou
    sélectionner **CheckIn’** or **‘CheckOut’**  pour basculer leur
    statut.)

- ![Interface utilisateur graphique, application Description générée
  automatiquement](./media/image7.png)

  Interface utilisateur graphique, application Description générée
  automatiquement

  > **Remarque :** Si **SmartHotel application**  **n'**est **pas**
  > affichée, attendez 10 minutes et réessayez. Il faut **au moins 1
  > heure** à partir du début du déploiement du modèle. Vous pouvez
  > également vérifier les niveaux d'activité du processeur, du réseau
  > et du disque pour la machine virtuelle **SmartHotelHost** dans le
  > portail Azure, pour voir si le provisionnement est toujours actif.

Vous avez terminé cette tâche. Ne fermez pas cet onglet pour passer à la
tâche suivante.

### Tâche 2 : Vérifier l'environnement de la zone d'atterrissage

1.  Revenez à l' onglet **SmartHotelHost** VM et sélectionnez **Home**.

- ![Interface utilisateur graphique, application, e-mail Description
  générée automatiquement](./media/image8.png)

  Interface utilisateur graphique, application, e-mail Description
  générée automatiquement

2.  Sélectionnez **Resource Groups**  de ressources.

- ![Interface utilisateur graphique, application Description générée
  automatiquement](./media/image9.png)

  Interface utilisateur graphique, application Description générée
  automatiquement

3.  Sélectionnez le groupe de ressources **SmartHotelRG**.

- ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image10.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

4.  Notez que le **Virtual Network**, **Bastion
    resource**, **Application Gateway**, **SQL Server**  et la
    **Database** sont disponibles.

- ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image11.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

  ![Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement](./media/image12.png)

  Interface utilisateur graphique, texte, application, e-mail
  Description générée automatiquement

### Résumé

À la fin du laboratoire, nous devrions avoir déployé avec succès le
modèle ARM et vérifié **Smart Hotel Application**  sur site qui devrait
être opérationnelle. **Azure Landing zone resource**  doit être
déployée, qui comprend un réseau virtuel, Azure Bastion, Application
Gateway et un Azure SQL Server avec un Azure SQL Database

**Smart Hotel Application**

![Interface utilisateur graphique, application Description générée
automatiquement](./media/image13.png)

Interface utilisateur graphique, application Description générée
automatiquement

La ressource de **Azure Landing zone**  dans le **SmartHotelRG**

![Interface utilisateur graphique, texte, application, e-mail
Description générée automatiquement](./media/image11.png)

Interface utilisateur graphique, texte, application, e-mail Description
générée automatiquement

![Interface utilisateur graphique, texte, application, e-mail
Description générée automatiquement](./media/image12.png)

Interface utilisateur graphique, texte, application, e-mail Description
générée automatiquement
