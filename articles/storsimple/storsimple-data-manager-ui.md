---
title: Microsoft Azure StorSimple Data Manager felhasználói felület
description: Ebből a témakörből megtudhatja, hogyan alakíthatja át a StorSimple 8000 Series-eszközökön található adatStorSimple Data Manager felhasználói felületét.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94957928"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>A StorSimple Data Manager szolgáltatás kezelése Azure Portal

Ez a cikk azt ismerteti, hogyan használhatja a StorSimple Data Manager felhasználói felületét a StorSimple 8000 Series-eszközökön tárolt adatátalakításhoz. Az átalakított adatmennyiségeket más Azure-szolgáltatások, például az Azure Media Services, az Azure HDInsight, az Azure Machine Learning és az Azure Cognitive Search is felhasználhatják.


## <a name="use-storsimple-data-transformation"></a>Az StorSimple-adatátalakítás használata

A StorSimple Data Manager az az erőforrás, amelyen belül az adatátalakítás példánya található. Az Adatátalakítási szolgáltatás lehetővé teszi az adatok StorSimple formátumból natív formátumba való átalakítását blobokban vagy Azure Filesban. A StorSimple natív formátumának átalakításához meg kell adnia a StorSimple 8000 sorozatú eszköz adatait és az átalakítandó érdeklődési adatokat.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple Data Manager szolgáltatás létrehozása

StorSimple Data Manager szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiók hitelesítő adataival.

