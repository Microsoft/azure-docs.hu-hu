---
title: Azure Security Center biztonsági javaslatai az MFA-hoz
description: Ismerje meg, hogyan kényszerítheti ki az Azure-előfizetések többtényezős hitelesítését a Azure Security Center használatával
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631897"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>A többtényezős hitelesítés (MFA) kényszerítésének kezelése az előfizetéseken

Ha csak jelszavakat használ a felhasználók hitelesítéséhez, megnyit egy támadási vektort. A felhasználók gyakran gyenge jelszavakat használnak, vagy több szolgáltatáshoz használják őket. Ha az [MFA](https://www.microsoft.com/security/business/identity/mfa) engedélyezve van, a fiókok biztonságosabbak, és a felhasználók továbbra is elvégezhetik a hitelesítést az egyszeri bejelentkezéssel (SSO) rendelkező alkalmazásokkal.

Több módon is engedélyezheti az MFA használatát a Azure Active Directory (AD) felhasználók számára a szervezet tulajdonában lévő licencek alapján. Ez az oldal a Azure Security Center kontextusában részletezi az adatokat.


## <a name="mfa-and-security-center"></a>MFA és Security Center 

Security Center magas értéket helyez el az MFA-on. A biztonságos pontszámhoz legjobban hozzájáruló biztonsági szabályozás **lehetővé teszi az MFA** használatát. 

Az MFA engedélyezése vezérlőelemben szereplő javaslatok biztosítják az előfizetések felhasználói számára ajánlott eljárásoknak való megfelelést:

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjaiban

A többtényezős hitelesítés három módon engedélyezhető, és megfelel a két javaslatnak Security Center: biztonsági alapértékek, felhasználónkénti hozzárendelés, feltételes hozzáférés (CA) házirend. Ezeket a lehetőségeket az alábbiakban ismertetjük.

### <a name="free-option---security-defaults"></a>Ingyenes lehetőség – biztonsági alapértékek
Ha az Azure AD ingyenes kiadását használja, használja a [biztonsági alapértelmezéseket](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) a többtényezős hitelesítés engedélyezéséhez a bérlőn.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA Microsoft 365 Vállalati verzió, E3 és E5 ügyfelek számára
A Microsoft 365 használó ügyfelek **felhasználónkénti hozzárendelést** is használhatnak. Ebben az esetben az Azure AD MFA az összes felhasználó számára engedélyezve van vagy le van tiltva az összes bejelentkezési esemény esetében. A többtényezős hitelesítés nem engedélyezhető a felhasználók egy részhalmaza számára, vagy bizonyos helyzetekben, és a felügyelet az Office 365 portálon keresztül történik.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA prémium szintű Azure AD ügyfelek számára
A jobb felhasználói élmény érdekében frissítsen a prémium szintű Azure AD P1 vagy P2 szintre a **feltételes hozzáférés (CA) házirend** -beállításaihoz. A HITELESÍTÉSSZOLGÁLTATÓI házirend konfigurálásához [Azure Active Directory (ad) bérlői engedélyekre](../active-directory/roles/permissions-reference.md)van szükség.

A HITELESÍTÉSSZOLGÁLTATÓI házirendnek a következőket kell tennie:
- MFA betartatása
- a Microsoft Azure Management app ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) vagy az összes alkalmazás belefoglalása
- a Microsoft Azure felügyeleti alkalmazás AZONOSÍTÓjának kizárása

**Prémium szintű Azure ad P1** -ügyfelek az Azure ad CA-t használhatják a többtényezős hitelesítésre a felhasználók számára az üzleti igényeknek megfelelően bizonyos helyzetekben vagy eseményeknél. Egyéb licencek, amelyek tartalmazzák ezt a funkciót: Enterprise Mobility + Security E3, Microsoft 365 F1 és Microsoft 365 E3.

**Prémium szintű Azure ad P2** a legerősebb biztonsági funkciókat és továbbfejlesztett felhasználói élményt nyújt. Ez a licenc a prémium szintű Azure AD P1 funkcióinak [kockázatkezelési alapú feltételes hozzáférését](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) adja hozzá. A kockázatalapú HITELESÍTÉSSZOLGÁLTATÓ alkalmazkodik a felhasználói mintákhoz, és lekicsinyíti a többtényezős hitelesítési kéréseket. A funkciót tartalmazó egyéb licencek: Enterprise Mobility + Security E5 vagy Microsoft 365 E5.

