---
title: Gépek csoportosítása az értékeléshez Azure Migrate | Microsoft Docs
description: Útmutatást nyújt a gépek csoportosításához, mielőtt az értékelést a Azure Migrate szolgáltatással futtatja.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 364b5949e944a4317aa25f1f1b12545122881cec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752107"
---
# <a name="create-a-group-for-assessment"></a>Csoport létrehozása az értékeléshez

Ez a cikk azt ismerteti, hogyan hozhatók létre csoportok az értékeléshez a Azure Migrate: Server Assessment használatával.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz. 

## <a name="grouping-machines"></a>Gépek csoportosítása

A gépeket csoportokba gyűjtve megállapíthatja, hogy alkalmasak-e az Azure-ba való áttelepítésre, valamint az Azure méretezési és költségbecslés megszerzésére. A csoportok létrehozása több módon is lehetséges:

- Ha ismeri azokat a gépeket, amelyeket át kell telepíteni, a csoportot manuálisan is létrehozhatja Azure Migrateban.
- Ha nem biztos abban, hogy a gépeket együtt kell csoportosítani, a Azure Migrate függőség vizualizáció funkcióját használhatja a csoportok létrehozásához. 

> [!NOTE]
> A függőségi vizualizáció funkció Azure Governmentban nem érhető el.

## <a name="create-a-group-manually"></a>Csoport létrehozása manuálisan

Létrehozhat egy csoportot is, ha létrehoz egy [értékelést](how-to-create-assessment.md).

Ha manuálisan szeretne létrehozni egy csoportot az értékelés létrehozásán kívül, tegye a következőket:

1. A Azure Migrate projekt > **áttekintése** területen kattintson a **kiszolgálók felmérése és migrálása** elemre. **Azure Migrate: kiszolgáló értékelése**, kattintson a **csoportok** elemre.
    - Ha még nem adta hozzá a Azure Migrate: Server Assessment eszközt, kattintson ide a hozzáadásához. [További információ](how-to-assess.md).
    - Ha még nem hozott létre Azure Migrate projektet, [További információt itt](./create-manage-projects.md)olvashat.

    ![Csoportok kiválasztása](./media/how-to-create-a-group/select-groups.png)

2. Kattintson a **csoport** ikonra.
3. A **csoport létrehozása** területen adja meg a csoport nevét és a **készülék neve** területen válassza ki a számítógép-felderítéshez használt Azure Migrate készüléket.
4. A számítógép listából válassza ki azokat a gépeket, amelyeket hozzá szeretne adni a csoporthoz > **Létrehozás** gombra.

    ![Csoport létrehozása](./media/how-to-create-a-group/create-group.png)

Mostantól használhatja ezt a csoportot, amikor [létrehoz egy Azure-beli VM-felmérést](how-to-create-assessment.md) vagy [egy Azure VMware-megoldás (AVS) értékelését](how-to-create-azure-vmware-solution-assessment.md). Vegye figyelembe, hogy csak a VMware virtuális gépekkel rendelkező csoportokra vonatkozó AVS-értékelést hozhat létre. 

## <a name="refine-a-group-with-dependency-mapping"></a>Csoport szűkítése függőségi leképezéssel

Függőségek leképezése segít a függőségek megjelenítésében a gépek között. Jellemzően függőségi leképezést használ, ha a magasabb szintű megbízhatóságú számítógépcsoportok értékelésére van szükség.
- Az értékelés futtatása előtt segít a számítógép-függőségek ellenőrzésében. 
- Emellett segít az Azure-ba való Migrálás hatékony megtervezésében azáltal, hogy nem marad a háttérben, és így elkerülhetők a meglepő kimaradások az áttelepítés során.
- Felderítheti azokat a kölcsönösen függő rendszereket, amelyeknek együtt kell áttérniük, és meg kell határozniuk, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt.

Ha már [beállította a függőségi leképezést](how-to-create-group-machine-dependencies.md), és egy meglévő csoportot szeretne pontosítani, tegye a következőket:

1. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson a **csoportok** elemre.
2. Kattintson arra a csoportra, amelyet szeretne pontosítani.
    - Ha még nem állította be a függőségi leképezést, akkor a **függőségek** oszlopban a **szükséges telepítési** állapot jelenik meg. Minden olyan virtuális géphez, amelynek függőségeit szeretné megjeleníteni, kattintson a **telepítés szükséges** elemre. Telepítsen néhány ügynököt az egyes virtuális gépekre, mielőtt le tudja képezni a számítógép-függőségeket. [További információ](how-to-create-group-machine-dependencies.md).

        ![Függőségi leképezés hozzáadása](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Ha már beállította a függőségi leképezést, a csoport lapon kattintson a **függőségek megtekintése** elemre a csoport függőségi térképének megnyitásához.

3. Miután a **függőségek megtekintése** elemre kattintott, a csoport függőségi térképe a következőket jeleníti meg:

    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a csoportban lévő összes olyan gépre, amelyeken telepítve van a függőségi ügynök.
    - Azok a függő gépek, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A telepített függőségi ügynökökkel rendelkező függő gépek külön mezőkként jelennek meg.
    - A gépen futó folyamatok. Bontsa ki az egyes gépek mezőt a folyamatok megtekintéséhez.
    - Számítógép tulajdonságai (beleértve a teljes tartománynevet, az operációs rendszert, a MAC-címeket). A részletek megtekintéséhez kattintson az egyes gépek mezőre.

4. Ha egy adott időintervallumban szeretné megtekinteni a függőségeket, akkor a kezdő és a záró dátum, illetve az időtartam megadásával módosítsa az időtartományt (alapértelmezés szerint egy órát).

    > [!NOTE]
    > Az időtartomány akár egy óráig is elvégezhető. Ha hosszabb tartományra van szüksége, használja a [Azure monitort a függő adatlekérdezés hosszabb időtartamra való lekéréséhez](how-to-create-group-machine-dependencies.md) .

5. Miután azonosította a csoportba felvenni vagy eltávolítani kívánt függőségeket, módosíthatja a csoportot. A CTRL + kattintás használatával hozzáadhat vagy eltávolíthat gépeket a csoportból.

    - Csak a felderített gépeket veheti fel.
    - A gépek hozzáadása és eltávolítása érvényteleníti egy csoport múltbeli értékeléseit.
    - Szükség esetén új értékelést is létrehozhat a csoport módosításakor.


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthatja be és használhatja a [függőségi leképezést](how-to-create-group-machine-dependencies.md) a magas megbízhatóságú csoportok létrehozásához.