---
title: Azure Application Gateway URL-alapú tartalom-útválasztás áttekintése
description: Ez a cikk áttekintést nyújt az Azure Application Gateway URL-alapú tartalom-útválasztási, UrlPathMap-konfigurációs és PathBasedRouting-szabályról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a9b2e8148586ec58ea6a7a033099e726920857b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84987934"
---
# <a name="url-path-based-routing-overview"></a>Az URL-alapú útválasztás áttekintése

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. 

Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket a megfelelő háttér-kiszolgálókészlethez irányítja.

Az alábbi példában az alkalmazásátjáró a contoso.com webhelyet szolgálja ki a VideoServerPool, az ImageServerPool és a DefaultServerPool háttér-kiszolgálókészlettel.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

A http \: //contoso.com/video/* kérelmeket a rendszer átirányítja a VideoServerPool, a http \: //contoso.com/images/* pedig a ImageServerPool. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

> [!IMPORTANT]
> A v1 SKU esetében a szabályok a portálon felsorolt sorrendben lesznek feldolgozva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt. A v2 SKU esetében a pontos egyezések magasabb prioritással rendelkeznek. Azonban javasoljuk, hogy először konfigurálja a többhelyes figyelőket az alapszintű figyelő konfigurálása előtt. Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva.

## <a name="urlpathmap-configuration-element"></a>Az UrlPathMap konfigurációs elem

Az UrlPathMap elem elérésiút-minták meghatározására szolgál a háttér-kiszolgálókészletek leképezésében. Az alábbi kódpélda a sablonfájlból származó urlPathMap elem kódrészlete.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

A PathPattern az elérési utak mintáinak listája. Minden mintának a / jellel kell kezdődnie, a „*” jel pedig kizárólag a mintavégi „/” jel után állhat. Az elérési úthoz Matcher betáplált karakterlánc nem tartalmaz szöveget az első után? vagy #, és ezek a karakterek nem engedélyezettek itt. Ellenkező esetben az URL-címekben engedélyezett karakterek engedélyezettek a PathPattern.

A támogatott minták attól függnek, hogy Application Gateway v1-es vagy v2-es verzióját telepíti:

#### <a name="v1"></a>v1

Az elérésiút-szabályok a kis-és nagybetűk megkülönböztetése.

|v1 elérésiút-minta  |Támogatott?  |
|---------|---------|
|`/images/*`     |igen|
|`/images*`     |igen|
|`/images/*.jpg`     |nem|
|`/*.jpg`     |nem|
|`/Repos/*/Comments/*`     |nem|
|`/CurrentUser/Comments/*`     |igen|

#### <a name="v2"></a>v2

Az elérésiút-szabályok a kis-és nagybetűk megkülönböztetése.

|v2 elérésiút-minta  |Támogatott?  |
|---------|---------|
|`/images/*`     |igen|
|`/images*`     |igen|
|`/images/*.jpg`     |nem|
|`/*.jpg`     |nem|
|`/Repos/*/Comments/*`     |nem|
|`/CurrentUser/Comments/*`     |igen|

További információért tekintse át az [URL-alapú átirányításhoz készült Resource Manager-sablonokat](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

## <a name="pathbasedrouting-rule"></a>PathBasedRouting szabály

A PathBasedRouting típusú RequestRoutingRule szabály arra szolgál, hogy egy figyelőt az adott urlPathMap elemhez kössön. Minden kérés, amely ehhez a figyelőhöz kapcsolódik, az urlPathMap elemben meghatározott irányelvek alapján lesz átirányítva.
A PathBasedRouting szabály kódrészlete:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>Következő lépések

Miután elsajátította az URL-alapú tartalom-átirányításról szóló ismereteket, látogasson el [az URL-alapú átirányítást használó alkalmazásátjáró létrehozását bemutató témakörhöz](create-url-route-portal.md).
