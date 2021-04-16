---
title: Csoportok hozzáférési felülvizsgálatának létrehozása & alkalmazásokban – Azure AD
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot csoporttagok vagy alkalmazás-hozzáférések számára Azure Active Directory hozzáférési felülvizsgálatok során.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532274"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure AD hozzáférési felülvizsgálatok során

A csoportokhoz és alkalmazásokhoz való hozzáférés az alkalmazottak és vendégek számára idővel megváltozik. Az elavult hozzáférés-hozzárendelésekkel járó kockázat csökkentése érdekében a rendszergazdák a Azure Active Directory (Azure AD) használatával hozzáférési felülvizsgálatokat hozhatnak létre a csoporttagok vagy az alkalmazások hozzáférése számára. Ha rendszeresen át kell vizsgálnia a hozzáférést, ismétlődő hozzáférési felülvizsgálatokat is létrehozhat. További információ ezekről a forgatókönyvekről: [Felhasználói](manage-user-access-with-access-reviews.md) hozzáférés kezelése és [Vendég hozzáférés kezelése.](manage-guest-access-with-access-reviews.md)

Tekintsen meg egy gyors videót, amely a hozzáférési felülvizsgálatok engedélyezéséről szól:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Ez a cikk bemutatja, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot csoporttagok vagy alkalmazás-hozzáférések számára.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- globális rendszergazda vagy felhasználói rendszergazda

