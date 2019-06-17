---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy tesztkörnyezetet osztálytermi használatra.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 803fe6eff8804dbd407642386865fe975c8db524
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123259"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Felhasználók hozzáadása a labor létrehozása
> * Regisztrációs hivatkozás küldése a diákoknak

## <a name="prerequisites"></a>Előfeltételek
Osztályterem-tesztkörnyezet tesztkörnyezetfiók beállítása, egyik a labor-fiókban lévő szerepkör tagjának kell lennie: Tulajdonos, tesztkörnyezet létrehozója vagy közreműködő. A lab-fiók létrehozásához használt fiók automatikusan hozzáadódik a tulajdonosi szerepkör.

A lab tulajdonosa adhat hozzá más felhasználók számára a **tesztkörnyezet létrehozója** szerepkör. Például egy tesztkörnyezet tulajdonos abból hozzáadja a tesztkörnyezet létrehozója szerepkör. Ezt követően az abból labort létrehozni a virtuális gépek azok osztályok. Diákok használja abból a laborhoz regisztrálni által kapott regisztrációs hivatkozást. Regisztrálva vannak, ha azok használatával virtuális gépek a Labs-környezetben hajtsa végre az osztály és a kezdőlap munkát. A felhasználók hozzáadása a tesztkörnyezet létrehozója szerepkör részletes lépéseiért lásd: [felhasználó hozzáadása a tesztkörnyezet létrehozója szerepkör](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Adja meg a maximális **virtuális gépek száma** a tesztkörnyezetben. Növelheti vagy decreate a virtuális gépek száma, a tesztkörnyezet létrehozása után, vagy egy meglévő lab-ben. További információkért lásd: [a labor virtuális gépek számának frissítése](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válasszon **méretet** a tesztkörnyezetben létrehozott virtuális gépeknek. Jelenleg **kis**, **Közepes**, **közepes (virtualizálási)** , **nagy**, és **GPU** méretek a következők engedélyezett.
    3. Válassza ki a tesztkörnyezetben a virtuális gépek létrehozásához használni kívánt **virtuálisgép-rendszerképet**. Ha egy Linuxos rendszerképet választja, megjelenik egy lehetőség, a távoli asztali kapcsolat engedélyezése. További információkért lásd: [Linux esetén a távoli asztali kapcsolat engedélyezése](how-to-enable-remote-desktop-linux.md).
    4. Kattintson a **Tovább** gombra.

        ![Virtuális gép specifikációinak megadása](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. A **Hitelesítő adatok beállítása** oldalon adja meg az alapértelmezett hitelesítő adatokat a tesztkörnyezet összes virtuális gépéhez. 
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Miután a sablon konfigurálása befejeződött, az alábbi oldal jelenik meg: 

    ![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az a **konfigurálása sablon** lapon, tegye a következőket: Ezeknek a lépéseknek **választható** -t az oktatóanyaghoz.
    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Ha Linux rendszerű virtuális gép sablon, kiválaszthatja, hogy szeretné-e az SSH és a RDP-(ha az RDP engedélyezve van).
    1. Válassza ki **jelszó alaphelyzetbe állítása** a jelszót a virtuális gép számára. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
9. Válassza ki a **Tovább** gombot a sablon oldalán. 
10. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. Jelölje be a sablon azonnali közzétételének **közzététel**.  

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló befejezése után a Virtuálisgép-sablon teheti közzé. Konfigurálása és közzététele a varázsló befejezése után a részletekért lásd: [a sablon közzététele](how-to-create-manage-template.md#publish-the-template-vm) című rész a [osztályterem-tesztkörnyezetek kezelése](how-to-manage-classroom-labs.md) cikk.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **virtuális gépek** oldalán válassza a bal oldali menüben lévő virtuális gépek vagy a virtuális gépek csempe kiválasztásával. Győződjön meg arról, hogy a virtuális gépeket **nincs hozzárendelve** állapota. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a labor létrehozása

1. Válassza ki **felhasználók** a bal oldali menüben. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás ki kapcsolva, a felhasználó nem regisztrálja a labor létrehozása akkor is, ha a felhasználó nem rendelkezik a regisztráció hivatkozásra, ha a felhasználó szerepel a felhasználók listáját. Csak a listán szereplő felhasználók regisztrálhatja az a labor létrehozása a regisztrációs hivatkozással küld. Ezzel az eljárással, hozzá felhasználókat a listából. Másik megoldásként kikapcsolhatja a **hozzáférés korlátozása**, lehetővé teszi a felhasználók is rendelkeznek a regisztráció hivatkozásra a labor regisztrálni. 
2. Válassza ki **felhasználók hozzáadása** az eszköztáron. 

    ![Felhasználók gomb hozzáadása](../media/how-to-configure-student-usage/add-users-button.png)
1. Az a **felhasználók hozzáadása** lap, adja meg a felhasználók e-mail címét, külön sorban, vagy egyetlen sorban, egymástól pontosvesszővel elválasztva. 

    ![Adja hozzá a felhasználó e-mail-címei](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. Láthatja, hogy a felhasználók és azok állapotát (vagy nem regisztrált) a lista e-mail címét. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára
Felhasználónként kvóták az alábbi lépéseket követve állíthatja be: 

1. Válassza ki **felhasználók** a bal oldali menüben, ha az oldal már nem aktív. 
2. Válassza ki **felhasználónként kvóta:** az eszköztáron. 
3. Az a **felhasználónként kvóta** csoportjában adja meg kíván adni, az egyes felhasználók (student) órák száma: 
    1. **0 óra (csak ütemezés)** . Felhasználók a saját virtuális gépek csak a megadott időszakban, vagy ha a labor tulajdonosa, kapcsolja be a virtuális gépek a számukra.

        ![Nulla óra – csak az ütemezett időpont](../media/how-to-configure-student-usage/zero-hours.png)
    1. **Felhasználónként labor órák számának**. Felhasználók virtuális gépeiken használhatják a meghatározott számú (ebben a mezőben megadott) óra **mellett az ütemezett időpont**. Ha ezt a lehetőséget választja, adja meg a **órák száma** a szövegmezőben. 

        ![Felhasználónként órák száma](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Kattintson a **Mentés** gombra. 
5. Módosított értéke most már az eszköztáron látható: **Felhasználói kvóta: &lt;órák száma&gt;** . 

    ![Kvóta felhasználó szerint](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>A labor ütemezés beállítása
Ha konfigurálta a felhőkvóta-beállítást a **0 óra (csak ütemezés)** , be kell egy ütemezést a tesztkörnyezethez. Ebben az oktatóanyagban az ütemezés hetente ismétlődő ütemezés szerint kell beállítani.

1. Váltson a **ütemezések** lapon, és válassza ki **Hozzáadás ütemezés** az eszköztáron. 

    ![Az ütemezések lapon ütemezés gomb hozzáadása](../media/how-to-create-schedules/add-schedule-button.png)
2. Az a **Hozzáadás ütemezés** lapon, váltson **heti** tetején. 
3. A **ütemezett nap (kötelező)** , válassza ki azokat a napokat, amelyeken az ütemezés érvénybe szeretné. A következő példában hétfőtől péntekig van kiválasztva. 
4. Az a **a** mezőbe írja be a **ütemezett kezdési dátum** válasszon dátumot kiválasztásával, vagy a **naptár** gombra. Ez a mező kitöltése kötelező. 
5. A **az ütemezéshez záró dátumot**adja meg vagy válassza ki a befejező dátum, amelyen a virtuális gépek vannak, le kell állítani. 
6. A **kezdési idő**, válassza ki az időpontot, amelyeknél szeretné elindítani a virtuális gépeket. A kezdési időpontját kötelező megadni, ha nincs beállítva a leállítási ideje. Válassza ki **Remove esemény indítása** Ha meg szeretné határozni a csak a leállítási ideje. Ha a **kezdési idő** van válassza le van tiltva, **Hozzáadás indítása esemény** mellett a legördülő listából válassza ki az engedélyezéshez. 
7. A **leállítási ideje**, válassza ki az idő, amelyeknél szeretné a virtuális gépek leállítását. A leállási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza ki **Remove leállási esemény** Ha adja meg a kezdő időpont csak szeretné. Ha a **leállítási ideje** van válassza le van tiltva, **Hozzáadás leállási esemény** mellett a legördülő listából válassza ki az engedélyezéshez.
8. A **(kötelező) időzóna**, jelölje be a kezdő időzónáját, és állítsa le a megadott időpontok.  
9. A **megjegyzések**, adja meg az ütemezés bármely leírása, illetve a megjegyzések. 
10. Kattintson a **Mentés** gombra. 

    ![Heti ütemezés](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>A regisztrációs hivatkozást tartalmazó e-mail küldése

1. Váltson a **felhasználók** megtekintheti, ha nem az oldalon már. 
2. Válassza ki a specifikus vagy minden felhasználó a listán. Meghatározott felhasználóknak kínált, jelölje be a jelölőnégyzeteket a lista első oszlopában. Minden felhasználó kijelöléséhez jelölje be az első oszlop a cím elé (**neve**), vagy válassza ki az összes felhasználó számára az összes jelölőnégyzetet a listából. Megtekintheti az állapotát a **meghívó állapot** ebben a listában.  Az alábbi ábrán a diákok számára meghívó állapot értéke **meghívó nincs elküldve**. 

    ![Tanulók kiválasztása](../media/tutorial-setup-classroom-lab/select-students.png)
1. Válassza ki a **e-mail ikonra (boríték)** az egyik a sorok (vagy) válasszon **meghívó küldése** az eszköztáron. Az egér fölé az e-mailek ikon jelenik meg a listában lévő tanulói nevét is. 

    ![Regisztráció hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. Az a **regisztrációs-hivatkozás küldése e-mailben** lapon, kövesse az alábbi lépéseket: 
    1. Írja be egy **üzenetet** , amely elküldi a tanulók számára. Az e-mail automatikusan a regisztrációs hivatkozást tartalmaz. 
    2. Az a **regisztrációs-hivatkozás küldése e-mailben** lapon jelölje be **küldése**. Meghívó módosítása a következőre állapotának megtekintéséhez **meghívó küldése** majd **meghívó elküldve**. 
        
        ![Küldött meghívások](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy osztályterem-tesztkörnyezetet, és konfigurálta azt. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

