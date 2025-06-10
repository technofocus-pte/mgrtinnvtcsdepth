# 实验 04 - 将应用程序数据库从本地环境迁移到 Azure 

## 目的

在本实验室中，我们将利用云采用框架采用方法，使用 Azure
数据库迁移服务迁移本地数据库来迁移 SQL 数据库。Azure
数据库迁移服务是一种工具，可帮助你简化、指导和自动化数据库到 Azure
的迁移。轻松地将数据、架构和对象从多个来源大规模迁移到云中。

![A diagram of a cloud server Description automatically generated with
medium confidence](./media/image1.jpg)

以中等置信度自动生成的云服务器描述

> **注意：**如果您在上一个实验后停止了虚拟机，请启动它们。

## 练习 1 - 将 Microsoft SQL 数据库迁移到 Azure SQL 数据库

### 任务 1：注册 Microsoft.DataMigration 资源提供程序

在使用 Azure 数据库迁移服务之前，必须在目标订阅中注册资源提供程序
**Microsoft.DataMigration**。

1.  导航到 `https://shell.azure.com` 打开 Azure Cloud
    Shell。如果系统提示，请使用 Azure 订阅凭据登录，选择 PowerShell
    会话，并接受任何提示。

- ![A screenshot of a computer Description automatically
  generated](./media/image2.jpg)

  自动生成的计算机 Description 的屏幕截图

2.  在 **Get started** （开始） 窗口中，选择 **Mount storage account**
    （挂载存储帐户），然后选择相应的订阅，然后单击 **Apply** （应用）
    按钮。

- ![alt text](./media/image3.png)

  替换文本

3.  在 **Mount storage account** （挂载存储帐户） 窗口中，选择 **We will
    create a storage account for you**
    （我们将为您创建存储帐户），然后单击 **Next** （下一步） 按钮。

- ![alt text](./media/image4.png)

  替换文本

4.  等待部署完成。

5.  执行以下命令，注册 **Microsoft.DataMigration** 资源提供程序。

- `Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration`

  > **注意：**资源提供程序可能需要几分钟时间才能完成注册。您可以继续执行下一个任务，而无需等待注册完成。在任务
  > 3 之前，您不会使用资源提供程序。

  ![A screenshot of a computer Description automatically
  generated](./media/image5.jpg)

  自动生成的计算机 Description 的屏幕截图

6.  您可以通过运行以下命令来检查状态：

- `Get-AzResourceProvider -ProviderNamespace Microsoft.DataMigration | Select-Object ProviderNamespace, RegistrationState, ResourceTypes`

  ![A screenshot of a computer Description automatically
  generated](./media/image6.jpg)

  自动生成的计算机 Description 的屏幕截图

您已完成此任务。请勿关闭任何窗口，请继续执行下一个任务。

**任务摘要**

在此任务中，您向订阅注册了 **Microsoft.DataMigration**
资源提供程序。这使此订阅能够使用 Azure 数据库迁移服务。

### 任务 2：创建 Database Migration Service 

在此任务中，您将创建一个 Azure 数据库迁移服务资源。此资源由您在任务 1
中注册的 **Microsoft.DataMigration 资源**提供程序管理。

> **注意：**Azure Database Migrate 服务 （DMS）
> 需要对本地数据库进行网络访问，以检索要传输的数据。为了实现此访问，DMS
> 将部署到 Azure VNet 中。然后，你负责将该 VNet
> 安全地连接到数据库，例如，使用 Site-to-Site VPN 或 ExpressRoute 连接。

在本实验中， “本地” 环境由在 Azure VM 中运行的 Hyper-V 主机模拟。此 VM
将部署到 “smarthotelvnet” VNet。DMS 将部署到名为“DMSVnet”的单独
VNet。为了模拟本地连接，这两个 VNet 已对等互连。

1.  导航到 **Azure 门户**。在全局搜索框中，输入 SmartHotelHost，然后选择
    **SmartHotelHost** 虚拟机。

- ![](./media/image7.png)

2.  选择 **Connect**，从下拉列表中选择 **Connect**。

- ![A screenshot of a computer Description automatically
  generated](./media/image8.jpg)

  自动生成的计算机 Description 的屏幕截图

3.  选择 **Download RDP File**（下载 RDP 文件）。

- ![A screenshot of a computer Description automatically
  generated](./media/image9.jpg)

  自动生成的计算机 Description 的屏幕截图

4.  单击通知的 **Keep** 按钮，然后单击 **Open file** 进行连接。

- ![A screenshot of a computer Description automatically
  generated](./media/image10.jpg)

  自动生成的计算机 Description 的屏幕截图

