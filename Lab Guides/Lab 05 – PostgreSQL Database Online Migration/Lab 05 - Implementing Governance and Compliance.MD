# Lab 05 – PostgreSQL Database Online Migration

## Objective:

In this Lab we would utilize the Cloud Adoption Framework Adopt
methodology to migrate on-premises **PostgreSQL Database** using the **Azure
Database for Flexible Server (Migration)** feature to migrate the
PostgreSQL Database.

The migration Service in Azure Database for PostgreSQL is a fully
managed service, which supports both schema and data migrations,
ensuring a complete and accurate transfer of your entire database
environment to Azure.

![A diagram of a computer server Description automatically generated
with medium confidence](./media/image1.jpeg)


## Exercise 1 - Creating the PostgreSQL database for the On-premises environment.

### Task 1 – Deploy the Virtual machine to host the PostgreSQL database for the On-premises environment.

We will deploy **Ubuntu 22.0.4.4 LTS** VM, on which we will install
PostgreSQL Server 16 and then create the Sample Database that will be
used for Migration.

1.  From the Azure Portal open the Azure Cloud Shell

    ![A screenshot of a computer Description automatically
generated](./media/image31a.jpeg)

2.  Click on the **PowerShell** button.

    ![A screenshot of a computer error Description automatically
generated](./media/image32a.jpeg)

3.  On the **Getting started** window, choose the radio button
    for **Mount storage account** and then select the **Depth-lod@lab.LabInstance.Id** subscription and then click on the **Apply** button.

    ![A screenshot of a computer Description automatically
generated](./media/image33a.png)

4.  On the Mount storage account window, choose the **We will create
    storage account for you** radio button and then click on **Next**.

    ![A screenshot of a computer account Description automatically
generated](./media/image34a.jpeg)

5.  Wait for the deployment to complete

    ![A computer screen shot of a computer program Description automatically
generated](./media/image35a.png)

6.  In the Cloud Shell PowerShell window type the below commands to
    configure variables and create the VM that will be used for
    installing PostgreSQL server.

    `$cred = Get-Credential`

7.  When prompted to enter credentials provide the below

    - User – `postgres`

    - Password - `P@55w.rd1234`

    ![A screenshot of a computer Description automatically
generated](./media/image36a.jpeg)

8.  Enter the below command to create the Resource group

    `New-AzResourceGroup -ResourceGroupName "PostgresRG" -Location
    "WestUS"`

    ![A blue screen with white text Description automatically
generated](./media/image37a.jpeg)

9.  Enter the below command to deploy the **Ubuntu 22.0.4.4 LTS** VM


    # 
        New-AzVm `
            -ResourceGroupName "PostgresRG" `
            -Name "PostgresSrv" `
            -Location "WestUS" `
            -VirtualNetworkName "PGVnet" `
            -SubnetName "PGSubnet" `
            -SecurityGroupName "PostgresNSG" `
            -Securitytype "Standard" `
            -PublicIpAddressName "PostgresSrvIP" `
            -ImageName "Canonical:0001-com-ubuntu-server-jammy:22_04-lts-gen2:latest" `
            -Credential $cred `
            -Size "Standard_b2ms"

    ![A blue screen with white text Description automatically
generated](./media/image39a.jpeg)

10. Once the deployment is completed it will show the below

    ![A blue screen with white text Description automatically
generated](./media/image40a.jpeg)

11. Run the below command to connect to the Ubuntu VM, substitute the
    command using the **FullyQualifiedDomainName** from the output of
    the previous command

    `ssh postgres@FullyQualifiedDomainName`

    ![A screen shot of a computer Description automatically
generated](./media/image41a.jpeg)

12. When prompted to continue type **yes** and then enter the password
    provided in during the deployment - `P@55w.rd1234`

13. It should successfully connect to the Ubuntu Server

    ![A blue screen with white text Description automatically generated](./media/image42.jpeg)

14. Now we will install PostgreSQL ver. 16 on the Ubuntu VM, we will set
    the automated repository configuration by running the below command

    `sudo apt install -y postgresql-common`

    `sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh`

    ![A screenshot of a computer Description automatically
generated](./media/image43.jpeg)

    ![A blue screen with white text Description automatically
generated](./media/image44.jpeg)

15. Press the Enter key on keyboard to continue.

    ![A computer screen with white text Description automatically
generated](./media/image45.jpeg)

    ![A computer screen with white text Description automatically
generated](./media/image46.jpeg)

16. We will import the repository signing key by running the below
    commands.

    `sudo apt install curl ca-certificates`

    `sudo install -d /usr/share/postgresql-common/pgdg`

    `sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc`

    ![A screenshot of a computer Description automatically
generated](./media/image47.jpeg)

17. We will run the below command to create the repository configuration
    file

    `sudo apt update`

    `sudo apt install gnupg2 wget`

    `sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'`

    `curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg`

    ![](./media/image48.jpeg)

