---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179041"
---
## <a name="scenario"></a>Eset
Egyetlen hálózati adapterrel rendelkező virtuális gép jön létre és csatlakozik egy virtuális hálózathoz. A virtuális gépnek három különböző *magánhálózati* IP-címet és két *nyilvános* IP-címet kell megadnia. Az IP-címek a következő IP-konfigurációkhoz vannak rendelve:

* **Ipconfig-1:** Egy *statikus* magánhálózati IP-címet és egy *statikus* nyilvános IP-címet rendel hozzá.
* **Ipconfig-2:** Egy *statikus* magánhálózati IP-címet és egy *statikus* nyilvános IP-címet rendel hozzá.
* **Ipconfig-3:** *Statikus* magánhálózati IP-címet rendel hozzá, és nincs nyilvános IP-cím.
  
    ![Több IP-cím](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

A hálózati adapter létrehozásakor az IP-konfigurációk a hálózati adapterhez vannak társítva, és a virtuális gép létrehozásakor a hálózati adapter csatlakoztatva van a virtuális géphez. A forgatókönyvhöz használt IP-címek típusai illusztrációk. Bármilyen, Ön által igényelt IP-címet és hozzárendelési típust hozzárendelhet.

> [!NOTE]
> Bár a cikkben ismertetett lépések az összes IP-konfigurációt egyetlen hálózati adapterhez rendelik, több IP-konfigurációt is hozzárendelhet egy több hálózati adapterrel rendelkező virtuális gép bármely hálózati adapteréhez. Ha többet szeretne megtudni arról, hogyan hozhat létre több hálózati adapterrel rendelkező virtuális gépet, olvassa el a [virtuális gép létrehozása több hálózati adapterrel](../articles/virtual-machines/windows/multiple-nics.md) című cikket.