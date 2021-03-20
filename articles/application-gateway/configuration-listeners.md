---
title: Azure Application Gateway figyelő konfigurálása
description: Ez a cikk az Azure Application Gateway-figyelők konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397640"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway figyelő konfigurációja

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A figyelő olyan logikai entitás, amely a port, a protokoll, a gazdagép és az IP-cím használatával ellenőrzi a bejövő kapcsolati kérelmeket. A figyelő konfigurálásakor meg kell adnia azokat az értékeket, amelyek megfelelnek az átjárón bejövő kérelemben szereplő megfelelő értékeknek.

Amikor az Azure Portal használatával hoz létre egy Application Gateway-t, a figyelőhöz tartozó protokoll és port választásával létrehoz egy alapértelmezett figyelőt is. Megadhatja, hogy engedélyezi-e a HTTP2 támogatását a figyelőn. Az Application Gateway létrehozása után szerkesztheti az alapértelmezett figyelő (*appGatewayHttpListener*) beállításait, vagy létrehozhat új figyelőket.

## <a name="listener-type"></a>Figyelő típusa

Új figyelő létrehozásakor az [ *alapszintű* és a *többhelyes* beállítások](./application-gateway-components.md#types-of-listeners)közül választhat.

- Ha azt szeretné, hogy minden kérelem (bármely tartományhoz) el legyen fogadva és továbbítva legyen a háttérbeli készletekbe, válassza az alapszintű lehetőséget. Útmutató az [Application Gateway alapszintű figyelővel való létrehozásához](./quick-create-portal.md).

- Ha a kérelmeket különböző háttér-készletekbe szeretné továbbítani a *állomásfejléc* vagy az állomásnevek alapján, válassza a többhelyes figyelő lehetőséget, ahol meg kell adnia a bejövő kérelemnek megfelelő állomásnevet is. Ennek az az oka, hogy Application Gateway a HTTP 1,1 állomásfejléc-ra támaszkodik, hogy egynél több webhelyet működtessen ugyanazon a nyilvános IP-címen és porton. További információ: [több webhely üzemeltetése Application Gateway használatával](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Feldolgozási figyelők sorrendje

A v1 SKU esetében a kérelmek sorrendje a szabályok sorrendjének és a figyelő típusának megfelelően történik. Ha az alapszintű figyelővel rendelkező szabály először a sorrendben van feldolgozva, a rendszer először feldolgozza, és fogadja az adott portra és IP-kombinációra vonatkozó kéréseket. Ennek elkerülése érdekében először konfigurálja a többhelyes figyelőkkel rendelkező szabályokat, és az alapszintű figyelővel küldje el a szabályt az utolsó listán.

A v2 SKU esetében a többhelyes figyelők az alapszintű figyelők előtt lesznek feldolgozva.

## <a name="front-end-ip-address"></a>Előtérbeli IP-címek

Válassza ki azt az előtér-IP-címet, amelyet hozzá szeretne rendelni ehhez a figyelőhöz. A figyelő a bejövő kérelmeket az adott IP-címen fogja figyelni.

## <a name="front-end-port"></a>Előtér-port

Válassza ki az előtér-portot. Válasszon ki egy meglévő portot, vagy hozzon létre egy újat. Válassza ki a [portok megengedett tartományának](./application-gateway-components.md#ports)tetszőleges értékét. Nem csak a jól ismert portok, például a 80 és a 443, de a megfelelő egyéni portok is használhatók. Egy port használható nyilvános figyelők vagy magánjellegű figyelők számára.

## <a name="protocol"></a>Protokoll

HTTP vagy HTTPS kiválasztása:

- Ha a HTTP lehetőséget választja, az ügyfél és az Application Gateway közötti forgalom titkosítatlan.

- Válassza a HTTPS lehetőséget, ha a [TLS-lezárást](features.md#secure-sockets-layer-ssltls-termination) vagy [a végpontok közötti TLS-titkosítást](./ssl-overview.md)szeretné használni. Az ügyfél és az Application Gateway közötti forgalom titkosítva van. A TLS-kapcsolatok pedig az Application gatewaynél leállnak. Ha a végpontok közötti TLS-titkosítást szeretné használni, a HTTPS beállítást kell választania, és konfigurálnia kell a **háttérrendszer http** -beállítását. Ez biztosítja, hogy a forgalom újra titkosítva legyen, amikor az Application Gateway-ből a háttérbe utazik.


A TLS-megszakítás és a végpontok közötti TLS-titkosítás konfigurálásához hozzá kell adnia egy tanúsítványt a figyelőhöz, hogy az Application Gateway szimmetrikus kulcsot lehessen származtatni. Ezt a TLS protokoll specifikációja határozza meg. A szimmetrikus kulcs az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. Az átjáró tanúsítványának személyes információcsere (PFX) formátumúnak kell lennie. Ez a formátum lehetővé teszi a titkos kulcs exportálását, amelyet az átjáró használ a forgalom titkosításához és visszafejtéséhez.

## <a name="supported-certificates"></a>Támogatott tanúsítványok

Lásd: [a TLS-lezárás és a teljes körű TLS-végpont áttekintése Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>További protokollok támogatása

### <a name="http2-support"></a>HTTP2-támogatás

A HTTP/2 protokoll támogatása csak az Application Gateway-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttér-kiszolgáló készletekkel való kommunikáció HTTP/1.1-n keresztül történik. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. A következő Azure PowerShell kódrészlet azt mutatja be, hogyan engedélyezheti ezt:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket támogatás

A WebSocket-támogatás alapértelmezés szerint engedélyezve van. A felhasználó által konfigurálható beállítás nem engedélyezhető vagy tiltható le. A websocketek a HTTP és a HTTPS-figyelővel is használhatók.

## <a name="custom-error-pages"></a>Egyéni hibalapok

Egyéni hibát a globális szinten vagy a figyelő szintjén adhat meg. Azonban a Azure Portal globális szintű egyéni hibaüzenetek létrehozása jelenleg nem támogatott. Az 403-es webalkalmazási tűzfal hibája, vagy a figyelő szintjén a 502-es karbantartási oldal egyéni hibaüzenetet adhat meg. Meg kell adnia egy nyilvánosan elérhető blob URL-címet is az adott hibakódhoz. További információt az [egyéni Application Gateway-hibaoldalak létrehozását ismertető részben](./custom-error.md) talál.

![Application Gateway hibakódok](/azure/application-gateway/media/custom-error/ag-error-codes.png)

A globális egyéni hiba lap konfigurálásához lásd: [Azure PowerShell konfiguráció](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>TLS-házirend

Központosíthatja a TLS/SSL-tanúsítványok kezelését, és csökkentheti a háttér-kiszolgálófarm titkosítási és visszafejtési terhelését. A központosított TLS-kezelést is lehetővé teszi, hogy megadhat egy központi TLS-házirendet, amely megfelel a biztonsági követelményeknek. Kiválaszthatja az *alapértelmezett*, *előre definiált* vagy *Egyéni* TLS-házirendet.

A TLS-házirend konfigurálásával szabályozhatja a TLS protokoll verzióit. Az Application Gateway konfigurálható úgy, hogy a TLS-kézfogások minimális protokoll-verzióját használják a TLS 1.0, a TLS 1.1 és a TLS 1.2 használatával. Alapértelmezés szerint az SSL 2,0 és 3,0 le van tiltva, és nem konfigurálható. További információ: [Application Gateway TLS-házirend áttekintése](./application-gateway-ssl-policy-overview.md).

A figyelő létrehozása után társítsa azt egy kérelem-útválasztási szabállyal. Ez a szabály határozza meg, hogy a figyelőre érkező kérések hogyan legyenek átirányítva a háttér felé.

## <a name="next-steps"></a>Következő lépések

- [További információ a kérelmek útválasztási szabályairól](configuration-request-routing-rules.md).