---
title: Hozzáférési felülvizsgálatok Azure Active Directory megtervezése
description: Tervezési útmutató a hozzáférési felülvizsgálatok sikeres üzembe helyezéséhez
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
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532588"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>A Azure Active Directory áttekintések üzembe helyezésének megtervezése

[Azure Active Directory (Azure AD) hozzáférési](access-reviews-overview.md) felülvizsgálatok segítségével a szervezet az erőforrás-hozzáférési életciklus kezelésével biztosíthatja a hálózat [biztonságát.](identity-governance-overview.md) A hozzáférési felülvizsgálatokkal a következőre van képes:

* Rendszeres felülvizsgálatok ütemezése vagy alkalmi felülvizsgálatok végrehajtása annak ellenőrzéséhez, hogy ki férhet hozzá bizonyos erőforrásokhoz, például alkalmazásokhoz és csoportokhoz

* Elemzések, megfelelőség vagy szabályzati okok felülvizsgálatának nyomon követése

* Felülvizsgálatokat delegálhat adott rendszergazdáknak, üzleti tulajdonosoknak vagy végfelhasználóknak, akik igazolhatják, hogy folyamatos hozzáférésre van szükség

* Az elemzések segítségével hatékonyan meghatározhatja, hogy a felhasználóknak továbbra is hozzáférésre van-e szükség

* Az eredmények áttekintésének automatizálása, például a felhasználók erőforrásokhoz való hozzáférésének eltávolítása

  ![A hozzáférési felülvizsgálatok folyamatát bemutató diagram.](./media/deploy-access-review/1-planning-review.png)

A hozzáférési felülvizsgálatok [Azure AD Identity Governance](identity-governance-overview.md) képesség. A többi képesség a [jogosultságkezelés,](entitlement-management-overview.md) [a Privileged Identity Management](../privileged-identity-management/pim-configure.md) és a [használati feltételek.](../conditional-access/terms-of-use.md) Együttesen segítenek a szervezeteknek megválaszolni ezt a négy kérdést:

* Mely felhasználóknak kell hozzáférniük az erőforrásokhoz?

* Mit csinálnak ezek a felhasználók ezzel a hozzáféréssel?

* Van hatékony szervezeti vezérlés a hozzáférés-kezeléshez?

* Ellenőrizhetik az auditorok, hogy működnek-e a vezérlők?

A hozzáférési felülvizsgálatok üzembe helyezésének megtervezése elengedhetetlen ahhoz, hogy el tudja érni a kívánt cégirányítási stratégiát a szervezet felhasználói számára.

### <a name="key-benefits"></a>Főbb előnyök

A hozzáférési felülvizsgálatok engedélyezésének fő előnyei:

* **Az együttműködés szabályozása.** A hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára a felhasználók által szükséges összes erőforráshoz való hozzáférés kezelését. Amikor a felhasználók osztoznak és együttműködnek, a szervezetek biztosak lehetnek abban, hogy az információk csak a jogosult felhasználók között oszlnak meg.

* **Kockázatkezelés.** A hozzáférési felülvizsgálatok lehetőséget biztosítanak a szervezetek számára az adatokhoz és alkalmazásokhoz való hozzáférés áttekintésére, ami csökkenti az adatszivárgás és az adatszivárgás kockázatát. Ide tartoznak a külső partnerek vállalati erőforrásokhoz való hozzáférésének rendszeres áttekintésére rendelkezésre álló képességek. 

* **A megfelelőség és az irányítás kezelése.** A hozzáférési felülvizsgálatokkal csoportok, alkalmazások és webhelyek hozzáférési életciklusát irányíthatja és újrakattathatja. Szabályozhatja a megfelelőség vagy a szervezetre jellemző, kockázatérzékeny alkalmazások nyomon követését. 

* **Költségcsökkentés.** A hozzáférési felülvizsgálatok a felhőben vannak felépítve, és natív módon működnek a felhőbeli erőforrásokkal, például csoportokkal, alkalmazásokkal és hozzáférési csomagokkal. A hozzáférési felülvizsgálatok használata kevesebb költséggel jár, mint saját eszközök kiépítése vagy a helyszíni eszközkészlet más módon történő frissítése.

### <a name="training-resources"></a>Képzési források

Az alábbi videók hasznosak lehetnek, ha a hozzáférési felülvizsgálatokkal ismerkedik meg:

