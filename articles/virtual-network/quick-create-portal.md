---
title: 'Gyors útmutató: virtuális hálózat létrehozása – Azure Portal'
titleSuffix: Azure Virtual Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal használatával.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606068"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal használatával. Két virtuális gépet (VM) telepít. Ezután biztonságosan kommunikál a virtuális gépek között, és csatlakozhat a virtuális gépekhez az internetről. A virtuális hálózat az Azure-beli magánhálózat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek számára, hogy biztonságosan kommunikáljanak egymással és az internettel.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. A keresőmezőbe írja be a **Virtual Network** kifejezést. A keresési eredmények között válassza a **Virtual Network** lehetőséget.

3. A **Virtual Network** lapon válassza a **Létrehozás** lehetőséget.

4. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza az **Új létrehozása** lehetőséget.  </br> Adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |   |
    | Name | Adja meg a **myVNet** értéket. |
    | Region | Válassza ki az USA **keleti** régióját. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Virtuális hálózati Azure Portal létrehozása" border="true":::

5. Válassza az **IP-címek** fület, vagy válassza a **következő: IP-címek** gombot az oldal alján.

6. Az **IPv4-címtartomány** területen válassza ki a meglévő címtartományt, és módosítsa a **10.1.0.0/16** értékre.

7. Válassza az **+ alhálózat hozzáadása** lehetőséget, majd adja meg a **MySubnet** az alhálózat **neve** és a **10.1.0.0/24** **alhálózati címtartomány** mezőben.

8. Válassza a **Hozzáadás** lehetőséget.

9. Kattintson a **Biztonság** fülre, vagy válassza a **következő: biztonság** gombot az oldal alján.

10. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |

11. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

12. Válassza a **Létrehozás** lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM2** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | **Alapszintű** kiválasztása|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="connect-to-myvm1"></a>Kapcsolódás a myVM1

1. A saját virtuális gép kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki a saját virtuális gép **myVM1** nevét.

3. A virtuális gép menüsorában válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Kapcsolódás a myVM1 az Azure Bastion használatával" border="true":::

4. A **kapcsolat** lapon válassza a kék **használat Bastion** gombot.

5. A **megerősített** lapon adja meg a virtuális géphez korábban létrehozott felhasználónevet és jelszót.

6. Válassza a **Kapcsolódás** lehetőséget.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A **myVM1** megerősített kapcsolataiban nyissa meg a PowerShellt.

2. Írja be a következő szöveget: `ping myvm2`.

    Ehhez a kimenethez hasonló üzenet jelenik meg:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. A **myVM1** felé irányuló megerősített kapcsolódás lezárása.

4. Végezze el a [Kapcsolódás a myVM1](#connect-to-myvm1)-hez, de a **myVM2**-hez való kapcsolódás című témakör lépéseit.

5. Nyissa meg a PowerShellt a **myVM2** `ping myvm1` .

    Ehhez hasonló üzenet jelenik meg:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. A **myVM2** felé irányuló megerősített kapcsolódás lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. 

Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a két virtuális gép között.

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Keresse meg és válassza ki a **myResourceGroup**.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a **myResourceGroup** **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a virtuális hálózati beállításokról: [virtuális hálózat létrehozása, módosítása vagy törlése](manage-virtual-network.md).

További információ a virtuálisgép-hálózati kommunikáció típusairól: [hálózati forgalom szűrése](tutorial-filter-network-traffic.md).
