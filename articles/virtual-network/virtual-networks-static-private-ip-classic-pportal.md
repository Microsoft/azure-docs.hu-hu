---
title: Virtuális gépek magánhálózati IP-címeinek konfigurálása (klasszikus) – Azure Portal | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek magánhálózati IP-címeit a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222904"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Magánhálózati IP-címek konfigurálása virtuális géphez (klasszikus) a Azure Portal használatával

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. [A statikus magánhálózati IP-címeket a Resource Manager-alapú üzemi modellben is kezelheti](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi példa azt ismerteti, hogy egy egyszerű környezet már létre van hozva. Ha a jelen dokumentumban látható lépéseket szeretné futtatni, először létre kell [hoznia a vnet létrehozása](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)című témakörben leírt tesztkörnyezetben.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása virtuális gép létrehozásakor
A következő lépésekkel hozhat létre egy *DNS01* nevű virtuális gépet egy *TestVNet* nevű VNet előtér- *alhálózatában* a *192.168.1.101* statikus magánhálózati IP-címével:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza az **új**  >  **számítás**  >  **Windows Server 2012 R2 Datacenter** lehetőséget, és figyelje meg, hogy a **telepítési modell kiválasztása** listában már látható a **klasszikus**, majd válassza a **Létrehozás** lehetőséget.
   
    ![Képernyőfelvétel: a Azure Portal az új > számítási > a Windows Server 2012 R2 Datacenter csempe ki van emelve.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. A **virtuális gép létrehozása** területen adja meg a létrehozandó virtuális gép nevét (a forgatókönyv *DNS01* ), a helyi rendszergazdai fiókot és a jelszót.
   
    ![Képernyőkép: a virtuális gép nevének beírásával, a helyi rendszergazda felhasználónévvel és a jelszó megadásával megtudhatja, hogyan hozhat létre virtuális gépet.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Válassza a **választható konfigurációs**  >  **hálózati**  >  **Virtual Network**, majd a **TestVNet** lehetőséget. Ha a **TestVNet** nem érhető el, győződjön meg arról, hogy az *USA középső* régióját használja, és létrehozta a cikk elején leírt tesztkörnyezetben.
   
    ![Képernyőfelvétel: a választható konfiguráció > hálózati > Virtual Network > TestVNet lehetőség kiemelve.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. A **hálózat** területen győződjön meg arról, hogy az aktuálisan kiválasztott alhálózat *előtér, majd* válassza az **IP-címek** lehetőséget, majd az **IP-cím hozzárendelése** területen válassza a **statikus** lehetőséget, majd adja meg a *192.168.1.101* az **IP-címhez** az alább látható módon.
   
    ![Képernyőfelvétel: az IP-címek mezőben adja meg a statikus IP-címet.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Válassza **az OK** lehetőséget az **IP-címek** területen, majd kattintson **az OK** elemre a **hálózat** területen, majd válassza **az OK** lehetőséget a **választható konfiguráció** területen.
7. A **virtuális gép létrehozása** területen válassza a **Létrehozás** lehetőséget. Figyelje meg az irányítópulton az alábbi csempét:
   
    ![Képernyőkép, amely a Windows Server 2012 R2 Datacenter csempe létrehozását mutatja be.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Virtuális gép statikus magánhálózati IP-címére vonatkozó információk lekérése
A fenti lépésekkel létrehozott virtuális gép statikus magánhálózati IP-címére vonatkozó információk megtekintéséhez hajtsa végre az alábbi lépéseket.

1. A Azure Portal válassza a **Tallózás minden**  >  **virtuális gép (klasszikus)**  >  **DNS01**  >  **minden beállítás**  >  **IP-** címe lehetőséget, és figyelje meg az IP-cím hozzárendelését és az IP-címet az alább látható módon.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Statikus magánhálózati IP-cím eltávolítása egy virtuális gépről

Az **IP-címek** területen válassza a **dinamikus** lehetőséget az **IP-cím hozzárendeléstől** jobbra, válassza a **Mentés** lehetőséget, majd válassza az **Igen** lehetőséget, ahogy az alábbi képen látható:
   
![Képernyőfelvétel: a statikus magánhálózati IP-cím virtuális gépről való eltávolításához válassza az IP-cím-hozzárendelési címke jobb oldalán található dinamikus lehetőséget.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus magánhálózati IP-cím hozzáadása meglévő virtuális géphez

1. A korábban megjelenített **IP-címek** területen válassza a **statikus** lehetőséget az **IP-cím-hozzárendeléstől** jobbra.
2. Írja be az **IP-cím** *192.168.1.101* , válassza a **Mentés** lehetőséget, majd válassza az **Igen** lehetőséget.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

Azt javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure-beli virtuális géphez rendelt magánhálózati IP-címet, ha szükséges. Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure-beli virtuális géphez rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. Soha ne rendeljen hozzá manuálisan egy Azure-beli virtuális géphez hozzárendelt nyilvános IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>Következő lépések
* További információ a [fenntartott nyilvános IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) címekről.
* További információ a [példány szintű nyilvános IP-címekről (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Forduljon a [fenntartott IP REST API](/previous-versions/azure/reference/dn722420(v=azure.100))-khoz.