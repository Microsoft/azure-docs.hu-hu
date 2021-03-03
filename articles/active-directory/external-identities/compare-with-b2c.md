---
title: Külső identitások összehasonlítása – Azure Active Directory | Microsoft Docs
description: Az Azure AD külső identitásai lehetővé teszik a szervezeten kívüli személyek számára, hogy saját identitásukat használva hozzáférjenek alkalmazásaihoz és erőforrásaihoz. Külső identitásokkal kapcsolatos megoldások összehasonlítása, beleértve a Azure Active Directory B2B együttműködés és a Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644065"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Mik a külső identitások a Azure Active Directoryban?

Az Azure AD külső identitásokkal lehetővé teheti, hogy a szervezeten kívüli személyek hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben a felhasználó bármilyen identitással bejelentkezhetnek. Partnerei, terjesztői, szállítói, szállítói és más vendégei "saját identitást hozhatnak". Függetlenül attól, hogy rendelkezik-e a vállalat vagy a kormány által kiadott digitális identitással vagy egy nem felügyelt közösségi identitással, például a Google vagy a Facebook szolgáltatással, a saját hitelesítő adataikat használhatják a bejelentkezéshez. A külső felhasználó identitás-szolgáltatója kezeli a személyazonosságát, és az Azure AD-vel való hozzáférését Ön felügyelheti, hogy az erőforrások védve legyenek.

## <a name="external-identities-scenarios"></a>Külső identitások forgatókönyvei

Az Azure AD külső identitások kevesebbet mutatnak a felhasználó és a szervezet közötti kapcsolaton, és azt is, hogy a felhasználó hogyan szeretne bejelentkezni az alkalmazásaiba és erőforrásaiba. Ebben a keretrendszerben az Azure AD különféle forgatókönyveket támogat a vállalatok közötti (B2B) együttműködésből a fogyasztók, az ügyfelek és a polgárok felé irányuló alkalmazások (vállalat – ügyfél vagy B2C) felügyeletének eléréséhez.

- **Megoszthatja alkalmazásait és erőforrásait külső felhasználókkal (B2B-együttműködés)**. A külső felhasználók meghívása a saját bérlőnek a "vendég" felhasználók számára, akik engedélyeket rendelhetnek hozzá (az engedélyezéshez), miközben a meglévő hitelesítő adataikat használják (hitelesítéshez). A felhasználók egy egyszerű Meghívási és beváltási folyamattal jelentkeznek be a megosztott erőforrásokhoz a munkahelyi, iskolai vagy más e-mail-fiókkal. Az [Azure ad jogosultság-kezelési](../governance/entitlement-management-overview.md) szolgáltatással olyan házirendeket is beállíthat, amelyek a [külső felhasználók hozzáférését kezelik](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). Az [önkiszolgáló bejelentkezési felhasználói folyamatok](self-service-sign-up-overview.md)elérhetőségével azonban a külső felhasználók maguk is regisztrálhatják magukat az alkalmazásokra. A felhasználói élmény testreszabható a munkahelyi, iskolai vagy közösségi identitás (például a Google vagy a Facebook) használatával történő regisztráció engedélyezéséhez. A felhasználóval kapcsolatos információkat is gyűjthet a regisztrációs folyamat során. További információt az [Azure ad B2B dokumentációjában](index.yml)talál.

