---
title: Amazon Web Services (AWS) EC2-alapú virtuális gépek felderítése, felmérése és migrálása az Azure-ba
description: Ez a cikk az AWS virtuális gépek Azure-ba történő áttelepítését ismerteti Azure Migrateokkal.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 430ece58bd3dc1651ac391ba0e29515085ee507b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878189"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek felderítése, felmérése és migrálása az Azure-ba

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel, értékelheti és áttelepítheti Amazon Web Services (AWS) virtuális gépeket az Azure-beli virtuális gépekre Azure Migrate: Server Assessment és Azure Migrate: Server áttelepítési eszközök használatával.

> [!NOTE]
> Az AWS virtuális gépeket az Azure-ba migrálva fizikai kiszolgálóként kezelheti őket.

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
>
> * Ellenőrizze az áttelepítéshez szükséges előfeltételeket.
> * Azure-erőforrások előkészítése Azure Migrate: kiszolgáló áttelepítése. Állítsa be az Azure-fiókra és-erőforrásokra vonatkozó engedélyeket a Azure Migrate való együttműködéshez.
> * AWS EC2-példányok előkészítése az áttelepítéshez.
> * Adja hozzá a Azure Migrate: Server áttelepítési eszközt a Azure Migrate központban.
> * Állítsa be a replikációs készüléket, és telepítse a konfigurációs kiszolgálót.
> * Telepítse a mobilitási szolgáltatást az áttelepíteni kívánt AWS virtuális gépekre.
> * Engedélyezze a virtuális gépekhez a replikálást.
> * A replikálás állapotának nyomon követése és figyelése. 
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess"></a>Felderítés és Értékelés

Az Azure-ba való Migrálás előtt javasoljuk, hogy hajtsa végre a virtuális gépek felderítését és a Migrálás értékelését. Ez az értékelés segít az AWS-es virtuális gépek megfelelő méretének az Azure-ba való áttelepítéshez, valamint a lehetséges Azure-futtatási költségek becsléséhez.

Az értékelést a következőképpen állíthatja be:

1. Az [oktatóanyag](./tutorial-discover-physical.md) alapján állítsa be az Azure-t, és készítse elő az AWS virtuális gépeket egy értékeléshez. Vegye figyelembe:

    - A Azure Migrate a jelszó-hitelesítést használja az AWS-példányok felfedése során. Az AWS-példányok alapértelmezés szerint nem támogatják a jelszó-hitelesítést. A példány felderítése előtt engedélyeznie kell a jelszó-hitelesítést.
        - Windows rendszerű gépek esetén engedélyezze a WinRM 5985-es portját (HTTP). Ez lehetővé teszi a távoli WMI-hívásokat.
        - Linux rendszerű gépek esetén:
            1. Jelentkezzen be az egyes Linux-gépekre.
            2. Nyissa meg a sshd_config fájlt: VI/etc/ssh/sshd_config
            3. A fájlban keresse meg a **PasswordAuthentication** sort, és módosítsa az értéket **Igen** értékre.
            4. Mentse a fájlt, és a bezáráshoz. Indítsa újra az SSH-szolgáltatást.
    - Ha a Linux rendszerű virtuális gépek felderítéséhez root felhasználót használ, győződjön meg arról, hogy a virtuális gépeken engedélyezve van a rendszergazdai bejelentkezés.
        1. Bejelentkezés az egyes Linux rendszerű gépekre
        2. Nyissa meg a sshd_config fájlt: VI/etc/ssh/sshd_config
        3. A fájlban keresse meg a **PermitRootLogin** sort, és módosítsa az értéket **Igen** értékre.
        4. Mentse a fájlt, és a bezáráshoz. Indítsa újra az SSH-szolgáltatást.

2. Ezt az [oktatóanyagot](./tutorial-assess-physical.md) követve beállíthat egy Azure Migrate projektet és berendezést az AWS virtuális gépek felderítéséhez és értékeléséhez.

Bár javasoljuk, hogy kipróbáljon egy értékelést, és az értékelés végrehajtása nem kötelező lépés a virtuális gépek áttelepítéséhez.



## <a name="prerequisites"></a>Előfeltételek 