5.  使用用户名 demouser 和密码 demo！pass123 **连接**到虚拟机

6.  从桌面快捷方式启动 **Chrome**。

7.  导航到 Azure 门户 https://portal.azure.com 搜索 Azure
    数据库迁移，然后从下拉列表中选择 **Azure Database Migration
    Services**。

- ![A screenshot of a computer Description automatically
  generated](./media/image11.jpg)

  自动生成的计算机 Description 的屏幕截图

8.  在 “**Azure Database Migration Services**” 边栏选项卡上，选择“**+
    Create**” 。

- ![A screenshot of a computer Description automatically
  generated](./media/image12.jpg)

  自动生成的计算机 Description 的屏幕截图

9.  在 **Select migration scenario and Database Migration Service**
    页面上查看详细信息，然后单击 **Select** 按钮

- ![A screenshot of a computer Description automatically
  generated](./media/image13.jpg)

  自动生成的计算机 Description 的屏幕截图

10. 在 Create Data Migration Service （创建数据迁移服务） 页面的 Basics
    （基本） 选项卡上，提供以下详细信息。

    - 订阅 – **Depth-@lab.CloudSubscription.Id**

    - 资源组：**SmartHotelRG**

    - 地点 – **West US**

    - 名称：`SmartHotelDBMigration`

    - 单击 **Review + create**

- ![A screenshot of a data migration service Description automatically
  generated](./media/image14.png)

  自动生成的数据迁移服务描述的屏幕截图

11. 在 **Review + create** 选项卡上，单击 **Create** 按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image15.png)

  自动生成的计算机 Description 的屏幕截图

12. Deployment 应该在几秒钟内完成，单击 **Go to resource** 按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image16.png)

  自动生成的计算机 Description 的屏幕截图

13. 在 Settings 下选择 **Integration Runtime**，然后单击 **Configure
    integration runtime**。

- ![A screenshot of a computer Description automatically
  generated](./media/image17.jpg)

  自动生成的计算机 Description 的屏幕截图

14. 单击链接**Download and install the integration runtime**，然后在
    **SmartHotelHost** VM 上下载运行时

- ![A screenshot of a computer Description automatically
  generated](./media/image18.jpg)

  自动生成的计算机 Description 的屏幕截图

15. 点击 **Download**

- ![A screenshot of a computer Description automatically
  generated](./media/image19.png)

  自动生成的计算机 Description 的屏幕截图

16. 选择最新版本，然后单击 **Download**

- ![A screenshot of a computer Description automatically
  generated](./media/image20.png)

  自动生成的计算机 Description 的屏幕截图

17. 下载后，使用默认选项安装 Integration runtime

- ![A screenshot of a computer Description automatically
  generated](./media/image21.jpg)

  自动生成的计算机 Description 的屏幕截图

18. **Microsoft Integration Runtime Configuration Manager** 应在单击
    **Finish** 按钮时启动。

19. 在 Azure 门户的 “**Configure integration runtime**”
    选项卡中，复制“**Key 1**” 值

- ![A screenshot of a computer Description automatically
  generated](./media/image22.jpg)

  自动生成的计算机 Description 的屏幕截图

20. 返回 **Microsoft Integration Runtime Configuration
    Manager**，粘贴复制的密钥，然后单击 **Register** 按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image23.jpg)

  自动生成的计算机 Description 的屏幕截图

21. 点击 **Finish** 按钮

- ![A screenshot of a computer Description automatically
  generated](./media/image24.jpg)

  A screenshot of a computer Description automatically generated

  ![A yellow rectangle with black text Description automatically
  generated](./media/image25.jpg)

  自动生成带有黑色文本 Description 的黄色矩形

22. 注册完成后，单击 **Launch Configuration Manager** 按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image26.jpg)

  自动生成的计算机 Description 的屏幕截图

23. 查看 **Microsoft Integration Runtime Configuration Manager**
    上的详细信息

- ![A screenshot of a computer Description automatically
  generated](./media/image27.jpg)

  自动生成的计算机 Description 的屏幕截图

24. 切换回 Azure 门户，然后单击 “**Configure integration runtime**”
    选项卡上的 “Ok” 。

25. 对于 **Integration runtime**，Status （状态） 应更新为 Online
    （联机）

- ![A screenshot of a computer Description automatically
  generated](./media/image28.jpg)

  自动生成的计算机 Description 的屏幕截图

### 任务 3：将本地 SQL 数据库迁移到 Azure SQL 数据库

