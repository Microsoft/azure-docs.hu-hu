---
title: Azure Active Directory hozzáférési felülvizsgálatok üzembe helyezésének megtervezése
description: Tervezési útmutató a sikeres hozzáférési felülvizsgálatok üzembe helyezéséhez
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4122e645b76751e8944704a6405cf5dee09129f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932435"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Azure Active Directory hozzáférési felülvizsgálatok központi telepítésének tervezése

[Azure Active Directory (Azure ad) hozzáférési felülvizsgálatok](access-reviews-overview.md) segítségével a szervezet biztonságosabban megtarthatja a hálózatot az [erőforrás-hozzáférési életciklusának](identity-governance-overview.md)kezelésével. A hozzáférési felülvizsgálatok segítségével a következőket teheti:

* Rendszeres értékeléseket ütemezhet, vagy ad-hoc felülvizsgálatokat hajthat végre, hogy megismerje, ki férhet hozzá bizonyos erőforrásokhoz, például alkalmazásokhoz és csoportokhoz.

* Értékelések követése az adatvizsgálatok, a megfelelőség és a szabályzatok miatt

* Felülvizsgálatok delegálása adott rendszergazdák, üzleti tulajdonosok vagy végfelhasználók számára, akik önmagukban tudják igazolni a folyamatos hozzáférés szükségességét

* Az megállapítások segítségével hatékonyan meghatározhatja, hogy a felhasználók továbbra is rendelkeznek-e hozzáféréssel

* Automatizálja a felülvizsgálatok eredményeit, például a felhasználók erőforrásokhoz való hozzáférésének eltávolítását

  ![A hozzáférési felülvizsgálatok folyamatát ábrázoló diagram.](./media/deploy-access-review/1-planning-review.png)

A hozzáférési felülvizsgálatok egy [Azure ad Identity Governance](identity-governance-overview.md) képesség. A többi lehetőség a [jogosultságok kezelése](entitlement-management-overview.md), a [Privileged Identity Management](../privileged-identity-management/pim-configure.md) és a [használati feltételek](../conditional-access/terms-of-use.md). Együttesen segítenek a szervezeteknek a következő négy kérdés megválaszolásában:

* Mely felhasználók férhetnek hozzá az erőforrásokhoz?

* Mik azok a felhasználók, akik ezt a hozzáférést használják?

* Van-e hatékony szervezeti vezérlés a hozzáférések kezeléséhez?

* Ellenőrizhető, hogy a vezérlők működnek-e?

A hozzáférési felülvizsgálatok üzembe helyezésének megtervezése elengedhetetlen ahhoz, hogy biztosan elérje a kívánt irányítási stratégiát a szervezet felhasználói számára.

### <a name="key-benefits"></a>Főbb előnyök

A hozzáférési felülvizsgálatok használatának legfontosabb előnyei a következők:

* **Együttműködés vezérlése**. A hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára, hogy a felhasználók által igényelt összes erőforráshoz hozzáférjenek. Ha a felhasználók megosztják és együttműködnek, a szervezetek biztosak lehetnek abban, hogy az információ csak a csak a felhatalmazott felhasználók között van.

* **Kockázatkezelés**. A hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára, hogy az adatszivárgás és az adatvesztés kockázatának csökkentése érdekében vizsgálják át az adatkezelés és az alkalmazások hozzáférését. Ez olyan képességeket is tartalmaz, amelyekkel rendszeresen ellenőrizheti a külső partner vállalati erőforrásokhoz való hozzáférését. 

* **Foglalkozzon a megfelelőséggel és a szabályozással**. A hozzáférési felülvizsgálatok segítségével a csoportok, alkalmazások és webhelyek hozzáférési életciklusát szabályozhatja és újra hitelesítheti. A szervezete számára meghatározott megfelelőségi vagy kockázati szempontból kényes alkalmazások nyomon követését is szabályozhatja. 

* **Csökkentse a költségeket**. A hozzáférési felülvizsgálatok a felhőben vannak felépítve, és natív módon működnek a Felhőbeli erőforrásokkal, például a csoportokkal, alkalmazásokkal és hozzáférési csomagokkal. A hozzáférési felülvizsgálatok használata kevésbé költséges, mint a saját eszközeinek kiépítése vagy más módon a helyszíni eszközkészlet frissítése.

### <a name="training-resources"></a>Erőforrások betanítása

A következő videók hasznosak lehetnek a hozzáférési felülvizsgálatok megismeréséhez:

