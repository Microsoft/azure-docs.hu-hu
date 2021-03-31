---
title: 'Gyors útmutató: DNS-zóna és-rekord létrehozása – Azure Portal'
titleSuffix: Azure DNS
description: Ezzel a részletes útmutatóval megtudhatja, hogyan hozhat létre Azure DNS zónát és rekordot a Azure Portal használatával.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: be76a7902c2747c7a8dc1bfc21d58ae88f3ff343
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282156"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Gyors útmutató: Azure DNS zóna és rekord létrehozása a Azure Portal használatával

Az Azure DNS-t konfigurálhatja úgy, hogy feloldja a gazdagépneveket a nyilvános tartományban. Ha például megvásárolta a *contoso.xyz* tartománynevet egy tartománynév-regisztrálótól, akkor a Azure DNS konfigurálhatja a *contoso.xyz* tartomány üzemeltetéséhez és a *`www.contoso.xyz`* webkiszolgáló vagy a webalkalmazás IP-címéhez való feloldáshoz.

Ebben a rövid útmutatóban létrehoz egy tesztelési tartományt, majd létrehoz egy címet a *www* feloldásához az IP- *10.10.10.10*.

>[!IMPORTANT]
>Az ebben a rövid útmutatóban szereplő összes név és IP-cím olyan példa, amely nem jelent valós szituációkat.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Az összes portál lépéseihez jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

Egy DNS-zóna tartalmazza a tartomány DNS-bejegyzéseit. A tartomány Azure DNSban való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. 

**A DNS-zóna létrehozása:**

1. A bal felső sarokban válassza **az erőforrás létrehozása**, majd a **hálózatkezelés**, majd a **DNS-zóna** lehetőséget.

1. A **DNS-zóna létrehozása** lapon írja be vagy válassza ki a következő értékeket:

   - **Név**: írja be a *contoso.xyz* nevet ehhez a rövid útmutatóhoz. A DNS-zóna neve bármely olyan érték lehet, amely még nincs konfigurálva a Azure DNS-kiszolgálókon. Valós érték lehet például egy tartomány, amelyet egy tartományregisztrálótól vásárolt.
   - **Erőforráscsoport**: válassza az **új létrehozása** elemet, írja be a *MyResourceGroup*, majd kattintson **az OK gombra**. Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül. 

1. Válassza a **Létrehozás** lehetőséget.

   ![DNS-zóna](./media/dns-getstarted-portal/openzone650.png)

A zóna létrehozása eltarthat néhány percig.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-bejegyzéseket vagy-rekordokat hoz létre a tartományhoz a DNS-zónán belül. Hozzon létre egy új címlistát vagy "A" rekordot egy állomásnév IPv4-címmé való feloldásához.

**"A" rekord létrehozása:**

1. A Azure Portal az **összes erőforrás** területen nyissa meg a **contoso.xyz** DNS-zónát a **MyResourceGroup** erőforráscsoporthoz. Megadhatja a *contoso.xyz* a **szűrés név szerint** mezőben, hogy könnyebben megtalálja.

1. A **DNS-zóna** lap tetején válassza a **+ rekordazonosító** lehetőséget.

1. A **rekordazonosító hozzáadása** lapon írja be vagy válassza ki a következő értékeket:

   - **Név**: írja be a *www* nevet. A rekord neve annak az állomásnévnek a neve, amelyet a megadott IP-címhez kíván feloldani.
   - **Típus**: válassza **A** elemet. Az "a" rekordok a leggyakoribbak, de léteznek más rekordtípusok is a levelezési kiszolgálók (MX), az IP v6-címek ("AAAA") és így tovább. 
   - **TTL**: Type *1*. A DNS-kérelem *élettartama* határozza meg, hogy a DNS-kiszolgálók és az ügyfelek mennyi ideig tudják gyorsítótárazni a válaszokat.
   - **TTL-egység**: válassza az **órák** lehetőséget. Ez a **TTL** -érték időegysége. 
   - **IP-cím**: ehhez a rövid útmutatóhoz írja be a következőt: *10.10.10.10*. Ez az érték annak az IP-címnek a neve, amely a rekord nevét feloldja. Egy valós forgatókönyvben Ön adná meg a webkiszolgáló nyilvános IP-címét.

Mivel ez a rövid útmutató csak a gyors tesztelési célokra szolgál, nem kell konfigurálnia a Azure DNS névszervereket a tartománynév-regisztrálónál. Valódi üzemi tartománnyal azt is megteheti, hogy a webkiszolgálóhoz vagy alkalmazáshoz való kapcsolódáshoz az internetet bárki megoldják. A tartománynév-regisztrálót felkeresve cserélje le a névkiszolgálói rekordokat a Azure DNS névkiszolgálók helyére. További információ: [oktatóanyag: a tartomány üzemeltetése Azure DNSban](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy rendelkezik egy "A" teszttel rendelkező DNS-zónával, tesztelheti a névfeloldást egy *nslookup* nevű eszközzel. 

**DNS-névfeloldás tesztelése:**

1. A Azure Portal az **összes erőforrás** területen nyissa meg a **contoso.xyz** DNS-zónát a **MyResourceGroup** erőforráscsoporthoz. Megadhatja a *contoso.xyz* a **szűrés név szerint** mezőben, hogy könnyebben megtalálja.

1. Másolja az egyik névkiszolgálói nevet az **Áttekintés** oldalon található névkiszolgálók listájából. 

   ![zóna](./media/dns-getstarted-portal/viewzonens500.png)

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Például:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   A következő képernyőhöz hasonlóan kell megjelennie:

   ![Képernyőfelvétel: a parancssori ablak n s keresési paranccsal, valamint a kiszolgáló, a címe, a név és a címe értékeit jeleníti meg.](media/dns-getstarted-portal/nslookup.PNG)

A **www \. contoso.xyz** állomásnév a **10.10.10.10** hasonlóan oldódik meg, ugyanúgy, ahogy konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott erőforrásokra, távolítsa el őket a **MyResourceGroup** -erőforráscsoport törlésével. Nyissa meg a **MyResourceGroup** erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
