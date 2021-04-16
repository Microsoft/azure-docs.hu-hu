---
title: 'Rövid útmutató: Virtuális hálózat létrehozása – Azure Portal'
titleSuffix: Azure Virtual Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531182"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal. Két virtuális gépet helyez üzembe. Ezután biztonságosan kommunikál a virtuális gépek között, és csatlakozik a virtuális gépekhez az internetről. A virtuális hálózat az Azure-beli magánhálózat alapvető építőeleme. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek biztonságosan kommunikáljanak egymással és az internettel.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza **az Erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. A keresőmezőbe írja be a **következőt: Virtual Network.** Válassza **Virtual Network** lehetőséget a keresési eredmények között.

3. A **Virtual Network** válassza a **Létrehozás lehetőséget.**

4. A **Virtuális hálózat létrehozása lapon** adja meg vagy válassza ki ezt az információt az Alapvető **beállítások** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza az **Új létrehozása** lehetőséget.  </br> Adja meg **a myResourceGroup erőforráscsoportot.** </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |   |
    | Name | Adja meg a **myVNet** értéket. |
    | Region | Válassza **az (USA) USA keleti régiója lehetőséget.** |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Virtuális hálózati Azure Portal" border="true":::

5. Válassza az **IP-címek lapot,** vagy válassza a **Tovább: IP-címek** gombot a lap alján.

6. Az **IPv4-címtérben** válassza ki a meglévő címterületet, és módosítsa **a következőre: 10.1.0.0/16.**

7. Válassza **az + Alhálózat hozzáadása** lehetőséget,  majd az Alhálózat neve mezőben adja meg a **MySubnet,** alhálózati címtartományként pedig a **10.1.0.0/24** **címet.**

8. Válassza a **Hozzáadás** lehetőséget.

9. Válassza a **Biztonság** lapot, vagy kattintson a Lap alján található **Tovább:** Biztonság gombra.

10. A **BastionHost alatt** válassza az **Engedélyezés lehetőséget.** Adja meg a következő információkat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bastion neve | Írja be **a myBastionHost adhatja meg a myBastionHost adhatja meg** |
    | AzureBastionSubnet címtér | Írja **be a 10.1.1.0/24** |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **Név alatt** adja meg a **myBastionIP nevet.** </br> Válassza az **OK** lehetőséget. |

11. Válassza az **Áttekintés + létrehozás lapot,** vagy válassza az **Áttekintés + létrehozás** gombot.

12. Válassza a **Létrehozás** lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. A portál bal felső részén válassza az **Erőforrás létrehozása Számítási** virtuális gép  >    >  **lehetőséget.** 
   
2. A **Virtuális gép létrehozása lapon** írja be vagy válassza ki az Alapvető beállítások lapon **található** értékeket:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | Válassza **a myResourceGroup lehetőséget** |
    | **Példány adatai** |  |
    | Virtuális gép neve | Adja meg **a myVM1-et** |
    | Region | Válassza **az (USA) USA keleti régiója lehetőséget** |
    | Rendelkezésre állási lehetőségek | Válassza a **Nincs szükség infrastruktúra-redundanciát lehetőséget** |
    | Kép | A **Windows Server 2019 Datacenter kiválasztása** |
    | Azure Spot-példány | Válassza a **Nem lehetőséget** |
    | Méret | Válassza ki a virtuális gép méretét, vagy használja az alapértelmezett beállítást |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Jelszó megadása |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövőport-szabályok** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **Hálózat lapfület,** vagy válassza a **Tovább: Lemezek,** majd a **Tovább: Hálózatkezelés lehetőséget.**
  
4. A Hálózat lapon válassza ki vagy írja be a következőt:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza **a myVNet lehetőséget.** |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **Nincs lehetőséget** |
    | Hálózati adapter hálózati biztonsági csoportja | Válassza az **Alapszintű lehetőséget**|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson az oldal alján található kék Felülvizsgálat **+** létrehozás gombra.
  
