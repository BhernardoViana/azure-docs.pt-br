---
title: Início Rápido do Projeto Acústico com o Unity
titlesuffix: Azure Cognitive Services
description: Ao usar o conteúdo de exemplo, experimente os controles de design do Projeto Acústico no Unity e implante o Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 0ea020ca76381a4ae5d6b6e480c94e63f9aa2dab
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933102"
---
# <a name="project-acoustics-unity-quickstart"></a>Início Rápido do Unity do Projeto Acústico
Use o conteúdo de exemplo do Projeto Acústico para Unity para fazer experimento com controles de design com suporte de simulação.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows
* [Pacote de conteúdo de exemplo do Projeto Acústico](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena do Unity com geometria, fontes de som e controles de jogo
* Plugin do Projeto Acústico 
* Ativos acústicos preparados para a cena

## <a name="import-the-sample-package"></a>Importe o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity. 
* No Unity, vá para **ativos > Importar pacote > pacote personalizado...**

    ![Captura de tela das opções Importar Pacote do Unity](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

* Clique no botão **Importar** para integrar o pacote do Unity ao seu projeto  
  
    ![Captura da caixa de diálogo Importar Pacote do Unity](media/import-dialog.png)  

Se você estiver importando o pacote em um projeto existente, consulte [Integração ao Unity](unity-integration.md) para obter etapas e notas adicionais.

>[!NOTE] 
>Vários erros serão exibidos no log do console após a conclusão da importação.  Continue na próxima etapa e reinicie o Unity.

## <a name="restart-unity"></a>Reinicie o Unity
A parte de assar do kit de ferramentas de acústica requer a versão de tempo de execução de script do .NET 4.x. A importação de pacotes atualizará as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito.

Você pode verificar se essa configuração entrou em vigor abrindo as **Configurações do Player**:

![Captura de tela do painel Configurações do Player do Unity](media/player-settings.png)  

![Captura de tela do painel Configurações do Player do Unity com .NET 4.5 selecionado](media/net45.png)  

>[!NOTE]
>A captura de tela é obtida do Unity 2018.x. As versões mais recentes do Unity podem variar.

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela do bake do Projeto Acústico
Escolha **Window > Acústica** no menu Unity:

![Captura de tela do editor do Unity com a opção de menu da janela Acústico realçada](media/window-acoustics.png)

Uma nova janela flutuante chamada **Acústica** será exibida.  É nessa janela que as propriedades da simulação acústica são definidas.

![Captura do editor do Unity com a janela Acústica aberta](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-design-controls"></a>Experimente com os controles de design
Abra a cena de exemplo na pasta **ProjectAcousticsSample** e clique no botão reproduzir no editor do Unity. Use W, A, S, D e o mouse para mover-se. Para comparar o som da cena com e sem acústica, pressione o botão **R** até que o texto de sobreposição fique vermelho e informe "Acústica: desabilitada". Para ver os atalhos de teclado de outros controles, pressione **F1**. Os controles também podem ser usados clicando com o botão direito do mouse para selecionar a ação a ser executada e clicando com o botão esquerdo para executar a ação.

O script **AcousticsAdjust** é anexado às fontes de som na cena de exemplo, que permite os parâmetros de design por origem. 

![Captura de tela do script AcousticsAdjust do Unity](media/acoustics-adjust.png)

O exemplo a seguir explora alguns dos efeitos que podem ser produzidos com os controles fornecidos. Para obter informações detalhadas sobre cada controle, consulte o [Tutorial de Design do Unity do Projeto Acústico](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação de distância
O DSP de áudio fornecido pelo plug-in spatializer do Unity do **Projeto Acústico** respeita a atenuação baseada em distância por origem incorporada no Editor do Unity. Controles de atenuação de distância estão na **Audio Source** componente encontrado na **Inspetor** fontes de painel de som, em **configurações de som 3D**:

![Captura de tela do painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

O Projeto Acústico executa o cálculo em uma caixa de “região da simulação” centralizada em torno do local do player. Uma vez que os ativos acústicos no pacote de exemplo foram preparados com um tamanho de região de simulação de 45 m em torno do player, a atenuação do som deve ser projetada para cair para 0 em cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
* Se o multiplicador de **Oclusão** for maior do que 1 (o padrão é 1), a oclusão será exagerada. Defini-la para menos de 1 torna o efeito da oclusão mais sutil.

* Para habilitar a transmissão através da parede, mova o controle deslizante **Transmissão (dB)** para seu nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma fonte
* Para alterar como a umidade muda rapidamente com a distância, use a **Distorção de Distância Perceptiva**. O **Projeto Acústico** calcula os níveis de umidade em todo o espaço a partir da simulação, que variam levemente com a distância e fornecem dicas de distância perceptiva. Aumentar a distorção de distância exagera esse efeito aumentando os níveis de umidade relacionados à distância. Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil. Esse efeito também pode ser ajustado em detalhes mais refinados, ajustando a **Umidade (dB)** .

* Aumente o tempo de decaimento por todo o espaço, ajustando a **Escala de Tempo de Decaimento**. Se o resultado de simulação para um par de localização de ouvinte de origem específico for um tempo de decaimento de 1,5 s e a **Escala de Tempo de Decaimento** for definida como 2, o tempo de decaimento aplicado à fonte será de 3 s.

## <a name="next-steps"></a>Próximas etapas
* Leia os detalhes completos sobre os [controles de design do Projeto Acústico baseado no Unity](unity-workflow.md)
* Explore mais os conceitos relacionados ao [processo de design](design-process.md)
* [Crie uma conta do Azure](create-azure-account.md) para explorar os processos de pré-preparação e de preparação

