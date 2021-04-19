---
title: EC2 virtuális gépek felderítése, értékelése és Amazon Web Services (AWS) Az Azure-ba
description: Ez a cikk az AWS virtuális gépek Azure-ba való áttelepítését ismerteti Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 4879c8370953a5ac8c6b46efe8010db9692d3052
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714505"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek felderítése, felmérése és migrálása az Azure-ba

Ez az oktatóanyag bemutatja, hogyan deríthet fel, értékelhet ki és migrálhet Amazon Web Services (AWS) virtuális gépeket (VM-eket) Azure-beli virtuális gépekre a Azure Migrate: Server Assessment and Azure Migrate: Server Migration Tools használatával.

> [!NOTE]
> Az AWS virtuális gépeket az Azure-ba miminálja úgy, hogy fizikai kiszolgálókként kezeli őket.

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
>
> * Ellenőrizze a migrálás előfeltételeit.
> * Azure-erőforrások előkészítése a következő Azure Migrate: Server Migration. Állítson be engedélyeket az Azure-fiókhoz és az erőforrásokhoz a Azure Migrate.
> * Készítse elő az AWS EC2-példányokat a migráláshoz.
> * Adja hozzá a Azure Migrate: Server Migration eszközt a Azure Migrate központban.
> * Állítsa be a replikációs berendezést, és helyezze üzembe a konfigurációs kiszolgálót.
> * Telepítse a Mobility szolgáltatás az át telepíteni kívánt AWS virtuális gépekre.
> * Engedélyezze a virtuális gépekhez a replikálást.
> * Kövesse nyomon és figyelje a replikáció állapotát. 
> * Egy migrálási teszt futtatásával ellenőrizze, hogy minden a várt módon működik-e.
> * Teljes migrálás futtatása az Azure-ba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess"></a>Felderítés és értékelés

Az Azure-ba való migrálás előtt javasoljuk, hogy végezzen el egy virtuálisgép-felderítési és -migrálási értékelést. Ez az értékelés segít az AWS virtuális gépek megfelelő méretének megbecslésében az Azure-ba való migráláshoz, és megbecsüli az Azure lehetséges futtatás költségeit.

Az értékelést a következőképpen állíthatja be:

1. Kövesse az [oktatóanyagot](./tutorial-discover-physical.md) az Azure beállításhoz és az AWS virtuális gépek felméréshez való előkészítéséhez. Vegye figyelembe:

    - Azure Migrate AWS-példányok felderítéséhez jelszó-hitelesítést használ. Az AWS-példányok alapértelmezés szerint nem támogatják a jelszó-hitelesítést. A példány felderítése előtt engedélyeznie kell a jelszó-hitelesítést.
        - Windows rendszerű gépek esetén engedélyezze az 5985-ös (HTTP) WinRM-portot. Ez lehetővé teszi a távoli WMI-hívásokat.
        - Linux rendszerű gépeken:
            1. Jelentkezzen be minden Linux rendszerű gépre.
            2. Nyissa meg a sshd_config fájlt: vi /etc/ssh/sshd_config
            3. A fájlban keresse meg a **PasswordAuthentication sort,** és módosítsa az értéket igen **értékre.**
            4. Mentse a fájlt, és zárja be. Indítsa újra az ssh szolgáltatást.
    - Ha gyökér szintű felhasználót használ a Linux rendszerű virtuális gépek felderítése érdekében, győződjön meg arról, hogy a gyökér szintű bejelentkezés engedélyezve van a virtuális gépeken.
        1. Bejelentkezés az egyes Linux rendszerű gépekre
        2. Nyissa meg a sshd_config fájlt: vi /etc/ssh/sshd_config
        3. A fájlban keresse meg a **PermitRootLogin sort,** és módosítsa az értéket igen **értékre.**
        4. Mentse a fájlt, és zárja be. Indítsa újra az ssh szolgáltatást.

2. Ezután kövesse ezt az [oktatóanyagot,](./tutorial-assess-physical.md) és állítson be egy új Azure Migrate és berendezést az AWS virtuális gépek felderítéséhez és értékeléséhez.

Bár azt javasoljuk, hogy próbáljon ki egy értékelést, az értékelés végrehajtása nem kötelező lépés a virtuális gépek áttelepítésének végrehajtásához.



