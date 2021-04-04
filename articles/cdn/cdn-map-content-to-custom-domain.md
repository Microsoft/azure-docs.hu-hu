---
title: 'Oktatóanyag: egyéni tartomány hozzáadása a végponthoz'
titleSuffix: Azure Content Delivery Network
description: Ezzel az Oktatóanyaggal adhat hozzá egyéni tartományt egy Azure Content Delivery Network-végponthoz, így a tartománynév látható az URL-címben.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537746"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Oktatóanyag: egyéni tartomány hozzáadása a végponthoz

Ez az útmutató megmutatja, hogyan adható hozzá egyéni tartomány egy Azure Content Delivery Network- (CDN-) végponthoz. 

A CDN-profilban található végpont neve a azureedge.net altartománya. Alapértelmezés szerint a tartalom továbbításakor a CDN-profil tartománya szerepel az URL-címben.

Például: **https://contoso.azureedge.net/photo.png**.

Azure CDN lehetővé teszi, hogy egy egyéni tartományt egy CDN-végponttal társítson. Ez a beállítás az alapértelmezett tartomány helyett a tartalmat egy egyéni tartománnyal továbbítja az URL-címben.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány hozzárendelése a CDN-végponthoz.
> - Az egyéni tartomány ellenőrzése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag lépéseinek elvégzése előtt hozzon létre egy CDN-profilt és legalább egy CDN-végpontot. 
    * További információk: [Gyors útmutató: Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md)

* Ha nem rendelkezik egyéni tartománnyal, vásároljon egyet egy tartományi szolgáltatóval. 
    * További információ: [Egyéni tartománynév vásárlása](../app-service/manage-custom-dns-buy-domain.md).

* Ha az Azure-t használja a [DNS-tartományok](../dns/dns-overview.md)üzemeltetéséhez, delegálja az egyéni tartományt Azure DNSra. 
    * További információ: [tartomány delegálása Azure DNSra](../dns/dns-delegate-domain-azure-dns.md).

