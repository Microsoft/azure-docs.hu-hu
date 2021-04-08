---
title: Exchange-társ létrehozása vagy módosítása a Azure Portal használatával
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700480"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Exchange-társ létrehozása vagy módosítása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Exchange-társ a Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, az útmutatót a [PowerShell](howto-exchange-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.
* Ha már rendelkezik olyan Exchange-partnerekkel, amelyek nem az Azure-erőforrásokra lettek konvertálva, tekintse meg a [örökölt Exchange-társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)című témakört.

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társ létrehozása


Internetes Exchange-szolgáltatóként létrehozhat egy Exchange-partneri kérést [egy társ létrehozásával]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. A társítás **létrehozása** oldalon az **alapok** lapon adja meg az itt látható mezőket:

    > [!div class="mx-imgBorder"] 
    > ![A Peering Service regisztrálása](./media/setup-basics-tab.png)

*    Válassza ki az Azure-előfizetését.

* Az erőforráscsoport esetében választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az új létrehozása lehetőség kiválasztásával. Ehhez a példához hozzunk létre egy új erőforráscsoportot.

* A név megegyezik az erőforrás nevével, és bármi lehet, amit választ.

* A régió automatikusan ki van választva, ha a meglévő erőforráscsoportot választotta. Ha úgy döntött, hogy új erőforráscsoportot hoz létre, ki kell választania azt az Azure-régiót, ahol az erőforrást tárolni szeretné.

>[!NOTE]
>Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol a Microsofttal kívánja létrehozni a kapcsolatot. Az ajánlott eljárás azonban az, hogy a legközelebb eső Azure-régiókban található erőforráscsoportok között szervezzen egyenrangú erőforrásokat. Például a Ashburn-ben való társítások esetén létrehozhat egy erőforráscsoportot az USA keleti régiójában vagy a Kelet-RÉGIÓJA.

* Válassza ki az ASN-t a **PeerASN** mezőben.

>[!IMPORTANT] 
>A rendszer csak a jóváhagyott ValidationState rendelkező ASN-ket választhatja, mielőtt elküld egy kérést. Ha most elküldte a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítást jóvá szeretné hagyni. Ha a kiválasztott ASN-hitelesítés függőben van, hibaüzenet jelenik meg. Ha nem látja a kiválasztható ASN-t, ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. Ha igen, ellenőrizze, hogy már létrehozott-e PeerAsn az **[Azure-előfizetéshez való társítási peer ASN](https://go.microsoft.com/fwlink/?linkid=2129592)** használatával.

* Válassza a **Next (tovább): konfigurálás** a folytatáshoz lehetőséget.

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange-társak ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társak módosítása
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange-társ kiépítése
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társ létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társak konvertálása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>További források

További információ: [internetes peering GYIK](faqs.md).
