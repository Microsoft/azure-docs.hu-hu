---
title: VMware vCenter-kiszolgálók kezelése a Azure Site Recoveryban
description: Ez a cikk azt ismerteti, hogyan lehet VMware-vCenter hozzáadni és kezelni a VMware virtuális gépek vész-helyreállításához az Azure-ba Azure Site Recovery használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84692519"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server kezelése

Ez a cikk a [Azure Site Recovery](site-recovery-overview.md)VMware vCenter Server felügyeleti műveleteit összegzi.

## <a name="verify-prerequisites-for-vcenter-server"></a>vCenter Server előfeltételeinek ellenőrzése

A VMware virtuális gépek Azure-ba történő vész-helyreállítása során a vCenter-kiszolgálók és virtuális gépek előfeltételei a [támogatási mátrixban](vmware-physical-azure-support-matrix.md#replicated-machines)találhatók.

## <a name="set-up-an-account-for-automatic-discovery"></a>Fiók beállítása az automatikus felderítéshez

Ha a helyszíni VMware virtuális gépekhez állít be vész-helyreállítást, Site Recoverynak hozzá kell férnie a vCenter Server/vSphere-gazdagéphez. A Site Recovery Process Server automatikusan képes felderíteni a virtuális gépeket, és szükség esetén feladatátvételt hajt végre. Alapértelmezés szerint a Process Server a Site Recovery konfigurációs kiszolgálón fut. Vegyen fel egy fiókot a konfigurációs kiszolgálóhoz a vCenter Server/vSphere-gazdagéphez való kapcsolódáshoz a következőképpen:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
1. Nyissa meg a konfigurációs kiszolgáló eszközt (_cspsconfigtool.exe_) az asztali parancsikon használatával.
1. A **fiók kezelése** lapon kattintson a **fiók hozzáadása** lehetőségre.

   ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Adja meg a fiók adatait, majd kattintson **az OK** gombra a hozzáadásához. A fióknak rendelkeznie kell a fiók engedélyei táblában összefoglalt jogosultságokkal.

   > [!NOTE]
   > A fiók adatainak szinkronizálása körülbelül 15 percet vesz igénybe Site Recoveryával.

### <a name="account-permissions"></a>Fiók engedélyei

|**Feladat** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Virtuális gép felderítése/migrálása (feladat-visszavétel nélkül)** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználói fiók. | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).|
|**Replikálás/feladatátvétel** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználói fiók. | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).<br/><br/> Áttelepítési célokra hasznos, de nem teljes replikáció, feladatátvétel, feladat-visszavétel.|
|**Replikáció/feladatátvétel/feladat-visszavétel** | Javasoljuk, hogy hozzon létre egy szerepkört (AzureSiteRecoveryRole) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy-csoporthoz. | Adatközpont-objektum – > propagálás a gyermek objektumba, szerepkör = AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).|

## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

A helyszíni VMware virtuális gépekhez tartozó vész-helyreállítás beállításakor adja hozzá azt a vCenter Server/vSphere-gazdagépet, amelyen a virtuális gépeket felkeresi a Site Recovery-tárolóba, az alábbiak szerint:

1.   >  Nyissa meg a konfigurációs kiszolgálót a tárolóban > site Recovery infrastruktúra-**konfigurációs** kiszolgálókon.
1. A **részletek** lapon kattintson a **vCenter** elemre.
1. A **vCenter hozzáadása** területen adjon meg egy rövid nevet a vSphere-gazdagép vagy a vCenter-kiszolgáló számára.
1. Itt adhatja meg a kiszolgáló IP-címét vagy teljes tartománynevét.
1. A 443-as portot csak akkor módosítsa, ha a VMware-kiszolgálók úgy vannak konfigurálva, hogy más porton figyeljék a kéréseket.
1. Válassza ki a VMware vCenter vagy vSphere ESXi-kiszolgálóhoz való kapcsolódáshoz használt fiókot. Ezután kattintson az **OK** gombra.

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

