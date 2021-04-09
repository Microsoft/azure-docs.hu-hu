---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása – Azure Portal
description: Megtudhatja, hogyan hozhat létre statikus nyilvános IP-címmel rendelkező virtuális gépet a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221156"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása a Azure Portal használatával

A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. 

Statikus nyilvános IP-címet rendeljen hozzá, nem pedig dinamikus címet, hogy a cím ne legyen módosítva.   

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** vagy a keresés a **virtuális gépen** elemet a keresőmezőbe.
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőben adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Region | Válassza ki az **USA keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | Válassza a **Windows Server 2019 Datacenter – Gen1** elemet. |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Fogadja el az alapértelmezett hálózatnév nevét. |
    | Alhálózat | Fogadja el az alapértelmezett alhálózati konfigurációt. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **nyilvános IP-cím létrehozása** mezőben adja meg a **myPublicIP** nevet. </br> Az **SKU** esetében válassza a **standard** lehetőséget. </br> **Hozzárendelés**, válassza a **statikus** lehetőséget. </br> Válassza az **OK** lehetőséget.  |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése** lehetőséget. |
    | Válassza ki a bejövő portokat | **RDP kiválasztása (3389)** |

    > [!WARNING]
    > A portál 3389 van kiválasztva, hogy engedélyezze a távoli hozzáférést a Windows Server rendszerű virtuális géphez az internetről. Az 3389-as port az internetre való megnyitása nem ajánlott az éles számítási feladatok kezeléséhez. </br> Az Azure Virtual Machines szolgáltatáshoz való biztonságos hozzáférésért lásd: **[Mi az az Azure Bastion?](../bastion/bastion-overview.md)**
   
5. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a **myResourceGroup** nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a **myResourceGroup** nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Lásd: [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md):

* Nyilvános IP-cím módosítása dinamikusról statikusra.
* Magánhálózati IP-címek használata.

A nyilvános IP-címekhez [névleges díj](https://azure.microsoft.com/pricing/details/ip-addresses)tartozik. Az előfizetések által használható nyilvános IP-címek száma [korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

A virtuális gép nyilvános IP-címéhez tartozó SKU-nak meg kell egyeznie Azure Load Balancer nyilvános IP-címével, amikor hozzáadják a háttér-készlethez. Részletekért lásd: [Azure Load Balancer](../load-balancer/skus.md).

Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

- További információ a [statikus nyilvános IP-címekről](./public-ip-addresses.md#allocation-method).
- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).
- További információk a [magánhálózati IP-címekről](./private-ip-addresses.md) és a [statikus magánhálózati IP-](virtual-network-network-interface-addresses.md#add-ip-addresses) címek egy Azure-beli virtuális géphez való hozzárendeléséről.
