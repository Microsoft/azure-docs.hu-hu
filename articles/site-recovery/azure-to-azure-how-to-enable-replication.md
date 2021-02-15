---
title: Azure-beli virtuális gépek replikálásának konfigurálása Azure Site Recovery
description: Megtudhatja, hogyan konfigurálhat replikálást egy másik régióba Azure-beli virtuális gépekhez Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 427b471158e89b2b3ae4ea6477133f1e69247078
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518844"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Azure-beli virtuális gépek replikálása másik Azure-régióba


Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure-beli virtuális gépek replikálása az egyik Azure-régióból a másikba.

## <a name="before-you-start"></a>Előkészületek

Ez a cikk azt feltételezi, hogy előkészítette Site Recovery üzembe helyezését az [Azure-ról az Azure-ba való vész-helyreállítási oktatóanyagban](azure-to-azure-tutorial-enable-replication.md)leírtak szerint.

Meg kell adni az előfeltételeket, és létre kell hoznia egy Recovery Services-tárolót.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt. Ez az eljárás feltételezi, hogy az elsődleges Azure-régió Kelet-Ázsia, a másodlagos régió pedig Dél-Kelet-Ázsia.

1. A tárolóban kattintson a **+ replikálás** elemre.
2. Vegye figyelembe a következő mezőket:
   - **Forrás**: a virtuális gépek származási helye, amely ebben az esetben az **Azure**.
   - **Forrás helye**: az az Azure-régió, amelyből a virtuális gépeket biztosítani kívánja. Ebben az ábrán a forrás helye: "Kelet-Ázsia"
   - **Üzembe helyezési modell**: a forrásoldali gépek Azure-beli üzembe helyezési modellje.
   - **Forrás-előfizetés**: az előfizetés, amelybe a forrás virtuális gépek tartoznak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
   - **Erőforráscsoport**: az erőforráscsoport, amelyhez a forrás virtuális gépek tartoznak. A következő lépésben a kiválasztott erőforráscsoport alatti virtuális gépek jelennek meg a védelemhez.

     ![Képernyőfelvétel: a replikáció konfigurálásához szükséges mezők kiemelése.](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. A **Virtual Machines > válassza a virtuális gépek lehetőséget**, majd kattintson és válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.
    ![Képernyőkép, amely kiemeli a virtuális gépek kiválasztásának helyét.](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. A **Beállítások** lehetőségnél beállíthatja a célhely beállításait:

   - **Cél helye**: a forrásként szolgáló virtuális gép adatai replikálásának helye. A kiválasztott gépek helyétől függően Site Recovery megadja a megfelelő célcsoportok listáját. Javasoljuk, hogy a célhelyet a Recovery Services tároló helyével megegyező helyen tartsa.
   - **Célelőfizetés**: A vészhelyreállításhoz használt célelőfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel.
   - **Cél erőforráscsoport**: az az erőforráscsoport, amelyhez a replikált virtuális gépek tartoznak.
       - Alapértelmezés szerint a Site Recovery létrehoz egy új erőforráscsoportot a célként megadott régióban egy "ASR" utótaggal a névben.
       - Ha a Site Recovery által létrehozott erőforráscsoport már létezik, a rendszer újra felhasználja.
       - Testreszabhatja az erőforráscsoport beállításait.
       - A célként megadott erőforráscsoport helye bármely Azure-régió lehet, kivéve azt a régiót, amelyben a forrásként szolgáló virtuális gépek futnak.
   - **Célként megadott virtuális hálózat**: alapértelmezés szerint a site Recovery egy új virtuális hálózatot hoz létre a célként megadott régióban egy "ASR" utótaggal a névben. Ez a forrás-hálózatra van leképezve, és minden jövőbeli védelemhez használatos. [További](./azure-to-azure-network-mapping.md) információ a hálózati leképezésről.
   - **Cél Storage-fiókok (a forrásoldali virtuális gép nem használ felügyelt lemezeket)**: alapértelmezés szerint a site Recovery egy új célként szolgáló Storage-fiókot hoz létre, amely utánozza a forrás virtuális gép tárolási konfigurációját. Ha a Storage-fiók már létezik, az újra használatban van.
   - **Replika által felügyelt lemezek (a forrásoldali virtuális gép felügyelt lemezeket használ)**: a site Recovery új replika által felügyelt lemezeket hoz létre a céltartományban a forrás virtuális gép felügyelt lemezeit ugyanazzal a tárolási típussal (standard vagy prémium), mint a forrás virtuális gép felügyelt lemezével.
   - **Gyorsítótár-tárolási fiókok**: site Recovery a forrás régióban a cache Storage nevű további Storage-fiókra van szükség. A rendszer a forrásként szolgáló virtuális gépeken végrehajtott összes módosítást nyomon követi, és a gyorsítótár Storage-fiókjába küldi, mielőtt replikálja őket a célhelyre. A Storage-fióknak standard szintűnek kell lennie.
   - **Cél rendelkezésre állási** csoportok: alapértelmezés szerint a site Recovery egy új rendelkezésre állási csoportot hoz létre a céltartományban a név "ASR" utótagja alapján, amely a rendelkezésre állási csoport részét képezi a forrás régióban. Ha a Site Recovery által létrehozott rendelkezésre állási csoport már létezik, a rendszer újra felhasználja.
     >[!NOTE]
     >A cél rendelkezésre állási csoportok konfigurálása közben konfigurálja a különböző rendelkezésre állási csoportokat a különböző méretű virtuális gépekhez. 
     >
   - **Cél rendelkezésre állási zónák**: alapértelmezés szerint a site Recovery ugyanazt a zónát rendeli hozzá a célként megadott régióban, ha a célként megadott régió támogatja a rendelkezésre állási zónákat.

     Ha a célként megadott régió nem támogatja a rendelkezésre állási zónákat, a célként megadott virtuális gépek alapértelmezés szerint önálló példányként vannak konfigurálva. Ha szükséges, úgy is beállíthatja, hogy az ilyen virtuális gépek a célként megadott régióban rendelkezésre állási csoportok részévé legyenek. ehhez kattintson a "Testreszabás" gombra.

     >[!NOTE]
     >A replikáció engedélyezése után nem módosíthatja a rendelkezésre állási típust, a rendelkezésre állási készletet vagy a rendelkezésre állási zónát. A rendelkezésre állási típus módosításához le kell tiltania és engedélyeznie kell a replikálást.
     >

   - **Replikációs házirend**: meghatározza a helyreállítási pontok megőrzési előzményeinek és az alkalmazás konzisztens pillanatkép-gyakoriságának beállításait. Alapértelmezés szerint a Azure Site Recovery egy új replikációs házirendet hoz létre, amely a helyreállítási pontok megőrzéséhez és a "4 óra" alapértelmezett beállításokkal rendelkezik az alkalmazás konzisztens pillanatkép-gyakorisága esetében.

     ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>A további lemezek replikálásának engedélyezése

Ha lemezeket ad hozzá egy olyan Azure-beli virtuális géphez, amelyen engedélyezve van a replikáció, a következők történnek:
-   A virtuális gép replikálási állapota figyelmeztetést jelenít meg, és egy Megjegyzés tájékoztatja arról, hogy egy vagy több lemez is elérhető a védelemhez.
-   Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a lemez kezdeti replikálása után eltűnik.
-   Ha úgy dönt, hogy nem engedélyezi a lemez replikálását, kiválaszthatja, hogy elhagyhatja a figyelmeztetést.


    ![Új lemez hozzáadva](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Egy hozzáadott lemez replikálásának engedélyezéséhez tegye a következőket:

1.  A tárolóban > **replikált elemek** területen kattintson arra a virtuális gépre, amelyhez hozzáadta a lemezt.
2.  Kattintson a **lemezek** elemre, majd válassza ki azt az adatlemezt, amelynek engedélyezni szeretné a replikálását (ezek a lemezek **nem védett** állapottal rendelkeznek).
3.  A **lemez részletei** területen kattintson a **replikáció engedélyezése** elemre.

    ![A további lemez replikálásának engedélyezése](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

A replikációs feladatok futtatásának engedélyezése és a kezdeti replikálás befejezése után a rendszer eltávolítja a lemezre vonatkozó probléma replikálási állapotára vonatkozó figyelmeztetést.



## <a name="customize-target-resources"></a>Cél erőforrások testreszabása

A Site Recovery által használt alapértelmezett cél beállításokat módosíthatja.

1. Az alapértelmezett cél-előfizetés módosításához kattintson a **Testreszabás** elemre a cél előfizetés mellett. Válassza ki az előfizetést az azonos Azure Active Directory (HRE) bérlőben elérhető összes előfizetés listájából.

2. Kattintson a **Testreszabás gombra:** az alapértelmezett beállítások módosításához:
    - A **cél erőforráscsoport** területen válassza ki az erőforráscsoportot az előfizetés célhelyén lévő összes erőforráscsoport listájából.
    - A **cél virtuális hálózat** területen válassza ki a hálózatot a célhelyen lévő összes virtuális hálózat listájából.
    - A **rendelkezésre állási csoport** lehetőséget ad a rendelkezésre állási csoport beállításainak hozzáadására a virtuális géphez, ha azok egy rendelkezésre állási csoport részét képezik a forrás régióban.
    - A **cél Storage-fiókok** területen válassza ki a használni kívánt fiókot.

        ![A cél-előfizetési beállítások testreszabását bemutató képernyőkép.](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. A replikációs beállítások módosításához kattintson a **Testreszabás** elemre.
4. A **több virtuális gépre kiterjedő konzisztencia** területen válassza ki azokat a virtuális gépeket, amelyeket együtt szeretne replikálni.
    - A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd.
    - A több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a munkaterhelés teljesítményére (mivel ez a CPU-igényes). Ez csak akkor engedélyezhető, ha a gépek ugyanazt a számítási feladatot futtatják, és több gépen is konzisztencia szükséges.
    - Ha például egy alkalmazás 2 SQL Server virtuális géppel és két webkiszolgálóval rendelkezik, akkor csak a SQL Server virtuális gépeket kell felvennie egy replikációs csoportba.
    - Megadhatja, hogy legfeljebb 16 virtuális gép legyen egy replikációs csoportban.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
    - Győződjön meg arról, hogy nincs olyan tűzfalszabály, amely blokkolja a virtuális gépek közötti belső kommunikációt a 20004-as porton keresztül.
    - Ha azt szeretné, hogy a Linux rendszerű virtuális gépek egy replikációs csoport részévé legyenek, győződjön meg arról, hogy az 20004-as porton a kimenő forgalom manuálisan nyílik meg az adott Linux-verzió útmutatása szerint.
![A több virtuális gépre kiterjedő konzisztencia-beállításokat bemutató képernyőkép.](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Kattintson a **cél erőforrás létrehozása**  >  **replikáció engedélyezése** lehetőségre.
6. Miután a virtuális gépek engedélyezve vannak a replikáláshoz, a virtuális gép állapotának állapotát a **replikált elemek** területen tekintheti meg

>[!NOTE]
>
> - A kezdeti replikálás során az állapot frissítés nélkül is eltarthat egy ideig. Kattintson a **frissítés** gombra a legutóbbi állapot lekéréséhez.
> - Ha egy helyreállítási pont nem lett létrehozva az elmúlt 60 percben, a virtuális gép replikációs állapota kritikus lesz.

## <a name="next-steps"></a>Következő lépések

[További](site-recovery-test-failover-to-azure.md) információ a feladatátvételi teszt futtatásáról.