## <a name="prerequisites"></a>Előfeltételek 

- Győződjön meg arról, hogy az átminálni kívánt AWS virtuális gépek támogatott operációsrendszer-verziót futtatnak. Az AWS virtuális gépeket a migrálás során fizikai gépekként kezelik. Tekintse át [a fizikai kiszolgálók](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) áttelepítésének munkafolyamatához támogatott operációs rendszereket és kernelverziókat. A Linux rendszerű virtuális gépek operációsrendszer- és kernelverzióit olyan szabványos parancsokkal ellenőrizheti, mint a *hostnamectl* vagy *a uname -a.*  Javasoljuk, hogy hajtson végre egy migrálási tesztet (feladatátvételi tesztet), amely ellenőrzi, hogy a virtuális gép a várt módon működik-e, mielőtt folytatná a tényleges migrálást.
- Győződjön meg arról, hogy az [](./migrate-support-matrix-physical-migration.md#physical-server-requirements) AWS virtuális gépek megfelelnek az Azure-ba való migrálás támogatott konfigurációinak.
- Ellenőrizze, hogy az Azure-ba replikált AWS virtuális gépek megfelelnek-e az Azure-beli [virtuális gépekre vonatkozó követelményeknek.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- A virtuális gépeken módosításokra van szükség az Azure-ba való áttelepítés előtt.
    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végre is teszi ezeket a módosításokat.
    - A migrálás megkezdése előtt fontos, hogy ezeket a módosításokat el tudja eszközlni. Ha a módosítás előtt miigrál a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
Tekintse át a [szükséges Windows-](prepare-for-migration.md#windows-machines) és Linux-módosításokat. [](prepare-for-migration.md#linux-machines)

### <a name="prepare-azure-resources-for-migration"></a>Azure-erőforrások előkészítése migráláshoz

Az Azure előkészítése a migrálásra a Azure Migrate: Server Migration eszköz használatával.

**Feladat** | **Részletek**
--- | ---
**Azure Migrate-projekt létrehozása** | Az Azure-fióknak Közreműködői vagy Tulajdonosi engedélyekkel kell rendelkeznie [egy új projekt létrehozásához.](./create-manage-projects.md)
**Az Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak engedélyekre van szüksége egy virtuális gép létrehozásához és egy Azure-beli felügyelt lemezre való íráshoz.

### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése menüben** keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. Közreműködői **vagy** **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.

### <a name="assign-azure-account-permissions"></a>Azure-fiókengedélyek hozzárendelése

Rendelje hozzá a Virtuális gépek közreműködője szerepkört az Azure-fiókhoz. Ez a következő engedélyekkel rendelkezik:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás azure-beli felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

[Azure-beli](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuális hálózat (VNet) beállítása. Az Azure-ba való replikáláskor a létrehozott Azure-beli virtuális gépek a migrálás beállításakor megadott Azure-beli virtuális hálózathoz csatlakoznak.

## <a name="prepare-aws-instances-for-migration"></a>AWS-példányok előkészítése migráláshoz

Az AWS Azure-ba történő migrálásának előkészítéséhez elő kell készítenie és üzembe kell helyeznie egy replikációs berendezést a migráláshoz.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Gép előkészítése a replikációs berendezéshez

Azure Migrate: A Server Migration replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló:** A konfigurációs kiszolgáló koordinálja az AWS-környezet és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló:** A folyamatkiszolgáló replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazást, tömörítést és titkosítást használva optimalizálja, majd elküldi az adatokat egy Gyorsítótár-tárfiókba az Azure-ban.

Készítse elő a berendezés üzembe helyezését a következőképpen:

- Állítson be egy külön EC2 virtuális gépet a replikációs berendezéshez. Ennek a példánynak Windows Server 2012 R2 vagy Windows Server 2016 rendszert kell futnia. [Tekintse át](./migrate-replication-appliance.md#appliance-requirements) a berendezés hardver-, szoftver- és hálózatkövetelményét.
- A berendezést nem szabad olyan forrás virtuális gépre telepíteni, amely replikálni szeretné, vagy a Azure Migrate felderítési és értékelési berendezésre, amely korábban már telepítve lehet. Egy másik virtuális gépen kell üzembe helyezni.
- A migrálni szükséges forrás AWS virtuális gépeknek hálózati rálátással kell látniuk a replikációs berendezést. Ennek engedélyezéséhez konfigurálja a szükséges biztonságicsoport-szabályokat. Javasoljuk, hogy a replikációs berendezést ugyanabban a VPC-ben helyezze üzembe, mint a migrálni szükséges forrás virtuális gépeket. Ha a replikációs berendezésnek egy másik VPC-n kell lennie, a vpn-eket virtuális társviszony-létesítésen keresztül kell csatlakoztatni.
- A forrás AWS virtuális gépek a 443-as HTTPS(vezérlőcsatorna-vezénylés) és a TCP 9443 (adatátvitel) bejövő portokon kommunikálnak a replikációs beszállítóval a replikáció kezelése és a replikációs adatátvitel érdekében. A replikációs berendezés pedig összehangolja és elküldi a replikációs adatokat az Azure-nak a 443-as kimenő HTTPS-porton keresztül. A szabályok konfiguráláshoz szerkessze a biztonsági csoport bejövő/kimenő szabályait a megfelelő portokkal és forrás IP-címmel.

   ![AWS biztonsági csoportok ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Biztonsági beállítások szerkesztése ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- A replikációs berendezés a MySQL-t használja. Tekintse át [a](migrate-replication-appliance.md#mysql-installation) MySQL a berendezésre való telepítésének lehetőségeit.
- Tekintse át a replikációs berendezés nyilvános [](migrate-replication-appliance.md#url-access) és kormányzati felhők eléréséhez szükséges Azure URL-címeit. [](migrate-replication-appliance.md#azure-government-url-access)

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

A migrálás első lépése a replikációs berendezés beállítása. A berendezés AWS virtuális gépek migrálásra való beállításhoz le kell töltenie a berendezés telepítőfájlját, majd futtatnia kell az előkészített [virtuális gépen.](#prepare-a-machine-for-the-replication-appliance)

### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjának letöltése

1. A Azure Migrate kiszolgálók > **a** **Azure Migrate: Kiszolgálóáttelepítés** területén kattintson a **Felderítés elemre.**

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** alatt kattintson a Nem **virtualizált/Egyéb elemre.**
3. A **Célterület területen** válassza ki azt az Azure-régiót, amelybe a gépeket át szeretné miolni.
4. Válassza **a Győződjön meg arról, hogy a migrálás** célterülete <régiónévvel van>.
5. Kattintson az **Erőforrások létrehozása elemre.** Ez létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította a migrálást a Azure Migrate Server Migration segítségével, a cél beállítás nem konfigurálható, mivel az erőforrások korábban be voltak állítva.
    - Erre a gombra kattintva nem módosíthatja a projekt célterületét.
    - A virtuális gépek egy másik régióba való áttelepítéséhez létre kell hoznia egy új/Azure Migrate projektet.  
    > [!NOTE]
    > Ha a létrehozáskor a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, a recovery services-tároló is konfigurálva lesz a privát végpontkapcsolathoz. Győződjön meg arról, hogy a privát végpontok elérhetőek a replikációs berendezésről. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. A **Do you want to install a new replication appliance? (Szeretne** új replikációs berendezést telepíteni?) mezőben válassza **a Install a replication appliance (Replikációs berendezés telepítése) lehetőséget.**
7. A Download and install the replication appliance software (A replikációs berendezés szoftverének letöltése **és telepítése)** című fájlban töltse le a berendezés telepítőjét és a regisztrációs kulcsot. A berendezés regisztrálása érdekében a kulcsra van szükség. A kulcs a letöltést követően öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Másolja a berendezés telepítőfájlját és kulcsfájlját a replikációs berendezéshez létrehozott Windows Server 2016 vagy Windows Server 2012 AWS virtuális gépre.
9. Futtassa a replikációs berendezés telepítőfájlját a következő eljárásban leírtak szerint.  
    9.1. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget, majd válassza a **Tovább** lehetőséget.   
    9.2 A Külső szoftverlicencben jelölje be az **Elfogadom** a harmadik féltől származó licencszerződést lehetőséget, majd válassza a Tovább **lehetőséget.**    
    9.3 A **Regisztrációban** válassza a **Tallózás** lehetőséget, majd lépjen a tárolóregisztrációs kulcsfájlhoz. Kattintson a **Tovább** gombra.  
    9.4 Az **Internetbeállításokban** válassza a Csatlakozás Azure Site Recovery **proxykiszolgáló** nélkül lehetőséget, majd kattintson a Tovább **gombra.**  
    9.5 Az **Előfeltételek ellenőrzése** lap több elem ellenőrzéseit is ellenőrzi. Ha befejeződött, válassza a **Tovább** lehetőséget.  
    9.6 A **MySQL-konfigurációban** adjon meg egy jelszót a MySQL-adatbázishoz, majd válassza a Tovább **lehetőséget.**  
    9.7 A **Környezet részletei között válassza** a Nem **lehetőséget.** Nem kell védenie a virtuális gépeket. Ezután válassza a **Tovább lehetőséget.**  
    9.8 A **Telepítési hely beállításban** válassza **a Tovább** lehetőséget az alapértelmezett érték elfogadásához.  
    9.9 A **Hálózatválasztás részen** válassza **a Tovább** lehetőséget az alapértelmezett érték elfogadásához.  
    9.10 Az **Összefoglalás beállításban** válassza a **Telepítés lehetőséget.**   
    9.11 **A Telepítési folyamat** megjelenik a telepítési folyamattal kapcsolatos információk között. Ha befejeződött, válassza a **Befejezés** lehetőséget. Egy ablakban megjelenik egy újraindítással kapcsolatos üzenet. Válassza az **OK** lehetőséget.   
    9.12 Ezután egy ablakban megjelenik a konfigurációs kiszolgáló kapcsolati jelsértőjele. Másolja a jelszót a vágólapra, és mentse a jelszót egy ideiglenes szövegfájlba a forrás virtuális gépeken. Erre a jelszóra később, a mobilitási szolgáltatás telepítési folyamata során lesz szüksége.
10. A telepítés befejezése után a berendezés konfigurációs varázslója automatikusan elindul (a varázslót manuálisan is elindíthatja a berendezés asztalán létrehozott cspsconfigtool parancsikon használatával). A varázsló Fiókok kezelése lapján adja meg a leküldéses telepítéshez használható fiókadatokat a Mobility szolgáltatás. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a replikálni szükséges forrás virtuális gépekre, ezért ebben a lépésben hozzon létre egy próbafiókot, és folytassa. A hely nélküli fiók létrehozásához a következő adatokat használhatja : "vendég", felhasználónévként "felhasználónév", a fiók jelszavaként pedig "jelszó". Ezt a hely nélküli fiókot fogja használni a Replikáció engedélyezése szakaszban. 
11. Miután a berendezés újraindult a telepítést követően, a Gépek felderítése menüben válassza ki az új berendezést a **Konfigurációs** kiszolgáló kiválasztása párbeszédpanelen, majd kattintson a **Regisztráció véglegesíteni lehetőségre.** A regisztráció véglegesít néhány végső feladatot a replikációs berendezés előkészítéséhez.

    ![Regisztráció véglegesít](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

A Mobility szolgáltatás telepíteni kell a forrás AWS virtuális gépekre. Az ügynöktelepítők elérhetők a replikációs berendezésen. Megkeresi a megfelelő telepítőt, és telepíti az ügynököt minden áttelepíteni kívánt gépre. Tegye a következőt:

1. Jelentkezzen be a replikációs berendezésbe.
2. Lépjen a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappába.**
3. Keresse meg a forrás AWS virtuális gépek operációs rendszerének és verziójának telepítőjét. Tekintse [át a támogatott operációs rendszereket.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)
4. Másolja a telepítőfájlt az áttelepíteni kívánt AWS-forrás virtuális gépre.
5. Győződjön meg arról, hogy a mentett jelszó szövegfájlja a replikációs berendezés telepítésekor jött létre.
    - Ha elfelejtette menteni a jelszót, ezzel a lépéssel megtekintheti a jelszót a replikációs berendezésen. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v parancsot** az aktuális jelszó megtekintéséhez.
    - Most másolja ezt a jelszót a vágólapra, és mentse egy ideiglenes szövegfájlba a forrás virtuális gépeken.

### <a name="installation-guide-for-windows-aws-vms"></a>Telepítési útmutató Windows AWS virtuális gépekhez

1. Bontsa ki a telepítőfájl tartalmát egy helyi mappába (például C:\Temp) az AWS virtuális gépen a következőképpen:

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

### <a name="installation-guide-for-linux-aws-vms"></a>Telepítési útmutató Linux AWS virtuális gépekhez

1. Bontsa ki a telepítő tarball tartalmát egy helyi mappába (például /tmp/MobSvcInstaller) az AWS virtuális gépen az alábbiak szerint:
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

## <a name="enable-replication-for-aws-vms"></a>AWS virtuális gépek replikációjának engedélyezése

> [!NOTE]
> A portálon egyszerre legfeljebb 10 virtuális gép replikálható. Ha több virtuális gép replikálása egyszerre történik, hozzáadhatja őket 10-es kötegben.

1. A kiszolgálók Azure Migrate projektben **>:** **Azure Migrate Server Migration**, kattintson a **Replikálás elemre.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **Replikálás** lapon válassza > **forrásbeállítások**  >  **Virtualizáltak a gépeket?** beállításnál válassza a **Nem virtualizált/Egyéb lehetőséget.**
3. A **Helyszíni berendezés mezőben** válassza ki a beállított Azure Migrate berendezés nevét.
4. A **Folyamatkiszolgáló részen** válassza ki a replikációs berendezés nevét. 
5. A **Vendég hitelesítő adatai mezőben** válassza ki [](#download-the-replication-appliance-installer) a replikáció telepítője során korábban létrehozott próbafiókot a Mobility szolgáltatás manuális telepítéséhez (a leküldéses telepítés nem támogatott). Ezután kattintson **a Tovább: Virtuális gépek elemre.**   
 
    ![Beállítások replikálása](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. A **Virtual Machines** migrálási beállítások importálása **értékelésből?** beállításban hagyja meg az alapértelmezett Nem, manuálisan adhatja meg a migrálási **beállításokat.**
7. Ellenőrizze az átemelni kívánt virtuális gépeket. Ezután kattintson **a Tovább: Célbeállítások elemre.**

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
9. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.  
10. A  **Gyorsítótár tárfiókja beállításban** tartsa meg az alapértelmezett beállítást a projekthez automatikusan létrehozott gyorsítótár-tárfiók használatára. Ha egy másik tárfiókot szeretne használni a replikáció gyorsítótár-tárfiókjaként, használja a legördülő menüt. <br/> 
    > [!NOTE]
    >
    > - Ha a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, adjon hozzáférést a Recovery Services-tárolónak a gyorsítótár tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Privát társviszony-létesítés esetén az ExpressRoute használatával való replikáláshoz hozzon létre egy privát végpontot a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
11. A **Rendelkezésre állási beállítások között válassza** a következőt:
    -  Rendelkezésre állási zóna a migrált gép adott rendelkezésre állási zónában való rögzítéshez a régióban. Ezzel a beállítással elosztja a többcsomópontos alkalmazásréteget Availability Zones. Ha ezt a lehetőséget választja, a Számítás lapon meg kell adnia az egyes kiválasztott gépekhez használni kívánt rendelkezésre állási zónát. Ez a lehetőség csak akkor érhető el, ha a migráláshoz kiválasztott célterület támogatja a Availability Zones
    -  Rendelkezésre állási készlet az áttelepített gép rendelkezésre állási készletbe való beállításhoz. A kiválasztott célerőforrás-csoportnak egy vagy több rendelkezésre állási csoporttal kell lennie a beállítás használatának érdekében.
    - Nincs szükség infrastruktúra-redundanciának a beállításra, ha a migrált gépek egyik rendelkezésre állási konfigurációját sem kell megadnia.
    
12. A **Lemeztitkosítás típusa beállításban** válassza a következőt:
    - Titkosítás az rest-at-rest kulccsal platform által felügyelt kulccsal
    - Titkosítás az ügyfelek által felügyelt kulccsal
    - Kettős titkosítás platform által felügyelt és felhasználó által kezelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-val való replikálása érdekében létre kell hoznia egy lemeztitkosítási [készletet a](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) célként meghatározott erőforráscsoportban. A lemeztitkosítási készlet objektuma leképezi Managed Disks SSE-hez Key Vault CMK-t tartalmazó virtuális merevlemezre.
  
13. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-vmware/target-settings.png)

14. A **Számítás területen** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemeztípusát és rendelkezésre állási konfigurációját (ha az előző lépésben kiválasztotta). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

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

## <a name="track-and-monitor-replication-status"></a>Replikációs állapot nyomon követése és figyelése

- Amikor a **Replikálás megkezdése gombra** kattint, elindul a Replikáció megkezdése feladat.
- Amikor a Replikáció megkezdése feladat sikeresen befejeződik, a virtuális gépek megkezdik a kezdeti replikációt az Azure-ba.
- A kezdeti replikáció befejeződése után megkezdődik a változásreplikáció. Az AWS virtuálisgép-lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure replikalemezeire.

A feladat állapotát a portál értesítései között követheti nyomon.

A replikáció állapotának figyelése érdekében kattintson a Kiszolgálók replikálása elemre **a Azure Migrate: Server Migration elemre.**   

![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A változásreplikáció megkezdésekor futtathat egy migrálási tesztet a virtuális gépeken, mielőtt teljes migrálást futtat az Azure-ba. A migrálási teszt erősen ajánlott, és lehetőséget nyújt a lehetséges problémák felderítésére és a problémák megoldásához, mielőtt folytatja a tényleges migrálást. Javasoljuk, hogy ezt minden virtuális gépnél legalább egyszer tegye meg, mielőtt áttenjük.

- Migrálási teszt futtatása ellenőrzi, hogy a migrálás a várt módon fog-e működni anélkül, hogy ez hatással lenne a működőképes AWS virtuális gépekre, és folytatja a replikálást.
- A migrálási teszt egy Azure-beli virtuális gép replikált adatokkal való létrehozásával szimulálja a migrálást (általában az Azure-előfizetésben található nem éles virtuális hálózatra való migrálást).
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


## <a name="migrate-aws-vms"></a>AWS virtuális gépek áttelepítése

Miután ellenőrizte, hogy a migrálási teszt a várt módon működik-e, migrálhatja az AWS virtuális gépeket.

1. Az Azure Migrate Server >: **Server** Migration Azure Migrate  >  **projektben** kattintson a **Kiszolgálók replikálása elemre.**

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Migrate** Shut down virtual machines and perform a planned migration with no data loss (Virtuális gépek áttelepítésének áttelepítése adatvesztés nélkül) beállításnál válassza az  >   **Igen**  >  **OK lehetőséget.**
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem lehetőséget.**
4. A virtuálisgép-migrálási feladat elindul. A feladat állapotát a portál jobb felső oldalán található értesítési harang ikonra kattintva vagy a Server Migration eszköz Feladatok oldalára kattintva tekintheti meg (kattintson az Eszközcsempére az Áttekintés > a bal oldali menü Feladatok kiválasztása elemét).
5. A feladat befejeztével a virtuális gépet a Virtuális gépek oldalon tekintheti meg és kezelheti.

### <a name="complete-the-migration"></a>A migrálás befejezése

1. A migrálás után kattintson a jobb gombbal a virtuális gépre, > **migrálásának leállítása parancsra.** Ez a következőket teszi:
    - Leállítja az AWS virtuális gép replikációját.
    - Eltávolítja az AWS  virtuális gépet a Kiszolgálók replikálása számból a Azure Migrate: Server Migration területen.
    - Megtisztítja a virtuális gép replikációs állapotinformációit.
2. Telepítse a [Linux-ügynököt](../virtual-machines/extensions/agent-linux.md) a migrált gépekre. Az Azure-beli virtuális gép Windows-ügynöke előre telepítve van a migrálási folyamat során.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Forgalom átvágása a migrált Azure-beli virtuálisgép-példányra.
6. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 




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



## <a name="troubleshooting--tips"></a>Hibaelhárítás / Tippek

**Kérdés:** Nem látom az AWS virtuális gépemet a migráláshoz felderített kiszolgálók listájában   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy a mobilitási ügynök telepítve van a migrálni szükséges forrás virtuális gépre, és regisztrálva van a konfigurációs kiszolgáló. Ellenőrizze a hálózati beállításokat és a tűzfalszabályokat a replikációs berendezés és a forrás AWS virtuális gépek közötti hálózati elérési út engedélyezéséhez.  

**Kérdés:** Hogyan, hogy a virtuális gépem migrálva lett-e   
**Válasz:** A migrálás után a virtuális gépet a virtuális gép Virtual Machines megtekintheti és kezelheti. Az ellenőrzéshez csatlakozzon a migrált virtuális géphez.  

**Kérdés:** Nem tudok virtuális gépeket importálni a korábban létrehozott Server Assessment-eredményekből való migráláshoz   
**Válasz:** Jelenleg nem támogatjuk az értékelés importálását ehhez a munkafolyamathoz. Áthidaló megoldásként exportálhatja az értékelést, majd manuálisan kiválaszthatja a virtuális gépre vonatkozó javaslatot a Replikáció engedélyezése lépés során.
  
**Kérdés:** A "Nem sikerült behívni a BIOS GUID azonosítóját" hibaüzenetet kapom, miközben megpróbálom felderíteni az AWS virtuális gépeket   
**Válasz:** A hitelesítéshez mindig használjon gyökér szintű bejelentkezést, ne pszeudofelhasználót. Tekintse át az AWS virtuális gépek által támogatott operációs rendszereket is.  

**Kérdés:** A replikáció állapota nem halad   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy engedélyezte a szükséges portokat a replikációs berendezésen a 9443-as ÉS a HTTPS 443-as TCP-porton az adatátvitelhez. Győződjön meg arról, hogy a replikációs berendezésnek nincsenek elavult duplikált verziói ugyanathoz a projekthez csatlakoztatva.   

**Kérdés:** Nem tudom felderíteni az AWS-példányokat a Azure Migrate a távoli Windows felügyeleti szolgáltatásból származó 504-es HTTP-állapotkód miatt    
**Válasz:** Tekintse át az Azure Migrate-berendezésre vonatkozó követelményeket és az URL-hozzáférési igényeket. Győződjön meg arról, hogy nincsenek olyan proxybeállítások, amelyek blokkolják a berendezés regisztrációját.

**Kérdés:** Módosítanom kell az AWS virtuális gépeket az Azure-ba való miminálásom előtt?   
**Válasz:** Előfordulhat, hogy az EC2 virtuális gépek Azure-ba való áttelepítése előtt el kell sajátíthatja ezeket a módosításokat:

- Ha a cloud-initet használja a virtuális gép üzembeásához, akkor az Azure-ba való replikálás előtt tiltsa le a cloud-initet a virtuális gépen. A cloud-init által a virtuális gépen végrehajtott kiépítési lépések esetleg AWS-specifikusak, és nem érvényesek az Azure-ba való migrálás után. 
- Ha a virtuális gép PV virtuális gép (para virtualizált), és nem HVM virtuális gép, akkor előfordulhat, hogy nem tudja a jelen esetben futtatni az Azure-ban, mert a para virtualizált virtuális gépek egyéni rendszerindítási sorrendet használnak az AWS-ban. Előfordulhat, hogy az Azure-ba való migrálás előtt eltávolítja a PV-illesztőprogramokat a feladaton.  
- Javasoljuk, hogy a végső migrálás előtt mindig futtason egy migrálási tesztet.  


**Kérdés:** Át tudom telepíteni az Amazon Linux operációs rendszert futtató AWS virtuális gépeket?  
**Válasz:** Az Amazon Linuxot futtató virtuális gépek nem migrálhatóak a rendszertől függően, mivel az Amazon Linux operációs rendszer csak az AWS-ben támogatott.
Az Amazon Linuxon futó számítási feladatok migrálhatóak egy CentOS/RHEL virtuális géppel az Azure-ban, és a megfelelő migrálási megközelítéssel migrálhatja az AWS Linux-gépen futó számítási feladatokat. A számítási feladattól függően például lehetnek olyan munkaterhelés-specifikus eszközök, amelyek a migrálást segítik – például adatbázisokhoz vagy webkiszolgálók esetén az üzembe helyezési eszközökhöz.

## <a name="next-steps"></a>Következő lépések

A felhőbe [való migrálás folyamatának kivizsgálása](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure felhőadaptálási keretrendszer.