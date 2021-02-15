---
title: Azure Data Factory entitások elnevezésére vonatkozó szabályok
description: Leírja Data Factory entitások elnevezési szabályait.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f0d14760ce3e6403c9b6fe8cc7a2100aeb3f39a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372909"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – elnevezési szabályok

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Name | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| A Factory | Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket, azaz `MyDF` `mydf` ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/adatkészletek/folyamatok/adatfolyamok | Egyedi adatgyáron belül. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Az objektumok nevének betűvel kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" \\ , ""</li><li>A kötőjelek ("-") nem engedélyezettek a társított szolgáltatások, az adatfolyamatok és az adatkészletek nevében.</li></ul>  |
| Integrációs modul |Egyedi adatgyáron belül. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Az Integration Runtime neve csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Az első és az utolsó karakternek betűnek vagy számnak kell lennie. Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni.</li><li>Egymást követő kötőjelek nem engedélyezettek az integrációs modul nevében. </li></ul> |
| Adatfolyam-átalakítások | Egyedi adatfolyamon belül. A nevek megkülönböztetik a kis-és nagybetűket | <ul><li>Az adatfolyam-transzformációk nevei csak betűket és számokat tartalmazhatnak.</li><li>Az első karakternek betűnek kell lennie. </li></ul> |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. | További információ: [Azure elnevezési szabályok és korlátozások](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan hozhat létre adatgyárat a gyors üzembe helyezési útmutató lépéseit követve [: hozzon létre egy adatfeldolgozási](quickstart-create-data-factory-powershell.md) cikket. 
