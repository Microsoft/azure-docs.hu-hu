---
title: WebSocket-támogatás az Azure Application Gateway
description: Az Application Gateway natív támogatást nyújt a WebSockethez minden átjáróméret esetében. Nincsenek felhasználó által konfigurálható beállítások.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362695"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>A WebSocket-támogatás áttekintése Application Gateway

Az Application Gateway natív támogatást nyújt a WebSockethez minden átjáróméret esetében. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. 

A [RFC6455](https://tools.ietf.org/html/rfc6455) -ben szabványosított WebSocket protokoll lehetővé teszi a kiszolgáló és az ügyfél közötti teljes kétirányú kommunikációt a hosszú ideig futó TCP-kapcsolaton keresztül. Ez a funkció lehetővé teszi a webkiszolgáló és az ügyfél közötti interaktív kommunikációt, amely kétirányú lehet a HTTP-alapú implementációkban szükséges lekérdezési igény nélkül. A WebSocket szolgáltatás a HTTP-vel ellentétben alacsony terheléssel rendelkezik, és a több kérés/válasz esetében ugyanazt a TCP-kapcsolatot használja, ami az erőforrások hatékonyabb kihasználását eredményezi. A WebSocket-protokollok úgy vannak kialakítva, hogy a hagyományos 80-es és 443-as HTTP-portok használatával működjenek

A WebSocket-forgalom fogadásához továbbra is használhat szabványos HTTP-figyelőt a 80-es vagy a 443-es porton. A WebSocket-forgalmat ezután a rendszer az Application Gateway szabályaiban megadott megfelelő háttér-készlettel irányítja át a WebSocket-kompatibilis háttér-kiszolgálóra. A háttér-kiszolgálónak válaszolnia kell az Application Gateway-vizsgálatokra, amelyek az állapot-mintavételi [Áttekintés](application-gateway-probe-overview.md) szakaszban olvashatók. Az Application Gateway állapot-mintavétele csak HTTP/HTTPS. Minden háttér-kiszolgálónak válaszolnia kell az Application Gateway HTTP-mintavételére a WebSocket-forgalom a kiszolgálóra való átirányításához.

A szolgáltatás olyan alkalmazásokban használatos, amelyek a gyors és valós idejű kommunikációt, például a csevegést, az irányítópultot és a játékok alkalmazásait élvezik.

## <a name="how-does-websocket-work"></a>A WebSocket működése

WebSocket-kapcsolat létrehozásához a rendszer egy adott HTTP-alapú kézfogást cserél ki az ügyfél és a kiszolgáló között. Ha a művelet sikeres, az alkalmazás-réteg protokoll a HTTP-ről a WebSockets-re frissül, a korábban létesített TCP-kapcsolat használatával. Ha ez bekövetkezik, a HTTP teljesen ki van a képről; az adatküldés vagy fogadás a WebSocket protokollal mindkét végponton keresztül történhet, amíg a WebSocket-kapcsolat be nem zárul. 

![A diagram összehasonlítja a webkiszolgálóval kommunikáló ügyfelet, és kétszer csatlakozik a két válasz beszerzéséhez, és egy WebSocket-interakcióval, ahol az ügyfél több válasz beszerzéséhez csatlakozik egy kiszolgálóhoz.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Figyelő konfigurációs elem

A WebSocket-forgalom támogatásához egy meglévő HTTP-figyelő is használható. A következő egy httpListeners elemből álló kódrészlet a minta sablonból. A WebSocket és a Secure WebSocket-forgalom támogatásához HTTP-és HTTPS-figyelőre is szükség van. Hasonlóképpen használhatja a portált vagy a Azure PowerShellt, hogy a WebSocket-forgalmat támogató Application Gateway-t hozzon létre a 80/443-es porton lévő figyelőkkel.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, Backendhttpsetting értékre és útválasztási szabályok konfigurálása

A BackendAddressPool WebSocket-kompatibilis kiszolgálókkal rendelkező háttér-készlet definiálására szolgál. A Backendhttpsetting értékre a 80-es és a 443-es háttér-porton van definiálva. A HTTP-beállítások kérésének időtúllépési értéke a WebSocket-munkamenetre is vonatkozik. Nincs szükség módosításra az útválasztási szabályban, amely a megfelelő figyelő a megfelelő háttér-címkészlet összekötésére szolgál. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket-kompatibilis háttérrendszer

A háttérnek a WebSocket működéséhez konfigurált porton (általában 80/443) futó HTTP/HTTPS webkiszolgálóval kell rendelkeznie. Ennek a követelménynek az az oka, hogy a WebSocket protokoll a kezdeti kézfogást igényli a HTTP-ben a WebSocket protokollra való frissítéskor fejlécként. A következő példa egy fejlécre mutat:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Ennek egy másik oka, hogy az Application Gateway backend Health mintavétel csak a HTTP és a HTTPS protokoll használatát támogatja. Ha a háttér-kiszolgáló nem válaszol a HTTP-vagy HTTPS-tesztekre, a rendszer kiveszi a háttér-készletből.

## <a name="next-steps"></a>Következő lépések

A WebSocket-támogatás megismerése után lépjen az [Application Gateway létrehozása lehetőségre](quick-create-powershell.md) egy WebSocket-kompatibilis webalkalmazás használatának megkezdéséhez.