További információt az [Azure feltételes hozzáférési dokumentációjában talál](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>A multi-Factor Authentication (MFA) engedélyezése nélküli fiókok azonosítása

A felhasználói fiókok listáját a Security Center javaslatok részletei lapon vagy az Azure Resource Graph használatával tekintheti meg az MFA engedélyezése nélkül.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Az Azure Portalban engedélyezett MFA nélküli fiókok megtekintése
A javaslat részletei lapon válasszon ki egy előfizetést a nem kifogástalan **erőforrások** listából, vagy válassza a **művelet elvégzése** lehetőséget, és megjelenik a lista.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Az Azure Resource Graph használatával engedélyezett, MFA nélküli fiókok megtekintése
A következő Azure Resource Graph-lekérdezéssel megtekintheti, hogy mely fiókok esetében nem engedélyezett az MFA használata. A lekérdezés az összes nem megfelelő erőforrást adja vissza – a "MFA engedélyezése az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokban". 

1. Nyissa meg az **Azure Resource Graph Explorert**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Az Azure Resource Graph Explorer * * ajánlási oldalának elindítása" :::

1. Adja meg a következő lekérdezést, és válassza a **lekérdezés futtatása** lehetőséget.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. A `additionalData` tulajdonság felfedi az MFA által kényszerített fiókokhoz tartozó fiókazonosító-azonosítók listáját. 

    > [!NOTE]
    > A fiókok neve helyett objektumazonosítókként jelennek meg a fiókok tulajdonosainak védelme érdekében.

> [!TIP]
> Alternatív megoldásként használhatja a Security Center REST API metódusának [értékelését – Get](/rest/api/securitycenter/assessments/get).


## <a name="faq---mfa-in-security-center"></a>Gyakori kérdések – MFA Security Center

- [Már használjuk a CA-szabályzatot az MFA betartatására. Miért továbbra is megkapjuk a Security Center javaslatokat?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Egy harmadik féltől származó MFA-eszközt használunk az MFA betartatására. Miért továbbra is megkapjuk a Security Center javaslatokat?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Miért Security Center a felhasználói fiókokat az előfizetésre vonatkozó engedély nélkül, az "MFA megkövetelése" lehetőséggel?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Az MFA-t a PIM-mel érvényesítjük. Miért jelenik meg a PIM-fiókok nem megfelelőként?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Felvehetem vagy Elvetem néhány fiókot?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Vannak korlátozások a Security Center identitás-és hozzáférés-védelmére?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Már használjuk a CA-szabályzatot az MFA betartatására. Miért továbbra is megkapjuk a Security Center javaslatokat?
Ha meg szeretné vizsgálni, hogy a javaslatok miért jönnek létre, ellenőrizze a következő konfigurációs beállításokat az MFA HITELESÍTÉSSZOLGÁLTATÓI házirendjében:

- A fiókokat az MFA HITELESÍTÉSSZOLGÁLTATÓI szabályzatának **felhasználók** szakaszában (vagy a **csoportok** szakaszban található csoportok egyikén) is megadta.
- Az Azure Management app ID-t (797f4846-ba00-4fd7-ba43-dac1f8f63013) vagy az összes alkalmazást az MFA HITELESÍTÉSSZOLGÁLTATÓI szabályzatának **alkalmazások** szakasza tartalmazza.
- Az Azure felügyeleti alkalmazás AZONOSÍTÓját nem zárja ki az MFA HITELESÍTÉSSZOLGÁLTATÓI házirendjének **alkalmazások** szakasza.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Egy harmadik féltől származó MFA-eszközt használunk az MFA betartatására. Miért továbbra is megkapjuk a Security Center javaslatokat?
Security Center MFA-javaslatai nem támogatják a harmadik féltől származó MFA-eszközöket (például DUO).

Ha a javaslatok a szervezet szempontjából lényegtelenek, érdemes lehet azokat "enyhített"ként megjelölni, ahogy azt a [biztonságos pontszám alól az erőforrások és javaslatok](exempt-resource.md)kizárása című témakörben leírtak szerint. Emellett [letilthat egy javaslatot](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)is.

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Miért Security Center a felhasználói fiókokat az előfizetésre vonatkozó engedély nélkül, az "MFA megkövetelése" lehetőséggel?
Security Center MFA-javaslatai az [Azure RBAC](../role-based-access-control/role-definitions-list.md) szerepköreire és a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md) szerepkörre vonatkoznak. Ellenőrizze, hogy egyik fiók se rendelkezzen ilyen szerepkörökkel.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Az MFA-t a PIM-mel érvényesítjük. Miért jelenik meg a PIM-fiókok nem megfelelőként?
Security Center MFA-javaslatai jelenleg nem támogatják a PIM-fiókokat. A Felhasználók/Csoport szakaszban adhatja ezeket a fiókokat feltételes hozzáférési szabályzathoz.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Felvehetem vagy Elvetem néhány fiókot?
Az MFA-t nem használó fiókok mentesítésének lehetősége jelenleg nem támogatott.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Vannak korlátozások a Security Center identitás-és hozzáférés-védelmére?
A Security Center identitás-és hozzáférés-védelmének bizonyos korlátai vannak:

- Az Identity javaslatok nem érhetők el több mint 600 fiókkal rendelkező előfizetésekhez. Ilyen esetekben ezek a javaslatok a "nem elérhető értékelések" területen jelennek meg.
- Az identitásra vonatkozó javaslatok nem érhetők el a Cloud Solution Provider (CSP) partner rendszergazdai ügynökei számára.
- Az identitással kapcsolatos javaslatok nem azonosítják azokat a fiókokat, amelyeket a rendszer az emelt szintű Identity Management (PIM) rendszerrel felügyel. Ha PIM-eszközt használ, pontatlan eredmények jelenhetnek meg a **hozzáférés és engedélyek kezelése** vezérlőelemben.


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikket:

- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)