---
title: Több ág a leképezési adatfolyamban
description: Adatfolyamok replikálása több ággal rendelkező leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588751"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Új ág létrehozása leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adjon hozzá egy új ágat több művelet és átalakítás ugyanazon az adatfolyamon való végrehajtásához. Új ág hozzáadása akkor hasznos, ha ugyanazt a forrást szeretné használni több fogadóhoz vagy az adatok önálló egymáshoz való egymáshoz kötéséhez.

A transzformációs listából a többi átalakításhoz hasonlóan új ág is hozzáadható. **Az Új ág** csak akkor lesz elérhető műveletként, ha már létezik átalakítás az elágaztatási műveletet követően.

![Képernyőkép az Új ág lehetőségről a Több bemenet /kimenet menüben.](media/data-flow/new-branch2.png "Új ág hozzáadása")

Az alábbi példában az adatfolyam a taxis utazások adatait olvassa. A napi és a beszállító által összesített kimenetre van szükség. Ahelyett, hogy két különálló adatfolyamot hoz létre, amelyek ugyanattól a forrástól olvasnak, új ágat is hozzá lehet adni. Így mindkét összesítés végrehajtható ugyanannak az adatfolyamnak a részeként. 

![Képernyőkép az adatfolyamról a forrásból származó két ággal.](media/data-flow/new-branch.png "Új ág hozzáadása")

> [!NOTE]
> Ha a plusz (+) jelre kattintva átalakításokat ad hozzá a gráfhoz, az Új ág lehetőség csak akkor látható, ha további átalakítási blokkok is vannak. Ennek az az oka, hogy a New Branch létrehoz egy hivatkozást a meglévő streamre, és további, upstream feldolgozási műveleteket igényel a művelethez. Ha nem látja az Új ág lehetőséget, először adjon hozzá egy Származtatott oszlopot vagy más átalakítást, majd térjen vissza az előző blokkhoz, és az Új ág lehetőség látható.

## <a name="next-steps"></a>Következő lépések

Az elágaztatás után előfordulhat, hogy [adatfolyam-átalakításokat szeretne használni](data-flow-transformation-overview.md)
