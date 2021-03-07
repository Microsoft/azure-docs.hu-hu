---
title: Azure Active Directory feltételes hozzáférés központi telepítésének megtervezése
description: Megtudhatja, hogyan tervezhet feltételes hozzáférési szabályzatokat, és hogyan helyezheti üzembe hatékonyan a szervezetében.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13460fad0ed106f5c0590df961ceca54cb04cd7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427181"
---
# <a name="plan-a-conditional-access-deployment"></a>A feltételes hozzáférés üzembe helyezésének megtervezése

A feltételes hozzáférés megtervezése kritikus fontosságú a szervezete alkalmazásaihoz és erőforrásaihoz való hozzáférési stratégia megvalósításához.

A mobil-első, felhőalapú világában a felhasználók bárhonnan hozzáférhetnek a szervezet erőforrásaihoz a különböző eszközök és alkalmazások használatával. Ennek eredményeképpen az erőforrásokhoz való hozzáférésre való összpontosítás már nem elegendő. Azt is meg kell fontolnia, hogy hol van a felhasználó, a használt eszköz, az elérni kívánt erőforrás és még sok más. 

A Azure Active Directory (Azure AD) feltételes hozzáférése olyan jeleket elemez, mint például a felhasználó, az eszköz és a hely a döntések automatizálásához és az erőforráshoz tartozó szervezeti hozzáférési házirendek betartatásához. A feltételes hozzáférési szabályzatok használatával olyan hozzáférési vezérlőket alkalmazhat, mint például a Multi-Factor Authentication (MFA). A feltételes hozzáférési szabályzatok lehetővé teszik a felhasználók számára, hogy a biztonsághoz szükségesek legyenek a többtényezős hitelesítéshez, és nem szükségesek a felhasználók számára.

![Feltételes hozzáférés áttekintése](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

A Microsoft a biztonsági [alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) nevű szabványos feltételes házirendeket biztosít, amelyek biztosítják az alapszintű biztonságot. Előfordulhat azonban, hogy a szervezet nagyobb rugalmasságot igényel, mint a biztonsági alapértékek ajánlata. A feltételes hozzáférés segítségével testre szabhatja a biztonsági beállításokat, és részletesebben is testreszabhatja az igényeinek megfelelő új házirendeket.

## <a name="learn"></a>Learn

Mielőtt elkezdené, győződjön meg arról, hogyan működik a [feltételes hozzáférés](overview.md) , és hogy mikor érdemes használni.

### <a name="benefits"></a>Előnyök

A feltételes hozzáférés üzembe helyezésének előnyei a következők:

* Növelje a termelékenységet. Csak olyan bejelentkezési feltétellel rendelkező felhasználókat kell megszakítani, mint például az MFA, ha egy vagy több jel garantálja azt. A feltételes hozzáférési szabályzatok segítségével szabályozhatja, hogy a rendszer Mikor kéri a felhasználóktól az MFA-t, ha a hozzáférés le van tiltva, és ha megbízható eszközt kell használnia.

* Kockázatkezelés. A kockázatértékelés házirend-feltételekkel való automatizálása azt jelenti, hogy a kockázatos bejelentkezések észlelése és szervizelése vagy letiltása után történik. A feltételes hozzáférés az Identity Protection szolgáltatással való összekapcsolása, amely észleli a rendellenességeket és a gyanús eseményeket, lehetővé teszi, hogy az erőforrásokhoz való hozzáférés letiltását vagy [meglétét](../identity-protection/overview-identity-protection.md)engedélyezze. 

* Foglalkozzon a megfelelőséggel és a szabályozással. A feltételes hozzáférés lehetővé teszi az alkalmazásokhoz való hozzáférés naplózását, a részvétel használati feltételeit és a megfelelőségi szabályzatok alapján történő hozzáférés korlátozását.

* A Cost kezelése. A hozzáférési szabályzatok az Azure AD-be való áthelyezésével csökkenthető a feltételes hozzáférésre vonatkozó egyéni vagy helyszíni megoldások, valamint az infrastruktúra költségei.

### <a name="license-requirements"></a>Licenckövetelmények

Lásd a [feltételes hozzáférési licencekre vonatkozó követelményeket](overview.md).

Ha további funkciókra van szükség, szükség lehet a kapcsolódó licencekre is. További információ: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Előfeltételek

* Egy prémium szintű Azure AD-vagy próbaverziós licenccel rendelkező Azure AD-bérlő használata. Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy fiók feltételes hozzáférésű rendszergazdai jogosultságokkal.

* Egy nem rendszergazda felhasználó, aki ismeri a jelszót, például tesztfelhasználó. Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../fundamentals/add-users-azure-active-directory.md)című témakört.

