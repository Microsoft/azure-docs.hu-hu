---
title: Virtuálisgép-példányok felderítése, értékelése és Google Cloud Platform (GCP) az Azure-ba
description: Ez a cikk a GCP virtuális gépek Azure-ba való áttelepítését ismerteti Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 9965557115206cd512450d3411a70390f2249153
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713508"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Virtuális gépek felderítése, értékelése és Google Cloud Platform (GCP) az Azure-ba

Ez az oktatóanyag bemutatja, hogyan deríthet fel, értékelhet ki és migrálhet Google Cloud Platform (GCP) virtuális gépeket (VM-eket) Azure-beli virtuális gépekre a Azure Migrate: Server Assessment and Azure Migrate: Server Migration tools használatával.

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
>
> * Ellenőrizze a migrálás előfeltételeit.
> * Azure-erőforrások előkészítése a Azure Migrate: Server Migration. Állítson be engedélyeket az Azure-fiókhoz és az erőforrásokhoz a Azure Migrate.
> * Készítse elő a GCP virtuálisgép-példányokat a migráláshoz.
> * Adja hozzá a Azure Migrate: Server Migration eszközt a Azure Migrate központban.
> * Állítsa be a replikációs berendezést, és helyezze üzembe a konfigurációs kiszolgálót.
> * Telepítse a Mobility szolgáltatás az át telepíteni kívánt GCP virtuális gépekre.
> * Engedélyezze a virtuális gépekhez a replikálást.
> * A replikáció állapotának nyomon követése és figyelése. 
> * Egy migrálási teszt futtatásával ellenőrizze, hogy minden a várt módon működik-e.
> * Teljes migrálás futtatása az Azure-ba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess"></a>Felderítés és értékelés

Az Azure-ba való migrálás előtt javasoljuk, hogy végezzen egy virtuálisgép-felderítési és -migrálási értékelést. Ez az értékelés segít a GCP virtuális gépek megfelelő méretének megfelelő méretben való megbecslésében az Azure-ba való migráláshoz, és megbecsüli az Azure lehetséges futtatás költségeit.

Az értékelést a következőképpen állíthatja be:

1. Kövesse az [oktatóanyagot](./tutorial-discover-gcp.md) az Azure beállításhoz és a GCP virtuális gépek felméréshez való előkészítéséhez. Vegye figyelembe:

    - Azure Migrate GCP virtuálisgép-példányok felderítéséhez jelszó-hitelesítést használ. A GCP-példányok alapértelmezés szerint nem támogatják a jelszó-hitelesítést. A felderítés előtt engedélyeznie kell a jelszó-hitelesítést.
        - Windows rendszerű gépek esetén engedélyezze az 5985-ös (HTTP) WinRM-portot. Ez lehetővé teszi a távoli WMI-hívásokat.
        - Linux rendszerű gépeken:
            1. Jelentkezzen be minden Linux rendszerű gépre.
            2. Nyissa meg a sshd_config fájlt: vi /etc/ssh/sshd_config
            3. A fájlban keresse meg a **PasswordAuthentication sort,** és módosítsa az értéket igen **értékre.**
            4. Mentse a fájlt, és zárja be. Indítsa újra az ssh szolgáltatást.
    - Ha gyökér szintű felhasználóval deríti fel a Linux rendszerű virtuális gépeket, győződjön meg arról, hogy a virtuális gépeken engedélyezve van a gyökér szintű bejelentkezés.
        1. Bejelentkezés az egyes Linux rendszerű gépekre
        2. Nyissa meg a sshd_config fájlt: vi /etc/ssh/sshd_config
        3. A fájlban keresse meg a **PermitRootLogin sort,** és módosítsa az értéket igen **értékre.**
        4. Mentse a fájlt, és zárja be. Indítsa újra az ssh szolgáltatást.

2. Ezután kövesse ezt az [oktatóanyagot](./tutorial-assess-gcp.md) egy új Azure Migrate és berendezés beállítását a GCP virtuális gépek felderítéséhez és értékeléséhez.

Bár azt javasoljuk, hogy próbálja ki az értékelést, az értékelés végrehajtása nem kötelező lépés a virtuális gépek áttelepítésének végrehajtásához.



