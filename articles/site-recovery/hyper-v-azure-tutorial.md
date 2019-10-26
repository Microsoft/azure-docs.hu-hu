---
title: Vészhelyzeti helyreállítás beállítása helyszíni Hyper-V virtuális gépekhez (VMM nélkül) az Azure-ba a Site Recovery használatával | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek (VMM nélkül) az Azure-ba történő vész-helyreállítását Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 446c870ba60d7931fafb9f9b1c7e8fc017f60e4d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933869"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vészhelyreállítás beállítása a helyszíni Hyper-V virtuális gépekhez az Azure-ba

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni gépek és az Azure-beli virtuális gépek (VM-EK) replikációjának, feladatátvételének és feladat-visszavételének kezelésével és koordinálásával járul hozzá a vész-helyreállítási stratégiához.

Ez az oktatóanyag egy sorozat harmadik része. Bemutatja, hogyan állíthatja be a helyszíni Hyper-V virtuális gépek vész-helyreállítását az Azure-ba. Ez az oktatóanyag olyan Hyper-V virtuális gépeket alkalmaz, amelyeket nem Microsoft System Center Virtual Machine Manager (VMM) felügyel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * Állítsa be a forrás replikációs környezetet, beleértve a helyszíni Site Recovery összetevőket és a cél replikációs környezetet.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a [site Recovery dokumentációjának](https://docs.microsoft.com/azure/site-recovery) **útmutatók** szakaszának cikkeit.

> [!WARNING]
> Vegye figyelembe, hogy a SCVMM-konfiguráció fiókhoz való használatának ASR-támogatása hamarosan elavulttá válik, ezért javasoljuk, hogy a továbblépés előtt olvassa el az [elavult](scvmm-site-recovery-deprecation.md) adatokat.


## <a name="before-you-begin"></a>Előzetes teendők

Ez az oktatóanyag egy sorozat harmadik része. Feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagokban:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A Azure Portal lépjen a **Recovery Services** -tárolók elemre, és válassza ki a tárolót. Elkészítettük a tár **ContosoVMVault** az előző oktatóanyagban.
2. A **első lépések**területen válassza a **site Recovery**lehetőséget, majd válassza az **infrastruktúra előkészítése**lehetőséget.
3. A **védelmi cél** > **Hol találhatók a gépek?** területen válassza **a**helyszíni lehetőséget.
4. A **hová szeretné replikálni a gépeket?** területen válassza **Az Azure**lehetőséget.
5. A-ben **a gépek virtualizáltak?** , válassza az **Igen, a Hyper-V**lehetőséget.
6. A-ben a **System Center VMM használatával felügyelheti a Hyper-V-gazdagépeket?** válassza a **nem**lehetőséget.
7. Kattintson az **OK** gombra.

    ![Replikációs cél](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. Ha nagyméretű központi telepítést tervez, a **központi telepítés tervezése**során töltse le a Hyper-V Deployment Planner a lapon található hivatkozásra kattintva. [További](hyper-v-deployment-planner-overview.md) információ a Hyper-V üzembe helyezésének megtervezéséről.
2. Ebben az oktatóanyagban nincs szükségünk a Deployment Plannerra. A-ben **befejezte az üzembe helyezés megtervezését?** területen válassza a **később**lehetőséget, majd kattintson **az OK gombra**.

    ![Az üzembe helyezés megtervezése](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forrás-környezet beállításához létre kell hoznia egy Hyper-V helyet, és hozzá kell adnia a helyhez a replikálni kívánt virtuális gépeket tartalmazó Hyper-V-gazdagépeket. Ezután letöltheti és telepítheti az Azure Site Recovery-szolgáltatót és az Azure Recovery Services-ügynököt minden gazdagépre, és regisztrálja a Hyper-V-helyet a tárolóban.

1. Az **infrastruktúra előkészítése**területen válassza a **forrás**lehetőséget.
2. A **forrás előkészítése**területen válassza a **+ Hyper-V-hely**lehetőséget.
3. A **Hyper-V-hely létrehozása**lapon adja meg a hely nevét. **ContosoHyperVSite**használunk.

    ![Hyper-V-hely](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. A hely létrehozása után a **forrás előkészítése** > **1. lépés: válassza a Hyper-V hely lehetőséget**, majd válassza ki a létrehozott helyet.
5. Válassza a **+ Hyper-V kiszolgáló**lehetőséget.

    ![Hyper-V-kiszolgáló](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
7. Töltse le a tároló regisztrációs kulcsát. A szolgáltató telepítéséhez erre a kulcsra van szükség. A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató és regisztrációs kulcs letöltése](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Szolgáltató telepítése

Telepítse a letöltött telepítőfájlt (AzureSiteRecoveryProvider. exe) minden olyan Hyper-V-gazdagépre, amelyet hozzá szeretne adni a Hyper-V-helyhez. A telepítő telepíti a Azure Site Recovery-szolgáltatót és Recovery Services ügynököt minden Hyper-V-gazdagépen.

1. Futtassa a telepítőfájlt.
2. Az Azure Site Recovery szolgáltató telepítése varázslóban a **Microsoft Update** lehetőséggel engedélyezze a Microsoft Update használatát a szolgáltatófrissítések automatikus kereséséhez.
3. A **telepítés**területen fogadja el a szolgáltató és az ügynök alapértelmezett telepítési helyét, majd válassza a **telepítés**lehetőséget.
4. A telepítés után a Microsoft Azure Site Recovery regisztrációs varázsló > tár **beállításai**területen válassza a **Tallózás**lehetőséget, majd a **kulcsfájl**területen válassza ki a letöltött tároló-kulcsot.
5. Adja meg az Azure Site Recovery-előfizetést, a tároló nevét (**ContosoVMVault**) és a Hyper-V helyet (**ContosoHyperVSite**), amelyhez a Hyper-V kiszolgáló tartozik.
6. A **Proxybeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget.
7. A **regisztráció**során, miután a kiszolgáló regisztrálva van a tárolóban, válassza a **Befejezés**lehetőséget.

Az Azure Site Recovery lekéri a metaadatokat a Hyper-V kiszolgálóról, és a kiszolgáló megjelenik a **Site Recovery-infrastruktúra** > **Hyper-V gazdagépek** listában. Ez a folyamat akár 30 percet is igénybe vehet.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>A szolgáltató telepítése Hyper-V Core kiszolgálóra

Ha a Hyper-V Core-kiszolgálót futtatja, töltse le a telepítőfájlt, és kövesse az alábbi lépéseket:

1. Bontsa ki a fájlokat a AzureSiteRecoveryProvider. exe fájlból egy helyi könyvtárba a következő parancs futtatásával:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Futtassa az `.\setupdr.exe /i` parancsot. Az eredmények naplózása a%Programdata%\ASRLogs\DRASetupWizard.log.

3. Regisztrálja a kiszolgálót a következő parancs futtatásával:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Cél erőforrások kiválasztása és ellenőrzése:

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget.
2. Válassza ki az előfizetést és az erőforráscsoport **ContosoRG** , amelyben az Azure-beli virtuális gépeket a feladatátvételt követően létrehozza a rendszer.
3. Válassza ki a **Resource Manager**-alapú üzemi modellt.

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza az **infrastruktúra előkészítése** > **replikációs beállítások** >  **+ Létrehozás és hozzárendelés**lehetőséget.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. **ContosoReplicationPolicy**használunk.
3. Ebben az oktatóanyagban az alapértelmezett beállítások maradnak:
    - A **Másolás gyakorisága** azt jelzi, hogy a rendszer milyen gyakran replikálja a különbözeti adatváltozásokat (a kezdeti replikálás után) Az alapértelmezett gyakoriság 5 percenként történik.
    - A **helyreállítási pont megőrzése** azt jelzi, hogy a helyreállítási pontok két óráig megmaradnak.
    - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
    - A **kezdeti replikálás kezdő időpontja** azt jelzi, hogy a kezdeti replikáció azonnal elindul.
4. A szabályzat létrehozása után kattintson **az OK gombra**. Új szabályzat létrehozásakor a rendszer automatikusan hozzárendeli a megadott Hyper-V-helyhez. Az oktatóanyagban ez a **ContosoHyperVSite**.

    ![Replikációs szabályzat](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Az **alkalmazás replikálása**területen válassza a **forrás**lehetőséget.
2. A **Forrás** területen, válassza ki a **ContosoHyperVSite** helyet. Ezután válassza az **OK** lehetőséget.
3. A **cél**területen ellenőrizze a célt (Azure), a tároló-előfizetést és a **Resource Manager** -alapú üzemi modellt.
4. Ha az oktatóanyag beállításait használja, válassza ki az előző oktatóanyagban létrehozott **contosovmsacct1910171607** Storage-fiókot a replikált adatkezeléshez. Válassza ki azt a **ContosoASRnet** hálózatot is, amelyben az Azure-beli virtuális gépek a feladatátvétel után lesznek elhelyezve.
5. Válassza ki a **virtuális gépek** > **válassza**ki a replikálni kívánt virtuális gépet. Ezután válassza az **OK** lehetőséget.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** művelet befejezése után a kezdeti replikálás befejeződött, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
