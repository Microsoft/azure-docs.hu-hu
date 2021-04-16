---
title: Alkalmazáshitelesítés áthelyezése AD FS-Azure Active Directory
description: Megtudhatja, hogyan használhatja Azure Active Directory a Active Directory összevonási szolgáltatások (AD FS) (AD FS), hogy a felhasználók egyszeri bejelentkezést adjanak az összes alkalmazásukhoz.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: b0fd3dae2ff9c6de39462d19dc41a32ba51171e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534867"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Alkalmazáshitelesítés áthelyezése az Active Directory Federation Servicesből az Azure Active Directoryba

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) egy univerzális identitásplatformot kínál, amely egyetlen identitást biztosít a felhasználók, partnerek és ügyfelek számára az alkalmazások eléréséhez és az együttműködéshez bármilyen platformról és eszközről. Az Azure AD teljes [identitáskezelési képességekkel rendelkezik.](../fundamentals/active-directory-whatis.md) Ezeket az előnyöket az alkalmazáshitelesítés és az Azure AD-beli engedélyezés szabványosítása biztosítja.

> [!TIP]
> Ez a cikk fejlesztői közönség számára íródott. Az alkalmazások Azure AD-be való áthelyezését tervező projektvezetőknek és rendszergazdáknak érdemes megfontolni az Alkalmazáshitelesítés az Azure AD-be való [áttelepítését.](migrate-application-authentication-to-azure-active-directory.md)

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

Ha olyan helyszíni címtára van, amely felhasználói fiókokat tartalmaz, valószínűleg számos olyan alkalmazása van, amelyhez a felhasználók hitelesítést kérnek. Ezek az alkalmazások úgy vannak konfigurálva, hogy a felhasználók az identitásuk használatával férnek hozzá.

A felhasználók közvetlenül is hitelesíthetnek a helyi Active Directory. Active Directory összevonási szolgáltatások (AD FS) (AD FS) egy szabványalapú helyszíni identitásszolgáltatás. Kibővíti az egyszeri bejelentkezés (SSO) funkció használatát a megbízható üzleti partnerek között, így a felhasználóknak nem kell külön bejelentkezniük az egyes alkalmazásokba. Ezt összevont identitásnak nevezik.

