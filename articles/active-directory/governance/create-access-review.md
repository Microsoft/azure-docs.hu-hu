---
title: Csoportok hozzáférési felülvizsgálatának létrehozása &-alkalmazásokhoz – Azure AD
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot a csoporttagokról vagy az alkalmazás-hozzáférésről Azure Active Directory hozzáférési felülvizsgálatokban.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d1c40c26dd6b6992d8df85a986b4157a22226a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602931"
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

1. A bal oldali menüben kattintson a **hozzáférési felülvizsgálatok**elemre.

1. Új hozzáférési felülvizsgálat létrehozásához kattintson az **új hozzáférés-ellenőrzés** elemre.

    ![Az identitás-irányítás hozzáférési felülvizsgálatok panelje](./media/create-access-review/access-reviews.png)

1. Nevezze el a hozzáférési felülvizsgálatot. Szükség esetén adja meg a Leírás áttekintését. A név és a leírás a felülvizsgálók számára jelenik meg.

    ![Hozzáférési felülvizsgálat létrehozása – a név és a Leírás áttekintése](./media/create-access-review/name-description.png)

1. Állítsa be a **kezdő dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer is megtörténik, és egy hónapon belül megkezdődik. A kezdő és a záró dátum módosításával megkezdheti a hozzáférési felülvizsgálat megkezdését a jövőben, és több napot is megadhat.

    ![Hozzáférési felülvizsgálat létrehozása – kezdési és befejezési dátumok](./media/create-access-review/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődő legyen, módosítsa a **gyakoriság** beállítását **egy alkalommal** **hetente**, **havonta**, **negyedévente**, **félévente**vagy **évente**. Az **időtartam** csúszka vagy a szövegmező segítségével megadhatja, hogy a rendszer hány napig nyissa meg az ismétlődő adatsorozatok összes felülvizsgálatát a véleményezők számára. Például a havi felülvizsgálathoz beállítható maximális időtartam 27 nap, az átfedő felülvizsgálatok elkerülése érdekében.

1. A **befejezési** beállítással adhatja meg az ismétlődő hozzáférés-felülvizsgálati sorozat befejezésének módját. A sorozat három módon végződhet: 
    1. Folyamatosan futtatja az értékelést határozatlan ideig
    1. Egy adott dátumig
    1. Amíg egy meghatározott számú előfordulás nem fejeződött be. 
  
    Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda állíthatja le a sorozatot a létrehozás után, hogy megváltoztatta a dátumot a **beállításokban**, hogy az adott időpontban véget vessen.

1. A **felhasználók** szakaszban válassza ki azokat a felhasználókat, akikre a hozzáférési felülvizsgálat vonatkozik. A hozzáférési felülvizsgálatok lehetnek egy csoport tagjai vagy egy alkalmazáshoz hozzárendelt felhasználók számára. A hozzáférési felülvizsgálat továbbra is kiterjeszthető, hogy csak azok a vendég felhasználók legyenek áttekintve, akik tagjai (vagy az alkalmazáshoz vannak rendelve), és nem tekinti át az alkalmazáshoz hozzáférő összes felhasználót.

    ![Hozzáférési felülvizsgálat létrehozása – felhasználók](./media/create-access-review/users.png)

1. A **csoport** szakaszban válasszon ki egy vagy több olyan csoportot, amelyről meg szeretné tekinteni a tagságát.

    > [!NOTE]
    > Több csoport kiválasztásával több hozzáférési felülvizsgálat jön létre. Például öt csoport kiválasztásával öt külön hozzáférési felülvizsgálat jön létre.
    
    ![Hozzáférési felülvizsgálat létrehozása – csoport kiválasztása](./media/create-access-review/select-group.png)

1. Az **alkalmazások** szakaszban (ha kiválasztotta **egy alkalmazáshoz** a 8. lépésben), válassza ki azokat az alkalmazásokat, amelyeknek át szeretné tekinteni a hozzáférést.

    > [!NOTE]
    > Több alkalmazás kiválasztása több hozzáférési felülvizsgálatot is létrehoz. Ha például öt alkalmazást választ, öt külön hozzáférési felülvizsgálatot fog létrehozni.
    
    ![Hozzáférési felülvizsgálat létrehozása – alkalmazás kiválasztása](./media/create-access-review/select-application.png)

1. A **felülvizsgálók szakaszban válasszon** ki egy vagy több személyt a hatókör összes felhasználójának áttekintéséhez. Azt is megteheti, hogy a tagok a saját hozzáférését vizsgálják felül. Ha az erőforrás egy csoport, megkérheti, hogy a csoport tulajdonosai is áttekintsék. Azt is megkövetelheti, hogy a felülvizsgálók a hozzáférés jóváhagyásakor megadják a szükséges okot.

    ![Hozzáférési felülvizsgálat létrehozása – véleményezők](./media/create-access-review/reviewers.png)

1. A **programok** szakaszban válassza ki a használni kívánt programot. Az **alapértelmezett program** mindig jelen van.

    ![Hozzáférési felülvizsgálati programok létrehozása](./media/create-access-review/programs.png)

    A hozzáférési felülvizsgálatok gyűjtését és nyomon követését leegyszerűsítheti a programokba való rendszerezéssel. Az egyes hozzáférési felülvizsgálatok csatolhatók egy programhoz. Ezután amikor jelentést készít egy auditor számára, az adott kezdeményezés hatókörében lévő hozzáférési felülvizsgálatokra koncentrálhat. A programok és a hozzáférés-felülvizsgálati eredmények a globális rendszergazda, a felhasználói rendszergazda, a biztonsági rendszergazda vagy a biztonsági olvasó szerepkör felhasználói számára láthatók.

    A programok listájának megtekintéséhez nyissa meg a hozzáférési felülvizsgálatok lapot, és válassza a **programok**lehetőséget. Ha globális rendszergazdai vagy felhasználói rendszergazdai szerepkörrel rendelkezik, további programokat is létrehozhat. Dönthet például úgy, hogy az egyes megfelelőségi kezdeményezésekhez vagy üzleti célokhoz egy programot használ. Ha már nincs szüksége egy programra, és nem rendelkezik hozzá társított vezérlőkkel, akkor törölheti.

### <a name="upon-completion-settings"></a>Befejezési beállítások

1. Ha meg szeretné határozni, hogy mi történik egy ellenőrzés befejezése után, bontsa ki a **befejezési beállítások** szakaszban.

    ![Hozzáférési felülvizsgálat létrehozása a befejezési beállítások alapján](./media/create-access-review/upon-completion-settings-new.png)

2. Ha azt szeretné, hogy a rendszer automatikusan eltávolítsa a hozzáférést a megtagadott felhasználók számára, állítsa az **eredmények automatikus alkalmazása az erőforrásra** lehetőséget az **engedélyezéshez**. Ha a felülvizsgálat befejeződése után manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót a **Letiltás**lehetőségre.

3. Ha a felülvizsgálók nem **válaszolnak** a listára, adja meg, hogy mi történik azon felhasználók esetében, akiket a felülvizsgálati időszakon belül nem tekintenek át a véleményező. Ez a beállítás nem érinti azokat a felhasználókat, akiket manuálisan ellenőriztek a véleményezők. Ha megtagadja a végső felülvizsgáló döntését, a rendszer eltávolítja a felhasználó hozzáférését.

    - **Nincs változás** – a felhasználó hozzáférése változatlan marad
    - **Hozzáférés eltávolítása** – a felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – a felhasználó hozzáférésének jóváhagyása
    - **Javaslatok készítése** – a rendszer javaslata a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

    ![Hozzáférési felülvizsgálat létrehozása – speciális beállítások](./media/create-access-review/advanced-settings-preview-new.png)

4. Előnézet A megtagadott felhasználókra vonatkozó művelet végrehajtásával adhatja meg, hogy mi történik a vendég felhasználókkal, ha azok meg vannak tagadva.
    - Az **1. lehetőség** eltávolítja a megtagadott felhasználó hozzáférését az áttekintett csoporthoz vagy alkalmazáshoz, továbbra is bejelentkezhet a bérlőbe. 
    - A **2. lehetőség** megakadályozza, hogy a megtagadott felhasználók bejelentkezzenek a bérlőbe, függetlenül attól, hogy vannak-e hozzáférésük más erőforrásokhoz. Ha hiba történt, vagy ha egy rendszergazda úgy dönt, hogy újra engedélyezi az egyik hozzáférését, akkor a felhasználó letiltását követő 30 napon belül megteheti. Ha a letiltott felhasználók nem végeznek műveleteket, a rendszer törli a bérlőről.

Ha többet szeretne megtudni az olyan vendég felhasználók eltávolítására vonatkozó ajánlott eljárásokról, akik már nem férnek hozzá a szervezet erőforrásaihoz, olvassa el a [Azure ad Identity Governance használata című cikket, amellyel áttekintheti és eltávolíthatja azokat a külső felhasználókat, akik már nem rendelkeznek erőforrás-hozzáféréssel.](access-reviews-external-users.md)

>[!NOTE]
> A megtagadott felhasználókra alkalmazandó művelet csak akkor működik, ha korábban csak a vendég felhasználóinak tekinti át a felülvizsgálatot (lásd: **egy vagy több hozzáférési felülvizsgálat létrehozása szakasz,** 8. lépés)

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások** szakaszt.

1. Állítsa be a **javaslatok megjelenítése** lehetőséget **, hogy** megjelenjenek a felülvizsgálók a felhasználó hozzáférési adatai alapján.

1. Ha **engedélyezni** szeretné, hogy a felülvizsgáló megkövetelje a jóváhagyás okát, meg kell adnia a **jóváhagyáshoz szükséges okot** .

1. E- **mail-értesítések** beállításával **engedélyezheti** , hogy az Azure ad e-mailes értesítéseket küldjön a felülvizsgálók számára a hozzáférési felülvizsgálat indításakor, valamint a rendszergazdáknak a felülvizsgálat befejezésekor.

1. Az **emlékeztetők** beállításával **engedélyezheti** , hogy az Azure ad emlékeztetőket küldjön a folyamatban lévő hozzáférési felülvizsgálatokról azon felülvizsgálók számára, akik nem fejezték be a felülvizsgálatot. 

    >[!NOTE]
    > Alapértelmezés szerint az Azure AD automatikusan emlékeztetőt küld a befejezési dátumig a még nem válaszoló felülvizsgálók felé.

1. Előnézet A felülvizsgálók számára küldött e-mailek tartalma automatikusan létrejön a felülvizsgálati adatok alapján, például a felülvizsgálat neve, az erőforrás neve, a határidő stb. Ha további információk, például további utasítások vagy kapcsolattartási adatok közlésére van szüksége, megadhatja ezeket a részleteket a **felülvizsgáló e-mail** -címéhez, amely szerepelni fog a meghívóban és a hozzárendelt felülvizsgálók számára küldött emlékeztető e-mailekben. A Kiemelt szakasz az alábbi információkat jeleníti meg.

    ![Felhasználók hozzáférésének áttekintése egy csoporthoz](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start**gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

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

## <a name="create-reviews-via-apis"></a>Felülvizsgálatok létrehozása API-kon keresztül

Az API-k használatával hozzáférési felülvizsgálatokat is létrehozhat. A csoportok és alkalmazás-felhasználók hozzáférési felülvizsgálatának kezelése a Azure Portalban Microsoft Graph API-k használatával is elvégezhető. További információt az [Azure ad hozzáférési felülvizsgálatok API-referenciája](/graph/api/resources/accessreviews-root?view=graph-rest-beta)című témakörben talál. A mintakód esetében tekintse meg [Az Azure ad hozzáférési felülvizsgálatok Microsoft Graph használatával történő beolvasásának példáját](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférésének ellenőrzése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférésének áttekintése](review-your-access.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)