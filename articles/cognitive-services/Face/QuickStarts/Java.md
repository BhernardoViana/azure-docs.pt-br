---
title: 'Início rápido: detectar faces em uma imagem – API de Detecção Facial, Java'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você detectará faces em uma imagem usando a API de Detecção Facial com Java.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: 42a8b13b87623b92ee0fe92db4e6a0caff5c3a1b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339108"
---
# <a name="quickstart-detect-faces-in-an-image-using-java"></a>Início rápido: detectar faces em uma imagem usando Java

Neste início rápido, você detecta rostos humanos em uma imagem usando a API de Detecção Facial.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura para executar o exemplo. É possível obter chaves de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

Use o método [Face – Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para detectar faces em uma imagem e retornar atributos de detecção facial, incluindo:

* ID de Detecção Facial: ID exclusiva usada em vários cenários de API de Detecção Facial.
* Retângulo de Detecção Facial: o lado esquerdo, a parte superior, a largura e a altura que indicam o local do rosto na imagem.
* Pontos de Referência: uma matriz de 27 pontos de referência de detecção facial que aponta para as posições importantes dos componentes de detecção facial.
* Atributos faciais, incluindo idade, gênero, intensidade do sorriso, pose da cabeça e pelos faciais.

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de linha de comando no seu Java IDE favorito.
2. Substitua a classe Main pelo código a seguir (mantenha todas as instruções `package`).
3. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
4. Altere o valor `uriBase` para usar o local em que você adquiriu suas chaves de assinatura, se necessário.
5. Baixe essas bibliotecas global do repositório Maven para o diretório `lib` em seu projeto:
   * `org.apache.httpcomponents:httpclient:4.2.4`
   * `org.json:json:20170516`
6. Execute “Main”.

### <a name="face---detect-request"></a>Solicitação de Detecção Facial

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;

public class Main
{
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    public static void main(String[] args)
    {
        HttpClient httpclient = new DefaultHttpClient();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

### <a name="face---detect-response"></a>Resposta de Face – Detect

Uma resposta com êxito é retornada em JSON.

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Próximas etapas

Saiba como criar um aplicativo Android que usa o serviço de Detecção Facial para detectar faces em uma imagem. O aplicativo exibe a imagem com um quadro desenhado ao redor de cada face.

> [!div class="nextstepaction"]
> [Tutorial: introdução à API de Detecção Facial no Android](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
