---
title: Telepítse át a Windows Server 2008-kiszolgálókat az Azure-ba Azure Migrate/Site Recovery
description: Ez a cikk bemutatja, hogyan telepítheti át a helyszíni Windows Server 2008 rendszerű gépeket az Azure-ba, és javasolja a Azure Migrate használatát.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369488"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Windows Server 2008 rendszert futtató kiszolgálók migrálása az Azure-ba

Ez az oktatóanyag bemutatja, hogyan telepítheti át a Windows Server 2008 vagy 2008 R2 rendszert futtató helyszíni kiszolgálókat az Azure-ba a Azure Site Recovery használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő a helyszíni környezetet az áttelepítéshez.
> * Állítsa be a célkörnyezetet.
> * Állítson be replikációs szabályzatot.
> * Engedélyezze a replikációt.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Feladatátvétel az Azure-ba, és az áttelepítés befejezése.

## <a name="migrate-with-azure-migrate"></a>Migrálás Azure Migrate

Javasoljuk, hogy telepítse át a gépeket az Azure-ba a [Azure Migrate](../migrate/migrate-services-overview.md) szolgáltatás használatával. 

- A Azure Migrate a kiszolgáló áttelepítéséhez készült.
- Azure Migrate központosított hubot biztosít a helyszíni gépek Azure-ba való felderítéséhez, értékeléséhez és áttelepítéséhez. Azure Site Recovery csak vész-helyreállításra kell használni, és nem kell áttelepítenie.
- Azure Migrate támogatja a Windows Server 2008 rendszert futtató kiszolgálók áttelepítését.


## <a name="migrate-with-site-recovery"></a>Migrálás Site Recovery

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek


|Operációs rendszer  | Környezet  |
|---------|---------|
|Windows Server 2008 SP2 – 32 bites és 64 bites változatok (IA-32 és x86-64)</br>– Standard</br>– Vállalati</br>– Adatközpont   |     VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók    |
|Windows Server 2008 R2 SP1 – 64 bites változat</br>– Standard</br>– Vállalati</br>– Adatközpont     |     VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók|

> [!WARNING]
> - A Server Core-t futtató kiszolgálók migrálása nem támogatott.
> - A migrálás megkezdése előtt győződjön meg arról, hogy a legújabb szervizcsomag és Windows-frissítés van telepítve.


### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, érdemes áttekinteni a [VMware és a fizikai kiszolgáló áttelepítéséhez](vmware-azure-architecture.md) vagy a [Hyper-V virtuális gépek áttelepítéséhez](hyper-v-azure-architecture.md) szükséges Azure site Recovery architektúrát 

Windows Server 2008 vagy Windows Server 2008 R2 rendszerű Hyper-V virtuális gépek migrálásához kövesse a [Helyszíni gépek migrálása az Azure-ba](migrate-tutorial-on-premises-azure.md) című oktatóanyag lépéseit.

