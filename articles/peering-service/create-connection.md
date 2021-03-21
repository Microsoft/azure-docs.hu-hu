---
title: 'Azure-partneri szolgáltatás: létrehozás '
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat az Azure-szolgáltatások és az előtagok.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021095"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Oktatóanyag: egyenrangú szolgáltatási kapcsolatok létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy társ-szolgáltatási erőforrást, és hogyan konfigurálhat egy társ-szolgáltatási kapcsolatot. 

1. A társ-szolgáltatási kapcsolatok regisztrálásához válassza **az erőforrás létrehozása**  >  **társ**-kezelés lehetőséget.

 
    ![A Peering Service regisztrálása](./media/peering-service-portal/peering-servicecreate.png)

2. Adja meg a következő adatokat a **peering Service-kapcsolat létrehozása** lap **alapok** lapján.
 
3. Válassza ki az előfizetéshez tartozó előfizetést és erőforráscsoportot.

    ![A peering Service alapszintű lapjainak regisztrálása](./media/peering-service-portal/peering-servicebasics.png)

4. Adja meg azt a **nevet** , amelyhez regisztrálni szeretné a társ-szolgáltatási példányt.

5. Most kattintson a **következő: konfiguráció** gombra a lap alján. Megjelenik a **konfiguráció** lap.
## <a name="configure-the-peering-service-connection"></a>A társ-szolgáltatási kapcsolatok konfigurálása

1. A **konfiguráció** lapon válassza ki azt a helyet, amelyhez engedélyezni szeretné a társítási szolgáltatást. Ehhez válassza ki a megfelelő elemet a **társ-szolgáltatási hely** legördülő listából.

1. Válassza ki azt a szolgáltatót, akitől a társítási szolgáltatást meg kell adni, ha kiválasztja a szolgáltató nevét a **társ-szolgáltató** legördülő listából.
 
1. Válassza az **új előtag létrehozása** elemet az **előtagok** szakasz alján, és megjelenik a szövegmezők. Most adja meg az előtag-erőforrás nevét és a szolgáltatóhoz társított előtagokat.

1. Válassza ki az **előtag-kulcsot** , és adja hozzá a szolgáltató (ISP vagy IXP) által megadott előtag-kulcsot. Ez a kulcs lehetővé teszi, hogy az MS érvényesítse az IP-előtagot lefoglaló előtagot és szolgáltatót.

    ![Képernyőfelvétel: a társítási szolgáltatás kapcsolatainak létrehozása lap konfiguráció lapja, amelyen megadhatja az előtag kulcsát.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Kattintson a lap bal alsó sarkában található **felülvizsgálat + létrehozás** gombra. Megjelenik a **felülvizsgálat + létrehozás** lap, és az Azure ellenőrzi a konfigurációt.

 1. Amikor megjelenik az **átadott üzenet ellenőrzése** az ábrán látható módon, válassza a **Létrehozás** lehetőséget.

> ![A képernyőképen a peering Service-kapcsolatok létrehozása lap áttekintés + létrehozás lapja látható.](./media/peering-service-portal/peering-service-prefix.png)

1. A társ-szolgáltatási kapcsolat regisztrálása után a rendszer további ellenőrzéseket hajt végre a befoglalt előtagokon. Az érvényesítési állapotot az erőforrás neve **előtagok** szakaszában tekintheti meg. Ha az ellenőrzés sikertelen, a következő hibaüzenetek egyike jelenik meg:

   - Érvénytelen társ-szolgáltatási előtag, az előtagnak érvényes formátumúnak kell lennie, csak IPv4-előtag támogatott.
   - Nem érkezett előtag a (z) társ-szolgáltatótól.
   - Az előtag bejelentése nem rendelkezik érvényes BGP-Közösséggel, forduljon a partneri szolgáltatóhoz.
   - A biztonsági mentési útvonal nem található, forduljon a partneri szolgáltatóhoz.
   - Az előtag elérési útja már nem érhető el, forduljon a partneri szolgáltatóhoz.
   - A saját elérési úttal kapott előtag, forduljon a társas szolgáltatóhoz.

### <a name="add-or-remove-a-prefix"></a>Előtag hozzáadása vagy eltávolítása

Az előtagok lapon válassza az előtagok **hozzáadása** lehetőséget az **előtagok** hozzáadásához.

Válassza a felsorolt előtag melletti három pontot (...), majd kattintson a **Törlés** lehetőségre.

### <a name="delete-a-peering-service-connection"></a>Társ-szolgáltatási kapcsolatok törlése

A **minden erőforrás** lapon jelölje be a társítási szolgáltatás jelölőnégyzetét, és válassza a **Törlés** lehetőséget az oldal tetején.
## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatok megismeréséhez tekintse meg a társítási [szolgáltatás kapcsolódása](connection.md)című témakört.
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
- A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
- Ha Azure PowerShell használatával szeretné regisztrálni a kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure PowerShell](powershell.md).
- A Kapcsolódás az Azure CLI használatával történő regisztrálásával kapcsolatban lásd: [társ-szolgáltatási kapcsolatok regisztrálása – Azure CLI](cli.md).