## <a name="prerequisites"></a>Előfeltételek 

- Győződjön meg arról, hogy az átminálni kívánt GCP virtuális gépek támogatott operációsrendszer-verziót futtatnak. A GCP virtuális gépeket a migrálás során fizikai gépekként kezelik. Tekintse át a támogatott operációs rendszereket és [kernelverziókat](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) a fizikai kiszolgáló áttelepítési munkafolyamatához. A Linux rendszerű virtuális gépek operációsrendszer- és kernelverzióit olyan szabványos parancsokkal ellenőrizheti, mint a *hostnamectl* vagy *az uname -a.*  Javasoljuk, hogy a tényleges migrálás előtt végezzen el egy migrálási tesztet annak ellenőrzésére, hogy a virtuális gép a várt módon működik-e.
- Győződjön meg arról, hogy a [](./migrate-support-matrix-physical-migration.md#physical-server-requirements) GCP virtuális gépek megfelelnek az Azure-ba való migrálás támogatott konfigurációinak.
- Ellenőrizze, hogy az Azure-ba replikált GCP virtuális gépek megfelelnek-e az [Azure-beli virtuális gépekre vonatkozó követelményeknek.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- A virtuális gépeken bizonyos módosításokra van szükség az Azure-ba való áttelepítés előtt.
    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végre is teszi ezeket a módosításokat.
    - A migrálás megkezdése előtt fontos, hogy ezeket a módosításokat el is készítse. Ha a módosítás előtt átkullál egy virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
Tekintse át a [szükséges Windows-](prepare-for-migration.md#windows-machines) és Linux-módosításokat. [](prepare-for-migration.md#linux-machines)

### <a name="prepare-azure-resources-for-migration"></a>Azure-erőforrások előkészítése migráláshoz

Az Azure előkészítése a migrálásra Azure Migrate: Server Migration eszköz.

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

Rendelje hozzá a Virtuális gépek közreműködője szerepkört az Azure-fiókhoz. Ez a következő engedélyeket biztosítja:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás azure-beli felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

[Azure-beli](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuális hálózat (VNet) beállítása. Az Azure-ba való replikáláskor a létrehozott Azure-beli virtuális gépek a migrálás beállításakor megadott Azure-beli virtuális hálózathoz csatlakoznak.

## <a name="prepare-gcp-instances-for-migration"></a>GCP-példányok előkészítése migráláshoz

A GCP Azure-ba történő migrálásának előkészítéséhez elő kell készítenie és üzembe kell helyeznie egy replikációs berendezést a migráláshoz.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Gép előkészítése a replikációs berendezéshez

Azure Migrate: A Server Migration replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló:** A konfigurációs kiszolgáló koordinálja a GCP virtuális gépek és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló:** A folyamatkiszolgáló replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazást, tömörítést és titkosítást használva optimalizálja, majd elküldi az adatokat egy Gyorsítótár-tárfiókba az Azure-ban.

Készítse elő a berendezés üzembe helyezését a következőképpen:

- Állítson be egy külön GCP virtuális gépet a replikációs berendezéshez. Ennek a példánynak Windows Server 2012 R2 vagy Windows Server 2016 rendszert kell futnia. [Tekintse](./migrate-replication-appliance.md#appliance-requirements) át a berendezés hardver-, szoftver- és hálózatkövetelményét.
- A berendezést nem szabad olyan forrás virtuális gépre telepíteni, amelyről replikálni szeretne, vagy a Azure Migrate felderítési és értékelési berendezésre, amely korábban már telepítve lehet. Egy másik virtuális gépen kell üzembe helyezni.
- A migrálni szükséges forrás GCP virtuális gépeknek rá kell látniuk a hálózati rálátásra a replikációs berendezésre. Konfigurálja az engedélyezéshez szükséges tűzfalszabályokat. Javasoljuk, hogy a replikációs berendezést ugyanabban a VPC-hálózatban helyezze üzembe, mint a migrálni szükséges forrás virtuális gépeket. Ha a replikációs berendezésnek másik VPC-n kell lennie, a VPN-eket virtuális társviszony-létesítésen keresztül kell csatlakoztatni.
- A forrás GCP virtuális gépek a HTTPS 443 (vezérlési csatorna vezénylése) és a TCP 9443 (adatátvitel) bejövő portokon kommunikálnak a replikációs beszállítóval a replikáció kezelése és a replikációs adatátvitel érdekében. A replikációs berendezés pedig összehangolja és elküldi a replikációs adatokat az Azure-nak a 443-as kimenő HTTPS-porton keresztül. A szabályok konfigurálása érdekében szerkessze a biztonsági csoport bejövő/kimenő szabályait a megfelelő portokkal és forrás IP-címmel.

   ![GCP-tűzfalszabályok](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Tűzfalszabályok szerkesztése](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- A replikációs berendezés a MySQL-t használja. Tekintse át [a](migrate-replication-appliance.md#mysql-installation) MySQL a berendezésre való telepítésének lehetőségeit.
- Tekintse át a replikációs berendezés nyilvános [](migrate-replication-appliance.md#url-access) és kormányzati felhők eléréséhez szükséges Azure URL-címeit. [](migrate-replication-appliance.md#azure-government-url-access)

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

A migrálás első lépése a replikációs berendezés beállítása. A berendezés GCP virtuális gépek migráláshoz való beállítását le kell töltenie a berendezés telepítőfájlját, majd futtatnia kell a előkészített [virtuális gépen.](#prepare-a-machine-for-the-replication-appliance)

### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjának letöltése

1. A Azure Migrate kiszolgálók > **a** **Azure Migrate: Kiszolgáló** áttelepítése területén kattintson a Felderítés **elemre.**

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** menüben kattintson a **Nem virtualizált/Egyéb elemre.**
3. A **Célterület területen** válassza ki azt az Azure-régiót, amelybe a gépeket át szeretné miolni.
4. Válassza Annak ellenőrzése, hogy a migrálás célterülete <**régiónév-e>.**
5. Kattintson az **Erőforrások létrehozása elemre.** Ez létrehoz egy Azure Site Recovery a háttérben.
    - Ha már beállította az áttelepítést a Azure Migrate Server Migration segítségével, a cél beállítás nem konfigurálható, mivel az erőforrások korábban voltak beállítva.
    - Miután erre a gombra kattintott, nem módosíthatja a projekt célterületét.
    - A virtuális gépek másik régióba való áttelepítéséhez létre kell hoznia egy új/Azure Migrate projektet. 
    > [!NOTE]
    > Ha a létrehozáskor a privát végpontot választotta a Azure Migrate-projekt kapcsolódási módszereként, a recovery services-tároló is konfigurálva lesz a privát végpontok csatlakoztatására. Győződjön meg arról, hogy a privát végpontok elérhetőek a replikációs berendezésről: [ **További információ**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. A **Szeretne új replikációs berendezést telepíteni?** beállításban válassza **a Replikációs berendezés telepítése lehetőséget.**
7. A **Replikációs berendezés szoftverének** letöltése és telepítése című fájlban töltse le a berendezés telepítőjét és a regisztrációs kulcsot. A berendezés regisztrálása a kulccsal szükséges. A kulcs a letöltést követően öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Másolja a berendezés telepítőfájlját és kulcsfájlját a replikációs berendezéshez létrehozott Windows Server 2016 vagy Windows Server 2012 GCP virtuális gépre.
9. Futtassa a replikációs berendezés telepítőfájlját a következő eljárásban leírtak szerint.  
    9.1. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget, majd válassza a **Tovább** lehetőséget.   
    9.2 A Külső szoftverlicencben válassza az Elfogadom a harmadik féltől származó licencszerződést **lehetőséget,** majd válassza a Tovább **lehetőséget.**    
    9.3 A **Regisztrációban** válassza a **Tallózás** lehetőséget, majd lépjen a tárolóregisztrációs kulcs fájlba. Kattintson a **Tovább** gombra.  
    9.4 Az **Internetbeállításokban** válassza a Csatlakozás Azure Site Recovery **proxykiszolgáló** nélkül lehetőséget, majd kattintson a Tovább **gombra.**  
    9.5 Az **Előfeltételek ellenőrzése** lap több elem ellenőrzéseit is ellenőrzi. Ha befejeződött, válassza a **Tovább** lehetőséget.  
    9.6 A **MySQL-konfigurációban** adjon meg egy jelszót a MySQL-adatbázishoz, majd válassza a Tovább **lehetőséget.**  
    9.7 A **Környezet részletei között válassza** a Nem **lehetőséget.** Nem kell megvédenie a virtuális gépeket. Ezután válassza a **Tovább lehetőséget.**  
    9.8 A Telepítési **hely beállításban** válassza **a Tovább** lehetőséget az alapértelmezett érték elfogadásához.  
    9.9 A **Hálózatválasztás** részen válassza a **Tovább** lehetőséget az alapértelmezett érték elfogadásához.  
    9.10 Az **Összefoglalás mezőben** válassza a **Telepítés lehetőséget.**   
    9.11 **A Telepítési folyamat** megjelenik a telepítési folyamattal kapcsolatos információkkal. Ha befejeződött, válassza a **Befejezés** lehetőséget. Egy ablakban megjelenik egy újraindítással kapcsolatos üzenet. Válassza az **OK** lehetőséget.   
    9.12 Ezután egy ablakban megjelenik egy üzenet a konfigurációs kiszolgáló kapcsolati jelsérjelére vonatkozóan. Másolja a jelszót a vágólapra, és mentse a jelszót egy ideiglenes szövegfájlba a forrás virtuális gépeken. Erre a jelszóra később, a mobilitási szolgáltatás telepítési folyamata során lesz szüksége.
10. A telepítés befejezése után a berendezés konfigurációs varázslója automatikusan elindul (a varázslót manuálisan is elindíthatja a berendezés asztalán létrehozott cspsconfigtool parancsikon használatával). A varázsló Fiókok kezelése lapján adja meg a leküldéses telepítéshez használható fiókadatokat a Mobility szolgáltatás. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a replikálni szükséges forrás virtuális gépekre, ezért ebben a lépésben hozzon létre egy próbafiókot, és folytassa a műveletet. A hely nélküli fiók létrehozásához a következő adatokat használhatja : "vendég", felhasználónévként "felhasználónév", a fiók jelszavaként pedig "jelszó". Ezt a hely nélküli fiókot fogja használni a Replikáció engedélyezése szakaszban. 
11. Miután a berendezés újraindult a telepítés után, a Gépek felderítése gombra kattintva válassza ki az új berendezést a Konfigurációs kiszolgáló kiválasztása párbeszédpanelen, majd kattintson a Regisztráció **véglegesíteni lehetőségre.** A regisztráció véglegesít néhány végső feladatot a replikációs berendezés előkészítéséhez.

    ![Regisztráció véglegesít](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

A Mobility szolgáltatás telepíteni kell a forrás GCP virtuális gépekre. Az ügynöktelepítők elérhetők a replikációs berendezésen. Keresse meg a megfelelő telepítőt, és telepítse az ügynököt minden áttelepíteni kívánt gépre. Tegye a következőt:

1. Jelentkezzen be a replikációs berendezésre.
2. Lépjen a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappába.**
3. Keresse meg a forrás GCP virtuális gépek operációs rendszerének és verziójának telepítőjét. Tekintse [át a támogatott operációs rendszereket.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)
4. Másolja a telepítőfájlt az áttelepíteni kívánt forrás GCP virtuális gépre.
5. Győződjön meg arról, hogy a mentett jelszó szövegfájlja a replikációs berendezés telepítésekor jött létre.
    - Ha elfelejtette menteni a jelszót, ezzel a lépéssel megtekintheti a jelszót a replikációs berendezésen. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v parancsot** az aktuális jelszó megtekintéséhez.
    - Most másolja ezt a jelszót a vágólapra, és mentse egy ideiglenes szövegfájlba a forrás virtuális gépeken.

### <a name="installation-guide-for-windows-gcp-vms"></a>Telepítési útmutató Windows GCP virtuális gépekhez

1. Bontsa ki a telepítőfájl tartalmát egy helyi mappába (például C:\Temp) a GCP virtuális gépen az alábbiak szerint:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Futtassa a mobilitási szolgáltatás telepítőjét:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Regisztrálja az ügynököt a replikációs berendezésen:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-gcp-vms"></a>Telepítési útmutató Linux GCP virtuális gépekhez

1. Bontsa ki a telepítő tarball tartalmát egy helyi mappába (például /tmp/MobSvcInstaller) a GCP virtuális gépen a következőképpen:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Futtassa a telepítő szkriptjét:
    ```
    sudo ./install -r MS -q
    ```  

3. Regisztrálja az ügynököt a replikációs berendezésen:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-gcp-vms"></a>A GCP virtuális gépek replikációjának engedélyezése

> [!NOTE]
> A portálon egyszerre legfeljebb 10 virtuális gép replikálható. Ha több virtuális gép replikálása egyszerre történik, hozzáadhatja őket 10-es kötegben.

1. A Azure Migrate kiszolgálók **>:** **Azure Migrate server migration (Kiszolgálóáttelepítés)** projektben kattintson a Replicate (Replikálás) **elemre.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **Replikálás** lapon > **forrásbeállítások**  >  **Virtualizálva** vannak a gépek? , válassza a Nem **virtualizált/Egyéb lehetőséget.**
3. A **Helyszíni berendezés mezőben** válassza ki a Azure Migrate berendezés nevét.
4. A **Folyamatkiszolgáló mezőben** válassza ki a replikációs berendezés nevét. 
5. A **Vendég hitelesítő adatai mezőben** válassza ki [](#download-the-replication-appliance-installer) a replikáció telepítője során korábban létrehozott hely nélküli fiókot a Mobility szolgáltatás manuális telepítéséhez (a leküldéses telepítés nem támogatott). Ezután kattintson a **Tovább: Virtuális gépek elemre.**   
 
    ![Beállítások replikálása](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. A **Virtual Machines** az **Áttelepítési** beállítások importálása értékelésből? beállításnál hagyja meg az alapértelmezett Nem, manuálisan adhatja meg a **migrálási beállításokat.**
7. Ellenőrizze az összes átemelni kívánt virtuális gépet. Ezután kattintson **a Tovább: Célbeállítások elemre.**

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
9. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.  
10. A  **Gyorsítótár tárfiókja beállításban** tartsa meg az alapértelmezett beállítást a projekthez automatikusan létrehozott gyorsítótár-tárfiók használatára. Ha egy másik tárfiókot szeretne használni a replikáció gyorsítótár-tárfiókjaként, használja a legördülő menüt. <br/> 
    > [!NOTE]
    >
    > - Ha a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, adjon hozzáférést a Recovery Services-tárolónak a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Az ExpressRoute privát társviszony-létesítésen keresztüli replikálása érdekében hozzon létre egy privát végpontot a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
11. A **Rendelkezésre állási beállítások között válassza** a következőt:
    -  Rendelkezésre állási zóna az áttelepített gép adott rendelkezésre állási zónában való rögzítéshez a régióban. Ezzel a beállítással elosztja a többcsomópontos alkalmazásréteget Availability Zones. Ha ezt a lehetőséget választja, a Számítás lapon meg kell adnia az egyes kiválasztott gépekhez használni kívánt rendelkezésre állási zónát. Ez a lehetőség csak akkor érhető el, ha a migráláshoz kiválasztott célterület támogatja a Availability Zones
    -  Rendelkezésre állási készlet az áttelepített gép rendelkezésre állási készletbe való beállításhoz. A kiválasztott célerőforrás-csoportnak egy vagy több rendelkezésre állási csoporttal kell lennie a beállítás használatának érdekében.
    - Nincs szükség infrastruktúra-redundanciának beállításra, ha az áttelepített gépekhez nincs szükség egyik rendelkezésre állási konfigurációra sem.
12. A **Lemeztitkosítás típusa mezőben** válassza a következőt:
    - Titkosítás az rest-at-rest kulccsal platform által felügyelt kulccsal
    - Titkosítás az ügyfelek által felügyelt kulccsal
    - Kettős titkosítás platform által felügyelt és ügyfél által kezelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-val való replikálása érdekében létre kell hoznia egy lemeztitkosítási készletet [a](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) célként meghatározott erőforráscsoportban. A lemeztitkosítási készlet objektuma Managed Disks leképezi azokat egy Key Vault, amely tartalmazza az SSE-hez használni kívánt CMK-t.
  
13. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-vmware/target-settings.png)

14. A **Számítás területen** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusát és rendelkezésre állási konfigurációját (ha az előző lépésben kiválasztotta). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben megjelenik az ajánlott méret. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet.
    - **Operációsrendszer-lemez:** Adja meg a virtuális gép operációsrendszer- (rendszerindítási) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található.
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

## <a name="track-and-monitor-replication-status"></a>Replikációs állapot nyomon követése és figyelése

- Amikor a **Replikálás megkezdése gombra** kattint, elindul a Replikáció megkezdése feladat.
- Amikor a Replikáció megkezdése feladat sikeresen befejeződik, a virtuális gépek megkezdik a kezdeti replikációt az Azure-ba.
- A kezdeti replikáció befejeződése után megkezdődik a változásreplikáció. A GCP virtuálisgép-lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure replikalemezeibe.

A feladat állapotát a portál értesítései között követheti nyomon.

A replikáció állapotának figyelése érdekében kattintson a Kiszolgálók replikálása elemre **a Azure Migrate: Server Migration (Kiszolgálóáttelepítés) gombra.**   

![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A változásreplikáció megkezdésekor futtathat egy tesztmigrálást a virtuális gépeken, mielőtt teljes migrálást futtat az Azure-ba. A migrálási teszt erősen ajánlott, és lehetőséget biztosít a lehetséges problémák felderítésére és a problémák megoldásához, mielőtt folytatja a tényleges migrálást. Javasoljuk, hogy ezt minden virtuális gépnél legalább egyszer tegye meg, mielőtt mi áttelepítést ad.

- Migrálási teszt futtatása ellenőrzi, hogy a migrálás a várt módon működik-e anélkül, hogy ez hatással lenne a működőképes GCP virtuális gépekre, és folytatja a replikálásokat.
- A migrálási teszt egy Azure-beli virtuális gép replikált adatokkal való létrehozásával szimulálja a migrálást (általában az Azure-előfizetés nem éles virtuális hálózatára való migrálás).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti a migrálást, tesztelheti az alkalmazásokat, és a teljes migrálás előtt megoldhatja a problémákat.

A következőképpen hajtsa végre a migrálási tesztet:

1. Az **Áttelepítési célok**  >  **Kiszolgálók**  >  **Azure Migrate: Kiszolgálóáttelepítés alatt** kattintson a **Migrált kiszolgálók tesztelése elemre.**

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-gcp-vms"></a>GCP virtuális gépek áttelepítése

Miután ellenőrizte, hogy a migrálási teszt a várt módon működik-e, migrálhatja a GCP virtuális gépeket.

1. Az Azure Migrate server migration **>: Server** Migration Azure Migrate projektben kattintson a  >   **Kiszolgálók replikálása elemre.**

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Migrate** Shut down virtual machines and perform a planned migration with no data loss (Virtuális gépek áttelepítése adatvesztés nélkül) beállításnál válassza az  >   **Igen**  >  **OK lehetőséget.**
    - Ha nem szeretné leállítani a virtuális gépet, válassza a Nem **lehetőséget.**
4. A virtuálisgép-migrálási feladat elindul. A feladat állapotát a portál jobb felső oldalán található értesítési harang ikonra kattintva vagy a Server Migration eszköz Feladatok oldalára kattintva tekintheti meg (kattintson az Eszközcsempére az Áttekintés > a bal oldali menüBen válassza a Feladatok lehetőséget).
5. A feladat befejeztével a virtuális gépet a Virtuális gépek oldalon tekintheti meg és kezelheti.

### <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. A migrálás után kattintson a jobb gombbal a virtuális gépre, és > **migrálást.** Ez a következőket teszi:
    - Leállítja a GCP virtuális gép replikációját.
    - Eltávolítja a GCP  virtuális gépet a replikáló kiszolgálók számból a Azure Migrate: Server Migration területen.
    - Megtisztítja a virtuális gép replikációs állapotinformációit.
2. Telepítse az Azure-beli virtuális gép [Windows-](../virtual-machines/extensions/agent-windows.md) vagy [Linux-ügynökét](../virtual-machines/extensions/agent-linux.md) a migrált gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. A migrált Azure-beli virtuálisgép-példány forgalmának átvágása.
6. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 




## <a name="post-migration-best-practices"></a>Ajánlott eljárások a migrálást követő folyamathoz

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő forgalomhoz való hozzáférést [Azure Security Center - Időben elérhető adminisztráció.](../security-center/security-center-just-in-time.md)
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.



## <a name="troubleshooting--tips"></a>Hibaelhárítás / Tippek

**Kérdés:** Nem látom a GCP virtuális gépemet a migráláshoz felderített kiszolgálók listájában   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy a mobilitási ügynök telepítve van a migrálni szükséges forrás virtuális gépre, és regisztrálva van a konfigurációs kiszolgáló. Ellenőrizze a tűzfalszabályokat a replikációs berendezés és a forrás GCP virtuális gépek közötti hálózati elérési út engedélyezéséhez.  

**Kérdés:** Hogyan, hogy a virtuális gépem migrálva lett-e   
**Válasz:** A migrálás után a virtuális gépet a virtuális gép Virtual Machines megtekintheti és kezelheti. Az ellenőrzéshez csatlakozzon a migrált virtuális géphez.  

**Kérdés:** Nem tudok virtuális gépeket importálni a korábban létrehozott Server Assessment-eredményekből való migráláshoz   
**Válasz:** Jelenleg nem támogatjuk az értékelés importálását ehhez a munkafolyamathoz. Áthidaló megoldásként exportálhatja az értékelést, majd manuálisan kiválaszthatja a virtuális gépre vonatkozó javaslatot a Replikáció engedélyezése lépés során.
  
**Kérdés:** A "Nem sikerült behívni a BIOS GUID azonosítóját" hibaüzenet jelenik meg a GCP virtuális gépek felderítése közben   
**Válasz:** Hitelesítéshez használjon gyökér szintű bejelentkezést, ne pszeudofelhasználót. Ha nem tud gyökér szintű felhasználót használni, győződjön meg arról, hogy a szükséges képességek be vannak állítva a felhasználón a támogatási [mátrixban megadott utasítások szerint.](migrate-support-matrix-physical.md#physical-server-requirements) Tekintse át a GCP virtuális gépek támogatott operációs rendszereit is.  

**Kérdés:** A replikáció állapota nem halad   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy engedélyezte a szükséges portokat a replikációs berendezésen a 9443-as ÉS a HTTPS 443-as TCP-porton az adatátvitelhez. Győződjön meg arról, hogy a replikációs berendezésnek nincsenek elavult duplikált verziói ugyanathoz a projekthez csatlakoztatva.   

**Kérdés:** Nem sikerül felderíteni a GCP-példányokat a Azure Migrate a távoli Windows felügyeleti szolgáltatás 504-es HTTP-állapotkódja miatt    
**Válasz:** Tekintse át az Azure Migrate-berendezésre és az URL-hozzáférési igényekre vonatkozó követelményeket. Győződjön meg arról, hogy nincsenek olyan proxybeállítások, amelyek blokkolják a berendezés regisztrációját.

**Kérdés:** Módosítanom kell a GCP virtuális gépeket az Azure-ba való áttelepítés előtt?   
**Válasz:** Előfordulhat, hogy az EC2 virtuális gépek Azure-ba való áttelepítése előtt el kell majd látnia ezeket a módosításokat:

- Ha a cloud-initet használja a virtuális gép üzembeásához, akkor az Azure-ba való replikálás előtt tiltsa le a cloud-initet a virtuális gépen. A cloud-init által a virtuális gépen végrehajtott kiépítési lépések esetleg GCP-specifikusak, és nem érvényesek az Azure-ba való migrálás után.  
- Tekintse át [az előfeltételeket](#prerequisites) ismertető szakaszt, és állapítsa meg, hogy szükséges-e bármilyen módosítás az operációs rendszerhez az Azure-ba való áttelepítés előtt.
- Javasoljuk, hogy a végső migrálás előtt mindig futtatassa le a migrálás tesztelését.  

## <a name="next-steps"></a>Következő lépések

A felhőbe [való migrálás folyamatának kivizsgálása](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure felhőadaptálási keretrendszer.