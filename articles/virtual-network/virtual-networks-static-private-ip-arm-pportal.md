---
title: Magánhálózati IP-címek konfigurálása virtuális gépekhez – Azure Portal
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek magánhálózati IP-címeit a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015926"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Magánhálózati IP-cím konfigurálása virtuális géphez a Azure Portal használatával

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi példa egy egyszerű környezetet vár a már létrehozott környezetbe. Ha a jelen dokumentumban látható lépéseket szeretné futtatni, először [hozzon létre egy virtuális hálózatot](quick-create-portal.md#create-a-virtual-network). A 3. lépésben azonban ezeket az értékeket használja helyette:

| Beállítás | Érték |
| ------- | ----- |
| Név | *TestVNet* |
| Címtér | *192.168.0.0/16* |
| Erőforráscsoport | **TestRG** (ha szükséges, válassza az **új létrehozása** elemet a létrehozásához) |
| Alhálózat – név | *FrontEnd* |
| Alhálózat – címtartomány | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Virtuális gép létrehozása statikus magánhálózati IP-címek teszteléséhez
Ha a Resource Manager-alapú üzembe helyezési módban hoz létre virtuális gépet, nem állíthat be statikus magánhálózati IP-címet a Azure Portal használatával. Ehelyett először létre kell hoznia a virtuális gépet. Ezt követően beállíthatja, hogy a magánhálózati IP-cím statikus legyen.

Ha egy *DNS01* nevű virtuális gépet szeretne létrehozni egy *TestVNet* nevű virtuális hálózat előtér-alhálózatán, *kövesse az alábbi* lépéseket:

1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforrás létrehozása** lehetőséget.

    ![Erőforrás létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Válassza a **számítási**  >  **virtuális gép** lehetőséget.

    ![Virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Az **alapvető beállítások** területen az alábbi táblázatban leírtak szerint határozza meg az elemek értékeit. Ezután válassza a **tovább &nbsp; : &nbsp; lemezek** , majd a **tovább &nbsp; : &nbsp; hálózatkezelés** lehetőséget.

    | Elem | Érték |
    | --- | --- |
    | **Előfizetés** | Aktuális előfizetés |
    | **Erőforráscsoport** | **TestRG** (kiválasztás a legördülő listából) |
    | **Virtuális gép neve** | *DNS01* |
    | **Régió** | **(US) Az USA keleti régiója** |
    | **Kép** | **Windows Server 2019 Datacenter** |
    | **Méret** | **B1ls** **virtuális gép mérete** , **standard szintű** **ajánlat** |
    | **Felhasználónév** | A rendszergazdai fiók felhasználóneve |
    | **Jelszó** | A rendszergazda fiókja felhasználónevének jelszava |
    | **Jelszó megerősítése** | A jelszó újra |

    ![Alapismeretek lap, virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. A **hálózatkezelés** területen adja meg az elemek értékeit az alábbi táblázatban leírtak szerint, majd válassza a **tovább** lehetőséget.

    | Elem | Érték |
    | --- | --- |
    | **Virtuális hálózat** | **TestVNet** |
    | **Alhálózat** | **FrontEnd** |

    ![Hálózatkezelés lapon hozzon létre egy virtuális gépet, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. A **felügyelet** alatt a **diagnosztika Storage-fiók** területen válassza a **vnetstorage** lehetőséget. Ha a Storage-fiók nem jelenik meg a listában, válassza az **új létrehozása** elemet, adja meg a *vnetstorage* **nevét** , majd kattintson **az OK gombra**. Végül válassza a **&nbsp; + &nbsp; Létrehozás áttekintése** lehetőséget.

    ![Felügyelet lapon hozzon létre egy virtuális gépet, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Az Áttekintés **+ Létrehozás** lapon tekintse át az áttekintő információkat, majd kattintson a **Létrehozás** gombra.

    ![Áttekintés + Létrehozás lap, virtuális gép létrehozása Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A virtuális gép létrehozása után a következő üzenet jelenik meg.

![Üzembe helyezési befejezési üzenet, virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Virtuális gép magánhálózati IP-címére vonatkozó információk lekérése
Az új virtuális gép magánhálózati IP-címére vonatkozó információk megtekintése:

1. A virtuális gép megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

    ![Virtuális gépek, keresőmező, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Válassza ki az új virtuális gép nevét (**DNS01**).

    ![Virtuális gépek listája, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Válassza a **hálózatkezelés** lehetőséget, majd válassza a felsorolt egyetlen hálózati adaptert.

    ![Hálózati adapter, hálózatkezelés, virtuális gép, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Válassza az **IP-konfigurációk** lehetőséget, majd válassza ki a táblázatban szereplő IP-konfigurációt.

    ![IP-konfiguráció, hálózati adapter, hálózatkezelés, virtuális gép, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. A **magánhálózati IP-címek beállításaiban** a **TestVNet/** előtér virtuális hálózat/alhálózat területen jegyezze fel a **hozzárendelés** értékét (**dinamikus** vagy **statikus**) és az **IP-címet**.

    ![Dinamikus vagy statikus hozzárendelés, régi magánhálózati IP-cím beállításai, IP-konfiguráció, hálózati adapter, hálózat, virtuális gép, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus magánhálózati IP-cím hozzáadása meglévő virtuális géphez
Statikus magánhálózati IP-cím hozzáadása az új virtuális géphez:

1. Az IP-konfiguráció lapon adja meg a magánhálózati IP-cím hozzárendelését **statikusra**.
2. Módosítsa a magánhálózati **IP-címet** a *192.168.1.101*, majd válassza a **Mentés** lehetőséget.
   
    ![Dinamikus vagy statikus hozzárendelés, új magánhálózati IP-cím beállításai, IP-konfiguráció, hálózati adapter, hálózatkezelés, virtuális gép, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Ha úgy látja, hogy a **Mentés** lehetőség kiválasztása után a hozzárendelés továbbra is **dinamikus** értékre van állítva, akkor a beírt IP-cím már használatban van. Próbálkozzon egy másik IP-címmel.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Statikus magánhálózati IP-cím eltávolítása egy virtuális gépről
A statikus magánhálózati IP-cím eltávolítása a virtuális gépről:

Az IP-konfiguráció lapon adja meg a magánhálózati IP-cím hozzárendelését **dinamikus** értékre, majd válassza a **Mentés** lehetőséget.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

Egy virtuális gép operációs rendszerén belül nem kell statikusan hozzárendelni az Azure-beli virtuális géphez rendelt *magánhálózati* IP-címet. Csak akkor végezze el a magánhálózati IP-címek statikus hozzárendelését, ha szükség van rá, például [sok IP-cím a virtuális gépekhez való hozzárendelésekor](virtual-network-multiple-ip-addresses-portal.md). Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, ellenőrizze, hogy az megfelel-e az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt magánhálózati IP-címnek. Ellenkező esetben elveszítheti a kapcsolatot a virtuális géppel. További információ a [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#private) beállításairól.

Emellett soha ne rendeljen hozzá manuálisan egy Azure-beli virtuális géphez hozzárendelt *nyilvános* IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>Következő lépések

További információ az [IP-címek beállításainak](virtual-network-network-interface-addresses.md)kezeléséről.
