---
title: Azure-partneri szolgáltatás regisztrálása – Azure Portal
description: Ismerje meg, hogyan regisztrálhat Azure-alapú társ-szolgáltatási szolgáltatást a Azure Portal használatával
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534947"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>A Azure Portal használatával regisztrálja a társ-nyilvántartó szolgáltatást

Az Azure társközi szolgáltatás olyan hálózati szolgáltatás, amely a Microsoft Cloud Services, például a Microsoft 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatásai, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára teszi lehetővé az ügyfelek kapcsolódását.

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy társ-szolgáltatási kapcsolatot a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre most egy [fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> 

## <a name="prerequisites"></a>Előfeltételek

A következőkkel kell rendelkeznie:

### <a name="azure-account"></a>Azure-fiók

Érvényes és aktív Microsoft Azure fiókkal kell rendelkeznie. Ez a fiók szükséges a társ-szolgáltatási kapcsolatok beállításához. Az egyenrangú szolgáltatás az Azure-előfizetéseken belüli erőforrás. 

### <a name="connectivity-provider"></a>Kapcsolatszolgáltató

A hálózat Microsoft hálózattal való összekapcsolásához egy internetszolgáltatóval vagy egy internetes Exchange-partnerrel is dolgozhat.

Győződjön meg arról, hogy a [kapcsolati szolgáltatók](location-partners.md) a Microsofttal együttműködtek.



## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőben nyissa meg a Azure Portal, és jelentkezzen be az Azure-fiókjával.

## <a name="register-a-peering-service-connection"></a>Peering Service-kapcsolat regisztrálása

1. A társ-szolgáltatási kapcsolatok regisztrálásához válassza **az erőforrás létrehozása**  >  **társ**-kezelés lehetőséget.

    ![A Peering Service regisztrálása](./media/peering-service-portal/peering-servicecreate.png)
1. Adja meg a következő adatokat a **peering Service-kapcsolat létrehozása** lap **alapok** lapján.

 
1. Válassza ki az előfizetéshez tartozó előfizetést és erőforráscsoportot.

   ![Peering Basic lap regisztrálása](./media/peering-service-portal/peering-servicebasics.png)

1. Adja meg azt a **nevet** , amelyhez regisztrálni szeretné a társ-szolgáltatási példányt.
 
1. Most kattintson a **következő: konfiguráció** gombra a lap alján. Megjelenik a **konfiguráció** lap.

## <a name="configure-the-peering-service-connection"></a>A társ-szolgáltatási kapcsolatok konfigurálása

1. A **konfiguráció** lapon válassza ki azt a helyet, amelyhez engedélyezni szeretné a társítási szolgáltatást. Ehhez válassza ki a megfelelő elemet a **társ-szolgáltatási hely** legördülő listából.

1. Válassza ki azt a szolgáltatót, akitől a társítási szolgáltatást meg kell adni, ha kiválasztja a szolgáltató nevét a **társ-szolgáltató** legördülő listából.
 
1. Válassza az **új előtag létrehozása** elemet az **előtagok** szakasz alján, és megjelenik a szövegmezők. Most adja meg az előtag-erőforrás nevét és a szolgáltatóhoz társított előtagokat.

1. Válassza ki az **előtag-kulcsot** , és adja hozzá a szolgáltató (ISP vagy IXP) által megadott előtag-kulcsot. Ez a kulcs lehetővé teszi, hogy az MS érvényesítse az IP-előtagot lefoglaló előtagot és szolgáltatót.
   > ![Képernyőfelvétel: a társítási szolgáltatás kapcsolatainak létrehozása lap konfiguráció lapja, amelyen megadhatja az előtag kulcsát.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Kattintson a lap bal alsó sarkában található **felülvizsgálat + létrehozás** gombra. Megjelenik a **felülvizsgálat + létrehozás** lap, és az Azure ellenőrzi a konfigurációt.
    

1. Amikor megjelenik az **átadott üzenet ellenőrzése** az ábrán látható módon, válassza a **Létrehozás** lehetőséget.

   > ![A képernyőképen a peering Service-kapcsolatok létrehozása lap áttekintés + létrehozás lapja látható.](./media/peering-service-portal/peering-service-prefix.png)


1. A társ-szolgáltatási kapcsolat regisztrálása után a rendszer további ellenőrzéseket hajt végre a befoglalt előtagokon. Az érvényesítési állapotot az erőforrás neve **előtagok** szakaszában tekintheti meg. Ha az ellenőrzés sikertelen, a következő hibaüzenetek egyike jelenik meg:

   - Érvénytelen társ-szolgáltatási előtag, az előtagnak érvényes formátumúnak kell lennie, csak IPv4-előtag támogatott.
   - Nem érkezett előtag a (z) társ-szolgáltatótól.
   - Az előtag bejelentése nem rendelkezik érvényes BGP-Közösséggel, forduljon a partneri szolgáltatóhoz.
   - A biztonsági mentési útvonal nem található, forduljon a partneri szolgáltatóhoz.
   - Az előtagot a rendszer az elérési úttal meghaladva fogadja el, forduljon a partneri szolgáltatóhoz.
   - A (z) elérési úttal együtt kapott előtag a kapcsolati partneri szolgáltatóval.

### <a name="add-or-remove-a-prefix"></a>Előtag hozzáadása vagy eltávolítása

Az előtagok lapon válassza az előtagok **hozzáadása** lehetőséget az **előtagok** hozzáadásához.

Válassza a felsorolt előtag melletti három pontot (...), majd kattintson a **Törlés** lehetőségre.

### <a name="delete-a-peering-service-connection"></a>Társ-szolgáltatási kapcsolatok törlése

A **minden erőforrás** lapon jelölje be a társítási szolgáltatás jelölőnégyzetét, és válassza a **Törlés** lehetőséget az oldal tetején.

> [!NOTE]
> Meglévő előtag nem módosítható.
>

## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatok megismeréséhez tekintse meg a társítási [szolgáltatás kapcsolódása](connection.md)című témakört.
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
- A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
- Ha Azure PowerShell használatával szeretné regisztrálni a kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure PowerShell](powershell.md).
- A Kapcsolódás az Azure CLI használatával történő regisztrálásával kapcsolatban lásd: [társ-szolgáltatási kapcsolatok regisztrálása – Azure CLI](cli.md).
