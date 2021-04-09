---
title: Atlassian JIRA/torkolati felügyeleti útmutató – Azure Active Directory | Microsoft Docs
description: Felügyeleti útmutató a Atlassian-JIRA és a Azure Active Directory (Azure AD) összefolyásánál való használatához.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359157"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian-JIRA és-összefolyásánál felügyeleti útmutató a Azure Active Directory

## <a name="overview"></a>Áttekintés

A Azure Active Directory (Azure AD) egyszeri bejelentkezés (SSO) beépülő modulja lehetővé teszi, hogy a Microsoft Azure AD ügyfelei munkahelyi vagy iskolai fiókjával jelentkezzenek be a Atlassian JIRA és a torkolatánál kiszolgáló-alapú termékekbe. SAML 2,0-alapú egyszeri bejelentkezést valósít meg.

## <a name="how-it-works"></a>Működés

Amikor a felhasználók be szeretnének jelentkezni a Atlassian JIRA vagy a torkolatánál futó alkalmazásba, a bejelentkezési oldalon megjelennek a **Bejelentkezés az Azure ad-vel** gomb. Ha kijelölik, akkor be kell jelentkezniük az Azure AD-szervezet bejelentkezési oldala (azaz munkahelyi vagy iskolai fiókja) használatával.

A felhasználók hitelesítése után képesnek kell lenniük az alkalmazásba való bejelentkezésre. Ha már hitelesítve vannak a munkahelyi vagy iskolai fiókjához tartozó AZONOSÍTÓval és jelszóval, akkor közvetlenül bejelentkeznek az alkalmazásba. 

A bejelentkezés JIRA és torkolatánál is működik. Ha a felhasználók bejelentkeznek a JIRA alkalmazásba, és a torkolatánál ugyanabban a böngészőablakban van megnyitva, nem kell megadniuk a másik alkalmazás hitelesítő adatait. 

A felhasználók a munkahelyi vagy iskolai fiókkal is elérhetik a Atlassian terméket az alkalmazásaikban. A hitelesítő adatok kérése nélkül kell bejelentkezniük.

> [!NOTE]
> A felhasználó üzembe helyezése nem a beépülő modulon keresztül történik.

## <a name="audience"></a>Célközönség

A JIRA és a torkolatánál lévő rendszergazdák a beépülő modullal engedélyezhetik az egyszeri bejelentkezést az Azure AD használatával.

## <a name="assumptions"></a>Feltételezések

* A JIRA és a torkolatánál lévő példányok HTTPS-kompatibilisek.
* A felhasználók már létre lettek hozva a JIRA vagy a torkolatánál.
* A felhasználók JIRA vagy összefolyásánál vannak hozzárendelve a szerepkörökhöz.
* A rendszergazdák hozzáférhetnek a beépülő modul konfigurálásához szükséges adatokhoz.
* A JIRA vagy a torkolatánál a vállalati hálózaton kívül is elérhető.
* A beépülő modul csak a JIRA és a torkolatánál található helyszíni verzióval működik.

## <a name="prerequisites"></a>Előfeltételek

A beépülő modul telepítése előtt jegyezze fel a következő információkat:

* A JIRA és a torkolatánál a Windows 64 bites verziója van telepítve.
* A JIRA és a torkolatánál lévő verziók HTTPS-kompatibilisek.
* A JIRA és a torkolatánál elérhető az interneten.
* Rendszergazdai hitelesítő adatok állnak rendelkezésre a JIRA és a torkolatánál.
* Rendszergazdai hitelesítő adatok vannak érvényben az Azure AD-hez.
* A websudo le van tiltva a JIRA és a torkolatánál.

## <a name="supported-versions-of-jira-and-confluence"></a>A JIRA és a torkolatánál támogatott verziói

A beépülő modul a JIRA és a torkolatánál következő verzióit támogatja:

* JIRA Core és Software: 6,0 – 7,12
* JIRA Service Desk: 3.0.0 – 3.5.0
* A JIRA a 5,2-es frissítést is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5,2](./jira52microsoft-tutorial.md)
* Összefolyásánál: 5,0 – 5,10
* Torkolatánál: 6.0.1
* Torkolatánál: 6.1.1
* Torkolatánál: 6.2.1
* Torkolatánál: 6.3.4
* Torkolatánál: 6.4.0
* Torkolatánál: 6.5.0
* Torkolatánál: 6.6.2
* Torkolatánál: 6.7.0
* Torkolatánál: 6.8.1
* Torkolatánál: 6.9.0
* Torkolatánál: 6.10.0
* Torkolatánál: 6.11.0
* Torkolatánál: 6.12.0

## <a name="installation"></a>Telepítés

A beépülő modul telepítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a JIRA vagy az összefolyásánál-példányba rendszergazdaként.

2. Nyissa meg a JIRA/torkolatánál felügyeleti konzolt, és válassza a **bővítmények** lehetőséget.

