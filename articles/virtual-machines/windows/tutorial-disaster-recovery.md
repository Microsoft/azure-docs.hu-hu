---
title: Oktatóanyag – Windows rendszerű virtuális gépek vész-helyreállításának beállítása Azure Site Recovery
description: Megtudhatja, hogyan engedélyezheti a Windows rendszerű virtuális gépek vész-helyreállítását egy másik Azure-régióba az Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: fd5d8c3e2c6e4ee5556568ebd23ac99b48300e9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382023"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Oktatóanyag: a Windows rendszerű virtuális gépek vész-helyreállításának engedélyezése

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a Windows rendszerű Azure-beli virtuális gépek vész-helyreállítását. Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> * Windows rendszerű virtuális gépek vész-helyreállításának engedélyezése
> * Vész-helyreállítási gyakorlat futtatása a várt módon való működéshez
> * A virtuális gép replikálásának leállítása a részletezés után

Amikor engedélyezi a replikációt egy virtuális géphez, a Site Recovery mobilitási szolgáltatás bővítmény települ a virtuális gépre, és regisztrálja azt a [Azure site Recovery](../../site-recovery/site-recovery-overview.md). A replikáció során a rendszer a virtuálisgép-lemezek írásait egy, a forrás régiójában lévő cache Storage-fiókba továbbítja. Az adatok innen érkeznek a célhelyre, a helyreállítási pontok pedig az adatokból jönnek létre.  Ha egy virtuális gépet a vész-helyreállítási folyamat során hajt végre, a rendszer egy helyreállítási pontot hoz létre a megcélzott régióban lévő virtuális gépek létrehozásához.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

1. Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi, hogy létrehozzon egy virtuális gépet a megcélzott régióban. Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája, és rendelkezik a szükséges engedélyekkel.
2. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a következőt:
    - A virtuálisgép-közreműködő beépített szerepköre vagy a következőkre vonatkozó konkrét engedélyek:
        - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
        - Írás egy Azure Storage-fiókba.
        - Írás egy Azure által felügyelt lemezre.
    - A Site Recovery közreműködő beépített szerepköre a tárolóban lévő Site Recovery műveletek kezeléséhez. 
3. Javasoljuk, hogy Windows Server 2012 vagy újabb rendszert futtató Windows rendszerű virtuális gépet használjon. A virtuálisgép-lemez nem titkosítható az oktatóanyag céljára.
4. Ha a virtuális gép kimenő kapcsolatai URL-alapú proxyt használnak, győződjön meg arról, hogy az URL-címek elérhetők. A hitelesített proxy használata nem támogatott.

    **Név** | **Nyilvános felhő** | **Kormányzati felhő** | **Részletek**
    --- | --- | --- | ---
    Tárolás | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Adatok írása a virtuális gépről a forrás régióban lévő cache Storage-fiókba. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Engedélyezés és hitelesítés Site Recovery szolgáltatás URL-címeire. 
    Replikáció | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM-kommunikáció a Site Recovery szolgáltatással. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | A virtuális gép Site Recovery monitorozási és diagnosztikai adatot ír. 

4. Ha hálózati biztonsági csoportokat (NSG) használ a virtuális gépek hálózati forgalmának korlátozására, hozzon létre olyan NSG-szabályokat, amelyek engedélyezik a virtuális gép kimenő kapcsolatát (HTTPS 443) ezen szolgáltatás-címkék (IP-címek csoportjai) használatával. Először próbálja ki a tesztelési NSG vonatkozó szabályokat.

    **Tag** | **Engedélyezés** 
    --- | --- 
    Storage-címke | Lehetővé teszi az adatok a virtuális gépről a gyorsítótárbeli Storage-fiókba való írását.
    Azure AD-címke | Engedélyezi az Azure AD-nek megfelelő összes IP-cím elérését.
    EventsHub címke | Hozzáférést biztosít Site Recovery figyeléshez.
    AzureSiteRecovery címke | Engedélyezi a Site Recovery szolgáltatás elérését bármely régióban.
    GuestAndHybridManagement | Akkor használja, ha szeretné automatikusan frissíteni a replikálásra engedélyezett virtuális gépeken futó Site Recovery mobilitási ügynököt.
