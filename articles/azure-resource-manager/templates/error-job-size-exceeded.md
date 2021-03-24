---
title: A feladatok mérete túllépte a hibát.
description: Leírja, hogyan lehet elhárítani a hibákat, amikor a feladatok mérete vagy a sablon túl nagy.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889365"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Túllépte a feladatokhoz tartozó hibák elhárítását

Ez a cikk a **JobSizeExceededException** és a **DeploymentJobSizeExceededException** hibák elhárítását ismerteti.

## <a name="symptom"></a>Hibajelenség

A sablonok telepítésekor hibaüzenetet kap, amely túllépte az üzemelő példány korlátait.

## <a name="cause"></a>Ok

Ez a hibaüzenet akkor jelenik meg, ha az üzemelő példány túllépi az engedélyezett határértékek valamelyikét. Ez a hiba általában akkor jelenik meg, ha a sablon vagy a központi telepítést futtató művelet túl nagy.

Az üzembe helyezési feladatok nem haladhatják meg az 1 MB-ot. A feladat metaadatokat tartalmaz a kérésről. A nagyobb sablonoknál előfordulhat, hogy a metaadatok és a sablon mérete együttesen meghaladja a feladatok megengedett méretét.

A sablon mérete nem haladhatja meg a 4 MB-ot. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután kibővítettük azokat az erőforrás-definíciókat, amelyek számos példány létrehozásához a [másolást](copy-resources.md) használják. A végső állapotba beleszámítanak a változók és paraméterek feloldott értékei is.

A sablon egyéb korlátai a következők:

* 256 paraméterek
* 256 változók
* 800-erőforrások (beleértve a példányszámot)
* 64 kimeneti értékek
* 24 576 karakter egy sablon kifejezésében

Ha a másolási hurkokat használja az erőforrás üzembe helyezéséhez, ne használja a hurok nevét függőségként:

```json
dependsOn: [ "nicLoop" ]
```

Ehelyett használja az erőforrás példányát attól a huroktól, amelyre szüksége van. Például:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>1. megoldás – sablon leegyszerűsítése

Az első lehetőség a sablon egyszerűsítése. Ez a beállítás akkor működik, ha a sablon számos különböző erőforrástípust telepít. Érdemes lehet a sablont [csatolt sablonokba](linked-templates.md)osztani. Az erőforrástípusok logikai csoportokba oszthatók, és minden csoporthoz hozzá kell adni egy csatolt sablont. Ha például sok hálózati erőforrást kell központilag telepítenie, akkor ezeket az erőforrásokat egy csatolt sablonba helyezheti át.

A társított sablontól függőként más erőforrásokat is beállíthat, és [értékeket kaphat a csatolt sablon kimenetéről](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>2. megoldás – a név méretének csökkentése

Próbálja megrövidíteni a [paraméterekhez](template-parameters.md), [változókhoz](template-variables.md)és [kimenetekhez](template-outputs.md)használt nevek hosszát. Ha ezeket az értékeket a másolási hurkok használatával ismétli meg, a nagyméretű nevek többször is megszorul.