---
title: A TLS-házirend áttekintése az Azure Application Gateway
description: Ismerje meg, hogyan konfigurálhatja a TLS-házirendet az Azure Application Gatewayhoz, és csökkentheti a háttérbeli kiszolgálófarm titkosítását és visszafejtését.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253630"
---
# <a name="application-gateway-tls-policy-overview"></a>Application Gateway TLS-házirend áttekintése

Az Azure Application Gateway segítségével központosíthatja a TLS/SSL-tanúsítványok kezelését, és csökkentheti a háttérbeli kiszolgálófarm titkosítási és visszafejtési terhelését. Ez a központosított TLS-kezelési szolgáltatás azt is lehetővé teszi, hogy megadhat egy központi TLS-házirendet, amely megfelel a szervezeti biztonsági követelményeknek. Ez segít a megfelelőségi követelmények, valamint a biztonsági irányelvek és a javasolt eljárások teljesítésében.

A TLS-házirend szabályozza a TLS protokoll verziószámát, valamint a titkosító csomagokat, valamint azt is, hogy a titkosítási protokollok milyen sorrendben használatosak a TLS-kézfogások során. Application Gateway két mechanizmust biztosít a TLS-házirendek szabályozásához. Használhat előre definiált szabályzatot vagy egyéni szabályzatot is.

## <a name="predefined-tls-policy"></a>Előre definiált TLS-házirend

Application Gateway három előre definiált biztonsági házirenddel rendelkezik. Ezen szabályzatok bármelyikével konfigurálhatja az átjárót a megfelelő szintű biztonság eléréséhez. A szabályzatok neveit az év és a hónap, amelyben konfigurálták. Az egyes házirendek különböző TLS protokoll-és titkosítási csomagokat biztosítanak. Javasoljuk, hogy a legújabb TLS-házirendeket használja a legjobb TLS-biztonság biztosításához.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Tulajdonság  |Érték  |
|---|---|
|Name (Név)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Alapértelmezett| True (ha nincs megadva előre definiált házirend) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Tulajdonság  |Érték  |
|   ---      |  ---       |
|Name (Név)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Tulajdonság  |Érték  |
|---|---|
|Name (Név)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Egyéni TLS-házirend

Ha az előre definiált TLS-szabályzatot be kell állítani a követelményekhez, meg kell határoznia a saját egyéni TLS-házirendjét. Egyéni TLS-szabályzattal teljes mértékben szabályozhatja a TLS protokoll minimális verziójának támogatását, valamint a támogatott titkosítási csomagokat és azok prioritási sorrendjét.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL protokoll verziói

* Az SSL 2,0 és a 3,0 alapértelmezés szerint le van tiltva az összes Application Gateway átjáró esetében. A protokollok verziószáma nem konfigurálható.
* Az egyéni TLS-házirend lehetőséget ad a következő három protokoll egyikének kiválasztására az átjáró minimális TLS protokolljának verziójaként: TLSv1_0, TLSv1_1 és TLSv1_2.
* Ha nincs megadva TLS-házirend, mindhárom protokoll (TLSv1_0, TLSv1_1 és TLSv1_2) engedélyezve van.

### <a name="cipher-suites"></a>Titkosítási csomagok

Application Gateway a következő titkosítási csomagokat támogatja, amelyekről kiválaszthatja az egyéni házirendet. A titkosítási csomagok sorrendje meghatározza a prioritási sorrendet a TLS-egyeztetés során.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> A kapcsolatban használt TLS titkosítási csomagok a használt tanúsítvány típusától függően is megtalálhatók. Az ügyfél és az Application Gateway közötti kapcsolatok esetében a használt titkosítási csomagok az Application Gateway-figyelő kiszolgálói tanúsítványainak típusán alapulnak. Az Application Gateway és a háttérrendszer-készlet kapcsolatai között a használt titkosítási csomagok a háttérrendszer kiszolgálói tanúsítványainak típusától függenek.

## <a name="known-issue"></a>Ismert probléma
A Application Gateway v2 jelenleg nem támogatja a következő titkosítási műveleteket:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a TLS-házirend konfigurálásáról, olvassa el [a TLS-házirend verzióinak és a titkosítási csomagok konfigurálása Application Gatewayon](application-gateway-configure-ssl-policy-powershell.md)című témakört.
