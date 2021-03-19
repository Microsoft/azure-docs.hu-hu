---
title: Vezérlők megadása a feltételes hozzáférési házirendben – Azure Active Directory
description: Az Azure AD feltételes hozzáférési szabályzatában szereplő engedélyezési vezérlők
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d01a750948f8e3c264b9bcffdaad3ae72fa40ac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579110"
---
# <a name="conditional-access-grant"></a>Feltételes hozzáférés: Engedélyezés

Egy feltételes hozzáférési szabályzaton belül a rendszergazda a hozzáférés-vezérlés használatával engedélyezheti vagy letilthatja az erőforrásokhoz való hozzáférést.

![Feltételes hozzáférési szabályzat többtényezős hitelesítést igénylő engedélyezési vezérlővel](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Hozzáférés letiltása

A blokk figyelembe veszi az összes hozzárendelést, és megakadályozza a hozzáférést a feltételes hozzáférési házirend konfigurációja alapján.

A Block egy hatékony vezérlő, amelyet megfelelő ismeretekkel kell megforgatni. A blokk-utasításokkal rendelkező szabályzatok nem kívánt mellékhatással rendelkezhetnek. A méretezés engedélyezése előtt elengedhetetlen a megfelelő tesztelés és ellenőrzés. A rendszergazdáknak olyan eszközöket kell használniuk, mint például a [feltételes hozzáférés jelentésének módja](concept-conditional-access-report-only.md) és [a What if eszköz feltételes hozzáféréssel a](what-if-tool.md) módosítások végrehajtásakor.

## <a name="grant-access"></a>Hozzáférés biztosítása

A rendszergazdák dönthetnek úgy, hogy egy vagy több vezérlőt kényszerítenek a hozzáférés engedélyezésekor. Ezek a vezérlőelemek a következő beállításokat tartalmazzák: 

- [Többtényezős hitelesítés megkövetelése (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Eszköz megfelelőként való megjelölésének megkövetelése (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése](../devices/concept-azure-ad-join-hybrid.md)
- [Jóváhagyott ügyfélalkalmazás megkövetelése](app-based-conditional-access.md)
- [Alkalmazásvédelmi szabályzat megkövetelése](app-protection-based-conditional-access.md)
- [Jelszó módosításának megkövetelése](#require-password-change)

Ha a rendszergazdák úgy döntenek, hogy kombinálják ezeket a beállításokat, a következő módszereket választhatják ki:

- Az összes kijelölt vezérlő megkövetelése (vezérlés **és** vezérlés)
- A kiválasztott vezérlők (vezérlés **vagy** vezérlés) egyikének megkövetelése

Alapértelmezés szerint a feltételes hozzáférés minden kijelölt vezérlőt igényel.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

Ennek a jelölőnégyzetnek a bejelölése esetén a felhasználóknak az Azure AD-Multi-Factor Authentication kell végrehajtaniuk. Az Azure AD Multi-Factor Authentication telepítésével kapcsolatos további információkért tekintse meg a [felhőalapú Azure ad multi-Factor Authentication üzembe helyezésének megtervezése](../authentication/howto-mfa-getstarted.md)című cikket.

A [vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview) megfelel a többtényezős hitelesítés követelményének a feltételes hozzáférési házirendekben. 

### <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

A Microsoft Intune központilag telepített szervezetek a megfelelőségi követelményeknek megfelelő eszközök azonosítására használhatják az eszközük által visszaadott adatokat. A szabályzat megfelelőségi információi továbbítva lesznek az Intune-ból az Azure AD-ba, ahol a feltételes hozzáférés az erőforrásokhoz való hozzáférés engedélyezésére vagy letiltására vonatkozó döntéseket hozhat. A megfelelőségi szabályzatokkal kapcsolatos további információkért tekintse [meg az eszközökön a szervezet erőforrásaihoz való hozzáférés engedélyezése az Intune-](/intune/protect/device-compliance-get-started)nal című cikket.

Az eszközök az Intune (bármilyen eszköz operációs rendszer esetén) vagy külső gyártótól származó, Windows 10-es eszközökhöz tartozó MDM-rendszerek szerint megfelelőként jelölhetők meg. A JAMF Pro az egyetlen támogatott harmadik féltől származó MDM-rendszer. További információ az integrációról: a [JAMF Pro integrálása az Intune](/intune/protect/conditional-access-integrate-jamf)-nal a megfelelőség érdekében.

Az eszközöknek regisztrálva kell lenniük az Azure AD-ben, mielőtt azok megfelelőként jelölhetők meg. Az eszközök regisztrálásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi az az eszköz identitása](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

A szervezetek dönthetnek úgy, hogy az eszköz identitását használják a feltételes hozzáférési szabályzat részeként. A szervezeteknek meg kell követelniük, hogy az eszközök hibrid Azure AD-hez legyenek csatlakoztatva ezzel a jelölőnégyzettel. Az eszközök identitásával kapcsolatos további információkért tekintse meg a [Mi az eszköz identitása?](../devices/overview.md)című cikket.

Az [OAuth folyamat](../develop/v2-oauth2-device-code.md)használatakor a felügyelt eszközök engedélyezési vezérlésének vagy az eszköz állapotának megkövetelése nem támogatott. Ennek az az oka, hogy a hitelesítést végző eszköz nem tudja megadni az eszköz állapotát a kódot biztosító eszköz számára, és a tokenben lévő eszköz állapota zárolva van a hitelesítést végző eszközön. Használja helyette a multi-Factor Authentication engedélyezésének megkövetelése vezérlőt.

### <a name="require-approved-client-app"></a>Jóváhagyott ügyfélalkalmazás megkövetelése

A szervezeteknek meg kell követelniük, hogy a kiválasztott felhőalapú alkalmazásokhoz való hozzáférési kísérletet egy jóváhagyott ügyfélalkalmazás alapján kell végrehajtani. Ezek a jóváhagyott ügyfélalkalmazások a mobileszköz-kezelési (MDM) megoldástól függetlenül támogatják az [Intune app Protection-szabályzatokat](/intune/app-protection-policy) .

Ennek a támogatásnak a kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen Azure Active Directoryban, amelyhez közvetítő alkalmazást kell használni. A közvetítő alkalmazás a Microsoft Authenticator iOS-hez, vagy az Microsoft Authenticator vagy a Microsoft vállalati portál Android-eszközökhöz. Ha egy közvetítő alkalmazás nincs telepítve az eszközön, amikor a felhasználó megpróbál hitelesíteni, a rendszer átirányítja a felhasználót a megfelelő App Store-ba a szükséges közvetítő alkalmazás telepítéséhez.

Ez a beállítás a következő iOS-és Android-alkalmazásokra vonatkozik:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft-Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft-számlázás
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
- Microsoft-Yammer
- Microsoft faliújság
- Microsoft 365-rendszergazda

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune Mobile Application Management szolgáltatást.
- A **jóváhagyott ügyfélalkalmazás megkövetelése** :
   - A csak az iOS és az Android for Device platform feltételeit támogatja.
   - Az eszköz regisztrálásához egy Broker-alkalmazás szükséges. A közvetítő alkalmazás a Microsoft Authenticator iOS-hez, vagy az Microsoft Authenticator vagy a Microsoft vállalati portál Android-eszközökhöz.
- A feltételes hozzáférés nem tekintheti meg a Microsoft Edge-t InPrivate módban egy jóváhagyott ügyfélalkalmazás.
- Az Azure AD Application Proxy használatával lehetővé teheti, hogy a Power BI Mobile App a helyszíni Power BI jelentéskészítő kiszolgálóhoz kapcsolódjon, és nem támogatott olyan feltételes hozzáférési szabályzatok használata, amelyeken a Microsoft Power BI alkalmazás jóváhagyott ügyfélalkalmazás.

Tekintse meg a következő cikket [: a felhőalapú alkalmazások hozzáférésének megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](app-based-conditional-access.md) a konfigurációs példákhoz.

### <a name="require-app-protection-policy"></a>Alkalmazásvédelmi szabályzat megkövetelése

A feltételes hozzáférési házirendben megkövetelheti, hogy az [Intune app Protection szabályzata](/intune/app-protection-policy) elérhető legyen az ügyfélalkalmazás számára, mielőtt a kiválasztott felhőalapú alkalmazások hozzáférhessenek a hozzáféréshez. 

Ennek a támogatásnak a kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen Azure Active Directoryban, amelyhez közvetítő alkalmazást kell használni. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha egy közvetítő alkalmazás nincs telepítve az eszközön, amikor a felhasználó megkísérli a hitelesítést, a rendszer átirányítja a felhasználót az App Store-ba a Broker alkalmazás telepítéséhez.

Az alkalmazásoknak az **INTUNE SDK** **-val kell rendelkezniük, és meg** kell felelniük bizonyos egyéb követelményeknek a beállítás támogatásához. Az Intune SDK-val alkalmazásokat végrehajtó fejlesztők további információkat találhatnak az SDK dokumentációjában a követelményekről.

A következő ügyfélalkalmazások megerősítették a beállítás támogatását:

- Microsoft-Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Többsoros az Intune-hoz
- Kilenc e-mail-E-mail & naptár

> [!NOTE]
> A Microsoft Teams, a Microsoft Kaizala, a Microsoft Skype vállalati verzió és a Microsoft Visio nem támogatja az **alkalmazás-védelmi házirend megkövetelése beállítást** . Ha az alkalmazások működéséhez szükség van, használja a kizárólag **jóváhagyott alkalmazások** engedélyezése lehetőséget. A két támogatás között a vagy záradék használata nem fog működni ehhez a három alkalmazáshoz.

**Megjegyzések**

- Az App Protection-házirend alkalmazásai támogatják az Intune Mobile Application Management szolgáltatást a házirend-védelemmel.
- Az **alkalmazás-védelmi házirend követelményeinek megkövetelése** :
    - A csak az iOS és az Android for Device platform feltételeit támogatja.
    - Az eszköz regisztrálásához egy Broker-alkalmazás szükséges. IOS rendszeren a közvetítő alkalmazás Microsoft Authenticator és Android rendszeren, Intune Céges portál alkalmazás.

Tekintse meg a következő cikket [: How to: app Protection-házirend és egy jóváhagyott ügyfélalkalmazás a Cloud app Accesshez feltételes hozzáféréssel](app-protection-based-conditional-access.md) a konfigurációs példákhoz.

### <a name="require-password-change"></a>Jelszó módosításának megkövetelése 

Ha a rendszer felhasználói kockázatot észlel, a felhasználói kockázati házirend feltételeit használva a rendszergazdák az Azure AD önkiszolgáló jelszó-visszaállítási funkciójával biztonságosan módosíthatják a jelszót. Ha a rendszer a felhasználói kockázatot észleli, a felhasználók önkiszolgáló jelszó-visszaállítást végezhetnek el önkiszolgáló megoldásként, ez a felhasználó kockázati eseményét fogja letiltani, hogy megakadályozza a rendszergazdák számára a szükségtelen zajt. 

Amikor a rendszer kéri a felhasználótól, hogy változtassa meg a jelszavát, először a többtényezős hitelesítés végrehajtásához szükséges. Győződjön meg arról, hogy az összes felhasználó regisztrálva van a többtényezős hitelesítéshez, így azok a fiókhoz tartozó kockázat észlelése esetén is felkészültek.  

> [!WARNING]
> A felhasználói kockázati házirend elindítása előtt a felhasználóknak előzőleg regisztrálniuk kell magukat az önkiszolgáló jelszó-visszaállításhoz. 

Ha a jelszó-módosítási vezérlő használatával konfigurál egy házirendet, a rendszer néhány korlátozást is tartalmaz.  

1. A szabályzatot hozzá kell rendelni az "összes felhőalapú alkalmazáshoz". Ez megakadályozza, hogy egy másik alkalmazás használatával a támadók megváltoztassák a felhasználó jelszavát, és a fiók kockázatának visszaállítását. Ehhez egyszerűen egy másik alkalmazásba kell bejelentkeznie. 
1. A jelszó megkövetelése nem használható más vezérlőkkel, például megfelelő eszköz megkövetelésével.  
1. A jelszó-módosítási vezérlő csak a felhasználó-és csoport-hozzárendelési feltétellel, a Cloud app-hozzárendelési feltétellel (amely az összes értékre kell állítani) és a felhasználói kockázati feltételekkel használható. 

### <a name="terms-of-use"></a>Használati feltételek

Ha a szervezet létrehozta a használati feltételeket, az engedélyezési vezérlők területen további beállítások is láthatók. Ezek a beállítások lehetővé teszik a rendszergazdák számára, hogy a szabályzat által védett erőforrások elérésének feltétele megkövetelje a használati feltételek visszaigazolását. A használati feltételekkel kapcsolatos további információkért tekintse meg a cikkben [Azure Active Directory használati feltételeket](terms-of-use.md).

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: munkamenet-vezérlők](concept-conditional-access-session.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
