---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028110"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

Ha nincs átjárókapcsolata, és szeretne hozzáadni vagy eltávolítani IP-címelőtagokat, használja ugyanazt a parancsot, amellyel létrehozza a helyi hálózati átjárót: [az network local-gateway create](/cli/azure/network/local-gateway). Ezzel a paranccsal emellett a VPN-eszközhöz tartozó átjárói IP-címet is frissítheti. Az aktuális beállítások felülírásakor a helyi hálózati átjáró meglévő nevét használja. Ha más nevet használ, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

Minden módosításkor az előtagok teljes listáját meg kell adnia, nem csak azokat, amelyeket módosítani kíván. Csak a megtartandó előtagokat adja meg. Ebben az esetben a 10.0.0.0/24 és a 20.0.0.0/24 előtagokat.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha van átjárókapcsolata és szeretne hozzáadni vagy eltávolítani IP-címelőtagokat, frissítheti az előtagokat az [az network local-gateway update](/cli/azure/network/local-gateway) parancs használatával. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót.

Minden módosításkor az előtagok teljes listáját meg kell adnia, nem csak azokat, amelyeket módosítani kíván. Ebben a példában a 10.0.0.0/24 és a 20.0.0.0/24 már léteznek, A 30.0.0.0/24 és a 40.0.0.0/24 előtagokat most adjuk hozzá, és frissítéskor megadjuk mind a 4 előtagot.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```