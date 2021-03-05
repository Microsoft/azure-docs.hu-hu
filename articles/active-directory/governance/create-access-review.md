---
title: Csoportok hozzáférési felülvizsgálatának létrehozása &-alkalmazásokhoz – Azure AD
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot a csoporttagokról vagy az alkalmazás-hozzáférésről Azure Active Directory hozzáférési felülvizsgálatokban.
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
ms.openlocfilehash: 7143c3f9786d41c32ae954ab219197a9cfaa1050
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176875"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure AD hozzáférési felülvizsgálatokban

A csoportokhoz és alkalmazásokhoz való hozzáférés az alkalmazottak és a vendégek számára az idő múlásával változik. Az elavult hozzáférési hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében a rendszergazdák Azure Active Directory (Azure AD) használatával hozhatnak létre hozzáférési felülvizsgálatokat a csoporttagok vagy az alkalmazások eléréséhez. Ha rendszeresen szeretné áttekinteni a hozzáférést, akkor ismétlődő hozzáférési felülvizsgálatokat is létrehozhat. További információ ezekről a forgatókönyvekről: a [felhasználói hozzáférés kezelése](manage-user-access-with-access-reviews.md) és a [vendég-hozzáférés kezelése](manage-guest-access-with-access-reviews.md).

Megtekintheti a hozzáférési felülvizsgálatok engedélyezésével kapcsolatos gyors videót:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot a csoporttagok vagy az alkalmazások eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda vagy felhasználói rendszergazda

