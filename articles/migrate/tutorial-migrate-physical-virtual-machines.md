---
title: Telepítse át a gépeket fizikai kiszolgálóként az Azure-ba Azure Migrate használatával.
description: Ez a cikk bemutatja, hogyan telepíthet át fizikai gépeket az Azure-ba Azure Migrate használatával.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: aeeb810174ff5c21a81bcec8aa9265ff100edf91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626325"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Gépek migrálása fizikai kiszolgálóként az Azure-ba

Ez a cikk bemutatja, hogyan telepítheti át a gépeket fizikai kiszolgálóként az Azure-ba a Azure Migrate: Server áttelepítési eszköz használatával. A gépek áttelepítése a fizikai kiszolgálóként való kezeléssel számos esetben hasznos:

- Telepítse át a helyszíni fizikai kiszolgálókat.
- Virtuális gépek migrálása virtualizált platformokkal, például Xen, KVM.
- A Hyper-V-vagy VMware-alapú virtuális gépek áttelepítése, ha valamilyen okból kifolyólag nem tudja használni a [Hyper-v](tutorial-migrate-hyper-v.md)normál áttelepítési folyamatát vagy a [VMware](server-migrate-overview.md) -áttelepítést.
- Privát felhőben futó virtuális gépek áttelepítésére.
- Nyilvános felhőkben (például Amazon Web Services (AWS) vagy Google Cloud Platform (GCP) futó virtuális gépek áttelepítésére.


Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felkészülés az Azure használatára a Azure Migrate használatával: kiszolgáló áttelepítése.
> * Tekintse át az áttelepíteni kívánt gépekre vonatkozó követelményeket, és készítsen elő egy gépet az Azure Migrate replikációs berendezéshez, amely a gépek Azure-ba való felderítésére és átállítására szolgál.
> * Adja hozzá a Azure Migrate: Server áttelepítési eszközt a Azure Migrate központban.
> * Állítsa be a replikációs készüléket.
> * Telepítse a mobilitási szolgáltatást az áttelepíteni kívánt gépekre.
> * Engedélyezze a replikációt.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. Részletes utasításokért tekintse át a következő témakört: Azure Migrate.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

[Tekintse át](./agent-based-migration-architecture.md) az áttelepítési architektúrát.

## <a name="prepare-azure"></a>Az Azure előkészítése

Készítse elő az Azure-t az áttelepítéshez Azure Migrate: Server Migration.

**Feladat** | **Részletek**
--- | ---
**Azure Migrate-projekt létrehozása** | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie [egy új projekt létrehozásához](./create-manage-projects.md).
**Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak rendelkeznie kell a virtuális gép létrehozásához szükséges engedélyekkel, és írnia kell egy Azure-beli felügyelt lemezre.


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **hozzáférés engedélyezése** területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.


### <a name="assign-azure-account-permissions"></a>Azure-fiók engedélyeinek kiosztása

Rendelje hozzá a virtuális gépi közreműködő szerepkört az Azure-fiókhoz. Ez a következő engedélyekkel rendelkezik:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás egy Azure-beli felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

Azure-beli virtuális hálózat (VNet) [beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network) . Az Azure-ba történő replikáláskor Azure-beli virtuális gépek jönnek létre és csatlakozva lesznek az áttelepítés beállításakor megadott Azure-VNet.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

A fizikai kiszolgáló áttelepítésének előkészítéséhez ellenőriznie kell a fizikai kiszolgáló beállításait, és elő kell készítenie egy replikációs berendezés üzembe helyezését.

### <a name="check-machine-requirements-for-migration"></a>A gép követelményeinek ellenõrzése az áttelepítéshez

Győződjön meg arról, hogy a gépek megfelelnek az Azure-ba való Migrálás követelményeinek. 

> [!NOTE]
> Fizikai gépek áttelepítésekor Azure Migrate: a kiszolgáló áttelepítése ugyanazt a replikációs architektúrát használja, mint az ügynök-alapú vész-helyreállítás a Azure Site Recovery szolgáltatásban, és egyes összetevők ugyanazt a kódot használják. Előfordulhat, hogy egyes tartalmak Site Recovery dokumentációra hivatkoznak.

1. [Ellenőrizze](migrate-support-matrix-physical-migration.md#physical-server-requirements) a fizikai kiszolgáló követelményeit.
2. Ellenőrizze, hogy az Azure-ba replikált helyszíni gépek megfelelnek-e az Azure-beli virtuális gépekre [vonatkozó követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).
3. Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.
    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végrehajtja ezeket a módosításokat. 
    - Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban. Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Számítógép előkészítése a replikációs berendezéshez

Azure Migrate: a kiszolgáló áttelepítése replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és egy gyorsítótárbeli Storage-fiókba küldi az Azure-ban. 

Készítse elő a berendezés központi telepítését a következőképpen:

- Készítse elő a gépet a replikációs berendezés üzemeltetéséhez. [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a gép követelményeit.
- A replikációs berendezés a MySQL-t használja. Tekintse át a MySQL telepítésének [lehetőségeit](migrate-replication-appliance.md#mysql-installation) a készüléken.
- Tekintse át a replikációs berendezés számára a [nyilvános](migrate-replication-appliance.md#url-access) és a [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhők eléréséhez szükséges Azure URL-címeket.
- Tekintse át a replikációs berendezés [portra](migrate-replication-appliance.md#port-access) vonatkozó hozzáférési követelményeit.

> [!NOTE]
> A replikációs berendezést nem szabad olyan számítógépre telepíteni, amelyet replikálni szeretne, vagy a Azure Migrate felderítési és értékelési berendezésen, amelyre korábban már telepítve van.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. A fizikai kiszolgáló áttelepítésére szolgáló berendezés beállításához töltse le a készülék telepítőjének fájlját, majd futtassa az [előkészített gépen](#prepare-a-machine-for-the-replication-appliance). A készülék telepítése után regisztrálja a Azure Migrate: Server Migration.


### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjének letöltése

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **felderítés** elemre.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. A **felderítési gépeken** a  >  **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb** elemre.
4. A **cél régióban** válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
5. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
6. Kattintson az **erőforrások létrehozása** gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította a (z) Azure Migrate: kiszolgáló áttelepítését, a cél beállítás nem konfigurálható, mert az erőforrások korábban lettek beállítva.    
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - Az összes további áttelepítés erre a régióra vonatkozik.

7. A szeretné **telepíteni az új replikációs készüléket?** területen válassza **a replikációs berendezés telepítése** lehetőséget.
9. A **replikációs berendezés szoftverének letöltése és telepítése** után töltse le a készülék telepítőjét és a regisztrációs kulcsot. A készülék regisztrálásához a kulcsra van szükség. A kulcs a letöltés után öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Másolja a berendezés telepítési fájlját és a kulcs fájlját a készülékhez létrehozott Windows Server 2016 rendszerű gépre.
11. A telepítés befejezése után a berendezés konfigurálása varázsló automatikusan elindul (a varázslót manuálisan is elindíthatja a berendezés asztalán létrehozott cspsconfigtool-parancsikon használatával). A varázsló fiókok kezelése lapján adhatja meg a mobilitási szolgáltatás leküldéses telepítéséhez szükséges fiók adatait. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a forrásként szolgáló virtuális gépekre a replikáláshoz, ezért ebben a lépésben hozzon létre egy dummy-fiókot, és folytassa a következővel:. A következő adatok megadásával hozhatja létre a "vendég" nevet a "username" névvel, a felhasználónévvel és a jelszóval a fiók jelszavaként. Ezt a dummy-fiókot fogja használni a replikálás engedélyezése szakaszban. 

12. Miután a készülék újraindult a telepítés után, a **számítógépek felderítése** lapon válassza ki az új készüléket a **konfigurációs kiszolgáló kiválasztása** területen, majd kattintson a **regisztráció véglegesítése** lehetőségre. A regisztráció véglegesítése a replikációs berendezés előkészítésének néhány utolsó feladatát hajtja végre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

A regisztráció véglegesítése után is eltarthat egy ideig, amíg a felderített gépek megjelennek Azure Migrate: a kiszolgáló áttelepítése. Ahogy a virtuális gépek felderítése megtörtént, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

Az áttelepíteni kívánt gépeken telepítenie kell a mobilitási szolgáltatás ügynökét. Az ügynök-telepítők elérhetők a replikációs berendezésen. Megtalálja a megfelelő telepítőt, és telepítse az ügynököt minden olyan gépre, amelyet át szeretne telepíteni. Ezt a következőképpen teheti meg:

1. Jelentkezzen be a replikációs berendezésbe.
2. Navigáljon a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Keresse meg a számítógép operációs rendszerének és verziójának telepítőjét. Tekintse át a [támogatott operációs rendszereket](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Másolja a telepítőfájlt arra a gépre, amelyet át szeretne telepíteni.
5. Győződjön meg arról, hogy rendelkezik a készülék üzembe helyezésekor létrehozott jelszóval.
    - Tárolja a fájlt egy ideiglenes szövegfájlban a gépen.
    - A hozzáférési kódot a replikációs berendezésen szerezheti be. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** parancsot az aktuális jelszó megtekintéséhez.
    - Ne újragenerálta a jelszót. Ezzel megszakítja a kapcsolatot, és újra regisztrálnia kell a replikációs készüléket.

> [!NOTE]
> A */platform* paraméterben a *VMware* -t a VMWare virtuális gépek vagy fizikai gépek áttelepítését követően kell megadnia.

### <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Bontsa ki a telepítő fájl tartalmát egy helyi mappába (például C:\Temp) a gépen a következőképpen:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. A mobilitási szolgáltatás telepítőjének futtatása:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Bontsa ki a telepítőt a gép egy helyi mappájába (például/tmp/MobSvcInstaller) a következőképpen:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Futtassa a telepítő parancsfájlt:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Gépek replikálása

Most válassza ki a gépeket az áttelepítéshez. 

> [!NOTE]
> Egyszerre legfeljebb 10 gépet replikálhat. Ha többre van szüksége, replikálja őket egyszerre 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése** elemre, majd kattintson a **replikálás** gombra.

    ![Képernyőfelvétel a Azure Migrate-Servers (kiszolgálók) képernyőről, amely a Azure Migrate: kiszolgáló áttelepítése az áttelepítési eszközök alatt elemnél kiválasztott replikálás gombot mutatja.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **replikálás** során > a **forrásoldali beállítások**  >  **a gépek virtualizálva?**, válassza a **nem virtualizált/egyéb** lehetőséget.
3. A helyszíni **készülék** területen válassza ki a beállított Azure Migrate berendezés nevét.
4. A **Process Server** lapon válassza ki a replikációs berendezés nevét.
6. A **vendég hitelesítő adatai** területen válassza ki a [replikálási telepítő telepítése](#download-the-replication-appliance-installer) során korábban létrehozott dummy-fiókot a mobilitási szolgáltatás manuális telepítéséhez (a leküldéses telepítés nem támogatott). Ezután kattintson a **Tovább gombra: Virtual Machines**.   

    ![Képernyőfelvétel a replikálás képernyő forrás beállításai lapról, a vendég hitelesítő adatok mező kijelölve.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. **Virtual Machines** az **áttelepítési beállítások importálása az értékelésből?** beállításnál hagyja meg az alapértelmezett **nem beállítást, az áttelepítési beállításokat manuálisan kell megadnia**.
8. Tekintse át az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: cél beállítások** elemre.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
10. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
11. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
    
12. A **lemez titkosítása típusnál** válassza a következő lehetőséget:
    - Titkosítás – a platform által felügyelt kulccsal
    - Titkosítás – az ügyfél által felügyelt kulccsal
    - Dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-vel való replikálásához létre kell [hoznia egy lemezes titkosítási](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) csoportot a cél erőforráscsoport alatt. A lemezes titkosítási készlet objektum Managed Diskst képez le egy olyan Key Vaultra, amely az SSE-hoz használandó CMK tartalmazza.
  
13. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Cél beállításai](./media/tutorial-migrate-vmware/target-settings.png)

14. A **számításban** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemezének típusát és rendelkezésre állási konfigurációját (ha az előző lépésben van kiválasztva). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben az ajánlott méret látható. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet.
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található.
    - **Rendelkezésre állási zóna**: Itt adhatja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási csoport**: adja meg a használni kívánt rendelkezésre állási készletet.

![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. A **lemezek** területen adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemez) az Azure-ban. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemez beállításai](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat a replikáció elindítása előtt bármikor frissítheti, **kezelheti** a  >  **replikáló gépeket**. A replikáció kezdete után a beállítások már nem módosíthatók.

## <a name="track-and-monitor"></a>Nyomon követés és figyelés

- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.


A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése** elemre.
![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson az **áttelepített kiszolgálók tesztelése** elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **kiszolgálók replikálása** elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **áttelepítés** során  >  **állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett áttelepítést**, válassza az **Igen**  >  **OK** lehetőséget.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
    
    Megjegyzés: a fizikai kiszolgálók áttelepítéséhez az ajánlott eljárás az alkalmazásnak az áttelepítési időszak részeként való leállítása (ne hagyja, hogy az alkalmazások elfogadják a kapcsolatokat), majd kezdeményezik az áttelepítést (a kiszolgálónak futnia kell, így a hátralévő módosítások szinkronizálhatók) az áttelepítés befejeződése előtt.

4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása** elemre. Ez a következő műveleteket végzi el:
    - Leállítja a helyszíni gép replikálását.
    - Eltávolítja a gépet a **replikálási kiszolgálók** száma Azure Migrate: kiszolgáló áttelepítése.
    - A számítógép replikációs állapotának adatainak törlése.
2. Telepítse az Azure-beli VM [Windows](../virtual-machines/extensions/agent-windows.md) -vagy [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.