5.  Windows rendszerű virtuális gépeken telepítse a legújabb Windows-frissítéseket, és győződjön meg arról, hogy a virtuális gépek rendelkeznek a legfelső szintű tanúsítvánnyal.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Virtuális gép létrehozása és a vész-helyreállítás engedélyezése

A virtuális gépek létrehozásakor a vész-helyreállítást is engedélyezheti.

1. [Hozzon létre egy virtuális gépet](quick-create-portal.md).
2. A **kezelés** lapon válassza a vész- **helyreállítás engedélyezése** lehetőséget.
3. A **másodlagos régióban** válassza ki azt a célcsoportot, amelyre a virtuális gépet replikálni szeretné a vész-helyreállításhoz.
4. A **másodlagos előfizetés** területen válassza ki azt a cél-előfizetést, amelyben a célként megadott virtuális gép létre lesz hozva. A célként megadott virtuális gép akkor jön létre, amikor feladatátvételt hajt végre a forrás virtuális gépen a forrás régiójából a célként megadott régióba.
5. A **Recovery Services**-tárolóban válassza ki a replikáláshoz használni kívánt tárolót. Ha nem rendelkezik tárolóval, válassza az **új létrehozása** lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a tárolót és a tároló nevét kívánja elhelyezni.
6. **Site Recovery házirendben** hagyja meg az alapértelmezett házirendet, vagy válassza az **új létrehozása** lehetőséget az egyéni értékek megadásához.

    - A helyreállítási pontok a virtuálisgép-lemezek egy adott időpontban vett pillanatképei alapján jönnek létre. Ha feladatátvételt végez egy virtuális gépen, egy helyreállítási pont használatával állítja vissza a virtuális gépet a célként megadott régióban. 
    - A rendszer öt percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ez a beállítás nem módosítható. Az összeomlás-konzisztens Pillanatképek rögzítik a lemezen lévő, a pillanatkép elkészítéséhez szükséges adatok mennyiségét. Nem tartalmaz semmit a memóriában. 
    - Alapértelmezés szerint a Site Recovery 24 óráig megőrzi az összeomlás-konzisztens helyreállítási pontokat. 0 és 72 óra közötti egyéni értéket is beállíthat.
    - Az alkalmazással konzisztens pillanatképek készítése 4 óránként történik. Egy alkalmazás-konzisztens pillanatkép 
    - Alapértelmezés szerint a Site Recovery 24 óráig tárolja a helyreállítási pontokat.

7. A **rendelkezésre állási beállítások** területen adja meg, hogy a virtuális gép önállóként, rendelkezésre állási zónában vagy rendelkezésre állási csoportba legyen-e telepítve.

    ::: képtípus = "Content" forrás = "./Media/tutorial-Disaster-Recovery/create-vm.png" Alt-text = "a replikáció engedélyezése a virtuálisgép-kezelés tulajdonságai lapon."

8. Fejezze be a virtuális gép létrehozását.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Egy meglévő virtuális gép vész-helyreállításának engedélyezése

Ha az új virtuális gép helyett egy meglévő virtuális gépen szeretné engedélyezni a vész-helyreállítást, használja ezt az eljárást.

1. A Azure Portal nyissa meg a virtuális gép tulajdonságai lapot.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Nyisson meg egy meglévő virtuális gép vész-helyreállítási beállításait.":::

3. Az **alapvető beállításokban**, ha a virtuális gép rendelkezésre állási zónában van telepítve, akkor a rendelkezésre állási zónák között kiválaszthatja a vész-helyreállítást.
4. A **cél régióban** válassza ki azt a régiót, amelyre a virtuális gépet replikálni szeretné. A forrás-és a célcsoportnak ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Állítsa be a virtuális gép alapszintű vész-helyreállítási beállításait.":::

