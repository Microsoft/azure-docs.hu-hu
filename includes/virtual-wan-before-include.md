---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359500"
---
* Azure-előfizetéssel rendelkezik. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid útmutató [című cikket.](../articles/virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezhet meglévő virtuális hálózati átjárókkal. Ha a virtuális hálózatnak már van átjárója (VPN vagy ExpressRoute), a továbblépés előtt el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok csak a virtuális WAN hub-átjáróhoz kapcsolódjanak.

* A virtuális központ egy virtuális WAN által létrehozott és használt virtuális hálózat. A virtuális WAN-hálózat lényege egy régióban. Szerezze be a virtuális központ régiójához tartozó IP-címtartományt. A hub számára megadott címtartomány nem fedi át a meglévő virtuális hálózatok bármelyikét, amelyhez csatlakozik. Emellett nem fedi átfedésben azokat a helyszíni címtartományt, amelyekhez csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.
