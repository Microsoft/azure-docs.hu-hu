---
title: A Hyper-V (VMM) szolgáltatás vész-helyreállításának beállítása Azure Site Recovery használatával
description: Megtudhatja, hogyan állíthatja be a System Center VMM-felhőkben futó helyszíni Hyper-V virtuális gépek vész-helyreállítását az Azure-ba Site Recovery használatával.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89442820"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM-felhőkben található, Hyper-V rendszerű helyszíni virtuális gépek Azure-ba történő vészhelyreállításának beállítása

Ez az oktatóanyag azt ismerteti, hogyan engedélyezhető a (z) System Center Virtual Machine Manager (VMM) által felügyelt helyszíni Hyper-V virtuális gépek (VM-EK) replikálása az Azure-ban [Azure site Recovery](site-recovery-overview.md)használatával. Ha nem használja a VMM-t, [kövesse ezt az oktatóanyagot](hyper-v-azure-tutorial.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki a replikációs forrást és célt.
> * Állítsa be a forrás replikációs környezetet, beleértve a helyszíni Site Recovery összetevőket és a cél replikációs környezetet.
> * Hálózati leképezés beállítása VMM virtuálisgép-hálózatok és Azure-beli virtuális hálózatok között.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a [site Recovery dokumentációjának](./index.yml) **útmutatók** szakaszának cikkeit.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már végrehajtotta a következő oktatóanyagokat:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
1. [Helyszíni Hyper-V-kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A Azure Portal lépjen a **Recovery Services** -tárolók elemre, és válassza ki a **ContosoVMVault** -tárolót, amelyet az [Azure előkészítése](tutorial-prepare-azure.md#create-a-recovery-services-vault) oktatóanyagban hozott létre.
1. **Első lépések** válassza ki **site Recovery**  >  az **infrastruktúra előkészítése** és a következő beállítások konfigurálását:
    1. **Védelmi cél**  >  **Hol találhatók a gépek?** területen válassza **a** helyszíni lehetőséget.
    1. **Hová szeretné replikálni a gépeket**, válassza ki **Az Azure-t**.
    1. **Virtualizáltak a gépek?**, válassza **az igen, a Hyper-V** lehetőséget.
    1. A **System Center VMM használja a Hyper-V-gazdagépek felügyeletére?**, válassza az **Igen** lehetőséget.
1. Válassza az **OK** lehetőséget.

   ![Replikációs cél](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

1. Ha nagyméretű központi telepítést tervez, a **központi telepítés tervezése** során töltse le a Hyper-V Deployment Planner a lapon található hivatkozásra kattintva. [További](hyper-v-deployment-planner-overview.md) információ a Hyper-V üzembe helyezésének megtervezéséről.
1. Ebben az oktatóanyagban nincs szükségünk a Deployment Plannerra. A-ben **befejezte az üzembe helyezés megtervezését?** területen válassza a **később** lehetőséget, majd kattintson **az OK gombra**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forrás-környezet beállításakor telepíti a Azure Site Recovery szolgáltatót a VMM-kiszolgálóra, és regisztrálja a kiszolgálót a tárolóban. Minden Hyper-V-gazdagépen telepítenie kell az Azure Recovery Services-ügynököt.

1. **Készítse elő az infrastruktúrát**. Válassza a **forrás** lehetőséget.
1. **Forrás előkészítése**. VMM-kiszolgáló hozzáadásához válassza a **+ VMM** lehetőséget. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
1. Töltse le a Microsoft Azure Site Recovery Provider telepítőjét.
1. Töltse le a tároló regisztrációs kulcsát. Ezt a kulcsot a szolgáltató telepítőjének futtatásakor kell megadnia. A kulcs a generálásától számított öt napig érvényes.
1. Töltse le a Microsoft Azure Recovery Services-ügynök telepítőjét.

   ![Szolgáltató, regisztrációs kulcs és ügynök letöltése](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>A szolgáltató telepítése a VMM-kiszolgálón

1. A Azure Site Recovery Provider telepítővarázslójának **Microsoft Update**. A Microsoft Update használata a szolgáltató frissítéseinek kereséséhez.
1. **Telepítés**. Fogadja el az alapértelmezett telepítési helyet a szolgáltató számára, és válassza a **telepítés** lehetőséget.
1. A telepítés után a Microsoft Azure Site Recovery regisztrációs varázslóban válassza a tár **beállításai**, **Tallózás**, majd a **kulcsfájl** lehetőséget, válassza ki a letöltött tároló-kulcsot.
1. Adja meg a Azure Site Recovery-előfizetést és a tár nevét (**ContosoVMVault**). Adjon meg egy rövid nevet a VMM-kiszolgálónak a tárolóban való azonosításához.
1. **Proxybeállítások**. Válassza **a közvetlen csatlakozás Azure site Recovery proxy nélkül** lehetőséget.
1. Fogadja el az adattitkosításhoz használt tanúsítvány alapértelmezett helyét. A rendszer a feladatátvétel során visszafejti a titkosított adattitkosítást.
1. **Felhőbeli metaadatok szinkronizálása**. Válassza **a Felhőbeli metaadatok szinkronizálása lehetőséget site Recovery portálon**. Ennek a műveletnek csak egyszer kell történnie az egyes kiszolgálókon. Ezután válassza a **regisztráció** lehetőséget.
1. Miután regisztrálta a kiszolgálót a tárolóban, válassza a **Befejezés** lehetőséget.

A regisztráció befejeződése után a rendszer beolvassa a kiszolgáló metaadatait Azure Site Recovery és a VMM-kiszolgáló **site Recovery infrastruktúrában** jelenik meg.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>A Recovery Services-ügynök telepítése Hyper-V-gazdagépekre

Telepítse az ügynököt minden olyan Hyper-V-gazdagépre, amely a replikálni kívánt virtuális gépeket tartalmazza.

A Microsoft Azure Recovery Services ügynök telepítése varázslóban konfigurálja ezeket a beállításokat:

1. **Előfeltételek ellenőrzése**. Kattintson a **Tovább** gombra. A hiányzó Előfeltételek telepítése automatikusan megtörténik.
1. **Telepítési beállítások**. Fogadja el a telepítési helyet. Ezt követően válassza az **Install** (Telepítés) parancsot.

    >[!NOTE]
    >Azure Site Recovery esetén a **gyorsítótár helye** nem kötelező.

1. **Telepítés**. A telepítés befejezésekor kattintson a **Bezárás** gombra a varázsló befejezéséhez.

   ![Ügynök telepítése](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

1. Válassza az **infrastruktúra előkészítése**  >  **cél** lehetőséget.
1. Válassza ki azt az előfizetést és erőforráscsoportot (**ContosoRG**), amelyben az Azure-beli virtuális gépeket a feladatátvétel után létre kívánja hozni.
1. Válassza ki a **Resource Manager** -alapú üzemi modellt.

Site Recovery ellenőrzi, hogy van-e egy vagy több kompatibilis Azure Storage-fiók és-hálózat.

## <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. **Site Recovery infrastruktúra**  >  **Hálózati leképezések**  >  **Hálózati leképezés**. Válassza a **+ hálózat leképezése** ikont.
1. **Hálózati leképezés hozzáadása**. Válassza ki a **forrásoldali System Center VMM** -kiszolgálót. A **cél** beállításnál válassza az Azure lehetőséget.
1. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
1. **Forrásoldali hálózat**. Válassza ki a forrás helyszíni virtuálisgép-hálózatot.
1. **Célként megadott hálózat**. Válassza ki azt az Azure-hálózatot, amelyben a replika Azure virtuális gépek a feladatátvételt követően jönnek létre. Ez után válassza az **OK** gombot.

   ![Hálózatleképezés](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

1. Válassza az **infrastruktúra előkészítése**  >  **replikációs beállítások**  >  **+ Létrehozás és hozzárendelés** lehetőséget.
1. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. **ContosoReplicationPolicy** használunk.
1. Fogadja el az alapértelmezett beállításokat, majd kattintson **az OK gombra**:
   - A **másolási gyakoriság** azt jelzi, hogy a kezdeti replikálás után öt percenként replikálja a rendszer a különbözeti adataikat.
   - A **helyreállítási pont megőrzése** azt jelzi, hogy az egyes helyreállítási pontok két óráig maradnak.
   - Az **Alkalmazáskonzisztens pillanatkép gyakorisága** azt jelzi, hogy a rendszer minden órában létrehozza az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat.
   - A **kezdeti replikálás kezdő időpontja** azt jelzi, hogy a kezdeti replikáció azonnal elindul.
   - Az **Azure-on tárolt adatok titkosítása** az alapértelmezett (**kikapcsolt**) értékre van állítva, és azt jelzi, hogy az Azure-on kívüli adatok nem titkosítottak.
1. A szabályzat létrehozása után kattintson **az OK gombra**. Új szabályzat létrehozásakor a rendszer automatikusan hozzárendeli a VMM-felhőhöz.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. **Alkalmazás replikálása**. Válassza a **forrás** lehetőséget.
1. **Forrás**. Válassza ki a VMM-felhőt. Ez után válassza az **OK** gombot.
1. **Cél**. Ellenőrizze a célt (Azure), a tár előfizetését, és válassza ki a **Resource Manager** -modellt.
1. Válassza ki a **contosovmsacct1910171607** Storage-fiókot és a **ContosoASRnet** Azure-hálózatot.
1. **Virtuális gépek**  >  **Válassza a lehetőséget**. Válassza ki a replikálni kívánt virtuális gépet. Ez után válassza az **OK** gombot.

   A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** művelet befejezése után a kezdeti replikálás befejeződött, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
