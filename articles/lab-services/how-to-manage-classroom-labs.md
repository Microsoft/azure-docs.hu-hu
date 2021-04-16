---
title: Tesztkörnyezetek kezelése a Azure Lab Services | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és konfigurálhatja az osztályterem-tesztkörnyezetet, hogyan lehet megtekinteni az összes tesztkörnyezetet, hogyan oszthatja meg a regisztrációs hivatkozást egy tesztkörnyezet-felhasználóval, vagy hogyan törölhet tesztkörnyezetet.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c6acb9609abac15b9ff92250e3d5d44c585881cc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481803"
---
# <a name="manage-labs-in-azure-lab-services"></a>Tesztkörnyezetek kezelése a Azure Lab Services 
Ez a cikk bemutatja, hogyan hozhat létre és törölhet osztályterem-tesztkörnyezetet. Azt is bemutatja, hogyan lehet megtekinteni az összes tesztkörnyezetet egy tesztkörnyezetfiókban. 

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com).
1. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Válasszon ki vagy adjon meg **egy**  olyan felhasználói azonosítót, amely a tesztkörnyezet-létrehozó szerepkör tagja a tesztkörnyezetfiókban, és adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
1. Válassza az **Új tesztkörnyezet lehetőséget.** 
    
    ![Osztályterem-tesztkörnyezet létrehozása](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    1. Válassza **ki a osztályhoz szükséges** virtuális gépek méretét. Az elérhető méretek listáját a Virtuálisgép-méretek [című szakaszban](#vm-sizes) láthatja. 
    1. Válassza ki **az** osztályterem-tesztkörnyezethez használni kívánt virtuálisgép-rendszerképet. Ha Linux-rendszerképet választ, láthatja a távoli asztali **kapcsolat engedélyezésének beállítását.** Részletekért lásd: [Távoli asztali kapcsolat engedélyezése Linux rendszeren.](how-to-enable-remote-desktop-linux.md)

        Ha a tesztkörnyezetfiók tulajdonosának hitelesítő adataival jelentkezett be, látni fogja a lehetőséget, hogy további rendszerképeket engedélyez a tesztkörnyezetben. További információ: [Enable images at the time of lab creation (Lemezképek engedélyezése a tesztkörnyezet létrehozásakor).](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    1. Tekintse át **az oldalon megjelenített** óránkénti teljes árat. 
    1. Kattintson a **Mentés** gombra.

        ![Az "Új tesztkörnyezet" ablakot bemutató képernyőkép.](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Lehetőséget lát a tesztkörnyezet helyének kiválasztására, ha a tesztkörnyezetfiók úgy lett konfigurálva, hogy engedélyezze a tesztkörnyezet létrehozója számára a [tesztkörnyezet helyének kiválasztását.](allow-lab-creator-pick-lab-location.md) 
4. A Virtuális **gép hitelesítő adatai lapon** adja meg a tesztkörnyezet összes virtuális gépének alapértelmezett hitelesítő adatait.
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Ha **azt szeretné, hogy** a diákok saját jelszavukat használják, tiltsa le az Ugyanazt a jelszó használata az összes virtuális géphez beállítást. Ez a lépés **nem kötelező**. 

        Az oktatók dönthetnek úgy, hogy ugyanazt a jelszót használják a tesztkörnyezet összes virtuális gépe számára, vagy engedélyezhetik a diákoknak, hogy jelszót állítsanak be a virtuális gépeikhez. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows- és Linux-rendszerképhez engedélyezve van. Ha az Ubuntu VM **lehetőséget** választja, ez a beállítás le van tiltva, így a diákoknak az első bejelentkezéskor meg kell adnia egy jelszót.  

        ![Új tesztkörnyezet ablak](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Ezután válassza **a Tovább** lehetőséget a Virtuális gép hitelesítő **adatai** lapon. 
5. A **Labor-szabályzatok** lapon tegye a következőket:
    1. Adja meg az egyes felhasználók számára kiosztott órák számát **(az** egyes felhasználókra vonatkozó kvótát) a tesztkörnyezet ütemezett időpontán kívül. 
    2. A virtuális gépek automatikus leállítása beállításnál adja meg, hogy **szeretné-e** automatikusan leállítani a virtuális gépet, amikor a felhasználó lecsatlakozik. Azt is megadhatja, hogy a virtuális gép mennyi ideig várjon, amíg a felhasználó újracsatlakozik, mielőtt automatikusan leáll. További információ: Virtuális gépek [automatikus leállításának engedélyezése leválasztáskor.](how-to-enable-shutdown-disconnect.md)
    3. Ezután válassza a **Befejezés** gombot. 

        ![Az egyes felhasználókra vonatkozó kvóta](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. A következő képernyőnek kell látnia, amely a virtuálisgép-sablon létrehozásának állapotát mutatja. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![A virtuálisgép-sablon létrehozásának állapota](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A Sablon **lapon** tegye a következőket: Ezek a lépések nem **kötelezőek** az oktatóanyaghoz.

    1. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Ha linuxos virtuálisgép-sablonról van szó, válassza ki, hogy SSH vagy grafikus felhasználói felületű távoli asztal használatával szeretne-e csatlakozni.  A távoli grafikus felhasználói felületű asztalok használatának további beállítása szükséges. További [információ: Grafikus távoli asztal](how-to-use-remote-desktop-linux-student.md) engedélyezése Linux rendszerű virtuális gépekhez.
    1. Válassza **a Jelszó alaphelyzetbe állítása** lehetőséget a virtuális gép jelszavának alaphelyzetbe állításához. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
9.  A **Sablon lapon** válassza az eszköztár **Közzététel** elemét. 

    ![Sablon közzététele gomb](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
10. A Sablon **közzététele lapon** adja meg a tesztkörnyezetben létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel lehetőséget.** 

    ![Sablon közzététele – virtuális gépek száma](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Itt láthatja **a sablon** közzétételének állapotát az oldalon. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Váltson **a Virtuálisgép-készlet** lapra a bal oldali menü Virtuális gépek lehetőségének vagy a Virtuális gépek csempének a kiválasztásával. Győződjön meg arról, hogy nem hozzárendelt állapotban lévő virtuális **gépeket lát.** Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Ezen az oldalon a következő feladatokat kell elvégeznie (az oktatóanyag lépéseit ne tegye. Ezek a lépések csak a saját adataira vannak felhozva.): 
    
    1. A tesztkörnyezet kapacitásának (a tesztkörnyezetben található virtuális gépek számának) beállítását az eszköztár **Tesztkörnyezet** kapacitása beállítására kattintva módosíthatja.
    2. Ha az összes virtuális gép egyszerre indul el, válassza az **összes indítási lehetőséget** az eszköztáron. 
    3. Egy adott virtuális gép indításhoz kattintson a lefelé mutató nyílra az **Állapot** területen, majd válassza az Indítás **lehetőséget.** A virtuális gépet úgy is elindíthatja, hogy kiválaszt egy virtuális gépet az első oszlopban, majd az eszköztár **Indítás** gombját.                

### <a name="vm-sizes"></a>A virtuális gépek mérete  

| Méret | Cores | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kicsi | 2 | 3,5 GB | Ez a méret a legmegfelelőbb a parancssorhoz, webböngésző megnyitásához, alacsony forgalmú webkiszolgálókhoz, kis és közepes adatbázisokhoz. |
| Közepes | 4 | 7 GB | Ez a méret relációs adatbázisokhoz, memóriában való gyorsítótárazáshoz és elemzésekhez a legalkalmasabb | 
| Közepes (beágyazott virtualizálás) | 4 | 16 GB | Ez a méret relációs adatbázisokhoz, memóriában való gyorsítótárazáshoz és elemzésekhez a legalkalmasabb. Ez a méret támogatja a beágyazott virtualizálást is. <p>Ez a méret olyan forgatókönyvekben használható, ahol minden diáknak több virtuális gépre van szüksége. Az oktatók beágyazott virtualizálással állíthatnak be néhány kis méretű, beágyazott virtuális gépet a virtuális gépen belül. </p> |
| Kis GPU (Compute) | 6 | 56 GB | <p>Ez a méret olyan nagy számítási igényű és hálózatigényes alkalmazásokhoz a legalkalmasabb, mint a mesterséges intelligencia és a mélytanulás.</p><p>Azure Lab Services automatikusan telepíti és konfigurálja a szükséges GPU-illesztőprogramokat a GPU-képekkel létrehozott tesztkörnyezetben. </p> | 
| Kis GPU (vizualizáció) | 6 | 56 GB | Ez a méret távoli vizualizációkhoz, streameléshez, játékokhoz és kódoláshoz a legmegfelelőbb olyan keretrendszerek használatával, mint az OpenGL és a DirectX. | 
| Nagy | 8 | 16 GB | Ez a méret olyan alkalmazásokhoz a legalkalmasabb, amelyek gyorsabb processzorokat, jobb helyi lemezteljesítményt, nagy méretű adatbázisokat és nagy memória-gyorsítótárakat keresnek. |
| Nagy (beágyazott virtualizálás) | 8 | 32 GB | Ez a méret olyan alkalmazásokhoz a legalkalmasabb, amelyek gyorsabb processzorokat, jobb helyi lemezteljesítményt, nagy méretű adatbázisokat és nagy memória-gyorsítótárakat keresnek. Ez a méret támogatja a beágyazott virtualizálást is. |  
| Közepes GPU (vizualizáció) | 12 | 112 GB | Ez a méret távoli vizualizációhoz, streameléshez, játékokhoz, kódoláshoz, például OpenGL és DirectX keretrendszerek használatával a legmegfelelőbb. | 

> [!NOTE]
> Előfordulhat, hogy az osztályterem-tesztkörnyezetek létrehozásakor nem megjelenik néhány ilyen virtuálisgép-méret a listában. A lista a tesztkörnyezet aktuális kapacitásának megfelelő adatokkal lesz feltöltve. Ha a tesztkörnyezetfiók létrehozója lehetővé teszi a tesztkörnyezet létrehozói számára, hogy helyet válasszanak [a](allow-lab-creator-pick-lab-location.md)tesztkörnyezet számára, megpróbálhat másik helyet választani a tesztkörnyezethez, és megnézni, hogy a virtuális gép mérete elérhető-e. 

## <a name="view-all-labs"></a>Összes tesztkörnyezet megtekintése

1. Lépjen a [Azure Lab Services portálra.](https://labs.azure.com)
1. Válassza a **Bejelentkezés** lehetőséget. Válasszon ki vagy adjon meg **egy**  olyan felhasználói azonosítót, amely a tesztkörnyezet-létrehozó szerepkör tagja a tesztkörnyezetfiókban, és adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Győződjön meg arról, hogy a kiválasztott tesztkörnyezetfiókban az összes tesztkörnyezet látható. A tesztkörnyezet csempéjére kattintva láthatja a tesztkörnyezetben lévő virtuális gépek számát és az egyes felhasználókra vonatkozó kvótát (az ütemezett időponton kívül).

    ![Minden tesztkörnyezet](./media/how-to-manage-classroom-labs/all-labs.png)
1. A felső legördülő listában válasszon ki egy másik tesztkörnyezetfiókot. A tesztkörnyezeteket a kiválasztott tesztkörnyezetfiókban láthatja. 

## <a name="delete-a-classroom-lab"></a>Osztályterem-tesztkörnyezet törlése

1. A tesztkörnyezet csempéjére kattintson három pontra (...) a sarokban, majd válassza a **Törlés lehetőséget.** 

    ![Törlés gomb](./media/how-to-manage-classroom-labs/delete-button.png)
1. A **Tesztkörnyezet törlése** párbeszédpanelen válassza a **Törlés lehetőséget** a törlés folytatásához. 

## <a name="switch-to-another-classroom-lab"></a>Váltás másik osztályterem-tesztkörnyezetre

Ha az aktuálisból egy másik osztályterem-tesztkörnyezetre vált, válassza a tesztkörnyezetek legördülő listáját a tesztkörnyezetfiók tetején.

![Válassza ki a labort a felső legördülő listából](./media/how-to-manage-classroom-labs/switch-lab.png)

A legördülő listában található  Új tesztkörnyezet használatával is létrehozhat új tesztkörnyezetet. 

> [!NOTE]
> A tesztkörnyezetek kezeléséhez használhatja az Az.LabServices PowerShell-modult (előzetes verzió). További információkért lásd az [Az.LabServices kezdőlapját a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Másik tesztkörnyezetfiókra váltáshoz válassza a tesztkörnyezetfiók melletti legördülő menüt, és válassza ki a másik tesztkörnyezetfiókot. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezet tulajdonosaként állítsa be és tegye közzé a sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Tesztkörnyezet-felhasználóként hozzáférés a tesztkörnyezethez](how-to-use-classroom-lab.md)

