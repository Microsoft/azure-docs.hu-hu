---
title: Útmutató a kimenő nyilvános IP-címek azonosításához az Azure Spring Cloud-ban
description: A statikus kimenő nyilvános IP-címek megtekintése a külső erőforrásokkal való kommunikációhoz, például adatbázis, tárterület, Key Vault stb.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94663582"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Kimenő nyilvános IP-címek azonosítása az Azure Spring Cloud-ban

Ez az oldal azt ismerteti, hogyan lehet megtekinteni az Azure Spring Cloud-alkalmazások statikus kimenő nyilvános IP-címeit.  A nyilvános IP-címek a külső erőforrásokkal, például adatbázisokkal, tárolókkal és kulcstartókkal való kommunikációra szolgálnak.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Az IP-címek működése az Azure Spring Cloud-ban

Az Azure Spring Cloud Service egy vagy több kimenő nyilvános IP-címmel rendelkezik. A kimenő nyilvános IP-címek száma a rétegektől és más tényezőktől függően változhat. 

A kimenő nyilvános IP-címek általában állandóak, és változatlanok maradnak, de kivételek vannak.

## <a name="when-outbound-ips-change"></a>A kimenő IP-címek változásakor

Minden egyes Azure Spring Cloud-példányhoz megadott számú kimenő nyilvános IP-cím tartozik. Az alkalmazásokból kimenő kapcsolatok, például egy háttér-adatbázis esetében a kimenő nyilvános IP-címek egyikét használják a forrás IP-címként. Az IP-cím véletlenszerűen van kiválasztva futásidőben, ezért a háttér-szolgáltatásnak meg kell nyitnia a tűzfalat az összes kimenő IP-címhez.

A kimenő nyilvános IP-címek száma a következő műveletek valamelyikének végrehajtásakor változik:

- Frissítse Azure Spring Cloud-példányát a rétegek között.
- Az üzleti igényeknek megfelelő kimenő nyilvános IP-címekhez támogatási jegyet is felvehet.

## <a name="find-outbound-ips"></a>Kimenő IP-címek keresése

Ha a szolgáltatási példány által jelenleg használt kimenő nyilvános IP-címeket szeretné megkeresni a Azure Portalban, kattintson a **hálózat** elemre a példány bal oldali navigációs paneljén. Ezek a **kimenő IP-címek** mezőben vannak felsorolva.

Ugyanezeket az információkat a következő parancs futtatásával is megtalálhatja a Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
* [További információ az Azure-erőforrások felügyelt identitásáról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [További információ a Key vaultról az Azure Spring Cloud-ban](spring-cloud-tutorial-managed-identities-key-vault.md)
