---
title: VLAN-ok/alhálózatok létrehozása – Azure VMware-megoldás CloudSimple
description: Azure VMware Solutions by CloudSimple – ismerteti, hogyan hozhat létre és kezelhet VLAN-okat/alhálózatokat a privát felhők számára, majd alkalmazhatja a tűzfalszabályok alkalmazását.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06bebcb7369f6604fc79c1d3d0a4a6afa8b0a1da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896311"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>VLAN-ok/alhálózatok létrehozása és kezelése saját felhők számára

Nyissa meg a hálózat oldalon a VLAN-ok/alhálózatok lapot, és hozzon létre és kezeljen VLAN-okat/alhálózatokat a privát felhők számára. A VLAN/alhálózat létrehozása után tűzfalszabályok is alkalmazhatók.

## <a name="create-a-vlansubnet"></a>VLAN/alhálózat létrehozása

1. [Nyissa meg a CloudSimple-portált](access-cloudsimple-portal.md) , és válassza a **hálózat** lehetőséget az oldalsó menüben.
2. Válassza a **VLAN-ok/alhálózatok** lehetőséget.
3. Kattintson a **VLAN/alhálózat létrehozása** lehetőségre.

    ![VLAN/alhálózat lap](media/vlan-subnet-page.png)

4. Válassza ki az új VLAN/alhálózat saját Felhőjét.
5. Adja meg a VLAN-azonosítót.
6. Adja meg az alhálózat nevét.
7. Ha engedélyezni szeretné az útválasztást a VLAN-on (alhálózat), akkor az alhálózat CIDR tartományát kell megadnia. Győződjön meg arról, hogy a CIDR-tartomány nem fedi át a helyszíni alhálózatok, az Azure-alhálózatok vagy az átjáró-alhálózatok egyikét sem.
8. Kattintson a **Submit (Küldés**) gombra.

    ![VLAN/alhálózat létrehozása](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Saját felhőben 30 VLAN-ra vonatkozó kvóta van. Ezeket a korlátokat a [támogatási szolgálattal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)növelheti.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Elosztott porttartomány beállítása a vSphere-ben VLAN-információk használatával

Ha elosztott portot szeretne létrehozni a vSphere-ben, kövesse az <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere hálózati útmutatójának</a>"elosztott Port hozzáadása" nevű VMware-témakörében található utasításokat. Az elosztott porttartomány beállításakor adja meg a VLAN-adatokat a CloudSimple-konfigurációból.

![Elosztott port csoport](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Tűzfalszabály kiválasztása

A tűzfalak és a kapcsolódó szabályok a **Network > Firewall Tables** lapon vannak meghatározva. Ha ki szeretné választani a tűzfalat, amelyre alkalmazni kívánja a VLAN-t/alhálózatot a privát felhőhöz, válassza a VLAN/alhálózat kattintson a **tűzfal tábla melléklet** elemre a **VLAN-ok/alhálózatok** lapon. A tűzfalszabályok beállításával és a szabályok definiálásával kapcsolatos utasításokért tekintse meg a [Tűzfalszabályok](firewall.md) című témakört.

![Tűzfalszabály hivatkozása](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Az alhálózatok egy tűzfal táblához társíthatók. A tűzfal tábla több alhálózathoz is társítható.

## <a name="edit-a-vlansubnet"></a>VLAN/alhálózat szerkesztése

Egy VLAN/alhálózat beállításainak szerkesztéséhez válassza ki a **VLAN-ok/alhálózatok** lapon, és kattintson a **Szerkesztés** ikonra. Módosítsa a módosításokat, és kattintson a **Submet** elemre.

## <a name="delete-a-vlansubnet"></a>VLAN/alhálózat törlése

VLAN/alhálózat törléséhez válassza ki a VLAN-ok **/alhálózatok** lapon, és kattintson a **Törlés** ikonra. A megerősítéshez kattintson a **Törlés** gombra.
