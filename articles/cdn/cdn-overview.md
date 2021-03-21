---
title: Mi az a tartalomkézbesítési hálózat (CDN)? - Azure | Microsoft Docs
description: Megtudhatja, mi az az Azure tartalomkézbesítési hálózat (CDN), valamint hogyan használható a tartalmak nagy sávszélességű kézbesítéséhez.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: 54ee903b490e10f7ea4ca3bca91f742b5811404d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563302"
---
# <a name="what-is-a-content-delivery-network-on-azure"></a>Mit takar a tartalomkézbesítési hálózat az Azure-on?
Egy tartalomkézbesítési hálózat (CDN) kiszolgálók olyan elosztott hálózata, amely hatékonyan kézbesíti a webes tartalmakat a felhasználóknak. A CDNs a késések csökkentése érdekében tárolja a gyorsítótárazott tartalmakat a végfelhasználók számára közel álló (POP) helyszíneken található peremhálózati kiszolgálókon. 

Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé. Az Azure CDN a különféle hálózati optimalizációk CDN POP-k segítségével történő kihasználásával felgyorsíthatja a dinamikus, nem gyorsítótárazható tartalmak kézbesítését is. Például irányíthatja az optimalizációt a Border Gateway Protocol (BGP) megkerülésével.

A webhelyek objektumainak az Azure CDN használatával történő kézbesítése a következő előnyökkel jár:

* Jobb teljesítmény és felhasználói élmény a végfelhasználók számára – különösen az alkalmazások használata esetén, amikor a tartalom betöltéséhez több adatváltásra is szükség van.
* A teljesítmény könnyedén méretezhető az azonnali nagy terheléshez, például a termékbevezetési események kezdetén fellépő magas igényekhez.
* A felhasználói kérelmek elosztásával és a tartalom peremhálózati kiszolgálókról történő közvetlen szolgáltatásával csökken a forráskiszolgálóra jutó forgalom.

A jelenlegi CDN-csomópontok helyének listájáért tekintse meg az [Azure CDN POP-helyeket](cdn-pop-locations.md) ismertető cikket.

## <a name="how-it-works"></a>Működés
![A CDN áttekintése](./media/cdn-overview/cdn-overview.png)

1. Egy felhasználó (Alice) egy fájlt (más néven eszközt) kér egy speciális tartománynevet (például _&lt; végpont neve &gt;_. azureedge.net) használó URL-címmel. Ez a név lehet egy végpont gazdaneve vagy egy egyéni tartomány. A DNS a kérelmet a legjobb teljesítményt nyújtó jelenléti pontra (POP) irányítja. Ez általában a felhasználóhoz földrajzilag legközelebb elhelyezkedő jelenléti pont.
    
2. Ha a jelenléti pont peremhálózati kiszolgálóinak gyorsítótárában nem található meg a fájl, akkor a jelenléti pont lekéri a fájlt a forráskiszolgálóról. A forráskiszolgáló lehet egy Azure-webalkalmazás, az Azure Cloud Service, egy Azure Storage-fiók vagy bármilyen nyilvánosan elérhető webkiszolgáló.
   
3. A forráskiszolgáló visszaküldi a fájlt egy, a jelenléti ponton belüli peremkiszolgálóra.
    
4. A jelenléti pont peremhálózati kiszolgálója gyorsítótárazza a fájlt, és visszaadja az eredeti kérelmezőnek (Anna). A fájl gyorsítótárazva marad a jelenléti pont peremhálózati kiszolgálóján, amíg a HTTP-fejlécekben megadott élettartam (TTL) le nem jár. Ha a forráskiszolgáló nem adott meg élettartamot, akkor az alapértelmezett élettartam hét nap.
    
5. További felhasználók is lekérhetik ugyanazt a fájlt ugyanazzal az Anna által használt URL-címmel, és a rendszer őket is ugyanarra a jelenléti pontra irányíthatja.
    
6. Amennyiben a fájl élettartama még nem járt le, a jelenléti pont peremhálózati kiszolgálója a fájlt közvetlenül a gyorsítótárból adja vissza. A folyamat eredménye nagyobb sebesség, dinamikusabb működés, vagyis összességében jobb felhasználói élmény.

## <a name="requirements"></a>Követelmények
Az Azure CDN használatához rendelkeznie kell legalább egy Azure-előfizetéssel. Továbbá létre kell hoznia legalább egy CDN-profilt, amely egy CDN-végpontok által alkotott gyűjtemény. Minden CDN-végpont egy adott konfigurációt ad meg a tartalomkézbesítési viselkedésre és elérésre vonatkozólag. Ha rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján, több profilt is használhat. Mivel az [Azure CDN-tarifacsomag](https://azure.microsoft.com/pricing/details/cdn/) kiszámítása a CDN-profil szintjén történik, több CDN-profilt kell létrehoznia, ha vegyesen szeretne tarifacsomagokat használni. Az Azure CDN-számlázási struktúrára vonatkozó információért lásd: [Az Azure CDN számlázásának ismertetése](cdn-billing.md).

### <a name="limitations"></a>Korlátozások
Az egyes Azure-előfizetések alapértelmezett korlátokkal rendelkeznek a következő erőforrásokhoz:
 - A létrehozható CDN-profilok száma.
 - A CDN-profilokban létrehozható végpontok száma. 
 - A végpontokra leképezhető egyéni tartományok száma.

A CDN-előfizetés korlátaival kapcsolatos információért lásd a [CDN-korlátokkal](../azure-resource-manager/management/azure-subscription-service-limits.md) kapcsolatos szakaszt.
    
## <a name="azure-cdn-features"></a>Az Azure CDN szolgáltatásai
Az Azure CDN az alábbi főbb szolgáltatásokat kínálja:

- [Dinamikus helygyorsítás](cdn-dynamic-site-acceleration.md)
- [CDN-gyorsítótárazási szabályok](cdn-caching-rules.md)
- [HTTPS használata egyéni tartományban](cdn-custom-ssl.md)
- [Azure-beli diagnosztikai naplók](cdn-azure-diagnostic-logs.md)
- [Fájltömörítés](cdn-improve-performance.md)
- [Földrajzi szűrés](cdn-restrict-access-by-country.md)

Az egyes Azure CDN termékek által támogatott szolgáltatások teljes listájáért tekintse meg az [Azure CDN termékek szolgáltatásait összehasonlító](cdn-features.md) részt.

## <a name="next-steps"></a>Következő lépések

- A CDN használatának első lépései: [Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md).
- A CDN-végpontjait a [Microsoft Azure Portal](https://portal.azure.com) vagy a [PowerShell](cdn-manage-powershell.md) használatával kezelheti.
- Ismerje meg, hogyan automatizálhatja az Azure CDN-t a [.NET](cdn-app-dev-net.md) vagy a [Node.js](cdn-app-dev-node.md) segítségével.
