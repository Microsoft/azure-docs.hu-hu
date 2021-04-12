---
title: Előfizetések kezelése
description: Az előfizetések felügyelt véglegesített eszközökből állnak, és igény szerint beállíthatók vagy offboarded.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387326"
---
# <a name="manage-a-subscription"></a>Előfizetés kezelése

Az előfizetések havi rendszerességgel kezelhetők. Ha előfizetést készít, az előfizetés után a hónap végéig számlázunk. Hasonlóképpen, ha regisztrációjának megszüntetésére szolgáló egy előfizetést, akkor a hónap hátralévő részében, amelyben offboarded az előfizetést, díjat számítunk fel.

## <a name="onboard-a-subscription"></a>Előfizetés előkészítése

Az Azure Defender IoT való megkezdéséhez Microsoft Azure előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy ingyenes fiókkal. Ha már rendelkezik hozzáféréssel egy Azure-előfizetéshez, de nem szerepel a listáján, ellenőrizze a fiók adatait, és erősítse meg engedélyeit az előfizetés tulajdonosával.

Előfizetés előkészítése:

1. Navigáljon a Azure Portal díjszabási lapjára. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Navigáljon a Azure Portal díjszabási lapjára.":::

1. Válassza ki a **fedélzeti előfizetés** elemet.

1. A **beépített előfizetés** ablakban válassza ki az előfizetését, valamint a véglegesített eszközök számát a legördülő menükből. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Válassza ki az előfizetését és a véglegesített eszközök számát.":::

1. Válassza **a** bevezetés lehetőséget.

## <a name="offboard-a-subscription"></a>Előfizetés regisztrációjának megszüntetésére szolgáló

Az előfizetések havi rendszerességgel kezelhetők. Ha regisztrációjának megszüntetésére szolgáló egy előfizetést, az előfizetés után a hónap végéig számlázunk.

Távolítsa el az előfizetéshez társított összes érzékelőt, mielőtt offboarding az előfizetést. Az érzékelők törlésével kapcsolatos további információkért lásd: [szenzorok törlése](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor). 

Előfizetés regisztrációjának megszüntetésére szolgáló:

1. Navigáljon a **díjszabás** oldalra.
1. Válassza ki az előfizetést, majd kattintson a **Törlés** ikonra :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. A megerősítő felugró ablakban jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy törölte az előfizetéshez társított összes érzékelőt.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Jelölje be a jelölőnégyzetet, és válassza ki a regisztrációjának megszüntetésére szolgáló, hogy regisztrációjának megszüntetésére szolgáló az érzékelőt.":::

1. Kattintson a **regisztrációjának megszüntetésére szolgáló** gombra. 

## <a name="next-steps"></a>Következő lépések

[A helyszíni felügyeleti konzol aktiválása és beállítása](how-to-activate-and-set-up-your-on-premises-management-console.md)
