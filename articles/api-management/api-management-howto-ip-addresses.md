---
title: Az Azure API Management szolgáltatáscímeinek IP-| Microsoft Docs
description: Megtudhatja, hogyan olvashatja be egy Azure API Management szolgáltatás IP-címeit, és mikor változnak.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 5939292b6e810634723fada17521bb227764b989
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534041"
---
# <a name="ip-addresses-of-azure-api-management"></a>Az Azure-beli virtuális API Management

Ez a cikk bemutatja, hogyan lehet lekérni az Azure API Management IP-címét. Az IP-címek nyilvánosak vagy privátak is, ha a szolgáltatás egy virtuális hálózatban van.

Az IP-címek használatával tűzfalszabályokat hozhat létre, szűrheti a háttérszolgáltatások bejövő forgalmát, vagy korlátozhatja a kimenő forgalmat.

## <a name="ip-addresses-of-api-management-service"></a>A API Management IP-címei

A API Management, Alapszintű, Standard vagy Prémium szinten minden egyes szolgáltatáspéldány nyilvános IP-címmel rendelkezik, amelyek kizárólag erre a szolgáltatáspéldányra vannak kiosztva (nem osztják meg őket más erőforrásokkal). 

Az IP-címeket az erőforrás áttekintő irányítópultjáról is lekérheti a Azure Portal.

![API Management IP-cím](media/api-management-howto-ip-addresses/public-ip.png)

Ezeket programozott módon is lekérheti a következő API-hívással:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

A válasz a nyilvános IP-címek része lesz:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

A [többréigében üzemelő példányok](api-management-howto-deploy-multi-region.md)esetén minden regionális üzemelő példány egy nyilvános IP-címmel rendelkezik.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>A virtuális API Management IP-címei

Ha a API Management szolgáltatás egy virtuális hálózaton belül található, akkor két IP-címtípusa lesz : nyilvános és privát.

A nyilvános IP-címek a porton belüli kommunikációhoz használatosak – a konfiguráció kezeléséhez (például a porton `3443` keresztül Azure Resource Manager). A külső VNet-konfigurációban ezek a futásidejű API-forgalomhoz is használatosak. Amikor egy kérést küld API Management egy nyilvános (internetes) háttérnek, egy nyilvános IP-cím lesz látható a kérés eredeteként.

A magánhálózati virtuális IP-címek (VIP-címek) csak a belső [VNet](api-management-using-with-internal-vnet.md)módban érhetők el, és a hálózaton belülről csatlakoznak API Management végpontjaihoz – átjárókhoz, a fejlesztői portálhoz és a közvetlen API-hozzáférés felügyeleti síkjára.  Ezeket a hálózaton belüli DNS-rekordok beállításához használhatja.

A két típus címeit a Azure Portal API-hívás válaszában láthatja:

![API Management virtuális hálózat IP-címében található](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management nyilvános IP-címet használ a virtuális hálózaton kívüli kapcsolatokhoz, és egy magánhálózati IP-címet a virtuális hálózaton belüli kapcsolatokhoz.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>A szolgáltatás használatának rétegében API Management IP-címek

Ha a API Management szolgáltatás egy használat alapú szolgáltatás, akkor nem dedikált IP-címmel. A használat alapú szint szolgáltatás egy megosztott infrastruktúrán fut, determinisztikus IP-cím nélkül. 

Forgalomkorlátozási célokra használhatja az Azure-adatközpontok IP-címtartományát. A pontos [lépésekért Azure Functions tekintse meg a részletes](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) dokumentációt.

## <a name="changes-to-the-ip-addresses"></a>Az IP-címek változásai

A szolgáltatás Fejlesztői, Alapszintű, Standard és Prémium API Management a nyilvános IP-címek (VIP) a szolgáltatás teljes élettartama alatt statikusak, a következő kivételekkel:

* A szolgáltatás törlődik, majd újra létrejön.
* A szolgáltatás-előfizetés [](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) fel van [függesztve](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) vagy figyelmeztetést kapott (például fizetés nélküli fizetés esetén), majd visszaállítja.
* Az Azure Virtual Network hozzá lesz adva a szolgáltatáshoz, vagy el lesz távolítva a szolgáltatásból.
* API Management szolgáltatás a külső és a belső virtuális hálózat telepítési módja között vált.

[Többrépontos üzemelő példányok](api-management-howto-deploy-multi-region.md)esetén a regionális IP-cím megváltozik, ha egy régiót leküld, majd visszaállít. A regionális IP-cím is megváltozik, ha engedélyezi, hozzáadja vagy eltávolítja a [rendelkezésre állási zónákat.](zone-redundancy.md)
