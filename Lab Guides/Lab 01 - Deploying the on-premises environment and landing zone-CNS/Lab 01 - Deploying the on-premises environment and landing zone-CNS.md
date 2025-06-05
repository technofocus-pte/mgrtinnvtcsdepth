# 实验 01 - 部署和验证本地环境和登录区域

## 目的

在实验室中，我们将使用**本地环境**，包括以下内容

- 运行嵌套 Hyper-V 的 Azure VM，其中包含 4 个嵌套 VM

- **2** 个资源组，总共需要 **17** 个资源，在即将到来的练习中将需要。

- **SmartHotel 应用程序**，在 SmartHotelHost 上的 Hyper-V 中的嵌套 VM
  上运行

- 迁移后需要 VNet 对等互连。

- Azure SQL 数据库等

- ![](./media/image1.jpg)

在 **SmartHotelHostRG** 中，将创建一个运行嵌套 Hyper-V
的虚拟机，其中包含 4 个嵌套
VM。这表示您将在此实验室中评估和迁移的“本地”环境。

**SmartHotel** 应用程序包括 Hyper-V 中托管的 4 个 VM：

- **数据库层**托管在运行 Windows Server 2016 和 SQL Server 2017 的
  smarthotelSQL1 VM 上。

- **应用程序层** 托管在运行 Windows Server 2012R2 的 smarthotelweb2 VM
  上。

- **Web 层** 托管在运行 Windows Server 2012R2 的 smarthotelweb1 VM 上。

- **Web 代理**托管在 UbuntuWAF VM 上，该 VM 在 Ubuntu 18.04 LTS 上运行
  Nginx。

为简单起见，任何层中都没有冗余。

另一个名为 SmartHotelRG 的资源组包括

若要评估 Hyper-V 环境，你将使用 **Azure Migrate：服务器评估**。这包括在
Hyper-V 主机上部署 **Azure Migrate
设备**以收集有关环境的信息。为了进行更深入的分析，将在 VM 上安装
**Microsoft Monitoring Agent** 和 **Dependency Agent**，从而启用 **Azure
Migrate 依赖项可视化**。

通过在 Hyper-V 主机上安装 **Microsoft 数据迁移助手 （DMA）**
并使用它来收集有关数据库的信息，将评估 **SQL Server
数据库**。然后，将使用 **Azure 数据库迁移服务 （DMS）**
完成**架构迁移**和**数据迁移**。

**应用程序层、Web 层和 Web 代理层**将使用 **Azure
Migrate：服务器迁移**迁移到 **Azure VM。**您将完成构建 Azure
环境、将数据复制到 Azure、自定义 VM
设置以及执行故障转移以将应用程序迁移到 Azure 的步骤。

**注意：**迁移后，可以对应用程序进行现代化改造，以使用 **Azure
应用程序网关**而不是 **Ubuntu Nginx VM**，并使用 **Azure
应用服务**来托管 **Web
层**和**应用程序层**。这些优化超出了本实验室的范围，本实验室仅侧重于向
Azure VM 的“直接迁移”。

![A diagram of a server Description automatically
generated](./media/image2.jpg)

自动生成的服务器描述

> **注意：**在启动期间，使用模板生成本地环境，因此启动大约需要 7-10
> 分钟才能完成部署。模板部署完成后，将执行几个其他脚本来引导实验室环境。**从模板部署开始至少需要
> 1 小时才能运行脚本。**
>
> **设置本地环境后，请等待 30-40 分钟，然后继续执行任务 1。**

### 任务 1：验证本地环境

1.  在 Lab VM 上，打开浏览器，然后从 Lab
    界面的“**主页/资源**”**选项卡**中导航到 **Office 365 租户凭据**
    `https://portal.azure.com` 登录。

2.  在主页上选择 `Resource groups`（资源组）。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image3.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

3.  选择 **SmartHotelHostRG**。

- ![Graphical user interface, text, application Description
  automatically generated](./media/image4.png)

  自动生成图形用户界面、文本、应用程序描述

4.  选择上一模块中的模板部署的 SmartHotelHost VM。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image5.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

5.  记下**公有 IP 地址**。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image6.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

6.  打开浏览器选项卡并导航到 **SmartHotelHostVM 的公有
    IP**（在上一步中记下）。您应该会看到 **SmartHotel** 应用程序，它正在
    SmartHotelHost 上的 Hyper-V 中的嵌套 VM
    上运行。（该应用程序没有太多作用：您可以刷新页面以查看客人列表，或者选择
    “**CheckIn**” 或 “**CheckOut**” 来切换他们的状态。

- ![Graphical user interface, application Description automatically
  generated](./media/image7.png)

  图形用户界面，自动生成应用程序描述

  > **注意：**如果未显示 **SmartHotel 应用程序**，请等待 10
  > 分钟，然后重试。从模板部署开始**至少需要 1 小时**。您还可以在 Azure
  > 门户中检查 **SmartHotelHost** VM 的
  > CPU、网络和磁盘活动级别，以查看预配是否仍处于活动状态。

您已完成此任务。请勿关闭此选项卡以继续执行下一个任务。

### 任务 2：验证登录区环境

1.  切换回 **SmartHotelHost VM** 选项卡，然后选择 **Home**。

- ![Graphical user interface, application, email Description
  automatically generated](./media/image8.png)

  自动生成图形用户界面、应用程序、电子邮件描述

2.  选择 **Resource Groups service**（资源组服务）。

- ![Graphical user interface, application Description automatically
  generated](./media/image9.png)

  图形用户界面，自动生成应用程序描述

3.  选择 **SmartHotelRG** 资源组。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image10.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

4.  请注意，**虚拟网络、堡垒资源、应用程序网关、SQL Server**
    和**数据库**都可用。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image11.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

  ![Graphical user interface, text, application, email Description
  automatically generated](./media/image12.png)

  自动生成图形用户界面、文本、应用程序、电子邮件描述

### 总结

在实验室结束时，我们应该已成功部署 ARM
模板并验证本地**智能酒店应用程序**，该应用程序应该已启动并运行。应部署
**Azure 登陆区域资源**，其中包括虚拟网络、Azure 堡垒、应用程序网关和具有
Azure SQL 数据库的 Azure SQL Server。

**智能酒店应用程序**

![Graphical user interface, application Description automatically
generated](./media/image13.png)

图形用户界面，自动生成应用程序描述

**SmartHotelRG** 中的 **Azure 登陆区域**资源

![Graphical user interface, text, application, email Description
automatically generated](./media/image11.png)

自动生成图形用户界面、文本、应用程序、电子邮件描述

![Graphical user interface, text, application, email Description
automatically generated](./media/image12.png)

自动生成图形用户界面、文本、应用程序、电子邮件描述
