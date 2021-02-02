---
title: Összevonási tanúsítványok kezelése az Azure AD-ben | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre az összevonási tanúsítványok lejárati dátumát, és hogyan újíthatja meg a hamarosan lejáró tanúsítványokat.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9b6e81fd090a625ec7ac86963cc9d5e66f5010e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259219"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Az összevont egyszeri bejelentkezés tanúsítványainak kezelése Azure Active Directory

Ebben a cikkben az Azure Active Directory (Azure AD) által az összevont egyszeri bejelentkezés (SSO) és a szolgáltatott szoftver (SaaS) alkalmazásai számára létrehozott tanúsítványokkal kapcsolatos gyakori kérdéseket és információkat tárgyaljuk. Alkalmazások hozzáadása az Azure AD-katalógusból vagy egy nem katalógusból származó alkalmazás-sablon használatával. Konfigurálja az alkalmazást az összevont egyszeri bejelentkezés lehetőség használatával.

Ez a cikk csak azokra az alkalmazásokra vonatkozik, amelyek az Azure AD SSO [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML)-összevonás használatával történő használatára vannak konfigurálva.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatikusan generált tanúsítvány a katalógushoz és a nem katalógusbeli alkalmazásokhoz

Amikor új alkalmazást ad hozzá a katalógusból, és konfigurál egy SAML-alapú bejelentkezést (az **egyszeri bejelentkezés**  >  **SAML** kiválasztásával az alkalmazás áttekintés lapjáról), az Azure ad létrehoz egy tanúsítványt az alkalmazás számára, amely három évig érvényes. Az aktív tanúsítvány biztonsági tanúsítvány (**. cer**) fájlként való letöltéséhez térjen vissza ehhez az oldalhoz (**SAML-alapú bejelentkezés**), és válasszon egy letöltési hivatkozást az **SAML aláíró tanúsítvány** fejlécében. Választhat a nyers (bináris) tanúsítvány vagy a Base64 (Base 64-kódolt szöveg) tanúsítvány közül. A katalógusbeli alkalmazások esetében ez a szakasz egy hivatkozást is megjelenít, amely az alkalmazás követelményeitől függően az összevonási metaadatok XML-kódjának ( **. XML** fájljának) letöltésére szolgál.

