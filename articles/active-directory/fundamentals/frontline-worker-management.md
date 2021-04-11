---
title: Frontline Worker Management – Azure Active Directory
description: Ismerkedjen meg a saját munkatársak portálon keresztül elérhető, a munkavégzők felügyeleti képességeivel.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969240"
---
# <a name="frontline-worker-management"></a>Az élvonalbeli felügyelet

Az élvonalbeli munkavégzők a globális munkaerő több mint 80%-át teszik ki. A nagy léptékű, gyors forgalmú és töredezett folyamatok miatt azonban az élvonalbeli feldolgozók gyakran nem rendelkeznek olyan eszközökkel, amelyek megkönnyítik a nehéz feladatok elvégzését. A Frontline Worker Management a teljes élvonalbeli munkaerő digitális átalakítását teszi lehetővé. A munkaerő magában foglalhatja a kezelőket, az élvonalbeli dolgozókat, a műveleteket és az IT-t is.

Az élvonalbeli munkavégzők felügyelete a következő tevékenységek könnyebb megvalósítása érdekében teszi lehetővé az élvonalbeli munkaerő felügyeletét:
- A közös informatikai feladatok egyszerűsítése a saját munkatársakkal
- Egyszerűbben üzembe helyezés az élvonalbeli feldolgozók számára egyszerűsített hitelesítéssel
- A megosztott eszközök zökkenőmentes kiépítés és az élvonalbeli feldolgozók biztonságos kijelentkezése

## <a name="delegated-user-management-through-my-staff"></a>Delegált felhasználók felügyelete a saját munkatársain keresztül

Azure Active Directory (Azure AD) lehetővé teszi a felhasználók felügyeletének delegálását a [saját munkatársak portálon](../roles/my-staff-configure.md)keresztül, így értékes időt takaríthat meg, és csökkentheti a kockázatokat. Ha az egyszerűsített jelszó-visszaállítást és a telefonos kezelést közvetlenül az áruházból vagy a gyárból végzi, a kezelők hozzáférést biztosíthatnak az alkalmazottaknak anélkül, hogy a kérést az ügyfélszolgálaton, az IT-n vagy a műveleten keresztül átirányítják

![Delegált felhasználók kezelése a saját munkatársak portálján](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Gyorsított előkészítés egyszerűsített hitelesítéssel

A munkatársaim azt is lehetővé teszik a Frontline managerek számára, hogy regisztrálják a csapattagok telefonszámát [SMS-bejelentkezéshez](../authentication/howto-authentication-sms-signin.md). Számos vertikális feldolgozónak a helyi Felhasználónév és jelszó kombinációt kell fenntartania, ami gyakran nehézkes, költséges és hibákra hajlamos megoldás. Ha az SMS-bejelentkezéssel engedélyezi a hitelesítést, az Frontline-feldolgozók [egyszeri bejelentkezéssel (SSO)](../manage-apps/what-is-single-sign-on.md) jelentkezhetnek be a Microsoft-csapatoknak és más alkalmazásoknak, amelyek csak a telefonszámát és egy egyszeri jelszót (OTP) használnak SMS-ben. Így egyszerűen és biztonságosan jelentkezhet be a Frontline-feldolgozók számára, és gyors hozzáférést biztosítanak a szükséges alkalmazásokhoz.

![SMS-bejelentkezés](media/concept-fundamentals-frontline-worker/sms-signin.png)

Az élvonalbeli kezelők a felügyelt kezdőképernyő (MHS) alkalmazással is használhatják a felhasználók számára, hogy az Intune-ban regisztrált Android-eszközökön is hozzáférjenek az alkalmazások egy adott készletéhez. A dedikált eszközök regisztrálása az [Azure ad megosztott eszköz módban](../develop/msal-shared-devices.md)történik. Ha többalkalmazásos kioszk módban van konfigurálva a Microsoft Endpoint Manager-(MEM-) konzolon, az MHS automatikusan elindul az eszköz alapértelmezett képernyőjén, és a végfelhasználóként jelenik meg *a kezdőlapon* . További információ: [a Microsoft Managed Home Screen alkalmazás konfigurálása Android Enterprise rendszerhez](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Biztonságos kijelentkezés az élvonalbeli feldolgozók számára a megosztott eszközökről

Számos vállalat használja a megosztott eszközöket, így a feldolgozók a Készletkezelés és a pénztári tranzakciók elvégzése nélkül végezhetik el az egyes eszközök kiépítési és nyomkövetési terheit. A megosztott eszközök kijelentkezése esetén a rendszer egyszerűvé teszi, hogy egy Frontline-feldolgozó biztonságosan kijelentkezzen a megosztott eszközökön lévő összes alkalmazásból, mielőtt visszaadná azt egy központba, vagy átadja azt a következő eltolódásban lévő csapattársanak. A Microsoft Teams az egyik olyan alkalmazás, amely jelenleg támogatott a megosztott eszközökön, és lehetővé teszi, hogy a feldolgozók megtekintsék a hozzájuk rendelt feladatokat. Ha egy feldolgozó kijelentkezik egy megosztott eszközről, az Intune és az Azure AD törli az összes vállalati adatmennyiséget, így az eszköz biztonságosan átadható a következő munkatársnak. Ezt a funkciót integrálhatja az összes üzletági [iOS](../develop/msal-ios-shared-devices.md) -és [Android](../develop/msal-android-shared-devices.md) -alkalmazásba a [Microsoft Authentication Library](../develop/msal-overview.md)használatával.

![Megosztott eszköz kijelentkezése](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Következő lépések

- A delegált felhasználók felügyeletével kapcsolatos további információkért tekintse meg a [személyzet felhasználói dokumentációját](../user-help/my-staff-team-manager.md).
- Az SAP-SuccessFactors való bejövő felhasználói kiépítés esetén tekintse meg az [SAP-SuccessFactors konfigurálásának Active Directory a felhasználók üzembe](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)helyezése című oktatóanyagot.
- A munkaidőn kívüli bejövő felhasználók kiépítés esetén tekintse meg az [automatikus felhasználó-kiépítés konfigurálásának munkaidejét](../saas-apps/workday-inbound-tutorial.md)ismertető oktatóanyagot.