* Olyan csoport, amelynek a nem rendszergazda felhasználó a tagja. Ha csoportot kell létrehoznia, tekintse [meg a csoport létrehozása és a tagok hozzáadása Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Erőforrások betanítása

A feltételes hozzáférés megismeréséhez a következő erőforrások hasznosak lehetnek:


#### <a name="videos"></a>Videók

* [Mit jelent a feltételes hozzáférés?](https://youtu.be/ffMAw2IVO7A)
* [Feltételes hozzáférés üzembe helyezése](https://youtu.be/c_izIRNJNuk)
* [Hogyan lehet feltételes hozzáférési szabályzatokat felderíteni a végfelhasználók számára?](https://youtu.be/0_Fze7Zpyvc)
* [Felhasználók belefoglalása vagy kizárása a feltételes hozzáférési házirendből](https://youtu.be/5DsW1hB3Jqs)
* [Feltételes hozzáférés eszközvezérlőkkel](https://youtu.be/NcONUf-jeS4)
* [Feltételes hozzáférés az Azure AD MFA-val](https://youtu.be/Tbc-SU97G-w)
* [Feltételes hozzáférés Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Online tanfolyamok a PluralSight-on

* [Identitáskezelés tervezése Microsoft Azureban](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Tervezési hitelesítés Microsoft Azurehoz](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Microsoft Azure kialakításának engedélyezése](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

A környezetében érdemes figyelembe vennie a szervezeti igényeket is.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és a projekt szerepkörei egyértelműek.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Ha az új szabályzatok készen állnak a környezetre, az éles környezet fázisaiban helyezheti üzembe őket. Először alkalmazza a házirendet a felhasználók egy kis készletére, és ellenőrizze, hogy a szabályzat a várt módon működik-e. Tekintse [meg a próbaüzem ajánlott eljárásait](../fundamentals/active-directory-deployment-plans.md)ismertető témakört.

> [!NOTE]
> A rendszergazdák számára nem specifikus új szabályzatok kivonásához zárja ki az összes rendszergazdát. Ez biztosítja, hogy a rendszergazdák továbbra is hozzáférhessenek a Szabályzathoz, és módosításokat hajtanak végre, illetve visszavonják azt, ha jelentős hatással van rá. Mindig ellenőrizze a házirendet kisebb felhasználói csoportokkal, mielőtt alkalmazza az összes felhasználóra.

## <a name="understand-conditional-access-policy-components"></a>A feltételes hozzáférési szabályzat összetevőinek ismertetése
A feltételes hozzáférési szabályzatok a-then utasítások: Ha egy hozzárendelés teljesül, alkalmazza ezeket a hozzáférési vezérlőket.

A feltételes hozzáférési szabályzatok konfigurálásakor a feltételeket *hozzárendeléseknek* nevezzük. A feltételes hozzáférési házirendek lehetővé teszik, hogy bizonyos hozzárendelések alapján kényszerítse ki a szervezet alkalmazásaihoz való hozzáférés-vezérlést.


További információ: [feltételes hozzáférési szabályzat létrehozása](concept-conditional-access-policies.md).

![házirend létrehozása képernyő](media/plan-conditional-access/create-policy.png)

A [hozzárendelések](concept-conditional-access-policies.md#assignments) definiálják a

* A szabályzat által érintett [felhasználók és csoportok](concept-conditional-access-users-groups.md)

* [Felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md) , amelyekre a szabályzat érvényes lesz 

* A szabályzat hatálya alá tartozó [feltételek](concept-conditional-access-conditions.md) .

[Hozzáférés-vezérlési](concept-conditional-access-policies.md) beállítások határozzák meg a szabályzat érvényesítésének módját:

* A felhőalapú alkalmazásokhoz való hozzáférés [engedélyezése vagy letiltása](concept-conditional-access-grant.md) .

* A [munkamenet-vezérlők](concept-conditional-access-session.md) lehetővé teszik az adott felhőalapú alkalmazások korlátozott felhasználói élményét.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Kérdezze meg a megfelelő kérdéseket a szabályzatok létrehozásához

A szabályzatok választ adnak arra, hogy ki férhet hozzá az erőforrásaihoz, milyen erőforrásokhoz férhet hozzá, és milyen feltételek mellett. A szabályzatok a hozzáférés biztosítására vagy a hozzáférés letiltására használhatók. Ügyeljen arra, hogy a házirend által elérni kívánt kérdésekkel kapcsolatos megfelelő kérdéseket Kérdezzen meg. 

A felépítése előtt dokumentálja az egyes szabályzatok kérdéseire adott válaszokat. 

#### <a name="common-questions-about-assignments"></a>A hozzárendelésekkel kapcsolatos gyakori kérdések

[Felhasználók és csoportok](concept-conditional-access-users-groups.md)

* Mely felhasználók és csoportok szerepeljenek a szabályzatban, vagy ki lesznek zárva?

* Ez a szabályzat tartalmazza az összes felhasználót, a felhasználók adott csoportjait, a címtárbeli szerepköröket vagy a külső felhasználókat?

[Felhőalkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md)

* Milyen alkalmazás (oka) t fog alkalmazni a szabályzat?

* Milyen felhasználói műveletekre lesz érvényes a szabályzat?

[Feltételek](concept-conditional-access-conditions.md)

* Mely platformokat fogja tartalmazni vagy kizárni a szabályzatból?

* Mik a szervezet megbízható helyei?

* Milyen helyekre vonatkozik a szabályzat?

* Milyen típusú ügyfélalkalmazások (böngésző, mobil, asztali ügyfelek, örökölt hitelesítési módszerekkel rendelkező alkalmazások) lesznek belefoglalva vagy kizárva a szabályzatból?

* Vannak olyan szabályzatai, amelyek az Azure AD-hez csatlakoztatott vagy a hibrid Azure AD-hez csatlakoztatott eszközök kizárásával járnak a szabályzatok alapján? 

* Az [Identity Protection](../identity-protection/concept-identity-protection-risks.md)használata esetén érdemes beépíteni a kockázatos védelmet?

#### <a name="common-questions-about-access-controls"></a>A hozzáférés-vezérléssel kapcsolatos gyakori kérdések

[Engedélyezés vagy Letiltás ](concept-conditional-access-grant.md) 

Szeretne hozzáférést biztosítani az erőforrásokhoz a következők közül egy vagy több beírásával?

* MFA megkövetelése

* Eszköz megfelelőként való megjelölésének megkövetelése

* Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

* Jóváhagyott ügyfélalkalmazás megkövetelése

* Alkalmazásvédelmi szabályzat megkövetelése

[Munkamenet-vezérlő](concept-conditional-access-session.md)

Szeretné kikényszeríteni a következő hozzáférés-vezérlések valamelyikét a Cloud apps szolgáltatásban?

* Alkalmazás által kényszerített engedélyek használata

* feltételes hozzáférést biztosító alkalmazás-vezérlő használata

* Bejelentkezési gyakoriság érvényesítése

* Állandó böngésző-munkamenetek használata

### <a name="access-token-issuance"></a>Hozzáférési jogkivonat kiállítása

Fontos megérteni, hogy a hozzáférési jogkivonatok hogyan legyenek kibocsátva. 

![Hozzáférési jogkivonat kiállítási diagramja](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Ha nincs szükség hozzárendelésre, és nincs érvényben a feltételes hozzáférési házirend, akkor az alapértelmezett viselkedés egy hozzáférési jogkivonat kiadása. 

Vegyünk például egy olyan szabályzatot, ahol:

Ha a felhasználó az 1. csoportban van, akkor az 1. alkalmazás eléréséhez kényszerítse az MFA-t.

Ha egy, az 1. csoportban nem lévő felhasználó megpróbál hozzáférni az alkalmazáshoz, a "If" feltétel teljesül, és a rendszer jogkivonatot állít ki. Az 1. csoporton kívüli felhasználók kizárásához külön házirendre van szükség az összes többi felhasználó blokkolásához.

## <a name="follow-best-practices"></a>Ajánlott eljárások követése

A feltételes hozzáférési keretrendszer nagyszerű konfigurációs rugalmasságot biztosít. A nagy rugalmasság azonban azt is jelenti, hogy az egyes konfigurációs házirendeket alaposan át kell tekintenie, mielőtt a nemkívánatos eredmények elkerülnek.

### <a name="apply-conditional-access-policies-to-every-app"></a>Feltételes hozzáférési szabályzatok alkalmazása minden alkalmazásra

A hozzáférési jogkivonatok alapértelmezés szerint ki vannak állítva, ha egy feltételes hozzáférési szabályzat nem indít el hozzáférés-vezérlést. Győződjön meg arról, hogy minden alkalmazáshoz legalább egy feltételes hozzáférési szabályzat van alkalmazva

> [!IMPORTANT]
> Ügyeljen arra, hogy a blokkot és az összes alkalmazást egyetlen szabályzatban használja. Ez az Azure felügyeleti portálon kívülről is zárolhatja a rendszergazdákat, és a kizárások nem konfigurálhatók olyan fontos végpontokhoz, mint a Microsoft Graph.

### <a name="minimize-the-number-of-conditional-access-policies"></a>A feltételes hozzáférési szabályzatok számának csökkentése

Az egyes alkalmazásokra vonatkozó szabályzatok létrehozása nem hatékony, és bonyolult felügyeletet eredményez. A feltételes hozzáférés csak az első 195 szabályzatot fogja alkalmazni felhasználónként. Javasoljuk, hogy elemezze az alkalmazásokat, és csoportosítsa azokat olyan alkalmazásokba, amelyek ugyanazok az erőforrás-követelmények ugyanazon felhasználók számára. Ha például az összes Microsoft 365 alkalmazás vagy az összes HR-alkalmazás ugyanazokkal a felhasználókkal azonos, hozzon létre egy házirendet, és adja meg az összes olyan alkalmazást, amelyre vonatkozik. 

### <a name="set-up-emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok beállítása

Ha helytelenül konfigurált egy házirendet, akkor az a Azure Portalon kívülről is zárolhatja a szervezeteket. A szervezeten belül két vagy több [vészhelyzeti hozzáférési fiók](../roles/security-emergency-access.md) létrehozásával csökkentheti a véletlen rendszergazdai zárolás hatását.

* Hozzon létre egy házirend-felügyeleti célra dedikált felhasználói fiókot, és zárja ki az összes szabályzatot.

### <a name="set-up-report-only-mode"></a>Csak jelentési mód beállítása

Nehéz lehet megbecsülni a gyakori telepítési kezdeményezések által érintett felhasználók számát és nevét, például:

* örökölt hitelesítés blokkolása
* MFA megkövetelése
* a bejelentkezési kockázati szabályzatok megvalósítása

A [csak jelentési mód](concept-conditional-access-report-only.md) lehetővé teszi a rendszergazdák számára a feltételes hozzáférési házirendek hatásának kiértékelését, mielőtt engedélyezné azokat a környezetében.

Megtudhatja, hogyan [konfigurálhat csak jelentési üzemmódot feltételes hozzáférési szabályzaton](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Megszakadás tervezése

Ha egyetlen hozzáférés-vezérlést (például MFA-t vagy hálózati helyet) használ az IT-rendszerek biztonságossá tételéhez, akkor a rendszer nem fér hozzá a hibákhoz, ha az egyetlen hozzáférés-vezérlés elérhetetlenné válik vagy helytelenül van konfigurálva. Ha csökkenteni szeretné a zárolás kockázatát a váratlan megszakítások során, tervezze meg a szervezete számára szükséges [stratégiákat](../authentication/concept-resilient-controls.md) .

### <a name="set-naming-standards-for-your-policies"></a>A szabályzatok elnevezési szabványainak megadása

Az elnevezési szabvány segítségével megtalálhatja a szabályzatokat, és megtekintheti azok célját anélkül, hogy megnyitná őket az Azure felügyeleti portálon. Javasoljuk, hogy a szabályzatot a következőre nevezze:

* Sorszám

* A felhőalapú alkalmazás (ok) a következőre vonatkozik

* A válasz

* Kik érvényesek

* Ha érvényes (ha van ilyen)

![A szabályzatok elnevezési szabványait bemutató képernyőkép.](media/plan-conditional-access/11.png)

**Példa**; A többtényezős hitelesítés megkövetelése a külső hálózatokból származó Dynamics CRP-alkalmazáshoz hozzáférő felhasználók számára:

![Elnevezési szabvány](media/plan-conditional-access/naming-example.png)

A leíró név segít megőrizni a feltételes hozzáférés megvalósításának áttekintését. A sorozatszám akkor hasznos, ha egy beszélgetési szabályzatra kell hivatkoznia. Ha például a telefonon keresztül kommunikál egy rendszergazdával, megkérheti őket, hogy a probléma megoldásához nyissa meg a szabályzat CA01.

#### <a name="naming-standards-for-emergency-access-controls"></a>A vészhelyzeti hozzáférés-vezérlések elnevezési szabályai

Az aktív szabályzatokon kívül olyan letiltott házirendeket is végrehajthat, amelyek másodlagos [rugalmas hozzáférés-vezérléssel működnek leállás vagy vészhelyzeti helyzetekben](../authentication/concept-resilient-controls.md). A készenléti szabályzatok elnevezési szabványának a következőket kell tartalmaznia:
* Az elején engedélyezze a VÉSZHELYZETet, hogy a név kitűnjön a többi szabályzat között.

* Annak a megszakadásnak a neve, amelyre érvényesnek kell lennie.

* Egy megrendelési sorozatszám, amely segít a rendszergazdának megismerni, hogy mely rendelési házirendeket kell engedélyezni.

**Példa**

A következő név azt jelzi, hogy ez a házirend az első négy házirend, amely lehetővé teszi, hogy a rendszer MFA-fennakadást biztosítson:

EM01 – engedélyezés VÉSZHELYZETben: MFA-megszakítás [1/4] – Exchange SharePoint: hibrid Azure AD-csatlakozás szükséges a VIP-felhasználók számára.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Zárja ki azokat az országokat, amelyekről soha nem vár bejelentkezést.

Az Azure Active Directory lehetővé teszi, hogy [elnevezett helyszíneket](location-condition.md)hozzon létre. Hozzon létre egy elnevezett helyet, amely tartalmazza az összes olyan országot, amelyről soha nem várható a bejelentkezés. Ezután hozzon létre egy szabályzatot minden olyan alkalmazáshoz, amely blokkolja az adott helyről való bejelentkezést. **Győződjön meg arról, hogy a szabályzat alól mentesül a rendszergazdák** számára.

### <a name="plan-your-policy-deployment"></a>A szabályzat központi telepítésének megtervezése

Ha az új szabályzatok készen állnak a környezetére, mindenképpen tekintse át az egyes szabályzatokat, hogy elkerülje a nemkívánatos eredmények elkerülését.

## <a name="common-policies"></a>Gyakori szabályzatok

A feltételes hozzáférési szabályzat megtervezésekor mérje fel, hogy létre kell-e hoznia házirendeket a következő eredmények eléréséhez.

* [MFA megkövetelése](#require-mfa)
* [Válaszadás potenciálisan feltört fiókokra](#respond-to-potentially-compromised-accounts)
* [Felügyelt eszközök megkövetelése](#require-managed-devices)
* [Jóváhagyott ügyfélalkalmazások megkövetelése](#require-approved-client-apps)
* [Fájlhozzáférés](#block-access)

### <a name="require-mfa"></a>MFA megkövetelése

Általános használati esetek az MFA-hozzáférés megköveteléséhez:

* [Adminisztrátorok szerint](howto-conditional-access-policy-admin-mfa.md)

* [Adott alkalmazások számára](../authentication/tutorial-enable-azure-mfa.md)

* [Minden felhasználó számára](howto-conditional-access-policy-all-users-mfa.md)

* [A hálózati helyekről nem bízik meg](untrusted-networks.md)

* [Azure-felügyelet](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Válaszadás potenciálisan feltört fiókokra

A feltételes hozzáférési szabályzatokkal automatizált válaszokat alkalmazhat a bejelentkezésekre potenciálisan feltört identitásokkal. A fiók biztonságának valószínűsége kockázati szintek formájában van kifejezve. Az Identity Protection két kockázati szintet számít ki: a bejelentkezési kockázat és a felhasználói kockázat. A következő három alapértelmezett szabályzat engedélyezhető.

* [Az összes felhasználó regisztrálásának megkövetelése az MFA-ban](howto-conditional-access-policy-risk.md)

* [Jelszó megváltoztatásának megkövetelése a nagy kockázatú felhasználók számára](howto-conditional-access-policy-risk.md)

* [MFA megkövetelése közepes vagy magas bejelentkezési kockázatú felhasználók számára](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Felügyelt eszközök megkövetelése

A támogatott eszközöknek a Felhőbeli erőforrások eléréséhez való elterjedése segíti a felhasználók termelékenységének növelését. Valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásaihoz olyan eszközök férhessenek hozzá, amelyek ismeretlen védelmi szinttel rendelkeznek. Ezeknek az erőforrásoknak a [használatához a felhasználók csak felügyelt eszköz használatával férhetnek hozzájuk](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Jóváhagyott ügyfélalkalmazások megkövetelése

Az alkalmazottak személyes és munkahelyi feladatokhoz egyaránt használják mobileszközökön. BYOD-forgatókönyvek esetén el kell döntenie, hogy a teljes eszközt vagy csak a rajta lévő adatok kezelését végzi. csak az adatkezelés és a hozzáférés kezelése esetén olyan [jóváhagyott felhőalapú alkalmazásokat igényelhet](app-based-conditional-access.md) , amelyek védik a vállalati adatait. megkövetelheti például, hogy az e-mailek csak az Outlook Mobile használatával legyenek elérhetők, nem pedig általános levelezési programon keresztül.

### <a name="block-access"></a>Hozzáférés letiltása

Az [összes hozzáférés blokkolásának](howto-conditional-access-policy-block-access.md) lehetősége hatékony. Például akkor használható, ha egy alkalmazást az Azure AD-be telepít át, de még nem áll készen a bejelentkezésre. Hozzáférés letiltása: 

* Felülbírálja a felhasználó összes többi hozzárendelését

* A teljes szervezet beléptetésének letiltása a bérlőre való bejelentkezéssel

> [!IMPORTANT]
> Ha olyan házirendet hoz létre, amely letiltja az összes felhasználó hozzáférését, ügyeljen arra, hogy kizárják a vészhelyzeti hozzáférési fiókokat, és az összes rendszergazdát kizárja a szabályzatból.

Egyéb gyakori forgatókönyvek, amelyekkel letilthatja a felhasználók hozzáférését:

* [Bizonyos hálózati telephelyek letiltása](howto-conditional-access-policy-location.md) a felhőalapú alkalmazások eléréséhez. Ezzel a szabályzattal blokkolhatja azokat az országokat, amelyekről tudja, hogy a forgalom nem származik.

* Az Azure AD támogatja a régi hitelesítést. A régi hitelesítés azonban nem támogatja az MFA-t, és számos környezet megköveteli az identitások biztonságának kezelésében. Ebben az esetben [letilthatja, hogy az alkalmazások örökölt hitelesítéssel ](block-legacy-authentication.md) hozzáférjenek a bérlő erőforrásaihoz.

## <a name="build-and-test-policies"></a>Szabályzatok létrehozása és tesztelése

Az üzembe helyezés minden egyes fázisában ellenőrizze, hogy az eredmények a várt módon vannak-e kiértékelve. 

Ha új szabályzatok állnak készen, az éles környezet fázisaiban helyezheti üzembe őket:

* Belső módosítási kommunikáció biztosítása a végfelhasználók számára.

* Kezdje a felhasználókat egy kis készlettel, és ellenőrizze, hogy a szabályzat a várt módon működik-e.

* Ha kibővít egy szabályzatot több felhasználó belefoglalásához, folytassa az összes rendszergazda kizárásával. A rendszergazdák kizárásával gondoskodhat arról, hogy valaki továbbra is hozzáférhessen egy szabályzathoz, ha módosításra van szükség.

* Az összes felhasználóra vonatkozó szabályzatot csak alapos tesztelés után alkalmazhat. Győződjön meg arról, hogy van legalább egy rendszergazdai fiókja, amelyhez a szabályzat nem vonatkozik.

### <a name="create-test-users"></a>Tesztelési felhasználók létrehozása

Hozzon létre olyan tesztelési felhasználókat, akik az éles környezetben lévő felhasználókat tükrözik. A tesztelési felhasználók létrehozásával ellenőrizheti, hogy a szabályzatok a várt módon működnek-e, mielőtt a valós felhasználókra hatással lenne, és potenciálisan megzavarják az alkalmazásokhoz és erőforrásokhoz

Egyes szervezetek erre a célra rendelkeznek tesztelési Bérlővel. Azonban nehéz lehet az összes feltételt és alkalmazást újra létrehozni egy tesztelési bérlőben, hogy teljes körűen tesztelje a szabályzatok eredményét.

### <a name="create-a-test-plan"></a>Tesztelési terv létrehozása

A tesztelési terv fontos, hogy összehasonlítsa a várt eredmények és a tényleges eredmények összehasonlítását. A tesztelés előtt mindig legyen várakozás. A következő táblázat példákat mutat be a tesztelési esetekre. A feltételes hozzáférési szabályzatok konfigurálása alapján állítsa be a forgatókönyveket és a várt eredményeket.

| Szabályzat| Eset| Várt eredmény |
| - | - | - |
| [MFA megkövetelése, ha nem működik](untrusted-networks.md)| A meghatalmazott felhasználó bejelentkezik az alkalmazásba egy megbízható helyen/munkahelyen| A felhasználónak nem kell megadnia az MFA-t |
| [MFA megkövetelése, ha nem működik](untrusted-networks.md)| Az engedélyezve lévő felhasználó bejelentkezik az alkalmazásba, és nem megbízható helyen/munkahelyen| A rendszer felszólítja a felhasználót az MFA használatára, és sikeresen bejelentkezhet |
| [MFA megkövetelése (rendszergazda)](../fundamentals/concept-fundamentals-security-defaults.md)| Globális rendszergazda bejelentkezik az alkalmazásba| A rendszergazda az MFA-t kéri |
| [Kockázatos bejelentkezések](../identity-protection/howto-identity-protection-configure-risk-policies.md)| A felhasználó nem jóváhagyott böngésző használatával jelentkezik be az alkalmazásba| A rendszergazda az MFA-t kéri |
| [Eszközfelügyelet](require-managed-devices.md)| A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni egy jóváhagyott eszközről| Hozzáférés engedélyezve |
| [Eszközfelügyelet](require-managed-devices.md)| Az engedéllyel rendelkező felhasználó megpróbál bejelentkezni egy jogosulatlan eszközről| Hozzáférés letiltva |
| [A kockázatos felhasználók jelszavának módosítása](../identity-protection/howto-identity-protection-configure-risk-policies.md)| A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni a feltört hitelesítő adatokkal (nagy kockázatú bejelentkezés)| A rendszer megkéri a felhasználót, hogy változtassa meg a jelszót, vagy a hozzáférés le legyen tiltva a szabályzat alapján |


### <a name="configure-the-test-policy"></a>A tesztelési házirend konfigurálása

A [Azure Portal](https://portal.azure.com/)a feltételes hozzáférési szabályzatokat a Azure Active Directory > biztonság > feltételes hozzáférés területen konfigurálja.

Ha további információra van szüksége a feltételes hozzáférési házirendek létrehozásáról, tekintse meg a következő példát: [feltételes hozzáférési szabályzat, amely arra kéri az MFA-t, amikor egy felhasználó bejelentkezik a Azure Portalba](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Ez a rövid útmutató a következőket teszi lehetővé:

* Ismerkedjen meg a felhasználói felülettel

* A feltételes hozzáférés működésének első benyomása

### <a name="enable-the-policy-in-report-only-mode"></a>A házirend engedélyezése csak jelentési módban

A házirend hatásának felméréséhez először [csak jelentési módban](concept-conditional-access-report-only.md)engedélyezze a szabályzatot. A csak jelentési házirendek kiértékelése a bejelentkezés során történik, de a vezérlők és a munkamenet-vezérlőelemek megadása nem kényszerített. Ha a szabályzatot csak jelentési módban menti, a bejelentkezési naplókban láthatja a valós idejű bejelentkezések hatásait. A bejelentkezési naplók területen válasszon ki egy eseményt, és navigáljon a csak jelentés lapra, ahol megtekintheti az egyes jelentési házirendek eredményét.


![csak jelentés mód ](media/plan-conditional-access/report-only-mode.png)

A szabályzat kiválasztásával azt is megtudhatja, hogyan értékelték a szabályzat hozzárendeléseit és hozzáférés-vezérlését a szabályzat részletei képernyő használatával. Ahhoz, hogy egy házirend egy bejelentkezésre vonatkozzon, minden konfigurált hozzárendelésnek teljesülnie kell. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>A szabályzatok hatásának megismerése az adatfelismerések és a jelentéskészítési munkafüzet használatával

A feltételes hozzáférési szabályzatok összesített hatásait megtekintheti a bepillantást és a jelentéskészítési munkafüzetben. A munkafüzet eléréséhez szüksége van egy Azure Monitor előfizetésre, és a [bejelentkezési naplókat egy log Analytics munkaterületre kell továbbítania](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Bejelentkezések szimulálása a mi-if eszköz használatával

A feltételes hozzáférési szabályzat érvényesítésének egy másik módja a [mi-if eszköz](troubleshoot-conditional-access-what-if.md)használata, amely szimulálja, hogy mely házirendek lesznek érvényesek egy feltételezett körülmények között bejelentkezett felhasználóra. Válassza ki a tesztelni kívánt bejelentkezési attribútumokat (például a felhasználó, az alkalmazás, az eszköz platformja és a hely), és ellenőrizze, hogy mely szabályzatok érvényesek.

> [!NOTE] 
> Míg a szimulált Futtatás jó ötlet, hogy milyen hatással van a feltételes hozzáférési házirend, nem helyettesíti a tényleges teszt-futtatást.

### <a name="test-your-policy"></a>A szabályzat tesztelése

Tesztelje az egyes teszteket a tesztelési tervben a felhasználók tesztelésével.

**Győződjön meg arról, hogy teszteli a szabályzat kizárási feltételeit**. Előfordulhat például, hogy kizár egy felhasználót vagy csoportot egy olyan szabályzatból, amely MFA-t igényel. Annak tesztelése, hogy a rendszer a kizárt felhasználókat kéri-e a többtényezős hitelesítésre, mert az egyéb házirendek kombinációja megköveteli az MFA használatát a felhasználók számára.

### <a name="roll-back-policies"></a>Házirendek visszaállítása

Ha az újonnan megvalósított szabályzatokat vissza kell állítania, használja a következő lehetőségek közül egyet vagy többet:

* **Tiltsa le a házirendet.** A szabályzat letiltásával gondoskodhat arról, hogy ne legyen érvényes, amikor egy felhasználó megpróbál bejelentkezni. Bármikor visszatérhet, és engedélyezheti a szabályzatot, ha azt szeretné használni.

![házirend-rendszerkép engedélyezése](media/plan-conditional-access/enable-policy.png)

* **Felhasználó vagy csoport kizárása egy szabályzatból.** Ha a felhasználó nem tud hozzáférni az alkalmazáshoz, dönthet úgy, hogy kizárja a felhasználót a szabályzatból.

![felhasználók és csoportok kizárása](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Ezt a beállítást takarékosan kell használni, csak olyan helyzetekben, amikor a felhasználó megbízható. A felhasználót a lehető leghamarabb vissza kell venni a szabályzatba vagy a csoportba.

* **Törölje a szabályzatot.** Ha a házirend már nem szükséges, [törölje](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) azt.

## <a name="manage-access-to-cloud-apps"></a>Felhőalapú alkalmazásokhoz való hozzáférés kezelése

A feltételes hozzáférési szabályzatok vezérléséhez és kezeléséhez használja a következő kezelési lehetőségeket:

![A képernyőképen a C házirendek kezelési lehetőségei láthatók, többek között a nevesített helyszínek, az egyéni vezérlők, a Használati feltételek, a V P N kapcsolat és a kiválasztott klasszikus szabályzatok.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Nevesített helyek

A feltételes hozzáférési szabályzat helyének feltétele lehetővé teszi a hozzáférés-vezérlési beállítások összekapcsolását a felhasználók hálózati helyein. Az [elnevezett helyszínekkel](location-condition.md)logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára.

### <a name="custom-controls"></a>Egyéni vezérlők

Az [Egyéni vezérlők](controls.md) átirányítják a felhasználókat egy kompatibilis szolgáltatásba az Azure ad-n kívüli hitelesítési követelmények kielégítése érdekében. Ennek a vezérlőnek a kielégítéséhez a rendszer átirányítja a felhasználó böngészőjét a külső szolgáltatáshoz, végrehajtja a szükséges hitelesítést, majd átirányítja az Azure AD-re. Az Azure AD ellenőrzi a választ, és ha a felhasználó hitelesítése vagy ellenőrzése sikeres volt, a felhasználó folytatja a feltételes hozzáférés folyamatát.

### <a name="terms-of-use"></a>Használati feltételek

A környezet bizonyos felhőalapú alkalmazásaihoz való hozzáféréshez a felhasználók beleegyeznek a Használati feltételek (használati feltételek) elfogadásával. Kövesse ezt a rövid útmutatót [a használati feltételek létrehozásához](require-tou.md).

## <a name="troubleshoot-conditional-access"></a>A feltételes hozzáférés hibaelhárítása

Ha a felhasználó egy feltételes hozzáférési szabályzattal kapcsolatos problémával rendelkezik, a hibaelhárításhoz Gyűjtse össze a következő információkat.

* Felhasználói elv neve

* Felhasználó megjelenítendő neve

* Operációs rendszer neve

* Időbélyegző (közelítő ok)

* Célalkalmazás

* Ügyfélalkalmazás típusa (böngésző kontra ügyfél)

* Korrelációs azonosító (ez egyedi a bejelentkezéshez)

Ha a felhasználó egy további részletekkel rendelkező üzenetet kapott, a legtöbb információt összegyűjtheti Önnek.

![Nem lehet beolvasni az alkalmazás hibaüzenetét](media/plan-conditional-access/cant-get-to-app.png)

Miután összegyűjtötte az adatokat, tekintse meg a következő forrásokat:

* [Bejelentkezési problémák a feltételes hozzáféréssel](troubleshoot-conditional-access.md) – a feltételes hozzáféréssel kapcsolatos váratlan bejelentkezések ismertetése a hibaüzenetek és az Azure ad bejelentkezési naplójának használatával.

* [A What-If eszköz használata](troubleshoot-conditional-access-what-if.md) – megismerheti, hogy egy adott házirend miért lett vagy nem lett alkalmazva a felhasználóra adott körülmények között, vagy ha egy házirendet ismert állapotban kellene alkalmazni.

## <a name="next-steps"></a>Következő lépések

[További információ a többtényezős hitelesítésről](../authentication/concept-mfa-howitworks.md)

[További információ az Identity Protectionről](../identity-protection/overview-identity-protection.md)

[Feltételes hozzáférési szabályzatok kezelése Microsoft Graph API-val](/graph/api/resources/conditionalaccesspolicy)