További információ: [Licenckövetelmények.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Egy vagy több hozzáférési felülvizsgálat létrehozása

1. Jelentkezzen be a Azure Portal, és nyissa meg az [Identity Governance oldalt.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

2. A bal oldali menüben kattintson a **Hozzáférési felülvizsgálatok elemre.**

3. Új **hozzáférési felülvizsgálat létrehozásához kattintson** az Új hozzáférési felülvizsgálat elemre.

    ![Hozzáférési felülvizsgálatok panel az Identity Governanceban](./media/create-access-review/access-reviews.png)

4. Az **1. lépés: Válassza ki, hogy melyik** erőforrást szeretné felülvizsgálni.

    ![Hozzáférési felülvizsgálat létrehozása – Név és leírás áttekintése](./media/create-access-review/select-what-review.png)

5. Ha az 1. lépésben **a Teams + Csoportok** lehetőséget választotta, két lehetőség közül választhat a 2. lépésben
   - **Minden Microsoft 365 vendégfelhasználóval.** Válassza ezt a lehetőséget, ha ismétlődő felülvizsgálatokat szeretne létrehozni a szervezet összes Microsoft Teams- és M365-csoport összes vendégfelhasználója számára. Ha ki szeretne zárni bizonyos csoportokat, kattintson a Kizárni kívánt csoportok kijelölése elemre.
   - **Válassza a teams + groups (csapatok + csoportok) lehetőséget.** Válassza ezt a lehetőséget, ha véges számú áttekintni kívánt csapatot és/vagy csoportot szeretne megadni. Miután rákattintott erre a lehetőségre, megjelenik a jobb oldalon kiválasztható csoportok listája.

     ![Csapatok és csoportok](./media/create-access-review/teams-groups.png)

     ![A felhasználói felületen kiválasztott csapatok és csoportok](./media/create-access-review/teams-groups-detailed.png)

6. Ha az **1.** lépésben az Alkalmazások lehetőséget választotta, akkor a 2. lépésben kiválaszthat egy vagy több alkalmazást.

    >[!NOTE]
    > Ha több csoportot és/vagy alkalmazást választ ki, több hozzáférési felülvizsgálat jön létre. Ha például 5 csoportot választ ki az áttekintéshez, az 5 különálló hozzáférési felülvizsgálatot eredményez

   ![A felület akkor jelenik meg, ha csoportok helyett alkalmazásokat választ](./media/create-access-review/select-application-detailed.png)

7. Ezután a 3. lépésben kiválaszthatja a felülvizsgálat hatókörét. A lehetőségek:
   - **Csak vendégfelhasználók.** Ha ezt a lehetőséget választja, a hozzáférési felülvizsgálatot a címtárban található Azure AD B2B vendégfelhasználókra korlátozza.
   - **Mindenki.** Ha ezt a lehetőséget választja, a hozzáférési felülvizsgálat az erőforráshoz társított összes felhasználói objektumra kiterjed.

    >[!NOTE]
    > Ha a 2. lépésben a Minden Microsoft 365 csoport vendégfelhasználóval lehetőséget választotta, akkor az egyetlen lehetőség a vendégfelhasználók áttekintése a 3. lépésben

8. Kattintson a Tovább: Felülvizsgálatok elemre
9. A **Felülvizsgálók kiválasztása szakaszban** válasszon ki egy vagy több embert a hozzáférési felülvizsgálatok elvégzéséhez. A következő lehetőségek közül választhat:
    - **Csoporttulajdonos(k)** (Csak akkor érhető el, ha egy csapaton vagy csoporton végez felülvizsgálatot)
    - **Kiválasztott felhasználó(k) vagy csoportok**
    - **A felhasználók felülvizsgálják a saját hozzáférését**
    - **A felhasználók vezetői.**
    Ha a Felhasználók vezetői **vagy** a Csoporttulajdonosok lehetőséget **választja,**  tartalék felülvizsgáló megadására is lehetősége van. A tartalék felülvizsgálókat akkor kell felülvizsgálni, ha a felhasználónak nincs kezelője a címtárban, vagy ha a csoportnak nincs tulajdonosa.

    ![új hozzáférési felülvizsgálat](./media/create-access-review/new-access-review.png)

10. A **Felülvizsgálat ismétlődésének** megadása szakaszban megadhat egy gyakoriságot, például **Heti, Havi, Negyedéves, Féléves, Évente.** Ezután meg kell adnia egy **Duration (Időtartam)** értéket, amely meghatározza, hogy mennyi ideig legyen megnyitva egy felülvizsgálat a felülvizsgálók bemenetei számára. A havi felülvizsgálathoz beállítható maximális időtartam például 27 nap, hogy elkerülje az átfedésben lévő felülvizsgálatokat. Érdemes lehet lerövidíteni az időtartamot, hogy a felülvizsgálók által megadott adatok korábban alkalmazva legyen. Ezután kiválaszthatja a Kezdési **dátum és** a Záró **dátum lehetőséget.**

    ![Válassza ki, milyen gyakran történjen felülvizsgálat](./media/create-access-review/frequency.png)

11. Kattintson **a Tovább: Beállítások** gombra a lap alján
12. A **Befejezéskor beállításoknál megadhatja,** hogy mi történjen a felülvizsgálat befejezése után

    ![Hozzáférési felülvizsgálat létrehozása – a befejezési beállítások megadásakor](./media/create-access-review/upon-completion-settings-new.png)

Ha automatikusan el szeretné távolítani a hozzáférést a megtagadott felhasználóktól, állítsa az Eredmények automatikus alkalmazása erőforrásra beállítását Engedélyezés beállításra. Ha manuálisan szeretné alkalmazni az eredményeket a felülvizsgálat befejezésekor, állítsa a kapcsolót Letiltás beállításra.
Az If reviewers don't respond (Ha a felülvizsgálók nem válaszolnak) listában adhatja meg, hogy mi történjen azokkal a felhasználókkal, akik nem felülvizsgálatát követően felülvizsgálatát is el kell látnia a felülvizsgálati időszakon belül. Ez a beállítás nem vonatkozik a felülvizsgálók által manuálisan áttekintett felhasználókra. Ha a végső felülvizsgáló megtagadásról dönt, akkor a felhasználó hozzáférése el lesz távolítva.

- **Nincs változás** – A felhasználói hozzáférés változatlan marad
- **Hozzáférés eltávolítása** – Felhasználó hozzáférésének eltávolítása
- **Hozzáférés jóváhagyása** – Felhasználói hozzáférés jóváhagyása
- **Javaslatok –** A rendszer arra vonatkozó ajánlása, hogy tiltsa le vagy hagyja jóvá a felhasználó folyamatos hozzáférését

    ![Befejezéskor a beállítások beállításai](./media/create-access-review/upon-completion-settings-new.png)

A Művelet használatával alkalmazhatja a megtagadott **vendégfelhasználókra** annak megadását, hogy mi történjen a vendégfelhasználók számára, ha a rendszer megtagadja őket.
- Ha eltávolítja a felhasználó tagságát az erőforrásból, a rendszer eltávolítja a felhasználónak az áttekintett csoporthoz vagy alkalmazáshoz való hozzáférését, így továbbra is bejelentkezik a bérlőbe.
- A felhasználó 30 napig való bejelentkezésének 30 napig való blokkolása, majd a felhasználó bérlőből való eltávolítása letiltja a megtagadott felhasználók bejelentkezését a bérlőbe, függetlenül attól, hogy hozzáférnek-e más erőforrásokhoz. Ha hiba történt, vagy ha egy rendszergazda úgy dönt, hogy újra engedélyezi a hozzáférést, ezt a felhasználó letiltás utáni 30 napon belül teheti meg. Ha nem történik művelet a letiltott felhasználókon, azok törlődnek a bérlőből.

Ha többet szeretne megtudni az olyan vendégfelhasználók eltávolítására vonatkozó ajánlott eljárásokról, akik már nem férnek hozzá a szervezet erőforrásaihoz, olvassa el a Use Azure AD Identity Governance to review and remove external users who no longer have resource access (Az Azure AD Identity Governance használata az erőforrásokhoz már nem hozzáféréssel bíró külső felhasználók áttekintéséhez és eltávolításához) című [cikket.](access-reviews-external-users.md)

   >[!NOTE]
   >A elutasított vendégfelhasználókra alkalmazandó művelet nem konfigurálható a vendégfelhasználóknál többre vonatkozó felülvizsgálatok esetén. Emellett nem konfigurálható a vendégfelhasználókat használó **összes M365-csoport felülvizsgálatára.** Ha nem konfigurálható, a rendszer a felhasználótagság erőforrásból való eltávolításának alapértelmezett beállítását használja a megtagadott felhasználóknál.

13. Az **Enable review decision helpers (Felülvizsgálati** döntési segítők engedélyezése) részen eldöntheti, hogy szeretné-e, ha a felülvizsgáló javaslatokat kapna a felülvizsgálati folyamat során.

    ![Döntési segítők beállításainak engedélyezése](./media/create-access-review/helpers.png)

14. A Speciális **beállítások szakaszban** a következőket választhatja ki:
    - Az **Indoklás szükséges beállítás** **engedélyezésével követelje** meg a felülvizsgálótól a jóváhagyás indoklását.
    - Állítsa **az e-mail-értesítéseket** Engedélyezésre, hogy az Azure AD e-mail-értesítéseket küldjön a felülvizsgálóknak a hozzáférési felülvizsgálat kezdetekor, illetve a rendszergazdáknak a felülvizsgálat befejezésekor. 
    - Állítsa **az Emlékeztetők** **beállításnál** az Engedélyezést, hogy az Azure AD emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról az olyan felülvizsgálóknak, akik még nem fejezték be a felülvizsgálatot. Ezek az emlékeztetők félúton lesznek a felülvizsgálat idejére.
    - A felülvizsgálóknak küldött e-mail tartalmát a rendszer automatikusan generálja a felülvizsgálat részletei alapján, például a felülvizsgálat neve, az erőforrás neve, az esedékesség dátuma stb. alapján. Ha további információk, például további utasítások vagy kapcsolattartási adatok megadására van szüksége, ezeket az adatokat a További tartalom felülvizsgáló e-mailhez című szakaszban **adhatja** meg. A megadott információk a hozzárendelt felülvizsgálóknak küldött meghívó- és emlékeztető e-mailekben szerepelnek. Az alábbi képen kiemelt szakasz mutatja, hol jelennek meg ezek az információk.


      ![további tartalom a felülvizsgáló számára](./media/create-access-review/additional-content-reviewer.png)

15. Kattintson a **Tovább: Áttekintés + Létrehozás** gombra a következő lapra lépéshez
16. Nevezze el a hozzáférési felülvizsgálatot. Ha szükséges, adja meg a felülvizsgálat leírását. A név és a leírás megjelenik a felülvizsgálók számára.
17. Tekintse át az adatokat, és válassza a Létrehozás **lehetőséget**

       ![felülvizsgálati képernyő létrehozása](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat kezdete

A hozzáférési felülvizsgálat beállításainak megadása után kattintson a **Start gombra.** A hozzáférési felülvizsgálat megjelenik a listában, és jelzi annak állapotát.

![Hozzáférési felülvizsgálatok és állapotuk listája](./media/create-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD röviddel a felülvizsgálat kezdete után e-mailt küld a felülvizsgálóknak. Ha úgy dönt, hogy nem küld e-mailt az Azure AD-nek, mindenképpen tájékoztassa a felülvizsgálókat arról, hogy egy hozzáférési felülvizsgálat vár a számukra. Megmutathatja nekik a csoportokhoz vagy alkalmazásokhoz való [hozzáférés áttekintésére vonatkozó utasításokat.](perform-access-review.md) Ha a felülvizsgálat során a vendégeknek át kell vizsgálniuk a saját hozzáférésüket, mutassa meg nekik a csoportokhoz vagy alkalmazásokhoz való hozzáférés [áttekintésére vonatkozó utasításokat.](review-your-access.md)

Ha vendégeket rendelt hozzá felülvizsgálóként, és nem fogadták el a meghívást, akkor nem kapnak e-mailt a hozzáférési felülvizsgálattól, mert a felülvizsgálat előtt el kell fogadniuk a meghívást.

## <a name="access-review-status-table"></a>Hozzáférési felülvizsgálat állapottáblája

| status | Meghatározás |
|--------|------------|
|NotStarted (Nincs indítás) | Az ellenőrzés létrejött, a felhasználófelderítés indításra vár. |
|Inicializálás   | A felhasználófelderítés folyamatban van, hogy azonosítsa az összes olyan felhasználót, aki a felülvizsgálat részét képezi. |
|Indítás | A felülvizsgálat elkezdődik. Ha az e-mail-értesítések engedélyezve vannak, a rendszer e-maileket küld a felülvizsgálóknak. |
|Bejövő forgalom | A felülvizsgálat elindult. Ha engedélyezve vannak az e-mail-értesítések, a felülvizsgálók e-maileket küldnek. A felülvizsgálók a határidőig küldhetnek be döntéseket. |
|Befejezése | A felülvizsgálat folyamatban van, és a felülvizsgálat tulajdonosa e-maileket kap. |
|Automatikus felülvizsgálat | A felülvizsgálat egy rendszer-felülvizsgálati szakaszban van. A rendszer olyan felhasználókkal kapcsolatos döntéseket rögzít, akik nem felülvizsgálatát javaslatokat vagy előre konfigurált döntéseket hozott. |
|Automatikus felülvizsgálat | A rendszer rögzítette a nem felülvizsgált felhasználókra vonatkozó döntéseket. Az áttekintés készen áll arra, hogy folytassa az Alkalmazás lehetőség **alkalmazásával,** ha az automatikus alkalmazás engedélyezve van. |
|Alkalmazása | A jóváhagyott felhasználók hozzáférése nem változik. |
|Alkalmazva | Az elutasított felhasználók (ha vannak) el vannak távolítva az erőforrásból vagy címtárból. |
|Sikertelen | A felülvizsgálat nem haladt. Ez a hiba a bérlő törlésével, a licencek változásával vagy más belső bérlőváltozásokkal kapcsolatos lehet. |

## <a name="create-reviews-via-apis"></a>Felülvizsgálatok létrehozása API-k segítségével

Api-k használatával hozzáférési felülvizsgálatokat is létrehozhat. A csoport- és alkalmazásfelhasználók hozzáférési felülvizsgálatának kezelése a Azure Portal API-k használatával Microsoft Graph is. További információkért tekintse meg az [Azure AD hozzáférési felülvizsgálatok API-referenciáját.](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) Kódmintát az [Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)hozzáférési felülvizsgálatok beolvasásának példái a következőn keresztül Microsoft Graph.

## <a name="next-steps"></a>Következő lépések

- [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése](perform-access-review.md)
- [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése](review-your-access.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)