Számos szervezet saját szoftverszolgáltatással (SaaS) vagy egyéni üzletági alkalmazásokkal is össze van egyesülve közvetlenül az AD FS-Microsoft 365 és Azure AD-alapú alkalmazások mellett.

  ![Közvetlenül a helyszínen csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Az alkalmazásbiztonság növelése érdekében a cél az, hogy egyetlen hozzáférés-vezérlési és szabályzatkészlet legyen elérhető a helyszíni és a felhőalapú környezetekben.

  ![Azure AD-kapcsolaton keresztül csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Az átemelni szükséges alkalmazások típusai

Az összes alkalmazáshitelesítés Azure AD-be való áttelepítése optimális, mivel egyetlen vezérlősíkot biztosít az identitás- és hozzáféréskezeléshez.

Az alkalmazások modern vagy örökölt protokollokat használhatnak a hitelesítéshez. Amikor az Azure AD-be való migrálást tervezi, fontolja meg a modern hitelesítési protokollokat (például SAML és Open ID Connect) felhasználó alkalmazások migrálását. Ezek az alkalmazások újrakonfigurálhatóak az Azure AD-val való hitelesítésre az Azure App Gallery beépített összekötőivel vagy az alkalmazás Azure AD-ben való regisztrálásával. A régebbi protokollokat használó alkalmazások integrálhatók a alkalmazásproxy.

További információkért lásd:

* [Az Azure AD alkalmazásproxy helyszíni](what-is-application-proxy.md)alkalmazások közzétételére a távoli felhasználók számára.
* [Mi az alkalmazáskezelés?](what-is-application-management.md)
* [AD FS-tevékenységjelentést az alkalmazások Azure AD-be való áttelepítéséhez.](migrate-adfs-application-activity.md)
* [Figyelje AD FS az Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)használatával.

### <a name="the-migration-process"></a>A migrálási folyamat

Az alkalmazáshitelesítés Azure AD-be való áthelyezésének folyamata során tesztelje az alkalmazásokat és a konfigurációt. Javasoljuk, hogy az éles környezetbe való áttéréskor továbbra is használja a meglévő tesztkörnyezeteket a migrálási teszteléshez. Ha a tesztkörnyezet jelenleg nem érhető el, [](https://azure.microsoft.com/services/app-service/) az alkalmazás architektúrája alapján Azure App Service vagy [az Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)használatával állíthat be egyet.

Dönthet úgy, hogy külön Teszt Azure AD-bérlőt hoz létre az alkalmazáskonfigurációk fejlesztéséhez.

A migrálási folyamat a következő lehet:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>1. fázis – Aktuális állapot: Az éles alkalmazás hitelesítése AD FS

  ![1. migrálási szakasz ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>2. fázis – (nem kötelező) Az alkalmazás tesztpéldányának a teszt Azure AD-bérlőre való mutatás

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztpéldányát egy teszt Azure AD-bérlőre mutasson, és végreteheti a szükséges módosításokat. Az alkalmazás tesztelheti a teszt Azure AD-bérlő felhasználóival. A fejlesztési folyamat során olyan eszközökkel hasonlíthatja össze és ellenőrizheti a kéréseket és válaszokat, mint a [Fiddler.](https://www.telerik.com/fiddler)

Ha nem lehetséges külön tesztbérlőt beállítani, hagyja ki ezt a szakaszt, és mutasson az alkalmazás egy tesztpéldányára az éles Azure AD-bérlőre az alábbi 3. szakaszban leírtak szerint.

  ![2. migrálási szakasz ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>3. fázis – Az alkalmazás tesztpéldányának mutatás az éles Azure AD-bérlőre

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztpéldányát az éles Azure AD-bérlőre mutasson. Most már tesztelhet az éles bérlő felhasználóival. Ha szükséges, tekintse át a felhasználók átváltását ismertető szakaszt.

  ![3. migrálási fázis ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>4. fázis – Az éles alkalmazás éles Azure AD-bérlőre mutat

Frissítse az éles alkalmazás konfigurációját úgy, hogy az éles Azure AD-bérlőre mutasson.

  ![4. migrálási szakasz ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 A hitelesítést AD FS alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. A [Azure AD Connect szinkronizálásával szinkronizálhatja](../hybrid/how-to-connect-sync-whatis.md) az identitásadatokat a helyszíni környezet és az Azure AD között a migrálás megkezdése előtt. A migrálás előtt ellenőrizze ezeket a csoportokat és tagságot, hogy hozzáférést biztosítson ugyanazoknak a felhasználóknak az alkalmazás áttelepítésekor.

### <a name="line-of-business-apps"></a>Üzletági alkalmazások

Üzletági alkalmazásai azok, amelyek a szervezet által kifejlesztettek, vagy amelyek szabványos csomagolt termékek. Ilyenek például a Windows Identity Foundationre és a SharePoint-alkalmazásokra (nem a SharePoint Online-on) készült alkalmazások.

Az OAuth 2.0-t, OpenID Connect vagy WS-Federation alkalmazásregisztrációkként integrálhatók az Azure [AD-be.](../develop/quickstart-register-app.md) Az SAML 2.0-s vagy WS-Federation [](add-application-portal.md) alkalmazásokat nem katalógusbeli alkalmazásként használva integrálhatja a vállalati alkalmazások lapját a [Azure Portal.](https://portal.azure.com/)

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés

Az SAML 2.0-s hitelesítést használó alkalmazások [SAML-alapú](what-is-single-sign-on.md) egyszeri bejelentkezéshez (SSO) konfigurálhatóak. Az SAML-alapú SSO-val a felhasználókat az SAML-jogcímekben meghatározott szabályok alapján adott alkalmazás-szerepkörökhöz lehet leképezni.

SaaS-alkalmazás SAML-alapú egyszeri bejelentkezéshez való konfigurálásához lásd: Rövid [útmutató: SAML-alapú](add-application-portal-setup-sso.md)egyszeri bejelentkezés beállítása.

  ![Az SSO SAML felhasználói képernyőképei ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Számos SaaS-alkalmazáshoz van egy [alkalmazásspecifikus](../saas-apps/tutorial-list.md) oktatóanyag, amely végigveszi az SAML-alapú SSO konfigurálásán.

  ![alkalmazás oktatóanyag](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Egyes alkalmazások migrálása egyszerű. Az összetettebb követelményekkel (például egyéni jogcímekkel) kapcsolatos alkalmazások további konfigurálást igényelnek az Azure AD-ban és/vagy Azure AD Connect. A támogatott jogcím-leképezésekkel kapcsolatos információkért [lásd: How to: Customize claimsmitted in tokens for a specific app in a tenant (Preview) (A](../develop/active-directory-claims-mapping.md)bérlő egy adott alkalmazásához (előzetes verzió) kibocsátott jogcímek testreszabása).

Az attribútumok leképezésekor tartsa szem előtt a következő korlátozásokat:

* Az Azure AD-ban nem AD FS kibocsátható összes attribútum SAML-jogkivonatba bocsátható attribútumként, még akkor sem, ha ezek az attribútumok szinkronizálva vannak. Az attribútum szerkesztésekor az **Érték** legördülő lista megjeleníti az Azure AD-ban elérhető különböző attribútumokat. Ellenőrizze [Azure AD Connect szinkronizálási](../hybrid/how-to-connect-sync-whatis.md) témakörök konfigurációját, és győződjön meg arról, hogy a szükséges attribútum **(például samAccountName)** szinkronizálva van az Azure AD-be. A bővítményattribútumok használatával bármilyen olyan jogcímet kibocsáthat, amely nem része az Azure AD standard felhasználói sémának.
* A leggyakoribb forgatókönyvekben csak a **NameID** jogcím és egyéb általános felhasználóazonosító jogcímek szükségesek az alkalmazáshoz. Annak megállapításához, hogy szükség van-e további jogcímre, vizsgálja meg, milyen jogcímeket ad ki a AD FS.
* Nem minden jogcímet lehet kiállítani, mert egyes jogcímek az Azure AD-ban vannak védve.
* A titkosított SAML-jogkivonatok használatának lehetősége jelenleg előzetes verzióban érhető el. Lásd: How to: customize claims issued in the SAML token for enterprise applications (Az SAML-jogkivonatban kiadott [jogcímek testreszabása vállalati alkalmazásokhoz).](../develop/active-directory-saml-claims-customization.md)

### <a name="software-as-a-service-saas-apps"></a>Szolgáltatott szoftver (SaaS) alkalmazások

Ha a felhasználók SaaS-alkalmazásokba (például Salesforce, ServiceNow vagy Workday) jelentkeznek be, és integrálva vannak az AD FS-val, összevont bejelentkezést használ SaaS-alkalmazásokhoz.

A legtöbb SaaS-alkalmazás konfigurálható az Azure AD-ban. A Microsoft számos előre konfigurált kapcsolatot biztosít az SaaS-alkalmazásokhoz az  [Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)alkalmazáskatatárban, ami megkönnyíti a váltást. Az SAML 2.0-alkalmazások az Azure AD alkalmazásgyűjteményen keresztül vagy nem katalógusbeli alkalmazásként integrálhatók az Azure [AD-be.](add-application-portal.md)

Az OAuth 2.0-t vagy OpenID Connect alkalmazásregisztrációkként hasonló módon integrálhatók az Azure [AD-be.](../develop/quickstart-register-app.md) Az örökölt protokollokat felhasználó alkalmazások az [Azure AD alkalmazásproxy](application-proxy.md) az Azure AD-val való hitelesítéshez.

Az SaaS-alkalmazások felvételekor bármilyen probléma esetén forduljon a [SaaS-alkalmazásintegráció támogatási aliasához.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

### <a name="saml-signing-certificates-for-sso"></a>SAML-aláíró tanúsítványok SSO-hez

Az aláíró tanúsítványok minden SSO-telepítés fontos részét képezi. Az Azure AD létrehozza az aláíró tanúsítványokat, hogy SAML-alapú összevont SSO-t hozzon létre az SaaS-alkalmazásokhoz. Miután hozzáadta a katalógusban vagy a nem katalógusban található alkalmazásokat, az összevont SSO beállítással konfigurálhatja a hozzáadott alkalmazást. Lásd: [Tanúsítványok kezelése összevont egyszeri](manage-certificates-for-federated-single-sign-on.md)bejelentkezéshez Azure Active Directory.

### <a name="saml-token-encryption"></a>SAML-jogkivonat titkosítása

Az AD FS és az Azure AD egyaránt biztosít jogkivonat-titkosítást – ez a képesség az alkalmazásokra vonatkozó SAML biztonsági helyességi feltétel titkosításának képessége. A helyességi feltételt a rendszer egy nyilvános kulccsal titkosítja, és a fogadó alkalmazás visszafejti a megfelelő titkos kulccsal. A tokentitkosítás konfigurálásakor X.509-tanúsítványfájlokat tölt fel a nyilvános kulcsokhoz.

Az Azure AD SAML-jogkivonatok titkosításának és konfigurálásának további információiért [lásd: How to: Configure Azure AD SAML token encryption (Az Azure AD SAML-jogkivonat](howto-saml-token-encryption.md)titkosításának konfigurálása).  

> [!NOTE]
> A jogkivonat-titkosítás egy Azure Active Directory (Azure AD) prémium szintű szolgáltatás. További információ az Azure AD kiadásairól, funkcióiról és díjszabásról: [Az Azure AD díjszabása.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Ma áthelyezható alkalmazások és konfigurációk

Az egyszerűen áthelyezhető alkalmazások közé tartozik az SAML 2.0-alkalmazások is, amelyek a konfigurációs elemek és jogcímek szabványos készletét használják. Ezek a standard elemek a következőek:

* Felhasználó egyszerű neve
* E-mail-cím
* Utónév
* vezetéknév;
* Az SAML **NameID**-ként használt alternatív attribútum, például az Azure AD Mail attribútum, a Mail előtag, az alkalmazott azonosítója, az 1-15 bővítményattribútumok vagy a helyszíni **SamAccountName**. További információkat itt talál: A [NameIdentifier](../develop/active-directory-saml-claims-customization.md) jogcím szerkesztése.
* Egyéni jogcímek.

A következők további konfigurációs lépéseket igényelnek az Azure AD-be való áttelepítéshez:

* Egyéni hitelesítési vagy többtényezős hitelesítési (MFA-) szabályok AD FS. Ezeket az Azure AD feltételes hozzáférés [funkcióval konfigurálhatja.](../conditional-access/overview.md)
* Több válasz URL-végponttal rendelkező alkalmazások. Ezeket az Azure AD-ban a PowerShell vagy a Azure Portal konfigurálhatja.
* A WS-Federationt használó alkalmazások, például a SharePoint-alkalmazások, amelyek SAML 1.1-es verziójú jogkivonatot igényelnek. Ezeket manuálisan konfigurálhatja a PowerShell használatával. A katalógusból előre integrált általános sablont is hozzáadhat SharePoint- és SAML 1.1-alkalmazásokhoz. Az SAML 2.0 protokollt támogatjuk.
* Az összetett jogcímek kiállítása átalakítja a szabályokat. A támogatott jogcím-leképezésekkel kapcsolatos információkért lásd:
   *  [Jogcím-leképezés a Azure Active Directory.](../develop/active-directory-claims-mapping.md)
   * Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek [testreszabása a Azure Active Directory.](../develop/active-directory-saml-claims-customization.md)

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Az Azure AD-ben jelenleg nem támogatott alkalmazások és konfigurációk

Bizonyos képességeket igénylő alkalmazások jelenleg nem migrálhatóak.

#### <a name="protocol-capabilities"></a>Protokollképességek

Az alábbi protokollképességeket igénylő alkalmazások jelenleg nem migrálhatóak:

* A WS-Trust ActAs minta támogatása
* Az SAML-összetevő felbontása
* Aláírt SAML-kérelmek aláírás-ellenőrzése
  > [!Note]
  > A rendszer elfogadja az aláírt kéréseket, de az aláírás nincs ellenőrizve.

  Mivel az Azure AD csak az alkalmazásban előre konfigurált végpontokra adja vissza a jogkivonatot, az aláírás-ellenőrzésre a legtöbb esetben valószínűleg nincs szükség.

#### <a name="claims-in-token-capabilities"></a>Jogcímek a jogkivonat képességeiben

A jogkivonat-képességekben az alábbi jogcímeket igénylő alkalmazások jelenleg nem migrálhatóak.

* Az attribútumból származó jogcímek nem az Azure AD-címtárból vannak tárolva, kivéve, ha az adatok szinkronizálva vannak az Azure AD-be. További információ: [Az Azure AD synchronization API áttekintése.](/graph/api/resources/synchronization-overview)
* Többértékes címtárattribútumok kiállítása. Jelenleg például nem lehet többértékű jogcímet kivinni a proxycímek számára.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Alkalmazásbeállítások leképezés AD FS Azure AD-be

A migráláshoz fel kell értékelni az alkalmazás helyszíni konfigurálási módszerét, majd leképezni kell ezt a konfigurációt az Azure AD-be. Az AD FS és az Azure AD hasonlóan működik, így a megbízhatóság konfigurálásával, a bejelentkezési és kijelentkezési URL-címekkel és az azonosítókkal kapcsolatos fogalmak mindkettőre vonatkoznak. Dokumentálja AD FS alkalmazás konfigurációs beállításait, hogy könnyedén konfigurálni tudja őket az Azure AD-ban.

### <a name="map-app-configuration-settings"></a>Alkalmazáskonfigurációs beállítások leképezés

Az alábbi táblázat a függő AD FS megbízhatósága és az Azure AD Enterprise Application közötti beállítások leggyakoribb leképezését ismerteti:

* AD FS – Keresse meg a beállítást az AD FS megbízható függő AD FS alkalmazásban. Kattintson a jobb gombbal a függő félre, és válassza a Tulajdonságok lehetőséget.
* Azure AD – A beállítás Azure Portal [az](https://portal.azure.com/) egyes alkalmazások SSO-tulajdonságaiban.

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ban| SAML-jogkivonat |
| - | - | - | - |
| **Alkalmazás bejelentkezési URL-címe** <p>Az URL-cím, amely alapján a felhasználó bejelentkezik az alkalmazásba egy szolgáltató (SP) által kezdeményezett SAML-folyamatba.| N/A| SamL-alapú bejelentkezésből nyissa meg az SAML-alapkonfigurációt| N/A |
| **Alkalmazás válasz URL-címe** <p>Az alkalmazás URL-címe az identitásszolgáltató (IdP) szempontjából. Az internetszolgáltató ideküldi a felhasználót és a jogkivonatot, miután a felhasználó bejelentkezett az internetszolgáltatóba.  Ez más néven **SAML helyességi feltétel fogyasztói végpontja.**| Válassza **a Végpontok** lapot| SamL-alapú bejelentkezésből nyissa meg az SAML-alapkonfigurációt| Az SAML-jogkivonat céleleme. Példaérték: `https://contoso.my.salesforce.com` |
| **Alkalmazás kijelentkezési URL-címe** <p>Erre az URL-címre küldi a rendszer a kijelentkezés tisztítási kéréseit, amikor egy felhasználó kijelentkezik egy alkalmazásból. Az internetszolgáltató elküldi a kérést, hogy a felhasználót az összes többi alkalmazásból is kijelentkeztetje.| Válassza **a Végpontok** lapot| SamL-alapú bejelentkezésből nyissa meg az SAML-alapkonfigurációt| N/A |
| **Alkalmazásazonosító** <p>Ez az alkalmazásazonosító az internetszolgáltató szemszögéből. Gyakran (de nem mindig) a bejelentkezési URL-cím az azonosító.  Néha az alkalmazás ezt "entitásazonosítónak" nevezi.| Válassza az **Azonosítók** lapot|Az SAML-alapú bejelentkezés alapszintű SAML-konfigurációjának megnyitása| Az **SAML-jogkivonat Célközönség** elemére van leképezve. |
| **Alkalmazás összevonási metaadatai** <p>Ez az alkalmazás összevonási metaadatainak helye. Az identitásszolgáltató használja egyes konfigurációs beállítások, például a végpontok vagy a titkosítási tanúsítványok automatikus frissítéséhez.| Válassza a **Figyelés** lapot| N/A. Az Azure AD nem támogatja az alkalmazás-összevonási metaadatok közvetlen alkalmazását. Az összevonási metaadatokat manuálisan is importálhatja.| N/A |
| **Felhasználói azonosító/névazonosító** <p>A felhasználó identitását az Azure AD-ből vagy az AD FS-ből az alkalmazás felé egyértelműen azonosító attribútum.  Ez az attribútum általában a felhasználó UPN-jét vagy e-mail-címét tartalmaz.| Jogcímszabályok. A jogcímszabály a legtöbb esetben a **NameIdentifier** névre végződő típussal állít ki jogcímet.| Az azonosítót a Felhasználói attribútumok és jogcímek fejléc **alatt találja.** Alapértelmezés szerint a rendszer az UPN-t használja| Leképezi az **SAML-jogkivonat NameID** elemét. |
| **Egyéb jogcímek** <p>Az internetszolgáltató által az alkalmazásnak gyakran küldött jogcíminformációk közé tartozik például a vezetéknév, a vezetéknév, az e-mail-cím és a csoporttagság.| Az AD FS-ben ez a függő entitásra vonatkozó egyéb jogcímszabályokként található meg.| Az azonosítót a User Attributes (Felhasználói attribútumok) **fejléc alatt, a Claims (Jogcímek) & találja.** Válassza ki az **Egyéb felhasználói attribútumok megtekintése** és szerkesztése elemet.| N/A |

### <a name="map-identity-provider-idp-settings"></a>Identitásszolgáltató (IdP) beállításainak leképezása

Konfigurálja az alkalmazásokat úgy, hogy az Azure AD-AD FS az SSO-hoz. Itt az SAML protokollt használó SaaS-alkalmazásokra összpontosítunk. Ez a fogalom azonban az egyéni üzletági alkalmazásokra is kiterjed.

> [!NOTE]
> Az Azure AD konfigurációs értékei azt a mintát követik, amelyben az Azure-bérlő azonosítója lecseréli a {bérlőazonosító} azonosítót, az alkalmazásazonosító pedig az {alkalmazásazonosító} helyére. Ezt az információt [](https://portal.azure.com/) a Tulajdonságok Azure Portal **Azure Active Directory > találja:**

* A bérlőazonosítót a Címtár-azonosító kiválasztásával láthatja.
* Az alkalmazásazonosítót az Alkalmazásazonosító kiválasztásával láthatja.

 Magas szinten leképezi az alábbi kulcsfontosságú SaaS-alkalmazások konfigurációs elemeit az Azure AD-be.

| Elem| Konfigurációs érték |
| - | - |
| Identitásszolgáltató kibocsátója| https: \/ /sts.windows.net/{bérlőazonosító}/ |
| Identitásszolgáltató bejelentkezési URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Identitásszolgáltató kijelentkezésÉNEK URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Összevonási metaadatok helye| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>SaaS-alkalmazások SSO-beállításainak leképezés

Az SaaS-alkalmazásoknak tudják, hogy hová kell küldeni a hitelesítési kérelmeket, és hogyan kell ellenőrizni a fogadott jogkivonatokat. Az alábbi táblázat ismerteti az alkalmazás SSO-beállításainak konfigurálásához szükséges elemeket, valamint azok értékeit vagy helyét a AD FS Azure AD-n belül

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ban |
| - | - | - |
| **Az internetszolgáltató bejelentkezési URL-címe** <p>Az idP bejelentkezési URL-címe az alkalmazás szemszögéből (ahová a rendszer átirányítja a felhasználót a bejelentkezéshez).| A AD FS URL-cím az összevonási AD FS neve, amelyet az "/adfs/ls/" követ. <p>Például: `https://fs.contoso.com/adfs/ls/`| Cserélje le a {tenant-id} helyére a bérlőazonosítóját. <p> AZ SAML-P protokollt használva: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>A protokollt WS-Federation alkalmazások esetén: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Az internetszolgáltató kijelentkező URL-címe**<p>Az internetszolgáltató bejelentkezési URL-címe az alkalmazás szemszögéből (ahová a rendszer átirányítja a felhasználót, amikor kijelentkezik az alkalmazásból).| A kijelentkezés URL-címe megegyezik a bejelentkezési URL-címével, vagy ugyanaz az URL-cím, a "wa=wsignout1.0" hozzáfűzve. Például: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Cserélje le a {tenant-id} helyére a bérlőazonosítóját.<p>AZ SAML-P protokollt használva:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> A protokollt WS-Federation alkalmazások esetén: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Jogkivonat-aláíró tanúsítvány**<p>Az internetszolgáltató a tanúsítvány titkos kulcsát használja a kiállított jogkivonatok aláíráshoz. Igazolja, hogy a jogkivonat attól az identitásszolgáltatótól származik, amellyel az alkalmazás megbízhatósági kapcsolata konfigurálva van.| Az AD FS jogkivonat-aláíró tanúsítványa az AD FS-kezelőben a **Tanúsítványok** területen található.| A következő fejlécben Azure Portal az alkalmazás egyszeri bejelentkezési tulajdonságai között, az SAML **aláíró** **tanúsítvány fejléc alatt.** Innen letöltheti a tanúsítványt, hogy feltöltse az alkalmazásba.  <p>Ha az alkalmazás egynél több tanúsítvánnyal rendelkezik, az összes tanúsítványt megtalálja az összevonási metaadatok XML-fájljában. |
| **Azonosító/ "kiállító"**<p>Az idP azonosítója az alkalmazás szemszögéből (más néven kiállítóazonosító).<p>Az SAML-jogkivonatban az érték Kiállító elemként jelenik meg.| Az azonosító AD FS általában az összevonási szolgáltatás azonosítója az AD FS Management szolgáltatásban a **Szolgáltatás > Tulajdonságok szerkesztése összevonási szolgáltatás alatt.** Például: `http://fs.contoso.com/adfs/services/trust`| Cserélje le a {tenant-id} helyére a bérlőazonosítóját.<p>https: \/ /sts.windows.net/{bérlőazonosító}/ |
| **Az idP összevonási metaadatai**<p>Az internetszolgáltató nyilvánosan elérhető összevonási metaadatainak helye. (Az összevonási metaadatokat egyes alkalmazások alternatív megoldásként használják, hogy a rendszergazdának ne kelljen egyenként konfigurálnia az URL-címeket, azonosítókat és jogkivonat-aláíró tanúsítványokat.)| Keresse meg AD FS összevonási metaadatok URL-címét az AD FS Managementben a **Szolgáltatásvégpont> végpont és > Metaadatok > típusa: Összevonási metaadatok** alatt. Például: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Az Azure AD megfelelő értéke a következő mintát [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) követi: . Cserélje le a {TenantDomainName} helyére a bérlő nevét "contoso.onmicrosoft.com" contoso.onmicrosoft.com.   <p>További információkat itt talál: [Összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>A AD FS szabályzatok ábrázolása az Azure AD-ban

Az alkalmazáshitelesítés Azure AD-be való áthelyezéséhez hozzon létre leképezéseket a meglévő biztonsági szabályzatból az Azure AD-ban elérhető egyenértékű vagy alternatív változatokra. Ha gondoskodik arról, hogy ezek a leképezések az alkalmazás tulajdonosai által előírt biztonsági szabványoknak való megfelelés mellett is lekértek, azzal jelentősen megkönnyíti az alkalmazás migrálásának többi részét.

Minden szabály példához megmutatjuk, hogyan néz ki a szabály AD FS, a AD FS nyelvnek megfelelő kódot, és hogy ez hogyan van leképezve az Azure AD-be.

### <a name="map-authorization-rules"></a>Engedélyezési szabályok leképezése

Az alábbiakban példákat talál az engedélyezési szabályok különböző típusaira a AD FS, és hogy ezek hogyan vannak leképezve az Azure AD-be.

#### <a name="example-1-permit-access-to-all-users"></a>1. példa: Hozzáférés engedélye az összes felhasználó számára

Hozzáférés-hez való hozzáférés a következő AD FS:

  ![Képernyőkép az Egyszeres alkalmazás Sign-On SAML-sel párbeszédpanelről.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Ez az alábbi módokon leképezi az Azure AD-t:

1. Állítsa **a Felhasználó-hozzárendelés szükséges beállítását** a **Nem beállításra.**

    ![SaaS-alkalmazások hozzáférés-vezérlési szabályzatának szerkesztése ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Ha a **Felhasználó-hozzárendelés szükséges** beállítást **Igen-nek** kell megadni, a felhasználóknak hozzá kell rendelniük az alkalmazáshoz a hozzáféréshez. Ha a Nem **beállításra van** állítva, minden felhasználó rendelkezik hozzáféréssel. Ez a kapcsoló nem azt szabályozhatja, hogy a felhasználók mit látnak a **Saját alkalmazások** felhasználói élményben.

1. A Felhasználók **és csoportok lapon rendelje** hozzá az alkalmazást a Minden felhasználó **automatikus** csoporthoz. Ahhoz, [hogy az alapértelmezett](../enterprise-users/groups-create-rule.md) **Minden** felhasználó csoport elérhető legyen, engedélyeznie kell a dinamikus csoportokat az Azure AD-bérlőben.

    ![Saját SaaS-alkalmazások az Azure AD-ban ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>2. példa: Csoport explicit engedélyezése

Explicit csoportengedélyezési a AD FS:

  ![A Tartománygazdák jogcímszabályának engedélyezése párbeszédpanel képernyőképe.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

A szabály leképezés az Azure AD-be:

1. A [Azure Portal](https://portal.azure.com/) [hozzon létre egy felhasználói](../fundamentals/active-directory-groups-create-azure-portal.md) csoportot, amely megfelel a felhasználók csoportjának a AD FS.
1. Alkalmazásengedélyek hozzárendelése a csoporthoz:

    ![Hozzárendelés hozzáadása ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>3. példa: Adott felhasználó jogosultsága

Explicit felhasználói hitelesítés a AD FS:

  ![Képernyőkép a Szabály szerkesztése párbeszédpanelről, amely az Elsődleges S ID bejövő jogcímtípussal megadott felhasználói jogcímszabály engedélyezése.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

A szabály leképezés az Azure AD-be:

* A [Azure Portal](https://portal.azure.com/)adjon hozzá egy felhasználót az alkalmazáshoz az alkalmazás Hozzárendelés hozzáadása lapján az alábbi módon:

    ![Saját SaaS-alkalmazások az Azure-ban ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Többtényezős hitelesítési szabályok leképezés

A multi-factor [authentication (MFA)](../authentication/concept-mfa-howitworks.md) és a AD FS helyszíni telepítése továbbra is működik a migrálás után, mivel összevonták a AD FS. Érdemes lehet azonban áttérni az Azure beépített MFA-képességeire, amelyek az Azure AD feltételes hozzáférési munkafolyamataihoz kötődnek.

Az alábbiakban példákat talál az MFA-szabályok típusaira a AD FS, és hogy hogyan lehet leképezni őket az Azure AD-be különböző feltételek alapján.

MFA-szabálybeállítások a AD FS:

  ![Képernyőkép az Azure A D feltételeiről a Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>1. példa: MFA kényszerítés felhasználók/csoportok alapján

A felhasználók/csoportok választója egy olyan szabály, amely lehetővé teszi az MFA csoportonkénti (csoport biztonsági azonosító) vagy felhasználónkénti (elsődleges SID) alapján való kényszerítét. A felhasználók/csoportok hozzárendelései mellett az AD FS MFA-konfiguráció felhasználói felületének minden további jelölőnégyzete további szabályként működik, amelyek kiértékelése a felhasználókra/csoportokra vonatkozó szabály kényszerítése után történik.

MFA-szabályok megadása egy felhasználóhoz vagy csoporthoz az Azure AD-ban:

1. Hozzon létre [egy új feltételes hozzáférési szabályzatot.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Válassza **a Hozzárendelések lehetőséget.** Adja hozzá a felhasználót vagy csoportokat, amelyekhez kényszeríti az MFA-t.
1. Konfigurálja **a Hozzáférés-vezérlési** beállításokat az alábbiak szerint:

    ‎![Képernyőkép a Hozzáférés panelről, ahol hozzáférést adhat.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>2. példa: MFA kényszerítve a nem regisztrált eszközökön

MFA-szabályok megadása nem regisztrált eszközökhöz az Azure AD-ban:

1. Hozzon létre [egy új feltételes hozzáférési szabályzatot.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. A **Hozzárendelések beállításnál adja** meg **a Minden felhasználó halmazt.**
1. Konfigurálja **a Hozzáférés-vezérlési** beállításokat az alábbiak szerint:

    ![Képernyőkép a Hozzáférés panelről, ahol hozzáférést adhat meg, és egyéb korlátozásokat adhat meg.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Ha a  Több vezérlőhöz beállítást A kijelölt vezérlők egyikének megkövetelését választja, az azt jelenti, hogy ha a felhasználó a jelölőnégyzetben megadott feltételek bármelyikének teljesül, a felhasználó hozzáférést kap az alkalmazáshoz.

#### <a name="example-3-enforce-mfa-based-on-location"></a>3. példa: MFA kényszerítés hely alapján

MFA-szabályok megadása a felhasználó helye alapján az Azure AD-ban:

1. Hozzon létre [egy új feltételes hozzáférési szabályzatot.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. A **Hozzárendelések beállításnál adja** meg **a Minden felhasználó halmazt.**
1. [Neveselt helyek konfigurálása az Azure AD-ban.](../reports-monitoring/quickstart-configure-named-locations.md) Ellenkező esetben a vállalati hálózaton belüli összevonás megbízható.
1. Konfigurálja **a Feltételek szabályait** azon helyek megadásához, amelyeken érvényesíteni szeretné az MFA-t.

    ![Képernyőkép a Feltételek szabályainak Helyek paneléről.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Konfigurálja **a Hozzáférés-vezérlési** beállításokat az alábbiak szerint:

    ![Hozzáférés-vezérlési szabályzatok leképezés](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Attribútumkibocsátó attribútumok leképezés jogcímszabályként

Attribútumokat bocsáthat ki jogcímszabályként a AD FS:

  ![Képernyőkép a Szabály szerkesztése párbeszédpanelről, amely jogcímekként bocsát ki attribútumokat.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

A szabály leképezés az Azure AD-be:

1. A Azure Portal válassza [a](https://portal.azure.com/) **Vállalati** alkalmazások,  majd az Egyszeri bejelentkezés lehetőséget az SAML-alapú bejelentkezési konfiguráció megtekintéséhez:

    ![Képernyőkép a vállalati alkalmazás Egyszeri bejelentkezési oldalával.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Az **attribútumok** módosításához válassza a Szerkesztés (kiemelt) lehetőséget:

    ![Ezen az oldalon szerkesztheti a felhasználói attribútumokat és jogcímeket](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Beépített hozzáférés-vezérlési szabályzatok leképezés

Beépített hozzáférés-vezérlési szabályzatok a 2016 AD FS ban:

  ![Az Azure AD beépített hozzáférés-vezérlése](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Az Azure AD-ben a beépített szabályzatok megvalósításához használjon egy új feltételes hozzáférési szabályzatot, és konfigurálja [a](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) hozzáférés-vezérlést, vagy használja az egyéni szabályzattervezőt az AD FS 2016-ban a hozzáférés-vezérlési szabályzatok konfigurálása érdekében. A Szabályszerkesztő az Engedélyek és a Kivétel lehetőségek teljes listájával rendelkezik, amelyek segítségével bármilyen permutációt el lehet hozni.

  ![Azure AD hozzáférés-vezérlési szabályzatok](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

Ebben a táblázatban néhány hasznos Engedélyezési és kivételi lehetőséget soroltunk fel, és hogy ezek hogyan vannak leképezve az Azure AD-be.

| Beállítás | Az Engedélyek beállítás konfigurálása az Azure AD-ban| Az Except beállítás konfigurálása az Azure AD-ban |
| - | - | - |
| Adott hálózatról| Leképezések [elnevezett helyre az](../reports-monitoring/quickstart-configure-named-locations.md) Azure AD-ban| Megbízható helyek **kizárása** [lehetőség használata](../conditional-access/location-condition.md) |
| Adott csoportokból| [Felhasználó-/csoport-hozzárendelés beállítása](assign-user-or-group-access-portal.md)| A Felhasználók **és csoportok kizárása** lehetőség használata |
| Adott megbízhatósági szintű eszközökről| Állítsa be ezt az **Eszközállapot vezérlőből** a Hozzárendelések -> feltételek alatt| Használja a **Kizárás lehetőséget** az Eszközállapot feltétele és a Minden eszköz be **van foglalva alatt** |
| Adott jogcímekkel a kérelemben| Ez a beállítás nem migrálható| Ez a beállítás nem migrálható |

Az alábbi példa bemutatja, hogyan konfigurálhatja a kizárási beállítást a Azure Portal:

  ![Képernyőkép a hozzáférés-vezérlési szabályzatok leképezésről](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Felhasználók váltása AD FS Azure AD-be

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Szinkronizálási AD FS Azure AD-ban

Az engedélyezési szabályok leképezésekor a hitelesítést AD FS alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. Ilyen esetben az alkalmazások áttelepítése [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) a csoportokat az Azure AD-val való szinkronizáláshoz. A migrálás előtt ellenőrizze ezeket a csoportokat és tagságot, hogy hozzáférést biztosítson ugyanazokhoz a felhasználókhoz az alkalmazás áttelepítésekor.

További információkért lásd: A csoportból szinkronizált [csoportattribútumok használatának előfeltételei Active Directory.](../hybrid/how-to-connect-fed-group-claims.md)

### <a name="set-up-user-self-provisioning"></a>Felhasználó önkiépítésének beállítása

Egyes SaaS-alkalmazások támogatják a felhasználók önkiépítését, amikor először jelentkeznek be az alkalmazásba. Az Azure AD-ban az alkalmazáslétrehozás azt jelenti, hogy automatikusan hoz létre felhasználói identitásokat és szerepköröket a felhőben[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)olyan alkalmazásokban, amelyekhez a felhasználóknak hozzá kell férni. A migrált felhasználók már rendelkezik fiókkal az SaaS-alkalmazásban. A migrálás után hozzáadott új felhasználókat ki kellépítenünk. Tesztelje [az SaaS-alkalmazás üzembezását](../app-provisioning/user-provisioning.md) az alkalmazás migrálása után.

### <a name="sync-external-users-in-azure-ad"></a>Külső felhasználók szinkronizálása az Azure AD-ban

A meglévő külső felhasználók a következő két módon beállíthatók a AD FS:

* **A szervezeten belüli helyi** fiókkal rendelkező külső felhasználók – Ezeket a fiókokat továbbra is ugyanúgy használhatja, mint a belső felhasználói fiókokat. Ezeknek a külső felhasználói fiókoknak a szervezeten belül van egy alapelvneve, bár a fiók e-mail-címe külsőleg is rámutathat. A migrálás során kihasználhatja az Azure [AD B2B](../external-identities/what-is-b2b.md) által nyújtott előnyöket, ha migrálja ezeket a felhasználókat a saját vállalati identitásuk használatára, amikor ilyen identitás elérhetővé válik. Ez leegyszerűsíti a felhasználók bejelentkezését, mivel gyakran saját vállalati bejelentkezéssel jelentkeznek be. A szervezet felügyelete is egyszerűbb, mivel nem kell külső felhasználók fiókjait kezelnie.
* **Összevont külső identitások**– Ha jelenleg külső szervezetekkel való összevont szervezést használ, több módszer is rendelkezésre áll:
  * [Vegyen Azure Active Directory B2B együttműködési felhasználókat a Azure Portal.](../external-identities/add-users-administrator.md) Proaktív módon küldhet B2B-együttműködési meghívókat az Azure AD felügyeleti portálról a partnerszervezetnek, hogy az egyes tagok továbbra is használják a korábban használt alkalmazásokat és eszközöket.
  * [Hozzon létre egy önkiszolgáló B2B-bejelentkezési](../external-identities/self-service-portal.md) munkafolyamatot, amely kérést hoz létre a partnerszervezet egyéni felhasználói számára a B2B meghívó API használatával.

Függetlenül attól, hogy a meglévő külső felhasználók hogyan vannak konfigurálva, valószínűleg a fiókjukhoz társított engedélyekkel is rendelkezniük kell, akár csoporttagságban, akár meghatározott engedélyekben. Értékelje ki, hogy ezeket az engedélyeket migrálni vagy meg kell-e tisztítani. A szervezeten belüli, külső felhasználót képviselő fiókokat le kell tiltani a felhasználó külső identitásba való migrálása után. A migrálási folyamatot meg kell vitatni az üzleti partnerekkel, mivel az erőforrásokhoz való csatlakozás megszakadhat.

## <a name="migrate-and-test-your-apps"></a>Alkalmazások áttelepítése és tesztelése

Kövesse az ebben a cikkben részletesen részletezett migrálási folyamatot. Ezután a [Azure Portal,](https://aad.portal.azure.com/) hogy a migrálás sikeres volt-e.

Kövesse az alábbi utasításokat:

1. Válassza **a Vállalati alkalmazások** Minden alkalmazás  >  **lehetőséget,** és keresse meg az alkalmazást a listából.
1. Válassza **a Felhasználók** és csoportok kezelése lehetőséget, ha legalább egy felhasználót vagy csoportot szeretne  >   hozzárendelni az alkalmazáshoz.
1. Válassza **a Feltételes hozzáférés** kezelése  >  **lehetőséget.** Tekintse át a szabályzatok listáját, és győződjön meg arról, hogy nem blokkolja az alkalmazáshoz való hozzáférést feltételes hozzáférési [szabályzatokkal.](../conditional-access/overview.md)

Az alkalmazás konfigurálásától függően ellenőrizze, hogy az SSO megfelelően működik-e.

| Hitelesítéstípus| Tesztelés |
| :- | :- |
| OAuth/ OpenID Connect| Válassza **a Vállalati alkalmazások > engedélyek** lehetőséget, és győződjön meg arról, hogy az alkalmazás felhasználói beállításaiban hozzájárult az alkalmazáshoz.|
| SAML-alapú egyszeri bejelentkezés | Használja az Egyszeri bejelentkezés alatt található [SAML-beállítások](debug-saml-sso-issues.md) **tesztelése gombot.** |
| Password-Based SSO |  Töltse le és telepítse a [MyApps biztonságos bejelentkezési](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [bővítményt.](../user-help/my-apps-portal-end-user-access.md) Ez a bővítmény segít elindítani a szervezet bármely olyan felhőalkalmazását, amely egy SSO-folyamat használatát igényli. |
| Alkalmazásproxy | Győződjön meg arról, hogy az összekötő fut, és hozzá van rendelve az alkalmazáshoz. További [segítségért alkalmazásproxy a hibaelhárítási](application-proxy-troubleshoot.md) útmutatóban. |

> [!NOTE]
> A régi környezetben AD FS cookie-k megmaradnak a felhasználói gépeken. Ezek a cookie-k problémákat okozhatnak a migrálás során, mivel a felhasználók a régi bejelentkezési környezetbe AD FS az új Azure AD-bejelentkezéssel szemben. Előfordulhat, hogy manuálisan vagy egy szkript használatával kell eltávolítania a felhasználói böngésző cookie-jait. Használhatja a System Center Konfigurációkezelő hasonló platformot is.

### <a name="troubleshoot"></a>Hibaelhárítás

Ha a migrált alkalmazások tesztelése során hibák lépnek fel, a hibaelhárítás lehet az első lépés, mielőtt visszatér a meglévő függő AD FS függő felekhez. Lásd: [SAML-alapú egyszeri](debug-saml-sso-issues.md)bejelentkezés hibakeresése alkalmazásokban a Azure Active Directory.

### <a name="rollback-migration"></a>Visszaállítás migrálás

Ha a migrálás sikertelen, javasoljuk, hogy hagyja a meglévő függő AD FS a függő kiszolgálókon, és távolítsa el a függő felekhez való hozzáférést. Ez lehetővé teszi a gyors tartalékolást, ha szükséges az üzembe helyezés során.

### <a name="employee-communication"></a>Alkalmazottak kommunikációja

Bár maga a tervezett szolgáltatáskimaradási időszak is minimális lehet, továbbra is érdemes proaktív módon kommunikálni az időkeretekkel az alkalmazottakkal, miközben átvált az AD FS Azure AD-re. Győződjön meg arról, hogy az alkalmazás felhasználói élménye rendelkezik visszajelzési gombbal, vagy a problémákra mutató mutatókat az ön súgójára.

Az üzembe helyezés befejezése után tájékoztathatja a felhasználókat a sikeres telepítésről, és emlékeztetőt kaphat a szükséges lépésekről.

* Kérje meg a [felhasználókat,](https://myapps.microsoft.com) hogy Saját alkalmazások az összes áttelepített alkalmazás eléréséhez.
* Emlékezteti a felhasználókat, hogy esetleg frissíteniük kell az MFA-beállításokat.
* Ha Self-Service jelszó-visszaállítás telepítve van, előfordulhat, hogy a felhasználóknak frissíteniük vagy ellenőrizniük kell a hitelesítési módszereiket. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

### <a name="external-user-communication"></a>Külső felhasználói kommunikáció

A problémák általában ez a felhasználói csoportra vannak a legkritikusabb hatással. Ez különösen akkor igaz, ha a biztonsági helyzet más feltételes hozzáférési szabályokat vagy kockázati profilokat szab meg külső partnerek számára. Győződjön meg arról, hogy a külső partnerek ismerik a felhőbe való migrálás ütemezését, és legyen egy olyan időkeretük, amelynek során arra biztatjuk őket, hogy vegyenek részt egy próbatelepítésben, amely a külső együttműködésre vonatkozó összes egyedi folyamatot teszteli. Végül győződjön meg arról, hogy van módjuk a segítség segítségért való hozzáférésre arra az esetre, ha probléma merülne fel.

## <a name="next-steps"></a>Következő lépések

* Olvassa [el az Migrating application authentication to Azure AD (Alkalmazáshitelesítés áttelepítése az Azure AD-be) (Alkalmazáshitelesítés áttelepítése az Azure AD](https://aka.ms/migrateapps/whitepaper)
* Állítsa be [a feltételes hozzáférést és](../conditional-access/overview.md) az [MFA-t.](../authentication/concept-mfa-howitworks.md)
* Próbáljon ki egy részletes kódmintát: AD FS Azure AD-alkalmazás migrálási[forgatókönyve fejlesztők számára.](https://aka.ms/adfsplaybook)