- Győződjön meg arról, hogy az áttelepíteni kívánt AWS virtuális gépek támogatott operációsrendszer-verziót futtatnak. Az AWS-alapú virtuális gépeket a Migrálás céljára szolgáló fizikai gépek kezelik. Tekintse át a fizikai kiszolgáló áttelepítési munkafolyamatának [támogatott operációs rendszereit és kernel-verzióit](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) . A Linux rendszerű virtuális gépek operációsrendszer-és kernel-verzióit a *hostnamectl* vagy *a uname-a* standard parancsok használatával tekintheti meg.  Javasoljuk, hogy végezzen el egy teszt-áttelepítést (feladatátvételi teszt) annak ellenőrzéséhez, hogy a virtuális gép a várt módon működik-e a tényleges áttelepítés folytatása előtt.
- Győződjön meg arról, hogy az AWS-beli virtuális gépek megfelelnek az Azure-ba való Migrálás [támogatott konfigurációinak](./migrate-support-matrix-physical-migration.md#physical-server-requirements) .
- Ellenőrizze, hogy az Azure-ba replikált AWS virtuális gépek megfelelnek-e az Azure-beli [virtuális gép követelményeinek.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.
    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végrehajtja ezeket a módosításokat.
    - Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.

### <a name="prepare-azure-resources-for-migration"></a>Azure-erőforrások előkészítése az áttelepítéshez

Készítse elő az Azure-t a Azure Migrate: Server áttelepítési eszközzel történő áttelepítéshez.

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

Azure-beli virtuális hálózat (VNet) [beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network) . Az Azure-ba történő replikáláskor a létrehozott Azure-beli virtuális gépek az áttelepítés beállításakor megadott Azure-VNet csatlakoznak.

## <a name="prepare-aws-instances-for-migration"></a>AWS-példányok előkészítése áttelepítésre

Az AWS Azure-ba való áttelepítésre való felkészüléséhez elő kell készítenie és telepítenie kell egy replikációs berendezést az áttelepítéshez.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Számítógép előkészítése a replikációs berendezéshez

Azure Migrate: a kiszolgáló áttelepítése replikációs berendezés használatával replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja az AWS-környezet és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatokat fogad, optimalizálja azt gyorsítótárazással, tömörítéssel és titkosítással, és egy gyorsítótárbeli Storage-fiókba küldi el az Azure-ban.

Készítse elő a berendezés központi telepítését a következőképpen:

- Állítson be egy különálló EC2 virtuális gépet a replikációs berendezés üzemeltetéséhez. Ennek a példánynak Windows Server 2012 R2 vagy Windows Server 2016 rendszernek kell futnia. [Tekintse át](./migrate-replication-appliance.md#appliance-requirements) a készülék hardver-, szoftver-és hálózati követelményeit.
- A készüléket nem szabad olyan forrásoldali virtuális gépre telepíteni, amelyet replikálni szeretne, vagy a Azure Migrate felderítési és értékelési berendezésen, amelyre korábban már telepítve van. Ezt egy másik virtuális gépre kell telepíteni.
- Az áttelepítendő forrás AWS-beli virtuális gépeknek hálózati vonallal kell rendelkezniük a replikációs berendezéshez. Konfigurálja a szükséges biztonsági csoportok szabályait ennek engedélyezéséhez. Javasoljuk, hogy a replikációs berendezést ugyanabban a VPC-ban telepítse, mint az áttelepítendő forrásként szolgáló virtuális gépeket. Ha a replikációs berendezésnek egy másik VPC-ban kell lennie, a VPCs a VPC-kapcsolaton keresztül kell csatlakoztatni.
- A forrás AWS-alapú virtuális gépek a replikálási felügyelet és a replikálási adatforgalom esetében a HTTPS 443 (vezérlési csatorna összehangolása) és a TCP 9443 (adatátviteli) porton keresztül kommunikálnak a replikációs berendezéssel. A replikációs berendezés a HTTPS 443 kimenő porton keresztül irányítja és küldi el a replikációs adatokat az Azure-nak. Ezeknek a szabályoknak a konfigurálásához szerkessze a biztonsági csoport bejövő/kimenő szabályait a megfelelő portokkal és a forrás IP-címmel kapcsolatos információkkal.

   ![AWS biztonsági csoportok ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Biztonsági beállítások szerkesztése ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- A replikációs berendezés a MySQL-t használja. Tekintse át a MySQL telepítésének [lehetőségeit](migrate-replication-appliance.md#mysql-installation) a készüléken.
- Tekintse át a replikációs berendezés számára a [nyilvános](migrate-replication-appliance.md#url-access) és a [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhők eléréséhez szükséges Azure URL-címeket.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. Az AWS virtuális gépek áttelepítésére szolgáló készülék beállításához le kell töltenie a készülék telepítőjének fájlját, majd futtatnia kell az [előkészített virtuális gépen](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>A replikációs berendezés telepítőjének letöltése

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **felderítés** elemre.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. A **felderítési gépeken** a  >  **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb** elemre.
3. A **cél régióban** válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
4. Jelölje be **a ellenőrizze, hogy az áttelepítéshez használt cél régió <régió neve>**.
5. Kattintson az **erőforrások létrehozása** gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította az áttelepítést Azure Migrate kiszolgáló áttelepítésével, a célként megadott beállítás nem konfigurálható, mert az erőforrások korábban lettek beállítva.
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - A virtuális gépek másik régióba való áttelepítéséhez létre kell hoznia egy új/eltérő Azure Migrate projektet.

6. A szeretné **telepíteni az új replikációs készüléket?** területen válassza **a replikációs berendezés telepítése** lehetőséget.
7. A **replikációs berendezés szoftverének letöltése és telepítése** után töltse le a készülék telepítőjét és a regisztrációs kulcsot. A készülék regisztrálásához a kulcsra van szükség. A kulcs a letöltés után öt napig érvényes.

    ![Szolgáltató letöltése](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Másolja a készülék telepítési fájlját és a kulcs fájlját a replikációs berendezéshez létrehozott Windows Server 2016 vagy Windows Server 2012 AWS virtuális gépre.
9. Futtassa a replikációs berendezés telepítési fájlját a következő eljárásban leírtak szerint.  
    9.1. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget, majd válassza a **Tovább** lehetőséget.   
    9,2 **harmadik féltől származó** szoftverlicenc esetében válassza **az Elfogadom a harmadik féltől származó licencszerződést** lehetőséget, majd kattintson a **tovább** gombra.   
    9,3 a **regisztráció** lapon válassza a **Tallózás** lehetőséget, majd nyissa meg a tároló regisztrációs kulcsát tartalmazó fájlt. Kattintson a **Tovább** gombra.  
    9,4 az **Internetbeállítások** területen válassza a **Csatlakozás Azure site Recovery proxykiszolgáló nélkül** lehetőséget, majd kattintson a **tovább** gombra.  
    9,5 az **Előfeltételek ellenőrzése** lap több elemre vonatkozó ellenőrzéseket futtat. Ha befejeződött, válassza a **Tovább** lehetőséget.  
    9,6 a **MySQL-konfigurációban** adjon meg egy jelszót a MySQL-adatbázishoz, majd kattintson a **tovább** gombra.  
    9,7 a **környezet részletei** területen válassza a **nem** lehetőséget. Nem kell védelemmel ellátni a virtuális gépeket. Ezután válassza a **tovább** lehetőséget.  
    9,8 a **telepítés helye** területen a **tovább** gombra kattintva fogadja el az alapértelmezett értéket.  
    9,9 a **hálózat kiválasztása** területen válassza a **tovább** lehetőséget az alapértelmezett érték elfogadásához.  
    9,10 az **Összefoglalás** területen válassza a **telepítés** lehetőséget.   
    9,11 a **telepítési** folyamat adatai a telepítési folyamattal kapcsolatos információkat jelenítenek meg. Ha befejeződött, válassza a **Befejezés** lehetőséget. Egy ablakban megjelenik egy újraindítással kapcsolatos üzenet. Válassza az **OK** lehetőséget.   
    9,12 következő lépésként egy ablak egy üzenetet jelenít meg a konfigurációs kiszolgáló hozzáférési jelszavával kapcsolatban. Másolja a jelszót a vágólapra, és mentse a jelszót egy ideiglenes szövegfájlba a forrásként szolgáló virtuális gépeken. Ezt a jelszót később kell megadnia a mobilitási szolgáltatás telepítési folyamata során.
10. A telepítés befejezése után a berendezés konfigurálása varázsló automatikusan elindul (a varázslót manuálisan is elindíthatja a berendezés asztalán létrehozott cspsconfigtool-parancsikon használatával). A varázsló fiókok kezelése lapján adhatja meg a mobilitási szolgáltatás leküldéses telepítéséhez szükséges fiók adatait. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a forrásként szolgáló virtuális gépekre a replikáláshoz, ezért ebben a lépésben hozzon létre egy dummy-fiókot, és folytassa a következővel:. A következő adatok megadásával hozhatja létre a "vendég" nevet a "username" névvel, a felhasználónévvel és a jelszóval a fiók jelszavaként. Ezt a dummy-fiókot fogja használni a replikálás engedélyezése szakaszban. 
11. Miután a készülék újraindult a telepítés után, a **számítógépek felderítése** lapon válassza ki az új készüléket a **konfigurációs kiszolgáló kiválasztása** területen, majd kattintson a **regisztráció véglegesítése** lehetőségre. A regisztráció véglegesítése a replikációs berendezés előkészítésének néhány utolsó feladatát hajtja végre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatás ügynökét telepíteni kell az áttelepítendő, a forrás AWS virtuális gépekre. Az ügynök-telepítők elérhetők a replikációs berendezésen. Megtalálja a megfelelő telepítőt, és telepítse az ügynököt minden olyan gépre, amelyet át szeretne telepíteni. Tegye a következőket:

1. Jelentkezzen be a replikációs berendezésbe.
2. Navigáljon a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Keresse meg a forrás AWS virtuális gépek operációs rendszerének és verziójának telepítőjét. Tekintse át a [támogatott operációs rendszereket](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Másolja a telepítőfájlt arra a forrás AWS virtuális gépre, amelyet át szeretne telepíteni.
5. Győződjön meg arról, hogy a mentett jelszó szövegfájlja a replikációs berendezés telepítésekor lett létrehozva.
    - Ha elfelejtette a jelszó mentését, megtekintheti a hozzáférési kódot a replikációs berendezésen ezzel a lépéssel. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** parancsot az aktuális jelszó megtekintéséhez.
    - Most másolja ezt a jelszót a vágólapra, és mentse egy ideiglenes szövegfájlba a forrásként szolgáló virtuális gépeken.

### <a name="installation-guide-for-windows-aws-vms"></a>Telepítési útmutató Windows AWS-alapú virtuális gépekhez

1. Bontsa ki a telepítőfájl tartalmát egy helyi mappába (például C:\Temp) az AWS virtuális gépen a következőképpen:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. A mobilitási szolgáltatás telepítőjének futtatása:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Telepítési útmutató linuxos AWS-alapú virtuális gépekhez

1. Bontsa ki a telepítői csomag tartalmát egy helyi mappába (például/tmp/MobSvcInstaller) az AWS virtuális gépen, az alábbiak szerint:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Futtassa a telepítő parancsfájlt:
    ```
    sudo ./install -r MS -q
    ```  

3. Regisztrálja az ügynököt a replikációs berendezéssel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-aws-vms"></a>Az AWS-alapú virtuális gépek replikálásának engedélyezése

> [!NOTE]
> A portálon akár 10 virtuális gépet is hozzáadhat egyszerre a replikáláshoz. Ha egyszerre több virtuális gépet szeretne replikálni, 10 kötegben adhatja hozzá őket.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése** elemre, majd kattintson a **replikálás** gombra.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **replikálás** során > a **forrásoldali beállítások**  >  **a gépek virtualizálva?**, válassza a **nem virtualizált/egyéb** lehetőséget.
3. A helyszíni **készülék** területen válassza ki a beállított Azure Migrate berendezés nevét.
4. A **Process Server** lapon válassza ki a replikációs berendezés nevét. 
5. A **vendég hitelesítő adatai** területen válassza ki a [replikálási telepítő telepítése](#download-the-replication-appliance-installer) során korábban létrehozott dummy-fiókot a mobilitási szolgáltatás manuális telepítéséhez (a leküldéses telepítés nem támogatott). Ezután kattintson a **Tovább gombra: Virtual Machines**.   
 
    ![Replikálási beállítások](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. **Virtual Machines** az **áttelepítési beállítások importálása az értékelésből?** beállításnál hagyja meg az alapértelmezett **nem beállítást, az áttelepítési beállításokat manuálisan kell megadnia**.
7. Tekintse át az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: cél beállítások** elemre.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
9. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
10. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
    
11. A **lemez titkosítása típusnál** válassza a következő lehetőséget:
    - Titkosítás – a platform által felügyelt kulccsal
    - Titkosítás – az ügyfél által felügyelt kulccsal
    - Dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-vel való replikálásához létre kell [hoznia egy lemezes titkosítási](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) csoportot a cél erőforráscsoport alatt. A lemezes titkosítási készlet objektum Managed Diskst képez le egy olyan Key Vaultra, amely az SSE-hoz használandó CMK tartalmazza.
  
12. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Cél beállításai](./media/tutorial-migrate-vmware/target-settings.png)

13. A **számításban** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemezének típusát és rendelkezésre állási konfigurációját (ha az előző lépésben van kiválasztva). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben az ajánlott méret látható. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet.
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található.
    - **Rendelkezésre állási zóna**: Itt adhatja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási csoport**: adja meg a használni kívánt rendelkezésre állási készletet.

![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. A **lemezek** területen adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemez) az Azure-ban. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemez beállításai](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat a replikáció elindítása előtt bármikor frissítheti, **kezelheti** a  >  **replikáló gépeket**. A replikáció kezdete után a beállítások már nem módosíthatók.

## <a name="track-and-monitor-replication-status"></a>Replikáció állapotának nyomon követése és figyelése

- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre.
- Amikor a replikálás elindítása feladatainak végrehajtása sikeresen befejeződött, a virtuális gépek kezdeti replikációját megkezdik az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. Az AWS VM-lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.

A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése** elemre.  

![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. A tesztelési áttelepítés kifejezetten ajánlott, és lehetőséget biztosít a lehetséges problémák felderítésére, és a tényleges áttelepítés folytatása előtt kijavítani azokat. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el az egyes virtuális gépeket.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy befolyásolná az AWS virtuális gépek működését, és folytatja a replikálást.
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


## <a name="migrate-aws-vms"></a>AWS virtuális gépek migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti az AWS virtuális gépeket.

1. A Azure Migrate projekt > **kiszolgálók**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **kiszolgálók replikálása** elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **áttelepítés** során  >  **állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett áttelepítést**, válassza az **Igen**  >  **OK** lehetőséget.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladat állapotát úgy tekintheti meg, ha a portál oldal jobb felső sarkában található értesítési harang ikonra kattint, vagy a kiszolgáló áttelepítési eszközének feladatok lapjára kattint (az eszköz csempe > válassza a bal oldali menüben a feladatok lehetőséget).
5. A feladat befejeztével a virtuális gépet a Virtuális gépek oldalon tekintheti meg és kezelheti.

### <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása** elemre. Ez a következő műveleteket végzi el:
    - Leállítja az AWS virtuális gép replikálását.
    - Eltávolítja az AWS virtuális gépet a **replikálási kiszolgálók** száma Azure Migrate: kiszolgáló áttelepítése.
    - A virtuális gép replikációs állapotára vonatkozó információk tisztítása.
2. Telepítse a [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre. Az Azure VM Windows-ügynök előre telepítve van az áttelepítési folyamat során.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 




## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.



## <a name="troubleshooting--tips"></a>Hibaelhárítás/tippek

**Kérdés:** Nem látom az AWS virtuális gépet az áttelepítéshez használt kiszolgálók felderített listájában   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy az áttelepíteni kívánt virtuális gépre telepítve van a mobilitási ügynök, és hogy regisztrálva van a konfigurációs kiszolgálón. A hálózati beállítások és a tűzfalszabályok segítségével engedélyezze a hálózati elérési utat a replikációs berendezés és a forrás AWS virtuális gépek között.  

**Kérdés:** Hogyan arról, hogy a virtuális gép migrálása sikeres volt-e   
**Válasz:** Az áttelepítés után megtekintheti és kezelheti a virtuális gépet a Virtual Machines lapon. Kapcsolódjon az áttelepített virtuális géphez az ellenőrzéshez.  

**Kérdés:** Nem tudom importálni a virtuális gépeket a korábban létrehozott kiszolgáló-értékelési eredményekből való áttelepítéshez   
**Válasz:** Jelenleg nem támogatott az értékelés importálása ehhez a munkafolyamathoz. Megkerülő megoldásként exportálhatja az értékelést, majd manuálisan is kiválaszthatja a virtuális gép ajánlását a replikálás engedélyezése lépésben.
  
**Kérdés:** A következő hibaüzenet jelenik meg: "nem sikerült beolvasni a BIOS GUID azonosítóját" az AWS-es virtuális gépek felderítésére tett kísérlet során   
**Válasz:** A hitelesítéshez mindig használja a root logint, és ne használjon pszeudo-felhasználót. Tekintse át az AWS virtuális gépek támogatott operációs rendszereit is.  

**Kérdés:** A saját replikáció állapota nem halad előre   
**Válasz:** Ellenőrizze, hogy a replikációs berendezés megfelel-e a követelményeknek. Győződjön meg arról, hogy engedélyezte a szükséges portokat a 9443-es és HTTPS 443-es TCP-porton az adatátvitelhez. Győződjön meg arról, hogy az ugyanahhoz a projekthez kapcsolódó replikációs berendezésnek nincs elavult duplikált verziója.   

**Kérdés:** Nem tudom felderíteni az AWS-példányokat az Azure Migrate használatával a távoli Windows Management szolgáltatásból származó 504 HTTP-állapotkód miatt    
**Válasz:** Győződjön meg arról, hogy az Azure migráló készülékre vonatkozó követelmények és URL-hozzáférési igényeket tekinti át. Győződjön meg arról, hogy nincs proxy-beállítás a készülék regisztrációjának blokkolására.

**Kérdés:** Az AWS-beli virtuális gépek Azure-ba való migrálása előtt végre kell hajtani a módosításokat.   
**Válasz:** Előfordulhat, hogy a EC2 virtuális gépek Azure-ba való migrálása előtt el kell végeznie ezeket a módosításokat:

- Ha Cloud-init-t használ a virtuális gépek üzembe helyezéséhez, érdemes lehet letiltani a Cloud-init szolgáltatást a virtuális gépen az Azure-ba való replikálás előtt. A Cloud-init által a virtuális gépen végrehajtott kiépítési lépések talán AWS-specifikusak, és az Azure-ba való Migrálás után nem lesznek érvényesek. 
- Ha a virtuális gép egy PV virtuális gép (nem virtualizált), és nem HVM VM, előfordulhat, hogy nem tudja futtatni az Azure-ban, mert a para-virtualizált virtuális gépek egyéni rendszerindítási sorozatot használnak az AWS-ben. Előfordulhat, hogy az Azure-ba való áttelepítés előtt a PV-illesztőprogramok eltávolításával el tudja érni ezt a problémát.  
- Javasoljuk, hogy a végső áttelepítés előtt futtasson egy teszt-áttelepítést.  


**Kérdés:** Áttelepíthetem az Amazon Linux operációs rendszert futtató AWS virtuális gépeket  
**Válasz:** Az Amazon Linux rendszerű virtuális gépek nem telepíthetők át, mert az Amazon Linux operációs rendszer csak az AWS-t támogatja.
Az Amazon Linux rendszeren futó munkaterhelések áttelepítéséhez egy CentOS/RHEL virtuális gépet is üzembe helyezhet az Azure-ban, és áttelepítheti az AWS linuxos gépen futó munkaterhelést a megfelelő munkaterhelés-áttelepítési módszer használatával. Például a munkaterheléstől függően előfordulhat, hogy a munkaterhelés-specifikus eszközök az áttelepítést segítik – például a webkiszolgálók adatbázisai vagy központi telepítési eszközei esetében.

## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.