1.  仍在 Azure
    数据库迁移服务页面上时，选择“概述”，然后单击“入门”选项卡下的“**New
    Migration**”按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image29.jpg)

  自动生成的计算机 Description 的屏幕截图

2.  在 Select new migration scenario （选择新的迁移方案）
    页面上，提供以下详细信息

    - 源服务器类型 – **SQL Server**

    - 目标服务器类型 – **Azure SQL Database**

- ![A screenshot of a computer Description automatically
  generated](./media/image30.jpg)

  自动生成的计算机 Description 的屏幕截图

3.  点击 **Select** 按钮

4. 在 Azure SQL Database Offline Migration Wizard页面上，在源详细信息选项卡上提供以下详细信息。
    - 您的源 SQL Server 实例是否在 Azure 中被跟踪？- No 
    - 源基础架构类型：Hyper-V
    - 订阅 - 保留默认选择。
    - 身份验证类型： SQL Authentication
    - 资源组：SmartHotelHostRG
    - 位置： West US 
    - SQL Server 实例名称：192.168.0.6

    ![alt text](./media/image30a.png)

4.  在 Azure SQL 数据库脱机迁移向导页面上，在**连接到源 SQL Server**
    选项卡上提供以下详细信息。

    - 源服务器名称：`192.168.0.6`

    - 身份验证类型：**SQL Authentication**

    - 用户名：`sa`

    - 密码：`demo!pass123`

    - 连接属性 – **启用两个复选框**

- ![A screenshot of a login Description automatically
  generated](./media/image31.jpg)

  自动生成的登录 Description 的屏幕截图

5.  单击 **Next: Select database for migration \>\>**

6.  在 **Select database for migration** 选项卡上，选择
    SmartHotel.Registration 数据库，然后单击 **Next: Connect to the
    target Azure SQL Database \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image32.jpg)

  自动生成的计算机 Description 的屏幕截图

7.  在 “**Connect to the target Azure SQL Database**”
    选项卡上，所有信息都应该已填充，您可以查看信息，然后提供密码 –
    `demo!pass123` 并单击 **Next: Map source and target databases \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image33.jpg)

  自动生成的计算机 Description 的屏幕截图

8.  在 **Map source and target databases** 选项卡上，从 目标数据库
    的下拉列表中选择 **smarthoteldb**，然后单击 **Next: Select database
    tables to migrate \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image34.jpg)

  自动生成的计算机 描述的屏幕截图

9.  在 **Select database tables to migrate** 选项卡上，单击下拉菜单
    **SmartHotel.Registration tables selected 2/2** 并确保
    \[dbo\]。\[Bookings\] 只是所选的表，然后单击 **Next: Database
    migration summary \>\>**

- ![A screenshot of a computer Description automatically
  generated](./media/image35.jpg)

  自动生成的计算机 Description 的屏幕截图

10. 在 **Database migration summary** （数据库迁移摘要）
    选项卡上，查看详细信息，然后单击 **Start migration** （开始迁移）
    按钮。

- ![A screenshot of a computer Description automatically
  generated](./media/image36.jpg)

  自动生成的计算机 Description 的屏幕截图

11. 的 Migration status（迁移状态）可以在 **Migration** 选项卡下看到

- ![A screenshot of a computer Description automatically
  generated](./media/image37.jpg)

  自动生成的计算机 描述的屏幕截图

  > **注意：迁移大约需要 10 分钟**

  ![A screenshot of a computer Description automatically
  generated](./media/image38.jpg)

  自动生成的计算机 描述的屏幕截图

12. 单击 **Refresh** 按钮几次，直到 Migration status（迁移状态）更改为
    **Succeeded**（成功）。

- ![A screenshot of a computer Description automatically
  generated](./media/image39.jpg)

  自动生成的计算机 描述的屏幕截图

13. 单击 Source name （源名称）， **192.168.0.6**

- ![A screenshot of a computer Description automatically
  generated](./media/image40.jpg)

  自动生成的计算机 描述的屏幕截图

14. 查看迁移的详细信息

- ![A screenshot of a computer Description automatically
  generated](./media/image41.jpg)

  自动生成的计算机 描述的屏幕截图

15. 我们已成功将本地 SQL 数据库迁移到 Azure SQL 数据库。

### 总结

在本实验中，我们应该使用 Azure 数据库迁移服务，并在 **SmartHotelHost**
VM 上安装所需的集成运行时，以便能够使用数据库迁移服务 （**DMS**）
将本地数据库成功迁移到 Azure SQL 数据库。

![A screenshot of a computer Description automatically
generated](./media/image41.jpg)

自动生成的计算机 描述的屏幕截图
