---
title: Http-fejlécek protokolltámogatása a Azure Front Door | Microsoft Docs
description: Ez a cikk a támogatott HTTP Front Door protokollokat ismerteti.
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
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785756"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Http-fejlécek protokolltámogatása a Azure Front Door
Ez a cikk a hívási útvonal Front Door protokollt ismerteti (lásd a képet). A következő szakaszok további információkat tartalmaznak a támogatott HTTP-fejlécekkel Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door HTTP-fejlécek protokollja":::

>[!IMPORTANT]
>Front Door nem minősíti az itt nem dokumentált HTTP-fejléceket.

## <a name="client-to-front-door"></a>Az ügyfél számára Front Door
Front Door a legtöbb fejlécet elfogadja a bejövő kéréshez azok módosítása nélkül. A rendszer eltávolít néhány fenntartott fejlécet a bejövő kérésből, beleértve az X-FD-* előtagú fejléceket is.

## <a name="front-door-to-backend"></a>Front Door a háttérhez

Front Door fejléceket tartalmaz a bejövő kéréshez, kivéve, ha korlátozások miatt eltávolítja őket. Front Door a következő fejléceket is hozzáadja:

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| Via |  *Keresztül: 1.1 Azure* </br> Front Door hozzáadja az ügyfél HTTP-verzióját, majd az *Azure-t* az Via fejléc értékeként. Ez a fejléc jelzi az ügyfél HTTP-verzióját, Front Door az ügyfél és a háttéralkalmazás közötti kérés köztes címzettje volt.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> A feldolgozott kéréshez társított ügyfél IP-címét jelöli. Egy proxyról érkező kérés például hozzáadhatja az X-Forwarded-For fejlécet az eredeti hívó IP-címének jelzéséhez. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Annak a TCP-kapcsolatnak a szoftvercsatorna IP-címét jelöli, amelyről az aktuális kérés származik. Előfordulhat, hogy egy kérés ügyfél IP-címe nem egyenlő a szoftvercsatorna IP-címével, mert a felhasználó tetszőlegesen felülírhatja az ügyfél IP-címét.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYWYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Egyedi hivatkozási sztring, amely azonosítja a kérést, amelyet a Front Door. A hozzáférési naplók keresését és a hibaelhárítás szempontjából kritikus fontosságú adatokat használja.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Egy fejléc, Front Door észleli a kéréshurkokat, és a felhasználók nem függnek a kéréstől. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> A kérést azonosító hivatkozási sztring egy adott Front Door érkezett. Az érték látható a Azure Portal felügyeleti API-val lekérhető. Ezt a fejlécet IP ACL-ekkel együtt használva zárolhatja a végpontot, hogy csak egy adott erőforrástól Front Door kéréseket. További részletekért tekintse meg [a gyakori kérdéseket](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-for | *X-Forwarded-for: 127.0.0.1* </br> Az X-Forwarded-for (XFF) HTTP-fejlécmező gyakran azonosítja a webkiszolgálóhoz HTTP-proxyn vagy terheléselosztáson keresztül csatlakozó ügyfél eredeti IP-címét. Ha van meglévő XFF-fejléc, a Front Door hozzáfűzi hozzá az ügyfél szoftvercsatornája IP-címét, vagy hozzáadja az XFF-fejlécet az ügyfél szoftvercsatorna IP-címével. |
| X-Forwarded-Host | *X-Forwarded-Host (X-továbbított gazdagép): contoso.azurefd.net* </br> Az X-Forwarded-Host HTTP-fejlécmező egy gyakori metódus, amely azonosítja az ügyfél által kért eredeti gazdagépet a Gazdagép HTTP-kérés fejlécében. Ennek az az oka, hogy a Front Door gazdaneve eltérhet a kérést kezelő háttérkiszolgáló esetében. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Az X-Forwarded-Proto HTTP-fejlécmezőt gyakran használják a HTTP-kérések eredeti protokollja azonosítására. Front Door konfiguráción alapuló kapcsolatok HTTPS használatával kommunikálhatnak a háttérvel. Ez akkor is igaz, ha a fordított proxynak adott kérés HTTP. |
| X-FD-HealthProbe | Az X-FD-HealthProbe HTTP-fejlécmező az állapot-mintavétel azonosítására használható a Front Door. Ha ez a fejléc 1-re van állítva, a kérés állapot-mintavétel. Akkor használhatja a parancsot, ha szigorú hozzáférést szeretne Front Door X-Forwarded-Host fejlécmezővel. |
| X-Azure-FDID | *X-Azure-FDID fejléc: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Ez a mező tartalmazza a frontdoorID azonosítót, amellyel azonosítható, Front Door a bejövő kérés melyik részből érkezik. Ezt a mezőt a Front Door tölti ki. | 

## <a name="front-door-to-client"></a>Front Door ügyfélhez

A háttérből a Front Door küldött fejlécek is át vannak küldve az ügyfélnek. A következő fejlécek a Front Door küldve.

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYWYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ez egy egyedi hivatkozási sztring, amely azonosítja a Front Door által kiszolgált kérést, ami kritikus fontosságú a hibaelhárításhoz, mivel a hozzáférési naplókban való kereséshez használatos.|
| X-Cache | *X-Cache: TCP_HIT* </br> Ez a fejléc ismerteti a kérés gyorsítótárának állapotát, amely lehetővé teszi annak azonosítását, hogy a válasz tartalma a kérés gyorsítótára által kiszolgált Front Door. |

A következő választható válaszfejlécek engedélyezéséhez el kell küldenie az "X-Azure-DebugInfo: 1" kérelemfejlécet.

| Fejléc  | Példa és leírás |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Ez a fejléc tartalmazza a háttér által visszaadott HTTP-állapotkódot. Ezzel a fejléccel azonosíthatja a háttéralkalmazás által visszaadott HTTP-állapotkódot anélkül, hogy végigfut a háttérnaplókon. Ez az állapotkód különbözhet a http-állapotkódtól a válaszban, amelyet az ügyfélnek a Front Door. Ez a fejléc lehetővé teszi annak megállapítását, hogy a háttérkiszolgáló helytelenül viselkedik-e, vagy a probléma a Front Door szolgáltatással. |
| X-Azure-InternalError | Ez a fejléc tartalmazza a kérelem feldolgozásakor Front Door hibakódot. Ez a hiba azt jelzi, hogy a probléma a Front Door szolgáltatáson/infrastruktúrán belül van. Jelentse a problémát a támogatásnak.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, A hitelesítésszolgáltató ismeretlen.* </br> Ez a fejléc azt a hibakódot Front Door, amely a kérések feldolgozása érdekében a háttérkiszolgálóval való kapcsolat létesítését jelenti. Ez a fejléc segít azonosítani a problémákat a Front Door és a háttéralkalmazás közötti kapcsolatban. Ez a fejléc egy részletes hibaüzenetet tartalmaz, amely segít azonosítani a háttérhez való kapcsolódási problémákat (például DNS-feloldás, érvénytelen tanúsítvány stb.). |

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó létrehozása](quickstart-create-front-door.md)
- [Az Front Door működése](front-door-routing-architecture.md)
