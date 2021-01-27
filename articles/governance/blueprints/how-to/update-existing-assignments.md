---
title: Meglévő hozzárendelés frissítése a portálról
description: Ismerje meg, hogyan frissíthet egy meglévő tervezet-hozzárendelést a portálról az Azure-tervezetekben.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: c383ebedaf83b3a52062c91f98b816c3baf6618e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919393"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Meglévő terv-hozzárendelés frissítése

Tervezet hozzárendelésekor a hozzárendelés frissíthető. A meglévő hozzárendelések frissítése több okból is lehetséges, többek között:

- [Erőforrás-zárolás](../concepts/resource-locking.md) hozzáadása vagy eltávolítása
- [Dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) értékének módosítása
- A hozzárendelés frissítése a terv újabb **közzétett** verziójára

## <a name="updating-assignments"></a>Hozzárendelések frissítése

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról.

1. A tervrajzok listájában válassza ki a terv-hozzárendelést. Ezután használja a **hozzárendelés frissítése** gombot, vagy kattintson a jobb gombbal a terv-hozzárendelésre, és válassza a **hozzárendelés frissítése** lehetőséget.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Képernyőkép a terv-hozzárendelési oldalról, amely kiemelte a &quot;frissítés hozzárendelése&quot; gombot." border="false":::

1. A **terv hozzárendelése** lap az eredeti hozzárendelés összes értékével előre betöltődik. Megváltoztathatja a **terv definíciójának verzióját**, a **zárolás hozzárendelésének** állapotát, valamint a terv definíciójában létező dinamikus paramétereket. Válassza a **hozzárendelés** lehetőséget a módosítások végrehajtásakor.

1. A frissített hozzárendelés részletei lapon tekintse meg az új állapotot. Ebben a példában a hozzárendelést felvettük a **zárolásra** .

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Képernyőfelvétel a zárolási módot megváltoztató frissített terv-hozzárendelésről." border="false":::

1. A legördülő lista használatával megismerheti a többi **hozzárendelési művelet** részleteit. A **felügyelt erőforrások** a kijelölt hozzárendelési művelet által frissülő táblázata.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Képernyőkép a hozzárendelési műveleteket és azok állapotát bemutató, frissített terv-hozzárendelésről." border="false":::

## <a name="rules-for-updating-assignments"></a>A hozzárendelések frissítésére vonatkozó szabályok

A frissített hozzárendelések telepítése néhány fontos szabályt követ. Ezek a szabályok határozzák meg, hogy mi történik a már üzembe helyezett erőforrásokkal. A kért módosítás és az összetevő-erőforrás üzembe helyezése vagy frissítése határozza meg, hogy mely műveleteket kell elvégezni.

- Szerepkör-hozzárendelések
  - Ha a szerepkör vagy a hozzárendelt szerepkör (felhasználó, csoport vagy alkalmazás) megváltozik, a rendszer új szerepkör-hozzárendelést hoz létre. A korábban üzembe helyezett szerepkör-hozzárendelések továbbra is érvényben maradnak.
- Szabályzat-hozzárendelések
  - Ha a szabályzat-hozzárendelés paraméterei megváltoznak, a rendszer frissíti a meglévő hozzárendelést.
  - Ha a szabályzat-hozzárendelés definíciója módosul, létrejön egy új szabályzat-hozzárendelés.
    A korábban üzembe helyezett szabályzat-hozzárendelések továbbra is érvényben maradnak.
  - Ha a szabályzat-hozzárendelési összetevő el lett távolítva a tervből, a rendszer az üzembe helyezett szabályzat-hozzárendeléseket is megmarad.
- Azure Resource Manager sablonok (ARM-sablonok)
  - A sablon a Resource Manageren keresztül lesz feldolgozva **put**-ként. Mivel minden erőforrástípus eltérően kezeli ezt a műveletet, tekintse át az egyes befoglalt erőforrások dokumentációját, hogy meghatározza a művelet hatását a tervrajzok futtatásakor.

## <a name="possible-errors-on-updating-assignments"></a>A hozzárendelések frissítésének lehetséges hibái

A hozzárendelések frissítésekor lehetséges, hogy a végrehajtott módosítások megszakadnak. Ilyen például a már üzembe helyezett erőforráscsoport helyének módosítása. A [Resource Manager](../../../azure-resource-manager/management/overview.md) által támogatott bármely módosítás elvégezhető, de az erőforrás-kezelőn keresztüli hibát eredményező módosítások a hozzárendelés hibáját is eredményezik.

Korlátlan számú hozzárendelés frissíthető. Ha hiba történik, állapítsa meg a hibát, és készítsen egy másik frissítést a hozzárendeléshez.  Példa a hibákra:

- Rossz paraméter
- Már létező objektum
- A Resource Manager nem támogatja a változást

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).