18. We will run the below command to update the package lists

    `sudo apt update`

    ![A computer screen with white text Description automatically
generated](./media/image49.jpeg)

19. We will run the below command to install the latest version of
    PostgreSQL

    `sudo apt install postgresql-16 postgresql-contrib-16`

    ![A blue screen with white text Description automatically
generated](./media/image50.jpeg)

    > **Note** – The installation should complete in 1-2 minutes

    ![A blue screen with white text Description automatically
generated](./media/image51.jpeg)

    ![A blue screen with white text Description automatically
generated](./media/image52.jpeg)

20. Once the installation is completed, type the below command to launch
    the PSQL utility

    `psql`

    ![A blue background with white text Description automatically
generated](./media/image53.jpeg)

21. We will set the password for the postgres account in psql

    `\password postgres`

22. Provide the password as **postgres** Enter it again as **postgres**

    ![A computer screen with text and numbers Description automatically
generated](./media/image54.jpeg)

23. Now we will set the network and other permissions to all the
    PostgreSQL to be accessed remotely

24. Run the below command to access the postgresql.conf file

    `\q`

    `sudo nano /etc/postgresql/16/main/postgresql.conf`

25. Once the file is opened scroll down and update the setting to match
    the below

    - Under Connection Settings remove the \# and change
      listen_addresses = '\*'

    ![A screenshot of a computer program Description automatically
generated](./media/image55.jpeg)

    - Under WRITE-AHEAD LOG remove the \# and change wal_level = logical

    ![A computer screen shot of a blue screen Description automatically
generated](./media/image56.jpeg)

26. Once the above change is done press **Ctrl + X**

    ![A screenshot of a computer Description automatically
generated](./media/image57.jpeg)

27. Press type **Y** and enter to confirm.

28. Run the below command to access the **pg_hba.conf** file

    `sudo nano /etc/postgresql/16/main/pg_hba.conf`

29. Once the file is opened scroll down add the below lines at the
    bottom of the file

    `host all all 0.0.0.0/0 md5`

    `host all all ::/0 md5`

    ![A screenshot of a computer Description automatically
generated](./media/image58.jpeg)

30. Once the above change is done press **Ctrl + X**

    ![A blue screen with white text Description automatically
generated](./media/image59.jpeg)

31. Press type **Y** and enter to confirm.

32. Run the below command to restart the PostgreSQL service

    `sudo service postgresql restart`

    ![A blue screen with white text Description automatically
generated](./media/image60.jpeg)

33. On the Azure Portal, search and select Resource groups

    ![A screenshot of a computer Description automatically
generated](./media/image61.jpeg)

34. From the list of Resource groups select PostgresRG, then select the
    VM - **PostgresSrv**

35. On the **PostgresSrv** page, select **Networking setting** and then
    click on **+ Create port rule** and choose **Inbound port Rule**

    ![A screenshot of a computer Description automatically
generated](./media/image62.jpeg)

36. On the **Add inbound security rule** page, under service from the
    drop-down choose **PostgreSQL** and then click on
    the **Add** button.

    ![A screenshot of a computer Description automatically
generated](./media/image63.jpeg)

37. You should receive the notification as shown in below image.

    ![A screenshot of a computer Description automatically
generated](./media/image64.jpeg)

38. Now the PostgreSQL server is ready to be accessed remotely.

### Task 5 – Create a PostgreSQL database for the On-premises environment.

1.  Now we will import a sample database into the PostgreSQL server
    which we will use for Migration

2.  There are 15 tables in the DVD Rental database

    ![A diagram of a computer generated diagram Description automatically
generated with medium confidence](./media/image65.jpeg)

3.  From the Azure Portal open the **Azure Cloud Shell**

    ![A screenshot of a computer Description automatically
generated](./media/image31.jpeg)

4.  Ensure the Cloud Shell has launched with Bash, then run the below
    command to connect to the PostgresSrv VM

    `ssh postgres@`**ServerDNSName**

    ![A screenshot of a computer Description automatically
generated](./media/image66.jpeg)

5.  When prompted to continue type yes and then enter the password
    provided in during the deployment - **P@55w.rd1234**

6.  It should successfully connect to the Ubuntu Server

    ![A screenshot of a computer Description automatically
generated](./media/image67.jpeg)

7.  On the prompt postgres@PostgresSrv run the below command to create a
    folder to copy the file to be used for restoring the database.

    `mkdir dvdrentalbkp`

    ![](./media/image68.jpeg)

