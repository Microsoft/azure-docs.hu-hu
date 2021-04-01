---
title: A Azure Lab Services Labs használati beállításainak konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a tanulók számát a laborban, regisztrálhatja őket a laborban, meghatározhatja, hogy hány órát használhatnak a virtuális gép, és így tovább.
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 380a587eecb276c457b93ca3c3f3ac08b2239275
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791963"
---
# <a name="add-and-manage-lab-users"></a>Tesztkörnyezeti felhasználók létrehozása és felügyelete

Ez a cikk azt ismerteti, hogyan lehet tanuló felhasználókat felvenni egy laborba, regisztrálni őket a laborban, szabályozni a virtuális gépet (VM) használó további órák számát, és így tovább. 

Amikor felhasználókat ad hozzá, alapértelmezés szerint a **hozzáférés korlátozása** beállítás be van kapcsolva, és ha a felhasználók listáján szerepelnek, a tanulók nem regisztrálhatnak a laborba, még akkor sem, ha regisztrációs hivatkozással rendelkeznek. Csak a felsorolt felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Kikapcsolhatja a **hozzáférés korlátozása** lehetőséget, amely lehetővé teszi a tanulók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 

Ez a cikk bemutatja, hogyan adhat hozzá felhasználókat a laborhoz.

## <a name="add-users-from-an-azure-ad-group"></a>Felhasználók hozzáadása egy Azure AD-csoportból

### <a name="overview"></a>Áttekintés

Most már meglévő Azure Active Directory (Azure AD) csoportba szinkronizálhatja a tesztkörnyezet felhasználói listáját, így nem kell manuálisan hozzáadnia vagy törölnie a felhasználókat. 

A szervezeti erőforrásokhoz és a felhőalapú alkalmazásokhoz való hozzáférés kezeléséhez egy Azure AD-csoport hozható létre a szervezet Azure Active Directory belül. További információ: [Azure ad-csoportok](../active-directory/fundamentals/active-directory-manage-groups.md). Ha a szervezete Microsoft Office 365-es vagy Azure-szolgáltatásokat használ, a szervezete már rendelkezik a Azure Active Directoryt kezelő rendszergazdákkal. 

### <a name="sync-users-with-azure-ad-group"></a>Felhasználók szinkronizálása az Azure AD-csoporttal

> [!IMPORTANT]
> Győződjön meg arról, hogy a felhasználói lista üres. Ha a laborban meglévő felhasználók manuálisan vagy CSV-fájl importálásával lettek létrehozva, akkor a tesztkörnyezet meglévő csoportba való szinkronizálásának lehetősége nem jelenik meg. 