- A **felhasználók és az ügyfelek felé irányuló alkalmazások (Azure ad B2C) létrehozásához hozzon létre egy fehér címkével rendelkező Identity Management megoldást használó felhasználói útvonalakat**. Ha Ön üzleti vagy fejlesztői ügyfélre irányuló alkalmazást hoz létre, a Azure AD B2C használatával akár több millió fogyasztót, ügyfelet vagy polgárt is méretezheti. A fejlesztők az Azure AD-t használhatják az alkalmazások teljes funkcionalitású felhasználói identitás-és hozzáférés-kezelési (CIAM) rendszereként. Az ügyfelek be tudnak jelentkezni egy már létrehozott identitással (például Facebook vagy Gmail). A Azure AD B2C segítségével teljes mértékben testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor. További információ: [Azure ad B2C dokumentáció](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>External Identities-megoldások összehasonlítása

Az alábbi táblázat az Azure AD külső identitásokkal engedélyezhető forgatókönyvek részletes összehasonlítását ismerteti.

|   | Külső felhasználói együttműködés (B2B) | Hozzáférés a fogyasztók/ügyfelek felé irányuló alkalmazásokhoz (B2C)  |
| ---- | --- | --- |
| **Elsődleges forgatókönyv** | Együttműködés a Microsoft-alkalmazásokkal (Microsoft 365, csapatok stb.) vagy a saját alkalmazásaival (SaaS-alkalmazások, egyéni fejlesztésű alkalmazások stb.).  | Identitás-és hozzáférés-kezelés a modern SaaS-vagy egyéni fejlesztésű alkalmazásokhoz (nem a Microsofttól származó alkalmazások esetében).   |
| **A következőre szánt**    | Együttműködik az üzleti partnerekkel olyan külső szervezetekkel, mint a szállítók, a partnerek és a szállítók. A felhasználók vendégként jelennek meg a címtárban. Előfordulhat, hogy ezek a felhasználók nem felügyelték.  | A termék ügyfelei. Ezeket a felhasználókat külön Azure AD-címtárban felügyeli a rendszer.  |
| **Támogatott identitás-szolgáltatók** | A külső felhasználók a munkahelyi fiókok, az iskolai fiókok, az e-mail-címek, az SAML-és a WS-Fed-alapú személyazonosság-szolgáltatók, a Gmail és a Facebook használatával működhetnek.  | A felhasználók a helyi alkalmazás-fiókokkal (bármilyen e-mail-címmel vagy felhasználónévvel), a különböző támogatott közösségi identitásokkal és a vállalat és a kormányzat által kiadott identitásokkal rendelkező felhasználókat közvetlen összevonás útján.       |
| **Külső felhasználók kezelése**   | A külső felhasználók kezelése az alkalmazottakkal megegyező könyvtárban történik, de általában vendég felhasználóként van feljegyzetve. A vendég felhasználók ugyanúgy kezelhetők ugyanúgy, mint az alkalmazottak, ugyanazok a csoportok, és így tovább.    | A külső felhasználók kezelése a Azure AD B2C könyvtárban történik. Ezeket külön kezelik a szervezet alkalmazotti és partneri könyvtára (ha van ilyen).  |
| **Egyszeri bejelentkezés (SSO)**      | Az egyszeri bejelentkezés minden Azure AD-hez csatlakoztatott alkalmazáshoz támogatott. Hozzáférést biztosíthat például Microsoft 365 vagy helyszíni alkalmazásokhoz, valamint más SaaS-alkalmazásokhoz, például Salesforce vagy munkanapokhoz.    | Támogatja az Azure AD B2C bérlőkön belül az ügyfél tulajdonú alkalmazásokra az SSO-t. Az egyszeri bejelentkezés Microsoft 365 vagy más Microsoft SaaS-alkalmazásokhoz nem támogatott.    |
| **Biztonsági házirend és megfelelőség**        | A gazdagép/meghívó szervezet (például [feltételes hozzáférési szabályzatok](conditional-access.md)) kezeli. | A szervezet felügyeli a feltételes hozzáférés és az identitás védelme révén.        |
| **Arculat**  | A rendszer a gazdagép és a meghívó szervezet márkáját használja.    | Egy alkalmazás vagy szervezet teljes mértékben testreszabható arculata.   |
| **Számlázási modell** | [Külső identitások díjszabása](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) a havi aktív felhasználók (Mau) alapján. <br>(Lásd még: [B2B-telepítés részletei](external-identities-pricing.md)) | [Külső identitások díjszabása](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) a havi aktív felhasználók (Mau) alapján. <br>(Lásd még: [B2C-telepítés részletei](../../active-directory-b2c/billing.md)) |
| **További információ** | [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentáció](what-is-b2b.md)                   | [Termék lap](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentáció](../../active-directory-b2c/index.yml)       |

Az Azure AD külső identitásokkal biztonságossá teheti és kezelheti a szervezet határain túli ügyfeleket és partnereket.

## <a name="about-multitenant-applications"></a>Több-bérlős alkalmazások

Ha szolgáltatásként biztosít alkalmazást, és nem szeretné felügyelni ügyfelei felhasználói fiókjait, akkor a több-bérlős alkalmazás valószínűleg a legmegfelelőbb választás. Ha más Azure AD-bérlőknek szánt alkalmazásokat fejleszt, megcélozhatja a felhasználókat egyetlen szervezettől (egyetlen bérlőtől) vagy bármely olyan szervezettől, amely már rendelkezik Azure AD-Bérlővel (több-bérlős alkalmazásokkal). A Alkalmazásregisztrációk az Azure AD-ben alapértelmezés szerint egyetlen bérlő, de a regisztráció több bérlőt is igénybe vehet. Ezt a több-bérlős alkalmazást egyszer regisztrálja saját Azure AD-ben. Ezt követően azonban bármely szervezet bármely Azure AD-felhasználója használhatja az alkalmazást további munka nélkül. További információ: [az identitás kezelése több-bérlős alkalmazásokban](/azure/architecture/multitenant-identity/), [útmutató](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Tudnivalók az Azure AD B2C-ről](../../active-directory-b2c/overview.md)