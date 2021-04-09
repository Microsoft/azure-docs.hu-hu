---
title: Labs kezelése Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és konfigurálhat egy tantermi labort, megtekintheti az összes labort, megoszthatja a regisztrációs hivatkozást egy labor felhasználóval, vagy törölhet egy labort.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: db7a2f58f99252a7e5076dd86c37b65bbe8ea37a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434209"
---
# <a name="manage-labs-in-azure-lab-services"></a>Labs kezelése Azure Lab Services 
Ez a cikk a tantermi laborok létrehozását és törlését ismerteti. Azt is bemutatja, hogyan tekintheti meg az összes labort egy labor-fiókban. 

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Az Internet Explorer 11 még nem támogatott. 
1. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Válasszon ki vagy adjon meg egy olyan **felhasználói azonosítót** , amely tagja a **labor Creator** szerepkörnek a labor-fiókban, majd írja be a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
1. Válassza az **új Labor** elemet. 
    
    ![Osztályterem-tesztkörnyezet létrehozása](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    1. Válassza ki az osztályhoz szükséges **virtuális gépek méretét** . Az elérhető méretek listáját a virtuálisgép- [méretek](#vm-sizes) című szakaszban találja. 
    1. Válassza ki az osztályterem laborhoz használni kívánt **virtuálisgép-rendszerképet** . Ha Linux-rendszerképet választ, megjelenik egy lehetőség a **Távoli asztali kapcsolat engedélyezéséhez**. Részletekért lásd: [Távoli asztali kapcsolat engedélyezése Linux](how-to-enable-remote-desktop-linux.md)rendszerhez.

        Ha a labor-fiók tulajdonosának hitelesítő adataival jelentkezett be, megjelenik egy lehetőség, amely lehetővé teszi több rendszerkép használatát a laborban. További információ: [a rendszerképek engedélyezése a labor létrehozásakor](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Tekintse át az oldalon megjelenő **teljes óradíjat** . 
    1. Kattintson a **Mentés** gombra.

        ![Képernyőkép, amely az "új Labor" ablakot jeleníti meg.](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Megtekintheti a tesztkörnyezet helyét, ha a labor-fiók úgy lett konfigurálva, hogy a labor [-létrehozó engedélyezze a labor helyének](allow-lab-creator-pick-lab-location.md) kiválasztását. 
4. A **virtuális gép hitelesítő adatai** lapon a tesztkörnyezet összes virtuális gépe alapértelmezett hitelesítő adatait adhatja meg.
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Ha azt szeretné, hogy a diákok saját jelszavukat állítsanak be, tiltsa le **ugyanazt a jelszót az összes virtuális gép** beállításnál. Ez a lépés **nem kötelező**. 

        A pedagógus dönthet úgy, hogy ugyanazt a jelszót használja a laborban lévő összes virtuális géphez, vagy lehetővé teszi a tanulók számára a virtuális gépek jelszavának beállítását. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows-és Linux-lemezkép esetében engedélyezve van. Ha az **Ubuntu** virtuális gépet választja, akkor ez a beállítás le van tiltva, így a tanulóknak először be kell állítania egy jelszót, amikor első alkalommal jelentkeznek be.  

        ![Új tesztkörnyezet ablak](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Ezután válassza a **tovább** lehetőséget a **virtuális gép hitelesítő adatai** lapon. 
5. A **labor-házirendek** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg az egyes felhasználók számára kiosztott órák számát (az **egyes felhasználók kvótáját**) a laborhoz tartozó ütemezett időpontban kívül. 
    2. A **virtuális gépek automatikus leállításához** válassza ki, hogy szeretné-e automatikusan leállítani a virtuális gépet a felhasználó leválasztásakor. Azt is megadhatja, hogy a virtuális gép mennyi ideig várjon, amíg a felhasználó újra csatlakozik, mielőtt automatikusan leáll. További információ: a [virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor](how-to-enable-shutdown-disconnect.md).
    3. Ezután válassza a **Befejezés** gombot. 

        ![Kvóta az egyes felhasználók számára](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Az alábbi képernyő jelenik meg, amely a sablon virtuális gépek létrehozásának állapotát jeleníti meg. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![A sablon virtuális gép létrehozási állapota](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **sablon** oldalon hajtsa végre a következő lépéseket: ezek a lépések nem **kötelezőek** az oktatóanyaghoz.

    1. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy az SSH-val vagy a grafikus felhasználói felület távoli asztalával szeretne-e csatlakozni.  A GUI távoli asztal használatához további beállításra van szükség. További információ: a [grafikus távoli asztal engedélyezése Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md) .
    1. A virtuális gép jelszavának alaphelyzetbe állításához válassza a **jelszó alaphelyzetbe állítása** lehetőséget. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
9.  A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
10. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel** lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Váltson a **Virtual Machines Pool** lapra a bal oldali menüben, vagy válassza a virtuális gépek csempét. Győződjön meg arról, hogy a nem **hozzárendelt** állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Ezen a lapon a következő feladatokat hajthatja végre (ezeket a lépéseket nem kell végrehajtania az oktatóanyaghoz. Ezek a lépések csak az Ön adataira vonatkoznak.): 
    
    1. A labor kapacitásának módosításához (a laborban lévő virtuális gépek száma) válassza ki a **labor kapacitás** elemet az eszköztáron.
    2. Ha az összes virtuális gépet egyszerre szeretné elindítani, válassza az **összes elindítása** lehetőséget az eszköztáron. 
    3. Egy adott virtuális gép indításához válassza a lefelé mutató nyilat az **állapotban**, majd kattintson a **Start** gombra. Egy virtuális gépet úgy is elindíthat, ha kijelöl egy virtuális gépet az első oszlopban, majd kiválasztja az **Indítás** lehetőséget az eszköztáron.                

### <a name="vm-sizes"></a>A virtuális gépek mérete  

| Méret | Cores | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kicsi | 2 | 3,5 GB | Ez a méret a legmegfelelőbb a parancssorhoz, a webböngésző megnyitásához, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok eléréséhez. |
| Közepes | 4 | 7 GB | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. | 
| Közepes (beágyazott virtualizálás) | 4 | 16 GB | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. <p>Ezt a méretet olyan helyzetekben lehet használni, ahol minden tanulónak több virtuális gépre van szüksége. Az oktatók a beágyazott virtualizálás használatával állíthatnak be néhány kis méretű beágyazott virtuális gépet a virtuális gépen belül. </p> |
| Kis GPU (számítás) | 6 | 56 GB | <p>Ez a méret a legjobb megoldás a nagy számítási igényű és hálózati igényű alkalmazások, például a mesterséges intelligencia és a Deep learning-alkalmazások számára.</p><p>Azure Lab Services automatikusan telepíti és konfigurálja a szükséges GPU-illesztőprogramokat, amikor GPU-rendszerképekkel rendelkező labort hoz létre. </p> | 
| Kis GPU (vizualizáció) | 6 | 56 GB | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. | 
| Nagy | 8 | 16 GB | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott. |
| Nagyméretű (beágyazott virtualizálás) | 8 | 32 GB | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott. Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. |  
| Közepes GPU (vizualizáció) | 12 | 112 GB | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. | 

> [!NOTE]
> Az egyes virtuálisgép-méretek nem jelennek meg a listában a tantermi labor létrehozásakor. A lista a tesztkörnyezet helyének aktuális kapacitása alapján töltődik fel. Ha a labor-fiók létrehozója [lehetővé teszi, hogy a labor-készítők kiválasszák a labor helyét](allow-lab-creator-pick-lab-location.md), próbáljon meg egy másik helyet választani a laborhoz, és ellenőrizze, hogy elérhető-e a virtuális gép mérete. 

## <a name="view-all-labs"></a>Összes tesztkörnyezet megtekintése

1. Navigáljon [Azure Lab Services portálra](https://labs.azure.com).
1. Válassza a **Bejelentkezés** lehetőséget. Válasszon ki vagy adjon meg egy olyan **felhasználói azonosítót** , amely tagja a **labor Creator** szerepkörnek a labor-fiókban, majd írja be a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Ellenőrizze, hogy megjelenik-e az összes labor a kiválasztott labor-fiókban. A labor csempén láthatja a laborban található virtuális gépek számát és az egyes felhasználók kvótáját (az ütemezett időponton kívül).

    ![Minden labor](./media/how-to-manage-classroom-labs/all-labs.png)
1. A felső legördülő listából válassza ki a másik Lab-fiókot. A Labs a kiválasztott labor-fiókban jelenik meg. 

## <a name="delete-a-classroom-lab"></a>Tantermi labor törlése

1. A labor csempén válassza a három pontot (...) a sarokban, majd válassza a **Törlés** lehetőséget. 

    ![Törlés gomb](./media/how-to-manage-classroom-labs/delete-button.png)
1. A törlés folytatásához a **labor törlése** párbeszédpanelen válassza a **Törlés** lehetőséget. 

## <a name="switch-to-another-classroom-lab"></a>Váltás másik osztályterem laborra

Ha át szeretne váltani egy másik tantermi laborra az aktuálisból, válassza ki a labor-fiók felső részén található Labs legördülő listáját.

![Válassza ki a labort a felül lévő legördülő listából.](./media/how-to-manage-classroom-labs/switch-lab.png)

Ehhez a legördülő listából új labort is létrehozhat az **új Labor** használatával. 

> [!NOTE]
> A Labs felügyeletéhez az az. LabServices PowerShell-modult (előzetes verzió) is használhatja. További információ: az az [. LabServices Kezdőlap a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Ha másik labor-fiókra szeretne váltani, válassza a labor fiók melletti legördülő listát, és válassza ki a másik labor-fiókot. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként a hozzáférés Labs](how-to-use-classroom-lab.md)

