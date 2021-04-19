---
title: Gépek áttelepítése fizikai kiszolgálóként az Azure-ba Azure Migrate.
description: Ez a cikk a fizikai gépek Azure-ba való áttelepítését ismerteti Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: 1563543dec0a27094c00e446a205e94535e54229
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713515"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Gépek áttelepítése fizikai kiszolgálóként az Azure-ba

Ez a cikk bemutatja, hogyan migrálhatók a gépek fizikai kiszolgálóként az Azure-ba a Azure Migrate: Server Migration eszköz használatával. A gépek fizikai kiszolgálóként való kezelésük által való áttelepítése számos helyzetben hasznos lehet:

- Helyszíni fizikai kiszolgálók áttelepítése.
- A Xen és a KVM platformok által virtualizált virtuális gépek micializálása.
- Hyper-V vagy VMware virtuális gépek áttelepítése, ha valamilyen okból nem tudja használni a normál áttelepítési folyamatot [Hyper-V](tutorial-migrate-hyper-v.md)vagy [VMware](server-migrate-overview.md) áttelepítéshez.
- Magánfelhőkben futó virtuális gépek áttelepítése.
- Nyilvános felhőkben, például AWS-ben vagy Amazon Web Services (GCP) Google Cloud Platform virtuális gépek áttelepítése.


Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan értékelheti ki és miheti át a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure használatának előkészítése a Azure Migrate: Server Migration használatával.
> * Ellenőrizze az átemelni kívánt gépekre vonatkozó követelményeket, és készítse elő a gépet a Azure Migrate replikációs berendezéshez, amely a gépek felderítését és az Azure-ba való áttelepítését jelenti.
> * Adja hozzá a Azure Migrate: Server Migration eszközt a Azure Migrate központban.
> * A replikációs berendezés beállítása.
> * Telepítse a Mobility szolgáltatás az át telepíteni kívánt gépekre.
> * Engedélyezze a replikációt.
> * Egy migrálási teszt futtatásával ellenőrizze, hogy minden a várt módon működik-e.
> * Teljes migrálás futtatása az Azure-ba.

> [!NOTE]
> Az oktatóanyagok egy forgatókönyv legegyszerűbb üzembe helyezési útvonalát mutatják be, hogy gyorsan be tudja állítani a koncepció igazolását. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. Részletes útmutatásért tekintse át a használati útmutatókat Azure Migrate.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

[Tekintse át a](./agent-based-migration-architecture.md) migrálási architektúrát.

## <a name="prepare-azure"></a>Az Azure előkészítése

Az Azure előkészítése a migrálásra Azure Migrate: Server Migration.

**Feladat** | **Részletek**
--- | ---
**Azure Migrate-projekt létrehozása** | Az Azure-fióknak Közreműködői vagy Tulajdonosi engedélyekkel kell rendelkeznie [egy új projekt létrehozásához.](./create-manage-projects.md)
**Az Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak engedélyekkel kell rendelkeznie egy virtuális gép létrehozásához és egy Azure-beli felügyelt lemezre való íráshoz.


### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése menüben** keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. Közreműködői **vagy** **tulajdonosi engedélyekkel** kell rendelkeznie.
    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.


### <a name="assign-azure-account-permissions"></a>Azure-fiókengedélyek hozzárendelése

Rendelje hozzá a Virtuális gépek közreműködője szerepkört az Azure-fiókhoz. Ez a következő engedélyekkel rendelkezik:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás azure-beli felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