* [Mik azok a hozzáférési felülvizsgálatok az Azure AD-ban?](https://youtu.be/kDRjQQ22Wkk)

* [Hozzáférési felülvizsgálatok létrehozása az Azure AD-ban](https://youtu.be/6KB3TZ8Wi40)

* [Hozzáférési felülvizsgálatok engedélyezése az Azure AD-ban](https://youtu.be/X1SL2uubx9M)

* [Hozzáférés áttekintése a Saját hozzáférés](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licencek

Érvényes hozzáférési prémium szintű Azure AD (P2) licencre van szüksége minden olyan személyhez, kivéve a globális rendszergazdákat vagy a felhasználói rendszergazdákat, akik hozzáférési felülvizsgálatokat hoznak létre vagy hajt végre. További információ: [Hozzáférési felülvizsgálatok licenckövetelményei.](access-reviews-overview.md)

Az identitáskezelés egyéb funkcióira is szüksége lehet, például a Jogosultságok életciklusának kezelése [vagy](entitlement-management-overview.md) a Privileged Identity Managements. Ebben az esetben kapcsolódó licencekre is szüksége lehet. További információ: díjszabás [Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="plan-the-access-reviews-deployment-project"></a>A Hozzáférési felülvizsgálatok üzembe helyezési projekt megtervezése

Vegye figyelembe, hogy a szervezetének meg kell határoznia a hozzáférési felülvizsgálatok üzembe helyezésének stratégiáját a környezetében.

### <a name="engage-the-right-stakeholders"></a>A megfelelő érdekelt felek bevonására

Ha a technológiai projektek meghiúsulnak, ezt általában a hatásra, az eredményekre és a felelősségi körökre vonatkozó, nem egyező elvárások miatt érik el. A buktatók elkerülése érdekében győződjön meg [arról,](../fundamentals/active-directory-deployment-plans.md) hogy a megfelelő érdekelt feleket is be fogja vonni, és hogy a projektszerepk szerepkörök egyértelműek.

A hozzáférési felülvizsgálatok során valószínűleg a szervezet alábbi csapatának képviselőit fogja tartalmazni:

* **Az it-felügyelet** felügyeli az it-infrastruktúrát, és felügyeli a felhővel kapcsolatos befektetéseket és saaS-alkalmazásokat. Ez a csapat a következőt fogja:

   * Tekintse át az infrastruktúrához és alkalmazásokhoz való emelt szintű hozzáférést, beleértve a Microsoft 365 Azure AD-t is.

   * Hozzáférési felülvizsgálatok ütemezése és futtatása olyan csoportokon, amelyek a kivételek listái vagy az it-próbaprojektek karbantartására használhatók a naprakész hozzáférési listák fenntartása érdekében.

   * Győződjön meg arról, hogy az erőforrások szolgáltatásnévn keresztüli programozott (szkriptekkel futtatott) hozzáférése szabályozott és felülvizsgálva van.

* **A fejlesztői csapatok** alkalmazásokat építenek és tartnak karban a szervezet számára. Ez a csapat a következőt fogja:

   * Szabályozhatja, hogy ki férhet hozzá és kezelhet összetevőket a kifejlesztett megoldásokból álló SaaS-, PaaS- és IaaS-erőforrásokban.

   * Olyan csoportokat kezelhet, amelyek hozzáférhetnek a belső alkalmazásfejlesztéshez szükséges alkalmazásokhoz és eszközökhöz.

   * Az ügyfelek számára üzemeltetett éles szoftverekhez vagy megoldásokhoz hozzáféréssel rendelkező emelt szintű identitások megkövetelása

* **Az üzleti egységek projekteket** és saját alkalmazásokat kezelnek. Ez a csapat a következőt fogja: 

   * Tekintse át, hagyja jóvá vagy tiltsa le a csoportokhoz és alkalmazásokhoz való hozzáférést a belső és külső felhasználók számára.

   * Felülvizsgálatok ütemezése és végrehajtása az alkalmazottak és külső identitások, például üzleti partnerek folyamatos hozzáférésének ellenőrzésére.

* **A vállalati szabályozás** biztosítja, hogy a szervezet betartsa a belső szabályzatot, és megfeleljen a szabályozásnak. Ez a csapat a következőt fogja:

   * Új hozzáférési felülvizsgálatok kérése vagy ütemezése.

   * A hozzáférés áttekintésének folyamatai és eljárásai, beleértve a dokumentációt és a megfelelőség nyilvántartását.

   * Tekintse át a kritikus fontosságú erőforrásokra vonatkozó korábbi felülvizsgálatok eredményeit.

> [!NOTE]
> A manuális értékelést igénylő felülvizsgálatok esetén mindenképpen tervezze meg a megfelelő felülvizsgálókat és felülvizsgálati ciklusokat, amelyek megfelelnek a szabályzat- és megfelelőségi igényeknek. Ha a felülvizsgálati ciklusok túl gyakoriak, vagy túl kevés felülvizsgáló van, a minőség elveszhet, és túl sok vagy túl kevés felhasználó férhet hozzá. 

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció kritikus fontosságú az új üzleti folyamatok sikerességéhez. Proaktív módon közölheti a felhasználókkal, hogy hogyan és mikor változnak a felhasználói élményük, és hogyan nyerhetnek támogatást, ha problémákat tapasztalnak. 

#### <a name="communicate-changes-in-accountability"></a>Az elszámoltathatóság változásainak kommunikálás

A hozzáférési felülvizsgálatok támogatják az üzleti tulajdonosok folyamatos hozzáférésének áttekintésére és a folyamatos hozzáférésre való cselekvésre való felelősség áttűnését. A hozzáférési döntéseknek az IT-ről való leválasztása pontosabb döntéseket hoz a hozzáféréssel kapcsolatos döntésekhez. Ez kulturális változás az erőforrás-tulajdonos elszámoltathatóságában és felelősségében. Proaktívan kommunikálja ezt a változást, és győződjön meg arról, hogy az erőforrás-tulajdonosok betanítása és a betekintő adatok alapján jó döntéseket hozhat.

Egyértelmű, hogy az it-rendszergazda az infrastruktúrával kapcsolatos hozzáférési döntések és a kiemelt szerepkör-hozzárendelések teljes felügyeletében szeretne maradni. 

#### <a name="customize-email-communication"></a>E-mail-kommunikáció testreszabása

A felülvizsgálat ütemezésekor felhasználókat kell kijelölni, akik végrehajtják ezt a felülvizsgálatot. Ezek a felülvizsgálók ezután e-mailes értesítést kapnak a hozzájuk rendelt új értékelésekről, valamint emlékeztetőket a hozzájuk rendelt felülvizsgálat lejárta előtt.

A rendszergazdák félúton vagy a felülvizsgálat lejárta előtt vagy egy nappal a lejárata előtt küldhetik el ezt az értesítést. 

A felülvizsgálóknak küldött e-mail testre szabható egy egyéni rövid üzenettel, amely arra bátorítja őket, hogy járják el a felülvizsgálatot. Javasoljuk, hogy a további szöveget a következőre használja:

* Személyes üzenetet is küld a felülvizsgálóknak, hogy megértsék, az Ön megfelelőségi vagy it-részlege küldi el.

* Hivatkozás vagy hivatkozás beszúrása a felülvizsgálat elvárásainak és további referencia- vagy képzési anyagoknak a belső információira.

* Tartalmaz egy hivatkozást, amely a hozzáférés önértékelésének elvégzésére [vonatkozó utasításokra mutató hivatkozást tartalmaz.](review-your-access.md) 

  ![Felülvizsgáló e-mail](./media/deploy-access-review/2-plan-reviewer-email.png)

Az Áttekintés kezdete lehetőség kiválasztásakor a felülvizsgálók a [myAccess](https://myapplications.microsoft.com/) portálra lesznek irányítva csoport- és alkalmazás-hozzáférési felülvizsgálatokért. A portál áttekintést nyújt az összes olyan felhasználóról, aki hozzáféréssel rendelkezik az áttekintő erőforráshoz, valamint rendszerjavaslatokat tesz az utolsó bejelentkezés és a hozzáférési információk alapján.

### <a name="plan-a-pilot"></a>Próbaterv

Javasoljuk ügyfeleinknek, hogy először próba-hozzáférési felülvizsgálatokat egy kis csoporttal, és a nem kritikus fontosságú erőforrásokat célozzanak meg. A próbafolyamatok segítségével szükség szerint módosíthatja a folyamatokat és a kommunikációt, és növelheti a felhasználók és felülvizsgálók biztonsági és megfelelőségi követelményeinek való megfelelését.

A próbaúton a következőket javasoljuk:

* Kezdje olyan felülvizsgálatokkal, ahol a rendszer nem alkalmazza automatikusan az eredményeket, és szabályozhatja azok következményeit.

* Győződjön meg arról, hogy minden felhasználónak érvényes e-mail-címe van az Azure AD-ben, és e-mail-értesítést kap a megfelelő művelet érdekében. 

* Dokumentálja a próba részeként eltávolított hozzáféréseket arra az esetre, ha gyorsan vissza kellene állítania.

* Az auditnaplók figyelése annak biztosításához, hogy minden esemény naplózása megfelelő legyen.

További információkért tekintse meg [a teszthez ajánlott eljárásokat.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Bevezetés a hozzáférési felülvizsgálatokbe

Ez a szakasz bemutatja a hozzáférés-felülvizsgálati fogalmakat, amelyekről a felülvizsgálatok megtervezése előtt tudnia kell.

### <a name="what-resource-types-can-be-reviewed"></a>Milyen erőforrástípusok felülvizsgálata létezik?

Miután integrálta a szervezet erőforrásait az Azure AD-val (például felhasználókkal, alkalmazásokkal és csoportokkal), azok kezelhetők és áttekinthetőek. 

A felülvizsgálatok tipikus céljai a következők:

* [Az Egyszeri bejelentkezéshez az Azure AD-val](../manage-apps/what-is-application-management.md) integrált alkalmazások (például SaaS, üzletági).

* [Csoporttagság](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (szinkronizálva az Azure AD-be, vagy az Azure AD-ban vagy Microsoft 365, beleértve a Microsoft Teamst).

* [Hozzáférési csomag,](./entitlement-management-overview.md) amely egyetlen csomagba csoportosítja az erőforrásokat (csoportokat, alkalmazásokat és helyeket) a hozzáférés kezeléséhez.

* [Az Azure AD-szerepkörök és az Azure-erőforrások szerepkörei](../privileged-identity-management/pim-resource-roles-assign-roles.md) a Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Ki hozza létre és kezeli a hozzáférési felülvizsgálatokat?

A hozzáférési felülvizsgálatok létrehozásához, kezeléséhez vagy olvasásához szükséges rendszergazdai szerepkör az áttekintett erőforrás típusától függ. Az alábbi táblázat az egyes erőforrástípushoz szükséges szerepköröket jelöli:

| Erőforrás típusa| Hozzáférési felülvizsgálatok létrehozása és kezelése (létrehozók)| Hozzáférési felülvizsgálat eredményeinek olvasása |
| - | - | -|
| Csoport vagy alkalmazás| Globális rendszergazda <p>Felhasználói rendszergazda| Létrehozók és biztonsági rendszergazda |
| Kiemelt szerepkörök az Azure AD-ban| Globális rendszergazda <p>Kiemelt szerepkör rendszergazdája| Alkotók <p>Biztonsági olvasó<p>Biztonsági rendszergazda |
| Kiemelt szerepkörök az Azure-ban (erőforrások)| Globális rendszergazda<p>Felhasználói rendszergazda<p>Erőforrás tulajdonosa| Alkotók |
| Hozzáférési csomag| Globális rendszergazda<p>Hozzáférési csomag létrehozója| Csak globális rendszergazda |


További információ: Rendszergazdai [szerepkör engedélyei](../roles/permissions-reference.md)a Azure Active Directory.

### <a name="who-will-review-the-access-to-the-resource"></a>Ki fogja felülvizsgálni az erőforráshoz való hozzáférést?

A hozzáférési felülvizsgálat létrehozója a létrehozáskor dönti el, hogy ki fogja elvégezni a felülvizsgálatot. Ez a beállítás a felülvizsgálat elkezdődött után nem módosítható. A felülvizsgálókat három személy képviseli:

* Erőforrás-tulajdonosok, akik az erőforrás üzleti tulajdonosai.

* A Hozzáférési felülvizsgálatok rendszergazdája által kiválasztott, egyenként kiválasztott delegáltak halmaza.

* Azok a végfelhasználók, akik mind igazolják, hogy folyamatos hozzáférésre van szükségük.

Hozzáférési felülvizsgálat létrehozásakor a rendszergazdák egy vagy több felülvizsgáló közül választhatnak. Az összes felülvizsgáló elkezdhet és végrehajthat egy felülvizsgálatot, és kiválaszthat felhasználókat az erőforrásokhoz való folyamatos hozzáféréshez vagy azok eltávolításához. 

### <a name="components-of-an-access-review"></a>Hozzáférési felülvizsgálat összetevői

A hozzáférési felülvizsgálatok megvalósítása előtt meg kell tervezni a szervezet számára releváns felülvizsgálatok típusait. Ennek érdekében üzleti döntéseket kell hoznia arról, hogy mit szeretne felülvizsgálni, és milyen műveleteket kell meghoznia az értékelések alapján.

Hozzáférési felülvizsgálati szabályzat létrehozásához az alábbi információkra van szükség.

* Milyen erőforrásokat kell áttekintenünk?

* Akinek a hozzáférése felülvizsgálat alatt áll.

* Milyen gyakran kell lekövetkeztetni a felülvizsgálatot?

* Ki fogja elvégezni a felülvizsgálatot?

   * Hogyan kapják meg az értesítést a felülvizsgálatról?

   * Milyen ütemterveket kell kikényszeríteni a felülvizsgálathoz?

* Milyen automatikus műveleteket kell kikényszeríteni a felülvizsgálat alapján?

   * Mi történik, ha a felülvizsgáló nem válaszol időben?

* Milyen manuális műveletek lesznek ennek eredményeképpen a felülvizsgálat alapján?

* Milyen kommunikációt kell elküldeni a műveletek alapján?


**Példa hozzáférési felülvizsgálati tervre**

| Összetevő| Érték |
| - | - |
| **Áttekintő források**| Hozzáférés a Microsoft Dynamics szolgáltatáshoz |
| **Áttekintés gyakorisága**| Havonta |
| **Ki hajt végre felülvizsgálatot?**| Dynamics business group Program Managers |
| **Értesítés**| E-mailben 24 órával az aliasnevek Dynamics-Pms<p>Egyéni üzenet küldése a felülvizsgálóknak a részvétel biztonságossá |
| **Idővonal**| 48 óra az értesítéstől |
|**Automatikus műveletek**| Távolítsa el a hozzáférést minden olyan fiókból, amely 90 napon belül nem rendelkezik interaktív bejelentkezéssel, ha eltávolítja a felhasználót a biztonsági csoport dynamics-access szolgáltatásból. <p>*Műveletek végrehajtása, ha nem felülvizsgálatát az idővonalon belül végzi el.* |
| **Manuális műveletek**| A felülvizsgálók szükség esetén elvégezhetnek eltávolítási jóváhagyást az automatizált művelet előtt. |
| **Tájékoztatások**| Küldjön belső (tag) felhasználókat, akik el vannak távolítva egy e-mailben, amely elmagyarázza, hogyan távolíthatók el, és hogyan lehet ismét hozzáférést szerezni. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Műveletek automatizálása hozzáférési felülvizsgálatok alapján

A hozzáférés-eltávolítást automatizálhatja az Eredmények automatikus alkalmazása az erőforrásra beállítás Engedélyezésével.

  ![Hozzáférési felülvizsgálatok tervezése](./media/deploy-access-review/3-automate-actions-settings.png)

A felülvizsgálat befejezése és befejezése után a felülvizsgáló által nem jóváhagyott felhasználókat a rendszer automatikusan eltávolítja az erőforrásból, vagy folyamatos hozzáférést biztosít. Ez jelentheti a csoporttagságuk eltávolítását, az alkalmazás-hozzárendelésüket, vagy a jogosultságuk megemelése emelt szintű szerepkörre.

Javaslatok meghozás

A javaslatok a felülvizsgálók számára a felülvizsgáló élmény részeként jelennek meg, és jelzik, hogy valaki utoljára jelentkezik be a bérlőbe, vagy hogy utoljára fért hozzá egy alkalmazáshoz. Ezek az információk segítenek a felülvizsgálóknak a megfelelő hozzáférési döntések meghozásában. A Javaslatok ajánlásokat választva a hozzáférési felülvizsgálat javaslatait fogja követni. A hozzáférési felülvizsgálat végén a rendszer automatikusan alkalmazza ezeket a javaslatokat azokra a felhasználókra, akikre a felülvizsgálók nem válaszoltak.

A javaslatok a hozzáférési felülvizsgálatban megadott feltételeken alapulnak. Ha például úgy konfigurálja a felülvizsgálatot, hogy 90 napig ne legyen hozzáférése interaktív bejelentkezés nélkül, a rendszer azt javasolja, hogy a feltételeknek megfelelő összes felhasználót távolítsa el. A Microsoft folyamatosan dolgozik a javaslatok továbbfejlesztésen. 

### <a name="review-guest-user-access"></a>Vendégfelhasználói hozzáférés áttekintése

A Hozzáférési felülvizsgálatok használatával áttekinti és megtisztítja az együttműködési partnerek identitását a külső szervezetektől. A partnerenkénti felülvizsgálatok konfigurálása megfelelhet a megfelelőségi követelményeknek.

A külső identitások az alábbi műveletek egyikével kaphatnak hozzáférést a vállalati erőforrásokhoz:

* Hozzáadva egy csoporthoz 

* Meghívva a Teamsbe 

* Vállalati alkalmazáshoz vagy hozzáférési csomaghoz rendelve

* Kiemelt szerepkör hozzárendelése az Azure AD-ban vagy egy Azure-előfizetésben

Lásd: [példaszk szkript.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) A szkript megmutatja, hol vannak használva a bérlőbe meghívott külső identitások. A külső felhasználók csoporttagságát, szerepkör-hozzárendelését és alkalmazás-hozzárendelését az Azure AD-ban láthatja. A szkript nem fog az Azure AD-n kívüli hozzárendeléseket látni, például a SharePoint-erőforrások közvetlen jogosultság-hozzárendelését csoportok használata nélkül.

Amikor hozzáférési felülvizsgálatot hoz létre csoportokhoz vagy alkalmazásokhoz, dönthet úgy, hogy a felülvizsgáló a Mindenki hozzáféréssel vagy a Csak vendégfelhasználókra összpontosít. A Csak vendégfelhasználók lehetőség kiválasztásával a felülvizsgálók az Azure AD B2B azon külső identitásának célzott listáját kapják meg, amelyek hozzáféréssel rendelkezik az erőforráshoz.

 ![Vendégfelhasználók áttekintése](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Ez NEM fog olyan külső tagokat tartalmazni, akik a userType taggal vannak megszabadva. Ez nem tartalmazza az Azure AD B2B-együttműködésen kívül meghívott felhasználókat, például azokat, akik közvetlenül a SharePointon keresztül férnek hozzá a megosztott tartalmakhoz.

## <a name="plan-access-reviews-for-access-packages"></a>Hozzáférési csomagok hozzáférési felülvizsgálatának megtervezése

[A hozzáférési csomagok](entitlement-management-overview.md) jelentősen leegyszerűsítik a szabályozási és hozzáférési felülvizsgálati stratégiát. A hozzáférési csomag az összes olyan erőforrás csomagja, amely tartalmazza azt a hozzáférést, amely a felhasználónak a projekten való munkához vagy a feladat elvégzéséhez szükséges. Létrehozhat például egy hozzáférési csomagot, amely tartalmazza az összes olyan alkalmazást, amelyre a szervezet fejlesztőinek szükségük van, vagy az összes olyan alkalmazást, amelyhez külső felhasználóknak hozzáférésre van szükségük. A rendszergazda vagy a delegált hozzáférésicsomag-kezelő ezután csoportokba csomagolja az erőforrásokat (csoportokat vagy alkalmazásokat), valamint azokat a szerepköröket, amelyekre a felhasználóknak szükségük van az erőforrásokhoz.

Hozzáférési [csomag létrehozásakor](entitlement-management-access-package-create.md)létrehozhat egy vagy több hozzáférési szabályzatot, amelyek olyan feltételeket állíthatnak be, amelyekhez a felhasználók hozzáférési csomagot kérhetnek, milyen a jóváhagyási folyamat, és milyen gyakran kell egy személynek újra kérelmezni a hozzáférést. A hozzáférési felülvizsgálatok a hozzáférési csomag szabályzatának létrehozásakor vagy szerkesztésekor vannak konfigurálva.

Nyissa meg az Életciklus lapot, és görgessen le a Hozzáférési felülvizsgálatok lapra.

 ![Az "Életciklus" lapon található "Szabályzat szerkesztése" képernyőképe.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Hozzáférési felülvizsgálatok megtervezése csoportokhoz

A hozzáférési csomagok mellett a hozzáférés szabályozásának leghatékonyabb módja a csoporttagság felülvizsgálata. Javasoljuk, hogy az erőforrásokhoz való hozzáférést biztonsági csoportokon vagy Microsoft 365 keresztül rendeli hozzá, és hogy a hozzáférést a rendszer hozzáadja ezekhez a csoportokhoz. [](../fundamentals/active-directory-manage-groups.md)

Egyetlen csoport minden megfelelő erőforráshoz hozzáférést kaphat. A csoport hozzáférését hozzárendelheti egyes erőforrásokhoz, vagy egy hozzáférési csomaghoz, amely alkalmazásokat és más erőforrásokat csoportosít. Ezzel a módszerrel áttekintheti a csoporthoz való hozzáférést ahelyett, hogy az egyes alkalmazásokhoz hozzáférést ad egy személynek. 

A csoporttagságot a következővel lehet felülvizsgálni: 

* Rendszergazdák

* Csoporttulajdonosok

* Kiválasztott felhasználók, delegált felülvizsgálati képesség a felülvizsgálat létrehozásakor

* A csoport tagjai, akik igazolják saját magukat

### <a name="group-ownership"></a>Csoport tulajdonjoga

Javasoljuk, hogy a csoporttulajdonosok felülvizsgálják a tagságot, mivel a legjobb, ha tudják, kinek van szüksége hozzáférésre. A csoportok tulajdonjoga a csoport típusától függ:

A Microsoft 365 és az Azure AD-ban létrehozott csoportok egy vagy több jól meghatározott tulajdonosokkal is vannak. A legtöbb esetben ezek a tulajdonosok tökéletes felülvizsgálókat hoznak létre a saját csoportjaik számára, mivel tudják, kinek kell hozzáférniük. 

A Microsoft Teams például Microsoft 365-csoportok alapul szolgáló engedélyezési modellel biztosít hozzáférést a felhasználóknak a SharePointban, az Exchange-ban, a OneNote-ban vagy más Microsoft 365 erőforrásokhoz. A csapat létrehozója automatikusan tulajdonossá válik, és ő lesz a felelős a csoport tagságának tanúsításáért. 

Előfordulhat, hogy az Azure AD-portálon manuálisan létrehozott csoportok vagy a felhasználókon keresztüli szkriptek Microsoft Graph nem feltétlenül vannak meghatározott tulajdonosok. Javasoljuk, hogy ezeket az Azure AD-portálon, a csoport "Tulajdonosok" szakaszában vagy a Graph-on keresztül határozza meg.

A csoportból szinkronizált helyi Active Directory nem lehet tulajdonos az Azure AD-ban. Amikor hozzáférési felülvizsgálatot hoz létre számukra, ki kell választania azokat a személyeket, akik a legmegfelelőbbek a tagságuk kiválasztására.

> [!NOTE]
> Javasoljuk, hogy határozzon meg olyan üzleti szabályzatokat, amelyek meghatározzák a csoportok létrehozási mikéntét, hogy a csoport tulajdonjoga és elszámoltathatóságát a tagság rendszeres ellenőrzése érdekében biztosítsa. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Kizárási csoportok tagságának áttekintése a feltételes hozzáférési szabályzatok között 

Vannak olyan idők, amikor a hálózat biztonságának biztosítása érdekében kialakított feltételes hozzáférési szabályzatok nem vonatkoznak minden felhasználóra. Előfordulhat például, hogy egy feltételes hozzáférési szabályzat, amely csak a vállalati hálózaton való bejelentkezést teszi lehetővé, nem vonatkozik az értékesítési csapatra, amely sokat utazik. Ebben az esetben az értékesítési csapat tagjai egy csoportba kerülnek, és ez a csoport ki lesz zárva a feltételes hozzáférési szabályzatból. 

Rendszeresen tekintsen át egy ilyen csoporttagságot, mivel a kizárás potenciális kockázatot jelent, ha nem megfelelő tagok vannak kizárva a követelményből.

Az [Azure AD hozzáférési felülvizsgálatok segítségével](conditional-access-exclusion.md)kezelheti a feltételes hozzáférési szabályzatok hatálya alól kizárt felhasználókat.

### <a name="review-external-users-group-memberships"></a>Külső felhasználó csoporttagságának áttekintése

A manuális munka és a kapcsolódó [](../enterprise-users/groups-create-rule.md) hibák minimalizálása érdekében fontolja meg dinamikus csoportok használatával a csoporttagság hozzárendelését a felhasználó attribútumai alapján. Előfordulhat, hogy egy vagy több dinamikus csoportot szeretne létrehozni külső felhasználók számára. A belső szponzor felülvizsgálóként is viselkedhet a csoporttagságnál. 

Megjegyzés: A csoportból hozzáférési felülvizsgálat miatt eltávolított külső felhasználók nem törlődnek a bérlőből. 

Törölhetők egy bérlőből manuálisan vagy egy szkript használatával.

### <a name="review-access-to-on-premises-groups"></a>A helyszíni csoportokhoz való hozzáférés áttekintése

A hozzáférési felülvizsgálatok nem módosíthatják a helyszínről szinkronizált csoportok csoporttagságát a [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md) Ennek az az oka, hogy a hitelesítés forrása a helyszíni.

Továbbra is használhatja a hozzáférési felülvizsgálatokat a helyszíni csoportok rendszeres felülvizsgálatának ütemezése és fenntartása érdekében. A felülvizsgálók ezután a helyszíni csoportban is el fognak indulni. Ez a stratégia minden felülvizsgálat eszközeként megőrzi a hozzáférési felülvizsgálatokat.

A hozzáférési felülvizsgálatok eredményeit a helyszíni csoportokban használhatja, és a következővel tovább feldolgozhatja őket:

* Töltse le a CSV-jelentést a Hozzáférési felülvizsgálatból, és manuálisan kell elvégeznie a műveletet.

* A Microsoft Graph a befejezett hozzáférési felülvizsgálatok eredményeinek és döntéseinek programozott elérésére.

Egy Windows AD által felügyelt csoport eredményeinek eléréséhez például használja ezt a [PowerShell-példaszkent.](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises) A szkript felvázolja a szükséges Graph-hívásokat, és exportálja a Windows AD-PowerShell parancsokat a módosítások elvégzéséhez.

## <a name="plan-access-reviews-for-applications"></a>Hozzáférési felülvizsgálatok megtervezése alkalmazásokhoz 

Egy alkalmazáshoz való hozzáférés áttekintésekor áttekinti az alkalmazottak és a külső identitások hozzáférését az alkalmazáson belüli információkhoz és adatokhoz. Ha egy hozzáférési csomag vagy csoport helyett egy adott alkalmazáshoz való hozzáférésre van szüksége, válassza az alkalmazás áttekintését. 

Javasoljuk, hogy a következő esetekben tervezze meg az alkalmazások áttekintését:

* A felhasználók közvetlen hozzáférést kapnak az alkalmazáshoz (csoporton vagy hozzáférési csomagon kívül).

* Az alkalmazás kritikus vagy bizalmas információkat is elérhetővé teszi.

* Az alkalmazás olyan megfelelőségi követelményekkel rendelkezik, amelyeket igazolnia kell.

* Azt gyanítja, hogy nem megfelelő hozzáféréssel rendelkezik.

Ha hozzáférési felülvizsgálatokat hoz létre egy alkalmazáshoz, állítsa be a Felhasználó-hozzárendelés szükséges beállítását? a tulajdonságot igenre. Ha a Nem beállításra van állítva, a címtárban található összes felhasználó, beleértve a külső identitásokat is, hozzáférhet az alkalmazáshoz, és nem tudja felülvizsgálni az alkalmazáshoz való hozzáférést. 

 ![alkalmazás-hozzárendelések megtervezése](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Ezután hozzá kell [rendelnie a hozzáférést](../manage-apps/assign-user-or-group-access-portal.md) kívánó felhasználókat és csoportokat. 

### <a name="reviewers-for-an-application"></a>Alkalmazás felülvizsgálói

A hozzáférési felülvizsgálatok a csoport tagjaira vagy az alkalmazásokhoz rendelt felhasználókra is kihatnak. Az Azure AD-beli alkalmazásoknak nem feltétlenül van tulajdonosi engedélyük, ezért nem lehetséges felülvizsgálóként kiválasztani az alkalmazás tulajdonosát. Az áttekintés további hatókörrel is megszabadható, hogy csak az alkalmazáshoz rendelt vendégfelhasználókat tekintse át az összes hozzáférés áttekintése helyett.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Az Azure AD és az Azure-erőforrások szerepkörei áttekintésének megtervezése

[Privileged Identity Management (PIM) egyszerűbbé](../privileged-identity-management/pim-configure.md) teszi a vállalatok számára az Azure AD-erőforrásokhoz való emelt szintű hozzáférés kezelését. Így az [Azure AD-ban](../roles/permissions-reference.md) és az [Azure-erőforrásokban](../../role-based-access-control/built-in-roles.md) található kiemelt szerepkörök listája sokkal kisebb lesz, és növeli a címtár általános biztonságát.

A hozzáférési felülvizsgálatok lehetővé teszik a felülvizsgálók számára, hogy igazolják, hogy a felhasználóknak továbbra is szerepkörhöz kell-e szükségük. A hozzáférési csomagok hozzáférési felülvizsgálatjaihoz hasonló módon az Azure AD-szerepkörök és az Azure-erőforrások áttekintései is integrálva vannak a PIM rendszergazdai felhasználói felületbe. Javasoljuk, hogy rendszeresen tekintse át a következő szerepkör-hozzárendeléseket:

* Globális rendszergazda

* Felhasználói rendszergazda

* Privileged Authentication Administrator

* Feltételes hozzáférés rendszergazdája

* Biztonsági rendszergazda

* Minden Microsoft 365 dynamics service administration szerepkör

Az itt kiválasztott szerepkörök közé tartoznak a állandó és a jogosult szerepkörök. 

A Felülvizsgálók szakaszban válasszon ki egy vagy több felhasználót az összes felhasználó áttekintésére. Azt is választhatja, hogy a tagok felülvizsgálják a saját hozzáférésüket.

 ![A szabályzat szerkesztése](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Hozzáférési felülvizsgálatok üzembe helyezése

Miután előkészített egy stratégiát és egy tervet az Azure AD-val integrált erőforrásokhoz való hozzáférés áttekintéséhez, az alábbi erőforrások használatával üzembe helyezheti és kezelheti az értékeléseket.

### <a name="review-access-packages"></a>Hozzáférési csomagok áttekintése

Az elavult hozzáférés kockázatának csökkentése érdekében a rendszergazdák rendszeres időközönként felülvizsgálhatják a hozzáférési csomaghoz aktív hozzárendeléssel lévő felhasználókat. Kövesse az alábbi hivatkozás utasításait:

| Útmutatók| Leírás |
| - | - |
| [Hozzáférési felülvizsgálatok létrehozása](entitlement-management-access-reviews-create.md)| Hozzáférési csomag felülvizsgálatának engedélyezése. |
| [Hozzáférési felülvizsgálatok végrehajtása](entitlement-management-access-reviews-review-access.md)| Hozzáférési felülvizsgálatok végrehajtása hozzáférési csomaghoz hozzárendelt más felhasználók számára. |
| [Saját maga által hozzárendelt hozzáférési csomag(ak)](entitlement-management-access-reviews-self-review.md)| Hozzárendelt hozzáférési csomag(k) önértékelése |


> [!NOTE]
> A rendszer nem távolítja el azonnal az olyan végfelhasználókat a hozzáférési csomagból, akik önértékelést követően azt mondani, hogy már nincs szükségük hozzáférésre. Ha a felülvizsgálat véget ér, vagy ha egy rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja őket a hozzáférési csomagból.

### <a name="review-groups-and-apps"></a>Csoportok és alkalmazások áttekintése

A hozzáféréshez szükség van az alkalmazottak és vendégek csoportjaira és alkalmazására, amelyek idővel valószínűleg változnak. Az elavult hozzáférés-hozzárendelésekkel járó kockázat csökkentése érdekében a rendszergazdák hozzáférési felülvizsgálatokat hozhatnak létre a csoporttagok vagy az alkalmazások hozzáférése számára. Kövesse az alábbi hivatkozás utasításait:

| Útmutatók| Leírás |
| - | - |
| [Hozzáférési felülvizsgálatok létrehozása](create-access-review.md)| Hozzon létre egy vagy több hozzáférési felülvizsgálatot a csoporttagok vagy az alkalmazások hozzáférése számára. |
| [Hozzáférési felülvizsgálatok végrehajtása](perform-access-review.md)| Hozzáférési felülvizsgálatot végezhet egy csoport vagy egy alkalmazáshoz hozzáféréssel rendelkezik felhasználók számára. |
| [A hozzáférés önértékelése](review-your-access.md)| A tagok felülvizsgálják a saját hozzáférését egy csoporthoz vagy alkalmazáshoz |
| [Teljes hozzáférési felülvizsgálat](complete-access-review.md)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |
| [Művelet helyszíni csoportok esetében](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| PowerShell-példaszk szkript a helyszíni csoportok hozzáférési felülvizsgálataival való eléréshez. |


### <a name="review-azure-ad-roles"></a>Az Azure AD-szerepkörök áttekintése

Az elavult szerepkör-hozzárendelésekkel járó kockázatok csökkentése érdekében rendszeresen tekintse át a kiemelt Azure AD-szerepkörökhöz való hozzáférést.

![Képernyőkép az Azure AD-szerepkörök "Tagság áttekintése" listájáról.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Kövesse az alábbi hivatkozásokon keresztül elérhető utasításokat:

| Útmutatók | Leírás |
| - | - |
 [Hozzáférési felülvizsgálatok létrehozása](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálatok létrehozása kiemelt Azure AD-szerepkörökhöz a PIM-ban |
| [A hozzáférés önértékelése](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ha rendszergazdai szerepkörhöz van rendelve, hagyja jóvá vagy tiltsa le a szerepkörhöz való hozzáférést |
| [Hozzáférési felülvizsgálat befejezése](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |


### <a name="review-azure-resource-roles"></a>Azure-erőforrásszerepk szerepkörök áttekintése

Az elavult szerepkör-hozzárendelésekkel járó kockázatok csökkentése érdekében rendszeresen tekintse át a kiemelt Azure-erőforrás-szerepkörök hozzáférését. 

![azure ad-szerepkörök áttekintése](./media/deploy-access-review/9-review-azure-roles-picker.png)

Kövesse az alábbi hivatkozásokon keresztül elérhető utasításokat:

| Útmutatók| Leírás |
| - | -|
| [Hozzáférési felülvizsgálatok létrehozása](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálatok létrehozása kiemelt Azure-erőforrás-szerepkörökhöz a PIM-ban |
| [A hozzáférés önértékelése](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ha rendszergazdai szerepkörhöz van rendelve, hagyja jóvá vagy tiltsa le a szerepkörhöz való hozzáférést |
| [Hozzáférési felülvizsgálat befejezése](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Hozzáférési felülvizsgálat megtekintése és az eredmények alkalmazása |


## <a name="use-the-access-reviews-api"></a>A Hozzáférési felülvizsgálatok API használata

Tekintse meg a [Graph API metódusokat,](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) [valamint a szerepkör-](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) és alkalmazásengedély-engedélyezési ellenőrzéseket a felülvizsgálható erőforrások kezeléséhez és kezeléséhez. A hozzáférési felülvizsgálatok metódusai az Microsoft Graph API-ban alkalmazás- és felhasználói környezetben is elérhetők. Ha szkripteket futtat az alkalmazáskörnyezetben, az API futtatásához használt fióknak (a szolgáltatás alapelve) az "AccessReview.Read.All" engedélyt kell biztosítani a Hozzáférési felülvizsgálatok információinak lekérdezéséhez.

A népszerű hozzáférési felülvizsgálati feladatok a következő Graph API automatizálhatóak:

* Hozzáférési felülvizsgálat létrehozása és kezdete

* Hozzáférési felülvizsgálat manuálisan, az ütemezett vége előtt

* List all running Access Reviews and their status

* Tekintse meg a felülvizsgálati sorozatok előzményeit, valamint az egyes felülvizsgálatok során meghozott döntéseket és műveleteket.

* Döntések gyűjtése hozzáférési felülvizsgálatból

* Döntéseket gyűjt a befejezett felülvizsgálatok alapján, ahol a felülvizsgáló más döntést hozott, mint amit a rendszer javasolt.

Az automatizáláshoz Graph API új lekérdezések létrehozásakor javasoljuk a [Graph Explorer használatát.](https://developer.microsoft.com/en-us/graph/graph-explorer) A Graph-lekérdezéseket még azelőtt felépítheti és feltárhatja, mielőtt szkriptekbe és kódokba íratja őket. Így gyorsan iterálhatja a lekérdezést, így pontosan a keresett eredményeket kaphatja meg a szkript kódjának módosítása nélkül.

## <a name="monitor-access-reviews"></a>Hozzáférési felülvizsgálatok figyelése

A hozzáférési felülvizsgálati tevékenységek rögzítve vannak, és az Azure AD auditnaplóiban [érhetők el.](../reports-monitoring/concept-audit-logs.md) A naplózási adatokat szűrheti a kategóriára, a tevékenységtípusra és a dátumtartományra. Példa a lekérdezésre:

| Kategória| Szabályzat |
| - | - |
| Tevékenység típusa| Hozzáférési felülvizsgálat létrehozása |
| | Hozzáférési felülvizsgálat frissítése |
| | Hozzáférési felülvizsgálat véget ért |
| | Hozzáférési felülvizsgálat törlése |
| | Döntés jóváhagyása |
| | Elutasítási döntés |
| | Döntés az alaphelyzetbe állításról |
| | Döntés alkalmazása |
| Dátumtartomány| hét nap |


A hozzáférési felülvizsgálatok fejlettebb lekérdezéséhez és elemzéséhez, valamint a változások és a felülvizsgálatok befejezésének nyomon követéséhez javasoljuk, hogy exportálja az Azure AD-auditnaplókat az [Azure Log Analyticsbe](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) vagy az Azure Event Hubba. Az Azure Log Analyticsben tárolva használhatja a hatékony [elemzési](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) nyelvet, és saját irányítópultokat hozhat létre.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az alábbi kapcsolódó technológiákat.

* [Mi az az Azure AD-jogosultságkezelés?](entitlement-management-overview.md)

* [Mi az az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)