---
title: Kapcsolódás Azure Data Lake Storage Gen1 a virtuális hálózatok | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az olyan Azure-beli virtuális gépek Azure Data Lake Storage Gen1 való hozzáférését, amelyek korlátozott hozzáféréssel rendelkeznek az erőforrásokhoz.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: e319cf9dfc01546607e20572c5bf4930fd974c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104036"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Hozzáférési Azure Data Lake Storage Gen1 egy Azure-VNET belüli virtuális gépekről
Azure Data Lake Storage Gen1 egy nyilvános internetes IP-címeken futó, Minden olyan kiszolgáló, amely csatlakozhat a nyilvános internethez, jellemzően Azure Data Lake Storage Gen1 végpontokhoz is csatlakozhat. Alapértelmezés szerint az Azure virtuális hálózatok-ban található összes virtuális gép hozzáfér az internethez, így a Azure Data Lake Storage Gen1hoz is hozzáférhet. Előfordulhat azonban, hogy a virtuális gépeket egy VNET konfigurálja, hogy ne legyen hozzáférése az internethez. Ilyen virtuális gépek esetén a Azure Data Lake Storage Gen1hoz való hozzáférés is korlátozott. Az Azure virtuális hálózatok-beli virtuális gépek nyilvános internet-hozzáférésének blokkolása az alábbi módszerek bármelyikével végezhető el:

* Hálózati biztonsági csoportok (NSG) konfigurálásával
* A felhasználó által megadott útvonalak konfigurálásával (UDR)
* Az útvonalak BGP-n keresztüli cseréjével (iparági szabványnak megfelelő dinamikus útválasztási protokollal) a ExpressRoute használata esetén blokkolja az internet elérését

Ebből a cikkből megtudhatja, hogyan engedélyezheti az Azure-beli virtuális gépekről Azure Data Lake Storage Gen1 való hozzáférést, amelyek a korábban felsorolt három módszer egyikével vannak korlátozva az erőforrásokhoz való hozzáférésre.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>A korlátozott kapcsolattal rendelkező virtuális gépekről Azure Data Lake Storage Gen1 való kapcsolódás engedélyezése
Az ilyen virtuális gépekről való Azure Data Lake Storage Gen1 eléréséhez konfigurálnia kell azokat a régió IP-címének eléréséhez, ahol a Azure Data Lake Storage Gen1 fiók elérhető. Az Data Lake Storage Gen1-fiók régiói IP-címeit a fiókok () DNS-neveinek feloldásával azonosíthatja `<account>.azuredatalakestore.net` . A fiókok DNS-neveinek feloldásához olyan eszközöket is használhat, mint az **nslookup**. Nyisson meg egy parancssort a számítógépen, és futtassa a következő parancsot:

```console
nslookup mydatastore.azuredatalakestore.net
```

A kimenet az alábbihoz hasonló. A **cím** tulajdonság értéke a Data Lake Storage Gen1 fiókjához tartozó IP-cím.

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>A NSG használatával korlátozott virtuális gépekről való kapcsolódás engedélyezése
Ha egy NSG-szabály az Internet elérésének blokkolására szolgál, létrehozhat egy másik NSG, amely hozzáférést biztosít a Data Lake Storage Gen1 IP-címhez. További információ a NSG-szabályokról: [hálózati biztonsági csoportok áttekintése](../virtual-network/network-security-groups-overview.md). A NSG létrehozásával kapcsolatos útmutatásért tekintse meg [a hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)című témakört.

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>A UDR vagy a ExpressRoute által korlátozva lévő virtuális gépek kapcsolatának engedélyezése
Ha az útvonalak UDR vagy BGP-Exchange-útvonalakat használnak az internethez való hozzáférés blokkolására, akkor egy speciális útvonalat kell konfigurálni, hogy az ilyen alhálózatokban lévő virtuális gépek hozzáférhessenek Data Lake Storage Gen1 végpontokhoz. További információ: [felhasználó által megadott útvonalak áttekintése](../virtual-network/virtual-networks-udr-overview.md). A UDR létrehozásával kapcsolatos utasításokért lásd: [UDR létrehozása a Resource Managerben](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>A ExpressRoute használatával korlátozott virtuális gépekről való kapcsolódás engedélyezése
Ha egy ExpressRoute-áramkör konfigurálva van, a helyszíni kiszolgálók hozzáférhetnek Data Lake Storage Gen1 a nyilvános közvetítéssel. A nyilvános ExpressRoute konfigurálásával kapcsolatos további részletekért lásd: [ExpressRoute GYIK](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Lásd még
* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme](data-lake-store-security-overview.md)