2. Kattintson **az + erőforrás létrehozása** lehetőségre, és keressen rá StorSimple Data Manager.

    ![StorSimple Data Manager szolgáltatás létrehozása 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kattintson a StorSimple Data Manager, majd a **Létrehozás** gombra.
    
    ![StorSimple Data Manager szolgáltatás létrehozása 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Az új szolgáltatás esetében a következőt kell megadnia:

   1. Adjon egyedi **szolgáltatásnevet** a StorSimple Data Manager számára. Ez a szolgáltatás azonosítására szolgáló rövid név. A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

   2. Válasszon egy **előfizetést** a legördülő listából. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező automatikusan ki van töltve (és nem választható), ha csak egy előfizetéssel rendelkezik.

   3. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](../azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Adja meg a Storage-fiókokat és a StorSimple Data Manager szolgáltatást tároló szolgáltatás **helyét** . A StorSimple Eszközkezelő szolgáltatásra, Data Manager szolgáltatásra és a társított Storage-fiókra a támogatott régiókban kell lennie.
    
   5. Ha a szolgáltatásra mutató hivatkozást szeretne kapni az irányítópulton, válassza a **rögzítés az irányítópulton** lehetőséget.
    
   6. Kattintson a **Létrehozás** lehetőségre.

      ![StorSimple Data Manager szolgáltatás létrehozása 3](./media/storsimple-data-manager-ui/create-service-4.png)

A szolgáltatás létrehozása eltarthat néhány percig. Ekkor megjelenik egy értesítés a szolgáltatás sikeres létrehozása után, és megjelenik az új szolgáltatás.

### <a name="create-a-data-transformation-job-definition"></a>Adatátalakítási feladat definíciójának létrehozása

Egy StorSimple Data Manager szolgáltatáson belül létre kell hoznia egy Adatátalakítási feladatsort. A feladatdefiníció adja meg azokat a StorSimple adatokat, amelyeket a Storage-fiók natív formátumban való áthelyezése érdekli. A feladatdefiníció létrehozása után a feladatot újra futtathatja a különböző futtatókörnyezet-beállításokkal.

A feladatdefiníció létrehozásához hajtsa végre az alábbi lépéseket.

1. Navigáljon a létrehozott szolgáltatáshoz. Lépjen a **felügyeleti > feladatok definíciói** között.

2. Kattintson a **+ feladatdefiníció** elemre.

    ![Kattintson a + feladatdefiníció](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Adja meg a feladatdefiníció nevét. A név 3 – 63 karakter hosszúságú lehet. A név kis-és nagybetűket, számokat és kötőjeleket is tartalmazhat.

4. Adja meg a feladatot futtató helyet. Ez a hely eltérő lehet a szolgáltatás központi telepítésének helyétől.

5. A forrás-adattár megadásához kattintson a **forrás** lehetőségre.

    ![Forrásoldali adattárház konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Mivel ez egy új Data Manager szolgáltatás, az adattárak nincsenek konfigurálva. Az **adatforrás konfigurálása** lapon adja meg a StorSimple 8000 sorozatú eszköz és az érintett adatok részleteit.

   Ha a StorSimple Eszközkezelő adattárházként szeretné felvenni, kattintson az **új hozzáadása** lehetőségre az adattár legördülő menüben, majd kattintson az **adattárház hozzáadása** lehetőségre.

    ![Új adattárház hozzáadása](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Az adattárház típusaként válassza a **StorSimple 8000 Series Manager** elemet.
    
   2. Adjon meg egy rövid nevet a forrás adattár számára.
    
   3. A legördülő listából válassza ki a StorSimple Eszközkezelő szolgáltatáshoz társított előfizetést.
    
   4. Adja meg az **erőforráshoz** Eszközkezelő StorSimple nevét.

   5. Adja meg a **szolgáltatási adattitkosítási** kulcsot a StorSimple Eszközkezelő szolgáltatáshoz. 

      ![Forrásoldali adattárház konfigurálása 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Ha elkészült, kattintson **az OK gombra** . Ezzel menti az adattárt. A StorSimple újrafelhasználása Eszközkezelő más feladatokban anélkül, hogy újra be kellene írni ezeket a paramétereket. Néhány másodperc elteltével az újonnan létrehozott forrás adattárházhoz tartozó **OK** gombra kattinthat a legördülő menüben.

7. Az **adattár** legördülő listájában válassza ki a létrehozott adattárházat. 

   1. Adja meg az StorSimple 8000 sorozatú eszköz nevét, amely tartalmazza a fontos adatmennyiséget.

   2. Adja meg annak a kötetnek a nevét, amely a StorSimple-eszközön található.

   3. A **Filter** alszakaszban adja meg azt a gyökérkönyvtárat, amely a _\MyRootDirectory\Data_ formátumban tartalmazza az Ön számára fontos adatait. Meghajtóbetűjelek, például a _\C: a \Data_ nem támogatottak. Itt is hozzáadhat szűrőket.

   4. Az Adatátalakítási szolgáltatás csak az Azure-ba küldött adatok legújabb pillanatképén működik.

   5. Kattintson az **OK** gombra.

      ![Forrásoldali adattárház konfigurálása 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Ezután meg kell adni a célként megadott adattárat. Válassza ki a Storage-fiókok lehetőséget, hogy a fájlokat a fiókba helyezze a blobba. A legördülő menüben válassza az **új hozzáadása** , majd a **beállítások konfigurálása** lehetőséget.

9. Válassza ki a hozzáadni kívánt cél tárház típusát és az adattárhoz társított egyéb paramétereket.

    Ha kijelöl egy tárolási fióktípus célját, megadhat egy rövid nevet, egy előfizetést (válassza a szolgáltatás vagy más) és egy Storage-fiók nevét.
        ![A TARGET adattárház konfigurálása 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    A rendszer a feladatok futtatásakor létrehoz egy tárolási várólistát. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével.
    
10. Az adattár hozzáadása után várjon néhány percet.
    
    1. Válassza ki a célként létrehozott tárházat a **cél fiók neve** legördülő listából.

    2. Válassza ki a tároló típusát blobként vagy fájlként. Adja meg annak a tárolási tárolónak a nevét, ahol az átalakított adat található. Kattintson az **OK** gombra.

        ![A TARGET adattárház Storage-fiókjának konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Azt is megteheti, hogy a feladatok futtatása előtt a feladatok időtartamára vonatkozó becslést is megtekintheti. Kattintson az **OK** gombra a feladatdefiníció létrehozásához. A feladatdefiníció már befejeződött. Ezt a feladatdefiníció többször is használhatja a felhasználói felületen a különböző futtatókörnyezeti beállításokkal.

    ![Feladatdefiníció befejezése](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Az újonnan létrehozott feladatdefiníció hozzá lett adva a szolgáltatáshoz tartozó feladatdefiníciók listájához.

### <a name="run-the-job-definition"></a>A feladatdefiníció futtatása

Ha az StorSimple-ből a feladatdefiníció által megadott Storage-fiókba kell áthelyeznie az adatait, futtatnia kell azt. Futásidőben egyes paramétereket különbözőképpen lehet megadni. Ennek lépései a következők:

1. Válassza ki a StorSimple Data Manager szolgáltatást, és lépjen a **felügyeleti > feladatok definíciói** között. Jelölje ki, majd kattintson a futtatni kívánt feladatdefiníció elemre.
     
     ![Indítsa el az 1. feladatot](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kattintson a **Futtatás most** lehetőségre.
     
     ![Indítsa el a 2. feladatot](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kattintson a **Beállítások futtatása** lehetőségre a feladatok futtatásához módosítani kívánt beállítások módosításához. Kattintson **az OK** gombra, majd kattintson a **Futtatás** gombra a feladatok elindításához.

    ![Indítsa el a 3. futtatási feladatot](./media/storsimple-data-manager-ui/start-job-run3.png)

4. A feladat figyeléséhez lépjen a StorSimple Data Manager **feladatok** elemére. A **feladatok** panelen a figyelés mellett azt is megfigyelheti, hogy a tárolási várólistán hol jelenik meg egy üzenet, amikor egy fájlt áthelyeznek a StorSimple a Storage-fiókba.

    ![A 4. futtatási hely elindítása](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Naplók megtekintése a feladatok befejezését követően

A feladatok befejezése után megtekintheti a feladatok állapotát. A feladatok állapota **sikeres** lehet, **részben sikeres** és **sikertelen volt**. Megtekintheti a sikeresen másolt fájlok listáját és a nem másolható fájlokat. Ezek a felsorolások a **"storsimple-Manager-joblogs"** nevű tárolóban érhetők el a cél Storage-fiókon belül. Ezen a tárolón belül megkeresheti a feladattípus nevével megegyező nevű mappát. Ebben az esetben a rendszer létrehoz egy mappát minden olyan futtatáshoz, amely tartalmazza a listát. Ennek a mappának a neve lesz a feladathoz tartozó GUID-azonosító, amelyet a feladatok részletei lapon lehet beolvasni. Azt is megteheti, hogy a legtöbb esetben a feladatok oldalon található másolási naplókra mutató hivatkozást fog látni.
A mappában két CSV-fájl látható. A **copiedfilelist..** . kezdetű összes fájl tartalmazni fogja a fájlok sikeres másolásának listáját. A **failedfilelist...** értékkel kezdődő összes fájl olyan fájlokat tartalmaz, amelyek nem másolhatók, valamint hibaüzenetet tartalmaznak.


## <a name="next-steps"></a>Következő lépések

[StorSimple Data Manager feladatok elindításához használja a .net SDK-t](storsimple-data-manager-dotnet-jobs.md).