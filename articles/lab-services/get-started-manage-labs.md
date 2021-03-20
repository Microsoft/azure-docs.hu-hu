---
title: Ismerkedés a Azure Lab Services
description: Ez a cikk bemutatja, hogyan kezdheti meg a Azure Lab Services használatát.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165042"
---
# <a name="get-started-with-lab-services"></a>Ismerkedés a labor Services szolgáltatással 

Azure Lab Services a diákok és a tanárok számára közvetlenül a saját számítógépeiről biztosít hozzáférést a Virtual Computer Labs szolgáltatáshoz.

A tanároknak ismerniük kell, hogyan tanítják meg a diákokat és a szülőket, hogy a laboratóriumi szolgáltatásokat a tanulók egy-az-egyhez által kiadott hardver használatával használják. Ennek eredményeképpen a tanulók számára elérhetővé válik az iparági szabványnak megfelelő szoftver, amely a Virtual Machines (VM) használatával végzett tanulmányi programokhoz szükséges. 

A virtuális gép virtuális számítógépként működő virtuális környezet. A virtuális gépek saját processzorral, memóriával és tárterülettel rendelkeznek. A virtuális gépek helyettesíthetik a valódi gépet, és hozzáférést biztosíthatnak a felhasználóknak az operációs rendszerekhez és a szoftverekhez anélkül, hogy azokat a saját eszközén kellene megadniuk. A Azure Lab Services eszközzel a tanulók hozzáférhetnek a virtuális gépekhez, és megkereshetik azokat, és a munkatársak kezelhetik a Virtual Computer Labs szolgáltatást. 

Ez a cikk tájékoztatást nyújt a tanulók és a szülők számára a Azure Lab Services használatához való hozzáféréshez, kezeléséhez és megtanításához.

## <a name="key-concepts"></a>Fő fogalmak

### <a name="quota-hours"></a>Kvóta órája

A tanulók az ütemezett osztályok ideje alatt bármikor hozzáférhetnek a virtuális gépekhez, anélkül, hogy ez a kvóta órája lenne. A kvóta órája a teljes félévre van beállítva, és meghatározza, hogy a tanulók hány óra alatt használhatják a virtuális gépet a rendszeresen ütemezett osztályon kívül.

heti 8 óra, alaphelyzetbe állítása vasárnap – nem kumulatív.

További információt a [kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)című témakörben talál.

### <a name="automatic-shut-down"></a>Automatikus leállítás

A költségek megtartása és a tanulók kvótájának eltakarítása érdekében az automatikus leállítások engedélyezve vannak a laborokban. Az automatikus leállítások kikapcsolják a virtuális gépeket egy tétlenségi idő után (nincs egér vagy billentyűzet bemenet). Az automatikus leállítások két lépésben működnek, először a tanulók a virtuális gép leválasztása után egy ideig inaktivitás után le lesznek választva. Ezen a ponton a virtuális gép továbbra is **fut** , és a tanulók képesek csatlakozni. A leválasztást követően egy másik inaktivitási időszak után a virtuális gép le fog állni.

Az automatikus leállítások fontos költségmegtakarítási eszköznek minősülnek, azonban a tanulók számára kihívást jelentenek a munkájuk megtakarítása és a nagyméretű projektfájlok megjelenítése tekintetében. Ha a tanulók gyakran le vannak választva, vagy túl gyorsan kikapcsolják a virtuális gépeket, forduljon a TÁBLAKIFEJEZÉSEK-rendszergazdához. 

