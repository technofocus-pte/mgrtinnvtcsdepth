# Lab 06 – PostgreSQL Database Online Migration

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

## Exercise 1 - Migrating PostgreSQL database to Azure Database for PostgreSQL Flexible Server

### Task 1 – Create an Azure Database for PostgreSQL flexible Server

1.  While on the PostgresSrv VM open the Edge browser navigate to the
    Azure Portal `https://portal.azure.com`

2.  Search for postgres and choose **Azure Database for PostgreSQL
    flexible Servers**

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
    information and then click on **Next: Select Runtime Server\**

    - Migration name - PostgreSQLToAzurePG

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

    ![A screenshot of a computer Description automatically
generated](./media/image24.jpeg)

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

### Task 3: Stop on-premises environment VMs

1.  Select **Resource group** on home page.

    ![A screenshot of a computer Description automatically
generated](./media/image36.jpeg)

2.  Select **SmartHotelHostRG** and then select **SmartHotelHost VM**.

    ![A screenshot of a computer Description automatically
generated](./media/image37.jpeg)

3.  Select **SmartHotelHost VM**.

    ![A screenshot of a computer Description automatically
generated](./media/image38.jpeg)

4.  Stop the VM if you would not be doing the next Labs the same day,
    this is to ensure we have sufficient credits to complete all Labs.

    ![A screenshot of a computer Description automatically
generated](./media/image39.jpeg)

    ![A screenshot of a computer Description automatically
generated](./media/image40.jpeg)

    ![A screenshot of a phone Description automatically
generated](./media/image41.jpeg)

> **Note** -- The SmartHotelHost VM is stopped as it is a Standard D8s VM
which will consume Azure Pass credits if it is left running, which may
result in insufficient credits to complete the remaining lab.
