---
title: Belső támogatási utasítás hozzáadása laborhoz Azure DevTest Labs
description: Megtudhatja, hogyan tehet közzé belső támogatási utasítást egy laborban Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480473"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Belső támogatási utasítás hozzáadása laborhoz Azure DevTest Labs

Azure DevTest Labs segítségével testre szabhatja a labort egy belső támogatási utasítással, amely támogatást nyújt a felhasználóknak a laborra vonatkozó támogatási információkkal. Például megadhatja a kapcsolattartási adatokat, hogy egy felhasználó tudja, hogyan érheti el a belső támogatást, ha segítségre van szüksége a laborban található erőforrások hibaelhárításához vagy eléréséhez. Olyan belső webhelyekre vagy GYIK-hivatkozásokat is megadhat, amelyekhez a csapat hozzáférhet, mielőtt felvette a kapcsolatot a támogatási szolgálattal.

A belső támogatási nyilatkozat célja, hogy lehetővé tegye, hogy a laboratóriumi információk ne legyenek általában túl gyakran változnak. A felhasználók értesítése az olyan laboratóriumi adatokról, amelyek több átmeneti jellegűek – például a tesztkörnyezet-szabályzatok legutóbbi frissítései – lásd [a hirdetmény közzététele a laborban](devtest-lab-announcements.md)című témakört.

A támogatási nyilatkozatot egyszerűen letilthatja vagy szerkesztheti, miután már nem alkalmazható.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Támogatási utasítások meglévő laborba való felvételének lépései

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza a **minden szolgáltatás** lehetőséget, majd válassza a **DevTest Labs** elemet a listából. (Előfordulhat, hogy a labor már megjelenik az irányítópulton az **összes erőforrás** alatt).
1. A Labs listából válassza ki azt a labort, amelyben támogatási nyilatkozatot szeretne hozzáadni.  
1. A labor **Áttekintés** területén válassza a **konfiguráció és szabályzatok** lehetőséget.  

    ![Konfigurálás és házirendek gomb](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A **Beállítások** területen kattintson a **belső támogatás** elemre.

    ![Belső támogatás gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Ha belső támogatási üzenetet szeretne létrehozni a tesztkörnyezetben lévő felhasználók számára, állítsa az **Igen** értékre.

1. A **támogatási üzenet** mezőben adja meg a labor felhasználói számára bemutatni kívánt belső támogatási nyilatkozatot. A támogatási üzenet elfogadja a Markdown. Az üzenet szövegének megadásakor megtekintheti a képernyő alján található **előnézet** területét, és megtekintheti, hogy az üzenet hogyan jelenik meg a felhasználók számára.

    ![Belső támogatási képernyő az üzenet létrehozásához.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Válassza a **Mentés** lehetőséget, ha a támogatási utasítás készen áll a közzétételre.

Ha már nem szeretné megjeleníteni ezt a támogatási üzenetet a labor felhasználói számára, térjen vissza a **belső támogatás** lapra, és állítsa a **nem** értékre **a beállítást.**

## <a name="steps-for-users-to-view-the-support-message"></a>A támogatási üzenet megtekintésének lépései a felhasználók számára

1. A [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)válassza ki a labort.

1. A labor **Áttekintés** területén válassza a **belső támogatás** lehetőséget.  

    ![Belső támogatás gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Ha a rendszer támogatási üzenetet küld, a felhasználó megtekintheti a belső támogatási panelen.

    ![A támogatási üzenetet bemutató belső támogatási ablaktábla](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
* A belső támogatási utasítások jellemzően olyan támogatási információk biztosítására szolgálnak, amelyek nem változnak gyakran. Azt is megtudhatja, hogyan tehet [közzé egy bejelentést laborba](devtest-lab-announcements.md) , hogy tájékoztassa a felhasználókat az ideiglenes változásokról vagy a tesztkörnyezet frissítéseiről.
* A [szabályzatok és az ütemtervek beállítása](devtest-lab-set-lab-policy.md) információt nyújt arról, hogyan alkalmazhat más korlátozásokat és konvenciókat az előfizetésben testreszabott szabályzatok használatával.