5. Válassza a **Tovább: speciális beállítások** lehetőséget.
6. A **Speciális beállítások** lapon áttekintheti a beállításokat, és módosíthatja az értékeket egyéni beállításokra. Alapértelmezés szerint a Site Recovery a forrás beállításait a célként megadott erőforrások létrehozásához.

    - **Cél-előfizetés**. Az előfizetés, amelyben a célként megadott virtuális gép létrejön a feladatátvétel után.
    - **Cél** virtuálisgép-erőforráscsoport. Az az erőforráscsoport, amelyben a célként megadott virtuális gép létrejön a feladatátvétel után.
    - **Célként megadott virtuális hálózat**. Az Azure-beli virtuális hálózat, amelyben a célként megadott virtuális gép a feladatátvételt követően jön létre.
    - **Cél rendelkezésre állása**. Ha a célként megadott virtuális gép egyetlen példányként jön létre, a rendelkezésre állási csoport vagy a rendelkezésre állási zónában.
    - **Közelség elhelyezése**. Ha alkalmazható, válassza ki azt a Proximity-elhelyezési csoportot, amelyben a cél virtuális gép a feladatátvételt követően található.
    - **Tárolási beállítások – gyorsítótár Storage-fiók**. A helyreállítás a forrás régióban lévő Storage-fiókot használja ideiglenes adattárként. A forrásként szolgáló virtuális gép módosításait a rendszer gyorsítótárazza ebben a fiókban, mielőtt replikálja a célhelyre.
        - Alapértelmezés szerint a rendszer létrehoz egy gyorsítótár-tárolási fiókot a tárolóban, és újból felhasználja.
        - Ha testre szeretné szabni a virtuális gép gyorsítótár-fiókját, másik Storage-fiókot is kijelölhet.
    - **Tárolási beállítások – replika felügyelt lemez**. Alapértelmezés szerint a Site Recovery replika felügyelt lemezeket hoz létre a célként megadott régióban.
        -  Alapértelmezés szerint a célként kezelt lemez felügyeli a forrásként szolgáló virtuális gép által felügyelt lemezeket ugyanazzal a tárolási típussal (standard HDD/SSD vagy prémium SSD).
        - Igény szerint testre szabhatja a tárolási típust.
    - **Replikációs beállítások**. Megjeleníti azt a tárolót, amelyben a virtuális gép található, valamint a virtuális géphez használt replikációs házirendet. Alapértelmezés szerint a virtuális gép Site Recovery által létrehozott helyreállítási pontok 24 óráig tartanak.
    - **Bővítmény beállításai**. Azt jelzi, hogy a Site Recovery a replikált virtuális gépeken telepített Site Recovery mobilitási szolgáltatás bővítményének frissítéseit kezeli.
        - A jelzett Azure Automation-fiók kezeli a frissítési folyamatot.
        - Testreszabhatja az Automation-fiókot.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="A cél-és replikációs beállítások összegzését bemutató oldal.":::


6. Válassza a **felülvizsgálat + replikáció indítása** lehetőséget.

7. Válassza a **replikáció indítása** lehetőséget. Az üzembe helyezés elindul, és Site Recovery megkezdi a cél erőforrások létrehozását. Az értesítésekben nyomon követheti a replikálás folyamatát.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Értesítés a replikálás előrehaladásáról.":::

## <a name="check-vm-status"></a>Virtuális gép állapotának keresése

A replikálási feladatok befejeződése után megtekintheti a virtuális gép replikálási állapotát.

1. Nyissa meg a virtuális gép tulajdonságai lapot.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.
3. Az **alapvető** erőforrások szakasz kibontásával tekintheti meg a tár, a replikációs házirend és a cél beállításainak alapértelmezéseit.
4. Az állapot **és állapot** területen tájékozódjon a virtuális gép replikációs állapotáról, az ügynök verziószámáról, a feladatátvétel készültségéről és a legújabb helyreállítási pontokról. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Essentials-nézet a virtuális gép vész-helyreállításához.":::

5. Az **infrastruktúra nézetben** a forrás-és a cél virtuális gépek, a felügyelt lemezek és a cache Storage-fiók vizuális áttekintését olvashatja.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="infrastruktúra-vizualizációs Térkép a virtuális gép vész-helyreállításához.":::


## <a name="run-a-drill"></a>Részletezés futtatása

Futtasson egy részletezést, és győződjön meg arról, hogy a vész-helyreállítás a várt módon működik. A feladatátvételi teszt futtatásakor a létrehozza a virtuális gép másolatát, amely nincs hatással a folyamatos replikálásra vagy az éles környezetre. 