További információ: [virtuális gépek automatikus leállításának beállítása labor-fiókhoz](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Virtuális gépek kezelése

A labor kezelése lehetővé teszi a tanárok számára, hogy a laboratóriumi kapacitást (a tanulók számára elérhető virtuális gépek számát) és a virtuális gépek manuális indítását, leállítását vagy alaphelyzetbe állítását vezérelje. a tanárok a virtuális gépekhez is csatlakozhatnak a tanulói felület, a fájlok elérése és a szoftverekkel vagy a virtuális géppel kapcsolatos hibák elhárítása érdekében.

A virtuális gépek kezelése során a legfontosabb megjegyezni, hogy a gép bármikor **fut**. a költségek akkor is felmerülnek, ha senki sem csatlakozik a virtuális géphez.

## <a name="lab-dashboards"></a>Labor-irányítópultok

### <a name="overview"></a>Áttekintés

A Azure Lab Services Labs-irányítópultok az adott labor különböző szempontjait, például a virtuálisgép-információkat, a hozzárendelt és a nem hozzárendelt virtuális gépek számát, a regisztrált és a nem regisztrált felhasználók számát, valamint a labor-ütemtervekkel kapcsolatos információkat tartalmaznak. 

> [!NOTE]
> Noha az irányítópult és a [Azure Lab Services webhely](https://labs.azure.com/) legtöbb felügyeleti aspektusa látható lesz a tanárok számára, a szerepkörre vonatkozó engedélyek befolyásolhatják az irányítópult bizonyos feltételeinek módosítását. Ha probléma merül fel az adott labor beállításakor, forduljon a TÁBLAKIFEJEZÉSEK-rendszergazdához.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="a Azure Lab Services portál":::

### <a name="examine-a-dashboard"></a>Irányítópult vizsgálata

1. Navigáljon a [Azure Lab Services webhelyére](https://labs.azure.com/), és jelentkezzen be.
1. Válassza ki a labort.
1. Ekkor megjelenik egy **irányítópult** az ablak bal oldalán. Kattintson az **irányítópultra** , és az irányítópulton több csempét fog látni.
1. A **költségek & a számlázási** csempén a sablonok, a virtuálisgép-készletek, a felhasználók és az ütemtervek csempéi is vannak, amelyek lehetővé teszik a szempontok módosítását és további részletek megtekintését az osztályterem laborban.

    * Sablon – leírja a sablon létrehozásának és utolsó közzétételének dátumát. 
    * Virtuálisgép-készlet – a hozzárendelt és a hozzá nem rendelt virtuális gépek száma.
    * Felhasználók – a laborhoz hozzáadott regisztrált felhasználók és felhasználók száma, de nincs regisztrálva.
    * Ütemezések – megjeleníti a labor közelgő ütemezett eseményeit, valamint egy hivatkozást a további események megtekintéséhez.

További információ: [az irányítópult használata](use-dashboard.md).

### <a name="manually-starting-vms"></a>Virtuális gépek manuális elindítása

1. A virtuálisgép-készlet lapon az összes **virtuális gép** elindítható egy laborban az oldal tetején található **összes elindítása** gombra kattintva.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Virtuális gépek indítása":::
1. Az egyes virtuális gépeket az állapot váltógomb kiválasztásával indíthatja el. 

    A váltógomb a virtuális **gép indításakor indul el,** majd a virtuális gép elindítása után **fut** .
1. A **Name (név** ) oszlop bal oldalán található ellenőrzésekkel is kiválaszthatja a virtuális gépek számát. 

    Miután kiválasztotta a kívánt virtuális gépeket, kattintson a képernyő tetején található **Start** gombra.
1. A kezdést követően az összes virtuális gép leállításához kattintson az **összes leállítása** gombra.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Virtuális gépek leállítása":::

### <a name="stopping-and-resetting-vms"></a>Virtuális gépek leállítása és alaphelyzetbe állítása

* Az egyes virtuális gépeket az állapot váltására kattintva állíthatja le.
* Az ellenőrzések segítségével több virtuális gépet is leállíthat, a képernyő tetején pedig a "Leállítás" gombra kattintva.

Ha egy tanuló nehézségekbe ütközik a virtuális géphez való csatlakozás során, vagy a virtuális gépet más okból vissza kell állítani, használhatja az Alaphelyzetbe állítás funkciót.
1. Egy vagy több virtuális gép alaphelyzetbe állításához jelölje ki őket a csekkek használatával, majd kattintson az oldal tetején található **Alaphelyzetbe állítás** gombra.
1. Az előugró ablakban kattintson az **Alaphelyzetbe állítás** elemre.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="A virtuális gép alaphelyzetbe állítása":::

    > [!NOTE]
    > A tanulói virtuális gépek bekapcsolása nem befolyásolja a tanulóra vonatkozó kvótát. A felhasználók kvótái a felhasználó számára az ütemezett osztály időpontján kívül elérhető labor-órák számát határozzák meg.

### <a name="connect-to-vms"></a>Kapcsolódás virtuális gépekhez

A tanárok képesek csatlakozni a tanulói virtuális géphez, amíg be van kapcsolva, és a tanuló nem csatlakozik a virtuális géphez. A virtuális géphez való csatlakozással hozzáférhet a virtuális gépen található helyi fájlokhoz, és segítséget nyújthat a tanulóknak a problémák elhárításában.

1. A tanuló virtuális géphez való kapcsolódáshoz vigye az egeret a listából a virtuális gépre, és kattintson a **Kapcsolódás** gombra. 
1. Ezután kövesse az első lépések útmutató tanulók számára Chromebook, Mac vagy PC-ket

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Kapcsolódás tanulói virtuális géphez gomb":::

## <a name="manage-users-in-a-lab"></a>Tesztkörnyezetben lévő felhasználók kezelése

A tanárok hozzáadhatnak tanuló felhasználókat a laborhoz, és megfigyelheti az óránkénti kvótákat. A felhasználók e-mail-cím vagy számolótábla-lista használatával történő hozzáadásával és a felhasználók regisztrálásával kapcsolatos további információkért lásd: [labor-felhasználók hozzáadása és kezelése](how-to-configure-student-usage.md).

Miután megkérte a felhasználókat, vagy megosztotta a hivatkozást, megfigyelheti, hogy mely felhasználók regisztrálása sikeresen megtörtént a **felhasználók** lapon az **állapot** oszlopban. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az ebben a rövid útmutatóban létrehozott erőforrások használatát, törölje az erőforrásokat.

## <a name="next-steps"></a>Következő lépések

[Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
