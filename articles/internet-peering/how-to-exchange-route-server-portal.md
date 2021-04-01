---
title: Exchange-partnerek társítása az útválasztási kiszolgálóval a portálon keresztül
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537191"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Exchange-társ létrehozása vagy módosítása a Azure Portal a Route Serverrel

Ez a cikk azt ismerteti, hogyan hozható létre a Microsoft Exchange-társ egy útválasztási kiszolgálóval a Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.


## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.
* Ha már rendelkezik olyan Exchange-partnerekkel, amelyek nem az Azure-erőforrásokra lettek konvertálva, tekintse meg a [örökölt Exchange-társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)című témakört.

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Exchange-társ létrehozása az útválasztási kiszolgálóval


Internetes Exchange-szolgáltatóként létrehozhat egy Exchange-partneri kérést [egy társ létrehozásával]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. A társítás **létrehozása** oldalon az **alapok** lapon adja meg az itt látható mezőket:

    > [!div class="mx-imgBorder"] 
    > ![A Peering Service regisztrálása](./media/setup-basics-tab.png)

* Válassza ki az Azure-előfizetését.

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

1. A társítás létrehozása oldalon a konfiguráció lapon töltse ki a mezőket az itt látható módon:

    > [!div class="mx-imgBorder"]
    > ![Útvonal-kiszolgáló konfigurálása](./media/setup-exchange-conf-tab-routeserver.png)
 
    * A Társrendszer típusa beállításnál válassza a **közvetlen** lehetőséget.
    * A Microsoft Network esetében válassza **a AS8075 Exchange Route Server** lehetőséget. 
    * Válassza az SKU **alapszintű ingyenes** lehetőséget. Ne válassza a prémium szintű ingyenes lehetőséget, mert speciális alkalmazások számára van fenntartva.
    * Válassza ki azt a **metró** helyet, ahová a társítást telepíteni kívánja.

1. A társítási **kapcsolatok** területen válassza az **új létrehozása** lehetőséget.

1.  A **közvetlen** társítási kapcsolat területen adja meg a következő BGP-munkamenet részleteit:

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a közvetlen társas kapcsolat ablaktábla, amely tartalmazza a részleteket.](./media/setup-exchange-conf-tab-direct-route.png)


     * Partneri létesítmény, válassza ki a megfelelő fizikai helyet a társításhoz
     * Munkamenet-címek szolgáltatója, társ kiválasztása
     * A munkamenet IPv4-előtagját az Exchange Provider partner biztosítja
     * A társ-munkamenet IPv4-címét a rendszer az IP-előtag tartományában lévő Exchange-társ választja ki az útválasztási kiszolgáló számára.
     * A Microsoft munkamenet IPv4-címe lesz az IP-előtag tartományában lefoglalt útválasztó IP-cím.
     * A munkamenet IPv6-beállítása jelenleg nem kötelező.
     * A meghirdetett IPv4-előtag legfeljebb 20000 lehet. 
     * A for peering szolgáltatás használata alapértelmezés szerint le van tiltva. Ez akkor engedélyezhető, ha az Exchange-szolgáltató egy társ-szolgáltatási szerződést írt alá a Microsofttal.

1. A befejezést követően kattintson a **Mentés** gombra. 

1. A társítás létrehozása területen látni fogja az érvényesítést. Az érvényesítést követően kattintson a **Létrehozás** gombra.

    > [!div class="mx-imgBorder"]
    > ![Beállítások érvényesítése](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >A Microsoft társ-szolgáltatási partnerként használt normál internetszolgáltató (ISP) esetében az ügyfél IP-előtagjainak regisztrációja szükséges. Ha azonban Exchange-partnereknek van útválasztó-kiszolgálója, akkor regisztrálni kell az ügyfél-ASN, és nem előtagokat. Ugyanez az ASN-kulcs érvényes az ügyfél előtag-regisztrációja esetén.

1. A beállítások szakaszban válassza a **regisztrált ASN** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A képernyőképen a bejelentkezett, az S NS menüpontot tartalmazó panel jelenik meg.](./media/setup-exchange-registered-asn.png)

1. Válassza a **regisztrált ASN hozzáadása** lehetőséget egy új ügyfél ASN létrehozásához az Exchange-előfizetés alatt.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a S N panel regisztrálása névvel és S N szövegmezővel.](./media/setup-exchange-register-new-asn.png)

1. Az ASN regisztrálása területen jelöljön ki egy nevet, töltse ki az ügyfél ASN-jét, majd kattintson a Mentés gombra.

1. A regisztrált ASN alatt minden ASN-hez hozzá lesz rendelve egy társított előtag-kulcs. Exchange-szolgáltatóként meg kell adnia ezt az előtag-kulcsot az ügyfél számára, hogy az előfizetésük alatt regisztrálja a társ-szolgáltatási szolgáltatást.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a regisztrált az S NS panel előtag-kulcsokkal.](./media/setup-exchange-register-asn-prefixkey.png)




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
