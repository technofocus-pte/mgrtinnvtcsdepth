# Laboratório 01 - Implementação e verificação do ambiente local e landing zone

## Objetivo

No laboratório, trabalharemos com o **ambiente local,** incluindo o
seguinte

- Uma VM do Azure executando Hyper-V aninhado, com 4 VMs aninhadas

- **2** grupos de recursos com um total de **17** recursos que seriam
  necessários nos próximos exercícios.

- Um **aplicativo SmartHotel,** que está sendo executado em VMs
  aninhadas no Hyper-V no SmartHotelHost

- Peering de VNet necessário após a migração.

- Banco de dados SQL do Azure etc.

&nbsp;

- ![](./media/image1.jpg)

No **SmartHotelHostRG**, é criada uma máquina virtual executando o
Hyper-V aninhado, com 4 VMs aninhadas. Isso representa o ambiente
"local" que você avaliará e migrará durante este laboratório.

O aplicativo **SmartHotel** é composto por 4 VMs hospedadas no Hyper-V:

- **Camada de banco de dados** hospedada na VM smarthotelSQL1, que
  executa o Windows Server 2016 e o SQL Server 2017.

- **Camada de aplicativo** hospedada na VM smarthotelweb2, que executa o
  Windows Server 2012R2.

- **Camada da Web** hospedada na VM smarthotelweb1, que executa o
  Windows Server 2012R2.

- **Proxy da Web** hospedado na VM UbuntuWAF, que executa o Nginx no
  Ubuntu 18.04 LTS.

Para simplificar, não há redundância em nenhuma das camadas.

O outro grupo de recursos denominado SmartHotelRG compreende

Para avaliar o ambiente Hyper-V, você usará o **Azure Migrate: Avaliação
de Servidor.** Isso inclui a implementação do **dispositivo Azure
Migrate** no Hyper-V host para coletar informações sobre o ambiente.
Para uma análise mais aprofundada, o **Agente de Monitoramento** e o
**Agente de Dependência da Microsoft** serão instalados nas VMs,
permitindo a **visualização de dependências do Azure Migrate.**

O **banco de dados SQL Server** será avaliado por meio da instalação do
**Microsoft Data Migration Assistant (DMA)** no Hyper-V host e usando-o
para coletar informações sobre o banco de dados. **A migração de
esquema** e a **migração de dados** serão então concluídas usando o
**Azure Database Migration Service (DMS).**

**As camadas de aplicativo, web e proxy da web** serão migradas para
**VMs do Azure** usando **o Azure Migrate: Migração de Servidor.** Você
percorrerá as etapas de criação do ambiente do Azure, replicação de
dados para o Azure, personalização das configurações da VM e execução de
um failover para migrar o aplicativo para o Azure.

**Observação:** após a migração, o aplicativo pode ser modernizado para
usar **o Azure Application Gateway** em vez da **VM Ubuntu Nginx** e
para usar o **Serviço de Aplicativo do Azure** para hospedar as
**camadas** **da Web** e **do aplicativo.** Essas otimizações estão fora
do escopo deste laboratório, que se concentra apenas em uma migração
"lift and shift" para VMs do Azure.

![A diagram of a server Description automatically
generated](./media/image2.jpg)

Um diagrama de um servidor Descrição gerada automaticamente

> **Observação:** Um modelo foi usado para gerar o ambiente local
> durante o lançamento, portanto, a implementação levou de 7 a 10
> minutos. Após a conclusão da implementação do modelo, vários scripts
> adicionais são executados para inicializar o ambiente de laboratório.
> Aguarde aguarde pelo menos uma hora após o início da implementação do
> modelo **para que os scripts sejam executados.**
>
> **Enquanto o ambiente local estiver configurado, aguarde de 30 a 40
> minutos e prossiga com a Tarefa 1.**

### Tarefa 1: Verificar o ambiente local

1.  Na sua VM do Lab, abra um navegador e acesse
    `https://portal.azure.com` e faça login usando **Office 365 Tenant
    Credential** na **aba Home/Resources** da interface do Lab.

2.  Selecione `Resource group` na página inicial.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image3.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

3.  Selecione **SmartHotelHostRG.**

- ![Graphical user interface, text, application Description
  automatically generated](./media/image4.png)

  Interface gráfica do usuário, texto, descrição do aplicativo gerada
  automaticamente

4.  Selecione a VM **SmartHotelHost** que foi implementada pelo modelo
    no módulo anterior.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image5.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

5.  Anote o **public IP address**.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image6.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

6.  Abra uma aba do navegador e navegue até o **Public IP of the
    SmartHotelHostVM** (observado na etapa anterior). Você deverá ver o
    aplicativo **SmartHotel,** que está sendo executado em VMs aninhadas
    dentro do Hyper-V no SmartHotelHost. (O aplicativo não faz muita
    coisa: você pode atualizar a página para ver a lista de hóspedes ou
    selecionar **"Check-in"** ou **"Check-out"** para alternar o status
    deles.)

- ![Graphical user interface, application Description automatically
  generated](./media/image7.png)

  Interface gráfica do usuário, descrição do aplicativo gerada
  automaticamente

  > **Observação:** se o **aplicativo SmartHotel não** for exibido,
  > aguarde 10 minutos e tente novamente. O processo leva **pelo menos 1
  > hora** a partir do início da implementação do modelo. Você também
  > pode verificar os níveis de atividade da CPU, da rede e do disco da
  > VM **SmartHotelHost** no portal do Azure para ver se o
  > provisionamento ainda está ativo.

Você concluiu esta tarefa. Não feche esta aba para prosseguir para a
próxima tarefa.

### Tarefa 2: Verificar o ambiente da **landing zone**

1.  Volte para a aba VM do **SmartHotelHost** e selecione **Home**.

- ![Graphical user interface, application, email Description
  automatically generated](./media/image8.png)

  Interface gráfica do usuário, aplicativo, e-mail De uma descrição de
  computador gerada automaticamente

2.  Selecione o serviço **Resource Groups**.

- ![Graphical user interface, application Description automatically
  generated](./media/image9.png)

  Interface gráfica do usuário, descrição do aplicativo gerada
  automaticamente

3.  Selecione o grupo de recursos **SmartHotelRG.**

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image10.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

4.  Observe que a **Virtual Network**, **Bastion
    resource**, **Application Gateway**, **SQL Server** e o **Database**
    estão disponíveis.

- ![Graphical user interface, text, application, email Description
  automatically generated](./media/image11.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

  ![Graphical user interface, text, application, email Description
  automatically generated](./media/image12.png)

  Interface gráfica do usuário, texto, aplicativo, e-mail de uma
  descrição de computador gerada automaticamente

### Resumo

Ao final do laboratório, devemos ter implementado com sucesso o template
ARM e verificado que a **aplicação Smart Hotel** local, deve estar em
execução. O **recurso da Azure Landing zone** deve ser implementado,
composto por Rede Virtual, Azure Bastion, Application Gateway e um
Servidor SQL do Azure com um Banco de Dados SQL do Azure.

**Aplicação Smart Hotel**

![Graphical user interface, application Description automatically
generated](./media/image13.png)

Interface gráfica do usuário, descrição do aplicativo gerada
automaticamente

**Recurso Azure Landing zone** no **SmartHotelRG**

![Graphical user interface, text, application, email Description
automatically generated](./media/image11.png)

Interface gráfica do usuário, texto, aplicativo, e-mail de uma descrição
de computador gerada automaticamente

![Graphical user interface, text, application, email Description
automatically generated](./media/image12.png)

Interface gráfica do usuário, texto, aplicativo, e-mail de uma descrição
de computador gerada automaticamente