1. A virtuális gép vész-helyreállítási lapján válassza a **feladatátvételi teszt** lehetőséget.
2. A **feladatátvételi teszt** területen hagyja meg a helyreállítási pont alapértelmezett, **legutóbb feldolgozott (alacsony RPO)** beállítását.

   Ez a beállítás biztosítja a legalacsonyabb helyreállítási időkorlátot (RPO), és általában a célként megadott virtuális gép leggyorsabb felpörgetését. Először dolgozza fel az Site Recovery szolgáltatásnak elküldett összes adatát, hogy minden virtuális gép számára hozzon létre egy helyreállítási pontot, a feladatátvétel előtt. Ez a helyreállítási pont a feladatátvétel elindítása után Site Recovery replikált összes adattal rendelkezik.

3. Válassza ki azt a virtuális hálózatot, amelyben a virtuális gép a feladatátvétel után lesz elhelyezve. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Lapon a feladatátvételi teszt beállításainak megadásához.":::

4. A teszt feladatátvételi folyamat megkezdődik. Nyomon követheti az értesítések előrehaladását.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Feladatátvételi értesítések tesztelése."::: 
    
   A feladatátvételi teszt befejezése után a virtuális gép a *kitakarítási teszt feladatátvétele függőben* állapotú a **Essentials** lapon. 



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rendszer a részletezés után Site Recovery automatikusan megtisztítja a virtuális gépet.

1. Az automatikus karbantartás megkezdéséhez válassza a **feladatátvételi teszt törlése** lehetőséget.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="A karbantartás megkezdése az Essentials oldalon."::: 

2. A **feladatátvételi teszt karbantartása** lapon írja be a feladatátvételhez rögzíteni kívánt megjegyzéseket, majd válassza a **tesztelés befejezése lehetőséget. A feladatátvételi teszt virtuális gép törlése**. Ez után válassza az **OK** gombot.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Lap a megjegyzések rögzítéséhez és a teszt virtuális gép törléséhez."::: 

7. A törlési folyamat megkezdődik. Az értesítésekben nyomon követheti a folyamat előrehaladását.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Értesítések a DELETE teszt virtuális gép figyeléséhez."::: 

### <a name="stop-replicating-the-vm"></a>A virtuális gép replikálásának leállítása

A vész-helyreállítási gyakorlat befejezése után javasoljuk, hogy folytassa a teljes feladatátvétel kipróbálásával. Ha nem szeretne teljes feladatátvételt végezni, tiltsa le a replikációt. Ez a következő műveleteket végzi el:

- Eltávolítja a virtuális gépet a replikált gépek Site Recovery listájáról.
- Leállítja a virtuális gép Site Recovery számlázását.
- Automatikusan törli a forrás-replikációs beállításokat.

Állítsa le a replikálást a következő módon:

1. A virtuális gép vész-helyreállítási lapján válassza a **replikáció letiltása** lehetőséget.
2. A **replikáció letiltása** területen válassza ki a replikáció letiltásának okát. Ez után válassza az **OK** gombot.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Lapon letilthatja a replikációt, és megadhat egy okot."::: 


A virtuális gépen a replikálás során telepített Site Recovery bővítmény nem törlődik automatikusan. Ha letiltja a virtuális gép replikálását, és később nem kívánja újra replikálni, akkor manuálisan eltávolíthatja a Site Recovery-bővítményt az alábbiak szerint: 

1. Nyissa meg a virtuális gép > **Beállítások**  >  **bővítményeit**.
2. A **bővítmények** lapon válassza ki az egyes *Microsoft. Azure. recoveryservices szolgáltatónál* -bejegyzéseket a Linux rendszerhez.
3. A bővítmény Tulajdonságok lapján válassza az **Eltávolítás** lehetőséget.

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Lapon távolítsa el a Site Recovery virtuálisgép-bővítményt.":::



## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gép vész-helyreállítását konfigurálta, és a vész-helyreállítási részletezést futtatta. Most elvégezheti a virtuális gép teljes feladatátvételét.

> [!div class="nextstepaction"]
> [Virtuális gép feladatainak átadása egy másik régióba](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