1. Jelentkezzen be a [Azure Lab Services webhelyére](https://labs.azure.com/).
1. Válassza ki a labort, amellyel dolgozni szeretne.
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

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>Virtuális gépek automatikus felügyelete az Azure AD-csoport változásai alapján 

Miután a labor szinkronizálva lett egy Azure AD-csoporttal, a laborban lévő virtuális gépek száma automatikusan megegyezik a csoportban lévő felhasználók számával. Többé nem fogja tudni manuálisan frissíteni a labor kapacitását. Ha hozzáad egy felhasználót az Azure AD-csoporthoz, a labor automatikusan hozzáadja az adott felhasználóhoz tartozó virtuális gépet. Ha töröl egy felhasználót az Azure AD-csoportból, a labor automatikusan törli a felhasználó virtuális gépét a laborból. 

## <a name="add-users-manually-from-emails-or-csv-file"></a>Felhasználók manuális hozzáadása e-mail-vagy CSV-fájlból

Ebben a szakaszban manuálisan adja hozzá a tanulókat (e-mail-cím vagy CSV-fájl feltöltésével). 

### <a name="add-users-by-email-address"></a>Felhasználók hozzáadása e-mail-cím alapján

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

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével

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

## <a name="send-invitations-to-users"></a>Meghívók küldése a felhasználóknak

Az új felhasználóknak szóló regisztrációs hivatkozás elküldéséhez használja az alábbi módszerek egyikét. 

Ha a **hozzáférés korlátozása** beállítás engedélyezve van a laborban, csak a felsorolt felhasználók használhatják a regisztrációs hivatkozást a laborba való regisztrációhoz. A beállítás alapértelmezés szerint engedélyezett. 

### <a name="invite-all-users"></a>Minden felhasználó meghívása

Ez a módszer azt mutatja be, hogyan küldhet e-mailt egy regisztrációs hivatkozással, és egy opcionális üzenetet az összes felsorolt tanulónak.

1. A **felhasználók** ablaktáblán válassza az **összes meghívása** lehetőséget. 

    ![Az "összes meghívása" gomb](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális üzenetet, majd válassza a **Küldés** lehetőséget. 

    Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. A regisztrációs hivatkozás külön beszerzéséhez és mentéséhez válassza a **felhasználók** ablaktábla tetején található három pontot (**..**.), majd válassza a **regisztrációs hivatkozás** lehetőséget. 

    ![A "regisztrációs hivatkozás küldése e-mailben" ablak](./media/tutorial-setup-classroom-lab/send-email.png)

    A **felhasználók** lista **Meghívási** oszlopa megjeleníti az egyes hozzáadott felhasználók Meghívási állapotát. Az állapotnak a **Küldés** gombra kell váltania, majd **\<date> a küldéshez.** 

### <a name="invite-selected-users"></a>Kiválasztott felhasználók meghívása

Ez a módszer azt mutatja be, hogyan hívhat meg csak bizonyos tanulókat, és hogyan szerezhet be olyan regisztrációs hivatkozást, amelyet másokkal is megoszthat.

1. A **felhasználók** ablaktáblán válasszon ki egy tanulót vagy több tanulót a listában. 

1. A kiválasztott diák sorában válassza a **boríték** ikont, vagy az eszköztáron válassza a **meghívás** lehetőséget. 

    ![Kiválasztott felhasználók meghívása](./media/how-to-configure-student-usage/invite-selected-users.png)

1. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális **üzenetet**, majd válassza a **Küldés** lehetőséget. 

    ![E-mail küldése a kiválasztott felhasználóknak](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    A **felhasználók** ablaktábla a tábla **meghívás** oszlopában jeleníti meg a művelet állapotát. A meghívó e-mail-címe tartalmazza azt a regisztrációs hivatkozást, amelyet a tanulók a laborban használhatnak a regisztráláshoz.

## <a name="get-the-registration-link"></a>Regisztrációs hivatkozás beszerzése

Ebben a szakaszban a portálról kérheti le a regisztrációs hivatkozást, és elküldheti azt a saját e-mail alkalmazásával. 

1. A **felhasználók** ablaktáblán válassza a **regisztrációs hivatkozás** lehetőséget.

    ![A diákok regisztrációs hivatkozása](./media/how-to-configure-student-usage/registration-link-button.png)

1. A **felhasználó regisztrálása** ablakban válassza a **Másolás** lehetőséget, majd válassza a **kész** lehetőséget. 

    ![A "felhasználói regisztráció" ablak](./media/how-to-configure-student-usage/registration-link.png)

    A rendszer a vágólapra másolja a hivatkozást. 
    
1. Az e-mail-alkalmazásban illessze be a regisztrációs hivatkozást, majd küldje el az e-mailt egy tanulónak, hogy a tanuló regisztráljon az osztályra. 

## <a name="view-registered-users"></a>Regisztrált felhasználók megtekintése

1. Lépjen a [Azure Lab Services](https://labs.azure.com) webhelyére. 
1. Válassza a **Bejelentkezés** lehetőséget, majd adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is.
1. A **saját laborok** oldalon válassza ki azt a labort, amelynek a felhasználását nyomon szeretné követni. 
1. A bal oldali ablaktáblán válassza a **felhasználók** lehetőséget, vagy válassza a **felhasználók** csempét. 

    A **felhasználók** ablaktáblán megjelenik azoknak a tanulóknak a listája, akik regisztrálva vannak a laborban.  

    ![Regisztrált felhasználók listája](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára

Az egyes tanulók számára a következő módon állíthat be egy órás kvótát: 

1. A **felhasználók** ablaktáblán válassza a **kvóta felhasználónként: \<number> óra** lehetőséget az eszköztáron. 
1. A **kvóta felhasználónként** ablakban adja meg, hogy hány órát szeretne adni az egyes tanulóknak az ütemezett osztály időpontján kívül, majd válassza a **Mentés** lehetőséget.

    ![A "kvóta felhasználónként" ablak](./media/how-to-configure-student-usage/quota-per-user.png)    

    A módosított értékek mostantól a **kvóta felhasználónként: \<number of hours>** gomb jelenik meg az eszköztáron és a felhasználók listán, ahogy az itt látható:

    ![Kvóta órája felhasználónként](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a tanulók számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-additional-quotas-for-specific-users"></a>További kvóták beállítása adott felhasználók számára

Bizonyos tanulók számára megadhat kvótákat az előző szakaszban szereplő összes felhasználóhoz beállított általános kvótán kívül. Ha például oktatóként állítja be a kvótát az összes tanuló számára 10 órára, és egy adott diák esetében 5 óra további kvótát állít be, a tanuló 15 (10 + 5) órányi kvótát kap. Ha később módosítja a közös kvótát, mondjuk 15, a tanuló 20 (15 + 5) kvótát kap. Ne feledje, hogy ez az általános kvóta kívül esik az ütemezett időponton. Az az idő, ameddig a tanuló egy laboratóriumi virtuális gépen az ütemezett időpontra költ, nem számít bele a kvótába. 

További kvóták beállításához tegye a következőket:

1. A **felhasználók** ablaktáblán válasszon ki egy tanulót a listából, majd válassza a **kvóta módosítása** lehetőséget az eszköztáron. 

    ![A "kvóta módosítása" gomb](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. A **kvóta módosítása a esetében \<selected user or users email address>** mezőben adja meg a kiválasztott tanuló vagy tanulók számára megadni kívánt további labor-órák számát, majd válassza az **alkalmaz** lehetőséget. 

    ![A "kvóta módosítása..." ablak](./media/how-to-configure-student-usage/additional-quota.png)

    A **használat** oszlopban látható a kiválasztott tanulók frissített kvótája. 

    ![A felhasználó új használata](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Tanulói fiókok

Ha tanulókat szeretne felvenni egy osztályterem laborba, használja az e-mail-fiókjait. A diákok a következő típusú e-mail-fiókokkal rendelkezhetnek:

- Az Egyetem Azure Active Directory példánya által biztosított tanulói e-mail-fiók.
- Egy Microsoft-tartományi e-mail-fiók, például a *Outlook.com*, a *hotmail.com*, a *MSN.com* vagy a *Live.com*.
- Nem a Microsofttól származó e-mail-fiók, például egy Yahoo! által biztosított vagy a Google. Az ilyen típusú fiókoknak azonban Microsoft-fiókhoz kell kapcsolódniuk.
- Egy GitHub-fiók. Ennek a fióknak egy Microsoft-fiók-vel kell összekapcsolnia.

### <a name="use-a-non-microsoft-email-account"></a>Nem a Microsofttól származó e-mail-fiók használata

A tanulók a nem a Microsofttól származó e-mail-fiókokkal regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba.  A regisztrációhoz azonban először létre kell hoznia egy Microsoft-fiók, amely a nem a Microsoft e-mail-címéhez van csatolva.

Előfordulhat, hogy számos tanuló már rendelkezik egy Microsoft-fiók, amely nem a Microsoft e-mail-címéhez van csatolva. Például a tanulóknak már van Microsoft-fiókuk, ha az e-mail-címüket más Microsoft-termékekkel vagy-szolgáltatásokkal (például Office, Skype, OneDrive vagy Windows) használták.  

Ha a tanulók a regisztrációs hivatkozásra kattintva bejelentkeznek egy osztályterembe, a rendszer kéri az e-mail-címük és jelszavuk megadását. Azok a tanulók, akik nem a Microsoft-fiókhoz kapcsolódó nem Microsoft-fiókkal próbálnak bejelentkezni, a következő hibaüzenetet kapják: 

![Bejelentkezéskor megjelenő hibaüzenet](./media/how-to-configure-student-usage/cant-find-account.png)

A diákoknak itt egy hivatkozása van a [Microsoft-fiókra való feliratkozáshoz](http://signup.live.com).  

> [!IMPORTANT]
> Ha a tanulók bejelentkeznek egy osztályterem laborba, nem kapnak lehetőséget a Microsoft-fiók létrehozására. Ezért javasoljuk, hogy ezt a regisztrációs hivatkozást, http://signup.live.com a nem Microsoft-fiókokat használó tanulók számára elküldött tantermi labor regisztrációs e-mailben adja meg.

### <a name="use-a-github-account"></a>GitHub-fiók használata

A tanulók egy meglévő GitHub-fiókkal is regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba. Ha már rendelkezik egy Microsoft-fiók a GitHub-fiókjával, a tanulók bejelentkezhetnek, és megadhatják a jelszavukat az előző szakaszban látható módon. 

Ha még nem csatolták GitHub-fiókját egy Microsoft-fiókhoz, a következőket teheti:

1. Válassza ki a **bejelentkezési beállítások** hivatkozást, ahogy az itt látható:

    ![A "bejelentkezési beállítások" hivatkozás](./media/how-to-configure-student-usage/signin-options.png)

1. A **bejelentkezési beállítások** ablakban válassza a **Bejelentkezés a githubkal** lehetőséget.

    ![A "Bejelentkezés a GitHubtal" hivatkozás](./media/how-to-configure-student-usage/signin-github.png)

    Ekkor a tanulók a GitHub-fiókhoz kapcsolódó Microsoft-fiók hozhatnak létre. A csatolás automatikusan megtörténik, amikor a **tovább** gombra kattintanak. Ezután azonnal bejelentkeznek és csatlakoznak az osztályterem laborhoz.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Felhasználók listájának exportálása CSV-fájlba

1. Nyissa meg a **felhasználók** ablaktáblát.
1. Az eszköztáron válassza a három pontot (**..**.), majd válassza a **CSV exportálása** lehetőséget. 

    ![A "CSV exportálása" gomb](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- Rendszergazdáknak: [labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- Labor tulajdonosainak: [Labs létrehozása és kezelése](how-to-manage-classroom-labs.md) , [sablonok beállítása és közzététele](how-to-create-manage-template.md)
- Tesztkörnyezet felhasználói számára: [hozzáférés a laborokhoz](how-to-use-classroom-lab.md)