---
title: Összevonási tanúsítványok kezelése az Azure AD-| Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre az összevonási tanúsítványok lejárati dátumát, és hogyan újíthatja meg a hamarosan lejáró tanúsítványokat.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379451"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Összevont egyszeri bejelentkezés tanúsítványának kezelése a Azure Active Directory

Ebben a cikkben az Azure Active Directory (Azure AD) által a szolgáltatott szoftver (SaaS) alkalmazásaiba való összevont egyszeri bejelentkezés (SSO) létrehozására létrehozott tanúsítványokkal kapcsolatos gyakori kérdéseket és információkat fedjük le. Alkalmazásokat adhat hozzá az Azure AD alkalmazás-katalógusából vagy nem katalógusbeli alkalmazássablon használatával. Konfigurálja az alkalmazást az összevont SSO beállítással.

Ez a cikk csak olyan alkalmazásokra terjed ki, amelyek [](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) az Azure AD SSO-t Security Assertion Markup Language (SAML) összevonással használják.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatikusan létrehozott tanúsítvány katalógus- és nem katalógusalkalmazások számára

Amikor új alkalmazást ad hozzá a katalógusból, és SAML-alapú bejelentkezést konfigurál (az egyszeri bejelentkezés SAML kiválasztásával az alkalmazás áttekintő oldalán), az Azure AD létrehoz egy tanúsítványt az alkalmazáshoz, amely három évig  >   érvényes. Az aktív tanúsítvány biztonsági tanúsítványfájlként **(.cer)** való letöltéséhez térjen vissza erre az oldalra **(SAML-alapú** bejelentkezés), és válasszon egy letöltési hivatkozást az **SAML** aláíró tanúsítvány fejlécében. Választhat a nyers (bináris) tanúsítvány vagy a Base64-tanúsítvány (base 64 kódolású szöveg) közül. Katalógusalkalmazások esetén ez a szakasz egy hivatkozást is mutathat, amely az alkalmazás követelményeitől függően összevonási metaadatok XML-fájljaként **(.xml** fájlként) letölti a tanúsítványt.