6. Tekintse át a beállításokat, majd válassza a **Létrehozás lehetőséget.**

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

1. A portál bal felső részén válassza az **Erőforrás létrehozása Számítási** virtuális gép  >    >  **lehetőséget.** 
   
2. A **Virtuális gép létrehozása lapon** írja be vagy válassza ki az Alapvető beállítások lapon **található** értékeket:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | Válassza **a myResourceGroup lehetőséget** |
    | **Példány adatai** |  |
    | Virtuális gép neve | Adja meg **a myVM2-t** |
    | Region | Válassza **az (USA) USA keleti régiója lehetőséget** |
    | Rendelkezésre állási lehetőségek | Válassza a **Nincs szükség infrastruktúra-redundanciát lehetőséget** |
    | Kép | A **Windows Server 2019 Datacenter kiválasztása** |
    | Azure Spot-példány | Válassza a **Nem lehetőséget** |
    | Méret | Válassza ki a virtuális gép méretét, vagy használja az alapértelmezett beállítást |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Jelszó megadása |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövőport-szabályok** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |

3. Válassza a **Hálózat lapfület,** vagy válassza a **Tovább: Lemezek,** majd a **Tovább: Hálózatkezelés lehetőséget.**
  
4. A Hálózat lapon válassza ki vagy írja be a következőt:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | Válassza **a myVNet lehetőséget.** |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **Nincs lehetőséget** |
    | Hálózati adapter hálózati biztonsági csoportja | Válassza az **Alapszintű lehetőséget**|
    | Nyilvános bejövő portok hálózata | Válassza a **Nincs** lehetőséget. |
   
5. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson az oldal alján található kék Felülvizsgálat **+** létrehozás gombra.
  
6. Tekintse át a beállításokat, majd válassza a **Létrehozás lehetőséget.**

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Csatlakozás a myVM1-hez

1. A virtuális gép [Azure Portal](https://portal.azure.com) a virtuális gép kezeléséhez. Keresse meg és válassza a **Virtuális gépek lehetőséget.**

2. Válassza ki a **myVM1** nevű privát virtuális gépet.

3. A virtuális gép menüsávjában válassza **a** Csatlakozás lehetőséget, majd a **Bastion lehetőséget.**

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Csatlakozás a myVM1-hez Azure Bastion" border="true":::

4. A Csatlakozás **lapon** válassza a Kék **Megerősített használat gombot.**

5. A **Bastion lapon** adja meg a virtuális géphez korábban létrehozott felhasználónevet és jelszót.

6. Válassza a **Kapcsolódás** lehetőséget.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A **myVM1** megerősített kapcsolatán nyissa meg a PowerShellt.

2. Írja be a következő szöveget: `ping myvm2`.

    A következő kimenethez hasonló üzenet jelenik meg:

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

3. Zárja be a **myVM1 megerősített kapcsolatát.**

4. Kövesse a Csatlakozás a [myVM1-hez lépéseit,](#connect-to-myvm1)de csatlakozzon a **myVM2-hez.**

5. Nyissa meg a PowerShellt **a myVM2-n,** és írja be a `ping myvm1` parancsot.

    A következő üzenethez hasonló üzenetet fog kapni:

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

7. Zárja be a **myVM2-hez való megerősített kapcsolatot.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. 

Csatlakozott egy virtuális géphez az internetről, és biztonságosan kommunikált a két virtuális gép között.

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és az összes benne található erőforrást:

1. Keresse meg és válassza ki a **myResourceGroup et.**

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be **a myResourceGroup** nevet **az ERŐFORRÁSCSOPORT NEVE** mezőbe, majd válassza a Törlés **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

További információ a virtuális hálózati beállításokról: Virtuális hálózat [létrehozása, módosítása](manage-virtual-network.md)vagy törlése.

A virtuális gépek hálózati kommunikációtípusokkal kapcsolatos további információért lásd: [Hálózati forgalom szűrése.](tutorial-filter-network-traffic.md)
