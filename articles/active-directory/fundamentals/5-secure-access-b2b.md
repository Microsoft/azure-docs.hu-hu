---
title: Áttérés a szabályozott együttműködésre Azure Active Directory B2B együttműködéssel
description: A szabályozott együttműködésre való áttérés az Azure ad B2B együttműködéssel.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648587"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Áttérés a szabályozott együttműködésre Azure Active Directory B2B együttműködéssel 

A felügyelet alatt álló együttműködés kulcsfontosságú az erőforrásaihoz való külső hozzáférés biztosításához. Mielőtt elkezdené ezt a cikket, győződjön meg róla, hogy rendelkezik a következővel:

* [A biztonsági helyzet megállapítása](1-secure-access-posture.md)

* [A jelenlegi állapot felderítve](2-secure-access-current-state.md)

* [Biztonsági terv létrehozva](3-secure-access-plan.md)

* [Megértette, hogy a csoportok és a biztonság hogyan működik együtt](4-secure-access-groups.md)

Ha elvégezte ezeket a dolgokat, készen áll a szabályozott együttműködésre való áttérésre. Ebből a cikkből megtudhatja, hogyan helyezheti át az összes külső együttműködést [Azure Active Directory B2B-együttműködésbe](../external-identities/what-is-b2b.md) (Azure ad B2B). Az Azure ad B2B az [Azure ad külső identitások](../external-identities/compare-with-b2c.md)egyik funkciója.

## <a name="control-who-your-organization-collaborates-with"></a>Annak szabályozása, hogy a szervezet hogyan működjön együtt

El kell döntenie, hogy szeretné-e korlátozni, hogy a felhasználók milyen szervezeteket tudnak együttműködni, és a szervezeten belül kik is indíthatnak együttműködést. A legtöbb szervezet azt a megközelítést alkalmazza, amellyel az üzleti egységek dönthetnek arról, hogy kivel működnek együtt, és szükség szerint delegálják a jóváhagyást és a felügyeletet. Bizonyos kormányzati, oktatási és pénzügyi szolgáltatói szervezetek például nem engedélyezik a nyílt együttműködést. Előfordulhat, hogy az Azure AD funkcióit a hatókör együttműködésére szeretné használni, ahogy azt a szakasz további részében tárgyaljuk.

### <a name="determine-collaboration-partners"></a>Együttműködési partnerek meghatározása

Először is győződjön meg arról, hogy dokumentálta azokat a szervezeteket, amelyekkel jelenleg együttműködik, valamint a szervezetek felhasználói számára a tartományokat. Egy együttműködési partner több tartománnyal is rendelkezhet. Előfordulhat például, hogy egy partner több, különálló tartománnyal rendelkező üzleti egységgel rendelkezik.

Ezután állapítsa meg, hogy szeretné-e engedélyezni a jövőbeli együttműködést a következővel 

* bármely tartomány (a legtöbb befogadó)

* minden tartomány, kivéve a explicit módon megtagadva 

* csak meghatározott tartományok (a legszigorúbb)

> [!NOTE]
> Minél szigorúbb együttműködési beállítások vannak érvényben, annál valószínűbb, hogy a felhasználók a jóváhagyott együttműködési keretrendszeren kívül fognak esni. Javasoljuk, hogy a lehető legszélesebb körű együttműködés lehetővé tegye a biztonsági igényeket, és szorosan áttekintse az együttműködést, nem pedig túlságosan korlátozó. 

Azt is vegye figyelembe, hogy egyetlen tartományra való korlátozás véletlenül megakadályozhatja a jogosult együttműködést a szervezetekkel, amelyek más nem kapcsolódó tartományokkal rendelkeznek a felhasználók számára. Ha például egy szervezet contoso céggel folytatja a munkát, akkor a contoso első kapcsolatfelvételi pontja az egyik USA-beli alkalmazottja, akinek e-mail-címe ". com" tartománnyal rendelkezik. Ha azonban csak a ". com" tartományt engedélyezi, előfordulhat, hogy véletlenül kihagyja a ". ca" tartományba tartozó kanadai alkalmazottakat. 

Vannak olyan esetek, amikor csak bizonyos együttműködési partnereket szeretne engedélyezni. Előfordulhat például, hogy egy egyetemi rendszer csak azt szeretné, hogy a saját oktatók hozzáférhessenek egy erőforrás-bérlőhöz. Vagy egy konglomerátum csak bizonyos leányvállalatok számára engedélyezheti, hogy működjenek együtt a szükséges keretrendszerrel.

#### <a name="using-allow-and-deny-lists"></a>Engedélyezési és megtagadási listák használata

