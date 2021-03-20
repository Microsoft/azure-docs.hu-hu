---
title: Azure DNS integrálása az Azure-erőforrásokkal – Azure DNS
description: Ebből a cikkből megtudhatja, hogyan használható a Azure DNS együtt a DNS Azure-erőforrások számára történő biztosításához.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487398"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Az Azure-szolgáltatás egyéni tartományi beállításainak megadása a Azure DNS használatával

A Azure DNS a DNS-t az egyéni tartományokat támogató vagy teljes tartománynevet (FQDN) használó Azure-erőforrások számára biztosít egyéni tartományhoz. Ilyen például egy Azure-webalkalmazás, és azt szeretné, hogy a felhasználók a contoso.com vagy a www \. contoso.com teljes tartománynevével férhessenek hozzá. Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure-szolgáltatást Azure DNS az egyéni tartományok használatára.

## <a name="prerequisites"></a>Előfeltételek

Ha az egyéni tartományhoz Azure DNS szeretne használni, először delegálnia kell a tartományt a Azure DNSra. Látogasson el [a tartomány delegálása Azure DNSre](./dns-delegate-domain-azure-dns.md) , amely útmutatást nyújt a névkiszolgálók delegáláshoz való konfigurálásához. Ha a tartomány delegálva van a Azure DNS zónába, konfigurálhatja a szükséges DNS-rekordokat.

