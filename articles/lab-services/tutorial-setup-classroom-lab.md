---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban a Azure Lab Services használatával állít be egy tantermi labort olyan virtuális gépekkel, amelyeket az osztályban tanulók használnak.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787418"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Felhasználók hozzáadása a laborhoz
> * A laborhoz tartozó ütemterv beállítása
> * Meghívó e-mail küldése a tanulóknak

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban egy labort állít be az osztályhoz tartozó virtuális gépekkel. Ha Lab-fiókban szeretné beállítani a tantermi labort, akkor az egyik szerepkör tagjának kell lennie a labor-fiókban: tulajdonos, labor létrehozó vagy közreműködő. A rendszer automatikusan hozzáadja a tulajdonosi szerepkörhöz a labor-fiók létrehozásához használt fiókot. Így használhatja azt a felhasználói fiókot, amellyel létrehoz egy Lab-fiókot egy osztályterem labor létrehozásához. 

A Azure Lab Services használata esetén a tipikus munkafolyamat:

1. A labor-fiók létrehozója más felhasználókat is felvesz a **labor létrehozói** szerepkörbe. Például a labor-fiók létrehozója/rendszergazdája felveszi az oktatókat a **labor létrehozói** szerepkörbe, hogy a laborokat a saját osztályaik számára is létrehozzák. 
2. Ezután a pedagógusok létrehozhatnak virtuális gépeket a saját osztályaik számára, és regisztrációs hivatkozásokat küldhetnek a tanulóknak az osztályban. 
3. A tanulók az oktatóktól kapott regisztrációs hivatkozást használják a laborba való regisztrációhoz. Ha regisztrálva vannak, a laborokban használhatnak virtuális gépeket az osztály munkahelyi és otthoni működéséhez. 

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása
Ebben a lépésben létrehoz egy labort az osztályhoz az Azure-ban. 

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **új Labor** elemet. 
    
    ![Képernyőfelvétel: "Azure Lab Services", az "új Labor" gomb kiválasztásával.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg a labor **nevét** , majd kattintson a **Tovább gombra**.  

        ![Osztályterem-tesztkörnyezet létrehozása](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. A **virtuális gép hitelesítő adatai** lapon a tesztkörnyezet összes virtuális gépe alapértelmezett hitelesítő adatait adhatja meg. Adja meg a felhasználó **nevét** és **jelszavát** , majd kattintson a **tovább** gombra.  

        ![Új tesztkörnyezet ablak](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. A **labor-házirendek** lapon válassza a **Befejezés** lehetőséget. 

        ![Kvóta az egyes felhasználók számára](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Az alábbi képernyő jelenik meg, amely a sablon virtuális gépek létrehozásának állapotát jeleníti meg. A művelet akár 20 percet is igénybe vehet. 

    ![A sablon virtuális gép létrehozási állapota](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **sablon** oldalon hajtsa végre a következő lépéseket: ezek a lépések nem **kötelezőek** az oktatóanyaghoz.

    1. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy SSH vagy RDP használatával szeretne-e csatlakozni (ha az RDP engedélyezve van).
    3. Telepítse és konfigurálja az osztályhoz szükséges szoftvereket a sablon virtuális gépén. 
    4. **Állítsa le** a sablon virtuális gépet.  

    > [!NOTE]
    > A sablon virtuális gépei a futtatáskor **költségekkel** járnak, ezért győződjön meg arról, hogy a sablon virtuális gépe leáll, ha nincs szükség a futtatására. 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele
Ebben a lépésben közzéteszi a sablon virtuális gépet. Amikor közzéteszi a sablon virtuális gépet, Azure Lab Services létrehozza a virtuális gépeket a laborban a sablon használatával. A virtuális gépek konfigurációja megegyezik a sablonéval.

1. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
2. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel** lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Várjon, amíg a közzététel befejeződik, majd váltson a **Virtual Machines Pool** lapra, ehhez válassza a bal oldali menüben a **virtuális gépek** lehetőséget, vagy a **virtuális gépek** csempét. Győződjön meg arról, hogy a nem **hozzárendelt** állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Ha egy oktató bekapcsol egy tanulói virtuális gépet, az nem érinti a tanulóra vonatkozó kvótát. A felhasználóhoz tartozó kvóta meghatározza, hogy a felhasználó számára hány labor óra legyen elérhető az ütemezett osztály időpontján kívül. A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md?#set-quotas-for-users)számára.

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta (alapértelmezett: 10 óra) az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

1. Váltson az **ütemezések** lapra, és válassza az eszköztár **ütemezett esemény hozzáadása** elemét. 

    ![Képernyőkép az "ütemezett esemény hozzáadása" gombra az "ütemezések" lapon.](./media/how-to-create-schedules/add-schedule-button.png)
2. Az **ütemezett esemény hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Ellenőrizze, hogy a **standard** érték van-e kiválasztva az **esemény típusára**.  
    2. Válassza ki az osztály **kezdő dátumát** . 
    4. Válassza ki a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    5. Válassza ki a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    6. Válassza ki az **időzónát** a megadott kezdési és befejezési időponthoz. 
3. Az **ütemezett esemény hozzáadása** lapon válassza ki az aktuális ütemezést az **ismétlés** szakaszban.  

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](./media/how-to-create-schedules/select-current-schedule.png)
5. A **REPEAT (ismétlés** ) párbeszédpanelen hajtsa végre a következő lépéseket:
    1. Győződjön meg arról, hogy **minden héten** be van állítva az **ismétlés** mező. 
    2. Válassza ki azokat a napokat, amelyeknek érvénybe szeretné venni az ütemtervet. A következő példában Monday-Friday van kiválasztva. 
    3. Válassza ki az ütemterv **befejezési dátumát** .
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlődő ütemterv beállítása](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Az **ütemezett esemény hozzáadása** lapon a **Megjegyzések (nem kötelező)** mezőben adja meg az ütemezés leírását vagy megjegyzéseit. 
4. Az **ütemezett esemény hozzáadása** lapon válassza a **Mentés** lehetőséget. 

    ![Heti ütemterv](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. A naptárban navigáljon a kezdő dátumhoz, és ellenőrizze, hogy az ütemterv be van-e állítva.
    
    ![Ütemterv a naptárban](./media/how-to-create-schedules/schedule-calendar.png)

    További információ egy osztályhoz tartozó ütemtervek létrehozásáról és kezeléséről: [az ütemterv létrehozása és kezelése a laborokhoz](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

Amikor felhasználókat ad hozzá, alapértelmezés szerint a **hozzáférés korlátozása** beállítás be van kapcsolva, és ha a felhasználók listáján szerepelnek, a tanulók nem regisztrálhatnak a laborba, még akkor sem, ha regisztrációs hivatkozással rendelkeznek. Csak a felsorolt felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Kikapcsolhatja a **hozzáférés korlátozása** lehetőséget, amely lehetővé teszi a tanulók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 

### <a name="add-users-from-an-azure-ad-group"></a>Felhasználók hozzáadása egy Azure AD-csoportból

Meglévő Azure Active Directory (Azure AD) csoportba szinkronizálhatja a tesztkörnyezet felhasználói listáját, így nem kell manuálisan hozzáadnia vagy törölnie a felhasználókat. 

A szervezeti erőforrásokhoz és a felhőalapú alkalmazásokhoz való hozzáférés kezeléséhez egy Azure AD-csoport hozható létre a szervezet Azure Active Directory belül. További információ: [Azure ad-csoportok](../active-directory/fundamentals/active-directory-manage-groups.md). Ha a szervezete Microsoft Office 365-es vagy Azure-szolgáltatásokat használ, a szervezete már rendelkezik a Azure Active Directoryt kezelő rendszergazdákkal. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a felhasználói lista üres. Ha a laborban meglévő felhasználók manuálisan vagy CSV-fájl importálásával lettek létrehozva, akkor a tesztkörnyezet meglévő csoportba való szinkronizálásának lehetősége nem jelenik meg. 

1. A bal oldali ablaktáblán válassza a **felhasználók** lehetőséget. 
1. Kattintson **a csoport szinkronizálása** elemre. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Felhasználók hozzáadása egy Azure AD-csoport szinkronizálásával":::
    
1. A rendszer kérni fogja, hogy válasszon ki egy meglévő Azure AD-csoportot, hogy szinkronizálja a labort a következővel:. 
    
    Ha nem látja az Azure AD-csoportot a listában, az a következő okok miatt lehet:

    -   Ha Ön egy Azure Active Directory vendég felhasználója (általában az Azure AD-t birtokló szervezeten kívüli), és nem tud az Azure AD-n belüli csoportokat keresni. Ebben az esetben nem fog tudni Azure AD-csoportot hozzáadni a laborhoz ebben az esetben. 
    -   A csapatokon keresztül létrehozott Azure AD-csoportok nem jelennek meg a listában. A csapatokon belüli Azure Lab Services alkalmazás hozzáadásával és felügyeletével közvetlenül is létrehozhatja és kezelheti a laborokat. A [laborok felhasználói listájának a csapatokból való kezelésével](how-to-manage-user-lists-within-teams.md)kapcsolatos további információkért tekintse meg a következőt:. 
1. Miután kiválasztotta az Azure AD-csoportot, hogy szinkronizálja a labort, kattintson a **Hozzáadás** gombra.
1. Miután a labor szinkronizálva lett, az Azure AD-csoporton belül mindenkit lehívhat a laborba felhasználóként, és megtekintheti a felhasználók listáját. Csak az ebben az Azure AD-csoportban lévő személyek férhetnek hozzá a laborhoz. A felhasználói lista 24 óránként frissül, hogy az megfeleljen az Azure AD-csoport legújabb tagságának. A felhasználók lap szinkronizálás gombjára kattintva manuálisan is szinkronizálhatja az Azure AD-csoport legújabb módosításait.
1. Hívja meg a felhasználókat a laborba úgy, hogy az **összes meghívása** gombra kattint, amely e-mailt küld az összes felhasználónak a laborhoz tartozó regisztrációs hivatkozással. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Felhasználók manuális hozzáadása e-mail-vagy CSV-fájlból

Ebben a szakaszban manuálisan adja hozzá a tanulókat (e-mail-cím vagy CSV-fájl feltöltésével). 

#### <a name="add-users-by-email-address"></a>Felhasználók hozzáadása e-mail-cím alapján

1. A bal oldali ablaktáblán válassza a **felhasználók** lehetőséget. 
1. Kattintson a **felhasználók manuális hozzáadása** lehetőségre. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Felhasználók manuális hozzáadása":::
1. Válassza a **Hozzáadás e-mail-cím szerint** (alapértelmezett) lehetőséget, adja meg a tanulók e-mail-címeit külön vonalakon, vagy egyetlen sorban pontosvesszővel elválasztva. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Felhasználók e-mail címeinek hozzáadása":::
1. Kattintson a **Mentés** gombra. 

    A lista megjeleníti az aktuális felhasználók e-mail-címeit és állapotát, függetlenül attól, hogy regisztrálva vannak-e a laborban. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Felhasználók listája":::

    > [!NOTE]
    > Miután a tanulók regisztrálva lettek a laborban, a lista megjeleníti a nevüket. A listában megjelenő név a Azure Active Directory tanulóinak vezetékneve és vezetékneve alapján jön létre. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével

Hozzáadhat felhasználókat úgy is, hogy feltölt egy CSV-fájlt, amely tartalmazza az e-mail-címüket. 

A CSV-szövegfájlok vesszővel tagolt (CSV) táblázatos adatok (számok és szöveg) tárolására szolgálnak. Az oszlopok mezőiben (például a számolótáblákban) tárolt adatok tárolása helyett a CSV-fájlok vesszővel elválasztva tárolják az adatokat. Egy CSV-fájl minden sora azonos számú vesszővel elválasztott mezővel fog rendelkezni. Az Excel használatával egyszerűen hozhat létre és szerkeszthet CSV-fájlokat.

1. A Microsoft Excelben hozzon létre egy CSV-fájlt, amely egy oszlopban a tanulók e-mail-címeit listázza.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV-fájlban lévő felhasználók listája":::
1. A **felhasználók** ablaktábla tetején válassza a **felhasználók hozzáadása** lehetőséget, majd válassza a CSV-fájl **feltöltése** lehetőséget.
1. Válassza ki a diákok e-mail-címeit tartalmazó CSV-fájlt, majd kattintson a **Megnyitás** gombra.

    A **felhasználók hozzáadása** ablakban megjelenik az e-mail-címek listája a csv-fájlból. 
1. Kattintson a **Mentés** gombra. 
1. A **felhasználók** ablaktáblán tekintse meg a felvett diákok listáját. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="A hozzáadott felhasználók listája a felhasználók ablaktáblán"::: 

## <a name="send-invitation-emails-to-users"></a>Meghívó e-mailek küldése a felhasználóknak

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon, és válassza az **összes meghívása** lehetőséget az eszköztáron. 

    ![Tanulók kiválasztása](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. A **Meghívás küldése e-mailben** lapon adjon meg egy opcionális üzenetet, majd válassza a **Küldés** lehetőséget. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. Ezt a regisztrációs hivatkozást a következő parancs kiválasztásával érheti el: **... (három pont)** az eszköztáron és a **regisztrációs hivatkozáson**. 

    ![Regisztrációs hivatkozás küldése e-mailben](./media/tutorial-setup-classroom-lab/send-email.png)
4. A **meghívás** állapota megjelenik a **felhasználók** listájában. Az állapotnak a **Küldés** gombra kell váltania, majd el kell **küldenie a &lt; dátumot &gt;**. 

A tanulók osztályhoz való hozzáadásával és a labor használatának felügyeletével kapcsolatos további információkért lásd: [a tanulói használat konfigurálása](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy labort az osztályhoz az Azure-ban. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)