---
title: Kiszolgálók az értékeléshez Azure Migrate | Microsoft Docs
description: Útmutatás a kiszolgálók csoportosításához a Azure Migrate szolgáltatással végzett értékelés futtatása előtt.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780854"
---
# <a name="create-a-group-for-assessment"></a>Csoport létrehozása az értékeléshez

Ez a cikk azt ismerteti, hogyan hozhatók létre kiszolgálók az értékeléshez Azure Migrate: felderítés és Értékelés.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz. 

## <a name="grouping-servers"></a>Kiszolgálók csoportosítása

A kiszolgálókat csoportokba gyűjtve megállapíthatja, hogy alkalmasak-e az Azure-ba való áttelepítésre, valamint az Azure méretezési és költségbecslés megszerzésére. A csoportok létrehozása több módon is lehetséges:

- Ha ismeri azokat a kiszolgálókat, amelyeket át kell telepíteni, a csoportot manuálisan is létrehozhatja Azure Migrateban.
- Ha nem biztos abban, hogy milyen kiszolgálókat kell csoportosítani, a Azure Migrate függőség vizualizáció funkcióját használhatja a csoportok létrehozásához. 

> [!NOTE]
> A függőségi vizualizáció funkció Azure Governmentban nem érhető el.

## <a name="create-a-group-manually"></a>Csoport létrehozása manuálisan

Létrehozhat egy csoportot is, ha létrehoz egy [értékelést](how-to-create-assessment.md).

Ha manuálisan szeretne létrehozni egy csoportot az értékelés létrehozásán kívül, tegye a következőket:

1. A Azure Migrate projekt > **áttekintése** területen kattintson a **kiszolgálók felmérése és migrálása** elemre. **Azure Migrate: felderítés és értékelés**, kattintson a **csoportok** elemre.
    - Ha még nem adta hozzá a Azure Migrate: Discovery and Assessment Tool eszközt, kattintson ide a hozzáadásához. [További információk](how-to-assess.md).
    - Ha még nem hozott létre Azure Migrate projektet, [További információt itt](./create-manage-projects.md)olvashat.

    ![Csoportok kiválasztása](./media/how-to-create-a-group/select-groups.png)

2. Kattintson a **csoport** ikonra.
3. A **csoport létrehozása** területen adja meg a csoport nevét és a **készülék neve** területen válassza ki a kiszolgáló felderítéséhez használt Azure Migrate készüléket.
4. A kiszolgáló listából válassza ki azokat a kiszolgálókat, amelyeket hozzá szeretne adni a csoporthoz > **létrehozásához**.

    ![Csoport létrehozása](./media/how-to-create-a-group/create-group.png)

Mostantól használhatja ezt a csoportot, amikor [létrehoz egy Azure-beli VM-felmérést](how-to-create-assessment.md) vagy [egy Azure VMware-megoldás (AVS) értékelését](how-to-create-azure-vmware-solution-assessment.md) vagy [egy Azure SQL-értékelést](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Csoport szűkítése függőségi leképezéssel

A függőségek leképezése segítséget nyújt a kiszolgálók közötti függőségek megjelenítéséhez. Általában függőségi leképezést használ, ha a magasabb szintű megbízhatósággal rendelkező kiszolgálócsoportok értékelésére van szükség.
- Az értékelés futtatása előtt segítséget nyújt a kiszolgálók függőségeinek ellenőrzéséhez. 
- Emellett segít az Azure-ba való Migrálás hatékony megtervezésében azáltal, hogy nem marad a háttérben, és így elkerülhetők a meglepő kimaradások az áttelepítés során.
- Felderítheti azokat a kölcsönösen függő rendszereket, amelyeknek együtt kell áttérniük, és meg kell határozniuk, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt.

Ha már [beállította a függőségi leképezést](how-to-create-group-machine-dependencies.md), és egy meglévő csoportot szeretne pontosítani, tegye a következőket:

1. A **kiszolgálók** lap **Azure Migrate: felderítés és értékelés** csempén kattintson a **csoportok** elemre.
2. Kattintson arra a csoportra, amelyet szeretne pontosítani.
    - Ha még nem állította be a függőségi leképezést, akkor a **függőségek** oszlopban a **szükséges telepítési** állapot jelenik meg. Az egyes kiszolgálók esetében, amelyekhez függőségeket kíván megjeleníteni, kattintson a **telepítés szükséges** elemre. Telepítsen néhány ügynököt az egyes kiszolgálókon, mielőtt le tudja képezni a kiszolgálói függőségeket. [További információk](how-to-create-group-machine-dependencies.md).

        ![Függőségi leképezés hozzáadása](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Ha már beállította a függőségi leképezést, a csoport lapon kattintson a **függőségek megtekintése** elemre a csoport függőségi térképének megnyitásához.

3. Miután a **függőségek megtekintése** elemre kattintott, a csoport függőségi térképe a következőket jeleníti meg:

    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a csoport összes olyan kiszolgálójára, amelyeken a függőségi ügynökök telepítve vannak.
    - Azok a függő kiszolgálók, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A telepített függőségi ügynökökkel rendelkező függő kiszolgálók külön mezőkként jelennek meg.
    - A kiszolgálón belül futó folyamatok. Bontsa ki az egyes kiszolgálók panelt a folyamatok megtekintéséhez.
    - Kiszolgáló tulajdonságai (beleértve a teljes tartománynevet, az operációs rendszert, a MAC-címeket). A részletek megtekintéséhez kattintson az egyes kiszolgálók mezőre.

4. Ha egy adott időintervallumban szeretné megtekinteni a függőségeket, akkor a kezdő és a záró dátum, illetve az időtartam megadásával módosítsa az időtartományt (alapértelmezés szerint egy órát).

    > [!NOTE]
    > Az időtartomány akár egy óráig is elvégezhető. Ha hosszabb tartományra van szüksége, használja a [Azure monitort a függő adatlekérdezés hosszabb időtartamra való lekéréséhez](how-to-create-group-machine-dependencies.md) .

5. Miután azonosította a csoportba felvenni vagy eltávolítani kívánt függőségeket, módosíthatja a csoportot. A CTRL + kattintás használatával adhat hozzá vagy távolíthat el kiszolgálókat a csoportból.

    - Csak felderített kiszolgálókat adhat hozzá.
    - A kiszolgálók hozzáadása és eltávolítása érvényteleníti egy csoport múltbeli értékeléseit.
    - Szükség esetén új értékelést is létrehozhat a csoport módosításakor.


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthatja be és használhatja a [függőségi leképezést](how-to-create-group-machine-dependencies.md) a magas megbízhatóságú csoportok létrehozásához.