3. A Microsoft letöltőközpontból töltse le a JIRA Microsoft SAML SSO beépülő [modult](https://www.microsoft.com/download/details.aspx?id=56506)a /  [összefolyásánál](https://www.microsoft.com/download/details.aspx?id=56503).

   A beépülő modul megfelelő verziója megjelenik a keresési eredmények között.

4. Válassza ki a beépülő modult, és az univerzális beépülőmodul-kezelő (UPM) telepíti azt.

A beépülő modul telepítését követően megjelenik a **Bővítmények kezelése** a **felhasználók által telepített bővítmények** szakaszban.

## <a name="plug-in-configuration"></a>Beépülő modul konfigurálása

A beépülő modul használatának megkezdése előtt konfigurálnia kell azt. Válassza ki a beépülő modult, kattintson a **Konfigurálás** gombra, és adja meg a konfigurációs adatokat.

Az alábbi képen a JIRA és a torkolatánál található konfigurációs képernyő látható:

![Beépülő modul konfigurációs képernyője](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metaadatok URL-címe**: az összevonási metaadatok Azure ad-ből való lekérésére szolgáló URL-cím.

* **Azonosítók**: az az URL-cím, amelyet az Azure ad a kérelem forrásának ellenőrzésére használ. Az Azure AD **azonosító** eleméhez rendeli hozzá. A beépülő modul automatikusan az https:///-ként származtatja ezt az URL-címet *\<domain:port>* .

* **Válasz URL-címe**: a válasz URL-címe a identitásszolgáltató, amely kezdeményezi az SAML-bejelentkezést. Leképezi az Azure AD **Válasz URL-** elemét. A beépülő modul automatikusan származtatja ezt az URL-címet https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **Bejelentkezési URL-cím**: a identitásszolgáltató bejelentkezési URL-címe, amely az SAML-bejelentkezést kezdeményezi. Leképezi az Azure AD **bejelentkezési** elemét. A beépülő modul automatikusan származtatja ezt az URL-címet https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **Identitásszolgáltató-entitás azonosítója**: a identitásszolgáltató által használt entitás azonosítója. Ez a mező a metaadatok URL-címének feloldásakor töltődik fel.

* **Bejelentkezési URL-cím**: a bejelentkezési URL-cím a identitásszolgáltató. Ez a mező az Azure AD-ből van feltöltve a metaadatok URL-címének feloldásakor.

* **Kijelentkezési URL-cím**: a identitásszolgáltató kijelentkezési URL-címe. Ez a mező az Azure AD-ből van feltöltve a metaadatok URL-címének feloldásakor.

* **X. 509 tanúsítvány**: a identitásszolgáltató X. 509 tanúsítványa. Ez a mező az Azure AD-ből van feltöltve a metaadatok URL-címének feloldásakor.

* **Bejelentkezési gomb neve**: annak a bejelentkezési gombnak a neve, amelyet a szervezet a felhasználók számára látni szeretne a bejelentkezési oldalon.

* **SAML felhasználói azonosító** helye: az SAML-válaszban a JIRA vagy az összefolyásánál felhasználói azonosító helyét kell megvárni. Ez lehet a **NameID** vagy egy egyéni attribútum neve.

* **Attribútum neve**: annak az attribútumnak a neve, amelybe a rendszer a felhasználói azonosítót várta.

* A **Kezdőlap tartomány felderítésének engedélyezése**: a kijelölés, hogy a vállalat Active Directory összevonási szolgáltatások (AD FS) (AD FS) alapú bejelentkezést használjon.

* **Tartománynév**: a tartomány neve, ha a bejelentkezés AD FS alapul.

* **Egyszeri kijelentkezés engedélyezése**: ezt a lehetőséget kell választania, ha ki szeretne jelentkezni az Azure ad-ből, amikor egy felhasználó kijelentkezik a JIRA vagy a torkolatánál.

## <a name="troubleshooting"></a>Hibaelhárítás

* **Több tanúsítvány-hibaüzenetet kap**: Jelentkezzen be az Azure ad-be, és távolítsa el az alkalmazáson keresztül elérhető több tanúsítványt. Győződjön meg arról, hogy csak egy tanúsítvány van jelen.

* **A tanúsítvány hamarosan lejár az Azure ad-ben**: a bővítmények gondoskodnak a tanúsítvány automatikus átváltásáról. Ha egy tanúsítvány hamarosan lejár, egy új tanúsítványt kell megjelölni, és törölni kell a használaton kívüli tanúsítványokat. Ha a felhasználó ebben az esetben megpróbál bejelentkezni a JIRA, a beépülő modul lekéri és menti az új tanúsítványt.

* **Szeretné letiltani a websudo-t (a biztonságos rendszergazdai munkamenet letiltása)**:

  * A JIRA esetében a biztonságos rendszergazdai munkamenetek (azaz jelszó-megerősítés a felügyeleti funkciók elérése előtt) alapértelmezés szerint engedélyezve vannak. Ha el szeretné távolítani ezt a képességet a JIRA-példányban, adja meg a következő sort a JIRA-config. properties fájlban: `jira.websudo.is.disabled = true`

  * A torkolatánál való összefolyásánál kövesse az [összefolyásánál támogatási webhelyének](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)lépéseit.

* **A metaadatok URL-címével kitöltendő mezők nem lesznek feltöltve**:

  * Ellenőrizze, hogy helyes-e az URL-cím. Ellenőrizze, hogy a megfelelő bérlő és alkalmazás-azonosító van-e leképezve.

  * Adja meg az URL-címet egy böngészőben, és ellenőrizze, hogy az összevonási metaadatok XML-fájlja megjelenik-e.

* **Belső kiszolgálóhiba történt**: Ellenőrizze a naplókat a telepítés naplózási könyvtárában. Ha olyan hibaüzenetet kap, amikor a felhasználó az Azure AD SSO használatával próbál bejelentkezni, megoszthatja a naplókat a támogatási csapattal.

* **"Felhasználói azonosító nem található" hibaüzenet jelenik meg, amikor a felhasználó megpróbál bejelentkezni**: hozza létre a felhasználói azonosítót a JIRA vagy a torkolatánál.

* **"Az alkalmazás nem található" hibaüzenet jelenik meg az Azure ad-ben**: Ellenőrizze, hogy a megfelelő URL-cím van-e leképezve az alkalmazáshoz az Azure ad-ben.

* **Támogatásra van szüksége**: az [Azure ad SSO integrációs csapatának](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)elérhetősége. A csapat 24-48 munkaidőben válaszol.

  A Microsoft támogatási jegyét a Azure Portal csatornán keresztül is megteheti.

## <a name="plug-in-faq"></a>Beépülő modul – gyakori kérdések

Ha bármilyen lekérdezésre van szüksége a beépülő modullal kapcsolatban, tekintse meg az alábbi gyakori kérdéseket.

### <a name="what-does-the-plug-in-do"></a>Mit tesz a beépülő modul?

A beépülő modul egyszeri bejelentkezéses (SSO) képességet biztosít a Atlassian JIRA (beleértve a JIRA Core-t, a JIRA szoftvert, a JIRA-szolgáltatást és a helyszíni szoftvereket). A beépülő modul Azure Active Directory (Azure AD) identitás-szolgáltatóként (identitásszolgáltató) működik.

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Mely Atlassian-termékek működnek a beépülő modullal?

A beépülő modul a JIRA és a torkolatánál helyszíni verzióival működik.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Működik a beépülő modul a felhőalapú verziókon?

Nem. A beépülő modul csak a JIRA és a torkolatánál helyszíni verzióit támogatja.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>A JIRA és a torkolatánál mely verziói támogatják a beépülő modult?

A beépülő modul a következő verziókat támogatja:

* JIRA Core és Software: 6,0 – 7,12
* JIRA Service Desk: 3.0.0 – 3.5.0
* A JIRA a 5,2-es frissítést is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5,2](./jira52microsoft-tutorial.md)
* Összefolyásánál: 5,0 – 5,10
* Torkolatánál: 6.0.1
* Torkolatánál: 6.1.1
* Torkolatánál: 6.2.1
* Torkolatánál: 6.3.4
* Torkolatánál: 6.4.0
* Torkolatánál: 6.5.0
* Torkolatánál: 6.6.2
* Torkolatánál: 6.7.0
* Torkolatánál: 6.8.1
* Torkolatánál: 6.9.0
* Torkolatánál: 6.10.0
* Torkolatánál: 6.11.0
* Torkolatánál: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Ingyenes vagy fizetős a beépülő modul?

Ez egy ingyenes bővítmény.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>A beépülő modul üzembe helyezése után újra kell indítani a JIRA vagy a torkolatánál?

Újraindítás nem szükséges. Azonnal elkezdheti használni a beépülő modult.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hogyan kap támogatást a beépülő modulhoz?

Az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) elérheti a beépülő modulhoz szükséges támogatást. A csapat 24-48 munkaidőben válaszol.

A Microsoft támogatási jegyét a Azure Portal csatornán keresztül is megteheti.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Működik-e a beépülő modul a JIRA és a torkolatánál futó Mac vagy Ubuntu rendszeren?

A beépülő modult csak a JIRA és a torkolatánál elérhető, 64 bites Windows Server-példányokon teszteltük.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Működik a beépülő modul az Azure AD-től eltérő IDP?

Nem. Csak az Azure AD-vel működik.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Az SAML milyen verziója működik a beépülő modullal?

Az SAML 2,0-mel működik.

### <a name="does-the-plug-in-do-user-provisioning"></a>A beépülő modul a felhasználó üzembe helyezését végzi?

Nem. A beépülő modul csak SAML 2,0-alapú egyszeri bejelentkezést biztosít. A felhasználót az egyszeri bejelentkezéses bejelentkezés előtt kell kiépíteni az alkalmazásban.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Támogatja a beépülő modul a JIRA és a torkolatánál a fürt verzióját?

Nem. A beépülő modul a JIRA és a torkolatánál helyszíni verzióival működik.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Működik a beépülő modul a JIRA és a torkolatánál HTTP-verzióival?

Nem. A beépülő modul csak HTTPS-kompatibilis telepítésekkel működik.