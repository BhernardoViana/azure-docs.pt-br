---
title: Geometrias geojson estendidas no Azure Maps | Microsoft Docs
description: Saiba como estender geometrias GeoJSON no Azure Mapas
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881992"
---
# <a name="extended-geojson-geometries"></a>Geometrias geojson estendidas

O Azure Mapas fornece uma lista de APIs poderoso para pesquisa interna/ao longo de recursos geográficos.
Essas APIs padronizam a [especificação geojson][1] para representar os recursos geográficos (por exemplo: limites de estado, rotas).  

A [especificação geojson][1] só dá suporte às seguintes geometrias:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Algumas APIs do Azure Maps (por exemplo: [Pesquisar dentro de geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) aceite geometrias como "Circle", que não fazem parte da [especificação geojson][1].

Este artigo fornece uma explicação detalhada sobre como o Azure Maps estende a [especificação geojson][1] para representar determinadas geometrias.

## <a name="circle"></a>Círculo

A `Circle` [especificação geojson][1]não dá suporte à geometria. Usamos um `GeoJSON Point Feature` objeto para representar um círculo.

Uma geometria `Circle` representada usando o objeto `GeoJSON Feature` __deve__ conter o seguinte:

- Centralizado

    O centro do círculo é representado usando um `GeoJSON Point` objeto.

- Raio

    O círculo `radius` é representada usando as propriedades de `GeoJSON Feature`. O valor de raio está em _metros_ e deve ser do tipo `double`.

- SubType

    A geometria do círculo também deve conter a propriedade `subType`. Essa propriedade deve ser uma parte das `GeoJSON Feature`Propriedades do e seu valor deve ser _Circle_

#### <a name="example"></a>Exemplo

Veja como você representará um círculo centralizado em (Latitude: 47,639754, Longitude:-122,126986) com um raio igual a 100 metros, usando um `GeoJSON Feature` objeto:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Nele

A `Rectangle` [especificação geojson][1]não dá suporte à geometria. Usamos um `GeoJSON Polygon Feature` objeto para representar um retângulo. A extensão de retângulo é usada principalmente pelo módulo de ferramentas de desenho do SDK da Web.

Uma geometria `Rectangle` representada usando o objeto `GeoJSON Polygon Feature` __deve__ conter o seguinte:

- Corner

    Os cantos do retângulo são representados usando as coordenadas de `GeoJSON Polygon` um objeto. Deve haver cinco coordenadas, uma para cada canto e uma quinta coordenada igual à primeira para fechar o anel do polígono. Essas coordenadas serão consideradas alinhadas e giradas conforme desejado pelo desenvolvedor.

- SubType

    A geometria do retângulo também deve conter `subType` a propriedade. Essa propriedade deve ser uma parte das `GeoJSON Feature`Propriedades do e seu valor deve ser _Rectangle_

### <a name="example"></a>Exemplo

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre dados geojson no Azure Maps:

> [!div class="nextstepaction"]
> [Formato geojson de limite geográfico](geofence-geojson.md)

Examine o Glossário de termos técnicos comuns associados a aplicativos do Azure Maps e do local Intelligence:

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
