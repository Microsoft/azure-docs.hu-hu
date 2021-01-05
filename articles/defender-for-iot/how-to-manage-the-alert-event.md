---
title: Riasztási események kezelése
description: A hálózatban észlelt riasztási események kezelése.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 023b33732b80198c4ed187328e8d18314f385f94
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839785"
---
# <a name="manage-alert-events"></a>Riasztási események kezelése

A riasztási események kezeléséhez a következő lehetőségek állnak rendelkezésre:

 | Művelet | Leírás |
 |--|--|
 | **Learn** | Engedélyezze az észlelt eseményt. További információkért lásd: [az események megismerése és](#about-learning-and-unlearning-events)a nem betanulás. |
 | **Tudomásul vesz** | A riasztás elrejtése egyszer az észlelt eseménynél. A riasztás újra aktiválódik, ha az eseményt újra észleli a rendszer. További információ: [Tudnivalók a nyugtázás és a nem nyugtázott eseményekről](#about-acknowledging-and-unacknowledging-events). |
 | **Némító** | A tevékenységek folyamatos figyelmen kívül hagyása azonos eszközökkel és hasonló adatforgalommal. További információ: [az elnémítással és az elnémítással kapcsolatos események](#about-muting-and-unmuting-events). |

## <a name="about-learning-and-unlearning-events"></a>Az események megismerése és a nem betanulás

Azok az események, amelyek jelzik, hogy a megtanult hálózattól való eltérések érvényes hálózati módosításokat tükröznek. Ilyenek lehetnek például egy új, a hálózathoz csatlakozó vagy a belső vezérlőprogram-frissítésre alkalmas eszköz.

Ha kiválasztja a **tanulás** lehetőséget, az érzékelő a forgalom, a konfigurációk vagy a tevékenység érvényességét veszi figyelembe. Ez azt jelenti, hogy a riasztások többé nem lesznek aktiválva az eseménynél. Ez azt is jelenti, hogy az esemény nem lesz kiszámítva, ha az érzékelő kockázatértékelést, támadási vektort és más jelentéseket hoz létre.

Olyan riasztást kap például, amely egy hálózati olvasó által korábban nem definiált eszközön a címek vizsgálatára szolgáló tevékenységet jelez. Ha ez az eszköz a hálózatra lett adva a vizsgálat céljára, utasíthatja arra az érzékelőt, hogy olvassa be az eszközt ellenőrzési eszközként.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Az észlelt keresés ablaka.":::

A megismert események nem tanulhatnak. Ha az érzékelő nem ismeri fel az eseményeket, a rendszer újraaktiválja az eseménnyel kapcsolatos riasztásokat.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Az események visszaigazolása és visszaigazolása

Bizonyos helyzetekben előfordulhat, hogy nem szeretné, hogy egy érzékelő észlelje az észlelt eseményt, vagy előfordulhat, hogy a beállítás nem érhető el. Ehelyett előfordulhat, hogy az incidens mérséklést igényel. Például:

- **Hálózati konfiguráció vagy eszköz csökkentése**: a rendszer riasztást küld, amely jelzi, hogy a rendszer új eszközt észlelt a hálózaton. A vizsgálat során kiderül, hogy az eszköz egy jogosulatlan hálózati eszköz. Az incidenst úgy kezelheti, hogy leválasztja az eszközt a hálózatról.
- **Az érzékelő konfigurációjának frissítése**: riasztást kap arról, hogy a kiszolgáló túl sok távoli kapcsolatot kezdeményezett. Ez a riasztás azért lett aktiválva, mert az érzékelő anomáliára vonatkozó küszöbértékek úgy lettek meghatározva, hogy egy percen belül bizonyos számú munkamenet után indítson riasztásokat. Az incidenst a küszöbértékek frissítésével kezelheti.

A mérséklés vagy a vizsgálat elvégzése után utasíthatja az érzékelőt a riasztás elrejtéséhez az **nyugtázás** lehetőség kiválasztásával. Ha a rendszer ismét észleli az eseményt, a rendszer újraaktiválja a riasztást.

A riasztás elrejtése:

  - Válassza a **nyugtázás** lehetőséget.

A riasztás újbóli megtekintése:

  - Nyissa meg a riasztást, és válassza a **visszaigazolást**.

Riasztások visszaigazolása, ha további vizsgálatra van szükség.

## <a name="about-muting-and-unmuting-events"></a>Az elnémítással és az elnémítással kapcsolatos események

Bizonyos körülmények között érdemes lehet arra utasítani az érzékelőt, hogy figyelmen kívül hagyja egy adott forgatókönyvet a hálózatán. Például:

  - Az **anomália** -motor riasztást indít a sávszélességen a két eszköz között, de a nyárs érvényes az ilyen eszközök esetében.

  - A **protokoll-megsértési** motor riasztást küld a két eszköz között észlelt protokoll-eltérésről, de az eltérés az eszközök között érvényes.

Ilyen helyzetekben a tanulás nem érhető el. Ha a tanulás nem hajtható végre, és el kívánja távolítani a riasztást, és el szeretné távolítani az eszközt a kockázatok és a támadási vektorok kiszámításakor, a riasztási eseményt nem lehet elnémítani.

> [!NOTE] 
> Nem lehet némítani azokat az eseményeket, amelyekben egy internetes eszköz a forrásként vagy a célhelyként van meghatározva.

### <a name="what-traffic-is-muted"></a>Milyen forgalom van elnémítva?

A tompított forgatókönyvek között szerepelnek az események által észlelt hálózati eszközök és forgalom. A riasztás címe az elnémítva forgalmat mutatja.

Az elnémítva kívánt eszköz vagy eszközök képként lesznek megjelenítve a riasztásban. Ha két eszköz jelenik meg, a közöttük lévő forgalom el lesz némítva.

**1\. példa**

Ha egy esemény el van némítva, azt a rendszer figyelmen kívül hagyja, amikor az elsődleges (forrás) nem küldi el a másodlagos (célként megadott) függvény kódját a szállító által meghatározott módon.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Másodlagos eszköz érkezett.":::

**2\. példa**

Ha egy esemény el van némítva, figyelmen kívül hagyja a rendszer minden alkalommal, amikor a forrás HTTP-fejlécet küld az illegális tartalommal, a célhelytől függetlenül.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Képernyőkép az illegális HTTP-fejlécek tartalmáról.":::

**Egy esemény elnémítása után:**

- A riasztás a **visszaigazolt** riasztási nézetben lesz elérhető, amíg nem áll le az Elnémítás.

- Az Elnémítás művelet az **esemény idővonalán** fog megjelenni.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="A rendszer eseményt észlelt és elnémítva.":::

- Az érzékelő a kockázatértékelés, a támadási vektor és egyéb jelentések létrehozásakor újraszámítja az eszközöket. Ha például elnémítva egy olyan riasztást, amely rosszindulatú adatforgalmat észlelt egy eszközön, az eszköz nem lesz kiszámítva a kockázatértékelési jelentésben.

**Riasztás némítása és feloldása:**

- Válassza az **Elnémítás** ikont a riasztáson.

**Az elnémítva riasztások megtekintése:**

1. Jelölje be a **riasztások** főképernyője a **visszaigazolt** beállítás formájában.

2. Vigye az egérmutatót a riasztás fölé, és ellenőrizze, hogy elnémítva van-e.  

## <a name="see-also"></a>További információ

[Jelentések készítése](how-to-generate-reports.md)

[A figyelt forgalom szabályozása](how-to-control-what-traffic-is-monitored.md)