Az engedélyezési lista vagy a megtagadási lista használatával [korlátozhatja a vállalatközi felhasználóknak szóló meghívókat](../external-identities/allow-deny-list.md) adott szervezeteknél. Egyszerre csak engedélyezési vagy megtagadási listát használhat.

* Az [engedélyezési lista](../external-identities/allow-deny-list.md) csak a felsorolt tartományokra korlátozza az együttműködést. az összes többi tartomány hatékonyan szerepel a megtagadási listán.

* Egy [megtagadási lista](../external-identities/allow-deny-list.md) lehetővé teszi, hogy a megtagadási listán nem szereplő tartományokkal működjenek együtt.

> [!IMPORTANT]
> Ezek a lista nem vonatkozik azokra a felhasználókra, akik már szerepelnek a címtárban. Nem vonatkoznak a OneDrive for Business és a SharePoint szolgáltatásra is, amelyek elkülönítik a megtagadási listát.

Egyes szervezetek a megtagadási listához a felügyelt biztonsági szolgáltató által biztosított ismert "rossz Actor" tartományokat használják. Ha például a szervezet a contoso vállalatot használja, és egy. com tartományt használ, akkor előfordulhat, hogy egy nem kapcsolódó szervezet, amely a contoso. org tartományt használta, és egy adathalászat-támadást kísérelt meg megszemélyesíteni a contoso alkalmazottai számára. 

## <a name="control-how-external-users-gain-access"></a>Annak szabályozása, hogy a külső felhasználók hozzáférjenek

Az Azure AD B2B-vel többféleképpen lehet együttműködni külső partnerekkel. Az együttműködés megkezdéséhez meghívja a partnert, vagy egyéb módon engedélyezheti az erőforrásai elérését. A felhasználók a következőre reagálva érhetik el:

* Egy [e-mailben küldött meghívás](../external-identities/redemption-experience.md)beváltása vagy egy erőforrás [megosztására szolgáló közvetlen hivatkozás](../external-identities/redemption-experience.md) .

* Hozzáférés kérése [egy Ön által létrehozott alkalmazáson keresztül](../external-identities/self-service-sign-up-overview.md)

* Hozzáférés kérése a [saját hozzáférési](../governance/entitlement-management-request-access.md) portálon keresztül

Amikor engedélyezi az Azure AD B2B-t, lehetővé teszi a vendég felhasználók meghívását a közvetlen hivatkozások és az e-mailes meghívások alapértelmezés szerint. A meghívások e-mailben és önkiszolgáló portálon keresztül jelenleg előzetes verzióban érhetők el, és a külső identitásokon belül engedélyezni kell őket | Külső együttműködési beállítások az Azure AD-portálon.

### <a name="control-who-can-invite-guest-users"></a>Annak szabályozása, hogy ki hívhat meg vendég felhasználókat

Határozza meg, hogy kik hívhatják meg a vendég felhasználókat az erőforrások eléréséhez.

* A legszigorúbb beállítás az, hogy csak a rendszergazdák és a felhasználók a vendég [meghívó szerepkört](../external-identities/delegate-invitations.md) kapják meg a vendégek meghívásához.

* Ha a biztonsági követelmények lehetővé teszik, javasoljuk, hogy minden felhasználó számára lehetővé váljon a userType a vendégek meghívása.

* Állapítsa meg, hogy szeretné-e, hogy a felhasználók userType legyenek, ez az alapértelmezett fióktípus az Azure AD B2B-felhasználók számára, hogy más vendégek is meghívhatók legyenek. 

