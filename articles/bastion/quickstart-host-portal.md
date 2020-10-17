---
title: 'Gyors útmutató: az Azure Bastion konfigurálása és a virtuális géphez való kapcsolódás magánhálózati IP-cím és böngésző használatával'
titleSuffix: Azure Bastion
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure-alapú megerősített gazdagépet egy virtuális gépről, és hogyan csatlakozhat a virtuális GÉPHEZ a böngészőben a privát IP-cím használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150485"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Gyors útmutató: biztonságos virtuális gép csatlakoztatása a böngészőben egy privát IP-cím használatával

A virtuális géphez (VM) a Azure Portal és az Azure Bastion használatával csatlakozhat a böngészőjében. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja az Azure-t a virtuális gép beállításai alapján, majd a portálon keresztül csatlakozhat a virtuális géphez. A virtuális gépnek nincs szüksége nyilvános IP-címekre, ügyfélszoftverre, ügynökre vagy speciális konfigurációra. A szolgáltatás üzembe helyezését követően az RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. További információ az Azure Bastion-ről: [Mi az az Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ahhoz, hogy a megerősített használatával csatlakozhasson a virtuális géphez a böngészőn keresztül, be kell tudnia jelentkezni a Azure Portalba.

* Egy virtuális hálózatban található Windows rendszerű virtuális gép. Ha nem rendelkezik virtuális géppel, hozzon létre egyet a gyors üzembe helyezési útmutatóval [: hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

  * Ha például értékekre van szüksége, tekintse meg a megadott [példákat](#values).
  * Ha már rendelkezik virtuális hálózattal, a virtuális gép létrehozásakor ügyeljen rá, hogy a hálózatkezelés lapon válassza ki azt.
  * Ha még nem rendelkezik virtuális hálózattal, létrehozhat egyet egy időben, amikor létrehozza a virtuális GÉPET.
  * Ehhez a virtuális géphez nem kell nyilvános IP-címet adni ahhoz, hogy csatlakozni lehessen az Azure Bastion-n keresztül.

* Szükséges virtuálisgép-szerepkörök:
  * Olvasó szerepkör a virtuális gépen.
  * Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.
  
* Szükséges VM-portok:
  * Bejövő portok: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Példaértékek

A konfiguráció létrehozásakor a következő példában szereplő értékeket használhatja, vagy helyettesíthet sajátját is.

**Alapszintű VNet és virtuálisgép-értékek:**

|**Név** | **Érték** |
| --- | --- |
| Virtuális gép| TestVM |
| Erőforráscsoport | TestRG |
| Régió | USA keleti régiója |
| Virtuális hálózat | TestVNet1 |
| Címtér | 10.0.0.0/16 |
| Alhálózatok | Előtér: 10.0.0.0/24 |

**Azure-megerősített értékek:**

|**Név** | **Érték** |
| --- | --- |
| Név | TestVNet1-Bastion |
| + Alhálózat neve | AzureBastionSubnet |
| AzureBastionSubnet-címek | Egy alhálózaton belüli VNet-címtartomány egy/27 alhálózati maszkkal. Például: 10.0.1.0/27.  |
| Nyilvános IP-cím |  Új létrehozása |
| Nyilvános IP-cím | VNet1BastionPIP  |
| Nyilvános IP-cím SKU |  Standard  |
| Hozzárendelés  | Statikus |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Megerősített gazdagép létrehozása

A megerősített gazdagépek konfigurálásának néhány különböző módja van. A következő lépésekben a Azure Portal közvetlenül a virtuális gépről hozzon létre egy megerősített gazdagépet. Amikor létrehoz egy gazdagépet egy virtuális gépről, a különböző beállítások automatikusan kitöltik a virtuális gép és/vagy a virtuális hálózat megfelelő beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon arra a virtuális gépre, amelyhez csatlakozni szeretne, majd válassza a **Kapcsolódás**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="virtuális gép beállításai" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. A legördülő listából válassza a **Bastion**lehetőséget.
1. A **TestVM | A kapcsolat lapon**válassza a **megerősített használata**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="virtuális gép beállításai" border="false":::

1. A **megerősített** lapon töltse ki a következő beállítások mezőket:

   * **Név**: nevezze el a megerősített gazdagépet.
   * **Alhálózat**: Ez a virtuális hálózati címtartomány, amelyre a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. Használjon legalább/27 vagy nagyobb (/27,/26,/25 stb.) alhálózatot.
   * Válassza az **alhálózat konfigurációjának kezelése**lehetőséget.
1. Az **alhálózatok** lapon válassza a **+ alhálózat**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="virtuális gép beállításai":::
    
1. Az **alhálózat hozzáadása** oldalon a **név**mezőbe írja be a következőt: **AzureBastionSubnet**.
   * Az alhálózati címtartomány területen válassza ki a virtuális hálózati címtartomány keretén belül található alhálózati címeket.
   * Ne módosítsa a többi beállítást. Az alhálózat módosításainak elfogadásához és mentéséhez kattintson **az OK gombra** .

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="virtuális gép beállításai":::
1. A böngészőben a Vissza gombra kattintva lépjen vissza a **megerősített** oldalra, és folytassa az értékek megadását.
   * **Nyilvános IP-cím**: ne **hozzon létre újként**.
   * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
   * **Hozzárendelés**: az alapértelmezett érték a statikus. Az Azure Bastion-hez nem használhat dinamikus hozzárendelést.
   * **Erőforráscsoport**: ugyanazt az erőforráscsoportot, mint a virtuális gép.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="virtuális gép beállításai":::
1. Válassza a **Létrehozás** lehetőséget a megerősített gazdagép létrehozásához. Az Azure ellenőrzi a beállításokat, majd létrehozza a gazdagépet. A gazdagép és az erőforrásai 5 percet vesznek igénybe a létrehozás és a telepítés során.

## <a name="connect"></a><a name="connect"></a>Kapcsolódás

Miután a megerősített állapotot telepítette a virtuális hálózatra, a képernyő a kapcsolódás lapra változik.

1. Írja be a virtuális gép felhasználónevét és jelszavát. Ezután válassza a **kapcsolat**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="virtuális gép beállításai":::
1. Az ehhez a virtuális géphez tartozó RDP-kapcsolat közvetlenül a Azure Portal (HTML5-en keresztül) lesz megnyitva a 443-as port és a megerősített szolgáltatás használatával.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="virtuális gép beállításai":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Adja meg az erőforráscsoport nevét a portál tetején található **keresőmezőbe** , és válassza ki a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport **nevét, írja be az erőforráscsoport nevét** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy megerősített gazdagépet hozott létre a virtuális hálózathoz, majd biztonságosan kapcsolódott egy virtuális géphez a Bastion használatával. Ezután folytassa a következő lépéssel, ha egy virtuálisgép-méretezési csoporthoz szeretne csatlakozni.

> [!div class="nextstepaction"]
> [Kapcsolódás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával](bastion-connect-vm-scale-set.md)