![Az SAML aktív aláíró tanúsítvány letöltési beállításai](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Az SAML aláíró tanúsítvány fejlécének Szerkesztés ikonját (ceruza) választva aktív vagy inaktív tanúsítványt is letölthet, amely megjeleníti az **SAML** aláíró tanúsítvány **oldalát.**  Válassza a letölteni kívánt tanúsítvány melletti három pont **(...)** lehetőséget, majd válassza ki a kívánt tanúsítványformátumot. Lehetősége van arra is, hogy adatvédelmi nyilatkozattal bővített e-mail (PEM) formátumban töltse le a tanúsítványt. Ez a formátum megegyezik a Base64 formátumával, de **.pem** fájlnévkiterjesztéssel rendelkezik, amelyet a Windows nem ismer fel tanúsítványformátumként.

![SamL aláíró tanúsítvány letöltési lehetőségei (aktív és inaktív)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Az összevonási tanúsítvány lejárati dátumának testreszabása és átváltása egy új tanúsítványra

Alapértelmezés szerint az Azure úgy konfigurálja a tanúsítványt, hogy három év után lejárjon, amikor az SAML egyszeri bejelentkezési konfigurációja során automatikusan létrejön. Mivel a mentés után nem módosíthatja a tanúsítvány dátumát, a következőt kell:

1. Hozzon létre egy új tanúsítványt a kívánt dátummal.
1. Mentse az új tanúsítványt.
1. Töltse le az új tanúsítványt a megfelelő formátumban.
1. Töltse fel az új tanúsítványt az alkalmazásba.
1. Az új tanúsítványt aktívvá kell tenni a Azure Active Directory portálon.

A következő két szakasz segít végrehajtani ezeket a lépéseket.

### <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Először hozzon létre és mentsen új tanúsítványt egy másik lejárati dátummal:

1. Jelentkezzen be a [Azure Active Directory portálra.](https://aad.portal.azure.com/) Megjelenik **Azure Active Directory Felügyeleti központ** lapja.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókjában található vállalati alkalmazások listája.
1. Válassza ki az érintett alkalmazást. Megjelenik az alkalmazás áttekintő oldala.
1. Az alkalmazás áttekintő oldalának bal oldali panelen válassza az **Egyszeri bejelentkezés lehetőséget.**
1. Ha **megjelenik az Egyszeri bejelentkezési** módszer kiválasztása lap, válassza az **SAML lehetőséget.**
1. A **Set up Single Sign-On with SAML - Preview** (Egyetlen alkalmazás beállítása  SAMLSign-On előzetes verzióval) lapon keresse meg az **SAML aláíró** tanúsítvány fejlécét, és válassza a Szerkesztés ikont (ceruza). Megjelenik az **SAML-aláíró** tanúsítvány lap, amely az egyes tanúsítványok **állapotát** (Aktív vagy **Inaktív),** lejárati dátumát és ujjlenyomatát (kivonatsring) jeleníti meg.
1. Válassza **az Új tanúsítvány lehetőséget.** Egy új sor jelenik meg a tanúsítványlista alatt, ahol a lejárati dátum alapértelmezés szerint pontosan három évvel az aktuális dátum után van. (A módosítások még nincsenek mentve, így továbbra is módosíthatja a lejárati dátumot.)
1. Az új tanúsítvány sorban mutasson a lejárati dátum oszlopra, és válassza a **Dátum kiválasztása** ikont (naptár). Megjelenik egy naptárvezérlő, amely az új sor aktuális lejárati dátumának hónap napjait jeleníti meg.
1. Új dátum beállításához használja a naptárvezérlőt. Az aktuális dátum és az azt követően három év közötti dátumot állíthat be.
1. Kattintson a **Mentés** gombra. Az új tanúsítvány most Inaktív állapotúként **jelenik** meg, a választott lejárati dátummal és ujjlenyomattal.
1. Válassza az **X-et,** hogy visszatérjen a Set up Single Sign-On with SAML - Preview (Egyszeri Sign-On **beállítása SAML- előzetes verzióval) oldalra.**

### <a name="upload-and-activate-a-certificate"></a>Tanúsítvány feltöltése és aktiválása

Ezután töltse le az új tanúsítványt a megfelelő formátumban, töltse fel az alkalmazásba, és tegye aktívvá az Azure Active Directory:

1. Tekintse meg az alkalmazás SAML-bejelentkezésre vonatkozó további konfigurációs utasításait:

   - Válassza ki **a konfigurációs útmutató** hivatkozását, hogy egy külön böngészőablakban vagy lapon tekintse meg, vagy
   - Lépjen a **beállítási fejlécre,** és válassza a **View step-by-step instructions** to view in a sidebar (Részletes útmutató megtekintése) et az oldalsávon való megtekintéshez.

1. Az utasításokban jegyezze fel a tanúsítványfeltöltéshez szükséges kódolási formátumot.
1. Kövesse a korábban a katalógusban és nem katalógusban található alkalmazásokhoz automatikusan létrehozott tanúsítvány című [szakaszban](#auto-generated-certificate-for-gallery-and-non-gallery-applications) található utasításokat. Ez a lépés az alkalmazás által feltöltéshez szükséges kódolási formátumban tölti le a tanúsítványt.
1. Ha az új tanúsítványra szeretne áttűnni, akkor vissza kell mennie az **SAML** aláíró tanúsítvány oldalára, és az újonnan mentett tanúsítvány sorban válassza a három pont **(...),** majd a **Tanúsítvány** aktívvá tenni lehetőséget. Az új tanúsítvány állapota **Aktív** lesz, a korábban aktív tanúsítvány állapota **Pedig Inaktív** lesz.
1. Folytassa az alkalmazás SAML-bejelentkezési konfigurációs utasításait, amelyek korábban megjelennek, hogy a megfelelő kódolási formátumban tölthető fel az SAML aláíró tanúsítványa.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mail-értesítési címek hozzáadása a tanúsítvány lejáratakor

Az Azure AD 60, 30 és 7 nappal az SAML-tanúsítvány lejárta előtt küld e-mailes értesítést. Az értesítések fogadására több e-mail-címet is hozzáadhat. Az e-mail-cím(ök) megadása, amely(ök)nek el szeretné küldeni az értesítéseket:

1. Az **SAML-aláíró tanúsítvány lapon** kattintson az értesítési e-mail-címek **fejlécére.** Alapértelmezés szerint ez a fejléc csak annak a rendszergazdának az e-mail-címét használja, aki hozzáadta az alkalmazást.
1. A végső e-mail-cím alatt írja be azt az e-mail-címet, amelyről a tanúsítvány lejárati értesítése fog kapni, majd nyomja le az Enter billentyűt.
1. Ismételje meg az előző lépést minden hozzáadni kívánt e-mail-címnél.
1. Minden törölni kívánt e-mail-címhez válassza a **Törlés** ikont (kuka) az e-mail-cím mellett.
1. Kattintson a **Mentés** gombra.

Az Értesítési listához legfeljebb 5 e-mail-címet adhat hozzá (beleértve annak a rendszergazdának az e-mail-címét is, aki az alkalmazást hozzáadta). Ha további személyeket kell értesítenie, használja a terjesztési lista e-mailjeit.

Az értesítő e-mailt a következő címről kapja: aadnotification@microsoft.com . Ha el szeretne kerülni, hogy az e-mail a levélszemét helyére kerül, adja hozzá ezt az e-mailt a névjegyekhez.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Hamarosan elévülő tanúsítvány megújítása

Ha egy tanúsítvány hamarosan lejár, egy olyan eljárással újíthatja meg, amely nem jár jelentős állásidővel a felhasználók számára. Lejáró tanúsítvány megújítása:

1. Kövesse a korábban az Új tanúsítvány [létrehozása](#create-a-new-certificate) szakaszban található utasításokat egy olyan dátummal, amely átfedésben van a meglévő tanúsítvánnyal. Ez a dátum korlátozza a tanúsítvány lejárata által okozott állásidőt.
1. Ha az alkalmazás automatikusan át tudja állítani a tanúsítványt, állítsa az új tanúsítványt aktívra az alábbi lépésekkel:
   1. Vissza AZ SAML aláíró **tanúsítványa lapra.**
   1. Az újonnan mentett tanúsítvány sorban válassza a három pont **(...**), majd a **Tanúsítvány aktívvá tenni lehetőséget.**
   1. Hagyja ki a következő két lépést.

1. Ha az alkalmazás egyszerre csak egy tanúsítványt tud kezelni, válasszon állásidő-időközt a következő lépés végrehajtásához. (Ellenkező esetben, ha az alkalmazás nem automatikusan felveszi az új tanúsítványt, de több aláíró tanúsítványt is képes kezelni, a következő lépést bármikor végrehajthatja.)
1. A régi tanúsítvány lejárata előtt kövesse a korábban a Tanúsítvány feltöltése és aktiválása [szakaszban található](#upload-and-activate-a-certificate) utasításokat.
1. A tanúsítvány megfelelő működése érdekében jelentkezzen be az alkalmazásba.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
- [Alkalmazásfelügyelet az Azure Active Directoryval](what-is-application-management.md)
- [Egyszeri bejelentkezés az alkalmazásokba a Azure Active Directory](what-is-single-sign-on.md)
- [Az Azure Active Directoryban található alkalmazásokba történő SAML-alapú egyszeri bejelentkezés hibaelhárítása](./debug-saml-sso-issues.md)