![SAML aktív aláíró tanúsítvány letöltési lehetőségei](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Az aktív vagy inaktív tanúsítványok letöltéséhez válassza az **SAML aláíró tanúsítvány** fejlécének **szerkesztési** ikonját (ceruza), amely megjeleníti az **SAML aláíró tanúsítvány** lapot. Válassza a letölteni kívánt tanúsítvány melletti három pontot (**..**.), majd válassza ki a kívánt tanúsítványt. A tanúsítvány az adatvédelem-kibővített levelek (PEM) formátumában is letölthető. Ez a formátum megegyezik a Base64-vel, de a **. PEM** fájlnévkiterjesztéssel, amely nem ismerhető fel a Windowsban tanúsítvány formátuma.

![SAML-aláíró tanúsítvány letöltési beállításai (aktív és inaktív)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Az összevonási tanúsítvány lejárati dátumának testreszabása és átadása egy új tanúsítványra

Alapértelmezés szerint az Azure három év elteltével állítja be a tanúsítvány érvényességét, amikor az SAML egyszeri bejelentkezés konfigurálása során automatikusan létrejön. Mivel a mentés után nem lehet módosítani a tanúsítvány dátumát, a következőket kell tennie:

1. Hozzon létre egy új tanúsítványt a kívánt dátummal.
1. Mentse az új tanúsítványt.
1. Töltse le az új tanúsítványt a megfelelő formátumban.
1. Töltse fel az új tanúsítványt az alkalmazásba.
1. Az új tanúsítvány aktívvá tétele a Azure Active Directory-portálon.

A következő két szakaszban elvégezheti ezeket a lépéseket.

### <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Először hozzon létre és mentsen új tanúsítványt más lejárati dátummal:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/). Megjelenik a **Azure Active Directory felügyeleti központ** lap.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókjában a vállalati alkalmazások listája.
1. Válassza ki az érintett alkalmazást. Megjelenik az alkalmazás áttekintő lapja.
1. Az alkalmazás – Áttekintés lap bal oldali ablaktábláján válassza az **egyszeri bejelentkezés** lehetőséget.
1. Ha megjelenik az **egyszeri bejelentkezési módszer kiválasztása** lap, válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-előnézettel** lapon keresse meg az **SAML-aláíró tanúsítvány** fejlécét, és válassza a **Szerkesztés** ikont (egy ceruza). Megjelenik az **SAML aláíró tanúsítvány** lapja, amely megjeleníti az egyes tanúsítványok állapotát (**aktív** vagy **inaktív**), a lejárati dátumot és az ujjlenyomatot (a kivonatoló karakterláncot).
1. Válassza az **új tanúsítvány** lehetőséget. Egy új sor jelenik meg a tanúsítványok listája alatt, ahol a lejárati dátum alapértelmezett értéke pontosan három év az aktuális dátum után. (A módosítások még nem lettek mentve, így továbbra is módosíthatja a lejárati dátumot.)
1. Az új tanúsítvány sorában vigye a kurzort a lejárat dátuma oszlop fölé, és válassza a **dátum kiválasztása** ikont (a naptárat). Ekkor megjelenik egy naptár vezérlőelem, amely megjeleníti az új sor aktuális lejárati dátumának napját.
1. Az új dátum beállításához használja a naptár vezérlőt. Az aktuális dátum és az aktuális dátumot követő három év közötti időpontot állíthatja be.
1. Kattintson a **Mentés** gombra. Az új tanúsítvány mostantól **inaktív** állapottal, a választott lejárati dátummal és egy ujjlenyomattal jelenik meg.
1. Az **X** érték kiválasztásával térjen vissza a **Set up Single Sign-On for SAML-Preview** oldalára.

### <a name="upload-and-activate-a-certificate"></a>Tanúsítvány feltöltése és aktiválása

Ezután töltse le az új tanúsítványt a megfelelő formátumban, töltse fel az alkalmazásba, és tegye aktívvá a Azure Active Directoryban:

1. Az alkalmazás további SAML-bejelentkezési konfigurációs utasításait az alábbiak szerint tekintheti meg:

   - Válassza ki a **konfigurációs útmutató** hivatkozását egy külön böngészőablakban vagy lapon való megjelenítéshez, vagy
   - Nyissa **meg** a címsort, és válassza a megtekintés **lépésről lépésre** egy oldalsávon.

1. Az utasításokban jegyezze fel a tanúsítvány feltöltéséhez szükséges kódolási formátumot.
1. Kövesse az [automatikusan generált tanúsítvány a katalógushoz és a nem Gallery-alkalmazásokhoz](#auto-generated-certificate-for-gallery-and-non-gallery-applications) című szakasz utasításait. Ez a lépés letölti a tanúsítványt az alkalmazás feltöltéséhez szükséges kódolási formátumban.
1. Ha át szeretné térni az új tanúsítványra, lépjen vissza az **SAML aláíró tanúsítvány** lapra, és az újonnan mentett tanúsítvány sorban válassza a három pontot (**...**), majd válassza a **tanúsítvány aktívvá tétele** lehetőséget. Az új tanúsítvány állapota **aktívra** változik, és a korábban aktív tanúsítvány **inaktív** állapotra változik.
1. Folytassa az alkalmazás SAML-bejelentkezési konfigurációs utasításait, amelyeket korábban megjelenített, így feltöltheti az SAML-aláíró tanúsítványt a megfelelő kódolási formátumba.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mail értesítési címek hozzáadása a tanúsítvány lejáratához

Az Azure AD e-mailben értesítést küld a 60, 30 és 7 nappal az SAML-tanúsítvány lejárta előtt. Az értesítések fogadásához több e-mail-címet is hozzáadhat. Az értesítések küldéséhez használandó e-mail-cím (ek) megadása:

1. Az **SAML aláíró tanúsítvány** lapon lépjen az **értesítési e-mail címek** fejlécre. Alapértelmezés szerint ez a fejléc csak az alkalmazást hozzáadó rendszergazda e-mail-címét használja.
1. A végső e-mail-cím alatt írja be azt az e-mail-címet, amelyre a tanúsítvány lejárati értesítése érkezik, majd nyomja le az ENTER billentyűt.
1. Ismételje meg az előző lépést minden hozzáadni kívánt e-mail-cím esetében.
1. Minden törölni kívánt e-mail-cím esetében válassza a **Törlés** ikont (a szemetet is) az e-mail-cím mellett.
1. Kattintson a **Mentés** gombra.

Az értesítő e-mailt a következő címről küldi: aadnotification@microsoft.com . Ha el szeretné kerülni, hogy az e-mail a levélszemét helyére kerüljön, vegye fel ezt az e-mailt a névjegyalbumba.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Hamarosan lejáró tanúsítvány megújítása

Ha egy tanúsítvány hamarosan lejár, megújíthatja azt egy olyan eljárással, amely nem eredményez jelentős állásidőt a felhasználók számára. Lejáró tanúsítvány megújítása:

1. Kövesse az [új tanúsítvány létrehozása](#create-a-new-certificate) című szakasz utasításait a meglévő tanúsítvánnyal átfedésben lévő dátum használatával. Ez a dátum korlátozza a tanúsítvány lejárata által okozott állásidő mennyiségét.
1. Ha az alkalmazás automatikusan átadhat egy tanúsítványt, az alábbi lépéseket követve állítsa az új tanúsítványt aktívra:
   1. Térjen vissza az **SAML aláíró tanúsítvány** lapjára.
   1. Az újonnan mentett tanúsítvány sorban válassza a három pontot (**..**.), majd válassza a **tanúsítvány aktívvá tétele** lehetőséget.
   1. Ugorja át a következő két lépést.

1. Ha az alkalmazás egyszerre csak egy tanúsítványt tud kezelni, válasszon egy állásidőt a következő lépés végrehajtásához. (Ellenkező esetben, ha az alkalmazás nem tudja automatikusan felvenni az új tanúsítványt, de több aláíró tanúsítványt is kezelhet, bármikor végrehajthatja a következő lépést.)
1. A régi tanúsítvány lejárta előtt kövesse a [tanúsítvány feltöltése és aktiválása](#upload-and-activate-a-certificate) című szakasz utasításait.
1. Jelentkezzen be az alkalmazásba, és győződjön meg arról, hogy a tanúsítvány megfelelően működik.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
- [Alkalmazásfelügyelet az Azure Active Directoryval](what-is-application-management.md)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](what-is-single-sign-on.md)
- [Az Azure Active Directoryban található alkalmazásokba történő SAML-alapú egyszeri bejelentkezés hibaelhárítása](./debug-saml-sso-issues.md)