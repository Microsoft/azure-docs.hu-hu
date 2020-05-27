---
title: Örökölt Exchange-társ átalakítása Azure-erőforrásra a Azure Portal használatával
titleSuffix: Azure
description: Örökölt Exchange-társ átalakítása Azure-erőforrásra a Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: ca26189709405ca6dc0d2954bd98f0d933963bf4
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800829"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Örökölt Exchange-társ átalakítása Azure-erőforrásra a Azure Portal használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt Exchange-társat egy Azure-erőforrásra a Azure Portal használatával.

Ha szeretné, az útmutatót a [PowerShell](howto-legacy-exchange-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Örökölt Exchange-társ konvertálása Azure-erőforrásra

Internetes Exchange-szolgáltatóként létrehozhat egy közvetlen társ-összevonási kérést a társítás [létrehozásával]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. A társítás **létrehozása** oldalon az **alapok** lapon adja meg az itt látható mezőket:

>    [!div class="mx-imgBorder"]
>   ![A Peering Service regisztrálása](./media/setup-basics-tab.png)

* Válassza ki az Azure-előfizetését.

* Az erőforráscsoport esetében választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az új létrehozása lehetőség kiválasztásával. Ehhez a példához hozzunk létre egy új erőforráscsoportot.

* A név megegyezik az erőforrás nevével, és bármi lehet, amit választ.

* A régió automatikusan ki van választva, ha a meglévő erőforráscsoportot választotta. Ha úgy döntött, hogy új erőforráscsoportot hoz létre, ki kell választania azt az Azure-régiót, ahol az erőforrást tárolni szeretné.

>[!NOTE]
    Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol a Microsofttal kívánja létrehozni a kapcsolatot. Az ajánlott eljárás azonban az, hogy a legközelebb eső Azure-régiókban található erőforráscsoportok között szervezzen egyenrangú erőforrásokat. Például a Ashburn-ben való társítások esetén létrehozhat egy erőforráscsoportot az USA keleti régiójában vagy a Kelet-RÉGIÓJA.

* Válassza ki az ASN-t a **PeerASN** mezőben.

>[!IMPORTANT]  
    A rendszer csak a jóváhagyott ValidationState rendelkező ASN-ket választhatja, mielőtt elküld egy kérést. Ha most elküldte a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítást jóvá szeretné hagyni. Ha a kiválasztott ASN-hitelesítés függőben van, hibaüzenet jelenik meg. Ha nem látja a kiválasztható ASN-t, ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. Ha igen, ellenőrizze, hogy már létrehozott-e PeerAsn az **[Azure-előfizetéshez való társítási peer ASN](https://go.microsoft.com/fwlink/?linkid=2129592)** használatával.

* Válassza a **Next (tovább): konfigurálás** a folytatáshoz lehetőséget.

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Örökölt Exchange-társ konvertálása

A **régi társ-** összekapcsolási kapcsolatokat a társítási erőforrás használatával alakíthatja át.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Az erőforrás elindítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange-társítás ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>További források

További információ: [internetes peering GYIK](faqs.md).

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
