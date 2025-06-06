# 實驗 01 - 部署和驗證本地環境和登錄區域

## 目的

在實驗室中，我們將使用**本地環境**，包括以下內容

- 運行嵌套 Hyper-V 的 Azure VM，其中包含 4 個嵌套 VM

- **2** 個資源組，總共需要 **17** 個資源，在即將到來的練習中將需要。

- **SmartHotel 應用程序**，在 SmartHotelHost 上的 Hyper-V 中的嵌套 VM
  上運行

- 遷移後需要 VNet 對等互連。

- Azure SQL 數據庫等

&nbsp;

- ![](./media/image1.jpg)

在 **SmartHotelHostRG** 中，將創建一個運行嵌套 Hyper-V
的虛擬機，其中包含 4 個嵌套
VM。這表示您將在此實驗室中評估和遷移的“本地”環境。

**SmartHotel** 應用程序包括 Hyper-V 中託管的 4 個 VM：

- **數據庫層**託管在運行 Windows Server 2016 和 SQL Server 2017 的
  smarthotelSQL1 VM 上。

- **應用程序層** 託管在運行 Windows Server 2012R2 的 smarthotelweb2 VM
  上。

- **Web 層** 託管在運行 Windows Server 2012R2 的 smarthotelweb1 VM 上。

- **Web 代理**託管在 UbuntuWAF VM 上，該 VM 在 Ubuntu 18.04 LTS 上運行
  Nginx。

為簡單起見，任何層中都沒有冗餘。

另一個名為 SmartHotelRG 的資源組包括

若要評估 Hyper-V 環境，你將使用 **Azure Migrate：服務器評估**。這包括在
Hyper-V 主機上部署 **Azure Migrate
設備**以收集有關環境的信息。為了進行更深入的分析，將在 VM 上安裝
**Microsoft Monitoring Agent** 和 **Dependency Agent**，從而啟用 **Azure
Migrate 依賴項可視化**。

通過在 Hyper-V 主機上安裝 **Microsoft 數據遷移助手 （DMA）**
並使用它來收集有關數據庫的信息，將評估 **SQL Server
數據庫**。然後，將使用 **Azure 數據庫遷移服務 （DMS）**
完成**架構遷移**和**數據遷移**。

**應用程序層、Web 層和 Web 代理層** 將使用 **Azure
Migrate：服務器遷移**遷移到 **Azure VM。** 您將完成構建 Azure
環境、將數據複製到 Azure、自定義 VM
設置以及執行故障轉移以將應用程序遷移到 Azure 的步驟。

**注意** 遷移後，可以對應用程序進行現代化改造，以使用 **Azure
應用程序網關**而不是 **Ubuntu Nginx VM**，並使用 **Azure
應用服務**來託管 **Web
層**和**應用程序層**。這些優化超出了本實驗室的範圍，本實驗室僅側重于向
Azure VM 的“直接遷移”。

![A diagram of a server Description automatically
generated](./media/image2.jpg)

自動生成的服務器描述

> **注意** 在啟動期間，使用模板生成本地環境，因此啟動大約需要 7-10
> 分鐘才能完成部署。模板部署完成後，將執行幾個其他腳本來引導實驗室環境。**從模板部署開始至少需要
> 1 小時才能運行腳本。**
>
> **設置本地環境後，請等待 30-40 分鐘，然後繼續執行任務 1。**

### 任務 1：驗證本地環境

1.  在 Lab VM 上，打開瀏覽器，然後從 Lab
    界面的“**主頁/資源**”**選項卡**中導航到 **Office 365 租戶憑據**
    `https://portal.azure.com` 登錄。

2.  在主頁上選擇 `Resource groups`（資源組）。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image3.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

3.  選擇 **SmartHotelHostRG**。

- ![Graphical user interface, text, application Description
  automatically generated](./media/image4.png)

  自動生成圖形用戶界面、文本、應用程序描述

4.  選擇上一模塊中的模板部署的 SmartHotelHost VM。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image5.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

5.  記下**公有 IP 地址**。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image6.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

6.  打開瀏覽器選項卡並導航到 **SmartHotelHostVM 的公有
    IP**（在上一步中記下）。您應該會看到 **SmartHotel** 應用程序，它正在
    SmartHotelHost 上的 Hyper-V 中的嵌套 VM
    上運行。（該應用程序沒有太多作用：您可以刷新頁面以查看客人列表，或者選擇
    “**CheckIn**” 或 “**CheckOut**” 來切換他們的狀態。

- ![Graphical user interface, application Description automatically
  generated](./media/image7.png)

  圖形用戶界面，自動生成應用程序描述

  > **注意** 如果未顯示 **SmartHotel 應用程序**，請等待 10
  > 分鐘，然後重試。從模板部署開始**至少需要 1 小時**。您還可以在 Azure
  > 門戶中檢查 **SmartHotelHost** VM 的
  > CPU、網絡和磁盤活動級別，以查看預配是否仍處於活動狀態。

您已完成此任務。請勿關閉此選項卡以繼續執行下一個任務。

### 任務 2：驗證登錄區環境

1.  切換回 **SmartHotelHost VM** 選項卡，然後選擇 **Home**。

- ![Graphical user interface, application, email Description
  automatically generated](./media/image8.png)

  自動生成圖形用戶界面、應用程序、電子郵件描述

2.  選擇 **Resource Groups service**（資源組服務）。

- ![Graphical user interface, application Description automatically
  generated](./media/image9.png)

  圖形用戶界面，自動生成應用程序描述

3.  選擇 **SmartHotelRG** 資源組。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image10.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

4.  請注意，**虛擬網絡、堡壘資源、應用程序網關、SQL Server**
    和**數據庫**都可用。

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image11.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

  ![Graphical user interface, text, application, email Description
  automatically generated](./media/image12.png)

  自動生成圖形用戶界面、文本、應用程序、電子郵件描述

### 總結

在實驗室結束時，我們應該已成功部署 ARM
模板並驗證本地**智能酒店應用程序**，該應用程序應該已啟動並運行。應部署
**Azure 登陸區域資源**，其中包括虛擬網絡、Azure 堡壘、應用程序網關和具有
Azure SQL 數據庫的 Azure SQL Server。

**智能酒店應用程序**

![Graphical user interface, application Description automatically
generated](./media/image13.png)

圖形用戶界面，自動生成應用程序描述

**SmartHotelRG** 中的 **Azure 登陸區域**資源

![Graphical user interface, text, application, email Description
automatically generated](./media/image11.png)

自動生成圖形用戶界面、文本、應用程序、電子郵件描述

![Graphical user interface, text, application, email Description
automatically generated](./media/image12.png)

自動生成圖形用戶界面、文本、應用程序、電子郵件描述