[Azure-beli](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuális hálózat (VNet) beállítása. Az Azure-ba való replikáláskor a rendszer Azure-beli virtuális gépeket hoz létre, és csatlakozik a migrálás beállításakor megadott Azure-beli virtuális hálózathoz.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

A fizikai kiszolgálók áttelepítésének előkészítéséhez ellenőriznie kell a fizikai kiszolgáló beállításait, és elő kell készítenie egy replikációs berendezés üzembe helyezését.

### <a name="check-machine-requirements-for-migration"></a>A migrálás gépi követelményeinek ellenőrzése

Győződjön meg arról, hogy a gépek megfelelnek az Azure-ba való migrálás követelményeinek. 

> [!NOTE]
> Fizikai gépek áttelepítésekor Azure Migrate: A Server Migration ugyanazt a replikációs architektúrát használja, mint az ügynökalapú vészhelyreállítás az Azure Site Recovery szolgáltatásban, és egyes összetevők ugyanazt a kódbázist használják. Egyes tartalmak a dokumentációra Site Recovery hivatkoznak.

1. [Ellenőrizze a](migrate-support-matrix-physical-migration.md#physical-server-requirements) fizikai kiszolgálóra vonatkozó követelményeket.
2. Ellenőrizze, hogy az Azure-ba replikált helyszíni gépek megfelelnek-e az Azure-beli [virtuális gépekre vonatkozó követelményeknek.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)
3. A virtuális gépeken módosításokra van szükség az Azure-ba való minálás előtt.
    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végre is teszi ezeket a módosításokat. 
    - A migrálás megkezdése előtt fontos, hogy ezeket a módosításokat el tudja eszközlni. Ha a módosítás előtt miigrál a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban. Tekintse át a [szükséges Windows-](prepare-for-migration.md#windows-machines) és Linux-módosításokat. [](prepare-for-migration.md#linux-machines)

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Gép előkészítése a replikációs berendezéshez

Azure Migrate: A Server Migration replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló:** A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló:** A folyamatkiszolgáló replikációs átjáróként működik. Replikációs adatokat fogad; gyorsítótárazást, tömörítést és titkosítást optimalizál, és egy gyorsítótár-tárfiókba küldi az Azure-ban. 

Készítse elő a berendezés üzembe helyezését a következőképpen:

- Előkészít egy gépet a replikációs berendezés gazdagépeként. [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a gépre vonatkozó követelményeket.
- A replikációs berendezés a MySQL-t használja. Tekintse át [a](migrate-replication-appliance.md#mysql-installation) MySQL a berendezésre való telepítésének lehetőségeit.
- Tekintse át a replikációs berendezés nyilvános [](migrate-replication-appliance.md#url-access) és kormányzati felhők eléréséhez szükséges Azure URL-címeit. [](migrate-replication-appliance.md#azure-government-url-access)
- Tekintse [át a replikációs](migrate-replication-appliance.md#port-access) berendezés port-hozzáférési követelményeit.

> [!NOTE]
> A replikációs berendezést nem szabad telepíteni a replikálni kívánt forrásgépre vagy a Azure Migrate korábban esetleg telepített felderítési és értékelési berendezésre.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

A migrálás első lépése a replikációs berendezés beállítása. A berendezés fizikai kiszolgáló migrálásra való beállításhoz töltse le a berendezés telepítőfájlját, majd futtassa a előkészített [gépen.](#prepare-a-machine-for-the-replication-appliance) A berendezés telepítése után regisztrálja a következő Azure Migrate: Server Migration.


### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjának letöltése

1. A Azure Migrate kiszolgálók > **a** **Azure Migrate: Kiszolgáló** áttelepítése területén kattintson a Felderítés **elemre.**

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** menüben kattintson a **Nem virtualizált/Egyéb elemre.**
3. A **Célterület területen** válassza ki azt az Azure-régiót, amelybe a gépeket át szeretné miolni.
4. Válassza **a Győződjön meg arról, hogy a migrálás célterülete régiónév.**
5. Kattintson az **Erőforrások létrehozása elemre.** Ez létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította a migrálást a Azure Migrate: Server Migration segítségével, a cél beállítás nem konfigurálható, mivel az erőforrások korábban be voltak állítva.    
    - Erre a gombra kattintva nem módosíthatja a projekt célterületét.
    - Minden további migrálás ebbe a régióba lesz. 
    > [!NOTE]
    > Ha a létrehozáskor a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, a recovery services-tároló is konfigurálva lesz a privát végpontkapcsolathoz. Győződjön meg arról, hogy a privát végpontok elérhetőek a replikációs berendezésről. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. A **Do you want to install a new replication appliance? (Szeretne** új replikációs berendezést telepíteni?) mezőben válassza **a Install a replication appliance (Replikációs berendezés telepítése) lehetőséget.**
7. A Download and install the replication appliance software (A replikációs berendezés szoftverének letöltése **és telepítése)** című fájlban töltse le a berendezés telepítőjét és a regisztrációs kulcsot. A berendezés regisztrálása érdekében a kulcsra van szükség. A kulcs a letöltést követően öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Másolja a berendezés telepítőfájlját és kulcsfájlját a berendezéshez létrehozott Windows Server 2016-gépre.
9. A telepítés befejezése után a berendezés konfigurációs varázslója automatikusan elindul (a varázslót manuálisan is elindíthatja a berendezés asztalán létrehozott cspsconfigtool parancsikon használatával). A varázsló Fiókok kezelése lapján adja meg a leküldéses telepítéshez használható fiókadatokat a Mobility szolgáltatás. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a replikálni szükséges forrás virtuális gépekre, ezért ebben a lépésben hozzon létre egy próbafiókot, és folytassa a műveletet. A hely nélküli fiók létrehozásához a következő adatokat használhatja : "vendég", felhasználónévként "felhasználónév", a fiók jelszavaként pedig "jelszó". Ezt a hely nélküli fiókot fogja használni a Replikáció engedélyezése szakaszban. 

10. Miután a berendezés újraindult a telepítést követően, a Gépek felderítése párbeszédpanelen válassza ki az új berendezést a **Konfigurációs** kiszolgáló kiválasztása párbeszédpanelen, majd kattintson a **Regisztráció véglegesíteni lehetőségre.** A regisztráció véglegesít néhány végső feladatot a replikációs berendezés előkészítéséhez.

    ![Regisztráció véglegesít](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

A regisztráció véglegesítődését követően némi időre lehet szükség, amíg a felderített gépek megjelennek a Azure Migrate: Server Migrationben. Ahogy a virtuális gépek fel vannak fedezve, a **Felderített kiszolgálók** száma növekszik.

![Felderített kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

Az át telepíteni kívánt gépeken telepítenie kell a Mobility szolgáltatás ügynököt. Az ügynöktelepítők elérhetők a replikációs berendezésen. Megkeresi a megfelelő telepítőt, és telepíti az ügynököt minden áttelepíteni kívánt gépre. Ezt a következőképpen teheti meg:

1. Jelentkezzen be a replikációs berendezésbe.
2. Lépjen a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappába.**
3. Keresse meg a gép operációs rendszerének és verziójának telepítőjét. Tekintse [át a támogatott operációs rendszereket.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 
4. Másolja a telepítőfájlt az áttelepíteni kívánt gépre.
5. Győződjön meg arról, hogy a berendezés üzembe helyezésekor létrehozott jelszóval van meg.
    - Tárolja a fájlt egy ideiglenes szövegfájlban a gépen.
    - A jelszót a replikációs berendezésen szerezheti be. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v parancsot** az aktuális jelszó megtekintéséhez.
    - Ne újragenerálja a jelszót. Ez megszakítja a kapcsolatot, és újra kell regisztrálja a replikációs berendezést.

> [!NOTE]
> A */Platform paraméterben* megadhatja a VMware-t, ha *VMware* virtuális gépeket vagy fizikai gépeket minál.

### <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Bontsa ki a telepítőfájl tartalmát egy helyi mappába (például C:\Temp) a gépen a következőképpen:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Futtassa a mobilitási szolgáltatás telepítőjét:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Regisztrálja az ügynököt a replikációs berendezésen:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Bontsa ki a telepítő tarball tartalmát egy helyi mappába (például /tmp/MobSvcInstaller) a gépen a következőképpen:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Futtassa a telepítő szkriptjét:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Regisztrálja az ügynököt a replikációs berendezésen:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Gépek replikálása

Most válassza ki a migráláshoz a gépeket. 

> [!NOTE]
> Legfeljebb 10 gépet replikálhat együtt. Ha többet kell replikálni, akkor egyidejűleg replikálja őket 10-es kötegben.

1. A Azure Migrate kiszolgálók **>:** **Azure Migrate server migration (Kiszolgálóáttelepítés)** projektben kattintson a Replicate (Replikálás) **elemre.**

    ![Képernyőkép a Azure Migrate – Kiszolgálók képernyőről, amely a Azure Migrate: Kiszolgálóáttelepítés alatt a Migrálási eszközök alatt kiválasztott Replikálás gombot mutatja.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **Replikálás** lapon válassza > **forrásbeállítások**  >  **Virtualizáltak a gépeket?** beállításnál válassza a **Nem virtualizált/Egyéb lehetőséget.**
3. A **Helyszíni berendezés mezőben** válassza ki a beállított Azure Migrate berendezés nevét.
4. A **Folyamatkiszolgáló részen** válassza ki a replikációs berendezés nevét.
5. A **Vendég hitelesítő adatai mezőben** válassza ki [](#download-the-replication-appliance-installer) a replikáció telepítője során korábban létrehozott próbafiókot a Mobility szolgáltatás manuális telepítéséhez (a leküldéses telepítés nem támogatott). Ezután kattintson a **Tovább: Virtuális gépek elemre.**   

    ![A Replikálás képernyő Forrásbeállítások lapjának képernyőképe, kiemelt Vendég hitelesítő adatok mezővel.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

6. A **Virtual Machines** migrálási beállítások importálása **értékelésből?** beállításban hagyja meg az alapértelmezett Nem, manuálisan adhatja meg a migrálási **beállításokat.**
7. Ellenőrizze az átemelni kívánt virtuális gépeket. Ezután kattintson **a Tovább: Célbeállítások elemre.**

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


8. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
9. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.   
10. A  **Gyorsítótár tárfiókja beállításban** tartsa meg az alapértelmezett beállítást a projekthez automatikusan létrehozott gyorsítótár-tárfiók használatára. Ha egy másik tárfiókot szeretne használni a replikáció gyorsítótár-tárfiókjaként, használja a legördülő menüt. <br/> 
    > [!NOTE]
    >
    > - Ha a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, adjon hozzáférést a Recovery Services-tárolónak a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Az ExpressRoute privát társviszony-létesítés használatával való replikáláshoz hozzon létre egy privát végpontot a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
  
11. A **Rendelkezésre állási beállítások között válassza** a következőt:
    -  Rendelkezésre állási zóna a migrált gép adott rendelkezésre állási zónában való rögzítéshez a régióban. Ezzel a beállítással elosztja a többcsomópontos alkalmazásréteget Availability Zones. Ha ezt a lehetőséget választja, a Számítás lapon meg kell adnia az egyes kiválasztott gépekhez használni kívánt rendelkezésre állási zónát. Ez a lehetőség csak akkor érhető el, ha a migráláshoz kiválasztott célterület támogatja a Availability Zones
    -  Rendelkezésre állási készlet az áttelepített gép rendelkezésre állási készletbe való beállításhoz. A kiválasztott célerőforrás-csoportnak egy vagy több rendelkezésre állási csoporttal kell lennie a beállítás használatának érdekében.
    - Nincs szükség infrastruktúra-redundanciának a beállításra, ha a migrált gépek egyik rendelkezésre állási konfigurációját sem kell megadnia.
    
12. A **Lemeztitkosítás típusa beállításban** válassza a következőt:
    - Titkosítás az rest-at-rest kulccsal platform által felügyelt kulccsal
    - Titkosítás az ügyfelek által felügyelt kulccsal
    - Kettős titkosítás platform által felügyelt és felhasználó által kezelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-val való replikálása érdekében létre kell hoznia egy lemeztitkosítási [készletet a](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) célként meghatározott erőforráscsoportban. A lemeztitkosítási készlet objektuma Managed Disks SSE-hez Key Vault CMK-t tartalmazó virtuális merevlemezre van leképezve.
  
13. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

14. A **Számítás** területen tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemeztípusát és rendelkezésre állási konfigurációját (ha az előző lépésben kiválasztotta). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a Virtuális gép mérete legördülő menüben megjelenik az ajánlott méret. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet.
    - **Operációsrendszer-lemez:** Adja meg a virtuális gép operációsrendszer-lemezét (rendszerindítási lemezét). Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található.
    - **Rendelkezésre állási zóna:** Adja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási készlet:** Adja meg a használni kívánt rendelkezésre állási készletet.

![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. A **Lemezek területen** adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemezek) az Azure-ban. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemezbeállítások](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikációs beállításokat a replikáció kezdete előtt bármikor frissítheti: **Gépek**  >  **replikálása kezelése.** A replikáció kezdete után a beállítások már nem módosíthatók.

## <a name="track-and-monitor"></a>Nyomon követése és figyelése

- Amikor a **Replikálás megkezdése gombra** kattint, elindul a Replikáció megkezdése feladat. 
- Amikor a Replikáció megkezdése feladat sikeresen befejeződik, a gépek megkezdik a kezdeti replikációt az Azure-ba.
- A kezdeti replikáció befejeződése után megkezdődik a változásreplikáció. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replikalemezekbe.


A feladat állapotát a portál értesítései között követheti nyomon.

A replikáció állapotának figyelése érdekében kattintson a Kiszolgálók replikálása elemre **a Azure Migrate: Server Migration elemre.** 
![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A változásreplikáció megkezdésekor futtathat egy migrálási tesztet a virtuális gépeken, mielőtt teljes migrálást futtat az Azure-ba. Javasoljuk, hogy ezt minden gépnél legalább egyszer, az áttelepítés előtt tegye meg.

- Migrálási teszt futtatása ellenőrzi, hogy a migrálás a várt módon működik-e anélkül, hogy ez hatással lenne a továbbra is működőképes helyszíni gépekre, és folytatja a replikálás működését. 
- A migrálási teszt egy Replikált adatokkal (általában az Azure-előfizetésben található nem éles virtuális hálózatra való migrálás) használó Azure-beli virtuális gép létrehozásával szimulálja a migrálást.
- A replikált Azure-beli virtuális gép használatával ellenőrizheti a migrálást, elvégezheti az alkalmazástesztelést, és a teljes migrálás előtt megoldhatja a problémákat.

A következőképpen hajtsa végre a migrálási tesztet:


1. Az **Áttelepítési célok**  >  **Kiszolgálók**  >  **Azure Migrate: Kiszolgálóáttelepítés alatt** kattintson az **Áttelepített kiszolgálók tesztelése elemre.**

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a migrálási teszt a várt módon működik-e, migrálhatja a helyszíni gépeket.

1. Az Azure Migrate Server >: **Server** Migration Azure Migrate  >  **projektben** kattintson a **Kiszolgálók replikálása elemre.**

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Migrate** Shut down virtual machines and perform a planned  >  **migration with no data loss**(Virtuális gépek áttelepítésének áttelepítése adatvesztés nélkül) beállításnál válassza az **Igen**  >  **OK lehetőséget.**
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
    
    Megjegyzés: A fizikai kiszolgáló áttelepítése esetén azt javasoljuk, hogy az alkalmazást az áttelepítési ablak részeként (ne hagyja, hogy az alkalmazások kapcsolatokat fogadjanak el), majd kezdeményezze az áttelepítést (a kiszolgálót továbbra is futnia kell, hogy a többi módosítás szinkronizálható legyen) az áttelepítés befejezése előtt.

4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. A migrálás után kattintson a jobb gombbal a virtuális gépre, > **migrálásának leállítása parancsra.** Ez a következőket teszi:
    - Leállítja a helyszíni gép replikációját.
    - Eltávolítja a gépet a **replikáló** kiszolgálók számból a Azure Migrate: Server Migration gombra.
    - Megtisztítja a gép replikációs állapotinformációit.
2. Telepítse az Azure-beli virtuális gép [Windows-](../virtual-machines/extensions/agent-windows.md) vagy [Linux-ügynökét](../virtual-machines/extensions/agent-linux.md) a migrált gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Forgalom átvágása a migrált Azure-beli virtuálisgép-példányra.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Ajánlott eljárások a migrálást követő folyamathoz

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - A bejövő forgalomhoz való hozzáférés zárolása és korlátozása a [Azure Security Center – Időben elérhető adminisztráció.](../security-center/security-center-just-in-time.md)
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

A felhőbe [való migrálás folyamatának kivizsgálása](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure felhőadaptálási keretrendszer.