Ez az oktatóanyag a továbbiakban a Windows Server 2008 vagy 2008 R2 rendszert futtató, helyszíni VMware virtuális gépek és fizikai kiszolgálók migrálásának módját mutatja be.
> [!TIP]
> Ügynök nélküli módot keres a VMware virtuális gépek Azure-ba való áttelepítésére? [Kattintson ide](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

- A Windows Server 2008 SP2-kiszolgálók migrálásához használt konfigurációs kiszolgálónak, további folyamatkiszolgálóknak és mobilitási szolgáltatásnak az Azure Site Recovery szoftver 9.19.0.0-ás vagy újabb verzióját kell futtatnia.

- Az alkalmazáskonzisztens helyreállítási pontok és a több virtuális gépre kiterjedő konzisztencia a Windows Server 2008 SP2 rendszerű kiszolgálók replikációja esetében nem támogatottak. A Windows Server 2008 SP2-kiszolgálókat összeomlás-konzisztens helyreállítási pontra kell migrálni. Az összeomlás-konzisztens helyreállítási pontok alapértelmezés szerint öt (5) percenként jönnek létre. A replikációs házirend és a konfigurált alkalmazáskonzisztens pillanatkép-készítési gyakoriság együttes használata az alkalmazáskonzisztens helyreállítási pontok hiánya miatt a replikáció állapota kritikusra válthat. A téves riasztások elkerülése érdekében állítsa az alkalmazáskonzisztens pillanatkép-készítési gyakoriságot „Ki” állapotba a replikációs házirendben.

- A migrált kiszolgálók esetében a .NET-keretrendszer 3.5 Service Pack 1 verziójára van szükség a mobilitási szolgáltatás működéséhez.

- Ha a kiszolgáló dinamikus lemezekkel rendelkezik, bizonyos konfigurációk esetében előfordulhat, hogy ezek a lemezek a feladatátvételen átesett kiszolgálón offline állapotúként vannak megjelölve, illetve külső lemezként jelennek meg. Előfordulhat továbbá, hogy a dinamikus lemezeken található tükrözött kötetek tükrözött készleteinek állapota esetében a „Redundanciahiba” van megjelölve. A problémát a diskmgmt.msc segítségével oldhatja meg a lemezek manuális importálásával és újraaktiválásával.

- A migrált kiszolgálóknak rendelkezniük kell a vmstorfl.sys illesztővel. A feladatátvétel meghiúsulhat, ha az illesztő nem található meg a migrált kiszolgálón. 
  > [!TIP]
  >Ellenőrizze, hogy az illesztő elérhető-e a „C:\Windows\system32\drivers\vmstorfl.sys” útvonalon. Ha az illesztőprogram nem található, a probléma megkerüléséhez hozzon létre helyette egy helyőrző fájlt. 
  >
  > Nyissa meg a parancssort (run > cmd), és futtassa a következőt: „copy nul c:\Windows\system32\drivers\vmstorfl.sys”

- Előfordulhat, hogy nem lehet RDP-n keresztül csatlakozni a 32 bites operációs rendszert futtató Windows Server 2008 SP2-kiszolgálókhoz közvetlenül az Azure-ba történő feladatátvétel vagy feladatátvételi teszt után. Indítsa újra a feladatátvételen átesett virtuális gépet az Azure Portalról, és próbáljon újracsatlakozni. Ha még mindig nem lehet csatlakozni, ellenőrizze, hogy a kiszolgáló beállítása engedélyezi-e a távoli asztali kapcsolatokat, és győződjön meg arról is, hogy nincsenek érvényben olyan tűzfalszabályok vagy hálózati biztonsági csoportok, amelyek blokkolhatják a kapcsolatot. 
  > [!TIP]
  > A kiszolgálók migrálása előtt feltétlenül javasolt egy feladatátvételi teszt futtatása. Győződjön meg arról, hogy legalább egy sikeres feladatátvételi tesztet hajtott végre minden áttelepíteni kívánt kiszolgálón. A feladatátvételi teszt részeként csatlakozzon a tesztelt, feladatátvételen átesett számítógéphez, és győződjön meg arról, hogy minden az elvárt módon működik.
  >
  >A feladatátvételi teszt zavart nem okozó művelet, amely virtuális gépek egy tetszőleges, elkülönített hálózatban való létrehozásával nyújt segítséget a migrálás teszteléséhez. A feladatátvételi művelettel ellentétben a feladatátvételi teszt során az adatreplikáció tovább folytatódik. A migrálás megkezdése előtt tetszőleges számú feladatátvételi teszt hajtható végre. 
  >
  


### <a name="get-started"></a>Bevezetés

Az Azure-előfizetés és a helyszíni VMware-/fizikai környezet előkészítéséhez az alábbi feladatokat kell végrehajtania:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. A helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md) előkészítése


### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)  >  **Recovery Servicesba**.
2. Kattintson **az erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **biztonsági mentése és site Recovery** elemre.
3. A **Név** mezőben adja meg a **W2K8-migration** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozza létre a **w2k8migrate** erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók kereséséhez tekintse meg a földrajzi elérhetőség [Azure site Recovery díjszabását](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Képernyőfelvétel: új tár-létrehozási beállítások.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Az új tároló megjelenik az **Irányítópult****Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.


### <a name="prepare-your-on-premises-environment-for-migration"></a>A helyszíni környezet előkészítése a migráláshoz

- A VMware-en futó, Windows Server 2008 rendszerű virtuális gépek migrálásához [állítsa be a helyszíni konfigurációs kiszolgálót a VMware-en](vmware-azure-tutorial.md#set-up-the-source-environment).
- Ha nem lehet VMware virtuális gépként beállítani, [helyszíni fizikai kiszolgálón vagy virtuális gépen állítsa be a konfigurációs kiszolgálót](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **infrastruktúra előkészítése**  >  **cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.


### <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Új replikációs házirend létrehozásához kattintson **site Recovery infrastruktúra**-  >  **replikációs házirendek**  >  **+ replikációs házirend** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). A rendszer riasztást ad, ha a replikációs RPO túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált kiszolgálók az ablak bármely pontjára visszaállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításnál adja meg a **Ki** értéket. A szabályzat létrehozásához kattintson az **OK** gombra.

A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.

> [!WARNING]
> Győződjön meg arról, hogy a replikációs szabályzat Alkalmazáskonzisztens pillanatkép gyakorisága beállításánál a **KI** érték szerepel. A Windows Server 2008 rendszert futtató kiszolgálók replikálása esetében csak az összeomlás-konzisztens helyreállítási pontok támogatottak. Az alkalmazás-konzisztens Pillanatképek gyakoriságának bármilyen más értékének megadása hamis riasztásokat eredményez azáltal, hogy az alkalmazás-konzisztens helyreállítási pontok hiánya miatt a kiszolgáló replikációs állapotát kritikus állapotba állítja.

   ![A replikációs házirend létrehozási lehetőségeit bemutató képernyőkép.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>A replikáció engedélyezése

[Engedélyezze a replikációt](physical-azure-disaster-recovery.md#enable-replication) a migrálásra váró Windows Server 2008 SP2-/Windows Server 2008 R2 SP1-kiszolgáló számára.
   
   ![A fizikai gépek hozzáadására szolgáló lehetőségeket bemutató képernyőkép.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![A replikálást engedélyező beállításokat bemutató képernyőkép.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Migrálási teszt futtatása

Ha a kezdeti replikálás befejeződött, és a kiszolgáló állapota **Védett**, elvégezhet egy feladatátvételi tesztet a replikálási kiszolgálókkal.

A [rest failover](tutorial-dr-drill-azure.md) parancs Azure-ban történő futtatásával győződjön meg arról, hogy minden a vártnak megfelelően működik-e.

   ![A feladatátvételi teszt paranccsal ábrázoló képernyőkép.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy feladatátvételt a migrálni kívánt gépen.

1. A **Beállítások**  >  **replikált elemek** elemnél kattintson a gép > **feladatátvétel** elemre.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a kiszolgálót a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető.
4. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
5. A **replikált elemek** területen kattintson a jobb gombbal a kiszolgálóra > a **teljes áttelepítést**. Ez a következő műveleteket végzi el:

    - Befejezi az áttelepítési folyamatot, leállítja a kiszolgáló replikálását, és leállítja a kiszolgálás Site Recovery számlázását.
    - Ezzel a lépéssel megtisztítja a replikációs adatvédelmet. Nem törli az áttelepített virtuális gépeket.

   ![Képernyőkép a teljes áttelepítési parancsról.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: a kiszolgáló replikációja leáll a feladatátvétel elindítása előtt. Ha megszakít egy folyamatban lévő feladatátvételt, a feladatátvétel leáll, de a kiszolgáló nem fog tovább replikálni.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.