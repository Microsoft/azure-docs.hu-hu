---
title: B2B-együttműködés – gyakori kérdések – Azure Active Directory | Microsoft Docs
description: Válaszok az Azure Active Directory B2B együttműködéssel kapcsolatos gyakori kérdésekre.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7879b233bf94442de2cad83de8adfe54b6b81e0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365514"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B-együttműködés – gyakori kérdések

Az Azure Active Directory (Azure AD) vállalatközi (B2B) együttműködéssel kapcsolatos gyakori kérdések (GYIK) rendszeres időközönként frissülnek, hogy új témaköröket tartalmazzanak.

> [!IMPORTANT]
> - **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.
> - **Október 2021-től kezdve** a Microsoft nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Testre szabható a bejelentkezési oldal, így a B2B-együttműködéshez tartozó vendég felhasználói számára még intuitívabb lehet?
Feltétlenül! A [szolgáltatásról a blogbejegyzésben](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)talál további információt. A szervezet bejelentkezési oldalának testreszabásával kapcsolatos további információkért lásd: [vállalati arculat hozzáadása a bejelentkezéshez és a hozzáférési panel oldalaihoz](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>A B2B-együttműködés felhasználói hozzáférhetnek a SharePoint Online-hoz és a OneDrive-hoz?
Igen. A meglévő vendég felhasználók a SharePoint Online-ban való keresésének lehetősége azonban alapértelmezés szerint **ki van kapcsolva** a People Picker használatával. Ha be szeretné kapcsolni a meglévő vendég felhasználók keresésének lehetőségét, állítsa be a **ShowPeoplePickerSuggestionsForGuestUsers** beállítást **a** be értékre. Ezt a beállítást a bérlő vagy a webhelycsoport szintjén is bekapcsolhatja. Ezt a beállítást a Set-SPOTenant és Set-SPOSite parancsmagok segítségével módosíthatja. Ezekkel a parancsmagokkal a tagok a címtárban lévő összes meglévő felhasználónál kereshetnek. A bérlői hatókör változásai nem érintik a már kiépített SharePoint Online-helyeket.

### <a name="is-the-csv-upload-feature-still-supported"></a>A CSV-feltöltési funkció továbbra is támogatott?
Igen. A. csv fájl feltöltési funkciójával kapcsolatos további információkért tekintse meg [ezt a PowerShell-mintát](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hogyan szabható testre a meghívói e-mailek?
A [meghívót a B2B Meghívási API](customize-invitation-api.md)-k használatával szinte mindenre testreszabhatja.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Alaphelyzetbe állíthatja a vendég felhasználói a multi-Factor Authentication módszert?
Igen. A vendég felhasználók a többtényezős hitelesítési módszert ugyanúgy állíthatják vissza, mint a normál felhasználók.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Melyik szervezet felelős a multi-Factor Authentication licencek esetében?
A meghívó szervezet többtényezős hitelesítést hajt végre. A meghívó szervezetnek meg kell győződnie arról, hogy a szervezetnek van-e elegendő licence a többtényezős hitelesítést használó B2B-felhasználók számára.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Mi a teendő, ha a partner szervezetnek már van beállítva a többtényezős hitelesítés? Megbízhat a multi-Factor Authentication szolgáltatásban, és nem használhatjuk a saját multi-Factor Authentication szolgáltatást?
Ez a funkció jelenleg nem támogatott. Ha a szervezet erőforrásaihoz való hozzáféréshez többtényezős hitelesítés szükséges, a partner szervezetnek regisztrálnia kell a többtényezős hitelesítést a (hívogató) szervezeten belül.

### <a name="how-can-i-use-delayed-invitations"></a>Hogyan használhatom a késleltetett meghívókat?
Előfordulhat, hogy egy szervezet VÁLLALATKÖZI együttműködéssel rendelkező felhasználókat kíván hozzáadni, szükség szerint kiépíti őket az alkalmazásokhoz, majd elküldi a meghívókat. A B2B Collaboration Meghívási API segítségével testre szabhatja a bevezetési munkafolyamatot.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Elérhetővé tehetem vendég felhasználókat az Exchange globális címlistában?
Igen. A vendég objektumok alapértelmezés szerint nem láthatók a szervezet globális címlistában (GAL), de a Azure Active Directory PowerShell használatával láthatóvá teheti őket. Lásd: [a vendég objektumokat láthatóvá tehetem a globális címlistában?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Használhatok-e egy vendég felhasználót korlátozott rendszergazdaként?
Természetesen. További információ: [vendég felhasználók hozzáadása szerepkörhöz](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Az Azure AD B2B együttműködés lehetővé teszi a B2B-felhasználók számára a Azure Portal elérését?
Ha egy felhasználó a korlátozott rendszergazda szerepkörhöz van rendelve, a B2B-együttműködés felhasználói nem igényelnek hozzáférést a Azure Portalhoz. Azonban a korlátozott rendszergazda szerepkört hozzárendelő B2B-együttműködés felhasználói hozzáférhetnek a portálhoz. Továbbá, ha egy vendég felhasználó, aki nincs hozzárendelve ezen rendszergazdai szerepkörhöz, hozzáfér a portálhoz, előfordulhat, hogy a felhasználó hozzáférhet a felhasználói élmény bizonyos részeihez. A vendég felhasználói szerepkörnek van néhány engedélye a címtárban.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Letilthatom a Azure Portal a vendég felhasználóinak hozzáférését?

Igen! Létrehozhat egy feltételes hozzáférési szabályzatot, amely letiltja az összes vendég és külső felhasználó hozzáférését a Azure Portalhoz. Ha beállítja ezt a házirendet, ügyeljen arra, hogy elkerülje a tagok és a rendszergazdák számára a hozzáférés véletlen blokkolását.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) biztonsági rendszergazdaként vagy feltételes hozzáférésű rendszergazdaként.
2. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget. 
3. A **kezelés** területen válassza a **Biztonság** elemet.
4. A **védelem** területen válassza a **feltételes hozzáférés** lehetőséget. Válassza az **új szabályzat** lehetőséget.
5. Az **új** lap **név** szövegmezőbe írja be a szabályzat nevét (például "a vendégek blokkolják a portál elérését").
6. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
7. A **beágyazás** lapon válassza a **felhasználók és csoportok kiválasztása** lehetőséget, majd válassza az **összes vendég és külső felhasználó (előzetes verzió)** lehetőséget.
9. Válassza a **Kész** lehetőséget.
10. Az **új** lap **hozzárendelések** területén válassza a **Cloud apps vagy műveletek** elemet.
11. A **Cloud apps vagy műveletek** lapon válassza az **alkalmazások kiválasztása**, majd a **kiválasztás** lehetőséget.
12. A **Kiválasztás** lapon válassza a **Microsoft Azure-felügyelet** elemet, majd a **Kiválaszt** lehetőséget.
13. A **Cloud apps vagy műveletek** lapon válassza a **kész** lehetőséget.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Támogatja az Azure AD B2B együttműködés a többtényezős hitelesítést és a fogyasztói e-mail-fiókokat?
Igen. A multi-Factor Authentication és a fogyasztói e-mail-fiókok egyaránt támogatottak az Azure AD B2B együttműködésben.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Támogatja az Azure AD B2B együttműködési felhasználók jelszavának visszaállítását?
Ha az Azure AD-bérlő a felhasználó saját könyvtára, [alaphelyzetbe állíthatja a felhasználó jelszavát](../fundamentals/active-directory-users-reset-password-azure-portal.md) a Azure Portal. Azonban nem lehet közvetlenül alaphelyzetbe állítani egy olyan vendég felhasználó jelszavát, aki egy másik Azure AD-címtár vagy külső identitás-szolgáltató által felügyelt fiókkal jelentkezik be. A jelszót csak a vendég felhasználó vagy a felhasználó kezdőkönyvtárának rendszergazdája állíthatja alaphelyzetbe. Íme néhány példa arra, hogyan működik a jelszó-visszaállítás a vendég felhasználói számára:
 
* Azok a vendég felhasználók, akik Microsoft-fiókkal jelentkeznek be (például guestuser@live.com ), visszaállíthatják saját jelszavukat Microsoft-fiók önkiszolgáló jelszó-visszaállítás (SSPR) használatával. Lásd: [a Microsoft-fiók jelszavának alaphelyzetbe állítása](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Azok a vendégek, akik Google-fiókkal vagy más külső identitás-szolgáltatóval jelentkeznek be, visszaállíthatják saját jelszavukat a személyazonosság-szolgáltató SSPR metódusának használatával. A Google-fiókkal rendelkező vendég felhasználó például a guestuser@gmail.com [jelszó módosítása vagy alaphelyzetbe állítása](https://support.google.com/accounts/answer/41078)című részben leírtak szerint állíthatja vissza a jelszavát.
* Ha az identitás bérlője egy igény szerinti (JIT) vagy "vírusos" bérlő (azaz egy különálló, nem felügyelt Azure-bérlő), csak a vendég felhasználó állíthatja alaphelyzetbe a jelszavát. Néha a szervezet [átveszi az olyan vírusos bérlők felügyeletét](../enterprise-users/domains-admin-takeover.md) , amelyek akkor jönnek létre, amikor az alkalmazottak a munkahelyi e-mail-címeiket használják a szolgáltatások regisztrálására. Miután a szervezet átvesz egy vírusos bérlőt, csak az adott szervezet rendszergazdája állíthatja alaphelyzetbe a felhasználó jelszavát, vagy engedélyezheti a SSPR. Ha szükséges, a meghívó szervezetnél távolítsa el a vendég felhasználói fiókot a címtárból, és küldje el újra a meghívót.

* Ha a vendég felhasználói kezdőkönyvtár az Azure AD-bérlő, alaphelyzetbe állíthatja a felhasználó jelszavát. Előfordulhat például, hogy létrehozott egy felhasználót, vagy szinkronizált egy felhasználót a helyszíni Active Directory, és beállította a UserType a vendégnek. Mivel ez a felhasználó a címtárban található, alaphelyzetbe állíthatja a jelszót a Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>A Microsoft Dynamics 365 online támogatást biztosít az Azure AD B2B együttműködéshez?
Igen, a Dynamics 365 (online) támogatja az Azure AD B2B-együttműködést. További információ: a Dynamics 365 cikke a [felhasználók meghívása az Azure ad B2B együttműködéssel](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Milyen élettartammal rendelkezik egy újonnan létrehozott B2B csoportmunka-felhasználó kezdeti jelszava?
Az Azure AD egy rögzített karakter-, jelszó-és fiókzárolási követelményekkel rendelkezik, amelyek egyformán érvényesek az összes Azure AD Cloud felhasználói fiókra. A felhőalapú felhasználói fiókok olyan fiókok, amelyek nem összevontak más identitás-szolgáltatóval, például 
* Microsoft-fiók
* Facebook
* Active Directory összevonási szolgáltatások
* Egy másik felhőalapú bérlő (B2B-együttműködéshez)

Összevont fiókok esetén a jelszóházirend a helyszíni bérletben alkalmazott házirendtől és a felhasználó Microsoft-fiók beállításaitól függ.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Előfordulhat, hogy a szervezet a bérlői felhasználók és a vendég felhasználók számára különböző tapasztalatokat kíván használni az alkalmazásaikban. Van erre vonatkozó általános útmutató? A megfelelő modellt kell használni a személyazonosság-szolgáltató számára?
A vendég felhasználó bármilyen identitás-szolgáltatót használhat a hitelesítéshez. További információ: [B2B csoportmunka-felhasználó tulajdonságai](user-properties.md). A felhasználói élmény megállapításához használja a **UserType** tulajdonságot. A **UserType** jogcím jelenleg nem szerepel a jogkivonatban. Az alkalmazásoknak a Microsoft Graph API-t kell használniuk a felhasználó címtárának lekérdezéséhez és a UserType lekéréséhez.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Hol találhatok egy B2B együttműködési Közösséget a megoldások megosztására és ötletek beküldésére?
Folyamatosan figyelünk visszajelzést a B2B-együttműködés javítására. Ossza meg a felhasználói forgatókönyveket, az ajánlott eljárásokat és az Azure AD B2B-együttműködéshez hasonló információkat. Csatlakozzon a [Microsoft technikai közösségének](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)vitafórumához.
 
Meghívjuk Önt arra is, hogy küldje el ötleteit, és szavazzon a jövőbeli funkciókra a [B2B együttműködési ötleteket](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)illetően.

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Küldhetünk automatikusan beváltott meghívót, hogy a felhasználó csak "készen álljon"? Vagy a felhasználónak mindig a beváltási URL-címre kell kattintania?
A partner szervezet más felhasználóit a felhasználói felület, a PowerShell-parancsfájlok vagy az API-k használatával hívhatja meg. Ezután elküldheti a vendég felhasználónak egy megosztott alkalmazásra mutató közvetlen hivatkozást. A legtöbb esetben már nem szükséges megnyitnia az e-mailes meghívót, és kattintson a beváltási URL-címre. Lásd: [Azure Active Directory B2B együttműködés meghívásának beváltása](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hogyan működik a VÁLLALATKÖZI együttműködés, ha a meghívott partner összevonást használ a saját helyszíni hitelesítés hozzáadásához?
Ha a partner rendelkezik a helyszíni hitelesítési infrastruktúrához összevont Azure AD-Bérlővel, a helyszíni egyszeri bejelentkezés (SSO) automatikusan elérhető. Ha a partner nem rendelkezik Azure AD-Bérlővel, a rendszer létrehoz egy Azure AD-fiókot az új felhasználók számára. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azt hittem, hogy az Azure AD B2B nem fogadta el a gmail.com és a outlook.com e-mail-címeit, és ezt a B2C-t használták ilyen típusú fiókokhoz?
A B2B és a vállalat – fogyasztó (B2C) együttműködés közötti különbségeket a rendszer az identitások támogatásának szempontjából is eltávolítja. A használt identitás nem jó ok a B2B vagy a B2C használatával való választásra. Az együttműködési lehetőség kiválasztásával kapcsolatos információkért lásd: [vállalatközi együttműködés és B2C Összevetése Azure Active Directoryban](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Meghívható Azure AD B2C helyi fiók egy Azure AD-bérlőnek a B2B-együttműködéshez?
Nem. Azure AD B2C helyi fiók csak a Azure AD B2C bérlőbe való bejelentkezéshez használható. A fiók nem használható az Azure AD-bérlőbe való bejelentkezéshez. Egy Azure AD B2C helyi fiók meghívása egy Azure AD-bérlőhöz VÁLLALATKÖZI együttműködéshez nem támogatott.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Milyen alkalmazások és szolgáltatások támogatják az Azure B2B Guest Users szolgáltatást?
Minden Azure AD-integrált alkalmazás támogatja az Azure B2B vendég felhasználókat, de a vendég felhasználói hitelesítéshez bérlőként beállított végpontot kell használniuk. Előfordulhat, hogy testre kell [szabnia a jogcímeket](claims-mapping.md) az SAML-jogkivonatban, amely akkor jön ki, amikor egy vendég felhasználó hitelesíti az alkalmazást. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>A többtényezős hitelesítés a VÁLLALATKÖZI vendég felhasználói számára is kikényszeríthető, ha a partnereink nem rendelkeznek többtényezős hitelesítéssel?
Igen. További információ: [feltételes hozzáférés vállalatközi együttműködéssel rendelkező felhasználók](conditional-access.md)számára.

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>A SharePointban meghatározhat egy "engedélyezés" vagy "megtagadás" listát a külső felhasználók számára. Ezt megtehetjük az Azure-ban?
Igen. Az Azure AD B2B együttműködés támogatja az engedélyezési és a letiltási listát. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Milyen licencekre van szükségünk az Azure AD B2B használatához?
További információ arról, hogy a szervezetnek milyen licenceket kell használnia az Azure AD B2B használatához: [külső identitások díjszabása](external-identities-pricing.md).

### <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)