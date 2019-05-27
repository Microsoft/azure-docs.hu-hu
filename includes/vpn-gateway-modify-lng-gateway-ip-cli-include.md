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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121045"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Helyi hálózati átjáró „gatewayIpAddress” értékének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Az átjáró IP-címe megváltoztatható a létező VPN átjárókapcsolat eltávolítása nélkül (ha van ilyen). Az átjáró IP-címének módosításához cserélje ki a „Site2” és a „TestRG1” értékeket a sajátjaira az [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway) parancs használatakor.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Ellenőrizze, hogy az IP-cím megfelelő-e a kimenetben:

```
"gatewayIpAddress": "23.99.222.170",
```
