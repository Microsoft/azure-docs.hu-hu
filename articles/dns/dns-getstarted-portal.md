---
title: 'Rövid útmutató: DNS-zóna és -rekord létrehozása – Azure Portal'
titleSuffix: Azure DNS
description: Ebből a részletes útmutatóból megtudhatja, hogyan hozhat létre egy Azure DNS-zónát és -rekordot a Azure Portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: d08c5768c2c400ff3d842f58081a58708bfc28db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537773"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Rövid útmutató: Azure DNS zóna és rekord létrehozása a Azure Portal

Az Azure DNS-t konfigurálhatja úgy, hogy feloldja a gazdagépneveket a nyilvános tartományban. Ha például az contoso.xyz-tartománynevet egy tartományregisztrálótól vásárolta meg, beállíthatja, hogy az Azure DNS a *contoso.xyz-tartományt* használja, és a webkiszolgáló vagy a  *`www.contoso.xyz`* webalkalmazás IP-címére oldja fel a címet.

Ebben a rövid útmutatóban létrehoz egy teszttartományt, majd létrehoz egy címrekordot a *www* címnek a *10.10.10.10 IP-címre való feloldásához.*

>[!IMPORTANT]
>A rövid útmutatóban szereplő nevek és IP-címek mind olyan példák, amelyek nem valós forgatókönyveket mutatnak be.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A portál összes lépésenél jelentkezzen be a [Azure Portal.](https://portal.azure.com)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna egy tartomány DNS-bejegyzését tartalmazza. Ahhoz, hogy a tartományt a Azure DNS, hozzon létre egy DNS-zónát a tartománynévhez. 

**A DNS-zóna létrehozása:**

1. A bal felső sarokban válassza **az Erőforrás létrehozása,** majd a **Hálózat,** majd a **DNS-zóna lehetőséget.**

1. A **DNS-zóna létrehozása lapon** írja be vagy válassza ki a következő értékeket:

   - **Név:** *Contoso.xyz* rövid útmutató példához adja meg a következőt: . A DNS-zóna neve bármilyen érték lehet, amely még nincs konfigurálva a Azure DNS kiszolgálón. Valós érték lehet például egy tartomány, amelyet egy tartományregisztrálótól vásárolt.
   - **Erőforráscsoport:** Válassza az **Új létrehozása** lehetőséget, írja be a *MyResourceGroup gombra,* majd kattintson az **OK gombra.** Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül. 

1. Válassza a **Létrehozás** lehetőséget.

   ![DNS-zóna](./media/dns-getstarted-portal/openzone650.png)

A zóna létrehozása eltarthat néhány percig.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-bejegyzéseket vagy -rekordokat a tartományhoz a DNS-zónában hozhat létre. Hozzon létre egy új címrekordot vagy "A" rekordot az állomásnév IPv4-címre való feloldásához.

**"A" rekord létrehozása:**

1. A Azure Portal a **Minden** erőforrás területen nyissa meg contoso.xyz DNS-zónát a  **MyResourceGroup erőforráscsoportban.** A contoso.xyz  a Szűrés **név** alapján mezőbe.

1. A **DNS-zóna** lap tetején válassza a **+ Rekordhalmaz lehetőséget.**

1. A **Rekordhalmaz hozzáadása lapon** írja be vagy válassza ki a következő értékeket:

   - **Név:** Írja be a *www nevet.* A rekord neve a megadott IP-címre feloldani kívánt állomásnév.
   - **Írja be** a következőt: Válasszon **ki egy et.** Az "A" rekordok a leggyakoribbak, de más rekordtípusok is vannak a levelezési kiszolgálókhoz (MX), az IP v6-címekhez (AAAA) és így tovább. 
   - **TTL:** Írja be *a következőt: 1.* *A DNS-kérelem hosszú ideje* határozza meg, hogy a DNS-kiszolgálók és -ügyfelek mennyi ideig gyorsítótáraznak választ.
   - **TTL-egység:** Válassza az Óra **lehetőséget.** Ez az **TTL-érték időegysége.** 
   - **IP-cím:** Ebben a rövid útmutatóban írja be a *következőt: 10.10.10.10.* Ez az érték az az IP-cím, amelybe a rekord neve feloldódik. Egy valós forgatókönyvben Ön adná meg a webkiszolgáló nyilvános IP-címét.

Mivel ez a rövid útmutató csak rövid tesztelési célokra szolgál, nem kell konfigurálni a Azure DNS névkiszolgálókat egy tartománynév-regisztrálónál. Valós éles tartomány használata során azt szeretné, hogy az interneten bárki feloldja a gazdanevet, és csatlakozzon a webkiszolgálóhoz vagy az alkalmazáshoz. Felkeresi a tartománynév-regisztrálót, és lecseréli a névkiszolgáló rekordjait az Azure DNS névkiszolgálókra. További információ: [Oktatóanyag: A tartomány gazdagépként való Azure DNS.](dns-delegate-domain-azure-dns.md#delegate-the-domain)

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy már van egy "A" tesztrekordot teszt DNS-zónája, tesztelheti a névfeloldást egy *nslookup nevű eszközzel.* 

**A DNS-névfeloldás tesztelése:**

1. A Azure Portal a **Minden** erőforrás területen nyissa meg contoso.xyz DNS-zónát a  **MyResourceGroup erőforráscsoportban.** A jobb *contoso.xyz* **a** Szűrés név alapján mezőbe.

1. Másolja ki a névkiszolgálók egyik nevét az Áttekintés lap **névkiszolgálói listájából.** 

   ![zóna](./media/dns-getstarted-portal/viewzonens500.png)

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Például:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Az alábbi képernyőhöz hasonlónak kell lennie:

   ![Képernyőkép egy parancssori ablakról, amely tartalmaz egy n s keresési parancsot, valamint a Kiszolgáló, Cím, Név és Cím értékeket.](media/dns-getstarted-portal/nslookup.PNG)

A **www \. contoso.xyz** **10.10.10.10** lesz feloldva a konfigurálásnak megfelelően. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott erőforrásokra, távolítsa el őket a **MyResourceGroup erőforráscsoport** törlésével. Nyissa meg **a MyResourceGroup** erőforráscsoportot, és válassza az **Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
