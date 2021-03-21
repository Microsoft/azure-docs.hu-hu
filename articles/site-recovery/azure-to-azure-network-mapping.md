---
title: Virtuális hálózatok leképezése két régió között Azure Site Recovery
description: Ismerkedjen meg a virtuális hálózatok két Azure-régió közötti leképezésével az Azure-beli virtuális gépek vész-helyreállítási Azure Site Recoveryával.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95811630"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Hálózatleképezés és IP-cím beállítása virtuális hálózatokhoz

Ez a cikk bemutatja, hogyan képezheti le a különböző Azure-régiókban található Azure Virtual Networks (virtuális hálózatok) két példányát, és hogyan állíthatja be az IP-címzést a hálózatok között. A hálózati leképezés a replikálás engedélyezésekor a forrás hálózat alapján alapértelmezett viselkedést biztosít a célként kijelölt hálózat kiválasztásához.

## <a name="prerequisites"></a>Előfeltételek

A hálózatok leképezése előtt [Azure virtuális hálózatok](../virtual-network/virtual-networks-overview.md) kell rendelkeznie a forrás és a cél Azure-régiókban. 

## <a name="set-up-network-mapping-manually-optional"></a>Hálózati megfeleltetés manuális beállítása (nem kötelező)

A hálózatokat a következőképpen kell leképezni:

