---
title: HTTP/2 támogatás a Azure CDNban | Microsoft Docs
description: További információ a HTTP/2 és a CDN-támogatásról.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67849972"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2 támogatás a Azure CDNban

A HTTP/2 a HTTP/1.1 \ fő változat. Gyorsabb webes teljesítményt, kisebb válaszidőt és továbbfejlesztett felhasználói élményt biztosít, miközben megtartja az ismerős HTTP-metódusokat, az állapot-kódokat és a szemantikai funkciókat. Bár a HTTP/2 a HTTP-vel és a HTTPS-vel való együttműködésre lett tervezve, sok ügyfél-webböngésző csak a HTTP/2 TLS-t támogatja.

### <a name="http2-benefits"></a>HTTP/2 előnyök

A HTTP/2 előnyei a következők:

*   **Multiplex és Egyidejűség**

    A HTTP 1,1 használatával több erőforrás-kérelemhez több TCP-kapcsolat szükséges, és minden kapcsolathoz teljesítménybeli terhelés tartozik. A HTTP/2 lehetővé teszi több erőforrás kérelmezését egyetlen TCP-kapcsolatban.

*   **Fejléc tömörítése**

    Ha tömöríti a HTTP-fejléceket a kiszolgált erőforrásokhoz, a vezetékes idő jelentősen csökken.

*   **Stream-függőségek**

    A stream-függőségek lehetővé teszik az ügyfél számára, hogy jelezze a kiszolgálónak, hogy mely erőforrások rendelkeznek prioritással.


## <a name="http2-browser-support"></a>HTTP/2 böngésző-támogatás

Az összes fő böngésző implementálta a HTTP/2-támogatást a jelenlegi verzióiban. A nem támogatott böngészők automatikusan tartalék HTTP/1.1-re.

|Böngésző|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Operát| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>A HTTP/2 támogatásának engedélyezése Azure CDN

Jelenleg a HTTP/2 támogatás minden Azure CDN profil esetében aktív. Az ügyfelektől nem szükséges további művelet.

## <a name="next-steps"></a>Következő lépések

Ha működés közben szeretné megtekinteni a HTTP/2 előnyeit, tekintse meg [ezt a bemutatót a Akamai](https://http2.akamai.com/demo).

Ha többet szeretne megtudni a HTTP/2-ről, látogasson el az alábbi forrásokra:

*   [HTTP/2 specifikációs Kezdőlap](https://http2.github.io/)
*   [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
*   [Akamai HTTP/2 információ](https://http2.akamai.com/)

Ha többet szeretne megtudni a Azure CDN elérhető funkcióiról, tekintse meg a [Azure CDN áttekintését](https://azure.microsoft.com/documentation/articles/cdn-overview/).