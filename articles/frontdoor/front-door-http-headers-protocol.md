---
title: A HTTP-fejlécek protokoll-támogatása az Azure bejárati ajtóban | Microsoft Docs
description: Ez a cikk a bejárati ajtó által támogatott HTTP-fejlécek protokollait ismerteti.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 7f40b48473c04238d504288307039948fcacf90a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511144"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>A HTTP-fejlécek protokoll-támogatása az Azure-beli bejárati ajtón
Ez a cikk azt a protokollt ismerteti, amelyet a bevezető ajtó támogat a hívási útvonal részeivel (lásd a képet). A következő szakaszokban további információkat talál a bejárati ajtó által támogatott HTTP-fejlécekről.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure bejárati ajtó HTTP-fejlécek protokollja":::

>[!IMPORTANT]
>A bejárati ajtó nem tanúsít olyan HTTP-fejléceket, amelyek nincsenek dokumentálva.

## <a name="client-to-front-door"></a>Ügyfélről a bejárat felé
A bejárati ajtó fogadja a legtöbb fejlécet a bejövő kérelem módosítása nélkül. Egyes fenntartott fejlécek el lesznek távolítva a bejövő kérelemből, ha el vannak küldve, beleértve az X-FD-* előtaggal rendelkező fejléceket is.

## <a name="front-door-to-backend"></a>A háttérbe való bejárati

A bejárati ajtó a bejövő kérelmek fejléceit tartalmazza, kivéve, ha a korlátozások miatt el vannak távolítva. A bejárati ajtó a következő fejléceket is hozzáadja:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Keresztül |  *A: 1,1 Azure-on keresztül* </br> A bejárati ajtó hozzáadja az ügyfél HTTP-verzióját, majd az *Azure* -t a on keresztül fejléc értékeként. Ez a fejléc azt jelzi, hogy az ügyfél HTTP-verziója, és a bejárati ajtó egy köztes címzett volt az ügyfél és a háttér közötti kérelemhez.  |
| X – Azure – Ügyfélip | *X-Azure-Ügyfélip: 127.0.0.1* </br> A feldolgozott kérelemhez társított ügyfél IP-címét jelöli. Egy proxytól érkező kérés például hozzáadhatja az X által továbbított-for fejlécet, hogy jelezze az eredeti hívó IP-címét. |
| X – Azure – SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Azt a szoftvercsatorna IP-címet jelöli, amely a jelenlegi kérelemből származó TCP-kapcsolathoz tartozik. Előfordulhat, hogy a kérelem ügyfél-IP-címe nem egyenlő a szoftvercsatorna IP-címével, mert azt egy felhasználó önkényesen felülírhatja.|
| X – Azure-ref | *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Egy egyedi hivatkozási sztring, amely a bejárati ajtó által kiszolgált kéréseket azonosítja. A rendszer a hozzáférési naplók keresésére, valamint a hibaelhárítás szempontjából kritikus fontosságú megoldására szolgál.|
| X – Azure – RequestChain | *X-Azure-RequestChain: ugrások = 1* </br> Az a fejléc, amelyet a bejárati ajtó használ a kérési hurkok észlelésére, és a felhasználóknak nem kell függőséget végezniük. |
| X – Azure – FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Egy, a kérést azonosító hivatkozási sztring egy adott előtérben lévő erőforrásból származik. Az érték a Azure Portalban látható, vagy a felügyeleti API használatával kérhető le. Ezt a fejlécet az IP ACL-ekkel együtt használva zárolhatja a végpontot úgy, hogy csak egy adott bejárati erőforrástól érkező kéréseket fogadjon. [További részletekért](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) tekintse meg a gyakori kérdéseket |
| X – továbbított – a következőhöz: | *X – továbbított – a következőhöz: 127.0.0.1* </br> Az X-továbbított-for (XFF) HTTP-fejléc mező gyakran azonosítja a webkiszolgálóhoz a HTTP-proxyn vagy a Load balanceren keresztül csatlakozó ügyfél származó IP-címét. Ha van meglévő XFF-fejléc, akkor a bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címét, vagy hozzáadja a XFF-fejlécet az ügyfél szoftvercsatorna IP-címéhez. |
| X-továbbított-gazdagép | *X-továbbított-gazdagép: contoso.azurefd.net* </br> Az X-Forwarded-Host HTTP-fejléc mező az ügyfél által a gazdagép HTTP-kérelmének fejlécében kért eredeti gazdagép azonosítására szolgáló közös módszer. Ennek az az oka, hogy az előtérben lévő gazdagép neve eltérhet a kérést kezelő háttér-kiszolgálótól. |
| X – továbbított – proto | *X – továbbított – proto: http* </br> Az X-Forwarded-proto HTTP-fejléc mező gyakran használatos egy HTTP-kérelem kezdeményező protokolljának azonosítására. A konfiguráción alapuló bejárati ajtó HTTPS használatával kommunikálhat a háttérrel. Ez akkor is igaz, ha a fordított proxyra irányuló kérelem HTTP. |
| X-FD-HealthProbe | Az X-FD-HealthProbe HTTP-fejléc mező a bejárati állapot azonosítására szolgál. Ha ez a fejléc 1 értékre van beállítva, a kérelem állapota az állapot. Akkor használhatja, ha szigorú hozzáférést szeretne elérni az adott bejárati ajtóról az X-Forwarded-Host fejléc mezővel. |
| X – Azure – FDID | *X-Azure-FDID fejléc: 437c82cd-360A-4a54-94c3-5ff707647783* </br> Ez a mező olyan frontdoorID tartalmaz, amelyek segítségével azonosítható, hogy a bejövő kérelem melyik bejárati ajtótól származik. Ezt a mezőt a bejárati ajtó szolgáltatás tölti fel. | 