8.  On the Lab VM, right-click on the Start menu and **select Windows
    Terminal (admin)**

    ![A screenshot of a computer Description automatically
generated](./media/image69.jpeg)

9.  In the Windows PowerShell window run the command to copy the
    PostgreSQL database backup to the folder dvdrentalbkp on the
    PostgresSrv.

    `scp "C:\Labfiles\dvdrental.tar"
    postgres@YOURPOSTGRESDNSNAME:"dvdrentalbkp"`

    When prompted to continue type yes and then enter the password
    - P@55w.rd1234

    ![A computer screen with a black background Description automatically
generated](./media/image70.jpeg)

10. Switch back to the tab on the prompt postgres@PostgresSrv run the
    below command to launch PSQL

    `psql`

    ![A black screen with white text Description automatically
generated](./media/image71.jpeg)

    On the psql prompt run the below command to create a database

    `CREATE DATABASE dvdrental;`

    ![A black background with yellow text Description automatically
generated](./media/image72.jpeg)

    `\q`

    ![A black background with green text Description automatically
generated](./media/image73.jpeg)

11. Back on postgres@PostgresSrv prompt type the below command to
    restore the backup into the newly created database.

    `cd dvdrentalbkp`

    `pg_restore -U postgres -d dvdrental "dvdrental.tar"`

    ![](./media/image74.jpeg)

**Note –** if any error or warning message appear, they can be safely
ignored, and the blank database is updated with 15 Tables.

12. We can check the database details by running the below commands

    `psql`

    `\c dvdrental`

    ![A computer screen with white text and red text Description
automatically generated](./media/image75.jpeg)

    `\dt`

    ![A screenshot of a computer Description automatically
generated](./media/image76.jpeg)


## Exercise 2 - Migrating PostgreSQL database to Azure Database for PostgreSQL Flexible Server

### Task 1 – Create an Azure Database for PostgreSQL flexible Server

1.  While on the PostgresSrv VM open the Edge browser navigate to the
    Azure Portal `https://portal.azure.com`

2.  Search for postgres and choose `Azure Database for PostgreSQL
    flexible Servers`

    ![A screenshot of a computer Description automatically
generated](./media/image2.jpeg)

3.  Click on **+ Create**

    ![A screenshot of a computer Description automatically
generated](./media/image3.jpeg)

4.  On the New Azure Database for PostgreSQL Flexible Server page on the
    Basics tab, provide the below details

    -  Resource group – Click on Create new and provide name
        – `RG4AzPGDb`

    -  Server name - `ad4pfssrvXXXXX` substitute XXXXX with random
        number

    -  Region – **West US**

    -  PostgreSQL version – **16**

    -  Workload type – **Production**

    ![A screenshot of a computer Description automatically
generated](./media/image4.jpeg)

    - High availability - **Disabled**

    - Authentication method – **PostgreSQL Authentication only**

    - Admin username – `postgres`

    - Password – `P@55w.rd1234`

    - Confirm password – `P@55w.rd1234`

    - Click on **Next: Networking**

    ![A screenshot of a computer Description automatically
generated](./media/image5.jpeg)

5.  On the **Networking** tab, enable the check box for **Allow public
    access from any Azure services within Azure to this server** and
    click on **+ Add Client IP address** also add the **Public IP address**
    of the PostgresSrv and then click on **Review + create** button.

    ![A screenshot of a computer Description automatically
generated](./media/image6.jpeg)

    ![A screenshot of a computer Description automatically
generated](./media/image7.jpeg)

6.  Review the details and click on the **Create** button.

    ![A screenshot of a computer Description automatically
generated](./media/image8.jpeg)

7.  The Deployment will start.

    ![A screenshot of a computer Description automatically
generated](./media/image9.jpeg)

8.  Once the Deployment is completed click on **Go to resource** button.

    ![A screenshot of a computer Description automatically
generated](./media/image10.jpeg)

### Task 2 – Migrate the PostgreSQL database to Azure Database for PostgreSQL flexible server (Migration)

1.  The **Overview** page of the **Azure Database for PostgreSQL
    flexible server** should open

    ![A screenshot of a web page Description automatically
generated](./media/image11.jpeg)

2.  Review the overview page and check out the various tabs

    ![A screenshot of a computer Description automatically
generated](./media/image12.jpeg)

3.  Under **Settings** select **Databases**, you should be able to see 3
    Databases listed.

    ![A screenshot of a computer Description automatically
generated](./media/image13.jpeg)

4.  Click on **Migration** and then select **+ Create** button.

    ![A screenshot of a computer Description automatically
generated](./media/image14.jpeg)

