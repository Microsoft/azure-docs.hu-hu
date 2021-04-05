---
title: Magánhálózati IP-cím használata belső útválasztáshoz egy bejövő végpont esetében
description: Ez a cikk azt ismerteti, hogyan használhatók a magánhálózati IP-címek a belső útválasztáshoz, és így a fürtön belüli bejövő végpontok a többi VNet legyenek kitéve.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397308"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Magánhálózati IP használata belső útválasztáshoz egy bejövő végpont esetében 

Ez a funkció lehetővé teszi, hogy a bejövő végpontot a `Virtual Network` privát IP-cím használatával tegye elérhetővé.

## <a name="pre-requisites"></a>Előfeltételek  
Application Gateway [magánhálózati IP-konfigurációval](./configure-application-gateway-with-private-frontend-ip.md)

A vezérlőt kétféleképpen lehet úgy konfigurálni, hogy magánhálózati IP-címet használjon a bejövő forgalom számára.

## <a name="assign-to-a-particular-ingress"></a>Hozzárendelés egy adott bejövő forgalomhoz
Ha a magánhálózati IP-címekről szeretne beérkező adatokat közzétenni, használja a jegyzeteket [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) a bejövő forgalomban.

### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

A magánhálózati IP-címek nélküli Application Gateway-Ingresses `appgw.ingress.kubernetes.io/use-private-ip: "true"` figyelmen kívül lesz hagyva. Ezt a rendszer a bejövő események és a AGIC Pod naplójában fogja jelezni.

* Hiba a bejövő eseményben jelzett módon

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Hiba a AGIC-naplókban jelzett módon

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Globális hozzárendelés
Abban az esetben, ha a szükséges, hogy az összes Ingresses legyen elérhető a privát IP-címeken, használja `appgw.usePrivateIP: true` a parancsot a `helm` konfigurációban.

### <a name="usage"></a>Használat
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Így a bejövő vezérlő szűri az IP-címek konfigurációit egy magánhálózati IP-címen, amikor a Application Gatewayon konfigurálja a előtér-figyelőket.
A AGIC pánikba kerül és összeomlik, ha `usePrivateIP: true` nincs hozzárendelve magánhálózati IP-cím.

> [!NOTE]
> Application Gateway v2 SKU-nak nyilvános IP-címet kell megadnia. Ha a Application Gateway magánjellegűnek kell lennie, csatolja a [`Network Security Group`](../virtual-network/network-security-groups-overview.md) -t a Application Gateway alhálózatához a forgalom korlátozása érdekében.