## <a name="front-door-to-client"></a>Első ajtó az ügyfélnek

A háttérbeli bejáratra küldött fejlécek is át lesznek adva az ügyfélnek. A következőkben a bejárati ajtóról az ügyfeleknek továbbított fejlécek találhatók.

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| X – Azure-ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy egyedi hivatkozási karakterlánc, amely a bejárati ajtó által kiszolgált kéréseket azonosítja, ami kritikus fontosságú a hibaelhárításhoz, mivel a hozzáférési naplók keresésére szolgál.|
| X-cache | *X gyorsítótár: TCP_HIT* </br> Ez a fejléc leírja a kérelem gyorsítótárbeli állapotát, amely lehetővé teszi annak azonosítását, hogy a válasz tartalma a bejárati ajtó gyorsítótárából legyen kézbesítve. |

Az alábbi opcionális válasz fejlécek engedélyezéséhez az "X-Azure-DebugInfo: 1" kérelem fejlécét kell elküldeni.

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| X – Azure – OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Ez a fejléc tartalmazza a háttér által visszaadott HTTP-állapotkódot. Ezen fejléc használatával azonosíthatja a háttérben futó alkalmazás által visszaadott HTTP-állapotkódot a háttérbeli naplók nélkül. Ez az állapotkód eltérő lehet az ügyfélnek a bejárati ajtó által küldött válaszban szereplő HTTP-állapotkódtől. Ez a fejléc lehetővé teszi annak meghatározását, hogy a háttérrendszer működik-e, vagy hogy a probléma a bejárati ajtó szolgáltatással működik-e. |
| X – Azure – InternalError | Ez a fejléc tartalmazza azt a hibakódot, amely bevezeti az ajtót a kérelem feldolgozásakor. Ez a hiba azt jelzi, hogy a probléma belső a bejárati ajtó szolgáltatás/infrastruktúra számára. Probléma jelentése a támogatáshoz.  |
| X – Azure – ExternalError | *X-Azure-ExternalError: 0x830c1011, a hitelesítésszolgáltató ismeretlen.* </br> Ez a fejléc azt a hibakódot jeleníti meg, amelyet a bejárati ajtó-kiszolgálók a háttér-kiszolgálóval való kapcsolat létesítése során hoznak létre a kérelem feldolgozásához. Ez a fejléc segítséget nyújt a bejárati ajtó és a háttérbeli alkalmazás közötti kapcsolat hibáinak azonosításához. Ez a fejléc részletes hibaüzenetet tartalmaz, amely segítséget nyújt a háttérbeli kapcsolati problémák azonosításában (például DNS-feloldás, érvénytelen tanúsítvány stb.). |

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)