![Képernyőkép a vendég Meghívási beállításairól.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>További információk gyűjtése a külső felhasználókról

Ha az Azure AD-jogosultságok felügyeletét használja, a külső felhasználókra vonatkozó kérdéseket is megadhat. A kérdések ezután megjelennek a jóváhagyók számára, hogy döntéseket hozzanak. Az egyes [hozzáférési csomagokra vonatkozó szabályzatok](../governance/entitlement-management-access-package-approval-policy.md) különböző csoportjait konfigurálhatja, hogy a jóváhagyók rendelkezzenek a jóváhagyáshoz szükséges adatokkal. Ha például az egyik hozzáférési csomag a szállítói hozzáférésre van szánva, akkor a kérelmező kérheti a szállítói szerződésük számát. Egy másik, szállítók számára szánt hozzáférési csomag kérheti a származási országát.

Ha önkiszolgáló portált használ, az [API-összekötők](../external-identities/api-connectors-overview.md) segítségével további attribútumokat gyűjthet a felhasználókról a regisztráció során. Ezeket az attribútumokat később is használhatja a hozzáférés hozzárendeléséhez. Ha például a regisztrációs folyamat során begyűjti a szállítói AZONOSÍTÓját, akkor az adott attribútum használatával dinamikusan rendelheti hozzá őket egy csoporthoz vagy hozzáférési csomaghoz az adott szolgáltatóhoz. Létrehozhat egyéni attribútumokat a Azure Portalban, és használhatja azokat az önkiszolgáló bejelentkezési felhasználói folyamatokban. Ezeket az attribútumokat a [Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md)használatával is elolvashatja és elvégezheti. 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Az Azure AD-felhasználókra való meghívás visszaváltásának hibája

Az Azure AD-t használó együttműködési partnertől érkező vendég felhasználókat három példányban lehet meghívni.

* Engedélyezési lista használata esetén az engedélyezési lista nem tartalmazza a felhasználó tartományát.

* Ha az együttműködési partner otthoni bérlője bérlői korlátozásokkal rendelkezik, amelyek megakadályozzák a külső felhasználókkal való együttműködést.

* Ha a felhasználó nem tagja a partner Azure AD-bérlőnek. Például vannak olyan felhasználók a contoso.com, akik csak Active Directoryban (vagy egy másik helyszíni identitásszolgáltató) vannak, csak az e-mailes egyszeri jelszavas folyamaton keresztül tudják beváltani a meghívókat. További információ: [meghívás beváltási folyamata](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>A külső felhasználók által elérhető hozzáférés szabályozása

A legtöbb szervezet nem monolit. Ez azt eredményezi, hogy vannak olyan erőforrások, amelyeket érdemes megosztani a külső felhasználókkal, és néhány nem szeretné, hogy a külső felhasználók hozzáférjenek. Ezért meg kell határoznia, hogy milyen külső felhasználók férhessenek hozzá. Az adott erőforrásokhoz [való hozzáférés szabályozásához érdemes lehet jogosultság-kezelési és hozzáférési csomagokat](6-secure-access-entitlement-managment.md) használni.

Alapértelmezés szerint a vendég felhasználói láthatják a bérlői tagokkal és más partnerekkel kapcsolatos információkat és attribútumokat, beleértve a csoporttagságok adatait is. Vegye figyelembe, hogy a biztonsági követelmények a külső felhasználók ezen információkhoz való hozzáférésének korlátozására szólítanak fel.

![Képernyőkép a külső együttműködési beállítások konfigurálásáról.](media/secure-external-access/5-external-collaboration-settings.png)

A vendég felhasználói számára a következő korlátozásokat javasoljuk.

* **A vendég hozzáférésének korlátozása a tallózási csoportokhoz és egyéb tulajdonságokhoz a címtárban**

   * A külső együttműködési beállítások használatával korlátozhatja a vendég azon csoportok olvasását, amelyek nem tagjai a csoportnak.

* **Csak az alkalmazotti alkalmazásokhoz való hozzáférés letiltása**.

   * Hozzon létre egy feltételes hozzáférési szabályzatot, amely letiltja az olyan Azure AD-integrált alkalmazásokhoz való hozzáférést, amelyek csak a nem vendég felhasználók számára megfelelőek. 

* **A Azure Portal elérésének letiltása. Ritkán szükséges kivételeket hozhat**. 

   * Hozzon létre egy feltételes hozzáférési szabályzatot, amely tartalmazza az összes vendég és a külső felhasználót, majd [a hozzáférés letiltásához alkalmazza a szabályzatot](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>A már nem szükséges felhasználók eltávolítása

Az aktuális hozzáférés kiértékelése, hogy [áttekintse és eltávolítsa azokat a felhasználókat, akik már nem férnek hozzá](../governance/access-reviews-external-users.md). Vegyen részt a bérlőn lévő külső felhasználók és a tagok fiókjaival. 

Néhány szervezet olyan külső felhasználókat adott hozzá, mint a szállítók, a partnerek és a vállalkozók tagként. Előfordulhat, hogy ezek a tagok egy adott attribútummal vagy a-től kezdődő felhasználónevekkal rendelkeznek, például:

* v – szállítók számára

* p – partnerek számára

* c – alvállalkozók számára

Értékelje ki a tagok fiókjaival rendelkező külső felhasználókat, hogy továbbra is szükségük van-e a hozzáférésre. Ha igen, a következő szakaszban leírtak szerint váltson át ezeket a felhasználókat az Azure AD B2B-be.

A jogosultságok felügyeletén vagy az Azure AD B2B-ben nem meghívott vendég felhasználókkal is rendelkezhet

A felhasználók megkereséséhez a következőket teheti:

* [A jogosultságok kezelésével nem meghívott vendég felhasználók keresése](../governance/access-reviews-external-users.md).

   * Egy [minta PowerShell-parancsfájlt](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) biztosítunk.

Átválthatja ezeket a felhasználókat az Azure AD B2B-felhasználók számára a következő szakaszban leírtak szerint.

## <a name="transition-your-current-external-users-to-b2b"></a>A jelenlegi külső felhasználók átváltása a B2B-re

Ha még nem használta az Azure AD B2B-et, akkor valószínűleg nem alkalmazott felhasználókkal rendelkezik a bérlőben. Javasoljuk, hogy ezeket a fiókokat az Azure AD B2B külső felhasználói fiókjaira váltsa át, majd módosítsa a UserType a vendégre. Így kihasználhatja az Azure AD számos módszerének előnyeit, és a Microsoft 365 lehetővé teszi a külső felhasználók eltérő kezelését. Ilyenek például a következők:

* A feltételes hozzáférési szabályzatokban egyszerűen beleértve vagy kizárhatja a vendég felhasználókat

* Hozzáférés-csomagok és hozzáférési felülvizsgálatok egyszerű befoglalása vagy kizárása a vendégek számára

* A csapatokhoz, a SharePointhoz és más erőforrásokhoz való külső hozzáférések egyszerűen is meghozhatók.

Ha ezeket a belső felhasználókat a jelenlegi hozzáférésük, UPN-címeik és csoporttagságok fenntartása mellett szeretné áttérni, tekintse meg a [külső felhasználók meghívása vállalatközi együttműködésre](../external-identities/invite-internal-users.md)című témakört

## <a name="decommission-undesired-collaboration-methods"></a>Nem kívánt együttműködési módszerek leszerelése

A szabályozott együttműködésre való áttérés befejezéséhez el kell végeznie a nem kívánt együttműködési módszerek leszerelését. A leszerelést az Ön által az együttműködésre és a biztonsági helyzetre vonatkozó ellenőrzési mérték alapján kell kifejteni. További információ a végfelhasználói felügyeletről: [biztonsági helyzet meghatározása külső hozzáféréshez](1-secure-access-posture.md).

Az alábbi együttműködési járműveket érdemes lehet kiértékelni.

### <a name="direct-invitation-through-microsoft-teams"></a>Közvetlen meghívás a Microsoft csapatainak használatával

Alapértelmezés szerint a csapatok lehetővé teszik a külső hozzáférést, ami azt jelenti, hogy a szervezet képes kommunikálni az összes külső tartománnyal. Ha csak a csapatok számára szeretne korlátozni vagy engedélyezni bizonyos tartományokat, ezt a [csapatok felügyeleti portálján](https://admin.teams.microsoft.com/company-wide-settings/external-communications)teheti meg. 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Közvetlen megosztás a SharePoint és a OneDrive használatával

A SharePointon keresztüli közvetlen megosztás és a OneDrive a jogosultsági felügyeleti folyamaton kívül is hozzáadhat felhasználókat. A konfigurációk részletes ismertetését lásd: a [Microsoft Teams, a SharePoint és a OneDrive hozzáférésének kezelése a vállalat számára](9-secure-access-teams-sharepoint.md) , ha szükséges, le is [tilthatja a felhasználó személyes OneDrive használatát](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) .

### <a name="sending-documents-through-email"></a>Dokumentumok küldése e-mailben

A felhasználók e-mailben küldhetnek dokumentumokat a külső felhasználóknak. Vegye figyelembe, hogyan szeretné korlátozni és titkosítani a dokumentumokhoz való hozzáférést az érzékenységi címkék használatával. További információ: hozzáférés kezelése érzékenységi címkékkel.

### <a name="unsanctioned-collaboration-tools"></a>Nem engedélyezett csoportmunka-eszközök

Az együttműködési eszközök tájképe óriási. A felhasználók valószínűleg a hivatalos feladataik, például a Google docs, a DropBox, a Slack vagy a zoom platformon kívül is használják a felhasználókat. Le lehet tiltani az ilyen eszközök használatát a vállalati hálózatról a tűzfal szintjén, valamint a szervezet által felügyelt eszközökre vonatkozó mobileszköz-felügyeletet. Ez azonban letiltja a platformok összes engedélyezett példányát is, és nem blokkolja a hozzáférést a nem felügyelt eszközökről. Blokkolja azokat a platformokat, amelyeket nem kíván igény szerint használni, és üzleti szabályzatokat hozhat létre a használni kívánt platformok nem engedélyezett használatának mellőzése érdekében. 

A nem engedélyezett alkalmazások kezelésével kapcsolatos további információkért lásd:

* [Csatlakoztatott alkalmazások szabályozása](/cloud-app-security/governance-actions)

* [Az alkalmazások szankcionálása és visszavonása.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A külső hozzáférés biztonsági helyzetének meghatározása](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure ad B2B](5-secure-access-b2b.md) -re (itt van.)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)