További információkért lásd a [licencekre vonatkozó követelményeket](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Egy vagy több hozzáférési felülvizsgálat létrehozása

1. Jelentkezzen be a Azure Portalba, és nyissa meg az [Identity irányításáért lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. A bal oldali menüben kattintson a **hozzáférési felülvizsgálatok** elemre.

3. Új hozzáférési felülvizsgálat létrehozásához kattintson az **új hozzáférés-ellenőrzés** elemre.

    ![Az identitás-irányítás hozzáférési felülvizsgálatok panelje](./media/create-access-review/access-reviews.png)

4. Az **1. lépés: válassza ki, hogy mit szeretne** áttekinteni, hogy melyik erőforrást szeretné áttekinteni.

    ![Hozzáférési felülvizsgálat létrehozása – a név és a Leírás áttekintése](./media/create-access-review/select-what-review.png)

5. Ha a **Teams + groups** elemet választotta az 1. lépésben, a 2. lépésben két lehetőség közül választhat.
   - **A vendég felhasználókkal Microsoft 365 összes csoport.** Akkor válassza ezt a lehetőséget, ha ismétlődő értékeléseket kíván létrehozni az összes vendég felhasználóról a szervezet összes Microsoft-csapata és M365-csoportja között. Kiválaszthat bizonyos csoportokat, ha a kizárni kívánt csoport (ok) lehetőségre kattint.
   - **Válassza a csapatok és csoportok lehetőséget.** Válassza ezt a lehetőséget, ha meg szeretné adni az áttekinteni kívánt csapatok és/vagy csoportok véges csoportját. Ha erre a lehetőségre kattint, megjelenik a megfelelő csoportok listája.

     ![Csapatok és csoportok](./media/create-access-review/teams-groups.png)

     ![A felhasználói felületen kiválasztott csoportok és csoportok](./media/create-access-review/teams-groups-detailed.png)

6. Ha az 1. lépésben az **alkalmazásokat** választotta, akkor a 2. lépésben egy vagy több alkalmazást is kijelölhet.

    >[!NOTE]
    > Több csoport és/vagy alkalmazás kiválasztásakor a rendszer több hozzáférési felülvizsgálatot eredményez. Ha például 5 csoportot választ az áttekintéshez, az 5 külön hozzáférési felülvizsgálatot fog eredményezni.

   ![Az illesztőfelület akkor jelenik meg, ha a csoportok helyett az alkalmazásokat választotta](./media/create-access-review/select-application-detailed.png)

7. Ezután a 3. lépésben kiválaszthatja a felülvizsgálat hatókörét. A lehetőségek a következők
   - **Csak vendég felhasználók.** Ha ezt a beállítást választja, a hozzáférési felülvizsgálatot csak az Azure AD B2B-vendég felhasználói használhatják a címtárban.
   - **Mindenki.** Ha ezt a beállítást választja, a rendszer a hozzáférési felülvizsgálatot az erőforráshoz társított összes felhasználói objektumra kijelöli.

    >[!NOTE]
    > Ha a 2. lépésben a vendég felhasználókkal rendelkező összes Microsoft 365 csoportot választotta, akkor az egyetlen lehetőség, hogy a 3. lépésben megtekintse a vendég felhasználókat.

8. Kattintson a Tovább gombra: felülvizsgálatok
9. A **véleményezők kiválasztása** szakaszban válasszon ki egy vagy több személyt a hozzáférési felülvizsgálatok végrehajtásához. A következő lehetőségek közül választhat:
    - **Csoport tulajdonosa (k)** (csak akkor érhető el, ha egy csapatra vagy csoportra vonatkozó felülvizsgálatot kell végrehajtani)
    - **Kijelölt felhasználó (k) vagy csoportok**
    - **A felhasználók saját hozzáférést tekinthetnek meg**
    - **A felhasználók felettesei.**
    Ha a felhasználók vagy **a** **csoportok tulajdonosainak**  bármelyikét választja, lehetősége van tartalék felülvizsgáló megadására is. A tartalék felülvizsgálók akkor is megtalálhatók, ha a felhasználó nem rendelkezik a címtárban megadott Felettessel, vagy ha a csoport nem rendelkezik tulajdonossal.

    ![új hozzáférési felülvizsgálat](./media/create-access-review/new-access-review.png)

10. A **felülvizsgálati szakasz ismétlődésének meghatározása** szakaszban megadhat egy gyakoriságot, például **hetente, havonta, negyedévente, félévente, évente**. Ezután megadhat egy **időtartamot**, amely meghatározza, hogy a felülvizsgálatok milyen hosszú ideig legyenek megnyitva a véleményezők számára. Például a havi felülvizsgálathoz beállítható maximális időtartam 27 nap, az átfedő felülvizsgálatok elkerülése érdekében. Előfordulhat, hogy le kívánja rövidíteni az időtartamot, hogy a felülvizsgálók bemenete korábban legyen alkalmazva. Ezután kiválaszthatja a **kezdési dátumot** és a **befejezési dátumot**.

    ![Válassza ki, hogy milyen gyakran történjen a felülvizsgálat](./media/create-access-review/frequency.png)

11. Kattintson a **következő: beállítások** gombra a lap alján.
12. A **befejezési beállítások** lehetőségnél megadhatja, hogy mi történjen a felülvizsgálat befejeződése után

    ![Hozzáférési felülvizsgálat létrehozása a befejezési beállítások alapján](./media/create-access-review/upon-completion-settings-new.png)

Ha azt szeretné, hogy a rendszer automatikusan eltávolítsa a hozzáférést a megtagadott felhasználók számára, állítsa az eredmények automatikus alkalmazása az erőforrásra lehetőséget az engedélyezéshez. Ha a felülvizsgálat befejeződése után manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót a Letiltás lehetőségre.
Ha a felülvizsgálók nem válaszolnak a listára, adja meg, hogy mi történik azon felhasználók esetében, akiket a felülvizsgálati időszakon belül nem tekintenek át a véleményező. Ez a beállítás nem érinti azokat a felhasználókat, akiket manuálisan ellenőriztek a véleményezők. Ha megtagadja a végső felülvizsgáló döntését, a rendszer eltávolítja a felhasználó hozzáférését.

- **Nincs változás** – a felhasználó hozzáférése változatlan marad
- **Hozzáférés eltávolítása** – a felhasználó hozzáférésének eltávolítása
- **Hozzáférés jóváhagyása** – a felhasználó hozzáférésének jóváhagyása
- **Javaslatok készítése** – a rendszer javaslata a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

    ![A befejezési beállítások megadása után](./media/create-access-review/upon-completion-settings-new.png)

A megtagadott **vendég** -felhasználókra vonatkozó művelet végrehajtásával adhatja meg, hogy mi történik a vendég felhasználókkal, ha meg vannak tagadva.
- Ha eltávolítja a felhasználó tagságát az erőforrásból, a rendszer eltávolítja a megtagadott felhasználó hozzáférését az áttekintett csoporthoz vagy alkalmazáshoz, továbbra is bejelentkezhet a bérlőbe.
- A felhasználó 30 napig való beléptetésének tiltása, majd a bérlő felhasználójának eltávolítása megakadályozza, hogy a megtagadott felhasználók bejelentkezzenek a bérlőbe, függetlenül attól, hogy hozzáférnek-e más erőforrásokhoz. Ha hiba történt, vagy ha egy rendszergazda úgy dönt, hogy újra engedélyezi az egyik hozzáférését, akkor a felhasználó letiltását követő 30 napon belül megteheti. Ha a letiltott felhasználók nem végeznek műveleteket, a rendszer törli a bérlőről.

Ha többet szeretne megtudni az olyan vendég felhasználók eltávolítására vonatkozó ajánlott eljárásokról, akik már nem férnek hozzá a szervezet erőforrásaihoz, olvassa el a [Azure ad Identity Governance használata című cikket, amellyel áttekintheti és eltávolíthatja azokat a külső felhasználókat, akik már nem rendelkeznek erőforrás-hozzáféréssel.](access-reviews-external-users.md)

   >[!NOTE]
   >A megtagadott vendég felhasználóinak alkalmazására vonatkozó művelet nem konfigurálható az olyan felülvizsgálatokon, amelyek több mint vendég felhasználóra vonatkoznak. Nem konfigurálható a **vendég felhasználókkal rendelkező összes M365-csoport** felülvizsgálatára is. Ha nem konfigurálható, a felhasználó tagságának az erőforrásból való eltávolítására szolgáló alapértelmezett beállítás a megtagadott felhasználóknál használatos.

13. A **felülvizsgálati döntési segítők engedélyezése** lapon válassza ki, hogy szeretné-e, ha a felülvizsgáló javaslatokat kapjon a felülvizsgálati folyamat során.

    ![Döntési segítők beállításainak engedélyezése](./media/create-access-review/helpers.png)

14. A **Speciális beállítások** szakaszban a következők közül választhat:
    - Annak **engedélyezéséhez** , hogy a felülvizsgáló megkövetelje a jóváhagyás okát, meg kell adnia az **indoklást** .
    - Az **e-mail-értesítések** beállításával **engedélyezheti** , hogy az Azure ad e-mailes értesítéseket küldjön a felülvizsgálók számára a hozzáférési felülvizsgálat indításakor, valamint a rendszergazdáknak a felülvizsgálat befejezésekor.
    - Az **emlékeztetők** beállításával **engedélyezheti** , hogy az Azure ad emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról azon felülvizsgálók számára, akik nem fejezték be a felülvizsgálatot. Ezek az emlékeztetők a felülvizsgálat időtartama alatt önmagukban lesznek.
    - A felülvizsgálók számára küldött e-mailek tartalma automatikusan létrejön a felülvizsgálati adatok alapján, például a felülvizsgálat neve, az erőforrás neve, a határidő stb. Ha további információkkal (például további utasításokkal vagy kapcsolattartási információkkal) szeretne kommunikálni, ezeket a részleteket a **további tartalom a felülvizsgáló e-mailek számára** című szakaszban találja meg. A beírt információk a meghívót és az emlékeztető e-maileket tartalmazzák a hozzárendelt felülvizsgálók számára. Az alábbi képen Kiemelt szakasz megjeleníti, hogy hol jelennek meg ezek az információk.


      ![További tartalom a felülvizsgáló számára](./media/create-access-review/additional-content-reviewer.png)

15. Kattintson a **Tovább gombra: felülvizsgálat + létrehozás** gombra a következő lapra való áttéréshez
16. Nevezze el a hozzáférési felülvizsgálatot. Szükség esetén adja meg a Leírás áttekintését. A név és a leírás a felülvizsgálók számára jelenik meg.
17. Tekintse át az adatokat, és válassza a **Létrehozás** lehetőséget.

       ![felülvizsgálati képernyő létrehozása](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start** gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Hozzáférési felülvizsgálatok és azok állapotának listája](./media/create-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti a [csoportokhoz vagy alkalmazásokhoz való hozzáférés ellenőrzésének](perform-access-review.md)utasításait. Ha az Ön véleménye szerint a vendégek áttekinthetik a saját hozzáférését, megtudhatják, hogyan [tekintheti át a hozzáférést a csoportokhoz vagy alkalmazásokhoz](review-your-access.md).

Ha a vendégek felülvizsgálók vannak hozzárendelve, és nem fogadták el a meghívást, nem kapnak e-mailt a hozzáférési felülvizsgálatokból, mert először el kell fogadniuk a meghívót a felülvizsgálat előtt.

## <a name="access-review-status-table"></a>Hozzáférési felülvizsgálati állapot tábla

| status | Meghatározás |
|--------|------------|
|NotStarted | A felülvizsgálat létrejött, a felhasználó felderítése elindul. |
|Inicializálás   | A felhasználó felderítése folyamatban van a felülvizsgálat részét képező összes felhasználó azonosítására. |
|Indítás | A felülvizsgálat megkezdődött. Ha az e-mail-értesítések engedélyezve vannak, a rendszer e-maileket küld a felülvizsgálók számára. |
|Folyamatban | A felülvizsgálat megkezdődött. Ha az e-mail-értesítések engedélyezve vannak az e-mailek elküldése a felülvizsgálók számára. A felülvizsgálók a határidőig küldhetnek döntéseket. |
|Befejezése | A felülvizsgálat folyamatban van, és a rendszer elküldi az e-maileket a felülvizsgálati tulajdonosnak. |
|Automatikus áttekintés | A felülvizsgálat a rendszer-felülvizsgálati szakaszban található. A rendszer rögzíti azokat a felhasználókat, akik a javaslatok vagy az előre konfigurált döntések alapján nem voltak ellenőrizve. |
|Automatikusan felülvizsgálva | A rendszer minden olyan felhasználó számára rögzíti a döntéseket, akiket nem vizsgáltak meg. A felülvizsgálat készen áll a **alkalmazásra** , ha engedélyezve van az automatikus alkalmazás. |
|Alkalmazása | A jóváhagyott felhasználók hozzáférése nem változik. |
|Alkalmazva | A megtagadott felhasználókat, ha vannak ilyenek, el lettek távolítva az erőforrásból vagy a címtárból. |
|Sikertelen | Az ellenőrzés nem haladhat előre. Ez a hiba kapcsolódhat a bérlő törléséhez, a licencek változásához vagy a belső bérlő egyéb változásaihoz. |

## <a name="create-reviews-via-apis"></a>Felülvizsgálatok létrehozása API-kon keresztül

Az API-k használatával hozzáférési felülvizsgálatokat is létrehozhat. A csoportok és alkalmazás-felhasználók hozzáférési felülvizsgálatának kezelése a Azure Portalban Microsoft Graph API-k használatával is elvégezhető. További információt az [Azure ad hozzáférési felülvizsgálatok API-referenciája](/graph/api/resources/accessreviews-root?view=graph-rest-beta)című témakörben talál. A mintakód esetében tekintse meg [Az Azure ad hozzáférési felülvizsgálatok Microsoft Graph használatával történő beolvasásának példáját](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférésének ellenőrzése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférésének áttekintése](review-your-access.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)