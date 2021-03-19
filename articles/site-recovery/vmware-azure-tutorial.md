---
title: VMware virtuális gép vész-helyreállításának beállítása az Azure-ba Azure Site Recovery
description: Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recoveryvel.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: bd87265140a0bfaeb7ef4dada6dd76be1269654b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369369"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Ez a cikk azt ismerteti, hogyan engedélyezhető a helyszíni VMware virtuális gépek replikálása az Azure-ba a [Azure site Recovery](site-recovery-overview.md) szolgáltatással való vész-helyreállításhoz.

Ez a harmadik oktatóanyag egy sorozatban, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba helyszíni VMware virtuális gépekre. Az előző oktatóanyagban [elkészítettük a helyszíni VMware-környezetet](vmware-azure-tutorial-prepare-on-premises.md) az Azure-ba való vész-helyreállításhoz.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a forrás-replikációs beállításokat és a helyszíni Site Recovery konfigurációs kiszolgálót.
> * Állítsa be a replikációs cél beállításait.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze a replikációt egy VMware virtuális gép számára.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

## <a name="before-you-start"></a>Előkészületek

Fejezze be az előző oktatóanyagokat:
1. Győződjön meg arról, hogy [beállította az Azure](tutorial-prepare-azure.md) -t a helyszíni VMware vész-helyreállításhoz az Azure-ban.
2. Az [alábbi lépéseket](vmware-azure-tutorial-prepare-on-premises.md) követve előkészítheti a helyszíni VMware-telepítést az Azure-ba való vész-helyreállításra.
3. Ebben az oktatóanyagban bemutatjuk, hogyan replikálhat egyetlen virtuális gépet. Több VMware virtuális gép üzembe helyezésekor a [Deployment Planner eszközt](https://aka.ms/asr-deployment-planner)kell használnia. [További információ](site-recovery-deployment-planner.md) az eszközről.
4. Ez az oktatóanyag számos lehetőséget használ, amelyeket érdemes másképpen végrehajtani:
    - Az oktatóanyag egy PETESEJT-sablont használ a konfigurációs kiszolgáló VMware virtuális gép létrehozásához. Ha ezt valamilyen okból nem tudja elvégezni, a konfigurációs kiszolgáló manuális beállításához kövesse az [alábbi utasításokat](physical-manage-configuration-server.md) .
    - Ebben az oktatóanyagban Site Recovery automatikusan letölti és telepíti a MySQL-t a konfigurációs kiszolgálóra. Ha szeretné, manuálisan is beállíthatja. [További információ](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **védelmi cél**  >  ,**ahol a gépek találhatók**, válassza **a** helyszíni lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ez után válassza az **OK** gombot.



## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forrás-környezetben egyetlen, magasan elérhető helyszíni gépre van szükség a helyszíni Site Recovery-összetevők üzemeltetéséhez:

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és egy gyorsítótárbeli Storage-fiókba küldi az Azure-ban. A Process Server a mobilitási szolgáltatás ügynökét is telepíti a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- **Fő célkiszolgáló**: a fő célkiszolgáló kezeli a replikálási adatait az Azure-beli feladat-visszavétel során.


Ezen összetevők mindegyike a *konfigurációs kiszolgálóként* ismert helyszíni gépeken együtt települ. Alapértelmezés szerint a VMware vész-helyreállítás esetén a konfigurációs kiszolgálót egy magasan elérhető VMware virtuális gépként kell beállítani. Ehhez le kell töltenie egy előkészített Open Virtualization Application (PETESEJT) sablont, és importálnia kell a sablont a VMware-be a virtuális gép létrehozásához.

- A konfigurációs kiszolgáló legújabb verziója a portálon érhető el. Közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver)is letöltheti.
- Ha valamilyen okból kifolyólag nem lehet PETESEJT-sablont használni a virtuális gép beállításához, kövesse az [alábbi utasításokat](physical-manage-configuration-server.md) a konfigurációs kiszolgáló manuális beállításához.
- A OVF-sablonnal megadott licenc 180 napig érvényes próbaverziós licenc. A virtuális gépen futó Windows rendszernek aktiválnia kell a szükséges licencet.


### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban váltson az **infrastruktúra előkészítése**  >  **forrásra**.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló PETESEJTJEI-sablonját.



## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben


1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere-ügyféllel.
2. A **fájl** menüben válassza a **OVF-sablon telepítése** lehetőséget a **OVF-sablon központi telepítése varázsló** elindításához.

     ![Képernyőkép a OVF-sablon telepítése parancsról a VMWare vSphere-ügyfélen.](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A virtuális gép alapértelmezett beállításokkal való telepítéséhez a **Ready to complete** (Befejezésre kész) területen válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

   > [!TIP]
   > Ha további hálózati adaptert szeretne hozzáadni, törölje **a bekapcsolást az üzembe helyezés**  >  **befejezése** után. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, adja hozzá azt a tárolóban lévő kiszolgáló regisztrálása előtt. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Válassza a **Tovább** > **Befejezés** lehetőséget. Ez után válassza az **OK** gombot.


## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása

A konfigurációs kiszolgáló beállítása után regisztrálja a tárolóban.

1. A VMware vSphere-ügyfél konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával néhány másodpercen belül elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót. Győződjön meg arról, hogy a szükséges [szerepkörök](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) hozzá vannak rendelve ehhez a felhasználóhoz.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.


### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

A konfigurációs kiszolgáló beállításának és regisztrálásának befejezése. A folytatás előtt győződjön meg arról, hogy az összes [előfeltétel](vmware-azure-deploy-configuration-server.md#prerequisites) teljesül a konfigurációs kiszolgáló sikeres beállításakor.


1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **kapcsolat beállítása** lehetőséget. A legördülő listából válassza ki azt a hálózati adaptert, amelyet a beépített Process Server használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásoldali gépeken, majd válassza ki azt a hálózati adaptert, amelyet a konfigurációs kiszolgáló az Azure-hoz való kapcsolódáshoz használ. Kattintson a **Mentés** gombra. Ez a beállítás a konfigurálás után nem módosítható.
2. A **Recovery Services**-tároló kiválasztása lapon válassza ki az Azure-előfizetését, valamint a megfelelő erőforráscsoportot és tárat.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez. Ha a MySQL-t az elérési úton helyezte, akkor ez a lépés kihagyható. [További](vmware-azure-deploy-configuration-server.md#configure-settings) információ
4. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
5. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
6. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt felhasználói hitelesítő adatokat. Győződjön meg a felhasználónév és a jelszó helyességéről, és a védelmük érdekében arról is, hogy a virtuális gép Rendszergazdák csoportjába tartoznak. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.
7. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás virtuális gépekre való automatikus telepítéséhez használni kívánt felhasználónevet és jelszót, ha a replikáció engedélyezve van.
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
8. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
9. A regisztráció befejeződése után nyissa meg a Azure Portal, és ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel-e a **Recovery Services**-tárolóban  >    >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** kezelése.


A konfigurációs kiszolgáló regisztrálása után Site Recovery a megadott beállítások használatával csatlakozik a VMware-kiszolgálókhoz, és felfedi a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **konfigurációs kiszolgálók**  >  **_kiszolgáló neve_*_ > _* frissítési kiszolgáló** elemet.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Válassza az **infrastruktúra előkészítése**  >  **cél** lehetőséget. Válassza ki a használni kívánt Azure-előfizetést. Resource Manager-modellt használunk.
2. Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy virtuális hálózattal. Ezeknek már léteznie kell, amikor a jelen oktatóanyag-sorozat [első oktatóanyagában](tutorial-prepare-azure.md) beállítja az Azure-összetevőket.

   ![Képernyőfelvétel: az infrastruktúra előkészítése > cél beállításai.](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg és válassza ki **Recovery Services** tárolókat.
2. Válassza ki a Recovery Services helyreállítási tárat (ebben az oktatóanyagban ez a **ContosoVMVault**).
3. Replikációs szabályzat létrehozásához válassza az **site Recovery infrastruktúra**-  >  **replikációs házirendek**  >  **+ replikációs házirend** elemet.
4. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét. Itt a **VMwareRepPolicy** nevet használjuk.
5. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A jelen oktatóanyagban 72 órát adunk meg. A replikált virtuális gépek ezen a megőrzési időtartamon belül bármikor helyreállíthatók.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításban adhatja meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket. Az alapértelmezett 60 percet használjuk. A szabályzat létrehozásához válassza az **OK** gombot.

   ![Képernyőkép a replikációs házirend létrehozása lehetőségről.](./media/vmware-azure-tutorial/replication-policy.png)

- A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.
- Alapértelmezés szerint a rendszer a feladat-visszavételhez is automatikusan létrehoz egy megfelelő szabályzatot. Ha például a replikációs szabályzat a **rep-policy**, a feladat-visszavételi szabályzat a **rep-policy-failback** lesz. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

Megjegyzés: a VMware – Azure forgatókönyvben az összeomlás-konzisztens pillanatkép 5 percnél több idő alatt elvégezhető.

## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a virtuális gépek replikálását a következőképpen:

1. Válassza az **alkalmazás**  >  **forrásának** replikálása lehetőséget.
2. A **Forrás** mezőben válassza a **Helyszíni** lehetőséget, majd a **Forrás helye** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet vagy az azt felügyelő vCenter-kiszolgálót.
5. Válassza ki a folyamatkiszolgálót (alapértelmezés szerint telepítve van a konfigurációs kiszolgáló virtuális gépén). Ez után válassza az **OK** gombot. Az egyes folyamatok kiszolgálóinak állapota ajánlott korlátként és egyéb paraméterekként van megjelölve. Válassza ki az egészséges folyamat kiszolgálóját. Nem lehet kiválasztani egy [kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) Process Servert. A hibák [elhárításához és megoldásához,](vmware-physical-azure-troubleshoot-process-server.md) **illetve** a [kibővíthető folyamat kiszolgálójának](vmware-azure-set-up-process-server-scale.md)beállításához is használható.
6. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. A Resource Manager-alapú üzemi modellt használjuk.
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Ha a hálózati beállítást minden olyan virtuális gépre alkalmazni szeretné, amelyen engedélyezte a replikációt, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza a **Konfigurálás később** lehetőséget az Azure-hálózat számítógépenkénti kiválasztásához.
9. **Virtual Machines**  >  **válassza a virtuális gépek lehetőséget**, és válassza ki a replikálni kívánt gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ez után válassza az **OK** gombot. Ha nem tudja megtekinteni/kijelölni egy adott virtuális gépet, [további](./vmware-azure-troubleshoot-replication.md) információ a probléma megoldásáról.
10. A **Tulajdonságok**  >  **tulajdonságok konfigurálása** területen válassza ki azt a fiókot, amelyet a Process-kiszolgáló a mobilitási szolgáltatás automatikus telepítéséhez használ a gépen.
11. A replikációs **Beállítások**  >  **replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva.
12. Válassza a **replikáció engedélyezése** lehetőséget. Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
13. A **védelem engedélyezése** feladat előrehaladását a beállítások   >  **feladatok**  >  **site Recovery feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** és a helyreállítási pontok létrehozásának befejezése után a gép készen áll a feladatátvételre.
14. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
15. A hozzáadott virtuális gépek figyeléséhez tekintse meg a virtuális gépek legutóbbi felderített idejét a **konfigurációs kiszolgálók**  >  **utolsó kapcsolatfelvétele** című résznél. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.

## <a name="next-steps"></a>Következő lépések
A replikáció engedélyezése után futtasson egy részletezést, és győződjön meg róla, hogy minden a várt módon működik-e.
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)