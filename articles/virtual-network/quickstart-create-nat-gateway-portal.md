---
title: 'Oktatóanyag: NAT-átjáró létrehozása – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót a Azure Portal használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 98989a38a0c1a7f57fc8eba70e064e5d6f2cce8f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671538"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Oktatóanyag: NAT-átjáró létrehozása a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

Ha szeretné, hajtsa végre ezeket a lépéseket az [Azure CLI](quickstart-create-nat-gateway-cli.md)-vel, [Azure PowerShellával](quickstart-create-nat-gateway-powershell.md)vagy [ARM-sablon](quickstart-create-nat-gateway-powershell.md) üzembe helyezésével a portál helyett.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Egy virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni az erőforráscsoportot és a virtuális hálózatot.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA 2. keleti régiója      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Virtuális gép létrehozása a NAT-átjáró használatához

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához. Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését. A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **Ubuntu Server 18,04 LTS** lehetőséget, vagy keressen rá az **Ubuntu Server 18,04 LTS** kifejezésre a piactéren.

2. A **virtuális gép létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupNAT** lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: Type **myVM**.
   - **Példány részletei**  >  Régió > válassza az **USA 2. keleti** **régióját** .
   - **Rendszergazdai fiók**  >  **Hitelesítés típusa**: válassza a **jelszó** lehetőséget.
   - **Rendszergazdai fiók** > adja meg a **felhasználónevet**, a **jelszót** és a **Jelszó megerősítése** információt.
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **SSH (22)** lehetőséget.
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.

3. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnet**
   - **Alhálózat**: **mySubnet**
   - **Nyilvános IP-** > válassza az **új létrehozása** lehetőséget.  A **nyilvános IP-cím létrehozása** ablakban írja be **a MyPublicIPVM** **nevet a név** mezőbe, és válassza a **standard** értéket az **SKU** számára.  Kattintson az **OK** gombra.
   - **NIC hálózati biztonsági csoport**: válassza az **alapszintű** lehetőséget.
   - **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget.
   - **Bejövő portok kiválasztása**: az **SSH** megerősítése lehetőség van kiválasztva.

4. A felügyelet **lap** **figyelés** területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva** értékre.

5. Válassza az **Áttekintés + létrehozás** lehetőséget. 

6. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra.

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt. Hozzáadunk egy nyilvános IP-erőforrást, egy nyilvános IP-előtagot és egy NAT-átjáró erőforrást.

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózat**  >  **nyilvános IP-címe** lehetőséget, vagy keressen **nyilvános IP-címet** a piactér keresésében.

2. A **nyilvános IP-cím létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | IP-verzió | Válassza az **IPv4** lehetőséget.
    | Termékváltozat | Válassza a **standard** lehetőséget.
    | Name | Adja meg a **myPublicIP**. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroupNAT** lehetőséget. |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|

3. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás** lehetőséget.

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **nyilvános IP-előtag** lehetőséget, vagy keressen **nyilvános IP-előtagot** a piactér keresésében. 

2. A **nyilvános IP-cím létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: **myResourceGroupNAT** kiválasztása>
   - **Példány részletei**  >  **Név**: írja be a **myPublicIPprefix** nevet.
   - **Példány részletei**  >  Régió: válassza az **USA 2. keleti** **régiója** lehetőséget.
   - **Példány részletei**  >  **Előtag mérete**: Select **/31 (2 cím)**

3. Hagyja változatlanul az alapértelmezett beállításokat, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

4. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.
   

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **NAT-átjáró** lehetőséget, vagy keressen rá a **NAT-átjáróra** a piactér keresésében.

2. A **hálózati címfordítási (NAT-) átjáró létrehozása** területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupNAT** lehetőséget.
   - **Példány részletei**  >  **NAT-átjáró neve**: Type **myNATgateway**.
   - **Példány részletei**  >  Régió: válassza az **USA 2. keleti** **régiója** lehetőséget.
   - **Példány részletei**  >  **Üresjárati időkorlát (perc)**: Type **10**.
   - Válassza a **nyilvános IP-cím** lapot, vagy válassza a **Tovább: nyilvános IP-cím** elemet.

3. A **nyilvános IP-cím** lapon írja be vagy válassza ki a következő értékeket:
   - **Nyilvános IP-címek**: válassza a **myPublicIP** lehetőséget.
   - **Nyilvános IP-előtagok**: válassza a **myPublicIPprefix** lehetőséget.
   - Válassza az **alhálózat** lapot, vagy válassza a **Tovább: alhálózat** elemet.

4. Az **alhálózat** lapon írja be vagy válassza ki a következő értékeket:
   - **Virtual Network**: válassza a **myResourceGroupNAT**  >  **myVnet** elemet.
   - **Alhálózat neve**: válassza a **mySubnet** melletti jelölőnégyzetet.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felderítése

1. A portál bal oldalán válassza az **erőforráscsoportok** lehetőséget.
2. Válassza a **myResourceGroupNAT** lehetőséget.
3. Válassza a **myVM** lehetőséget.
4. Az **Áttekintés** területen másolja a **nyilvános IP-cím** értéket, és illessze be a Jegyzettömbbe, így a virtuális gép eléréséhez használhatja azt.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy hozzáférjen a virtuális géphez.

## <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépre

Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatára. 

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrásainak kimerítése további nyilvános IP-cím-erőforrások vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel foglalkozik.


- Tudnivalók az [Azure Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.
> [!div class="nextstepaction"]