Az [Azure Function apps](#azure-function-app), a [nyilvános IP-címek](#public-ip-address), a [app Service (Web Apps)](#app-service-web-apps), a [blob Storage](#blob-storage)és a [Azure CDN](#azure-cdn)esetében egy hiúság vagy egyéni tartomány is konfigurálható.

## <a name="azure-function-app"></a>Azure-függvényalkalmazás

Az Azure Function apps egyéni tartományának konfigurálásához létre kell hoznia egy CNAME rekordot, valamint magát a Function alkalmazás konfigurációját is.
 
Navigáljon **függvényalkalmazás** , és válassza ki a Function alkalmazást. Kattintson a **platform szolgáltatások** elemre, majd a **hálózat** területen kattintson az **Egyéni tartományok** elemre.

![function alkalmazás panel](./media/dns-custom-domain/functionapp.png)

Jegyezze fel az aktuális URL-címet az **Egyéni tartományok** panelen, ez a cím lesz a létrehozott DNS-rekord aliasa.

![egyéni tartomány panel](./media/dns-custom-domain/functionshostname.png)

Navigáljon a DNS-zónához, és kattintson a **+ rekordazonosító** elemre. Adja meg a következő információkat a **rekordazonosító hozzáadása** panelen, és kattintson **az OK** gombra a létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | myfunctionapp        | Ez az érték a tartománynév címkével együtt az Egyéni tartománynév teljes tartománynevét adja meg.        |
|Típus     | CNAME        | CNAME rekord használata alias használatával.        |
|TTL     | 1        | 1 óra használatos        |
|TTL-egység     | Óra        | A rendszer órákat használ az idő méréséhez         |
|Alias     | adatumfunction.azurewebsites.net        | Az aliast létrehozó DNS-név, ebben a példában ez a adatumfunction.azurewebsites.net DNS-név, amely alapértelmezés szerint a Function alkalmazáshoz van megadva.        |

Váltson vissza a Function alkalmazásra, kattintson a **platform szolgáltatásai** lehetőségre, majd a **hálózat** területen kattintson az **Egyéni tartományok** lehetőségre, majd az **Egyéni állomásnevek** elemre, végül az **állomásnév hozzáadása** lehetőségre.

Az **állomásnév hozzáadása** panelen írja be a CNAME rekordot az **állomásnév** szövegmezőbe, majd kattintson az **Érvényesítés** elemre. Ha a rekord megtalálható, megjelenik az **állomásnév hozzáadása** gomb. Az alias hozzáadásához kattintson az **állomásnév hozzáadása** elemre.

![function apps – állomásnév hozzáadása panel](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Nyilvános IP-cím

Ha egyéni tartományt szeretne konfigurálni olyan szolgáltatások számára, amelyek nyilvános IP-cím erőforrást használnak, például A Application Gateway, A Load Balancer, A Cloud Service, A Resource Manager-alapú virtuális gépek és a klasszikus virtuális gépek egy rekordot használnak.

Navigáljon a **hálózat**  >  **nyilvános IP-címéhez**, válassza ki a nyilvános IP-címet, és kattintson a **konfiguráció** elemre. A megjelenített IP-cím.

![nyilvános IP-cím panel](./media/dns-custom-domain/publicip.png)

Navigáljon a DNS-zónához, és kattintson a **+ rekordazonosító** elemre. Adja meg a következő információkat a **rekordazonosító hozzáadása** panelen, és kattintson **az OK** gombra a létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | mywebserver        | Ez az érték a tartománynév címkével együtt az Egyéni tartománynév teljes tartománynevét adja meg.        |
|Típus     | A        | Használjon egy rekordot, mert az erőforrás egy IP-cím.        |
|TTL     | 1        | 1 óra használatos        |
|TTL-egység     | Óra        | A rendszer órákat használ az idő méréséhez         |
|IP-cím     | `<your ip address>`       | A nyilvános IP-cím.|

![rekord létrehozása](./media/dns-custom-domain/arecord.png)

Az a rekord létrehozása után futtassa a parancsot a `nslookup` rekord feloldásának ellenőrzéséhez.

![nyilvános IP-cím DNS-keresés](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Az alábbi lépések végigvezetik az egyéni tartomány konfigurálásának lépésein egy app Service-webalkalmazáshoz.

Navigáljon **app Servicere** , és válassza ki az egyéni tartománynevet konfiguráló erőforrást, majd kattintson az **Egyéni tartományok** elemre.

Jegyezze fel az aktuális URL-címet az **Egyéni tartományok** panelen, ez a cím lesz a létrehozott DNS-rekord aliasa.

![Egyéni tartományok panel](./media/dns-custom-domain/url.png)

Navigáljon a DNS-zónához, és kattintson a **+ rekordazonosító** elemre. Adja meg a következő információkat a **rekordazonosító hozzáadása** panelen, és kattintson **az OK** gombra a létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | mywebserver        | Ez az érték a tartománynév címkével együtt az Egyéni tartománynév teljes tartománynevét adja meg.        |
|Típus     | CNAME        | CNAME rekord használata alias használatával. Ha az erőforrás IP-címet használt, a rendszer egy rekordot fog használni.        |
|TTL     | 1        | 1 óra használatos        |
|TTL-egység     | Óra        | A rendszer órákat használ az idő méréséhez         |
|Alias     | webserver.azurewebsites.net        | Az aliast létrehozó DNS-név, ebben a példában ez a webserver.azurewebsites.net DNS-név, amely alapértelmezés szerint a webalkalmazáshoz van megadva.        |


![CNAME-rekord létrehozása](./media/dns-custom-domain/createcnamerecord.png)

Váltson vissza az egyéni tartománynévhez konfigurált app Service-be. Kattintson az **Egyéni tartományok**, majd az **állomásnevek** elemre. A létrehozott CNAME rekord hozzáadásához kattintson a **+ állomásnév hozzáadása** lehetőségre.

![Képernyőfelvétel: a + állomásnév hozzáadása gomb.](./media/dns-custom-domain/figure1.png)

A folyamat befejezése után futtassa az **nslookup** parancsot a névfeloldás ellenőrzéséhez.

![1. ábra](./media/dns-custom-domain/finalnslookup.png)

Ha többet szeretne megtudni az egyéni tartomány App Servicera való leképezéséről, látogasson el a [meglévő egyéni DNS-név leképezése az Azure Web Apps-be](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)elemre.

Az aktív DNS-nevek áttelepítésének megismeréséhez tekintse meg [az aktív DNS-név Áttelepítését Azure app Servicere](../app-service/manage-custom-dns-migrate-domain.md)című témakört.

Ha egyéni tartományt kell vásárolnia, látogasson el az [Azure Web Apps Egyéni tartománynév megvásárlása](../app-service/manage-custom-dns-buy-domain.md) lehetőségre, és ismerkedjen meg a app Service-tartományokkal.

## <a name="blob-storage"></a>Blob Storage

A következő lépések végigvezetik a blob Storage-fiókhoz tartozó CNAME-rekordok konfigurálásának lépésein a asverify metódus használatával. Ez a módszer biztosítja, hogy nincs leállás.

Navigáljon a **Storage**  >  **Storage-fiókok** elemre, válassza ki a Storage-fiókját, és kattintson az **egyéni tartomány** lehetőségre. A 2. lépés alatt található teljes tartománynevet az első CNAME rekord létrehozásához használja a rendszer.

![BLOB Storage – egyéni tartomány](./media/dns-custom-domain/blobcustomdomain.png)

Navigáljon a DNS-zónához, és kattintson a **+ rekordazonosító** elemre. Adja meg a következő információkat a **rekordazonosító hozzáadása** panelen, és kattintson **az OK** gombra a létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | asverify. mystorageaccount        | Ez az érték a tartománynév címkével együtt az Egyéni tartománynév teljes tartománynevét adja meg.        |
|Típus     | CNAME        | CNAME rekord használata alias használatával.        |
|TTL     | 1        | 1 óra használatos        |
|TTL-egység     | Óra        | A rendszer órákat használ az idő méréséhez         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Az aliast létrehozó DNS-név, ebben a példában ez a asverify.adatumfunctiona9ed.blob.core.windows.net DNS-név, amely alapértelmezés szerint a Storage-fiókhoz van megadva.        |

A Storage-fiókok elemre **kattintva térjen** vissza a Storage-fiókra  >  , válassza ki a Storage-fiókját, és kattintson az **egyéni tartomány** lehetőségre. Írja be a asverify előtag nélkül létrehozott aliast a szövegmezőbe, jelölje be a **közvetett CNAME ellenőrzés használata** jelölőnégyzetet, majd kattintson a **Mentés** gombra. Ha ez a lépés elkészült, térjen vissza a DNS-zónához, és hozzon létre egy CNAME-rekordot a asverify előtag nélkül.  Ezután nyugodtan törölheti a CNAME-rekordot a cdnverify előtaggal.

![Képernyőkép, amely az egyéni tartomány lapot mutatja.](./media/dns-custom-domain/indirectvalidate.png)

DNS-feloldás ellenőrzése futtatásával `nslookup`

További információ az egyéni tartomány blob Storage-végpontra való leképezéséről [: Egyéni tartománynév beállítása a blob Storage-végponthoz](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

A következő lépések végigvezetik a cdnverify metódus használatával a CDN-végpont CNAME rekordjának konfigurálásán. Ez a módszer biztosítja, hogy nincs leállás.

Navigáljon a **Network**  >  **CDN-profilok** elemre, és válassza ki a CDN-profilját.

Válassza ki azt a végpontot, amellyel dolgozik, és kattintson az **+ egyéni tartomány** lehetőségre. Jegyezze fel a **végpont állomásnévjét** , mert ez az érték az a rekord, amelyet a CNAME-rekord mutat.

![Egyéni CDN-tartomány](./media/dns-custom-domain/endpointcustomdomain.png)

Navigáljon a DNS-zónához, és kattintson a **+ rekordazonosító** elemre. Adja meg a következő információkat a **rekordazonosító hozzáadása** panelen, és kattintson **az OK** gombra a létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | cdnverify. mycdnendpoint        | Ez az érték a tartománynév címkével együtt az Egyéni tartománynév teljes tartománynevét adja meg.        |
|Típus     | CNAME        | CNAME rekord használata alias használatával.        |
|TTL     | 1        | 1 óra használatos        |
|TTL-egység     | Óra        | A rendszer órákat használ az idő méréséhez         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Az aliast létrehozó DNS-név, ebben a példában ez a cdnverify.adatumcdnendpoint.azureedge.net DNS-név, amely alapértelmezés szerint a Storage-fiókhoz van megadva.        |

Váltson vissza a CDN-végpontra a **hálózati**  >  **CDN-profilok** elemre kattintva, és válassza ki a CDN-profilját. Kattintson az **+ egyéni tartomány** lehetőségre, és adja meg a CNAME rekord aliasát a cdnverify előtag nélkül, majd kattintson a **Hozzáadás** gombra.

Ha ez a lépés elkészült, térjen vissza a DNS-zónához, és hozzon létre egy CNAME-rekordot a cdnverify előtag nélkül.  Ezután nyugodtan törölheti a CNAME-rekordot a cdnverify előtaggal. A CDN-ről és az egyéni tartománynak a közbenső regisztrációs lépés nélküli konfigurálásával kapcsolatos további információkért tekintse meg a [Azure CDN tartalmat egy egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhat fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz](dns-reverse-dns-for-azure-services.md).
