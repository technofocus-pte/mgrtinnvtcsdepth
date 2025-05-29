# 실습 01 - 온프레미스 환경 및 landing zone 배포 및 검증

## 목표

실습에서는 아래를 포함한 **on-premises environment**에서 작업합니다.

- 4개의 중첩된 VM이 포함된 중첩된 Hyper-V를 실행하는 Azure VM

- 다음 연습에서 필요한 총 **17**개의 리소스로 구성된 **2**개의 리소스
  그룹.

- SmartHotelHost의 Hyper-V 내 중첩된 VM에서 실행되는 **SmartHotel
  application**

- 마이그레이션 후 VNet 피어링 필요

- Azure SQL 데이터베이스 등

&nbsp;

- ![](./media/image1.jpg)

**SmartHotelHostRG**에서는 4개의 중첩된 VM을 포함하는 중첩된 Hyper-V를
실행하는 가상 머신이 생성됩니다. 이는 이 실습에서 평가하고
마이그레이션할 '온프레미스' 환경을 나타냅니다.

**SmartHotel** 애플리케이션은 Hyper-V에 호스팅되는 4개의 VM으로
구성됩니다:

- **데이터베이스 계층**: Windows Server 2016 및 SQL Server 2017을
  실행하는 smarthotelSQL1 VM에 호스팅됩니다.

- **애플리케이션 계층**: Windows Server 2012R2를 실행하는 smarthotelweb2
  VM에 호스팅됩니다.

- **웹 계층**: Windows Server 2012R2를 실행하는 smarthotelweb1 VM에
  호스팅됩니다.

- **웹** **proxy**: Ubuntu 18.04 LTS에서 Nginx를 실행하는 UbuntuWAF VM에
  호스팅됩니다.

단순화를 위해 모든 계층에 중복성이 없습니다.

SmartHotelRG라는 다른 리소스 그룹은 다음으로 구성됩니다.

Hyper-V 환경을 평가하려면 **Azure Migrate: Server Assessment**를
사용합니다. 여기에는 Hyper-V 호스트에 **Azure Migrate appliance**를
배포하여 환경 정보를 수집하는 작업이 포함됩니다. 심층 분석을 위해
**Microsoft Monitoring Agent**와 **Dependency Agent**가 VM에 설치되어
**Azure Migrate dependency visualization**를 활성화합니다.

**SQL Server database** 는 Hyper-V 호스트에 **Microsoft Data Migration
Assistant(DMA)**를 설치하고 이를 통해 데이터베이스 정보를 수집하여
평가합니다. 그런 다음 **Azure Database Migration Service(DMS)**를
사용하여 **Schema migration**과 **data migration** 을 완료합니다.

**애플리케이션, 웹 및 웹 Proxy** 계층은 **Azure Migrate: Server
Migration**을 사용하여 **Azure VMs**으로 마이그레이션됩니다. Azure 환경
구축, Azure로 데이터 복제, VM 설정 사용자 지정, 그리고 애플리케이션을
Azure로 마이그레이션하기 위한 장애 조치(failover) 수행 단계를
안내합니다.

**참고**: 마이그레이션 후, **Ubuntu Nginx VM** 대신 **Azure Application
Gateway**를 사용하고 **Azure App Service**를 사용하여 **웹 계층**과
**애플리케이션 계층**을 모두 호스팅하도록 애플리케이션을 현대화할 수
있습니다. 이러한 최적화는 Azure VM으로의 'Lift and shift'
마이그레이션에만 초점을 맞춘 본 랩의 범위에 포함되지 않습니다.

![A diagram of a server Description automatically
generated](./media/image2.jpg)

자동으로 생성된 서버 설명 다이어그램

> **참고**: 실행 과정에서 온프레미스 환경을 생성하는 데 템플릿을
> 사용했기 때문에 배포하는 데 약 7~10분이 걸렸습니다. 템플릿 배포가
> 완료되면 랩 환경을 부트스트랩하기 위해 몇 가지 추가 스크립트가
> 실행됩니다. **스크립트가 실행되려면 템플릿 배포 시작 후 최소 1시간
> 정도 소요됩니다.**
>
> **온프레미스 환경이 설정되는 동안 30~40분 정도 기다린 후 작업 1을
> 진행합니다.**

