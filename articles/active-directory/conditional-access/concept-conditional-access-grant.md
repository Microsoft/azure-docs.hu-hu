---
title: Vezérlők megadása a feltételes hozzáférési szabályzatban – Azure Active Directory
description: Mik azok a vezérlők az Azure AD feltételes hozzáférési szabályzatában?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530191"
---
# <a name="conditional-access-grant"></a>Feltételes hozzáférés: Engedély

A feltételes hozzáférési szabályzaton belül a rendszergazdák hozzáférés-vezérléssel adhatnak vagy tilthat le erőforrásokhoz való hozzáférést.

![Feltételes hozzáférési szabályzat többtényezős hitelesítést igénylő engedélyvezérlővel](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Hozzáférés letiltása

A letiltás figyelembe veszi a hozzárendeléseket, és a feltételes hozzáférési szabályzat konfigurációja alapján megakadályozza a hozzáférést.

A blokk egy hatékony vezérlő, amely a megfelelő ismeretekkel rendelkezik. A blokk utasításokkal kapcsolatos szabályzatok nem szándékolt mellékhatásokkal is járnak. A megfelelő tesztelés és ellenőrzés létfontosságú a nagy léptékű engedélyezés előtt. A rendszergazdáknak olyan [](concept-conditional-access-report-only.md) eszközöket kell használniuk [](what-if-tool.md) a feltételes hozzáférés jelentési módhoz és What If a feltételes hozzáféréshez, amikor módosításokat eszköznek kell használniuk.

## <a name="grant-access"></a>Hozzáférés biztosítása

A rendszergazdák dönthetnek úgy, hogy egy vagy több vezérlőt kényszerít ki a hozzáférés megadásakor. Ezek a vezérlők a következő lehetőségeket tartalmazzák: 

- [Többtényezős hitelesítés megkövetelve (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Eszköz megfelelőként való jelölésének megkövetelve (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hibrid Azure AD-hez csatlakozott eszköz megkövetelve](../devices/concept-azure-ad-join-hybrid.md)
- [Jóváhagyott ügyfélalkalmazás megkövetelve](app-based-conditional-access.md)
- [Alkalmazásvédelmi szabályzat megkövetelése](app-protection-based-conditional-access.md)
- [Jelszóváltozás megkövetelése](#require-password-change)

Amikor a rendszergazdák úgy döntenek, hogy kombinálják ezeket a lehetőségeket, a következő módszereket választhatják:

- Az összes kiválasztott vezérlő megkövetelve (vezérlés **és** vezérlés)
- A kiválasztott vezérlők egyikének megkövetelt **(vagy** vezérlés)

Alapértelmezés szerint a feltételes hozzáféréshez az összes kiválasztott vezérlő szükséges.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelve

Ha be van jelölje be ezt a jelölőnégyzetet, a felhasználóknak Azure AD Multi-Factor Authenticationt kell végrehajtania. Az Azure AD Multi-Factor Authentication üzembe helyezéséről további információt a felhőalapú [Azure AD Multi-Factor Authentication üzembe](../authentication/howto-mfa-getstarted.md)helyezésének megtervezését cikkben talál.

[Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview) megfelel a feltételes hozzáférési szabályzatok többtényezős hitelesítésére vonatkozó követelménynek. 

### <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való jelölésének megkövetelve

Azok a szervezetek, amelyek üzembe Microsoft Intune az eszközeikről visszaadott információk alapján azonosítják az adott megfelelőségi követelményeknek megfelelő eszközöket. A szabályzat megfelelőségi információit az Intune továbbítja az Azure AD-nek, ahol a feltételes hozzáférés döntéseket hozhat az erőforrásokhoz való hozzáférés megadásáról vagy letiltásról. A megfelelőségi szabályzatokkal kapcsolatos további információkért lásd: Szabályok beállítása az eszközökön a szervezet erőforrásaihoz való hozzáféréshez az [Intune használatával.](/intune/protect/device-compliance-get-started)

Az eszközöket az Intune (bármilyen eszköz operációs rendszere esetén) megfelelőként, vagy külső MDM-rendszerként is megjelölheti Windows 10 eszközök esetében. A Jamf Pro az egyetlen támogatott külső MDM-rendszer. További információ az integrációról: A Jamf Pro integrálása az Intune-nal [a megfelelőség érdekében.](/intune/protect/conditional-access-integrate-jamf)

Az eszközöket regisztrálni kell az Azure AD-ban, mielőtt megfelelőként megjelölheti őket. További információt az eszközregisztrációról a Mi az eszközidentitás? cikkben [talál.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakozott eszköz megkövetelve

A szervezetek dönthetnek úgy, hogy az eszközidentitást a feltételes hozzáférési szabályzat részeként használják. Ezzel a jelölőnégyzettel a szervezetek megkövetelhetik az eszközök hibrid Azure AD-csatlakozását. Az eszközidentitásokkal kapcsolatos további információkért lásd a Mi [az eszközidentitás? cikket.](../devices/overview.md)

Az eszközkód [OAuth-folyamatának](../develop/v2-oauth2-device-code.md)használata esetén a felügyelt eszköz engedélyének kötelező megadása vagy az eszközállapot-feltétel nem támogatott. Ennek az az oka, hogy a hitelesítést végző eszköz nem tudja biztosítani az eszköz állapotát a kódot meg biztosító eszköznek, és a jogkivonatban található eszközállapot zárolva van a hitelesítést végző eszközhöz. Ehelyett használja a többtényezős hitelesítés megkövetelt engedélyének vezérlését.

### <a name="require-approved-client-app"></a>Jóváhagyott ügyfélalkalmazás megkövetelve

A szervezetek megkövetelhetik, hogy a kiválasztott felhőalkalmazások hozzáférési kísérletét egy jóváhagyott ügyfélalkalmazásból kísérelje meg. Ezek a jóváhagyott ügyfélalkalmazások a mobileszköz-kezelési (MDM) megoldásoktól függetlenül támogatják az [Intune](/intune/app-protection-policy) alkalmazásvédelmi szabályzatait.

A feltételes hozzáférés használatához a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen Azure Active Directory, amelyhez közvetítőalkalmazás szükséges. A közvetítőalkalmazás lehet az iOS Microsoft Authenticator, vagy a Microsoft Authenticator vagy a Microsoft Céges portál Android-eszközökhöz. Ha nem telepít közvetítőalkalmazást az eszközre, amikor a felhasználó megpróbál hitelesíteni, a rendszer átirányítja a felhasználót a megfelelő alkalmazás-áruházba a szükséges közvetítőalkalmazás telepítéséhez.

Ez a beállítás a következő iOS- és Android-alkalmazásokra vonatkozik:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft számlázás
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Vállalati verzió
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 rendszergazda

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune mobilalkalmazás-kezelési funkcióját.
- A **Jóváhagyott ügyfélalkalmazás megkövetelve** követelmény:
   - Csak az iOS és az Android operációs rendszereket támogatja az eszközplatform-feltételekhez.
   - Az eszköz regisztrálásához közvetítő alkalmazás szükséges. A közvetítőalkalmazás lehet az iOS Microsoft Authenticator, vagy a Microsoft Authenticator vagy a Microsoft Céges portál Android-eszközökhöz.
- A feltételes hozzáférés nem Microsoft Edge, hogy InPrivate módban engedélyezett ügyfélalkalmazást használjanak.
- Az Azure AD alkalmazásproxy használata, hogy az Power BI-mobilalkalmazás helyszíni Power BI jelentéskészítő kiszolgáló-hoz csatlakozzon, nem támogatottak az olyan feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a Microsoft Power BI alkalmazás jóváhagyott ügyfélalkalmazásként csatlakozzon.

Konfigurációs példákért tekintse meg a How [to: Require approved client apps for cloud app access with Conditional Access (How to: Require approved client apps for cloud app access with Conditional Access with Conditional Access)](app-based-conditional-access.md) (How to: Require approved client apps for cloud app access with Conditional Access (How to: Require approved client apps for cloud app access with Conditional

### <a name="require-app-protection-policy"></a>Alkalmazásvédelmi szabályzat megkövetelése

A feltételes hozzáférési szabályzatban megkövetelheti, hogy az [Intune](/intune/app-protection-policy) alkalmazásvédelmi szabályzat jelen legyen az ügyfélalkalmazásban, mielőtt a hozzáférés elérhetővé válik a kiválasztott felhőalkalmazások számára. 

A hozzáférés-vezérlés használatához a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen a Azure Active Directory, amelyhez közvetítőalkalmazásra van szükség. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha a felhasználó a hitelesítés során nem telepít közvetítőalkalmazást az eszközön, a rendszer átirányítja a felhasználót az alkalmazás-áruházba a közvetítőalkalmazás telepítéséhez.

Az alkalmazásoknak meg kell valósítaniük az **Intune SDK-t** a **Policy Assuranceval,** és meg kell felelnie bizonyos egyéb követelményeknek a beállítás támogatásához. Az Intune SDK-val alkalmazásokat fejlesztő fejlesztők az SDK dokumentációjában találhatnak további információt ezekről a követelményekről.

A következő ügyfélalkalmazások megerősítik, hogy támogatják ezt a beállítást:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft-listák (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine az Intune-ban
- Nine Mail – E-mail & Naptár

> [!NOTE]
> A Microsoft Teams, a Microsoft Kaizala, a Microsoft Skype Vállalati verzió és a Microsoft Visio nem támogatja az **Alkalmazásvédelmi** szabályzat szükséges engedélyét. Ha azt igényli, hogy ezek az alkalmazások működjön, használja a Kizárólag jóváhagyott **alkalmazások megkövetelve** engedélyt. A vagy záradék használata a két támogatás között nem működik a három alkalmazás esetében.

**Megjegyzések**

- Az alkalmazásvédelmi szabályzathoz használható alkalmazások támogatják az Intune mobilalkalmazás-kezelési funkcióját szabályzatvédelemmel.
- Az **Alkalmazásvédelmi szabályzat követelményeinek megkövetelve:**
    - Csak az iOS és az Android operációs rendszereket támogatja az eszközplatform-feltételekhez.
    - Az eszköz regisztrálásához közvetítő alkalmazás szükséges. iOS rendszeren a közvetítőalkalmazás Microsoft Authenticator, Androidon pedig Intune Céges portál alkalmazás.

Konfigurációs példákért [lásd: How to: Require app protection policy and an approved](app-protection-based-conditional-access.md) client app for cloud app access with Conditional Access (How to: Require app protection policy and an approved client app for cloud app access with Conditional Access with Conditional Access).

### <a name="require-password-change"></a>Jelszóváltozás megkövetelése 

Felhasználói kockázat észlelésekor a felhasználók kockázati szabályzatának feltételei alapján a rendszergazdák dönthetnek úgy, hogy az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatásával biztonságosan módosítják a jelszót. Felhasználói kockázat észlelése esetén a felhasználók önkiszolgáló jelszó-visszaállítást hajthat végre az önkiszolgáló jelszó-visszaállításhoz, ezzel lezárják a felhasználói kockázati eseményt, hogy a felesleges zaj ne okozzen felesleges zajt a rendszergazdáknak. 

Amikor a rendszer kéri a felhasználót a jelszavának módosításakor, először el kell látnia a többtényezős hitelesítést. Győződjön meg arról, hogy az összes felhasználó regisztrált a többtényezős hitelesítésre, így felkészültek arra az esetre, ha a fiók kockázatnak lenne kitéve.  

> [!WARNING]
> A felhasználóknak korábban regisztrálniuk kell az új jelszó önkiszolgáló nevére a felhasználói kockázati szabályzat aktiválása előtt. 

A szabályzat jelszóváltozás-vezérlővel való konfigurálásakor több korlátozás is vonatkozik.  

1. A szabályzatot hozzá kell rendelni "minden felhőalkalmazáshoz". Ez megakadályozza, hogy a támadók más alkalmazással módosítják a felhasználó jelszavát és visszaállítsa a fiók kockázatát, egyszerűen egy másik alkalmazásba bejelentkezve. 
1. A jelszóváltozás megkövetelése nem használható más vezérlőkkel, például a megfelelő eszköz megkövetelése.  
1. A jelszóváltozás-vezérlés csak a felhasználó- és csoport-hozzárendelési feltétellel, a felhőalkalmazás-hozzárendelési feltétellel (amelyet az összesre kell beállítani) és a felhasználói kockázati feltételekkel használható. 

### <a name="terms-of-use"></a>Használati feltételek

Ha a szervezet használati feltételeket hozott létre, előfordulhat, hogy további lehetőségek is láthatók lesznek az engedély-vezérlés alatt. Ezekkel a beállításokkal a rendszergazdák megkövetelhetik a használati feltételek nyugtázását a szabályzat által védett erőforrásokhoz való hozzáférés feltételeként. További információt a használati feltételekről a cikkben, a használati [Azure Active Directory talál.](terms-of-use.md)

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: Munkamenet-vezérlők](concept-conditional-access-session.md)

- [Feltételes hozzáférés – gyakori szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