5.  On the **Migrate PostgreSQL to Azure Database for PostgreSQL
    Flexible Server** page on the Setup tab, provide the below
    information and then click on **Next: Select Runtime Server**

    - Migration name - `PostgreSQLToAzurePG`

    - Source server – **On-premise Server**

    - Migration option – **Validate and Migrate**

    - Migration mode – **Online**

    ![A screenshot of a computer Description automatically
generated](./media/image15.jpeg)

6.  On the **Select Runtime Server** tab click on **Next: Connect to
    source\**

    ![A screenshot of a computer Description automatically
generated](./media/image16.jpeg)

7.  On the Connect to source tab, provide the below details and click on
    Next : Select migration target\a. Server name – **IP address / DNS
    name of PostgresSrv VM**

    -  Port – `5432`

    -  Server admin login name - `postgres`

    -  Password – `postgres`

    -  SSL mode – **Prefer**

    -  Test Connection – Click on **Connect to source**  [wait for the test
        connection to be Successful]

    ![A screenshot of a computer Description automatically
generated](./media/image17.jpeg)

8.  On the **Select migration target** tab, provide the below details

    -  Password - `P@55w.rd1234`

    -  Test Connection – Click on **Connect to target** [wait for the test
        connection to be Successful]

3.  Click on **Next : Select database(s) for migration**

    ![A screenshot of a computer Description automatically
generated](./media/image18.jpeg)

9.  On the **Select database(s) for migration** tab, select the Database
    – **dvdrental** and then click on **Next: Summary**

    ![A screenshot of a computer Description automatically
generated](./media/image19.jpeg)

10. On the **Summary** tan, review the information shown and click on
    the **Start Validation and Migration** button.

    ![A screenshot of a computer Description automatically
generated](./media/image20.jpeg)

11. On the Migration page, click on the **PostgreSQLToAzurePG** link

    ![A screenshot of a computer Description automatically
generated](./media/image21.jpeg)

12. On the **PostgreSQLToAzurePG** page, click on the refresh button to
    see the updates.

    ![A screenshot of a computer Description automatically
generated](./media/image22.jpeg)

13. Click on the Database name **dvdrental**

    ![A screenshot of a computer Description automatically
generated](./media/image23.jpeg)

14. On the **Validation** tab you should be able to see the Validation
    tasks details.

    ![](./media/image24.jpeg)

    <font color=darkred>

    * > Note - If the **LanguageValidation - failed** error appear, you can try to re-initiate migration again, if you are still facing then error, then please refer to following steps to refer how the migration could have been completed. 
    
        >You can **proceed/skip to the next Lab**.

    </font>

    ![](./media/image24a.png)


15. On the **Migration** tab, it will show the migration status queued.

    ![A screenshot of a computer Description automatically
generated](./media/image25.jpeg)


16. On the **PostgreSQLToAzurePG** page, click on the refresh button
    again and notice that Migration tasks are also completed and now it
    will **Waiting for User Action**, click on the **Cutover** button.

    ![A screenshot of a computer Description automatically
generated](./media/image26.jpeg)

17. When prompted **Please perform the following steps manually before
    doing the cutover**, click on the **Yes** button

    ![A screenshot of a computer Description automatically
generated](./media/image27.jpeg)

    ![A white background with black text Description automatically
generated](./media/image28.jpeg)

18. On the **PostgreSQLToAzurePG** page, click on the refresh button
    again, Cutover in progress status should appear under **Migration
    details**.

    ![A screenshot of a computer Description automatically
generated](./media/image29.jpeg)

19. Once the Cutover is **Completed**, close
    the **PostgreSQLToAzurePG** blade.

    ![A screenshot of a computer Description automatically
generated](./media/image30.jpeg)

20. Back on the Migration page, we can see that the Migration of the
    PostgreSQL database is completed.

    ![A screenshot of a computer Description automatically
generated](./media/image31.jpeg)

21. Select **Databases** under Settings, select the **dvdrental** and
    click on the **Connect** button

    ![A screenshot of a computer Description automatically
generated](./media/image32.jpeg)

22. When the Cloud Shell opens it will prompt for Password, provide the
    password as `P@55w.rd1234`

    ![A screenshot of a computer Description automatically
generated](./media/image33.jpeg)

23. Once it is successfully connected to the database, it will be listed
    as **devrental=\**

24. Run the below command to list the tables in the Target Database

    `\dt`

    ![A screenshot of a computer Description automatically
generated](./media/image34.jpeg)

    > **Note –** These tables are the same as in the Source database.

    ![A diagram of a computer generated diagram Description automatically
generated with medium confidence](./media/image35.jpeg)

Hence, we have successfully migrated the On-premises PostgreSQL database
to Azure Database for PostgreSQL Flexible Server.