* [Mik azok a hozzáférési felülvizsgálatok az Azure AD-ben?](https://youtu.be/kDRjQQ22Wkk)

* [Hozzáférési felülvizsgálatok létrehozása az Azure AD-ben](https://youtu.be/6KB3TZ8Wi40)

* [Hozzáférési felülvizsgálatok engedélyezése az Azure AD-ben](https://youtu.be/X1SL2uubx9M)

* [Hozzáférés ellenőrzése a saját hozzáférés használatával](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licencek

Érvényes prémium szintű Azure AD (P2) licencre van szükség minden olyan személyhez, amely a globális rendszergazdák vagy a felhasználói rendszergazdák számára is szükséges, akik hozzáférési felülvizsgálatokat hoznak létre vagy hajtanak végre. További információ: hozzáférés-felülvizsgálati [licencekre vonatkozó követelmények](access-reviews-overview.md).

Szükség lehet más identitás-irányítási funkciókra is, például a [jogosultságok életciklusának felügyeletére](entitlement-management-overview.md) vagy a privilegizált identitások kezelésére. Ebben az esetben szükség lehet a kapcsolódó licencekre is. További információ: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>A hozzáférési felülvizsgálatok telepítési Projektének megtervezése

Gondolja át, hogy a szervezetnek meg kell határoznia a hozzáférési felülvizsgálatok környezetében való üzembe helyezésére vonatkozó stratégiát.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és a projekt szerepkörei egyértelműek.

A hozzáférési felülvizsgálatok esetében valószínű, hogy a szervezeten belül a következő csapatok képviselői szerepelnek:

* Az **it-adminisztráció** kezeli az informatikai infrastruktúrát, és felügyeli a Felhőbeli és a szolgáltatott szoftverek (SaaS) alkalmazásait. A csapat a következőket teszi:

   * Tekintse át az infrastruktúrához és alkalmazásokhoz való emelt szintű hozzáférést, beleértve a Microsoft 365 és az Azure AD-t.

   * Az aktuális hozzáférési listák fenntartása érdekében ütemezzen és futtasson hozzáférési felülvizsgálatokat azokon a csoportokon, amelyek a kivételi listák vagy az informatikai kísérleti projektek fenntartására szolgálnak.

   * Győződjön meg arról, hogy a szolgáltatás-rendszerbiztonsági tag által az erőforrásokhoz való programozott (megírt) hozzáférés szabályozása és felülvizsgálata megtörténik.

* A **fejlesztői csapatok** alkalmazásokat hozhatnak létre és kezelhetnek a szervezet számára. A csapat a következőket teszi:

   * Szabályozhatja, hogy ki férhet hozzá az összetevőkhöz, és hogyan kezelheti azokat az SaaS-, a Pásti-és a IaaS-erőforrásokban, amelyek a kifejlesztett

   * A belső alkalmazások fejlesztéséhez alkalmazásokat és eszközöket elérő csoportok kezelése.

   * Olyan privilegizált identitások megkövetelése, amelyek hozzáférnek az ügyfelek számára üzemeltetett éles szoftverekhez vagy megoldásokhoz

* Az **üzleti egységek** a projekteket és a saját alkalmazásokat kezelik. A csapat a következőket teszi: 

   * A belső és külső felhasználókhoz tartozó csoportokhoz és alkalmazásokhoz való hozzáférés ellenőrzése és jóváhagyása vagy elutasítása.

   * Az alkalmazottak és a külső identitások, például az üzleti partnerek folyamatos hozzáférését tanúsító értékeléseket ütemezhet és hajt végre.

* A **vállalat irányítása** biztosítja, hogy a szervezet a belső szabályzatot követi, és megfelel a szabályozásoknak. A csapat a következőket teszi:

   * Új hozzáférési felülvizsgálatok kérése vagy beütemezett időpontja.

   * A hozzáférés áttekintésére szolgáló folyamatokat és eljárásokat, beleértve a dokumentációt és a megfelelőségi nyilvántartást is.

   * Tekintse át a kritikus fontosságú erőforrások múltbeli felülvizsgálatának eredményeit.

> [!NOTE]
> A manuális értékelést igénylő felülvizsgálatok esetében ügyeljen arra, hogy megtervezze a megfelelő felülvizsgálók és a szabályzatoknak és a megfelelőségi igényeknek megfelelő felülvizsgálati ciklusokat. Ha a felülvizsgálati ciklusok túl gyakoriak, vagy túl kevés a véleményező, akkor a minőség elvész, és túl sok vagy túl kevés személy férhet hozzá. 

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új üzleti folyamat sikere szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogyan és Mikor változnak, és hogyan szerezhetnek támogatást, ha problémákat tapasztalnak. 

#### <a name="communicate-changes-in-accountability"></a>A változások elszámoltathatóságának közlése

A hozzáférési felülvizsgálatok támogatják a felülvizsgáló és az üzleti tulajdonosok folyamatos hozzáférésének átváltási feladatait. A hozzáférési döntések leválasztása az IT-meghajtókon pontosabb hozzáférési döntéseket hoz. Ez az erőforrás-tulajdonos elszámoltathatóságának és felelősségének kulturális változása. Proaktív módon értesítheti ezt a változást, és gondoskodhat az erőforrás-tulajdonosok betanításáról és az elemzések használatáról a jó döntések elvégzéséhez.

Egyértelmű, hogy az infrastruktúrával kapcsolatos összes hozzáférési döntés és a Kiemelt szerepkör-hozzárendelések esetében is érdemes maradnia. 

#### <a name="customize-email-communication"></a>E-mail-kommunikáció testreszabása

A felülvizsgálatok beütemezhetik azokat a felhasználókat, akik elvégzik a felülvizsgálatot. Ezek a felülvizsgálók ezután e-mailben értesítik a hozzájuk rendelt új felülvizsgálatokról, valamint a hozzájuk rendelt felülvizsgálati időszak lejárta előtt emlékeztetőket kapnak.

A rendszergazdák dönthetnek úgy, hogy a felülvizsgálati időszak lejárta előtt vagy egy nappal a lejárat előtt elküldik ezt az értesítést. 

A felülvizsgálók számára elküldött e-mailek testreszabhatók úgy, hogy egy egyéni rövid üzenetet is felhasználhatnak, amely arra ösztönzi őket, hogy a felülvizsgálat során fellépjenek. Javasoljuk, hogy a további szöveget a következő szövegre használja:

* Vegyen fel egy személyes üzenetet a felülvizsgálók számára, hogy azok megértsék a megfelelőség vagy az IT-részleg által küldött üzeneteket.

* Adjon meg egy hivatkozást, vagy hivatkozzon a belső információkra a felülvizsgálat elvárásainak, valamint a további referenciák vagy tananyagok alapján.

* Adjon meg egy hivatkozást, amely útmutatást nyújt a [hozzáférés önellenőrzésének végrehajtásához.](review-your-access.md) 

  ![Felülvizsgáló e-mail címe](./media/deploy-access-review/2-plan-reviewer-email.png)

A Start Review utasítás kiválasztása után a felülvizsgálók a csoport-és alkalmazás-hozzáférési felülvizsgálatok [myAccess-portálra](https://myapplications.microsoft.com/) lesznek irányítva. A portál áttekintést nyújt azokról a felhasználókról, akik hozzáférnek a megtekintett erőforráshoz, valamint a legutóbbi bejelentkezési és hozzáférési információk alapján történő rendszer-ajánlásokhoz.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Javasoljuk ügyfeleinknek, hogy kezdetben egy kis csoporttal és a nem kritikus fontosságú erőforrásokkal való kezdeti hozzáférési felülvizsgálatokat. A tesztelés segítségével szükség szerint állíthatja be a folyamatokat és a kommunikációt, és növelheti a felhasználók és a felülvizsgálók képességét a biztonsági és megfelelőségi követelmények kielégítése érdekében.

A pilóta azt ajánljuk, hogy:

* Kezdje olyan értékelésekkel, amelyekben az eredmények nem lesznek automatikusan alkalmazva, és szabályozhatja a következményeket.

* Győződjön meg arról, hogy minden felhasználó rendelkezik az Azure AD-ben felsorolt érvényes e-mail-címekkel, és e-mailben értesítést kap a megfelelő művelet elvégzéséhez. 

* Ha gyorsan vissza szeretné állítani, dokumentálja a teszt részeként eltávolított összes hozzáférést.

* Figyelje a naplókat, hogy minden esemény megfelelően legyen naplózva.

További információ: [ajánlott eljárások a pilóta](../fundamentals/active-directory-deployment-plans.md)számára.

## <a name="introduction-to-access-reviews"></a>A hozzáférési felülvizsgálatok bemutatása

Ez a szakasz bemutatja a hozzáférés-felülvizsgálati fogalmakat, amelyeket érdemes tudni a felülvizsgálatok tervezése előtt.

### <a name="what-resource-types-can-be-reviewed"></a>Milyen típusú erőforrásokat lehet áttekinteni?

Miután integrálta a szervezet erőforrásait az Azure AD-vel (például felhasználók, alkalmazások és csoportok), felügyelheti és áttekintheti őket. 

Az ellenőrzéshez használt tipikus célok a következők:

* Az [Azure ad-vel integrált alkalmazások egyszeri bejelentkezéshez](../manage-apps/what-is-application-management.md) (például SaaS, üzletági).

* Csoporttagság [(](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) szinkronizálva az Azure ad-vel, vagy az Azure ad-ben vagy Microsoft 365ban, beleértve a Microsoft Teams-t).

* Olyan [hozzáférési csomag](./entitlement-management-overview.md) , amely az erőforrásokat (csoportokat, alkalmazásokat és helyeket) egyetlen csomagba csoportosítja a hozzáférés kezeléséhez.

* [Azure ad-szerepkörök és Azure-erőforrás-szerepkörök](../privileged-identity-management/pim-resource-roles-assign-roles.md) Privileged Identity Managementban definiált módon.

### <a name="who-will-create-and-manage-access-reviews"></a>Ki hozza létre és kezeli a hozzáférési felülvizsgálatokat?

A hozzáférési felülvizsgálat létrehozásához, kezeléséhez vagy olvasásához szükséges rendszergazdai szerepkör az áttekintett erőforrás típusától függ. Az alábbi táblázat az egyes erőforrástípusok számára szükséges szerepköröket jelöli:

| Erőforrás típusa| Hozzáférési felülvizsgálatok létrehozása és kezelése (Creators)| Olvasási hozzáférési felülvizsgálati eredmények |
| - | - | -|
| Csoport vagy alkalmazás| Globális rendszergazda <p>Felhasználói rendszergazda| Alkotók és biztonsági rendszergazda |
| Kiemelt szerepkörök az Azure AD-ben| Globális rendszergazda <p>Kiemelt szerepkörű rendszergazda| Alkotói <p>Biztonsági olvasó<p>Biztonsági rendszergazda |
| Kiemelt szerepkörök az Azure-ban (erőforrások)| Globális rendszergazda<p>Felhasználói rendszergazda<p>Erőforrás tulajdonosa| Alkotói |
| Hozzáférési csomag| Globális rendszergazda<p>A hozzáférési csomag létrehozója| Csak globális rendszergazda |


További információ: [rendszergazdai szerepkör engedélyei Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Ki fogja áttekinteni az erőforráshoz való hozzáférést?

A hozzáférési felülvizsgálat létrehozója a létrehozás időpontjában dönti el, hogy ki fogja elvégezni a felülvizsgálatot. Ez a beállítás a felülvizsgálat elindítása után nem módosítható. A felülvizsgálók három személy által reprezentáltak:

* Erőforrás-tulajdonosok, akik az erőforrás üzleti tulajdonosai.

* A hozzáférési felülvizsgálatok rendszergazdája által kiválasztott, egyenként kiválasztott meghatalmazottak összessége.

* A végfelhasználók, akik önmagukban tanúsítják, hogy továbbra is hozzáférést igényelnek.

Hozzáférési felülvizsgálat létrehozásakor a rendszergazdák egy vagy több felülvizsgáló közül választhatnak. Az összes felülvizsgáló elindíthatja és elvégezheti a felülvizsgálatot, és a felhasználók számára kiválaszthatja az erőforrásokhoz való folyamatos hozzáférést, vagy eltávolíthatja őket. 

### <a name="components-of-an-access-review"></a>Hozzáférési felülvizsgálat összetevői

A hozzáférési felülvizsgálatok megvalósítása előtt tervezze meg a szervezete számára releváns felülvizsgálati típusokat. Ehhez üzleti döntéseket kell hoznia arról, hogy mit szeretne áttekinteni, és milyen műveleteket kell végrehajtania a felülvizsgálatok alapján.

Hozzáférési felülvizsgálati szabályzat létrehozásához a következő információk szükségesek.

* Milyen erőforrás (oka) t szeretne áttekinteni?

* A hozzáférés felülvizsgálata folyamatban van.

* Milyen gyakran történjen a felülvizsgálat?

* Ki fogja elvégezni a felülvizsgálatot?

   * Hogyan értesülnek a rendszer a felülvizsgálatról?

   * Milyen határidőkkel kell érvényesíteni a felülvizsgálatot?

* Milyen automatikus műveleteket kell kikényszeríteni a felülvizsgálat alapján?

   * Mi történik, ha a felülvizsgáló nem válaszol időben?

* Milyen manuális műveletekre kerül sor a felülvizsgálat alapján?

* Milyen kommunikációt kell elküldeni a végrehajtott műveletek alapján?


**Példa hozzáférési felülvizsgálati tervre**

| Összetevő| Érték |
| - | - |
| **Áttekinthető erőforrások**| Hozzáférés a Microsoft Dynamicshoz |
| **Felülvizsgálati gyakoriság**| Havonta |
| **A felülvizsgálatot végző személy**| Dynamics Business Group program-kezelők |
| **Értesítés**| E-mailt 24 órával az alias áttekintése előtt Dynamics-Pms<p>Bemutatjuk, hogy a megvásárolt egyéni üzenetet a felülvizsgálók számára is ösztönözzék |
| **Idővonal**| 48 óra az értesítésből |
|**Automatikus műveletek**| Távolítsa el a hozzáférést bármely olyan fiókból, amely 90 napon belül nem rendelkezik interaktív bejelentkezéssel, ha eltávolítja a felhasználót a biztonsági csoport Dynamics-Access szolgáltatásból. <p>*Műveleteket hajthat végre, ha nem tekinti át az időkeretet.* |
| **Manuális műveletek**| A felülvizsgálók elvégezhetik az Eltávolítás jóváhagyását az automatizált művelet előtt, ha szükséges. |
| **Tájékoztatások**| Olyan belső (tag) felhasználók elküldése, akik el lettek távolítva az e-mailek eltávolításáról és a hozzáférés visszaszerzéséről. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Műveletek automatizálása hozzáférési felülvizsgálatok alapján

Kiválaszthatja, hogy a hozzáférés-eltávolítás automatizált legyen. ehhez az eredmények automatikus alkalmazása az erőforrásra beállítást kell beállítani az engedélyezéshez.

  ![Hozzáférési felülvizsgálatok tervezése](./media/deploy-access-review/3-automate-actions-settings.png)

A felülvizsgálat befejezése után a felülvizsgáló által nem jóváhagyott felhasználók automatikusan el lesznek távolítva az erőforrásból – vagy folyamatos hozzáférés mellett maradnak. Ez azt jelentheti, hogy a csoporttagság, az alkalmazás-hozzárendelésük, vagy a jogosultságuk visszavonása egy emelt szintű szerepkörre.

Javaslatok készítése

A javaslatok a felülvizsgáló felületének részeként jelennek meg a felülvizsgálók számára, és jelzik a személy utolsó bejelentkezését a bérlőnek, vagy egy alkalmazás utolsó hozzáférését. Ez az információ segíti a felülvizsgálók számára a megfelelő hozzáférési döntés elvégzését. A javaslatok elvégzése lehetőség kiválasztásakor a hozzáférés-felülvizsgálati javaslatok is megjelennek. A hozzáférési felülvizsgálat végén a rendszer automatikusan alkalmazza ezeket az ajánlásokat azokra a felhasználókra, akik számára a véleményezők nem válaszolt.

A javaslatok a hozzáférési felülvizsgálat feltételein alapulnak. Ha például úgy konfigurálja a felülvizsgálatot, hogy a 90 napos interaktív bejelentkezés nélkül is eltávolítsa a hozzáférést, akkor azt javasoljuk, hogy az összes olyan felhasználót, aki megfelel a feltételnek, el kell távolítania. A Microsoft folyamatosan dolgozik a javaslatok tökéletesítésén. 

### <a name="review-guest-user-access"></a>Vendég felhasználói hozzáférés áttekintése

A hozzáférési felülvizsgálatok segítségével áttekintheti és törölheti az együttműködési partnerek identitásait a külső szervezeteknél. A partneri felülvizsgálatok beállítása megfelel a megfelelőségi követelményeknek.

A külső identitások a következő műveletek egyikével kaphatnak hozzáférést a vállalati erőforrásokhoz:

* Hozzáadva egy csoporthoz 

* Csapatok meghívása 

* Vállalati alkalmazásokhoz vagy hozzáférési csomagokhoz társítva

* Kiemelt szerepkör hozzárendelése az Azure AD-ben vagy egy Azure-előfizetésben

Lásd: [minta parancsfájl](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). A szkript megmutatja, hogy a bérlőn meghívott külső identitások hol vannak használatban. Megtekintheti a külső felhasználó csoporttagság, szerepkör-hozzárendelések és az alkalmazás-hozzárendelések az Azure AD szolgáltatásban című témakört. A parancsfájl nem jeleníti meg az Azure AD-n kívüli hozzárendeléseket, például a közvetlen jogosultság-hozzárendelést a SharePoint-erőforrásokhoz, csoportok használata nélkül.

A csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozásakor dönthet úgy, hogy a felülvizsgáló mindenki számára elérhető, vagy csak vendég felhasználóra koncentrál. A csak vendég felhasználók lehetőség kiválasztásával a felülvizsgálók az Azure AD B2B-ből származó külső identitások célzott listáját kapják meg, amelyek hozzáférnek az erőforráshoz.

 ![Vendég felhasználók áttekintése](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Ez nem fogja tartalmazni a userType rendelkező külső tagokat. Ez nem tartalmazza az Azure AD B2B együttműködésen kívül meghívó felhasználókat is, például azokat, akik közvetlenül a SharePointon keresztül férnek hozzá a megosztott tartalomhoz.

## <a name="plan-access-reviews-for-access-packages"></a>Hozzáférési csomagok hozzáférési felülvizsgálatának megtervezése

A [hozzáférési csomagok](entitlement-management-overview.md) jelentősen leegyszerűsítik az irányítási és hozzáférési felülvizsgálati stratégiát. A hozzáférési csomag az összes olyan erőforrás kötegét képezi, amelynek hozzáférését a felhasználónak egy projekten kell dolgoznia, vagy a feladat elvégzéséhez. Előfordulhat például, hogy olyan hozzáférési csomagot szeretne létrehozni, amely tartalmazza az összes olyan alkalmazást, amely a szervezet fejlesztői számára szükséges, vagy minden olyan alkalmazáshoz, amelyhez a külső felhasználóknak hozzáférésre van szükségük. A rendszergazda vagy a meghatalmazott hozzáférés-kezelő csomagkezelő ezután csoportosítja az erőforrásokat (csoportokat vagy alkalmazásokat), valamint azokat a szerepköröket, amelyekre a felhasználóknak szüksége van az adott erőforrásokhoz.

[Hozzáférési csomag létrehozásakor](entitlement-management-access-package-create.md)létrehozhat egy vagy több olyan hozzáférési szabályzatot, amely olyan feltételeket határoz meg, amelyekkel a felhasználók hozzáférési csomagokat igényelhetnek, a jóváhagyási folyamat pedig azt, hogy egy személy milyen gyakran kér újra hozzáférést. A hozzáférési felülvizsgálatok a hozzáférési csomag házirendjének létrehozásakor vagy szerkesztésekor konfigurálhatók.

A hozzáférési felülvizsgálatok megtekintéséhez nyissa meg az életciklus lapot.

 ![Képernyőkép, amely az "életciklus" lapon megjeleníti a "szabályzat szerkesztése" lehetőséget.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Csoportok hozzáférési felülvizsgálatának megtervezése

A hozzáférési csomagok mellett a csoporttagság áttekintésének leghatékonyabb módja a hozzáférés szabályozása. Azt javasoljuk, hogy az erőforrásokhoz való hozzáférést [biztonsági csoportokon vagy Microsoft 365 csoportokon](../fundamentals/active-directory-manage-groups.md)keresztül rendelje hozzá, és hogy a felhasználók hozzáférjenek a csoporthoz.

Egyetlen csoport is jogosult az összes megfelelő erőforráshoz való hozzáférésre. Hozzárendelheti a csoporthoz az egyes erőforrásokhoz való hozzáférést, illetve az alkalmazásokat és egyéb erőforrásokat csoportosító hozzáférési csomagokat. Ezzel a módszerrel áttekintheti a csoport hozzáférését, nem pedig az egyes alkalmazásokhoz való hozzáférést. 

A csoporttagság a következőket tekintheti át: 

* Rendszergazdák

* Csoport tulajdonosainak

* Kiválasztott felhasználók, delegált felülvizsgálati képesség a felülvizsgálat létrehozásakor

* A csoport tagjai, akik tanúsítják magukat

### <a name="group-ownership"></a>Csoport tulajdonjoga

Javasoljuk, hogy a csoport tulajdonosai tekintsék meg a tagságot, mivel azok a legjobbak, akiknek a hozzáférésre van szükségük. A csoportok tulajdonjoga különbözik a csoport típusától:

A Microsoft 365 és az Azure AD-ben létrehozott csoportok egy vagy több jól definiált tulajdonossal rendelkeznek. A legtöbb esetben ezek a tulajdonosok tökéletes véleményezőket végeznek a saját csoportjaikban, és tudják, hogy kinek van hozzáférésük. 

A Microsoft Teams például Microsoft 365 csoportokat használ a mögöttes engedélyezési modellként, hogy hozzáférést biztosítson a felhasználóknak a SharePoint, az Exchange, a OneNote vagy más Microsoft 365-szolgáltatásokban található erőforrásokhoz. A csapat létrehozója automatikusan tulajdonosa lesz, és felelős a csoport tagságának igazolásához. 

Az Azure AD-portálon vagy a Microsoft Graph parancsfájlokon keresztül manuálisan létrehozott csoportok nem feltétlenül rendelkeznek a tulajdonosok által definiált csoportokkal. Azt javasoljuk, hogy az Azure AD-portálon keresztül definiálja őket a csoport "tulajdonosok" szakaszában vagy a gráfon keresztül.

A helyszíni Active Directoryról szinkronizált csoportok nem rendelkezhetnek tulajdonossal az Azure AD-ben. Amikor hozzáférési felülvizsgálatot hoz létre számukra, ki kell választania azokat a személyeket, akik a legjobban megfelelnek a tagságuk eldöntéséhez.

> [!NOTE]
> Javasoljuk, hogy definiáljon olyan üzleti házirendeket, amelyek meghatározzák a csoportok létrehozását, így biztosítva a csoport tulajdonjogának és elszámoltathatóságának egyértelművé tételét a tagság rendszeres áttekintéséhez. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Kizárási csoportok tagságának áttekintése a feltételes hozzáférési házirendekben 

Időnként előfordulhat, hogy a hálózat biztonságának megőrzésére tervezett feltételes hozzáférési szabályzatok nem vonatkoznak az összes felhasználóra. Például egy feltételes hozzáférési szabályzat, amely csak a vállalati hálózaton belüli bejelentkezést engedélyezi a felhasználók számára, előfordulhat, hogy nem vonatkozik az értékesítési csapatra, amely nagy mértékben utazik. Ebben az esetben az értékesítési csapat tagjai egy csoportba kerülnek, és ezt a csoportot kizárják a feltételes hozzáférési szabályzatból. 

Rendszeresen tekintse át az ilyen csoporttagság-tagságot, mivel a kizárás potenciális kockázatot jelent, ha a nem megfelelő tagokat kizárják a követelményből.

Az [Azure ad hozzáférési felülvizsgálatok segítségével a feltételes hozzáférési szabályzatokból kizárt felhasználókat kezelheti](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Külső felhasználó csoporttagságok áttekintése

A manuális munka és a kapcsolódó lehetséges hibák minimálisra csökkentése érdekében érdemes [dinamikus csoportokat](../enterprise-users/groups-create-rule.md) használni a csoporttagság hozzárendeléséhez a felhasználó attribútumai alapján. Érdemes lehet egy vagy több dinamikus csoportot létrehozni a külső felhasználók számára. A belső szponzor a csoport tagságának felülvizsgáló tagja lehet. 

Megjegyzés: a csoportból eltávolított külső felhasználók a hozzáférési felülvizsgálat eredményeként nem törlődnek a bérlőből. 

A bérlők törölhető manuálisan, vagy egy parancsfájl használatával is.

### <a name="review-access-to-on-premises-groups"></a>Helyszíni csoportok elérésének áttekintése

A hozzáférési felülvizsgálatok nem változtathatják meg a csoportokban szinkronizált csoportok tagságát [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md). Ennek az az oka, hogy a hatóság forrása a helyszínen van.

A hozzáférési felülvizsgálatok továbbra is használhatók a helyszíni csoportok rendszeres felülvizsgálatának megkezdéséhez és karbantartásához. A felülvizsgálók ezután végrehajtanak egy műveletet a helyszíni csoportban. Ez a stratégia az összes felülvizsgálat eszközeként tartja a hozzáférési felülvizsgálatokat.

Az eredményeket a helyszíni csoportok hozzáférési felülvizsgálatának eredményei alapján is felhasználhatja, a következők szerint:

* Töltse le a CSV-jelentést a hozzáférési felülvizsgálatból, és manuálisan végezze el a műveletet.

* A Microsoft Graph használatával programozott módon érheti el az eredményeket és a döntéseket a befejezett hozzáférési felülvizsgálatokban.

Ha például egy Windows AD által felügyelt csoport eredményeit szeretné elérni, használja ezt a [PowerShell-minta parancsfájlt](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). A parancsfájl ismerteti a szükséges gráf-hívásokat, és exportálja a Windows AD-PowerShell parancsokat a módosítások végrehajtásához.

## <a name="plan-access-reviews-for-applications"></a>Alkalmazások hozzáférési felülvizsgálatának megtervezése 

Amikor áttekinti az alkalmazáshoz való hozzáférést, az alkalmazottak és a külső identitások hozzáférését az alkalmazásban található információkhoz és adatokhoz tekinti át. Válassza az alkalmazás áttekintését, ha tudnia kell, hogy ki férhet hozzá egy adott alkalmazáshoz egy hozzáférési csomag vagy egy csoport helyett. 

Javasoljuk, hogy a következő esetekben tervezze meg az alkalmazások értékelését:

* A felhasználók közvetlen hozzáférést kapnak az alkalmazáshoz (egy csoport-vagy hozzáférési csomagon kívül).

* Az alkalmazás kritikus vagy bizalmas adatokat tesz elérhetővé.

* Az alkalmazásnak konkrét megfelelőségi követelményekkel kell rendelkeznie, amelyeknek igazolnia kell.

* A nem megfelelő hozzáférés gyanúja merül fel.

Egy alkalmazás hozzáférési felülvizsgálatának létrehozásához meg kell adni a felhasználó-hozzárendelést? tulajdonságot Igen értékre. Ha a nem értékre van állítva, a címtárban lévő összes felhasználó, beleértve a külső identitásokat, hozzáférhet az alkalmazáshoz, és nem tekintheti át az alkalmazáshoz való hozzáférést. 

 ![Alkalmazás-hozzárendelések megtervezése](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Ezután hozzá kell [rendelnie azokat a felhasználókat és csoportokat](../manage-apps/assign-user-or-group-access-portal.md) , amelyekhez hozzáférést szeretne biztosítani. 

### <a name="reviewers-for-an-application"></a>Felülvizsgálók egy alkalmazáshoz

A hozzáférési felülvizsgálatok lehetnek egy csoport tagjai vagy egy alkalmazáshoz hozzárendelt felhasználók számára. Az Azure ad-ben lévő alkalmazások nem feltétlenül rendelkeznek tulajdonossal, ezért nem lehetséges, hogy az alkalmazás tulajdonosát felülvizsgáló válassza ki. További hatókört tekinthet meg, hogy csak az alkalmazáshoz rendelt vendég felhasználókat vizsgálja felül, és ne tekintse át az összes hozzáférést.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Az Azure AD-és Azure-erőforrás-szerepkörök felülvizsgálatának megtervezése

A [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure ad-beli erőforrásokhoz való jogosultságú hozzáférést. Így az [Azure ad](../roles/permissions-reference.md) -ben és az [Azure-erőforrásokban](../../role-based-access-control/built-in-roles.md) egyaránt sokkal kisebb a Kiemelt szerepkörök listája, és növeli a címtár általános biztonságát.

A hozzáférési felülvizsgálatok lehetővé teszik a felülvizsgálók számára annak igazolását, hogy a felhasználóknak továbbra is szükségük van-e a szerepkörre. A hozzáférési csomagok hozzáférési felülvizsgálatához hasonlóan az Azure AD-szerepkörökkel és az Azure-erőforrásokkal kapcsolatos felülvizsgálatok is integrálva vannak a PIM rendszergazdai felhasználói felületéhez. Javasoljuk, hogy rendszeresen tekintse át a következő szerepkör-hozzárendeléseket:

* Globális rendszergazda

* Felhasználói rendszergazda

* Kiemelt jogosultságú hitelesítés rendszergazdája

* Feltételes hozzáférésű rendszergazda

* Biztonsági rendszergazda

* Az összes Microsoft 365-és Dynamics Service-felügyeleti szerepkör

Az itt kiválasztott szerepkörök állandó és jogosult szerepkört tartalmaznak. 

A felülvizsgálók szakaszban válasszon ki egy vagy több személyt az összes felhasználó áttekintéséhez. Azt is megteheti, hogy a tagok a saját hozzáférését vizsgálják felül.

 ![A szabályzat szerkesztése](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Hozzáférési felülvizsgálatok telepítése

Miután előkészített egy stratégiát és egy tervet az Azure AD-vel integrált erőforrások hozzáférésének áttekintéséhez, az alábbi erőforrásokkal végezheti el a felülvizsgálatok üzembe helyezését és kezelését.

### <a name="review-access-packages"></a>Hozzáférési csomagok áttekintése

Az elavult hozzáférés kockázatának csökkentése érdekében a rendszergazdák a hozzáférési csomag aktív hozzárendeléseivel rendelkező felhasználók rendszeres felülvizsgálatát is lehetővé teszik. Kövesse az alábbi hivatkozás utasításait:

| Útmutatók| Description |
| - | - |
| [Hozzáférési felülvizsgálatok létrehozása](entitlement-management-access-reviews-create.md)| Hozzáférési csomag felülvizsgálatának engedélyezése. |
| [Hozzáférési felülvizsgálatok végrehajtása](entitlement-management-access-reviews-review-access.md)| Hozzáférési felülvizsgálatok végrehajtása a hozzáférési csomaghoz rendelt más felhasználók számára. |
| [Hozzárendelt hozzáférési csomag (ok) önértékelése](entitlement-management-access-reviews-self-review.md)| Hozzárendelt hozzáférési csomag (ok) önálló felülvizsgálata |


> [!NOTE]
> Azok a végfelhasználók, akik önmagukban értékelik és azt mondják, hogy a továbbiakban nincs szükségük a hozzáférésre, nem törlődnek azonnal a hozzáférési csomagból. A rendszer eltávolítja azokat a hozzáférési csomagból, amikor a felülvizsgálat lejár, vagy ha a rendszergazda leállítja a felülvizsgálatot.

### <a name="review-groups-and-apps"></a>Csoportok és alkalmazások áttekintése

Az alkalmazottak és a vendégek számára az idő múlásával valószínűleg változnak a csoportok és az alkalmazások hozzáférése. Az elavult hozzáférési hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében a rendszergazdák hozzáférési felülvizsgálatokat hozhatnak létre a csoporttagok vagy az alkalmazások eléréséhez. Kövesse az alábbi hivatkozás utasításait:

| Útmutatók| Description |
| - | - |
| [Hozzáférési felülvizsgálatok létrehozása](create-access-review.md)| Hozzon létre egy vagy több hozzáférési felülvizsgálatot a csoporttagok vagy az alkalmazások eléréséhez. |
| [Hozzáférési felülvizsgálatok végrehajtása](perform-access-review.md)| Hozzáférési felülvizsgálat végrehajtása egy csoport vagy egy alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai számára. |
| [Saját hozzáférés ellenőrzése](review-your-access.md)| A tagok egy csoporthoz vagy alkalmazáshoz való saját hozzáférést tekinthetnek meg |
| [Teljes hozzáférési felülvizsgálat](complete-access-review.md)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |
| [Beavatkozás a helyszíni csoportok számára](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| PowerShell-parancsfájl a helyszíni csoportok hozzáférési felülvizsgálatához. |


### <a name="review-azure-ad-roles"></a>Azure AD-szerepkörök áttekintése

Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a Kiemelt Azure AD-szerepkörök elérését.

![Képernyőkép, amely az Azure A D-szerepkörök "tagság áttekintése" listáját jeleníti meg.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Kövesse az alábbi hivatkozásokon keresztül elérhető utasításokat:

| Útmutatók | Description |
| - | - |
 [Hozzáférési felülvizsgálatok létrehozása](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálatok létrehozása a Kiemelt Azure AD-szerepkörökhöz a PIM-ben |
| [Saját hozzáférés ellenőrzése](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ha rendszergazdai szerepkörhöz van rendelve, hagyja jóvá vagy tagadja meg a szerepkör elérését |
| [Hozzáférési felülvizsgálat végrehajtása](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |


### <a name="review-azure-resource-roles"></a>Azure-erőforrás szerepköreinek áttekintése

Az elavult szerepkör-hozzárendelések kockázatának csökkentése érdekében rendszeresen tekintse át az emelt szintű Azure-erőforrások szerepköreinek elérését. 

![Azure ad-szerepkörök áttekintése](./media/deploy-access-review/9-review-azure-roles-picker.png)

Kövesse az alábbi hivatkozásokon keresztül elérhető utasításokat:

| Útmutatók| Description |
| - | -|
| [Hozzáférési felülvizsgálatok létrehozása](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálatok létrehozása a Kiemelt Azure-beli erőforrás-szerepkörökhöz a PIM-ben |
| [Saját hozzáférés ellenőrzése](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ha rendszergazdai szerepkörhöz van rendelve, hagyja jóvá vagy tagadja meg a szerepkör elérését |
| [Hozzáférési felülvizsgálat végrehajtása](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |


## <a name="use-the-access-reviews-api"></a>A hozzáférési felülvizsgálatok API használata

Tekintse meg a [Graph API-metódusok](/graph/api/resources/accessreviews-root?view=graph-rest-beta) és a [szerepkör-és alkalmazás-engedély engedélyezési ellenőrzéseit](/graph/api/resources/accessreviews-root?view=graph-rest-beta) , és kezelje a felülvizsgálható erőforrásokat. A Microsoft Graph API hozzáférési felülvizsgálati módszerei az alkalmazás-és a felhasználói környezetekben egyaránt elérhetők. Ha parancsfájlokat futtat az alkalmazási környezetben, az API futtatásához használt fióknak (a szolgáltatási elv) meg kell adni az "AccessReview. Read. all" engedélyt a hozzáférési felülvizsgálatok adatainak lekérdezéséhez.

A népszerű hozzáférés-felülvizsgálati feladatok automatizálják az Graph API a hozzáférési felülvizsgálatok használatával:

* Hozzáférési felülvizsgálat létrehozása és elindítása

* Hozzáférési felülvizsgálat manuális befejezése az ütemezett befejezés előtt

* Az összes futó hozzáférési felülvizsgálat és azok állapotának listázása

* Tekintse meg a felülvizsgálati sorozat előzményeit, valamint az egyes felülvizsgálatokban végrehajtott döntéseket és műveleteket.

* Döntések begyűjtése hozzáférési felülvizsgálatból

* A befejezett felülvizsgálatokból származó döntések összegyűjtése, amelyekben a felülvizsgáló más döntést hozott, mint amit a rendszer javasolt.

Amikor új Graph API-lekérdezéseket hoz létre az automatizáláshoz, javasoljuk, hogy használja a [Graph Explorert](https://developer.microsoft.com/en-us/graph/graph-explorer). A Graph-lekérdezéseket felépítheti és feltárhatja, mielőtt parancsfájlokat és kódokat hozna létre. Ez segíthet gyorsan megismételni a lekérdezést, hogy pontosan a keresett eredményeket kapja meg, a parancsfájl kódjának módosítása nélkül.

## <a name="monitor-access-reviews"></a>Hozzáférési felülvizsgálatok figyelése

A hozzáférési felülvizsgálatok tevékenységeit a rendszer rögzíti, és elérhetővé válik az [Azure ad](../reports-monitoring/concept-audit-logs.md)naplófájljaiban. A naplózási adatok a kategória, a tevékenység típusa és a Dátumtartomány alapján szűrhetők. Íme egy példa lekérdezés:

| Kategória| Szabályzat |
| - | - |
| Tevékenység típusa| Hozzáférési felülvizsgálat létrehozása |
| | Hozzáférési felülvizsgálat frissítése |
| | A hozzáférési felülvizsgálat befejeződött |
| | Hozzáférési felülvizsgálat törlése |
| | Döntés jóváhagyása |
| | Döntés megtagadása |
| | Döntés alaphelyzetbe állítása |
| | Döntés alkalmazása |
| Dátumtartomány| hét nap |


A hozzáférési felülvizsgálatok részletesebb lekérdezése és elemzése, valamint a módosítások és a felülvizsgálatok befejezésének nyomon követése érdekében javasoljuk, hogy az Azure AD-naplókat az [azure log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) vagy az Azure Event hub szolgáltatásba exportálja. Az Azure Log Analytics-ban való tárolás során használhatja a [hatékony elemzési nyelvet](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) , és saját irányítópultokat építhet ki.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az alábbi kapcsolódó technológiákat.

* [Mi az Azure AD-jogosultságok kezelése?](entitlement-management-overview.md)

* [Mi az az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)