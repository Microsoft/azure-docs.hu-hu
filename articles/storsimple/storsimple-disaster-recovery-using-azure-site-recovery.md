---
title: StorSimple fájlmegosztás DR automatizálása a Azure Site Recoverykal | Microsoft Docs
description: Ismerteti a Microsoft Azure StorSimple Storage szolgáltatásban üzemeltetett fájlmegosztás vész-helyreállítási megoldásának létrehozásának lépéseit és ajánlott eljárásait.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: c6152d4b9ee28554efcb5b08b7a2d161a0723852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670904"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatikus vész-helyreállítási megoldás a StorSimple-on üzemeltetett fájlmegosztás Azure Site Recovery használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple egy hibrid felhőalapú tárolási megoldás, amely a fájlmegosztást gyakran társított strukturálatlan adatmennyiségek bonyolultságát kezeli. A StorSimple a felhőalapú tárolót használja a helyszíni megoldás kiterjesztéseként, és a helyszíni tárolók és a felhőalapú tárolók számára automatikusan tömöríti az adatszinteket. A helyi és a Felhőbeli pillanatképekkel rendelkező integrált adatvédelem szükségtelenné teszi a burjánzó tárolási infrastruktúra szükségességét.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) egy Azure-alapú szolgáltatás, amely vész-helyreállítási (Dr) képességeket biztosít a virtuális gépek replikálásának, feladatátvételének és helyreállításának összehangolása révén. Azure Site Recovery számos replikációs technológiát támogat a virtuális gépek és az alkalmazások magán-vagy nyilvános vagy üzemeltetett felhőbe való zökkenőmentes replikálásához, védetté tételéhez és zökkenőmentes feladatátvételéhez.

A Azure Site Recovery, a virtuális gépek replikálása és a StorSimple Cloud Snapshot funkcióinak használatával biztosíthatja a teljes fájlkiszolgáló-környezet védettségét. Megszakítás esetén egyetlen kattintással elvégezheti a fájlmegosztás online állapotba helyezését az Azure-ban mindössze néhány perc alatt.

Ez a dokumentum részletesen ismerteti, hogyan hozhat létre vész-helyreállítási megoldást a StorSimple tárolt fájlmegosztás számára, és hogyan hajthat végre tervezett, nem tervezett és feladatátvételi teszteket egy kattintással végzett helyreállítási terv használatával. Lényegében azt mutatja be, hogyan módosíthatja a helyreállítási tervet a Azure Site Recovery-tárolóban, hogy lehetővé váljon a StorSimple feladatátvétel a katasztrófa-forgatókönyvek esetén. Emellett leírja a támogatott konfigurációkat és előfeltételeket. Ez a dokumentum azt feltételezi, hogy ismeri a Azure Site Recovery és a StorSimple architektúrájának alapjait.

## <a name="supported-azure-site-recovery-deployment-options"></a>Támogatott Azure Site Recovery telepítési lehetőségek
Az ügyfelek a Hyper-V-n vagy VMware-en futó fizikai kiszolgálóként vagy virtuális gépekként (VM) telepíthetik a fájlkiszolgálók-t, majd létrehozhatnak fájlmegosztást a StorSimple-tárolóból kivésett kötetekből. A Azure Site Recovery fizikai és virtuális központi telepítéseket is el tud látni a másodlagos vagy az Azure-ba. Ez a dokumentum részletesen ismerteti az Azure-beli DR-megoldások helyreállítási helyét a Hyper-V-ben üzemeltetett fájlkiszolgáló virtuális gépen, valamint a StorSimple-tárolóban található fájlmegosztást. Egyéb forgatókönyvek, amelyekben a fájlkiszolgáló virtuális gép VMware virtuális gépen vagy fizikai gépen is megvalósítható.

## <a name="prerequisites"></a>Előfeltételek
A StorSimple-tárolón üzemeltetett fájlmegosztás Azure Site Recovery használó egykattintásos vész-helyreállítási megoldásának megvalósítása a következő előfeltételekkel rendelkezik:

   - Hyper-V-n vagy VMware-en vagy fizikai gépen üzemeltetett helyszíni Windows Server 2012 R2 fájlkiszolgáló virtuális gép
   - Helyszíni StorSimple az Azure StorSimple Managerrel regisztrálva
   - StorSimple Cloud Appliance létrehozva az Azure StorSimple Managerben. A készülék leállítási állapotban is tarthat.
   - A StorSimple tároló eszközön konfigurált köteteken tárolt fájlmegosztás
   - Microsoft Azure-előfizetésben létrehozott [Azure site Recovery Services](../site-recovery/hyper-v-vmm-azure-tutorial.md) -tároló

