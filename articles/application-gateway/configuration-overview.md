---
title: Az Azure Application Gateway konfigurációjának áttekintése
description: Ez a cikk az Azure-Application Gateway összetevőinek konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89651999"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway konfiguráció áttekintése

Az Azure Application Gateway számos olyan összetevőből áll, amelyek különböző helyzetekben különböző módokon konfigurálhatók. Ez a cikk bemutatja, hogyan konfigurálhatja az egyes összetevőket.

![Application Gateway összetevők folyamatábrája](./media/configuration-overview/configuration-overview1.png)

Ez a rendszerkép olyan alkalmazást mutat be, amely három figyelővel rendelkezik. Az első kettő a és a többhelyes figyelő `http://acme.com/*` `http://fabrikam.com/*` . Figyelje a 80-es portot is. A harmadik egy alapszintű figyelő, amely végpontok közötti Transport Layer Security (TLS) megszakítással, korábbi nevén SSL (SSL) megszakítással rendelkezik.

## <a name="infrastructure"></a>Infrastruktúra

A Application Gateway infrastruktúra magában foglalja a virtuális hálózatot, az alhálózatokat, a hálózati biztonsági csoportokat és a felhasználó által megadott útvonalakat.

További információ: [Application Gateway infrastruktúra-konfiguráció](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Előtérbeli IP-címek

Beállíthatja, hogy az Application Gateway nyilvános IP-címmel, magánhálózati IP-címmel vagy mindkettővel rendelkezzen. Egy nyilvános IP-címet kell megadni, ha a háttérrendszer futtatásakor az ügyfeleknek internetre irányuló virtuális IP-címmel (VIP) keresztül kell elérniük az internetet.

További információ: [Application Gateway előtér-IP-cím konfigurálása](configuration-front-end-ip.md).

## <a name="listeners"></a>Figyelők

A figyelő olyan logikai entitás, amely a port, a protokoll, a gazdagép és az IP-cím használatával ellenőrzi a bejövő kapcsolati kérelmeket. A figyelő konfigurálásakor meg kell adnia azokat az értékeket, amelyek megfelelnek az átjárón bejövő kérelemben szereplő megfelelő értékeknek.

További információ: [Application Gateway figyelő konfiguráció](configuration-listeners.md).

## <a name="request-routing-rules"></a>Kérelemirányítási szabályok

Amikor az Azure Portal használatával hoz létre egy Application Gateway-t, létrehoz egy alapértelmezett szabályt (*rule1*). Ez a szabály az alapértelmezett háttér-készlettel (*appGatewayBackendPool*) és az alapértelmezett HÁTTÉRbeli http-beállításokkal (*appGatewayBackendHttpSettings*) köti össze az alapértelmezett figyelőt (*appGatewayHttpListener*). Az átjáró létrehozása után szerkesztheti az alapértelmezett szabály beállításait, vagy létrehozhat új szabályokat is.

További információ: [Application Gateway kérelmek útválasztási szabályai](configuration-request-routing-rules.md).

## <a name="http-settings"></a>HTTP-beállítások

Az Application Gateway az itt megadott konfiguráció használatával irányítja át a forgalmat a háttér-kiszolgálókra. A HTTP-beállítás létrehozása után hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz.

További információ: [Application Gateway http-beállítások konfigurálása](configuration-http-settings.md).

## <a name="back-end-pool"></a>Háttérkészlet

Egy háttér-készletet a háttérbeli tagok négy típusára irányíthat: egy adott virtuális gépet, egy virtuálisgép-méretezési készletet, egy IP-címet/teljes tartománynevet vagy egy app Service-t. 

Miután létrehozta a háttér-készletet, hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz. Az alkalmazás-átjárón az összes háttér-készlethez is be kell állítania az állapot-mintavételt. Ha egy kérés-útválasztási szabály feltétele teljesül, az Application Gateway továbbítja a forgalmat a megfelelő háttér-készletben lévő kifogástalan állapotú kiszolgálókra.

## <a name="health-probes"></a>Állapotminták

Az Application Gateway alapértelmezés szerint figyeli az összes erőforrás állapotát a háttérben. Javasoljuk azonban, hogy az egyes háttérbeli HTTP-beállításokhoz hozzon létre egy egyéni mintavételt, hogy jobban kézben legyen az állapot figyelése. Az egyéni mintavétel konfigurálásával kapcsolatos további információkért lásd: [Egyéni állapot mintavételi beállításai](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Az egyéni állapotú mintavétel létrehozása után hozzá kell rendelnie azt egy háttérbeli HTTP-beállításhoz. Az egyéni mintavétel nem figyeli a háttér-készlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon van társítva egy figyelővel egy szabály használatával.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Application Gateway összetevőket, a következőket teheti:

- [Application Gateway létrehozása a Azure Portalban](quick-create-portal.md)
- [Application Gateway létrehozása a PowerShell használatával](quick-create-powershell.md)
- [Application Gateway létrehozása az Azure CLI használatával](quick-create-cli.md)