### 작업 1: 온-프레미스 환경 확인

1.  실습 VM에서 브라우저를 열고 랩 인터페이스의 **Home/Resources
    tab**에서 **Office 365 Tenant Credential**을 사용하여
    `https://portal.azure.com` 으로 로그인합니다.

2.  홈페이지에서 `Resource groups`을 선택합니다.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image3.png)

  그래픽 사용자 인터페이스(GUI), 텍스트, 애플리케이션, 이메일 설명 자동
  생성

3.  **SmartHotelHostRG**를 선택합니다.

- ![Graphical user interface, text, application Description
  automatically generated](./media/image4.png)

  그래픽 사용자 인터페이스, 텍스트, 응용 프로그램 설명 자동 생성

4.  이전 모듈의 템플릿에 의해 배포된 **SmartHotelHost** VM을 선택합니다.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image5.png)

  그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

5.  **public IP address**를 기록해 둡니다.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image6.png)

  그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

6.  브라우저 탭을 열고 **Public IP of the SmartHotelHostVM**  (이전
    단계에서 확인)로 이동합니다. **SmartHotelHost**의 Hyper-V 내 중첩된
    VM에서 실행 중인 **SmartHotel** 애플리케이션이 표시됩니다.
    (애플리케이션은 별다른 기능을 제공하지 않습니다. 페이지를 새로 고쳐
    투숙객 목록을 확인하거나 **‘CheckIn’** 또는 **‘CheckOut’** 을
    선택하여 투숙객 상태를 전환할 수 있습니다.)

- ![Graphical user interface, application Description automatically
  generated](./media/image7.png)

  그래픽 사용자 인터페이스, 애플리케이션 설명 자동 생성

  > **참고**: **SmartHotel application** 이 **표시되지 않으면** 10분
  > 정도 기다린 후 다시 시도하세요. 템플릿 배포 시작 후 최소 1시간이
  > 소요됩니다. Azure Portal에서 **SmartHotelHost** VM의 CPU, 네트워크
  > 및 디스크 활동 수준을 확인하여 프로비저닝이 아직 활성 상태인지
  > 확인할 수도 있습니다.

이 작업을 완료했습니다. 다음 작업을 진행하려면 이 탭을 닫지 않습니다.

### 작업 2: Landing zone환경 확인

1.  **SmartHotelHost** VM 탭으로 돌아가서 **Home**을 선택합니다.

- ![Graphical user interface, application, email Description
  automatically generated](./media/image8.png)

  그래픽 사용자 인터페이스, 애플리케이션, 이메일 설명 자동 생성

2.  **Resource Groups** 서비스를 선택합니다.

- ![Graphical user interface, application Description automatically
  generated](./media/image9.png)

  그래픽 사용자 인터페이스, 응용 프로그램 설명 자동 생성

3.  **SmartHotelRG** 리소스 그룹을 선택합니다.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image10.png)

  그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

4.  **Virtual Network**, **Bastion resource**, **Application
    Gateway**, **SQL Server** 및 **Database** 가 사용 가능합니다.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image11.png)

  그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

  ![Graphical user interface, text, application, email Description
  automatically generated](./media/image12.png)

  그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

### 요약

실습을 마치면 ARM 템플릿을 성공적으로 배포하고 온프레미스 **Smart Hotel
Application** 이 정상적으로 실행 중인지 확인했을 것입니다. **Azure
Landing zone resource** 는 가상 네트워크, Azure Bastion, 애플리케이션
게이트웨이, 그리고 Azure SQL database가 포함된 Azure SQL Server로
구성되어 배포되어야 합니다.

**Smart Hotel Application**

![Graphical user interface, application Description automatically
generated](./media/image13.png)

그래픽 사용자 인터페이스, 응용 프로그램 설명 자동 생성

**SmartHotelRG**의 **Azure Landing zone** resource

![Graphical user interface, text, application, email Description
automatically generated](./media/image11.png)

그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성

![Graphical user interface, text, application, email Description
automatically generated](./media/image12.png)

그래픽 사용자 인터페이스, 텍스트, 애플리케이션, 이메일 설명 자동 생성
