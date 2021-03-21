---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026797"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Helyi hálózati átjáró „gatewayIpAddress” értékének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Az átjáró IP-címe megváltoztatható a létező VPN átjárókapcsolat eltávolítása nélkül (ha van ilyen). Az átjáró IP-címének módosításához cserélje ki a „Site2” és a „TestRG1” értékeket a sajátjaira az [az network local-gateway update](/cli/azure/network/local-gateway) parancs használatakor.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Ellenőrizze, hogy az IP-cím megfelelő-e a kimenetben:

```
"gatewayIpAddress": "23.99.222.170",
```