Ha szükséges, módosíthatja a vCenter Server/vSphere gazdagéphez való kapcsolódáshoz használt hitelesítő adatokat a következőképpen:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
1. Nyissa meg a konfigurációs kiszolgáló eszközt (_cspsconfigtool.exe_) az asztali parancsikon használatával.
1. Kattintson a **fiók hozzáadása** lehetőségre a **fiók kezelése** lapon.

   ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Adja meg az új fiók adatait, majd kattintson **az OK** gombra. A fióknak szüksége van a [fiók engedélyei](#account-permissions) táblában felsorolt engedélyekre.
1. Nyissa meg a konfigurációs kiszolgálót a tárolóban > **site Recovery infrastruktúra**-  >  **konfigurációs** kiszolgálókon.
1. A **részletek** területen kattintson a **kiszolgáló frissítése** elemre.
1. A kiszolgáló frissítése feladatot követően válassza ki a vCenter Server.
1. Az **Összefoglalás** területen válassza ki az újonnan hozzáadott fiókot a **vCenter-kiszolgáló/vSphere-gazdagép fiókban**, és kattintson a **Mentés** gombra.

   ![fiók módosítása](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter Server törlése

1. Nyissa meg a konfigurációs kiszolgálót a tárolóban > **site Recovery infrastruktúra**-  >  **konfigurációs** kiszolgálókon.
1. A **részletek** lapon válassza ki a vCenter-kiszolgálót.
1. Kattintson a **Törlés** gombra.

   ![fiók törlése](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Az IP-cím és a port módosítása

Módosíthatja a vCenter Server IP-címét, illetve a kiszolgáló és a Site Recovery közötti kommunikációhoz használt portokat. Alapértelmezés szerint a Site Recovery a 443-es porton keresztül fér hozzá a vCenter Server/vSphere-gazdagép adataihoz.

1. A tároló > **site Recovery infrastruktúra**  >  -**konfigurációs kiszolgálók** területen kattintson arra a konfigurációs kiszolgálóra, amelyhez a vCenter Server hozzá van adva.
1. A **vCenter-kiszolgálók** területen kattintson a módosítani kívánt vCenter Serverra.
1. Az **Összefoglalás** lapon frissítse az IP-címet és a portot, majd mentse a módosításokat.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. A módosítások érvénybe léptetéséhez várjon 15 percet, vagy [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Az összes virtuális gép migrálása egy új kiszolgálóra

Ha az összes virtuális gépet új vCenter Server használatára szeretné áttelepíteni, csak frissítenie kell a vCenter Serverhoz rendelt IP-címet. Ne adjon hozzá egy másik VMware-fiókot, mert az ismétlődő bejegyzéseket eredményezhet. Frissítse a címeket a következőképpen:

1. A tároló > **site Recovery infrastruktúra**  >  -**konfigurációs kiszolgálók** területen kattintson arra a konfigurációs kiszolgálóra, amelyhez a vCenter Server hozzá van adva.
1. A **vCenter-kiszolgálók** szakaszban kattintson arra a vCenter Serverra, amelyet át szeretne telepíteni.
1. Az **Összefoglalás** lapon frissítse az IP-címet az új vCenter Server, és mentse a módosításokat.
1. Amint az IP-cím frissül, Site Recovery elindítja a virtuális gép felderítési információinak fogadását az új vCenter Server. Ez nem befolyásolja a folyamatban lévő replikációs tevékenységeket.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Néhány virtuális gép migrálása egy új kiszolgálóra

Ha csak néhány replikálási virtuális gépet szeretne áttelepíteni egy új vCenter Serverre, tegye a következőket:

1. [Adja hozzá](#add-vmware-server-to-the-vault) az új vCenter Server a konfigurációs kiszolgálóhoz.
1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az új kiszolgálóra áthelyezni kívánt virtuális gépek replikálását.
1. A VMware-ben telepítse át a virtuális gépeket az új vCenter Server.
1. Engedélyezze újra az áttelepített virtuális gépek [replikálását](vmware-azure-tutorial.md#enable-replication) , és válassza az új vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>A legtöbb virtuális gép migrálása egy új kiszolgálóra

Ha az új vCenter Server áttelepíteni kívánt virtuális gépek száma nagyobb, mint az eredeti vCenter Server megmaradó virtuális gépek száma, tegye a következőket:

1. Frissítse a konfigurációs kiszolgáló beállításaiban vCenter Serverhoz rendelt [IP-címet](#modify-the-ip-address-and-port) az új vCenter Server címére.
1. [Tiltsa le a replikációt](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a régi kiszolgálón maradó néhány virtuális gépen.
1. [Adja hozzá a régi vCenter Server](#add-vmware-server-to-the-vault) és az IP-címét a konfigurációs kiszolgálóhoz.
1. [Engedélyezze újra a replikációt](vmware-azure-tutorial.md#enable-replication) a régi kiszolgálón maradó virtuális gépek esetében.

## <a name="next-steps"></a>Következő lépések

Ha problémák merülnek fel, tekintse meg [vCenter Server felderítési hibák elhárítása](vmware-azure-troubleshoot-vcenter-discovery-failures.md)című témakört.
