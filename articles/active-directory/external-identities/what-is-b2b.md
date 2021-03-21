---
title: Mi a B2B együttműködés a Azure Active Directoryban?
description: Az Azure Active Directory B2B együttműködés támogatja a vendég hozzáférést, így biztonságosan megoszthat erőforrásokat és együttműködhet külső partnerekkel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/19/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231d3ab81c2376aa8a2a4bf182e1b5e9799e51d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669780"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Mi az vendég hozzáférés az Azure Active Directory B2B-ben?

A Azure Active Directory (Azure AD) vállalatok közötti (B2B) együttműködés a külső identitások egyik funkciója, amely lehetővé teszi, hogy meghívja a vendég felhasználókat a szervezettel való együttműködésre. A B2B-együttműködés segítségével biztonságosan megoszthatja vállalata alkalmazásait és szolgáltatásait a vendégekkel bármely más szervezettől, miközben megtarthatja a saját vállalati adatok felügyeletét. Biztonságban, biztosan dolgozhat -nagy vagy kicsi- külső partnerekkel, akkor is, ha nincs is náluk Azure AD vagy IT részleg. Az egyszerű meghívási és érvényesítési folyamat lehetővé teszi, hogy a vállalati erőforrásokhoz a partnerek saját hitelesítő adatokkal férjenek hozzá. A fejlesztők az Azure AD B2B API használatával személyre szabhatják a meghívási folyamatot vagy például önkiszolgáló regisztrációs portál alkalmazásokat írhatnak. A vendég felhasználókkal kapcsolatos licencelési és díjszabási információkért tekintse meg a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/).  

> [!IMPORTANT]
> - **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.
> - **Október 2021-től kezdve** a Microsoft nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Együttműködés bármely partnerrel saját identitásuk használatával

Az Azure AD B2B esetén a partner saját identitáskezelési megoldását használja, így nem jelent külső adminisztrációs terhelést a szervezet számára. A vendégfelhasználók a saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkeznek be az alkalmazásokba és szolgáltatásokba.

- A partner saját identitását és hitelesítő adatait használja; Azure AD nem szükséges.
- Nem kell külső fiókokat és jelszavakat kezelnie.
- Nem kell fiókokat szinkronizálni és a fiók életciklust kezelni.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Felhasználók egyszerű meghívása az Azure AD-portálról

A rendszergazdák egyszerűen felvehetnek vendégfelhasználókat a szervezetbe az Azure portálon.

- [Hozzon létre egy új vendég felhasználót](b2b-quickstart-add-guest-users-portal.md) az Azure ad-ben, hasonlóan ahhoz, ahogyan egy új felhasználót szeretne hozzáadni.
- Vendég felhasználók kiosztása alkalmazásokhoz vagy csoportokhoz.
- Beváltási hivatkozást tartalmazó meghívó e-mail küldése, vagy a megosztani kívánt alkalmazásra mutató közvetlen hivatkozás küldése.

![Képernyőfelvétel az új vendég felhasználó Meghívási bejegyzését megjelenítő oldalról](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- A vendég felhasználók néhány egyszerű [beváltási lépést](redemption-experience.md) követnek a bejelentkezéshez.

![A felülvizsgálati engedélyek oldalát ábrázoló képernyőkép](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Házirendek használata az alkalmazások és szolgáltatások biztonságos megosztásához

Az engedélyezési házirendek segítségével biztosíthatja a vállalati tartalmak védettségét. A feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés, kényszeríthető:

- A bérlői szinten.
- Az alkalmazási szinten.
- Meghatározott vendégfelhasználók számára a vállalati alkalmazások és adatok védelmére.

![A feltételes hozzáférés beállítását bemutató képernyőkép](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Alkalmazás és csoport gazdák számára saját vendégfelhasználóik kezelésének lehetővé tétele

A vendégfelhasználók kezelését rábízhatja a felhasználó tulajdonosokra, hogy közvetlenül ők vehessék fel a vendég felhasználókat a megosztani kívánt alkalmazásokba, akár Microsoft alkalmazásról van szó, akár nem.

- A rendszergazdák önkiszolgáló alkalmazás- és csoportkezelést állíthatnak be.
- A nem rendszergazdák a [Hozzáférési Panel](https://myapps.microsoft.com) használatával adhatnak vendégfelhasználókat az alkalmazásokhoz és csoportokhoz.

![Képernyőfelvétel: a vendég felhasználó hozzáférési paneljének megjelenítése](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>A B2B vendég felhasználók bevezetési élményének testreszabása

A szervezet igényei szerint testre szabott módon hozhatja ki a külső partnereit.

- A [külső felhasználók hozzáférését kezelő](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)házirendek konfigurálásához használja az [Azure ad-jogosultságok kezelését](../governance/entitlement-management-overview.md) .
- A [B2B együttműködés Meghívási API-jai](/graph/api/resources/invitation) segítségével testre szabhatja a bevezetési élményeket.

## <a name="integrate-with-identity-providers"></a>Integráció az identitás-szolgáltatókkal

Az Azure AD olyan külső identitás-szolgáltatókat támogat, mint például a Facebook, a Microsoft-fiókok, a Google vagy a vállalati identitás-szolgáltatók. Beállíthatja az összevonási szolgáltatást az identitás-szolgáltatókkal, így a külső felhasználók bejelentkezhetnek meglévő közösségi vagy vállalati fiókjaikkal ahelyett, hogy új fiókot hozna létre az alkalmazáshoz. További információ a [külső identitások identitás-szolgáltatókkal](identity-providers.md)kapcsolatban.

![Az Identity Providers oldalt ábrázoló képernyőfelvétel](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Önkiszolgáló bejelentkezési felhasználói folyamat létrehozása

Az önkiszolgáló regisztrációs felhasználói folyamattal létrehozhat egy regisztrációs élményt azon külső felhasználók számára, akik szeretnének hozzáférni az alkalmazásokhoz. A regisztrálási folyamat részeként különböző közösségi vagy vállalati identitás-szolgáltatók beállításait is megadhatja, és adatokat gyűjthet a felhasználóról. Ismerje meg az [önkiszolgáló regisztrációt és a beállítását](self-service-sign-up-overview.md).

Az [API-összekötők](api-connectors-overview.md) használatával integrálhatja önkiszolgáló bejelentkezési felhasználói folyamatait külső felhőalapú rendszerekkel. Az egyéni jóváhagyási munkafolyamatokkal, a személyazonosság-ellenőrzés elvégzésével, a felhasználó által megadott adatok érvényesítésével és egyebekkel is kapcsolódhat.

![A felhasználói folyamatok lapját ábrázoló képernyőfelvétel](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Következő lépések

- [Az External Identities díjszabása](external-identities-pricing.md)
- [B2B közreműködő vendégfelhasználók hozzáadása a portálon](add-users-administrator.md)
- [A meghívó érvényesítési folyamat ismertetése](redemption-experience.md)