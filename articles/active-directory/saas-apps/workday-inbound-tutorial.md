---
title: 'Tutorial: Configurar o Workday para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Workday.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: asmalser
ms.openlocfilehash: 930ca49a63e34214ec197d8dd37f38361b34fe90
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347028"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Tutorial: Configurar o Workday para provisionamento automático de usuário (versão prévia)

O objetivo deste tutorial é mostrar as etapas necessárias para importar pessoas do Workday tanto no Active Directory como no Azure Active Directory, com write-back opcional de alguns atributos para Workday.

## <a name="overview"></a>Visão geral

O [serviço de provisionamento de usuário do Azure Active Directory](../manage-apps/user-provisioning.md) integra-se com a [API de Recursos Humanos do Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para provisionar contas de usuários. O Azure AD utiliza essa conexão para habilitar os seguintes fluxos de trabalho de provisionamento de usuário:

* **Provisionamento de usuários para Active Directory** - Sincronizar conjuntos de usuários selecionados do Workday em uma ou mais florestas do Active Directory.

* **Provisionamento de usuários somente na nuvem para o Azure Active Directory** - Nos cenários em que o Active Directory local não for usado, os usuários poderão ser provisionados diretamente do Workday para o Azure Active Directory usando o serviço de provisionamento de usuário do Microsoft Azure AD. 

* **Fazer write-back de endereços de email para Workday** - O serviço de provisionamento de usuário do Microsoft Azure AD pode gravar os endereços de email dos usuários do Microsoft Azure AD de volta no Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quais cenários de recursos humanos ele cobre?

Os fluxos de trabalho de provisionamento de usuário do Workday com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidade e recurso humanos:

* **Contratação de novos funcionários** - quando um novo funcionário é adicionado ao Workday, uma conta de usuário é criada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e [ outros aplicativos SaaS suportados pelo Azure AD](../manage-apps/user-provisioning.md), com write-back do endereço de e-mail para Workday.

* **Atualizações de perfil e atributo de funcionário** - Quando um registro de funcionário é atualizado no Workday (como seu nome, cargo ou gerente), sua conta de usuário será atualizada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e [outros aplicativos SaaS com suporte do Azure AD](../manage-apps/user-provisioning.md).

* **Rescisão de funcionário** - Quando um funcionário é rescindido no Workday, sua conta de usuário será desabilitada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e [outros aplicativos SaaS com suporte do Azure AD](../manage-apps/user-provisioning.md).

* **Recontratação de funcionário** - Quando um funcionário é recontratado no Workday, sua conta antiga poderá ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) para o Active Directory, o Azure Active Directory e, opcionalmente, Office 365 e [outros aplicativos SaaS com suporte do Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é mais recomendada essa solução de provisionamento de usuário?

Essa solução de provisionamento de usuário do Workday está atualmente em versão prévia e é ideal para:

* Organizações que desejam uma solução predefinida, baseados em nuvem para o provisionamento de usuário do Workday

* Organizações que precisam de provisionamento de usuário direto do Workday para o Active Directory ou Azure Active Directory

* Organizações que precisam que os usuários sejam provisionados usando os dados obtidos do módulo HCM do Workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* As organizações que precisam que os usuários adicionados, transferidos e excluídos sejam sincronizados com um ou mais florestas, domínios e unidades organizacionais do Active Directory com base somente em alterar informações detectadas no módulo HCM Workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Office 365 para email

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>Planejando sua solução

Antes de iniciar sua integração no Workday, verifique os pré-requisitos abaixo e leia as seguintes orientações sobre como corresponder sua arquitetura do Active Directory atual e os requisitos de provisionamento de usuário com a(s) solução(ões) fornecida(s) pelo Azure Active Directory.

### <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura do Azure AD Premium P1 válida com acesso de administrador global
* Um locatário de implementação do Workday para fins de integração e teste
* Permissões de administrador no Workday para criar um usuário de integração de sistema e fazer alterações para testar dados do funcionário para fins de teste
* Para provisionamento de usuário no Active Directory, é necessário um servidor ingressado no domínio executando Windows Service 2012 ou superior para hospedar o [agente de sincronização local](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../connect/active-directory-aadconnect.md) para a sincronização entre Active Directory e Azure AD

### <a name="solution-architecture"></a>Arquitetura da solução

O Azure AD fornece um avançado conjunto de conectores de provisionamento para ajudá-lo a resolver o provisionamento e gerenciamento do ciclo de vida da identidade do Workday para o Active Directory, Azure AD, aplicativos SaaS, e muito mais. Que recursos serão utilizados e como a solução será configurada irão variar de acordo com os requisitos e o ambiente da sua organização. Como uma primeira etapa, avalie o número dos itens a seguir que estão presentes e implantados em sua organização:

* Quantas Florestas do Active Directory estão em uso?
* Quantos Domínios do Active Directory estão em uso?
* Quantas OUs (unidades organizacionais) do Active Directory estão em uso?
* Quantos locatários do Azure Active Directory estão em uso?
* Há usuários que precisam ser provisionados para o Active Directory e o Azure Active Directory (por exemplo, usuários "híbridos")?
* Há usuários que precisam ser provisionados para o Active Directory do Azure, mas não para o Active Directory (por exemplo, usuários "apenas na nuvem")?
* Os endereços de email do usuário precisam fazer write-back para Workday?

Após responder a essas perguntas será possível planejar sua implantação de provisionamento do Workday seguindo as instruções abaixo.

#### <a name="using-provisioning-connector-apps"></a>Utilizando aplicativos de conectores de provisionamento

O Azure Active Directory dá suporte a conectores de provisionamento pré-integrados para Workday e um grande número de outros aplicativos SaaS.

Um conector de provisionamento único faz interface com a API de um sistema de origem único e ajuda a provisionar dados para um sistema de destino único. A maioria dos conectores de provisionamento que o Azure AD oferece suporte para um único sistema de origem e destino (por exemplo, Azure AD para ServiceNow) e pode ser configurada adicionando o aplicativo em questão da galeria de aplicativos do Azure AD (por exemplo, ServiceNow).

Há uma relação um-para-um entre instâncias de conectores de provisionamento e instâncias de aplicativos no Azure AD:

| Sistema de origem | Sistema de destino |
| ---------- | ---------- |
| Locatário do Azure AD | Aplicativo SaaS |

No entanto, ao trabalhar com Workday e Active Directory, existem vários sistemas de origem e de destino a serem considerados:

| Sistema de origem | Sistema de destino | Observações |
| ---------- | ---------- | ---------- |
| Workday | Floresta do Active Directory | Cada floresta é tratada como um sistema de destino distinto |
| Workday | Locatário do Azure AD | Conforme necessário para os usuários somente na nuvem |
| Floresta do Active Directory | Locatário do Azure AD | Atualmente, esse fluxo é tratado pelo AAD Connect |
| Locatário do Azure AD | Workday | Para fazer write-back de endereços de email |

Para facilitar esses fluxos de trabalho múltiplos para vários sistemas de origem e destino, o Azure AD fornece vários aplicativos de conectores de provisionamento que podem ser adicionados na galeria de aplicativos do Azure AD:

![Galeria de Aplicativos do AAD](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **Workday para Provisionamento do Active Directory** - Esse aplicativo facilita o provisionamento de contas de usuário do Workday para uma floresta do Active Directory única. Se você tiver várias florestas será possível adicionar uma instância desse aplicativo a partir da galeria de aplicativos do Azure AD para cada floresta do Active Directory que precisa ser provisionada.

* **Workday para Provisionamento do Azure AD** - Embora o AAD Connect seja a ferramenta que deve ser utilizada para sincronizar usuários do Active Directory com o Azure Active Directory, esse aplicativo pode ser utilizado para facilitar o provisionamento de usuários somente na nuvem do Workday para um locatário único do Azure Active Directory.

* **Fazer write-back de Workday** - Esse aplicativo facilita fazer write-back de endereços de email do usuário do Azure Active Directory para Workday.

> [!TIP]
> O aplicativo "Workday" regular é utilizado para configurar logon único entre o Workday e o Azure Active Directory. 

Como instalar e configurar esses aplicativos de conectores de provisionamento especiais é o assunto das próximas seções deste tutorial. Os aplicativos que você escolher configurar dependerão de quais sistemas precisam ser provisionados e de quantos locatários das florestas do Active Directory e Azure AD estão no seu ambiente.

![Visão geral](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Configurar um usuário de integração de sistema no Workday
Um requisito comum de todos os conectores de provisionamento do Workday é que exigem credenciais para uma conta de integração do sistema do Workday para conectar a API de Recursos Humanos do Workday. Esta seção descreve como criar uma conta integradora de sistema no Workday.

> [!NOTE]
> É possível ignorar esse procedimento e utilizar uma conta Administrador global do Workday como a conta de integração do sistema. Isso pode funcionar aparentemente bem para demonstrações, mas não é recomendável para implementações de produção.

### <a name="create-an-integration-system-user"></a>Criar um usuário do sistema de integração

**Para criar um usuário do sistema de integração:**

1. Entre no locatário do Workday utilizando uma conta Administrador. No **Workday Workbench**, insira Criar usuário na caixa de pesquisa e clique em **Criar Usuário do Sistema de Integração**.

    ![Criar Usuário](./media/workday-inbound-tutorial/IC750979.png "Criar Usuário")
2. Conclua a tarefa **Criar Usuário do Sistema de Integração** fornecendo um nome de usuário e senha para um novo Usuário do Sistema de Integração.  
 * Deixe a opção **Exigir Nova Senha na Próxima Entrada** desmarcada, porque esse usuário efetuará logon por meio de programação.
 * Deixe **Minutos de Tempo Limite da Sessão** com seu valor padrão de 0, o que impedirá que as sessões do usuário expirem prematuramente.

    ![Criar Usuário do Sistema de Integração](./media/workday-inbound-tutorial/IC750980.png "Criar Usuário do Sistema de Integração")

### <a name="create-a-security-group"></a>Adicionar um grupo de segurança
É necessário criar um grupo de segurança do sistema de integração irrestrito e atribuir-lhe o usuário.

**Para criar um grupo de segurança:**

1. Insira Criar grupo de segurança na caixa de pesquisa e clique em **Criar Grupo de Segurança**.

    ![Criar Grupo de Segurança](./media/workday-inbound-tutorial/IC750981.png "Criar Grupo de Segurança")
2. Conclua a tarefa **Criar Grupo de Segurança**.  
3. Selecione **Grupo de Segurança de Sistema de Integração (Sem Restrições)** no menu suspenso **Tipo de Grupo de Segurança com Locatários**.
4. Crie um grupo de segurança ao qual os membros serão adicionados explicitamente.

    ![Criar Grupo de Segurança](./media/workday-inbound-tutorial/IC750982.png "Criar Grupo de Segurança")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Atribuir o usuário do sistema de integração ao grupo de segurança

**Para atribuir o usuário do sistema de integração:**

1. Insira Editar grupo de segurança na caixa de pesquisa e clique em **Editar Grupo de Pesquisa**.

    ![Editar Grupo de Segurança](./media/workday-inbound-tutorial/IC750983.png "Editar Grupo de Segurança")
1. Procure e selecione o novo grupo de segurança de integração por nome.

    ![Editar Grupo de Segurança](./media/workday-inbound-tutorial/IC750984.png "Editar Grupo de Segurança")
2. Adicione o novo usuário do sistema de integração ao novo grupo de segurança. 

    ![Grupo de Segurança do Sistema](./media/workday-inbound-tutorial/IC750985.png "Grupo de Segurança do Sistema")  

### <a name="configure-security-group-options"></a>Configurando opções de grupo de segurança
Nesta etapa, você concederá permissões de política de segurança de domínio para os dados de trabalho ao grupo de segurança.

**Para configurar opções de grupo de segurança:**

1. Insira as **Políticas de Segurança de Domínio** na caixa de pesquisa e, em seguida, clique no link **Políticas de Segurança de Domínio para a Área Funcional**.  

    ![Políticas de Segurança de Domínio](./media/workday-inbound-tutorial/IC750986.png "Políticas de Segurança de Domínio")  
2. Pesquise o sistema e selecione a área funcional **Sistema**.  Clique em **OK**.  

    ![Políticas de Segurança de Domínio](./media/workday-inbound-tutorial/IC750987.png "Políticas de Segurança de Domínio")  
3. Na lista de políticas de segurança para a área funcional do sistema, expanda a **Administração de Segurança** e selecione a política de segurança de domínio, **Provisionamento de Conta Externa**.  

    ![Políticas de Segurança de Domínio](./media/workday-inbound-tutorial/IC750988.png "Políticas de Segurança de Domínio")  
1. Clique no botão **Editar Permissões** e, em seguida, na página da caixa de diálogo **Editar permissões**, adicione o novo grupo de segurança à lista de grupos de segurança com as permissões de integração **Get** e **Put**.

    ![Editar Permissão](./media/workday-inbound-tutorial/IC750989.png "Editar Permissão")  

1. Repita as etapas 1 a 4 acima para cada uma dessas políticas de segurança restantes:

| Operação | Política de segurança de domínio |
| ---------- | ---------- | 
| Get e Put | Dados de trabalho: Relatórios de trabalho público |
| Get e Put | Dados de trabalho: Informações de contato de trabalho |
| Obter | Dados de trabalho: Todas as posições |
| Obter | Dados de trabalho: Informações atuais sobre a equipe |
| Obter | Dados de trabalho: Cargo de negócios no perfil de trabalhado |


### <a name="activate-security-policy-changes"></a>Ativar alterações de política de segurança

**Para ativar alterações de política de segurança:**

1. Digite Ativar na caixa de pesquisa e, em seguida, clique no link **Ativar alterações de política de segurança pendentes**.

    ![Ativar](./media/workday-inbound-tutorial/IC750992.png "Ativar") 
2. Inicie a tarefa Ativar Alterações de Política de Segurança Pendentes inserindo um comentário para fins de auditoria e clique em **OK**. 

    ![Ativar Segurança Pendente](./media/workday-inbound-tutorial/IC750993.png "Ativar Segurança Pendente")  
1. Conclua a tarefa na próxima tela marcando a caixa de seleção **Confirmar** e clique em **OK**.

    ![Ativar Segurança Pendente](./media/workday-inbound-tutorial/IC750994.png "Ativar Segurança Pendente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurando o provisionamento de usuário do Workday para o Active Directory
Siga essas instruções para configurar o provisionamento de conta de usuário do Workday para cada floresta do Active Directory que precisa ser provisionada.

### <a name="planning"></a>Planejamento

Antes de configurar o provisionamento de usuário para uma floresta do Active Directory, considere as seguintes perguntas. As respostas a essas perguntas determinarão como seus filtros de escopo e os mapeamentos de atributo precisam ser definidos. 

* **O que os usuários no Workday precisam para serem provisionados a essa floresta do Active Directory?**

   * *Exemplo: Usuários onde o atributo "Empresa" do Workday contém o valor "Contoso", e o atributo "Worker_Type" contém "Normal"*

* **Como os usuários são roteados em diferentes OUs (unidades organizacionais)?**

   * *Exemplo: Os usuários são roteados para OUs que correspondem a um local do escritório, conforme definido nos atributos "Município" e "Country_Region_Reference" do Workday*

* **Como os atributos a seguir devem ser preenchidos no Active Directory?**

   * Nome Comum (cn)
      * *Exemplo: Use o valor User_ID do Workday, conforme definido pelos recursos humanos*
      
   * ID de funcionário (employeeId)
      * *Exemplo: Use o valor Worker_ID do Workday*
      
   * Nome de conta SAM (sAMAccountName)
      * *Exemplo: Use o valor User_ID do Workday, filtrado por meio da expressão de provisionamento do Azure AD para remover caracteres inválidos*
      
   * Nome principal do usuário (userPrincipalName)
      * *Exemplo: Use o valor User_ID do Workday, com uma expressão de provisionamento do Azure AD para acrescentar um nome de domínio*

* **Como os usuários devem corresponder entre o Workday e o Active Directory?**

  * *Exemplo: Os usuários com um valor "Worker_ID" específico do Workday correspondem com usuários do Active Directory em que "employeeID" tem o mesmo valor. Se o valor de Worker_ID não for encontrado no Active Directory, crie um novo usuário.*
  
* **A floresta do Active Directory já contém as IDs de usuário necessárias para a lógica de correspondência funcionar?**

  * *Exemplo: Se esta for uma nova implantação do Workday, é altamente recomendável que o Active Directory seja preenchida previamente com os valores de Worker_ID do Workday corretos (ou valor de ID exclusivo de preferência) para manter a lógica de correspondência mais simples possível.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionar o aplicativo de conector de provisionamento e criar a conexão para Workday

**Para configurar o Workday para provisionamento do Active Directory:**

1.  Acesse <https://portal.azure.com>

2.  Na barra de navegação esquerda, selecione **Azure Active Directory**

3.  Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4.  Selecione **Adicionar um aplicativo** e a categoria **Todos**.

5.  Pesquise por **Provisionamento do Workday para Active Directory** e adicione esse aplicativo da galeria.

6.  Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7.  Altere o **Modo de** **Provisionamento** para **Automático**

8.  Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**  – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. **Deve ser semelhante a: username@contoso4**

   * **Senha do administrador –** Digite a senha da conta do sistema de integração do Workday

   * **URL do locatário –** Digite a URL para o ponto de extremidade de serviços Web do Workday para seu locatário. Isso deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde contoso4 é substituído pelo nome do locatário correto e wd3-impl é substituído pela cadeia de caracteres de ambiente correta.

   * **Florestas do Active Directory -** O "Nome" da sua floresta do Active Directory, conforme retornado pelo comando do powershell Get-ADForest. Geralmente, é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner do Active Directory -** Digite a cadeia de caracteres do contêiner que contém todos os usuários em sua floresta do AD. Exemplo: *UO= Usuários Padrão,UO=Usuários,DC=contoso,DC=teste*

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se as credenciais do Workday são válidas no Workday. 

![Portal do Azure](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos 

Nesta seção, você irá configurar o fluxo de dados de usuário do Workday para o Active Directory.

1.  Na guia Provisionamento, em **Mapeamentos**, clique em **Sincronizar Trabalhos do Workday para Locais**.

2.  No campo **Escopo do Objeto de Origem** é possível selecionar quais conjuntos de usuários no Workday devem estar no escopo de provisionamento para AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários no Workday". Filtros de exemplo:

   * Exemplo: Escopo para usuários com IDs de Trabalho entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: COINCIDIR.EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: somente funcionários e não trabalhadores contingentes 

      * Atributo: EmployeeID

      * Operador: NÃO NULO

3.  No campo **Ações do Objeto de Destino** é possível filtrar globalmente quais ações podem ser executadas no Active Directory. **Criar** e **Atualizar** são as mais comuns.

4.  Na seção **Mapeamentos de atributos** é possível definir como os atributos individuais do Workday mapeiam atributos do Active Directory.

5. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Grava o valor do atributo do Workday no atributo do AD, sem alterações

         * **Constante** - Grava um valor de cadeia de caracteres constante estático para o atributo do AD

         * **Expressão** – Permite gravar um valor personalizado para o atributo do AD baseado em um ou mais atributos do Workday. [Para obter mais informações, consulte este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

      * **Atributo de origem** - O atributo de usuário do Workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizar a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** –  O atributo de usuário no Active Directory.

      * **Corresponder objetos utilizando esse atributo** – Se esse mapeamento deverá ou não ser utilizado para identificar usuários do Workday e Active Directory com exclusividade. Normalmente, isso é definido no campo ID de Trabalho para o Workday que geralmente é mapeado para um dos atributos do ID do Funcionário no Active Directory.

      * **Precedência de correspondência** – Vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.

      * **Aplicar esse mapeamento**
       
         * **Sempre** – Aplicar esse mapeamento nas ações de atualização e criação de usuário

         * **Somente durante a criação** - Aplicar esse mapeamento somente nas ações de criação de usuário

6. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

![Portal do Azure](./media/workday-inbound-tutorial/WD_2.PNG)

**A seguir, estão alguns exemplos de mapeamentos de atributos entre o Workday e o Active Directory, com algumas expressões comuns**

-   A expressão que mapeia para o atributo parentDistinguishedName é utilizada para provisionar um usuário para OUs diferentes baseado em um ou mais atributos de origem do Workday. Este exemplo coloca os usuários em OUs diferentes com base na cidade que eles estão.

-   O atributo userPrincipalName do Active Directory é gerado ao concatenar a ID de usuário do Workday com um sufixo de domínio

-   [Há documentação sobre expressões de gravação aqui](../manage-apps/functions-for-customizing-application-data.md). Isso inclui exemplos sobre como remover caracteres especiais.

  
| ATRIBUTO WORKDAY | ATRIBUTO DO ACTIVE DIRECTORY |  ID DE CORRESPONDÊNCIA? | CRIAR / ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Sim** | Gravado na criação somente |
| **UserID**    |  cn    |   |   Gravado na criação somente |
| **Join ("@", [UserID], "contoso.com")**   | userPrincipalName     |     | Gravado na criação somente 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Gravado na criação somente |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | Criar + atualizar |
| **Nome**   | givenName       |     |    Criar + atualizar |
| **Sobrenome**   |   sn   |     |  Criar + atualizar |
| **PreferredNameData**  |  displayName |     |   Criar + atualizar |
| **Empresa**         | company   |     |  Criar + atualizar |
| **SupervisoryOrganization**  | department  |     |  Criar + atualizar |
| **ManagerReference**   | manager  |     |  Criar + atualizar |
| **BusinessTitle**   |  título     |     |  Criar + atualizar | 
| **AddressLineData**    |  streetAddress  |     |   Criar + atualizar |
| **Município**   |   l   |     | Criar + atualizar |
| **CountryReferenceTwoLetter**      |   co |     |   Criar + atualizar |
| **CountryReferenceTwoLetter**    |  c  |     |         Criar + atualizar |
| **CountryRegionReference** |  st     |     | Criar + atualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualizar |
| **PostalCode**  |   postalCode  |     | Criar + atualizar |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Criar + atualizar |
| **Fax**      | facsimileTelephoneNumber     |     |    Criar + atualizar |
| **Móvel**  |    Serviço Móvel       |     |       Criar + atualizar |
| **LocalReference** |  preferredLanguage  |     |  Criar + atualizar |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Criar + atualizar |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Parte 3: Configurar o agente de sincronização local

Para provisionar em Active Directory local, um agente deverá ser instalado em um servidor ingressado no domínio na floresta do Active Directory desejada. As credenciais do administrador de domínio (ou administrador corporativo) são necessárias para concluir o procedimento.

**[Você pode baixar o agente de sincronização local aqui](https://go.microsoft.com/fwlink/?linkid=847801)**

Após instalar o agente, execute os comandos do Powershell a seguir para configurar o agente para o seu ambiente.

**Comando #1**

> cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent" Agent\\Modules\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Comando #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Entrada: para "Nome do Diretório", digite o nome da floresta do AD, conforme inserido na parte \#2
* Entrada: nome de usuário e senha de administrador para floresta do Active Directory

>[!TIP]
> Se você receber a mensagem de erro "o relacionamento entre o domínio primário e o domínio confiável falhou", é porque o computador local está em um ambiente em que vários domínios ou florestas do Active Directory estão configurados e pelo menos uma relação de confiança configurada está falhando ou não operacional. Para resolver o problema, corrija ou remova a relação de confiança quebrada.

**Comando #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Entrada: nome de usuário e senha de administrador global para seu locatário do Azure AD

>[!IMPORTANT]
>Atualmente, há um problema conhecido com credenciais de administrador global que não funcionam ao se usar um domínio personalizado (por exemplo: admin@contoso.com). Como alternativa, criar e usar uma conta de administrador global com um domínio onmicrosoft.com (exemplo: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>Atualmente, há um problema conhecido com credenciais de administrador global que não funcionarão se a autenticação multifator delas estiver habilitada. Como alternativa, desabilite a autenticação multifator para o administrador global.

**Comando #4**

> Get-AdSyncAgentProvisioningTasks

* Ação: os dados de confirmação são retornados. Esse comando descobre automaticamente os aplicativos de provisionamento do Workday no seu locatário do Azure AD. Saída de exemplo:

> Nome: Minha Floresta do AD
>
> Habilitado: verdadeiro
>
> DirectoryName : mydomain.contoso.com
>
> Credenciado: falso
>
> Identificador: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Comando #5**

> Start-AdSyncAgentSynchronization -Automatic

**Comando #6**

> net stop aadsyncagent

**Comando #7**

> net start aadsyncagent

>[!TIP]
>Além de comandos "net" no Powershell, o serviço do agente de sincronização também pode ser iniciado e interrompido usando **Services.msc**. Se você receber erros ao executar os comandos do Powershell, certifique-se de que o **Agente de provisionamento do Microsoft Azure AD Connect** está sendo executado no **Services.msc**.

![Serviços](./media/workday-inbound-tutorial/Services.png)  

**Configuração adicional para os clientes na União Europeia**

Se o seu locatário do Azure Active Directory estiver localizado em um dos data centers da UE, siga as etapas adicionais a seguir.

1. Abra **Services.msc** e pare o **serviço** do Microsoft Azure AD Connect Provisioning Agent.
2. Vá para a pasta de instalação do agente (exemplo: C:\Program Files\Agente de provisionamento do Microsoft Azure AD Connect).
3. Abra o **SyncAgnt.exe.config** em um editor de texto.
4. Substitua https://manage.hub.syncfabric.windowsazure.com/Management por **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Substitua https://provision.hub.syncfabric.windowsazure.com/Provisioning por **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Salve o arquivo **SyncAgnt.exe.config**.
7. Abra o **Services.msc** e inicie o serviço do **Agente de provisionamento do Microsoft Azure AD Connect**.

**Solucionar problemas do agente**

O [Log de eventos do Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) na computador do Windows Server que hospeda o agente contém eventos para todas as operações executadas pelo agente. Para exibir esses eventos:
    
1. Abra **Eventvwr.msc**.
2. Selecione **Logs do Windows > Aplicativo**.
3. Exiba todos os eventos registrados na fonte **AADSyncAgent**. 
4. Verifique se há erros e avisos.

Se houver um problema nas permissões com as credenciais do Active Directory ou Azure Active Directory fornecidas nos comandos do Powershell, você verá um erro como este: 
    
![Logs de eventos](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Parte 4: Iniciar o serviço
Após concluir as partes de 1 a 3, você poderá iniciar o serviço de provisionamento no portal do Azure.

1.  Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Em**.

2. Clique em **Salvar**.

3. Isso dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no Workday.

4. A qualquer momento, verifique os **Logs de auditoria** no portal do Azure para ver as ações que o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização realizados pelo serviço de provisionamento, tais como aqueles em que os usuários estão sendo lidos do Workday e, posteriormente adicionados ou atualizados no Active Directory. **[Consulte o guia de relatórios de provisionamento para obter instruções detalhadas sobre como ler os logs de auditoria](../manage-apps/check-status-user-account-provisioning.md)**

1.  Verifique o [Log de eventos do Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) no computador do Windows Server que hospeda o agente para erros ou avisos. Esses eventos podem ser visualizados iniciando-se o **Eventvwr.msc** no servidor e selecionando **Logs do Windows > Aplicativo**. Todas as mensagens relacionadas ao provisionamento são registradas na fonte **AADSyncAgent**.

6. Após a conclusão, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

![Portal do Azure](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configurando o provisionamento de usuário para o Azure Active Directory
Como você irá configurar o provisionamento no Azure Active Directory dependerá dos requisitos de provisionamento, conforme detalhado na tabela a seguir.

| Cenário | Solução |
| -------- | -------- |
| **Os usuários precisam ser provisionados para Active Directory e Azure AD** | Utilize o **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **Os usuários precisam ser provisionados somente para Active Directory** | Utilize o **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **Os usuários precisam ser provisionados somente para Azure AD (somente na nuvem)** | Utilize o aplicativo **Workday para provisionamento do Azure Active Directory** na galeria de aplicativos |

Para obter instruções sobre a configuração do Azure AD Connect, consulte a [ documentação do Azure AD Connect](../connect/active-directory-aadconnect.md).

As seções a seguir descrevem a configuração de uma conexão entre o Workday e o Azure AD para provisionar usuários somente na nuvem.

> [!IMPORTANT]
> Siga o procedimento abaixo apenas se você tiver usuários somente na nuvem que precisam ser provisionados para o Azure AD e não para o Active Directory local.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionar o aplicativo de conectores de provisionamento do AD do Azure e criar a conexão ao Workday

**Para configurar o provisionamento do Workday to Azure Active Directory para usuários somente na nuvem:**

1.  Vá para <https://portal.azure.com>.

2.  Na barra de navegação esquerda, selecione **Azure Active Directory**

3.  Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4.  Selecione **Adicionar um aplicativo** e, em seguida, selecione a categoria **Todos**.

5.  Pesquise por **Workday para provisionamento do Azure AD** e adicione esse aplicativo da galeria.

6.  Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7.  Altere o **Modo de** **Provisionamento** para **Automático**

8.  Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**  – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Deve ser semelhante a: username@contoso4

   * **Senha do administrador –** Digite a senha da conta do sistema de integração do Workday

   * **URL do locatário –** Digite a URL para o ponto de extremidade de serviços Web do Workday para seu locatário. Isso deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde contoso4 é substituído pelo nome do locatário correto e wd3-impl é substituído pela cadeia de caracteres de ambiente correta. Se essa URL não for conhecida, trabalhe com seu representante de suporte ou parceiro de integração do Workday para determinar a URL correta a ser usada.

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

   * Clique no botão **Conexão de Teste**.

   * Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se o URL do Workday e as credenciais são válidas no Workday.

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos 

Nesta seção, você irá configurar o fluxo dos dados do usuário do Workday para o Azure Active Directory de usuários somente na nuvem.

1. Na guia Provisionamento em **Mapeamentos**, clique em **Sincronizar Trabalhos para o Microsoft Azure Active Directory**.

2. No campo **Escopo do Objeto de Origem** é possível selecionar quais conjuntos de usuários no Workday devem estar no escopo de provisionamento para Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários no Workday". Filtros de exemplo:

   * Exemplo: Escopo para usuários com IDs de Trabalho entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: COINCIDIR.EXREG

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Somente trabalhadores contingentes e não funcionários regulares

      * Atributo: ContingentID

      * Operador: NÃO NULO

3. No campo **Ações do Objeto de Destino** é possível filtrar globalmente quais ações podem ser realizadas no Azure AD. **Criar** e **Atualizar** são as mais comuns.

4. Na seção **Mapeamentos de atributos** é possível definir como os atributos individuais do Workday mapeiam atributos do Active Directory.

5. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual dá suporte para essas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – Grava o valor do atributo do Workday no atributo AD, sem alterações

      * **Constante** - Grava um valor de cadeia de caracteres constante estático para o atributo do AD

      * **Expressão** – Permite gravar um valor personalizado para o atributo do AD baseado em um ou mais atributos do Workday. [Para obter mais informações, consulte este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

   * **Atributo de origem** - O atributo de usuário do Workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizar a lista de atributos de usuário do Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valor padrão** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento irá gravar esse valor.
            A configuração mais comum é deixar em branco.

   * **Atributo de destino** – O atributo do usuário no Azure AD.

   * **Corresponder objetos utilizando esse atributo** – Se esse mapeamento deverá ou não ser utilizado para identificar usuários entre o Workday e o Azure AD com exclusividade. Normalmente, isso é definido no campo ID de Trabalho para o Workday que normalmente é mapeado para o atributo ID do Funcionário (novo) ou um atributo de extensão no Azure AD.

   * **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.

   * **Aplicar esse mapeamento**

     * **Sempre** – Aplicar esse mapeamento nas ações de atualização e criação de usuário

     * **Somente durante a criação** - Aplicar esse mapeamento somente nas ações de criação de usuário

6. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

### <a name="part-3-start-the-service"></a>Parte 3: Iniciar o serviço
Após concluir as partes de 1 a 2, você poderá iniciar o serviço de provisionamento.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Ligado**.

2. Clique em **Salvar**.

3. Isso dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no Workday.

4. Os eventos de sincronização individuais podem ser visualizados na guia **Logs de Auditoria**. **[Consulte o guia de relatórios de provisionamento para obter instruções detalhadas sobre como ler os logs de auditoria](../manage-apps/check-status-user-account-provisioning.md)**

5. Após a conclusão, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configurando write-back de endereços de email para Workday
Siga essas instruções para configurar o write-back de endereços de email do usuário do Azure Active Directory para Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionar o aplicativo de conector de provisionamento e criar a conexão para Workday

**Para configurar o Workday para provisionamento do Active Directory:**

1. Acesse <https://portal.azure.com>

2. Na barra de navegação esquerda, selecione **Azure Active Directory**

3. Selecione **Aplicativos Empresariais** e, em seguida, **Todos os Aplicativos**.

4. Selecione **Adicionar um aplicativo** e, em seguida, selecione a categoria **Todos**.

5. Pesquise por **Fazer write-back de Workday** e adicione esse aplicativo da galeria.

6. Após adicionar o aplicativo e a tela de detalhes do aplicativo for exibida, selecione **Provisionamento**

7. Altere o **Modo de** **Provisionamento** para **Automático**

8. Conclua a seção **Credenciais de Administrador**, conforme a seguir:

   * **Nome de Usuário Administrador**  – Digite o nome de usuário da conta do sistema de integração do Workday com o nome de domínio do locatário acrescentado. Deve ser semelhante a: username@contoso4

   * **Senha do administrador –** Digite a senha da conta do sistema de integração do Workday

   * **URL do locatário –** Digite a URL para o ponto de extremidade de serviços Web do Workday para seu locatário. Isso deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde contoso4 é substituído pelo seu nome de locatário correto e wd3-impl é substituído pela cadeia de caracteres de ambiente correta (se necessário).

   * **Email de notificação –** Digite seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

   * Clique no botão **Conexão de Teste**. Se o teste de conexão for bem-sucedido, clique no botão **Salvar** na parte superior. Se falhar, verifique novamente se o URL do Workday e as credenciais são válidas no Workday.

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos 

Nesta seção, você irá configurar o fluxo de dados de usuário do Workday para o Active Directory.

1. Na guia Provisionamento em **Mapeamentos**, clique em **Sincronizar Usuários do Microsoft Azure Active Directory para Workday**.

2. No campo **Escopo do Objeto de Origem** é possível, opcionalmente, filtrar quais conjuntos de usuários no Azure Active Directory devem fazer write-back dos endereços de email para o Workday. O escopo padrão é "todos os usuários no Azure AD". 

3. Na seção **Mapeamentos de atributo**, atualize a ID correspondente para indicar o atributo no Azure Active Directory em que a ID de trabalho do Workday ou a ID de funcionário é armazenada. Um método de correspondência popular é sincronizar o ID de Trabalho do Workday ou o ID de Funcionário para extensionAttribute1-15 no Azure AD e, em seguida, utilizar esse atributo no Azure AD para corresponder os usuários no Workday. 

4. Para salvar seus mapeamentos, clique em **Salvar** na parte superior da seção Mapeamento de Atributos.

### <a name="part-3-start-the-service"></a>Parte 3: Iniciar o serviço
Após concluir as partes de 1 a 2, você poderá iniciar o serviço de provisionamento.

1. Na guia **Provisionamento**, defina o **Status de Provisionamento** para **Ligado**.

2. Clique em **Salvar**.

3. Isso dará início à sincronização inicial, o que poderá demorar algumas horas dependendo de quantos usuários estiverem no Workday.

4. Os eventos de sincronização individuais podem ser visualizados na guia **Logs de Auditoria**. **[Consulte o guia de relatórios de provisionamento para obter instruções detalhadas sobre como ler os logs de auditoria](../manage-apps/check-status-user-account-provisioning.md)**

5. Após a conclusão, um relatório de resumo de auditoria será gravado na guia **Provisionamento** conforme mostrado abaixo.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizando a lista de atributos de usuário do Workday
O provisionamento de aplicativos do Workday para o Active Directory e o Azure AD incluem uma lista padrão de atributos de usuário do Workday da qual você pode selecionar. No entanto, essas listas não são abrangentes. O Workday é compatível com centenas de possibilidades de atributos de usuário, que podem ser padrão ou exclusivos para seu locatário do Workday. 

O serviço de provisionamento do Azure AD é compatível com possibilidade de personalizar sua lista ou seu atributo do Workday a fim de incluir todos os atributos expostos na operação [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) da API de Recursos Humanos.

Para fazer isso, você deve usar o [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que você deseja usar e, em seguida, adicioná-los à sua configuração de provisionamento usando o editor de atributo avançado no Portal do Azure.

**Para recuperar uma expressão XPath para um atributo de usuário do Workday:**

1. Baixe e instale o [Workday Studio](https://community.workday.com/studio-download). Você precisará de uma conta da comunidade do Workday para acessar o instalador.

2. Baixe o arquivo WDSL Human_Resources do Workday desta URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Inicie o Workday Studio.

4. Na barra de comandos, selecione a opção **Workday > Testar o serviço Web no Testador**.

5. Selecione **Externo** e selecione o arquivo WSDL Human_Resources que você baixou na etapa 2.

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. Defina o campo **local** como `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mas substituindo "IMPL-CC" pelo tipo de instância real e "LOCATÁRIO" pelo seu nome do locatário real.

7. Defina **Operação** como **Get_Workers**

8.  Clique no pequeno link **configurar** abaixo dos painéis de Solicitação/Resposta para definir suas credenciais do Workday. Marque a opção **Autenticação** e, em seguida, insira o nome de usuário e senha da sua conta do sistema de integração do Workday. Não se esqueça de formatar o nome de usuário como name@tenant e deixar a opção **WS-Security UsernameToken** selecionada.

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. Selecione **OK**.

10. No painel **Solicitação**, cole o XML abaixo e defina **Employee_ID** como a ID de funcionário de um usuário real em seu locatário do Workday. Selecione um usuário que tenha o atributo que você deseja extrair preenchido.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Clique em **Enviar Solicitação** (seta verde) para executar o comando. Se for bem-sucedido, a resposta deverá aparecer no painel **Resposta**. Verifique a resposta para garantir que ela tenha os dados da ID de usuário que você inseriu e não um erro.

12. Se for bem-sucedido, copie o XML do painel **Resposta** e salve-o como um arquivo XML.

13. Na barra de comandos do Workday Studio, selecione **File> Open File ...** e abra o arquivo XML que você salvou. Isso abre o arquivo no editor de XML do Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. Na árvore de arquivos, navegue por **/env:Envelope > env:Body > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** para localizar os dados do seu usuário. 

15. Em **wd:Worker**, localize o atributo que você deseja adicionar e selecione-o.

16. Copie a expressão XPath para o atributo que você selecionou no campo **Caminho de Documento**.

1. Remova o prefixo **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** da expressão copiada.

18. Se o último item da expressão copiada for um nó (por exemplo: "/wd:Birth_Date"), acrescente **/text()** ao final da expressão. Isso não é necessário se o último item for um atributo (exemplo: "/@wd: type").

19. O resultado deve ser algo como `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Isso é o que você copiará no Portal do Azure.


**Para adicionar o atributo de usuário personalizado do Workday à sua configuração de provisionamento:**

1. Inicie o [Portal do Azure](https://portal.azure.com) e navegue até a seção Provisionamento do aplicativo de provisionamento do Workday, conforme descrito anteriormente neste tutorial.

2. Defina o **Status de Provisionamento** como **Desativado** e selecione **Salvar**. Isso ajudará a garantir que as alterações entrem em vigor somente quando você estiver pronto.

3. Em **Mapeamentos**, selecione **Sincronizar Trabalhos para Locais** (ou **Sincronizar Trabalhos com o Azure AD**).

4. Role até a parte inferior da próxima tela e selecione **Mostrar opções avançadas**.

5. Selecione **Editar lista de atributos do Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Role até a parte inferior da lista de atributos, até o local em que estão os campos de entrada.

7. Em **Nome**, insira um nome de exibição para o atributo.

8. Em **Tipo**, selecione o tipo que corresponda adequadamente ao seu atributo (**Cadeia de Caracteres** é mais comum).

9. Em **Expressão API**, insira a expressão XPath que você copiou do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecione **Adicionar Atributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Selecione **Salvar** acima e, em seguida, **Sim**, na caixa de diálogo. Feche a tela Mapeamento de Atributo, se ainda estiver aberta.

12. Na guia **Provisionamento** principal, selecione **Sincronizar Trabalhos para Locais** (ou **Sincronizar Trabalhos com o Azure AD**) novamente.

13. Selecione **Adicionar novo mapeamento**.

14. Agora, o novo atributo deve aparecer na lista **Atributo de origem**.

15. Adicione um mapeamento para o novo atributo conforme desejado.

16. Quando terminar, lembre-se de definir novamente o **Status de Provisionamento** como **Ativado** e salvar.

## <a name="known-issues"></a>Problemas conhecidos

* Atualmente, ao executar o comando **Add-ADSyncAgentAzureActiveDirectoryConfiguration** do Powershell há um problema conhecido com credenciais de administrador global que não funcionam se um domínio personalizado for usado (exemplo: admin@contoso.com) . Como alternativa, criar e usar uma conta de administrador global no Azure AD com um domínio onmicrosoft.com (exemplo: admin@contoso.onmicrosoft.com).

* Atualmente, não há suporte para a gravação de dados para o atributo de usuário thumbnailPhoto no Active Directory local.

* O conector "Workday para Microsoft Azure AD" não tem suporte no momento em locatários do Microsoft Azure AD em que o AAD Connect está habilitado.  

* Um problema antigo com logs de auditoria que não eram exibidos nos locatários do Azure AD localizados na União Europeia foi resolvido. No entanto, uma configuração de agente adicional é necessária para locatários do Azure AD na UE. Para obter detalhes, consulte [Parte 3: Configurar o agente de sincronização local](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>Gerenciando dados pessoais

A solução de provisionamento do dia útil para o Active Directory exige que um agente de sincronização seja instalado em um servidor associado ao domínio e esse agente cria logs no log de eventos do Windows que podem conter informações de identificação pessoal.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o Workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)