Továbbá, ha az Azure a helyreállítási hely, futtassa az [Azure Virtual Machine Readiness Assessment eszközt a virtuális](https://azure.microsoft.com/downloads/vm-readiness-assessment/) gépeken, és győződjön meg arról, hogy kompatibilisek az Azure-beli virtuális gépekkel és a Azure site Recovery szolgáltatásokkal.

A késéssel kapcsolatos problémák elkerüléséhez (ami magasabb költségekhez vezethet), győződjön meg arról, hogy a StorSimple Cloud Appliance, az Automation-fiók és a Storage-fiók (oka) t ugyanabban a régióban hozza létre.

## <a name="enable-dr-for-storsimple-file-shares"></a>DR StorSimple-fájlmegosztás engedélyezése
A helyszíni környezet minden összetevőjét védeni kell a teljes replikáció és helyreállítás lehetővé tételéhez. Ez a szakasz a következőket ismerteti:
    
   - A Active Directory és a DNS-replikáció beállítása (nem kötelező)
   - Azure Site Recovery használata a fájlkiszolgáló virtuális gép védelmének engedélyezéséhez
   - StorSimple-kötetek védelmének engedélyezése
   - A hálózat konfigurálása

### <a name="set-up-active-directory-and-dns-replication-optional"></a>A Active Directory és a DNS-replikáció beállítása (nem kötelező)
Ha a Active Directoryt és a DNS-t futtató gépeket szeretné védelemmel ellátni, hogy azok elérhetők legyenek a DR webhelyén, explicit módon meg kell védelemmel ellátnia őket (hogy a fájlkiszolgálók a hitelesítéssel való feladatátvétel után is elérhetők legyenek). Az ügyfél helyszíni környezetének összetettsége alapján két ajánlott lehetőség van.

#### <a name="option-1"></a>1\. lehetőség
Ha az ügyfél kis számú alkalmazással rendelkezik, a teljes helyszíni hely egyetlen tartományvezérlője, és a teljes helyen meghiúsul, akkor azt javasoljuk, hogy Azure Site Recovery replikáció használatával replikálja a tartományvezérlői gépet egy másodlagos helyre (ez a helyek közötti és a helyek közötti Azure-hoz is alkalmazható).

#### <a name="option-2"></a>2\. lehetőség
Ha az ügyfél nagy számú alkalmazással rendelkezik, Active Directory erdőt futtat, és egyszerre csak néhány alkalmazást hajt végre, akkor javasoljuk, hogy állítson be egy további tartományvezérlőt a DR-helyen (másodlagos vagy az Azure-ban).

A DR webhelyre való csatlakozáshoz szükséges útmutatásért tekintse meg a [Active Directory és a DNS AUTOMATIZÁLT Dr-megoldását Azure site Recovery használatával](../site-recovery/site-recovery-active-directory.md) . A dokumentum további részében feltételezzük, hogy a DR webhelyén elérhető egy tartományvezérlő.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery használata a fájlkiszolgáló virtuális gép védelmének engedélyezéséhez
Ehhez a lépéshez elő kell készítenie a helyszíni fájlkiszolgáló környezetét, létre kell hoznia és elő kell készítenie egy Azure Site Recovery-tárolót, és engedélyeznie kell a virtuális gép védelmét.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>A helyszíni fájlkiszolgáló környezetének előkészítése
1. Állítsa be a **felhasználói fiókok felügyeletét** , hogy **Soha ne jelentsen értesítést**. Erre azért van szükség, hogy az Azure Automation-parancsfájlok használatával az iSCSI-tárolókat a Azure Site Recovery általi feladatátvétel után lehessen összekapcsolni.
   
   1. Nyomja le a Windows billentyű + Q billentyűkombinációt, és keressen rá az **UAC** kifejezésre.  
   1. Válassza a **felhasználói fiókok vezérlési beállításainak módosítása** lehetőséget.  
   1. Húzza a sávot az aljára, és **Soha ne küldjön értesítést**.  
   1. Kattintson **az OK gombra** , majd válassza az **Igen** lehetőséget, ha a rendszer kéri.  
   
      ![Felhasználói fiókok vezérlésének beállításai](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Telepítse a virtuálisgép-ügynököt mindegyik fájlkiszolgáló virtuális GÉPRE. Erre azért van szükség, hogy Azure Automation-parancsfájlokat futtasson a feladatátvételen áthelyezett virtuális gépeken.
   
   1. [Töltse le az ügynököt](https://aka.ms/vmagentwin) a következőre: `C:\\Users\\<username>\\Downloads` .
   1. Nyissa meg a Windows PowerShellt rendszergazdai módban (Futtatás rendszergazdaként), majd írja be a következő parancsot a letöltési hely eléréséhez:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > A fájl neve a verziótól függően változhat.
      
1. Kattintson a **Tovább** gombra.
1. Fogadja el a szerződés **feltételeit** , majd kattintson a **tovább** gombra.
1. Kattintson a **Finish** (Befejezés) gombra.
1. Fájlmegosztás létrehozása StorSimple-tárolóból kivésett kötetek használatával. További információ: [az StorSimple Manager szolgáltatás használata kötetek kezelésére](./index.yml).
   
   1. A helyszíni virtuális gépeken nyomja le a Windows billentyű + Q billentyűkombinációt, és keressen rá az **iSCSI** kifejezésre.
   1. Válasszon **iSCSI-kezdeményezőt**.
   1. Válassza a **konfiguráció** fület, és másolja a kezdeményező nevét.
   1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
   1. Válassza a **StorSimple** lapot, majd válassza ki a fizikai eszközt tartalmazó StorSimple Manager szolgáltatást.
   1. Hozzon létre mennyiségi tároló (ka) t, majd hozzon létre köteteket. (Ezek a kötetek a fájlkiszolgáló virtuális gépeken található fájlmegosztás (ok) ra vonatkoznak). Másolja a kezdeményező nevét, és adjon meg egy megfelelő nevet a Access Control rekordok számára a kötetek létrehozásakor.
   1. Válassza a **Konfigurálás** lapot, és jegyezze fel az eszköz IP-címét.
   1. A helyszíni virtuális gépeken nyissa meg újra az **iSCSI-kezdeményezőt** , és adja meg az IP-címet a gyors kapcsolódás szakaszban. Kattintson a **gyors kapcsolódás** lehetőségre (az eszköznek most már csatlakoztatva kell lennie).
   1. Nyissa meg a Azure Portal, és válassza a **kötetek és eszközök** lapot. Kattintson az **automatikus konfigurálás** elemre. A létrehozott kötetnek meg kell jelennie.
   1. A portálon válassza az **eszközök** fület, majd válassza az **új virtuális eszköz létrehozása lehetőséget.** (Feladatátvétel esetén ez a virtuális eszköz lesz használatban). Ezt az új virtuális eszközt offline állapotban is tárolhatja, hogy elkerülje a további költségeket. A virtuális eszköz offline állapotba helyezéséhez nyissa meg a portál **Virtual Machines** szakaszát, és állítsa le.
   1. Térjen vissza a helyszíni virtuális gépekhez, és nyissa meg a Lemezkezelés szolgáltatást (nyomja meg a Windows billentyű + X billentyűkombinációt, és válassza a **Lemezkezelés** lehetőséget).
   1. A rendszer néhány további lemezt is észrevesz (a létrehozott kötetek számától függően). Kattintson a jobb gombbal az elsőre, válassza a **lemez inicializálása** lehetőséget, majd kattintson az **OK gombra**. Kattintson a jobb gombbal a le nem **foglalt** szakaszra, válassza az **új egyszerű kötet** lehetőséget, rendeljen hozzá egy meghajtóbetűjelet, és fejezze be a varázslót.
   1. Ismételje meg az l lépést az összes lemez esetében. Most már megtekintheti a **számítógép** összes lemezét a Windows Intézőben.
   1. A fájl-és tárolási szolgáltatások szerepkör használatával fájlmegosztás hozható létre ezeken a köteteken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Azure Site Recovery-tároló létrehozása és előkészítése
A fájlkiszolgáló virtuális gép védelmének megkezdése előtt tekintse meg a [Azure site Recovery dokumentációját](../site-recovery/index.yml) Azure site Recovery.

#### <a name="to-enable-protection"></a>A védelem engedélyezése
1. Válassza le az iSCSI-tároló (ka) t a Azure Site Recovery segítségével védelemmel ellátni kívánt helyszíni virtuális gépekről:
   
   1. Nyomja le a Windows billentyű + Q billentyűkombinációt, és keressen rá az **iSCSI** kifejezésre.
   1. Válassza az **iSCSI-kezdeményező beállítása** lehetőséget.
   1. Válassza le a korábban csatlakoztatott StorSimple eszközt. Azt is megteheti, hogy néhány percen belül kikapcsolja a kiszolgálót a védelem engedélyezésekor.
      
   > [!NOTE]
   > Ez azt eredményezi, hogy a fájlmegosztás átmenetileg elérhetetlenné válik.
   
1. [Engedélyezze a Fájlkiszolgálói virtuális gép virtuálisgép-védelmét](../site-recovery/hyper-v-azure-tutorial.md) a Azure site Recovery portálról.
1. A kezdeti szinkronizálás megkezdésekor újra csatlakozhat a célhelyhez. Nyissa meg az iSCSI-kezdeményezőt, válassza ki a StorSimple eszközt, és kattintson a **Kapcsolódás** lehetőségre.
1. Ha a szinkronizálás befejeződött, és a virtuális gép állapota **védett**, válassza ki a virtuális gépet, válassza a **configure (Konfigurálás** ) lapot, és ennek megfelelően frissítse a virtuális gép hálózatát (ez az a hálózat, amelyre a feladatátvételt végző virtuális gép (ek) a része lesz). Ha a hálózat nem jelenik meg, az azt jelenti, hogy a szinkronizálás továbbra is folyamatban van.

### <a name="enable-protection-of-storsimple-volumes"></a>StorSimple-kötetek védelmének engedélyezése
Ha nem jelölte be a **kötet alapértelmezett biztonsági mentésének engedélyezése** beállítást a StorSimple kötetek esetében, ugorjon a **biztonsági mentési házirendek** elemre a StorSimple Manager szolgáltatásban, és hozzon létre egy megfelelő biztonsági mentési szabályzatot az összes kötethez. Azt javasoljuk, hogy állítsa be a biztonsági másolatok gyakoriságát arra a helyreállítási pontra (RPO), amelyet az alkalmazáshoz szeretne látni.

### <a name="configure-the-network"></a>A hálózat konfigurálása
A fájlkiszolgáló virtuális gép esetében konfigurálja a hálózati beállításokat Azure Site Recovery, hogy a virtuálisgép-hálózatok a megfelelő DR-hálózathoz legyenek csatlakoztatva a feladatátvételt követően.

A virtuális gépet a **replikált elemek** lapon választhatja ki a hálózati beállítások konfigurálásához, ahogy az alábbi ábrán is látható.

![Számítás és hálózat](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
Helyreállítási tervet is létrehozhat az ASR-ben a fájlmegosztás feladatátvételi folyamatának automatizálásához. Ha megszakad a probléma, néhány perc alatt megoszthatja a fájlmegosztást, mindössze egyetlen kattintással. Az automatizálás engedélyezéséhez szüksége lesz egy Azure Automation-fiókra.

#### <a name="to-create-an-automation-account"></a>Automation-fiók létrehozása
1. Lépjen a Azure Portal &gt; **Automation** szakaszra.
1. Kattintson a **+ Hozzáadás** gombra, és megnyílik az alábbi panel.
   
   ![Automation-fiók hozzáadása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Név – adjon meg egy új Automation-fiókot
   - Előfizetés – előfizetés kiválasztása
   - Erőforráscsoport – új csoport létrehozása/meglévő erőforráscsoport kiválasztása
   - Hely – válassza ki a helyet, tartsa meg ugyanabban a földrajzi helyen/régióban, ahol a StorSimple Cloud Appliance és a Storage-fiókokat létrehozták.
   - Azure-beli futtató fiók létrehozása – válassza az **Igen** lehetőséget.
   
1. Nyissa meg az Automation-fiókot, és kattintson a **runbookok** &gt; **tallózási** katalógus lehetőségre az összes szükséges runbookok az Automation-fiókba való importálásához.
1. Adja hozzá a következő runbookok a vész- **helyreállítási** címke megkeresésével a gyűjteményben:
   
   - StorSimple-kötetek tisztítása a feladatátvételi teszt után (TFO)
   - Feladatátvevő StorSimple mennyiségi tárolói
   - Kötetek csatlakoztatása a StorSimple-eszközön a feladatátvétel után
   - Egyéni szkriptek bővítményének eltávolítása az Azure-beli virtuális gépen
   - StorSimple virtuális készülék elindítása
   
      ![Tallózás a katalógusban](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Tegye közzé az összes parancsfájlt úgy, hogy kiválasztja a runbook az Automation-fiókban, és kattintson a közzététel **szerkesztése** lehetőségre, &gt;  majd az **Igen** gombra az ellenőrző üzenethez. A lépés után a **runbookok** lap a következőképpen fog megjelenni:
   
   ![Runbookok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Az Automation-fiókban kattintson a **változók** &gt; **változó hozzáadása** elemre, és adja hozzá a következő változókat. Dönthet úgy is, hogy titkosítja ezeket az eszközöket. Ezek a változók helyreállítási tervre vonatkoznak. Ha a helyreállítási terv, amelyet a következő lépésben fog létrehozni, a név TestPlan, akkor a változók TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName és így tovább.

   - **BaseUrl**: a Resource Manager URL-címe az Azure-felhőhöz. Get **-AzEnvironment | Select-Object name, ResourceManagerUrl** parancsmag használata.
   - _RecoveryPlanName_**-ResourceGroupName**: a StorSimple erőforrással rendelkező Resource Manager-csoport.
   - _RecoveryPlanName_**-ManagerName**: a StorSimple-eszközt tartalmazó StorSimple-erőforrás.
   - _RecoveryPlanName_**-DeviceName**: a feladatátvételt igénylő StorSimple-eszköz.
   - _RecoveryPlanName_**-DeviceIpAddress**: az eszköz IP-címe (ez a StorSimple Eszközkezelő szakasz  &gt; **Beállítások** &gt; **hálózati** &gt; **DNS-beállítások** csoportjának eszközök lapján található).
   - _RecoveryPlanName_**-VolumeContainers**: az eszközön található, vesszővel tagolt mennyiségi tárolók, amelyeknek feladatátvételre van szükségük; például: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: az a StorSimple Cloud Appliance, amelyen a tárolók feladatátvétele történik.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: a cél eszköz IP-címe (ez a **virtuális gép** szakasz &gt; **Beállítások** csoport &gt; **hálózatkezelés** lapján található).
   - _RecoveryPlanName_**-StorageAccountName**: a Storage-fiók neve, amelyben a parancsfájlt (amelyet a FELADATÁTVÉTELEN átadott virtuális gépen futtatni kell) tárolni fogja a rendszer. Ez lehet bármely olyan Storage-fiók, amely rendelkezik egy szóközzel a parancsfájl ideiglenes tárolásához.
   - _RecoveryPlanName_**-StorageAccountKey**: a fenti Storage-fiók elérési kulcsa.
   - _RecoveryPlanName_**-VMGUIDS**: a virtuális gép védelme során a Azure site Recovery minden virtuális gépet egy egyedi azonosítóval rendel hozzá, amely megadja a feladatátvételi virtuális gép részleteit. A VMGUID beszerzéséhez válassza a **Recovery Services** lapot, majd kattintson a **védett elemek** &gt; **védelmi csoportok** &gt; **gépek** &gt; **tulajdonságai** elemre. Ha több virtuális géppel rendelkezik, adja hozzá a GUID azonosítókat vesszővel tagolt karakterláncként.

     Ha például a helyreállítási terv neve fileServerpredayRP, akkor a **változók**, **kapcsolatok** és **tanúsítványok** lap a következőképpen jelenik meg az összes eszköz hozzáadása után.

      ![Objektumok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Töltse fel az StorSimple 8000 Series Runbook modult az Automation-fiókba. Modul hozzáadásához kövesse az alábbi lépéseket:
   
   1. Nyissa meg a PowerShellt, hozzon létre egy új mappát & módosítsa a könyvtárat a mappába.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Töltse le a nuget CLI-t a 1. lépés azonos mappájába.
      A nuget.exe különböző verziói a nuget- [letöltéseken](https://www.nuget.org/downloads)érhetők el. Minden letöltési hivatkozás közvetlenül egy. exe-fájlra mutat, ezért a jobb gombbal kattintson a fájlra, és mentse a fájlt a böngészőből, és ne futtassa azt a böngészőben.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. A függő SDK letöltése
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Hozzon létre egy Azure Automation Runbook modult a StorSimple 8000 sorozatú eszközök felügyeletéhez. Az alábbi parancsokkal hozzon létre egy Automation-modul ZIP-fájlját.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importálja a fenti lépésben létrehozott Azure Automation modul ZIP-fájlját (Microsoft.Azure.Management.StorSimple8000Series.zip). Ezt az Automation-fiók kiválasztásával végezheti el, kattintson a megosztott erőforrások területen található **modulok** elemre, majd kattintson **a modul hozzáadása** lehetőségre.
   
   Az StorSimple 8000 Series modul importálása után a **modulok** lap a következőképpen jelenik meg:
   
      ![Modulok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Nyissa meg a **Recovery Services** szakaszt, és válassza ki a korábban létrehozott Azure site Recovery-tárolót.
1. Válassza ki a **helyreállítási tervek (site Recovery)** lehetőséget a **kezelés** csoportban, és hozzon létre egy új helyreállítási tervet a következőképpen:
   
   - Kattintson a **+ terv helyreállítása** gombra, és megnyílik az alábbi panel.
      
      ![Helyreállítási terv létrehozása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Adja meg a helyreállítási terv nevét, válassza a forrás, a cél & a telepítési modell értékei elemet.
   
   - Válassza ki a helyreállítási tervbe felvenni kívánt védelmi csoportból a virtuális gépeket, majd kattintson **az OK** gombra.
   
   - Válassza ki a korábban létrehozott helyreállítási tervet, kattintson a **Testreszabás** gombra a helyreállítási terv testreszabási nézetének megnyitásához.
   
   - Kattintson a jobb gombbal az **összes csoport leállítása** elemre, majd kattintson az **előzetes művelet hozzáadása** parancsra.
   
   - Megnyitja a művelet beszúrása panelt, adjon meg egy nevet, válassza ki az **elsődleges oldal** lehetőséget a Run (Futtatás) lehetőségnél, válassza ki az Automation-fiók elemet (amelyben hozzáadta a runbookok), majd válassza ki a **feladatátvétel-StorSimple-Volume-containers** runbook.
   
   - Kattintson a jobb gombbal az **1. csoportra: indítsa el** , majd kattintson a **védett elemek hozzáadása** lehetőségre, majd válassza ki a helyreállítási tervben védeni kívánt virtuális gépeket, majd kattintson **az OK** gombra. Nem kötelező, ha már kiválasztott virtuális gépek.
   
   - Kattintson a jobb gombbal az **1. csoportra: kezdje el** , majd kattintson a **művelet közzététele** lehetőségre, majd adja hozzá a következő parancsfájlokat:  
      
      - Start-StorSimple-Virtual-Appliance runbook  
      - Feladatátvétel – StorSimple – mennyiségi tárolók runbook  
      - Csatlakoztatási kötetek – feladatátvétel utáni runbook  
      - A-Custom-script-Extension runbook eltávolítása  
        
   - Adjon hozzá egy manuális műveletet a fenti 4 parancsfájl után ugyanabban az **1. csoport: utólagos lépések** szakaszban. Ez a művelet az a pont, ahol ellenőrizheti, hogy minden megfelelően működik-e. Ezt a műveletet csak a feladatátvételi teszt részeként kell hozzáadni (tehát csak a **feladatátvételi teszt** jelölőnégyzetet jelölje be).
    
   - A manuális művelet után adja hozzá a **karbantartási** parancsfájlt ugyanazzal az eljárással, mint amelyet a másik runbookok használt. **Mentse** a helyreállítási tervet.
    
   > [!NOTE]
   > Feladatátvételi teszt futtatásakor ellenőrizze, hogy mindent a manuális művelet lépésben kell-e végrehajtani, mert a StorSimple klónozott kötetek a manuális művelet befejezése után törlődnek a tisztítás részeként.
       
      ![Helyreállítási terv](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása
A feladatátvételi teszt során Active Directoryra vonatkozó megfontolásokért tekintse meg a [Active Directory Dr megoldás](../site-recovery/site-recovery-active-directory.md) kiegészítő útmutatóját. A helyszíni telepítést a feladatátvételi teszt bekövetkeztekor egyáltalán nem zavarja. A helyszíni virtuális géphez csatolt StorSimple-kötetek klónozása az Azure-StorSimple Cloud Appliance. A virtuális gépek tesztelési célra való üzembe helyezhetők az Azure-ban, és a klónozott kötetek a virtuális géphez vannak csatlakoztatva.

#### <a name="to-perform-the-test-failover"></a>A feladatátvételi teszt végrehajtása
1. A Azure Portal válassza ki a Site Recovery-tárolót.
1. Kattintson a fájlkiszolgáló virtuális géphez létrehozott helyreállítási tervre.
1. Kattintson a **feladatátvételi teszt** elemre.
1. Válassza ki azt az Azure-beli virtuális hálózatot, amelyhez a feladatátvételt követően az Azure-beli virtuális gépek csatlakozni fognak.
   
   ![Feladatátvétel indítása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat nyomon követéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait vagy a tár **feladatátvételi teszt feladatát** a tár neve &gt; **feladatok** &gt; **site Recovery feladatok** területen.
1. A feladatátvétel befejeződése után a replika Azure-beli gép megjelenik a Azure Portal &gt; **Virtual Machines**. Elvégezheti az érvényességét.
1. Az érvényesítések elvégzése után kattintson az **érvényesítések kész** elemre. Ezzel eltávolítja a StorSimple köteteket, és leállítja a StorSimple Cloud Appliance.
1. Ha elkészült, kattintson a helyreállítási terv **feladatátvételi teszt törlése** elemére. A Jegyzetek területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ezzel a művelettel törli a feladatátvételi teszt során létrehozott virtuális gépet.

## <a name="perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtása
   A tervezett feladatátvétel során a helyszíni fájlkiszolgáló virtuális gép szabályosan leáll, és a StorSimple-eszközön lévő kötetek Felhőbeli biztonsági mentési pillanatképe készül. A StorSimple köteteket a rendszer átadja a virtuális eszköznek, a replika virtuális gép üzembe kerül az Azure-ban, és a kötetek a virtuális GÉPHEZ vannak csatlakoztatva.

#### <a name="to-perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtása
1. A Azure Portal válassza a fájlkiszolgáló virtuális géphez létrehozott **Recovery Services** - &gt; tároló **helyreállítási terveket (site Recovery)** &gt; **recoveryplan_name** .
1. A helyreállítási terv panelen kattintson a **további** &gt; **tervezett feladatátvétel** lehetőségre.  

   ![Képernyőkép a tervezett feladatátvételi és feladatátvételi lehetőségekről.](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. A **tervezett feladatátvétel megerősítése** panelen válassza ki a forrás-és célhelyeket, válassza a célként kijelölt hálózat lehetőséget, majd kattintson a pipa ikonra ✓ a feladatátvételi folyamat elindításához.
1. A replika virtuális gépek létrehozása után a rendszer függőben lévő állapotban van. A feladatátvétel elvégzéséhez kattintson a **véglegesít** gombra.
1. A replikáció befejezése után a virtuális gépek a másodlagos helyen kezdődnek.

## <a name="perform-a-failover"></a>Feladatátvétel végrehajtása
A nem tervezett feladatátvétel során a rendszer átadja a StorSimple köteteket a virtuális eszköznek, a replika virtuális gép üzembe kerül az Azure-ban, és a kötetek a virtuális GÉPHEZ vannak csatlakoztatva.

#### <a name="to-perform-a-failover"></a>Feladatátvétel elvégzése
1. A Azure Portal válassza a fájlkiszolgáló virtuális géphez létrehozott **Recovery Services** - &gt; tároló **helyreállítási terveket (site Recovery)** &gt; **recoveryplan_name** .
1. A helyreállítási terv panelen kattintson a **további** &gt; **feladatátvétel** lehetőségre.  
1. A **feladatátvétel megerősítése** panelen válassza ki a forrás és a cél helyét.
1. Válassza a **virtuális gépek leállítása lehetőséget, és szinkronizálja a legfrissebb adatokat** annak megadásához, hogy site Recovery megpróbálja leállítani a védett virtuális gépet, és szinkronizálja az adatokat, hogy az adat legújabb verziójának feladatátvétele megtörténjen.
1. A feladatátvételt követően a virtuális gépek véglegesítve függő állapotban vannak. A feladatátvétel elvégzéséhez kattintson a **véglegesít** gombra.


## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása
A feladat-visszavétel során a rendszer a biztonsági másolat készítése után visszaadja a StorSimple a fizikai eszközre.

#### <a name="to-perform-a-failback"></a>Feladat-visszavétel végrehajtása
1. A Azure Portal válassza a fájlkiszolgáló virtuális géphez létrehozott **Recovery Services** - &gt; tároló **helyreállítási terveket (site Recovery)** &gt; **recoveryplan_name** .
1. A helyreállítási terv panelen kattintson a **további** &gt; **tervezett feladatátvétel** lehetőségre.  
1. Válassza ki a forrás-és célhelyeket, válassza ki a megfelelő adatszinkronizálási és virtuálisgép-létrehozási beállításokat.
1. A feladat-visszavételi folyamat elindításához kattintson **az OK** gombra.
   
   ![Feladat-visszavétel indítása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitás megtervezése és készültségi felmérése
#### <a name="hyper-v-site"></a>Hyper-V-hely
A [felhasználói kapacitás Planner eszköz](https://www.microsoft.com/download/details.aspx?id=39057) használatával megtervezheti a Hyper-V replika környezetének kiszolgáló-, tároló-és hálózati infrastruktúráját.

#### <a name="azure"></a>Azure
Az [Azure Virtual Machine Readiness Assessment eszközt](https://azure.microsoft.com/downloads/vm-readiness-assessment/) futtathatja virtuális gépeken, így biztosítva, hogy kompatibilisek legyenek az Azure-beli virtuális gépekkel és a Azure site Recovery szolgáltatásokkal. A Readiness Assessment eszköz ellenőrzi a virtuális gépek konfigurációit, és figyelmezteti, ha a konfigurációk nem kompatibilisek az Azure-val. Például figyelmeztetést ad ki, ha a C: meghajtó mérete meghaladja a 127 GB-ot.

A kapacitás megtervezése legalább két fontos folyamatból tevődik fel:

   - Helyszíni Hyper-V virtuális gépek hozzárendelése Azure-beli virtuálisgép-méretekhez (például A6, A7, A8 és A9).
   - A szükséges internetes sávszélesség meghatározása.

## <a name="limitations"></a>Korlátozások
- Jelenleg csak 1 StorSimple-eszköz feladatátvétele lehetséges (egyetlen StorSimple Cloud Appliance). A több StorSimple-eszközt megölelő fájlkiszolgáló forgatókönyve még nem támogatott.
- Ha hibaüzenet jelenik meg a virtuális gép védelmének engedélyezése közben, győződjön meg arról, hogy leválasztotta az iSCSI-tárolókat.
- A rendszer a mennyiségi tárolók között átfedésben lévő biztonsági mentési házirendek miatt csoportosított összes kötetet együttesen adja át.
- A kiválasztott mennyiségi tárolókban lévő összes kötet feladatátvétele történik.
- A legfeljebb 64 TB-nál nagyobb kötetek feladatátvétele nem lehetséges, mert az egyetlen StorSimple Cloud Appliance maximális kapacitása 64 TB.
- Ha a tervezett/nem tervezett feladatátvétel meghiúsul, és a virtuális gépek az Azure-ban jönnek létre, akkor ne törölje a virtuális gépeket. Ehelyett végezzen feladat-visszavételt. Ha törli a virtuális gépeket, akkor a helyszíni virtuális gépek nem kapcsolhatók be újra.
- Ha a feladatátvételt követően nem tudja megtekinteni a köteteket, lépjen a virtuális gépekre, nyissa meg a Lemezkezelés szolgáltatást, ellenőrizze újra a lemezeket, majd kapcsolja online állapotba.
- Bizonyos esetekben a DR hely meghajtóbetűjelei eltérőek lehetnek a helyszíni levelektől. Ha ez történik, a feladatátvétel befejezése után manuálisan kell kijavítania a problémát.
- Feladatátvételi feladatok időtúllépése: a StorSimple parancsfájl túllépi az időkorlátot, ha a mennyiségi tárolók feladatátvétele több időt vesz igénybe, mint a parancsfájl Azure Site Recovery korlátja (jelenleg 120 perc).
- Biztonsági mentési feladatok időtúllépése: a StorSimple parancsfájl túllépi az időkorlátot, ha a kötetek biztonsági mentése több időt vesz igénybe, mint a parancsfájl Azure Site Recovery korlátja (jelenleg 120 perc).
   
  > [!IMPORTANT]
  > Futtassa manuálisan a biztonsági másolatot a Azure Portal, majd futtassa újra a helyreállítási tervet.
   
- Klónozási feladatok időtúllépése: a StorSimple parancsfájl túllépi az időkorlátot, ha a kötetek klónozása több időt vesz igénybe, mint a parancsfájl Azure Site Recovery korlátja (jelenleg 120 perc).
- Időszinkronizálási hiba: a StorSimple parancsfájlokban hibák léptek fel, mondván, hogy a biztonsági mentések sikertelenek voltak, noha a biztonsági mentés sikeres volt a portálon. Ennek lehetséges oka az lehet, hogy a StorSimple berendezés ideje nem szinkronizálható az időzóna aktuális idejével.
   
  > [!IMPORTANT]
  > Szinkronizálja a berendezés idejét az időzóna aktuális időpontjával.
   
- Készülék feladatátvételi hibája: a StorSimple parancsfájl meghiúsulhat, ha a helyreállítási terv futtatásakor a készülék feladatátvételt hajt végre.
   
  > [!IMPORTANT]
  > Futtassa újra a helyreállítási tervet a készülék feladatátvételének befejeződése után.


## <a name="summary"></a>Összefoglalás
A Azure Site Recovery használatával létrehozhat egy teljes automatikus vész-helyreállítási tervet egy olyan fájlkiszolgáló virtuális géphez, amely a StorSimple-tárolón üzemeltetett fájlmegosztást tárolja. A feladatátvételt másodpercek alatt elindíthatja bárhonnan, megszakítás esetén, és néhány percen belül üzembe helyezheti és futtathatja az alkalmazást.