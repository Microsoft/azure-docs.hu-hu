---
title: Az Azure Defender irányítópultja és funkciói
description: Ismerje meg az Azure Defender irányítópultjának funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 268781f7f14ad93ca63cbbcbef1fb697d46b33a3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977338"
---
# <a name="the-azure-defender-dashboard"></a>Az Azure Defender irányítópultja

Az Azure Defender irányítópultja a következőket biztosítja:

- Betekintést nyerhet az Azure Defender-lefedettségbe a különböző erőforrástípusok között
- A komplex veszélyforrások elleni védelem funkcióinak konfigurálására mutató hivatkozások
- A bevezetési állapot és az ügynök telepítése
- Azure Defender Threat észlelési riasztások 

Az Azure Defender irányítópultjának eléréséhez válassza az **Azure Defender** lehetőséget a Security Center menüjének Felhőbeli biztonság szakaszában.

## <a name="whats-shown-on-the-dashboard"></a>Mi jelenik meg az irányítópulton?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Példa az Azure Defender irányítópultra" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Az irányítópult a következő fejezeteket tartalmazza:

1. **Azure Defender-lefedettség** – Itt láthatja az előfizetésében lévő és az Azure Defender általi védelemre jogosult erőforrás-típusokat. Ahol szükséges, lehetősége lesz a frissítésre is. Ha az összes lehetséges erőforrást szeretné frissíteni, válassza az **összes frissítése**lehetőséget.

1. **Biztonsági riasztások terület** – ha az Azure Defender fenyegetést észlel a környezet bármely területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt szervizelési lépéseket, valamint bizonyos esetekben a logikai alkalmazások válaszként való aktiválásának lehetőségét. Az ebben a diagramban bárhová kiválasztva megnyílik a **biztonsági riasztások oldal**.

1. **Speciális védelem** – az Azure Defender számos komplex veszélyforrások elleni védelmet biztosít a virtuális gépek, az SQL-adatbázisok, a tárolók, a webalkalmazások, a hálózat és egyéb funkciók számára. Ebben a speciális védelem szakaszban megtekintheti a kiválasztott előfizetésekben lévő erőforrások állapotát az egyes védelemekhez. Válassza ki az egyiket, hogy közvetlenül a védelmi típus konfigurációs területére lépjen.

1. Információk **– a** Hírek, a javasolt olvasás és a magas prioritású riasztások részletesen bemutatják, Security Center betekintést nyerhetnek az Ön és az előfizetése szempontjából releváns biztonsági kérdések megnyomásával. Legyen szó a biztonsági rések elemzése eszköz által a virtuális gépeken észlelt magas súlyosságú CVEs, vagy a Security Center csapata egy új blogbejegyzése, itt találja az **Azure Defender irányítópultjának**elemzések ablaktábláján.




## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerheti az Azure Defender irányítópultját. 

Az Azure Defender szolgáltatással kapcsolatos további információkért lásd: [Az Azure Defender bemutatása](azure-defender.md)

> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](security-center-pricing.md)