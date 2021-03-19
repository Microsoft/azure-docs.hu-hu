---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875557"
---
A DNS-beállítások nem kötelező részét képezik ennek a konfigurációnak, de a DNS-re akkor van szükség, ha a virtuális gépek között névfeloldást szeretne végezni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni.

Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. Nyissa meg a virtuális hálózat beállításait, válassza a DNS-kiszolgálók lehetőséget, majd adja hozzá a névfeloldáshoz használni kívánt DNS-kiszolgáló IP-címét.

1. Keresse meg a virtuális hálózatot a portálon.
2. A virtuális hálózat lapjának **Beállítások** szakaszában válassza a **DNS-kiszolgálók** elemet.
3. Adjon meg egy DNS-kiszolgálót.
4. A beállítások mentéséhez kattintson a **Mentés** gombra az oldal tetején.