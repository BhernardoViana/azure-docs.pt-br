---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130060"
---
É recomendável usar pelo menos 30 imagens por marca no conjunto de treinamento inicial. Também é conveniente coletar algumas imagens adicionais para testar o seu modelo após o treinamento.

Para treinar o seu modelo com eficiência, use imagens com variedade de visual. Selecione imagens com variação em:
* ângulo da câmera
* iluminação
* background
* estilo do visual
* assuntos individuais/agrupados
* tamanho
* Tipo

Além disso, certifique-se de que todas as suas imagens de treinamento atendam aos seguintes critérios:
* Formato .jpg, .png, .bmp ou .gif
* não ultrapasse 6 MB em tamanho (4 MB para imagens de previsão)
* não tenha menos de 256 pixels na borda mais curta; qualquer imagem menor que isso será escalada verticalmente de maneira automática pelo Serviço de Visão Personalizada