* Ha tartományi szolgáltatót használ a DNS-tartomány kezelésére, folytassa a [CNAME DNS-rekord létrehozásával](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>CNAME DNS-rekord létrehozása

Ahhoz, hogy egyéni tartományt lehessen használni Azure CDN végponttal, először létre kell hoznia egy kanonikus név (CNAME) rekordot a Azure DNS vagy a DNS-szolgáltatóval, hogy a CDN-végpontra mutasson. 

A CNAME rekord egy olyan DNS-rekord, amely a forrás tartománynevét a cél tartománynevéhez rendeli. 

Az Azure CDN esetében a forrástartománynév az Ön egyéni tartományneve, a céltartománynév pedig a CDN-végpontja gazdaneve. 

Azure CDN a forrás egyéni tartomány felé irányuló forgalmat átirányítja a cél CDN-végpont állomásneve, miután ellenőrizte a CNAME rekordot.

Egy egyéni tartományt és altartományát egyszerre lehet társítani egyetlen végponthoz. 

Több CNAME rekordot is használhat a különböző altartományokhoz ugyanazon egyéni tartományból különböző Azure-szolgáltatásokhoz.

Egy egyéni tartományt különböző altartományokkal is leképezheti ugyanahhoz a CDN-végponthoz.

> [!NOTE]
> Ez az oktatóanyag a CNAME típusú rekordot használja. Ha vagy AAAA típusú bejegyzéstípusokat használ, kövesse az alábbi lépéseket, és cserélje le a CNAME értéket a választott bejegyzéstípusra.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS az Azure-erőforrásokhoz tartozó alias-rekordokat használja ugyanazon az előfizetésen belül.

Alias-rekord hozzáadása a Azure CDN végponthoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd az egyéni tartomány Azure DNS zónáját.

3. Az egyéni tartomány DNS-zónájában válassza a **+ rekord készlet** lehetőséget.

4. A **rekordtípus hozzáadása** mezőbe írja be vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név  | Adja meg a CDN-végponthoz használni kívánt aliast. Például: **www**. |
    | Típus  | Válassza a **CNAME** lehetőséget. |
    | Aliasrekord-halmaz | Válassza az **Igen** lehetőséget. |
    | Alias típusa | Válassza ki az **Azure-erőforrás** elemet. |
    | Előfizetés kiválasztása | Válassza ki előfizetését. |
    | Azure-erőforrás | Válassza ki a Azure CDN végpontot. |

5. Módosítsa a rekord **élettartamát** az értékre.

6. Válassza az **OK** lehetőséget.

# <a name="dns-provider"></a>[**DNS-szolgáltató**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Az ideiglenes cdnverify altartomány leképezése

Amikor meglévő, éles környezetben futó tartományt képez le, speciális szempontokat kell figyelembe vennie. 

A tartományhoz tartozó egyéni tartomány regisztrálásakor a rendszer rövid idő alatt bekövetkezett állásidőt eredményez a Azure Portalban.

A webes forgalom megszakításának elkerüléséhez rendelje hozzá az egyéni tartományt a CDN-végponti állomásnévhez az Azure **cdnverify** altartománnyal. Ez a folyamat létrehoz egy ideiglenes CNAME-leképezést. 

Ezzel a módszerrel a felhasználók megszakítás nélkül férhetnek hozzá a tartományhoz a DNS-hozzárendelés közben. 

Ha a termelési állásidővel kapcsolatos megfontolások nem jelentenek problémát, az egyéni tartományt közvetlenül a CDN-végpontra is leképezheti. Folytassa [az állandó egyéni tartomány leképezését](#map-the-permanent-custom-domain).

CNAME rekord létrehozása a cdnverify altartománnyal:

1. Jelentkezzen be a DNS-szolgáltató webhelyére az egyéni tartományhoz.

2. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás                    | Típus  | Cél                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Forrás: adja meg az egyéni tartománynevet, beleértve a cdnverify altartományt is, a következő formátumban: **cdnverify. \<custom-domain-name>**
        - Például: **cdnverify.www.contoso.com**

    - Típus: írja be vagy válassza a **CNAME** elemet.

    - Cél: adja meg a CDN-végpont állomásnevét, beleértve a cdnverify altartományt is, a következő formátumban: **cdnverify. \<endpoint-name> . azureedge.net**. 
        - Például: **cdnverify.contoso.azureedge.net**

3. Mentse a módosításokat.

## <a name="map-the-permanent-custom-domain"></a>Az állandó egyéni tartomány leképezése

Ebben a szakaszban az állandó egyéni tartományt képezi le a CDN-végpontra. 

CNAME rekord létrehozása az egyéni tartományhoz:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás          | Típus  | Cél           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Forrás: adja meg az egyéni tartománynevet.
        - Például: **www.contoso.com**

    - Típus: írja be vagy válassza a **CNAME** elemet.

    - Cél: adja meg a CDN-végpont állomásnevét a következő formátumban: **\<endpoint-name> . azureedge.net**. 
        - Például: **contoso.azureedge.net**

3. Mentse a módosításokat.

4. Ha korábban létrehozott egy ideiglenes cdnverify altartományhoz tartozó CNAME rekordot, törölje azt. 

5. Ha első alkalommal használja ezt az egyéni tartományt, kövesse az [egyéni tartomány a CDN-végponthoz való hozzárendelésének](#associate-the-custom-domain-with-your-cdn-endpoint) lépéseit, és [ellenőrizze az egyéni tartományt](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Az egyéni tartomány hozzárendelése a CDN-végponthoz

Miután regisztrálta az egyéni tartományát, hozzáadhatja azt a CDN-végpontjához. 


---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és navigáljon arra a CDN-profilra, amely tartalmazza azt a végpontot, amelyet az egyéni tartományhoz kíván leképezni.
    
2. A **CDN-profil** oldalon válassza ki a CDN-végpontot, amelyet társítani kíván az egyéni tartománnyal.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN-végpont kiválasztása" border="true":::
    
3. Válassza az **+ egyéni tartomány** lehetőséget. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Egyéni tartomány hozzáadása gomb" border="true":::

4. Az **egyéni tartomány hozzáadásakor** a **végponti állomásnév** előre ki van töltve, és a CDN-végpont URL-címéből származik: **\<endpoint-hostname>** . azureedge.net. A név nem módosítható.

5. Az **Egyéni gazdagépnév** mezőben adja meg az egyéni tartomány nevét az altartomány nevével együtt, amelyet a CNAME rekord forrástartományaként fog használni. 
    1. Például: **www.contoso.com** vagy **CDN.contoso.com**. **Ne használja a cdnverify aldomain nevét**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Egyéni tartomány hozzáadása" border="true":::

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz. 

   Időbe telhet, amíg az új egyéni tartománybeállítások propagálása az összes CDN-határcsomópontra megtörténik: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Jelentkezzen be Azure PowerShellba:

```azurepowershell-interactive
    Connect-AzAccount

```
2. A [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) használatával rendelje hozzá az egyéni TARTOMÁNYT a CDN-végponthoz. 

    * Cserélje le a **myendpoint8675.azureedge.net** -t a végpont URL-címére.
    * Cserélje le a **myendpoint8675** -t a CDN-végpont nevére.
    * Cserélje le az **www.contoso.com** -t az egyéni tartománynevére.
    * Cserélje le a **myCDN** -t a CDN-profil nevére.
    * Cserélje le a **myResourceGroupCDN** az erőforráscsoport nevére.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz. 

   Időbe telhet, amíg az új egyéni tartománybeállítások propagálása az összes CDN-határcsomópontra megtörténik: 

- A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
- Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
- A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be.   


---
## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Az egyéni tartomány regisztrációjának befejezése után ellenőrizze, hogy az egyéni tartomány hivatkozik-e a CDN-végpontra.
 
1. Győződjön meg arról, hogy rendelkezik olyan nyilvános tartalommal, amely a végponton van gyorsítótárazva. Ha például a CDN-végpont egy tárfiókkal van társítva, az Azure CDN egy nyilvános tárolóban fogja gyorsítótárazni a tartalmat. Állítsa be a tárolót úgy, hogy engedélyezze a nyilvános hozzáférést, és legalább egy fájlt tartalmaz az egyéni tartomány teszteléséhez.

2. A böngészőjében navigáljon a fájl címére az egyéni tartomány használatával. Ha például az egyéni tartomány `www.contoso.com` , a gyorsítótárazott fájl URL-címe a következő URL-címhez hasonló: `http://www.contoso.com/my-public-container/my-file.jpg` . Győződjön meg arról, hogy az eredmény ugyanaz, mint amikor a CDN-végpontot közvetlenül a. azureedge.net-on éri el. **\<endpoint-hostname>**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal-cleanup)

Ha már nem szeretné egyéni tartományhoz rendelni a végpontot, távolítsa el az egyéni tartományt a következő lépések végrehajtásával:
 
1. A CDN-profilban válassza ki azt a végpontot, amely tartalmazza az eltávolítani kívánt egyéni tartományt.

2. A **Végpont** oldalon, az Egyéni tartományok területen kattintson a jobb gombbal az eltávolítani kívánt egyéni tartományra, és válassza a **Törlés** lehetőséget a helyi menüből. Válassza az **Igen** lehetőséget.

   Az egyéni tartomány társítása a végponttal ekkor megszűnik.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Ha már nem szeretné egyéni tartományhoz rendelni a végpontot, távolítsa el az egyéni tartományt a következő lépések végrehajtásával:

1. A [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) használatával távolítsa el az egyéni tartományt a végpontból:

    * Cserélje le a **myendpoint8675** -t a CDN-végpont nevére.
    * Cserélje le az **www.contoso.com** -t az egyéni tartománynevére.
    * Cserélje le a **myCDN** -t a CDN-profil nevére.
    * Cserélje le a **myResourceGroupCDN** az erőforráscsoport nevére.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány hozzárendelése a CDN-végponthoz.
> - Az egyéni tartomány ellenőrzése.

Lépjen tovább a következő oktatóanyagra, amely azt mutatja be, hogyan konfigurálhatja a HTTPS-t egy Azure CDN egyéni tartományon.

> [!div class="nextstepaction"]
> [Oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon](cdn-custom-ssl.md)