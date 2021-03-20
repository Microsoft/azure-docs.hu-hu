---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan lehet elhárítani a tervezetek létrehozásával, hozzárendelésével és eltávolításával kapcsolatos problémákat, például a szabályzat megsértését és a terv paramétereinek funkcióit.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 65cf8ef9a5dcba0165aad8522f91ff1eb2c963a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918844"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Az Azure-tervezetekkel kapcsolatos hibák elhárítása

A tervrajzok létrehozásakor, kiosztásakor vagy eltávolításakor hibák léphetnek fel. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

Számos hiba lesz a terv egy hatókörhöz való hozzárendelésének eredménye. Ha egy hozzárendelés meghiúsul, a terv részletesen ismerteti a sikertelen telepítés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és a következő telepítés sikeres lesz.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról, és a keresőmező segítségével szűrje a terv-hozzárendeléseket a sikertelen hozzárendelés megkereséséhez. A hozzárendelések tábláját a **kiépítési állapot** oszlopban is rendezheti, ha az összes sikertelen hozzárendelést együtt szeretné látni.

1. Válassza ki a _sikertelen_ állapotú tervet, vagy kattintson a jobb gombbal, és válassza a **hozzárendelés részleteinek megtekintése** lehetőséget.

1. Egy piros szalagcím figyelmeztetése, hogy a hozzárendelés meghiúsult a terv-hozzárendelés oldal tetején. További részletekért kattintson a szalagcímen bárhová.

Gyakori, hogy a hiba oka egy összetevő, nem pedig a terv egésze. Ha egy összetevő létrehoz egy Key Vault, és Azure Policy megakadályozza a Key Vault létrehozását, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Forgatókönyv: szabályzat megsértése

#### <a name="issue"></a>Probléma

A sablon központi telepítése a szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend több okból is ütközhet az üzembe helyezéssel:

- A létrehozandó erőforrást házirend korlátozza (általában SKU vagy Location korlátozásokkal).
- Az üzemelő példány olyan mezőket állít be, amelyeket a szabályzat konfigurál (közös címkékkel)

#### <a name="resolution"></a>Feloldás

Módosítsa a tervet úgy, hogy az ne ütközzön a hiba részleteiben szereplő szabályzatokkal. Ha ez a változás nem lehetséges, egy másik lehetőség, hogy a házirend-hozzárendelés hatóköre megváltozott, így a terv már nem ütközik a szabályzattal.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Forgatókönyv: a Blueprint paraméter egy függvény

#### <a name="issue"></a>Probléma

A függvények feldolgozására szolgáló tervrajzi paramétereket a rendszer az összetevőknek való továbbítás előtt dolgozza fel.

#### <a name="cause"></a>Ok

Egy függvényt (például) használó tervrajzi paraméter átadása az `[resourceGroup().tags.myTag]` összetevőnek a dinamikus függvény helyett a tárgyban beállított függvény feldolgozott eredményét eredményezi.

#### <a name="resolution"></a>Feloldás

Egy függvény paraméterként való átadásához escape a teljes karakterláncot `[` oly módon, hogy a terv paramétere hasonlítson `[[resourceGroup().tags.myTag]` . Az escape-karakter olyan tervrajzokat okoz, amelyek az értéket karakterláncként kezelik a terv feldolgozásakor. A tervrajzok szolgáltatás ezután elhelyezi a függvényt az összetevőn, amely lehetővé teszi, hogy a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Törlési hibák

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Forgatókönyv: hozzárendelés törlésének időtúllépése

#### <a name="issue"></a>Probléma

A terv-hozzárendelés törlése nem fejeződött be.

#### <a name="cause"></a>Ok

A tervrajz-hozzárendelések nem terminálos állapotba kerülhetnek, ha törölve lett. Ez az állapot akkor áll elő, amikor a terv-hozzárendelés által létrehozott erőforrások még törlésre várók, vagy nem adnak vissza állapotkódot az Azure-tervezetekhez.

#### <a name="resolution"></a>Feloldás

A nem terminálos állapotú terv-hozzárendelések a _hat órás_ időkorlát után automatikusan **nem** lesznek megjelölve. Miután az időtúllépés kiigazította a terv-hozzárendelés állapotát, a törlés újrapróbálkozhat.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.