1. **Site Recovery infrastruktúrában** kattintson a **+ hálózati leképezés** elemre.

    ![ Hálózati leképezés létrehozása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. A **hálózati leképezés hozzáadása** lapon válassza ki a forrás-és célhelyeket. A példánkban a forrás virtuális gép a Kelet-Ázsia régióban fut, és replikálja a Délkelet-ázsiai régióba.

    ![Forrás és cél kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Most hozzon létre egy hálózati leképezést az ellenkező irányba. Példánkban a forrás mostantól Délkelet-Ázsiában lesz, és a cél Kelet-Ázsia lesz.

    ![Hálózati leképezési panel hozzáadása – a cél hálózat forrás-és célhelyének kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Hálózatok leképezése a replikáció engedélyezésekor

Ha nem készített hálózati leképezést az Azure-beli virtuális gépek vész-helyreállításának konfigurálása előtt, megadhat egy célként megadott hálózatot a [replikálás beállításakor és engedélyezésekor](azure-to-azure-how-to-enable-replication.md). Ha ezt teszi, a következők történnek:

- A kiválasztott cél alapján a Site Recovery automatikusan létrehoz hálózati leképezéseket a forrásról a célként megadott régióba, a cél és a forrás régió között.
- Alapértelmezés szerint a Site Recovery egy olyan hálózatot hoz létre a célként megadott régióban, amely megegyezik a forrásoldali hálózattal. Site Recovery hozzáadja a **-ASR** utótagot a forrásoldali hálózat neveként. Testreszabhatja a célként megadott hálózatot.
- Ha a hálózati leképezés már bekövetkezett egy forrásoldali hálózat esetében, a leképezett célként megadott hálózat mindig az alapértelmezett lesz, amikor több virtuális gép replikálását engedélyezi. A legördülő listából kiválaszthatja a cél virtuális hálózat módosítását is. 
- Az új replikálások alapértelmezett célként szolgáló virtuális hálózatának módosításához módosítania kell a meglévő hálózati leképezést.
- Ha módosítani szeretné egy hálózati leképezést az A régióból a B régióba, először törölje a B régióból az A régióhoz tartozó hálózati leképezést. Ha sztornírozta a leképezést, módosítsa az A régióból a B régióhoz tartozó hálózati leképezést, majd hozza létre a megfelelő fordított leképezést.

>[!NOTE]
>* A hálózati megfeleltetés módosítása csak az új virtuális gépek replikálásának alapértelmezett értékeit módosítja. Nem érinti a meglévő replikálások cél virtuális hálózatának beállításait. 
>* Ha módosítani szeretné a célként megadott hálózatot egy meglévő replikációhoz, ugorjon a replikált elem számítási és hálózati beállításaihoz.

## <a name="specify-a-subnet"></a>Alhálózat meghatározása

A célként megadott virtuális gép alhálózata a forrás virtuális gép alhálózatának neve alapján van kiválasztva.

- Ha a célként megadott hálózaton a forrás virtuálisgép-alhálózat nevével azonos nevű alhálózat található, akkor az alhálózat a célként megadott virtuális géphez van beállítva.
- Ha egy azonos nevű alhálózat nem létezik a célként megadott hálózaton, a betűrendben lévő első alhálózat célként megadott alhálózatként van beállítva.
- A célként megadott alhálózatot a virtuális gép **számítási és hálózati** beállításaiban módosíthatja.

    ![Számítási és hálózati számítási Tulajdonságok ablak](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>IP-címzés beállítása cél virtuális gépekhez

A célként megadott virtuális gépen található egyes hálózati adapterek IP-címe a következőképpen van konfigurálva:

- **DHCP**: Ha a forrás virtuális gép hálózati adaptere DHCP-t használ, a célként megadott virtuális gép hálózati adaptere is DHCP használatára van beállítva.
- **Statikus IP-cím**: Ha a forrás virtuális gép hálózati adaptere statikus IP-címzést használ, a cél virtuális gép hálózati adaptere statikus IP-címet is használ.


## <a name="ip-address-assignment-during-failover"></a>IP-címkiosztás feladatátvétel közben

**Forrás és cél alhálózatok** | **Részletek**
--- | ---
Azonos címtartomány | A forrásként szolgáló virtuális gép hálózati adapterének IP-címe a célként megadott virtuálisgép-hálózati adapter IP-címe.<br/><br/> Ha a cím nem érhető el, a következő elérhető IP-cím célként van beállítva.
Eltérő címterület | A célként megadott alhálózat következő elérhető IP-címe a célként megadott virtuálisgép-NIC-címnek van beállítva.



## <a name="ip-address-assignment-during-test-failover"></a>IP-cím hozzárendelése a feladatátvételi teszt során

**Célként megadott hálózat** | **Részletek**
--- | ---
A célként megadott hálózat a feladatátvételi VNet | – A cél IP-cím statikus lesz ugyanazzal az IP-címmel. <br/><br/>  -Ha ugyanaz az IP-cím már hozzá van rendelve, akkor az alhálózat tartományának végén az IP-cím lesz a következő. Például: Ha a forrás IP-címe 10.0.0.19, és a feladatátvételi hálózat a 10.0.0.0/24 tartományt használja, akkor a célként megadott virtuális géphez hozzárendelt következő IP-cím 10.0.0.254.
A célként megadott hálózat nem a feladatátvételi VNet | – A cél IP-cím statikus lesz ugyanazzal az IP-címmel.<br/><br/>  -Ha ugyanaz az IP-cím már hozzá van rendelve, akkor az alhálózat tartományának végén az IP-cím lesz a következő.<br/><br/> Például: Ha a forrás statikus IP-címe 10.0.0.19, és a feladatátvétel olyan hálózaton található, amely nem a feladatátvételi hálózat, a 10.0.0.0/24 tartományon belül, akkor a cél statikus IP-címe 10.0.0.19, ha elérhető, és egyéb esetben 10.0.0.254 lesz.

- A feladatátvételi VNet a katasztrófa-helyreállítás beállításakor kiválasztott célként megadott hálózat.
- Javasoljuk, hogy mindig használjon nem éles hálózatot a feladatátvételi teszthez.
- A cél IP-címet a virtuális gép **számítási és hálózati** beállításaiban módosíthatja.


## <a name="next-steps"></a>Következő lépések

- Tekintse át az Azure virtuális gép vész-helyreállítási [hálózati útmutatóját](./azure-to-azure-about-networking.md) .
- [További](site-recovery-retain-ip-azure-vm-failover.md) információ az IP-címek megtartásáról a feladatátvétel után.
