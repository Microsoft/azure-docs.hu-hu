---
title: Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával
titleSuffix: Azure
description: Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537208"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan hozható létre a Microsoft Direct peering egy internetszolgáltató vagy internetes Exchange-szolgáltató számára a Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető, illetve kiépíthető.

Ha szeretné, ezt az útmutatót az Azure [PowerShell](howto-direct-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen](walkthrough-direct-all.md) társítási útmutatót.
* Ha már rendelkezik olyan közvetlen kapcsolódási kapcsolattal a Microsofttal, amely nem az Azure-erőforrásokra lett konvertálva, tekintse meg a [örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)című témakört.

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társítás létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társ létrehozása

Internet-szolgáltatóként vagy internetes Exchange-szolgáltatóként létrehozhat egy új, közvetlen társítási kérést [egy társ létrehozásával]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. A társítás **létrehozása** oldalon az **alapok** lapon adja meg az itt látható mezőket:


    ![A képernyőképen megjelenik a társítási oldal létrehozása alapismeretek lap az új létrehozás hivatkozással.](./media/setup-basics-tab.png)

2. Válassza ki az Azure-előfizetését.

3. Az erőforráscsoport esetében választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az új létrehozása lehetőség kiválasztásával. Ehhez a példához hozzunk létre egy új erőforráscsoportot.

4. A név megegyezik az erőforrás nevével, és bármi lehet, amit választ.

5. A régió automatikusan ki van választva, ha a meglévő erőforráscsoportot választotta. Ha úgy döntött, hogy új erőforráscsoportot hoz létre, ki kell választania azt az Azure-régiót, ahol az erőforrást tárolni szeretné.

    >[!NOTE]
    > Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol a Microsofttal kívánja létrehozni a kapcsolatot. Az ajánlott eljárás azonban az, hogy a legközelebb eső Azure-régiókban található erőforráscsoportok között szervezzen egyenrangú erőforrásokat. Például a Ashburn-ben való társítások esetén létrehozhat egy erőforráscsoportot az USA keleti régiójában vagy a Kelet-RÉGIÓJA.

6. Válassza ki az ASN-t a **PeerASN** mezőben.

    >[!IMPORTANT]
    >A rendszer csak a jóváhagyott ValidationState rendelkező ASN-ket választhatja, mielőtt elküld egy kérést. Ha most elküldte a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítást jóvá szeretné hagyni. Ha a kiválasztott ASN-hitelesítés függőben van, hibaüzenet jelenik meg. Ha nem látja a kiválasztható ASN-t, ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. Ha igen, ellenőrizze, hogy már létrehozott-e PeerAsn az **[Azure-előfizetéshez való társítási peer ASN](https://go.microsoft.com/fwlink/?linkid=2129592)** használatával.

7. Válassza a **Next (tovább): konfigurálás** a folytatáshoz lehetőséget.



    ![A képernyőképen a társítási oldal létrehozása alapértékek lap látható az összes megadott értékkel.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társítás módosítása
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társak kiépítése
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társ átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

További információ: [internetes peering GYIK](faqs.md).
