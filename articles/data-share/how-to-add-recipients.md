---
title: Címzettek hozzáadása az Azure-adatmegosztásban
description: Megtudhatja, hogyan adhat hozzá címzetteket egy meglévő adatmegosztáshoz az Azure-adatmegosztásban.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680640"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Címzett hozzáadása a megosztáshoz

A címzettet új megosztás létrehozásakor vagy egy meglévő megosztásban adhatja hozzá. Az Azure-beli adatmegosztás felhasználói felületén a felhasználó Azure bejelentkezési e-mail-címével adhatja hozzá a címzettet.  Az API-ból a felhasználó/szolgáltatásnév és a bérlői azonosító kombinációját használhatja. Ha meg van adva egy bérlő azonosítója, a meghívót csak ebbe a bérlőbe lehet fogadni. Az API-ból is létrehozhat egy meghívót, anélkül, hogy e-mailt küldene a címzettnek. 

## <a name="add-recipient-to-an-existing-share"></a>Címzett hozzáadása egy meglévő megosztáshoz

Az Azure-adatmegosztás területen navigáljon az elküldött megosztáshoz, és válassza a **meghívások** fület. Itt látható az adatmegosztásra vonatkozó meghívók összes címzettje. Új hozzáadásához kattintson a **Címzett hozzáadása** lehetőségre.

![Képernyőfelvétel: kijelölt címzett hozzáadása.](./media/how-to/how-to-add-recipients/add-recipient.png)

Előugrik egy panel az oldal jobb szélén. Kattintson a **Címzett hozzáadása** lehetőségre, majd adja meg az új címzett e-mail-címét az üres sorban. Győződjön meg arról, hogy a címzett Azure bejelentkezési e-mail-címét használja (az e-mail alias használata nem fog működni). 

![Képernyőfelvétel: a címzett hozzáadása panel, ahol meghívót adhat hozzá és küldhet.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Kattintson **a Hozzáadás gombra, és küldje el a meghívót**. Az új címzett (ek) e megosztás e-mail-címzettjei lesznek elküldve.

## <a name="next-steps"></a>Következő lépések
További információ a [meghívások megosztásra való törléséről](how-to-delete-invitation.md).
