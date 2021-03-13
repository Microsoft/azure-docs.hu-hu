---
title: Alkalmazás-hitelesítés áthelyezése AD FSról Azure Active Directoryra
description: Megtudhatja, hogyan használhatja a Azure Active Directory az Active Directory összevonási szolgáltatások (AD FS) (AD FS) cseréjére, így a felhasználók egyszeri bejelentkezést biztosítanak az összes alkalmazására.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418349"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Alkalmazáshitelesítés áthelyezése az Active Directory Federation Servicesből az Azure Active Directoryba

[Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) univerzális identitási platformot kínál, amely a felhasználók, partnerek és ügyfelek számára egyetlen identitást biztosít az alkalmazások eléréséhez és a bármely platformról és eszközről való együttműködéshez. Az Azure AD [teljes körű Identitáskezelés-kezelési képességekkel](../fundamentals/active-directory-whatis.md)rendelkezik. Az alkalmazás-hitelesítés és az Azure AD-engedélyezés egységesítése biztosítja ezeket az előnyöket.

> [!TIP]
> Ez a cikk fejlesztői közönség számára készült. Az alkalmazások Azure AD-ba való áthelyezését tervező projektmenedzsereknek és rendszergazdáknak érdemes megfontolniuk az [alkalmazás-hitelesítés áttelepítését az Azure ad-be](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

Ha olyan helyszíni címtárral rendelkezik, amely felhasználói fiókokat tartalmaz, valószínűleg sok alkalmazásra van szüksége, amelyhez a felhasználók hitelesítést végeznek. Ezek az alkalmazások úgy vannak konfigurálva, hogy a felhasználók identitásuk alapján férhessenek hozzájuk.

A felhasználók közvetlenül a helyszíni Active Directory is hitelesíthetők. A Active Directory összevonási szolgáltatások (AD FS) (AD FS) egy szabványon alapuló helyszíni Identitáskezelő szolgáltatás. Kiterjeszti az egyszeri bejelentkezés (SSO) funkcióinak használatát a megbízható üzleti partnerek között, így a felhasználóknak nem kell külön bejelentkezniük az egyes alkalmazásokhoz. Ezt nevezzük összevont identitásnak.

Számos szervezet rendelkezik szolgáltatott szoftverrel (SaaS) vagy egyéni üzletági alkalmazásokkal, amelyeket közvetlenül AD FS összevontak, Microsoft 365 és az Azure AD-alapú alkalmazások mellett.

  ![Közvetlenül a helyszínen csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Az alkalmazások biztonságának növeléséhez a cél az, hogy egyetlen hozzáférés-vezérlést és szabályzatot hozzon létre a helyszíni és a felhőalapú környezetekben.

  ![Az Azure AD-n keresztül csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Az áttelepítendő alkalmazások típusai

Az összes alkalmazás-hitelesítés az Azure AD-be való migrálása optimális, mivel egyetlen vezérlési síkot biztosít az identitás-és hozzáférés-kezeléshez.

Alkalmazásai használhatnak modern vagy örökölt protokollokat a hitelesítéshez. Az Azure AD-ba való Migrálás tervezésekor érdemes először áttelepíteni a modern hitelesítési protokollt használó alkalmazásokat (például SAML és Open ID csatlakozás). Ezek az alkalmazások Újrakonfigurálhatók az Azure AD-vel való hitelesítéshez az Azure App Gallery beépített összekötője vagy az alkalmazás Azure AD-ben való regisztrálásával. A régebbi protokollokat használó alkalmazások az alkalmazásproxy használatával integrálhatók.

További információkért lásd:

* Helyszíni [alkalmazások közzététele az Azure ad Application proxy használatával távoli felhasználók](what-is-application-proxy.md)számára.
* [Mi az alkalmazáskezelés?](what-is-application-management.md)
* [AD FS alkalmazás-tevékenység jelentés az alkalmazások Azure ad-be való áttelepíthető](migrate-adfs-application-activity.md).
* [AD FS figyelése Azure ad Connect Health használatával](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Az áttelepítési folyamat

Az alkalmazás Azure AD-hitelesítésre való áthelyezése során tesztelje alkalmazásait és konfigurációját. Javasoljuk, hogy az éles környezetbe való áttéréskor továbbra is használja a meglévő tesztelési környezeteket az áttelepítés teszteléséhez. Ha a tesztkörnyezet jelenleg nem érhető el, az alkalmazás architektúrája alapján [Azure app Service](https://azure.microsoft.com/services/app-service/) vagy az [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)használatával is beállíthatja az egyiket.

Dönthet úgy is, hogy külön Azure AD-bérlőt állít be az alkalmazás konfigurációjának fejlesztéséhez.

Az áttelepítési folyamat így néz ki:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>1. lépés – aktuális állapot: az éles alkalmazás AD FS

  ![1. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>2. fázis – (nem kötelező) az alkalmazás egy teszt példányának mutatása az Azure AD-bérlő teszteléséhez

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztelési példányát egy teszt Azure AD-bérlőre mutassa, és végezze el a szükséges módosításokat. Az alkalmazás a teszt Azure AD-bérlőben található felhasználókkal tesztelhető. A fejlesztési folyamat során olyan eszközöket használhat, mint például a [Hegedűs](https://www.telerik.com/fiddler) a kérelmek és válaszok összehasonlításához és ellenőrzéséhez.

Ha nem valósítható meg külön tesztelési bérlő beállítása, ugorja át ezt a szakaszt, és az alkalmazás egy tesztelési példányát az éles Azure AD-bérlőre irányítsa az alábbi 3. szakaszban leírtak szerint.

  ![2. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>3. fázis – az alkalmazás egy teszt példányának az éles Azure AD-bérlőre való mutatása

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztelési példányát az éles Azure AD-bérlőre mutassa. Mostantól tesztelheti az éles bérlő felhasználóit. Ha szükséges, tekintse át a jelen cikk szakaszát a felhasználók átváltásához.

  ![3. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>4. fázis – az éles alkalmazás az éles Azure AD-bérlőre irányítva

Frissítse az éles alkalmazás konfigurációját, hogy az éles Azure AD-bérlőre mutasson.

  ![4. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 A AD FS hitelesítő alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. A Migrálás megkezdése előtt a [Azure ad Connect Sync](../hybrid/how-to-connect-sync-whatis.md) használatával szinkronizálhatja a helyszíni környezet és az Azure ad-beli identitás adatait. Az áttelepítés előtt ellenőrizze ezeket a csoportokat és tagságot, hogy az alkalmazás áttelepítésekor ugyanazokhoz a felhasználókhoz is hozzáférést biztosíthat.

### <a name="line-of-business-apps"></a>Üzletági alkalmazások

Az üzletági alkalmazások azok, amelyeket a szervezet fejlesztett ki, vagy amelyek standard csomagolású terméknek számítanak. Ilyenek például a Windows Identity Foundation és a SharePoint-alkalmazások (nem a SharePoint Online) szolgáltatásokra épülő alkalmazások.

Az OAuth 2,0, OpenID Connect vagy WS-Federationt használó üzletági alkalmazások az Azure AD-ben is integrálhatók az [alkalmazások regisztrálásával](../develop/quickstart-register-app.md). Integrálhatja az SAML 2,0-et vagy WS-Federationt használó [egyéni alkalmazásokat a](add-application-portal.md) [Azure Portal](https://portal.azure.com/)vállalati alkalmazások lapján.

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés

Az SAML 2,0 hitelesítést használó alkalmazások az [SAML-alapú egyszeri bejelentkezéshez](what-is-single-sign-on.md) (SSO) konfigurálhatók. Az SAML-alapú egyszeri bejelentkezés használata esetén a felhasználókat az SAML-jogcímek által meghatározott szabályok alapján rendelheti hozzá bizonyos alkalmazási szerepkörökhöz.

Az SAML-alapú egyszeri bejelentkezéshez szükséges SaaS-alkalmazások konfigurálásáról lásd: rövid útmutató [: SAML-alapú egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md).

  ![SSO SAML felhasználói képernyőképek ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Számos SaaS-alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal](../saas-apps/tutorial-list.md) , amely VÉGIGVEZETI az SAML-alapú egyszeri bejelentkezés konfigurációjának lépésein.

  ![alkalmazás-oktatóanyag](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Egyes alkalmazások migrálása egyszerű. Az összetettebb követelményekkel rendelkező alkalmazások, például az egyéni jogcímek esetében további konfigurálásra lehet szükség az Azure AD-ben és/vagy Azure AD Connectban. További információ a támogatott jogcímek hozzárendeléséről [: Hogyan lehet testre szabni a jogkivonatokban kibocsátott jogcímeket egy adott alkalmazáshoz a bérlőben (előzetes verzió)](../develop/active-directory-claims-mapping.md).

Vegye figyelembe a következő korlátozásokat az attribútumok leképezése során:

* Nem minden olyan attribútum, amely kiállítható AD FS az Azure AD-ben az SAML-tokenekre való kibocsátásra szolgáló attribútumokként, még akkor is, ha ezek az attribútumok szinkronizálva vannak. Amikor szerkeszti az attribútumot, az **érték** legördülő lista az Azure ad-ben elérhető különböző attribútumokat jeleníti meg. Ellenőrizze [Azure ad Connect szinkronizálási témakörök](../hybrid/how-to-connect-sync-whatis.md) konfigurációját, és győződjön meg arról, hogy egy szükséges attribútum – például **sAMAccountName**– szinkronizálva van az Azure ad-vel. A bővítmény attribútumaival olyan jogcímeket hozhat létre, amelyek nem részei az Azure AD általános felhasználói sémájának.
* A leggyakoribb forgatókönyvekben csak a **NameID** jogcím és egyéb általános felhasználóazonosító jogcímek szükségesek az alkalmazáshoz. Annak megállapításához, hogy szükség van-e további jogcímek elvégzésére, vizsgálja meg, milyen jogcímeket kell kiadnia AD FS
* Nem minden jogcímet lehet kibocsátani, mivel egyes jogcímek védelme az Azure AD-ben történik.
* A titkosított SAML-tokenek használatának lehetősége már előzetes verzióban érhető el. További információ [: a vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>Szolgáltatott szoftver (SaaS) alkalmazások

Ha a felhasználók SaaS-alkalmazásokba, például Salesforce, ServiceNow vagy munkanapokba jelentkeznek be, és integrálva vannak a AD FSkal, akkor összevont bejelentkezést használ az SaaS-alkalmazásokhoz.

A legtöbb SaaS-alkalmazás konfigurálható az Azure AD-ben. A Microsoft számos előre konfigurált kapcsolattal rendelkezik az SaaS-alkalmazásokhoz az  [Azure ad-alkalmazás galériájában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), amely egyszerűbbé teszi az áttérést. Az SAML 2,0-alkalmazások az Azure AD-katalógusban vagy nem katalógusbeli [alkalmazásokban](add-application-portal.md)is integrálhatók az Azure ad-be.

Az OAuth 2,0-as vagy OpenID connectet használó alkalmazások ugyanúgy integrálhatók az Azure AD-be, mint az [alkalmazások regisztrációja](../develop/quickstart-register-app.md). Az örökölt protokollokat használó alkalmazások az [azure ad Application proxy](application-proxy.md) használatával hitelesíthetők az Azure ad-vel.

Az SaaS-alkalmazások bevezetésével kapcsolatos esetleges problémákért forduljon a [SaaS Application Integration support aliashoz](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>SAML-aláíró tanúsítványok az egyszeri bejelentkezéshez

Az aláíró tanúsítványok fontos részét képezik az SSO-telepítésnek. Az Azure AD létrehozza az aláíró tanúsítványokat, hogy SAML-alapú összevont SSO-t hozzon létre SaaS-alkalmazásaihoz. A katalógus vagy a nem katalógusbeli alkalmazások hozzáadása után a hozzáadott alkalmazást az összevont egyszeri bejelentkezés lehetőséggel állíthatja be. Lásd: a [tanúsítványok kezelése összevont egyszeri bejelentkezéshez Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>SAML-jogkivonat titkosítása

Mind a AD FS, mind az Azure AD jogkivonat-titkosítást biztosít – lehetővé teszi az alkalmazások számára elérhető SAML biztonsági érvényesítések titkosítását. Az állítások nyilvános kulccsal vannak titkosítva, és a fogadó alkalmazás visszafejti a megfelelő titkos kulccsal. A jogkivonat-titkosítás konfigurálásakor X. 509 tanúsítványfájl feltöltésével adja meg a nyilvános kulcsokat.

Az Azure AD SAML-jogkivonat titkosításával és konfigurálásával kapcsolatos információkért lásd [: útmutató: az Azure ad SAML-jogkivonat titkosításának konfigurálása](howto-saml-token-encryption.md).  

> [!NOTE]
> A jogkivonat-titkosítás egy Azure Active Directory (Azure AD) prémium szintű szolgáltatás. Az Azure AD-kiadásokkal,-funkciókkal és-díjszabással kapcsolatos további információkért lásd: az [Azure ad díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>A ma áthelyezhető alkalmazások és konfigurációk

Az egyszerűen áthelyezhető alkalmazások közé tartoznak az SAML 2,0-alkalmazások, amelyek a konfigurációs elemek és jogcímek szabványos készletét használják. Ezek a standard elemek a következők:

* Felhasználó egyszerű neve
* E-mail-cím
* Utónév
* vezetéknév;
* Az SAML **NameID**-ként használt alternatív attribútum, például az Azure AD Mail attribútum, a Mail előtag, az alkalmazott azonosítója, az 1-15 bővítményattribútumok vagy a helyszíni **SamAccountName**. További információkat itt talál: A [NameIdentifier](../develop/active-directory-saml-claims-customization.md) jogcím szerkesztése.
* Egyéni jogcímek.

Az Azure AD-ba való Migrálás további konfigurációs lépéseinek megkövetelése:

* AD FS egyéni engedélyezési vagy többtényezős hitelesítési (MFA) szabályok. Ezeket az [Azure ad feltételes hozzáférés](../conditional-access/overview.md) funkciójával konfigurálhatja.
* Több válasz URL-végponttal rendelkező alkalmazások. Ezeket az Azure AD-ben a PowerShell vagy a Azure Portal felület használatával konfigurálhatja.
* A WS-Federationt használó alkalmazások, például a SharePoint-alkalmazások, amelyek SAML 1.1-es verziójú jogkivonatot igényelnek. Ezeket manuálisan is konfigurálhatja a PowerShell használatával. Hozzáadhat egy előre integrált általános sablont is a SharePoint és SAML 1,1-alkalmazásokhoz a katalógusból. Az SAML 2,0 protokollt támogatjuk.
* Az összetett jogcímek kiállítása átalakítja a szabályokat. A támogatott jogcímek hozzárendelésével kapcsolatos további információkért lásd:
   *  [Jogcímek leképezése Azure Active Directoryban](../develop/active-directory-claims-mapping.md).
   * [Az SAML-jogkivonatban kiadott jogcímek testreszabása Azure Active Directory-ben nagyvállalati alkalmazásokhoz](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Az Azure AD-ben jelenleg nem támogatott alkalmazások és konfigurációk

Az egyes képességeket igénylő alkalmazások még ma nem telepíthetők át.

#### <a name="protocol-capabilities"></a>Protokoll képességei

A következő protokoll-képességeket igénylő alkalmazások nem telepíthetők át még ma:

* Az WS-Trust ActAs minta támogatása
* Az SAML-összetevő felbontása
* Aláírt SAML-kérelmek aláírásának ellenőrzése
  > [!Note]
  > A rendszer elfogadja az aláírt kéréseket, de nem ellenőrzi az aláírást.

  Mivel az Azure AD csak a jogkivonatot adja vissza az alkalmazásban előre konfigurált végpontoknak, az aláírás-ellenőrzés valószínűleg nem szükséges a legtöbb esetben.

#### <a name="claims-in-token-capabilities"></a>Jogkivonatokkal kapcsolatos jogcímek

A jogkivonat-funkciókban a következő jogcímeket igénylő alkalmazások nem telepíthetők át még ma.

* Nem az Azure AD-címtárból származó attribútum-tárolók jogcímei, kivéve, ha az adatok szinkronizálva vannak az Azure AD-vel. További információ: az [Azure ad SYNCHRONIZATION API áttekintése](/graph/api/resources/synchronization-overview?view=graph-rest-beta).
* Több értékkel rendelkező címtár-attribútumok kiállítása. Például nem lehet kiadni többértékű jogcímet a proxy címeihez.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Alkalmazásbeállítások leképezése AD FSról az Azure AD-be

Az áttelepítés során meg kell vizsgálni, hogy az alkalmazás hogyan van konfigurálva a helyszínen, majd ezt a konfigurációt az Azure AD-hez rendeli. Az AD FS és az Azure AD hasonlóan működik, így a megbízhatóság konfigurálásával, a bejelentkezési és kijelentkezési URL-címekkel és az azonosítókkal kapcsolatos fogalmak mindkettőre vonatkoznak. Dokumentálja az alkalmazások AD FS konfigurációs beállításait, így egyszerűen konfigurálhatja őket az Azure AD-ben.

### <a name="map-app-configuration-settings"></a>Az alkalmazás konfigurációs beállításainak leképezése

Az alábbi táblázat egy AD FS függő entitás megbízhatósága Azure AD Enterprise-alkalmazáshoz való beállításainak leggyakoribb leképezéseit ismerteti:

* AD FS – megkeresheti a beállítást az alkalmazáshoz tartozó AD FS függő entitás megbízhatóságában. Kattintson a jobb gombbal a függő entitásra, és válassza a tulajdonságok lehetőséget.
* Azure AD – a beállítás az egyes alkalmazások SSO-tulajdonságainak [Azure Portal](https://portal.azure.com/) belül van konfigurálva.

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ben| SAML-jogkivonat |
| - | - | - | - |
| **Alkalmazás bejelentkezési URL-címe** <p>Annak az URL-címe, amellyel a felhasználó bejelentkezhet az alkalmazásba egy szolgáltató (SP) által kezdeményezett SAML-folyamat során.| N/A| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| N/A |
| **Alkalmazás válasz URL-címe** <p>Az alkalmazás URL-címe az identitás-szolgáltató szemszögéből (identitásszolgáltató). A identitásszolgáltató elküldi a felhasználót és a tokent, miután a felhasználó bejelentkezett a identitásszolgáltató.  Ezt az SAML-jogcímek **fogyasztói végpontjának** is nevezzük.| Válassza a **végpontok** fület.| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| A cél elem az SAML-jogkivonatban. Példaérték: `https://contoso.my.salesforce.com` |
| **Alkalmazás kijelentkezési URL-címe** <p>Ezt az URL-címet kell elküldeni a kijelentkezési törlési kérelmeknek, amikor egy felhasználó kijelentkezik az alkalmazásból. A identitásszolgáltató elküldi a kérést, hogy kijelentkezzen a felhasználótól az összes többi alkalmazásból is.| Válassza a **végpontok** fület.| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| N/A |
| **Alkalmazásazonosító** <p>Ez az alkalmazás azonosítója a identitásszolgáltató szemszögéből. Gyakran (de nem mindig) a bejelentkezési URL-cím az azonosító.  Néha az alkalmazás meghívja ezt az "Entity ID"-t.| Az **azonosítók** lap kijelölése|Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| Leképezi az SAML-jogkivonat **célközönség** elemét. |
| **Alkalmazás összevonási metaadatai** <p>Ez az alkalmazás összevonási metaadatainak helye. Az identitásszolgáltató használja egyes konfigurációs beállítások, például a végpontok vagy a titkosítási tanúsítványok automatikus frissítéséhez.| A **figyelés** lap kiválasztása| N/A. Az Azure AD nem támogatja az alkalmazások összevonási metaadatainak közvetlen felhasználását. Az összevonási metaadatokat manuálisan is importálhatja.| N/A |
| **Felhasználói azonosító/név azonosítója** <p>A felhasználó identitását az Azure AD-ből vagy az AD FS-ből az alkalmazás felé egyértelműen azonosító attribútum.  Ez az attribútum általában a felhasználó egyszerű felhasználóneve vagy e-mail-címe.| Jogcím-szabályok. A legtöbb esetben a jogcím szabály a **NameIdentifier** végződő típussal rendelkező jogcímet bocsát ki.| Az azonosítót a **felhasználói attribútumok és jogcímek** fejléce alatt találja. Alapértelmezés szerint a rendszer az UPN-t használja| Leképezi az SAML-token **NameID** elemét. |
| **Egyéb jogcímek** <p>A identitásszolgáltató és az alkalmazás között gyakran küldött egyéb jogcím-információk közé tartozik például az utónév, a vezetéknév, az e-mail cím és a csoporttagság.| Az AD FS-ben ez a függő entitásra vonatkozó egyéb jogcímszabályokként található meg.| Az azonosító a fejléc **felhasználói attribútumok & jogcímek** alatt található. Válassza ki az **Egyéb felhasználói attribútumok megtekintése** és szerkesztése elemet.| N/A |

### <a name="map-identity-provider-idp-settings"></a>A Térkép identitás-szolgáltatója (identitásszolgáltató) beállításai

Konfigurálja úgy az alkalmazásokat, hogy az egyszeri bejelentkezéshez az Azure AD-t és a AD FS mutassanak. Itt az SAML protokollt használó SaaS-alkalmazásokra fogunk összpontosítani. Ez a koncepció azonban az egyéni üzletági alkalmazásokra is kiterjed.

> [!NOTE]
> A konfigurációs értékek az Azure AD-ben azt a mintát követik, ahol az Azure-bérlő azonosítója {bérlő-azonosító}, az alkalmazás azonosítója pedig {Application-ID} értékre vált. Ezeket az információkat a [Azure Portal](https://portal.azure.com/) **Azure Active Directory > tulajdonságok** területén találja:

* Válassza a címtár-azonosító lehetőséget a bérlő AZONOSÍTÓjának megtekintéséhez.
* Válassza ki az alkalmazás AZONOSÍTÓját az alkalmazás AZONOSÍTÓjának megtekintéséhez.

 Az Azure AD-ben a következő Key SaaS-alkalmazások konfigurációs elemeit magas szinten képezheti le.

| Elem| Konfigurációs érték |
| - | - |
| Identitás-szolgáltató kiállítója| https: \/ /STS.Windows.net/{Tenant-ID}/ |
| Identitás-szolgáltató bejelentkezési URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Identitás-szolgáltató kijelentkezési URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Összevonási metaadatok helye| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Az SaaS-alkalmazások SSO-beállításainak leképezése

Az SaaS-alkalmazásoknak ismerniük kell, hogy hol kell elküldeni a hitelesítési kéréseket, és hogyan kell érvényesíteni a kapott jogkivonatokat. Az alábbi táblázat az egyszeri bejelentkezés beállításainak konfigurálásához szükséges elemeket ismerteti az alkalmazásban, valamint a AD FS és az Azure AD-n belüli értékeit vagy helyét.

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ben |
| - | - | - |
| **Identitásszolgáltató bejelentkezési URL-címe** <p>A identitásszolgáltató bejelentkezési URL-címe az alkalmazás szemszögéből (ahol a felhasználó át lesz irányítva a bejelentkezéshez).| A AD FS bejelentkezési URL-cím a AD FS összevonási szolgáltatás neve, majd a "/adfs/ls/." <p>Például: `https://fs.contoso.com/adfs/ls/`| Cserélje le a {bérlő-azonosító} helyet a bérlői AZONOSÍTÓra. <p> Az SAML-P protokollt használó alkalmazások esetében: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Az WS-Federation protokollt használó alkalmazások esetén: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Identitásszolgáltató kijelentkezési URL-címe**<p>A identitásszolgáltató kijelentkezési URL-címe az alkalmazás szemszögéből (ahol a rendszer átirányítja a felhasználót, amikor kijelentkezik az alkalmazásból).| A kijelentkezési URL-cím vagy azonos a bejelentkezési URL-címmel, vagy a "WA = wsignout 1.0" utótaggal megegyező URL-címmel. Például: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Cserélje le a {bérlő-azonosító} helyet a bérlői AZONOSÍTÓra.<p>Az SAML-P protokollt használó alkalmazások esetében:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Az WS-Federation protokollt használó alkalmazások esetén: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Jogkivonat-aláíró tanúsítvány**<p>A identitásszolgáltató a tanúsítvány titkos kulcsát használja a kiállított jogkivonatok aláírására. Igazolja, hogy a jogkivonat attól az identitásszolgáltatótól származik, amellyel az alkalmazás megbízhatósági kapcsolata konfigurálva van.| Az AD FS jogkivonat-aláíró tanúsítványa az AD FS-kezelőben a **Tanúsítványok** területen található.| Keresse meg az alkalmazás **egyszeri bejelentkezési tulajdonságainál** az **SAML-aláíró tanúsítvány** alatt található Azure Portalban. Innen letöltheti a tanúsítványt, hogy feltöltse az alkalmazásba.  <p>Ha az alkalmazás több tanúsítvánnyal is rendelkezik, az összes tanúsítvány megtalálható az összevonási metaadatok XML-fájljában. |
| **Azonosító/"kiállító"**<p>Az alkalmazás perspektívájában lévő identitásszolgáltató azonosítója (más néven "kiállító azonosító").<p>Az SAML-tokenben az érték a kiállító elemként jelenik meg.| A AD FS azonosítója általában az összevonási szolgáltatás azonosítója AD FS kezelés területen a **szolgáltatás > szerkesztés összevonási szolgáltatás tulajdonságok** elemre. Például: `http://fs.contoso.com/adfs/services/trust`| Cserélje le a {bérlő-azonosító} helyet a bérlői AZONOSÍTÓra.<p>https: \/ /STS.Windows.net/{Tenant-ID}/ |
| **Identitásszolgáltató-összevonási metaadatok**<p>A identitásszolgáltató nyilvánosan elérhető összevonási metaadatainak helye. (Az összevonási metaadatokat egyes alkalmazások alternatív megoldásként használják, hogy a rendszergazdának ne kelljen egyenként konfigurálnia az URL-címeket, azonosítókat és jogkivonat-aláíró tanúsítványokat.)| Keresse meg a AD FS összevonási metaadatok URL-címét AD FS kezelés területen **> végpontok > metaadatok > típus: összevonási metaadatok**. Például: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Az Azure AD megfelelő értéke követi a mintát [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Cserélje le a (z) {Bérlőtartományneve} nevet a bérlő nevére "contoso.onmicrosoft.com" formátumban.   <p>További információkat itt talál: [Összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS biztonsági szabályzatok jelölése az Azure AD-ben

Ha áthelyezi az alkalmazást az Azure AD-ba, hozzon létre leképezéseket a meglévő biztonsági házirendekről az Azure AD-ben elérhető egyenértékű vagy alternatív változatokra. Annak biztosítása, hogy ezek a leképezések elvégezhető legyenek, miközben az alkalmazás tulajdonosai által megkövetelt biztonsági szabványok teljesítése jelentősen megkönnyíti a többi alkalmazás áttelepítését.

Az egyes szabályokhoz például megmutatjuk, hogy a szabály hogyan néz ki a AD FSban, a AD FS szabály nyelvének megfelelő kódja, és hogyan történik ez a leképezés az Azure AD-re.

### <a name="map-authorization-rules"></a>Engedélyezési szabályok leképezése

A következő példák különböző típusú engedélyezési szabályokra vonatkoznak AD FSban, és hogy miként képezhetők le az Azure AD-be.

#### <a name="example-1-permit-access-to-all-users"></a>1. példa: az összes felhasználó hozzáférésének engedélyezése

Hozzáférés engedélyezése AD FS összes felhasználója számára:

  ![A képernyőképen az egyszeri Sign-On beállítása az SAML-vel párbeszédpanel jelenik meg.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Ez az alábbi módokon mutat az Azure AD-re:

1. A **nem** értékhez adja meg a **felhasználó-hozzárendelést** .

    ![az SaaS-alkalmazások hozzáférés-vezérlési házirendjének szerkesztése ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Az **Igen** értékre vonatkozó **felhasználói hozzárendelés** beállítása megköveteli, hogy a felhasználók hozzá legyenek rendelve az alkalmazáshoz, hogy hozzáférjenek. Ha a **nem** értékre van állítva, minden felhasználó rendelkezik hozzáféréssel. Ez a kapcsoló nem szabályozza, hogy a felhasználók mit látnak a **saját alkalmazások** felületén.

1. A **felhasználók és csoportok lapon** rendelje hozzá az alkalmazást a **minden felhasználó** automatikus csoporthoz. Engedélyeznie kell a [dinamikus csoportokat](../enterprise-users/groups-create-rule.md) az Azure ad-bérlőben az alapértelmezett **minden felhasználó** csoport számára.

    ![Saját SaaS-alkalmazások az Azure AD-ben ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>2. példa: explicit módon egy csoport engedélyezése

Explicit csoportos hitelesítés AD FSban:

  ![Képernyőfelvétel: a szabály szerkesztése párbeszédpanel a Tartománygazdák engedélyezése jogcím-szabályhoz.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

A szabály leképezése az Azure AD-re:

1. A [Azure Portal](https://portal.azure.com/) [hozzon létre egy felhasználói csoportot](../fundamentals/active-directory-groups-create-azure-portal.md) , amely megfelel a AD FS felhasználói csoportjának.
1. Alkalmazás engedélyeinek társítása a csoporthoz:

    ![Hozzárendelés hozzáadása ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>3. példa: egy adott felhasználó engedélyezése

Explicit felhasználói hitelesítés AD FSban:

  ![A képernyőképen a szabály szerkesztése párbeszédpanel jelenik meg, amely lehetővé teszi egy adott felhasználói jogcím szabályának engedélyezését az elsődleges S I D típusú bejövő jogcím-típussal.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

A szabály leképezése az Azure AD-re:

* A [Azure Portal](https://portal.azure.com/)adjon hozzá egy felhasználót az alkalmazáshoz az alkalmazás hozzárendelés hozzáadása lapján az alábbi ábrán látható módon:

    ![Saját SaaS-alkalmazások az Azure-ban ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication szabályok leképezése

[Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) helyszíni telepítése és AD FS továbbra is az áttelepítés után működik, mert összevontuk a AD FS. Azonban érdemes lehet áttelepíteni az Azure-ba az Azure AD feltételes hozzáférési munkafolyamataihoz kötött beépített MFA-képességeket.

Az alábbiakban példákat talál a AD FS MFA-szabályok típusaira, valamint arról, hogy miként képezhetők le az Azure AD-be különböző feltételek alapján.

MFA-szabályok beállításai a AD FSban:

  ![Képernyőfelvétel: az Azure a D-vel kapcsolatos feltételek a Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>1. példa: az MFA érvényesítése felhasználók/csoportok alapján

A felhasználók/csoportok választó egy olyan szabály, amely lehetővé teszi, hogy az MFA-t csoportonként (csoport SID) vagy felhasználónkénti (elsődleges SID) alapon kényszerítse ki. A felhasználók/csoportok hozzárendelésein kívül a AD FS MFA konfigurációs felhasználói felületének minden további jelölőnégyzete a felhasználók/csoportok szabály érvényesítése után kiértékelt további szabályokként használható.

MFA-szabályok megadása egy felhasználó vagy egy csoport számára az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Válassza a **hozzárendelések** lehetőséget. Adja hozzá azokat a felhasználó (ka) t vagy csoportot, amelyeknek az MFA-t ki szeretné kényszeríteni.
1. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:

    ‎![Képernyőfelvétel: az engedélyezési ablaktábla, ahol hozzáférést adhat meg.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>2. példa: az MFA kikényszerítés a nem regisztrált eszközökre

MFA-szabályok megadása a nem regisztrált eszközökhöz az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Állítsa be a **hozzárendeléseket** az **összes felhasználóra**.
1. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:

    ![Képernyőfelvétel: az engedélyezési ablaktábla, ahol hozzáférést biztosíthat, és egyéb korlátozásokat is megadhat.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Ha úgy állítja be a **több vezérlőt** , hogy **megkövetelje a kiválasztott vezérlők valamelyikét**, az azt jelenti, hogy ha a felhasználó a jelölőnégyzetben megadott feltételek valamelyikét teljesíti, a felhasználó hozzáférést kap az alkalmazáshoz.

#### <a name="example-3-enforce-mfa-based-on-location"></a>3. példa: MFA érvényesítése hely alapján

A felhasználó Azure AD-beli helye alapján megadott MFA-szabályok meghatározása:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Állítsa be a **hozzárendeléseket** az **összes felhasználóra**.
1. [Elnevezett helyszínek konfigurálása az Azure ad-ben](../reports-monitoring/quickstart-configure-named-locations.md). Ellenkező esetben a vállalati hálózaton belüli összevonás megbízható.
1. Adja meg a **feltételek szabályait** , hogy megadja azokat a helyszíneket, amelyeknek az MFA-t ki szeretné kényszeríteni.

    ![Képernyőfelvétel: a feltételek szabályai a helyszínek ablaktáblán láthatók.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:

    ![Hozzáférés-vezérlési házirendek leképezése](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Attribútumok kibocsátása jogcím szabályként

Attribútumok kibocsátása jogcím-szabályként a AD FSban:

  ![Képernyőfelvétel: a szabály szerkesztése párbeszédpanel, amely az attribútumok jogcímként való kibocsátását mutatja be.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

A szabály leképezése az Azure AD-re:

1. A [Azure Portal](https://portal.azure.com/)válassza a **vállalati alkalmazások** , majd az **egyszeri bejelentkezés** lehetőséget az SAML-alapú bejelentkezés konfigurációjának megtekintéséhez:

    ![A képernyőképen a vállalati alkalmazás egyszeri bejelentkezési lapja látható.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Az attribútumok módosításához válassza a **Szerkesztés** (Kiemelt) lehetőséget:

    ![Ezen a lapon módosíthatók a felhasználói attribútumok és jogcímek](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Beépített hozzáférés-vezérlési szabályzatok leképezése

Beépített hozzáférés-vezérlési szabályzatok a AD FS 2016-ben:

  ![Az Azure AD beépített hozzáférés-vezérléssel rendelkezik](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

A beépített szabályzatok Azure AD-ben történő megvalósításához használjon [új feltételes hozzáférési házirendet](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) , konfigurálja a hozzáférés-vezérlést, vagy használja a AD FS 2016 egyéni szabályzat-tervezőjét a hozzáférés-vezérlési házirendek konfigurálásához. A szabály szerkesztőjének teljes listája lehetővé teszi az engedélyezést, és kivéve azokat a lehetőségeket, amelyek segítségével bármilyen típusú permutációt készíthet.

  ![Azure AD-hozzáférés-vezérlési szabályzatok](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

Ebben a táblázatban néhány hasznos engedély szerepel, de a lehetőségek és az Azure AD-hez való leképezésük is.

| Beállítás | Az engedélyezési lehetőség konfigurálása az Azure AD-ben| Az Azure AD-ban való kivétel beállítása |
| - | - | - |
| Adott hálózatról| Leképezi az Azure AD-ben [elnevezett helyre](../reports-monitoring/quickstart-configure-named-locations.md)| A [megbízható helyek](../conditional-access/location-condition.md) **kizárási** beállításának használata |
| Adott csoportoktól| [Felhasználó/csoport hozzárendelésének beállítása](assign-user-or-group-access-portal.md)| A **kizárás** lehetőség használata a felhasználók és csoportok számára |
| Megadott megbízhatósági szinttel rendelkező eszközökről| Az **eszköz állapotának** beállítása a hozzárendelések – > feltételek szakaszban| Használja a **kizárás** lehetőséget az eszköz állapota feltételnél, és adja meg az **összes eszközt** |
| A kérelemben megadott jogcímek| Ez a beállítás nem telepíthető át| Ez a beállítás nem telepíthető át |

Íme egy példa arra, hogyan konfigurálható a megbízható helyek kizárási beállítása a Azure Portalban:

  ![A hozzáférés-vezérlési szabályzatok leképezése képernyőkép](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Felhasználók átváltása AD FSról az Azure AD-be

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS csoportok szinkronizálása az Azure AD-ben

Az engedélyezési szabályok leképezése esetén a AD FS hitelesítő alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. Ilyen esetben a [Azure ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771) használatával szinkronizálja ezeket a csoportokat az Azure ad-vel az alkalmazások áttelepítése előtt. Győződjön meg arról, hogy az áttelepítés előtt ellenőrzi a csoportokat és a tagságot, hogy az alkalmazás áttelepítésekor ugyanazokhoz a felhasználókhoz is hozzáférést biztosíthat.

További információ: [Active Directoryról szinkronizált Group attributes használatának előfeltételei](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="set-up-user-self-provisioning"></a>A felhasználó önkiszolgáló üzembe helyezésének beállítása

Egyes SaaS-alkalmazások támogatják a felhasználók önálló kiépítését, amikor először jelentkeznek be az alkalmazásba. Az Azure AD-ben az alkalmazások kiosztása arra utal, hogy automatikusan hozza létre a felhasználói identitásokat és szerepköröket a felhasználók számára szükséges felhőalapú ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) alkalmazásokban. Az áttelepített felhasználók már rendelkeznek fiókkal az SaaS-alkalmazásban. Az áttelepítés után hozzáadott új felhasználókat ki kell építeni. Az [SaaS-alkalmazások üzembe](../app-provisioning/user-provisioning.md) helyezésének tesztelése az alkalmazás migrálása után.

### <a name="sync-external-users-in-azure-ad"></a>Külső felhasználók szinkronizálása az Azure AD-ben

A meglévő külső felhasználók a következő két módon állíthatók be AD FSban:

* A **szervezeten belül helyi fiókkal rendelkező külső felhasználók**– ezeket a fiókokat ugyanúgy használhatja, mint a belső felhasználói fiókokat. Ezeknek a külső felhasználói fiókoknak van egy alapelve a szervezeten belül, bár a fiók e-mail-címe külsőleg is mutathat. Az áttelepítés folyamata során kihasználhatja az [Azure ad B2B](../external-identities/what-is-b2b.md) által kínált előnyök előnyeit azáltal, hogy áttelepíti ezeket a felhasználókat a saját vállalati identitásának használatára, ha az identitás elérhető. Ez leegyszerűsíti a bejelentkezések folyamatát, mivel azok gyakran be vannak jelentkezve a saját vállalati bejelentkezéssel. A szervezet adminisztrációja is egyszerűbb, mivel nem kell a külső felhasználók fiókjait kezelni.
* **Összevont külső identitások**– ha jelenleg egyesítő egy külső szervezettel, néhány módszer közül választhat:
  * [Vegye fel Azure Active Directory B2B együttműködési felhasználókat a Azure Portal](../external-identities/add-users-administrator.md). Az Azure AD felügyeleti portálon keresztül proaktív módon küldhet VÁLLALATKÖZI együttműködési meghívókat az egyes tagok számára, hogy továbbra is használhassa azokat az alkalmazásokat és eszközöket, amelyekhez a használatban van.
  * [Hozzon létre egy önkiszolgáló vállalatközi regisztrációs munkafolyamatot](../external-identities/self-service-portal.md) , amely a vállalatközi MEGHÍVó API-val kérelmet készít a partnervállalat egyes felhasználói számára.

Függetlenül attól, hogy a meglévő külső felhasználók hogyan vannak konfigurálva, valószínűleg rendelkeznek a fiókjához társított engedélyekkel a csoporttagság vagy a konkrét engedélyek szerint. Ellenőrizze, hogy az engedélyeket át kell-e telepíteni vagy törölni kell-e. A szervezeten belüli, külső felhasználót képviselő fiókokat le kell tiltani, ha a felhasználót áttelepítették egy külső identitásra. Az áttelepítési folyamatot az üzleti partnerekkel kell megvitatni, mivel előfordulhat, hogy a rendszer megszakítja az erőforrásokhoz való kapcsolódás lehetőségét.

## <a name="migrate-and-test-your-apps"></a>Alkalmazások migrálása és tesztelése

Kövesse a cikkben részletezett áttelepítési folyamatot. Ezután lépjen a [Azure Portalra](https://aad.portal.azure.com/) , és ellenőrizze, hogy sikeres volt-e az áttelepítés.

Kövesse az alábbi utasításokat:

1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget, és keresse meg az alkalmazást a listából.
1. Válassza   >  a **felhasználók és csoportok** kezelése lehetőséget, ha legalább egy felhasználót vagy csoportot szeretne hozzárendelni az alkalmazáshoz.
1. Válassza a  >  **feltételes hozzáférés** kezelése lehetőséget. Tekintse át a szabályzatok listáját, és ellenőrizze, hogy nem blokkolja-e az alkalmazáshoz való hozzáférést [feltételes hozzáférési szabályzattal](../conditional-access/overview.md).

Az alkalmazás konfigurálásának módjától függően ellenőrizze, hogy az SSO megfelelően működik-e.

| Hitelesítéstípus| Tesztelés |
| :- | :- |
| OAuth/OpenID Connect| Válassza a **vállalati alkalmazások > engedélyek** lehetőséget, és győződjön meg arról, hogy hozzájárult az alkalmazáshoz az alkalmazás felhasználói beállításaiban.|
| SAML-alapú egyszeri bejelentkezés | Használja az **egyszeri bejelentkezés** alatt található [SAML-beállítások tesztelése](debug-saml-sso-issues.md) gombot. |
| EGYSZERI BEJELENTKEZÉS Password-Based |  Töltse le és telepítse a [MyApps biztonságos bejelentkezési](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [bővítményét](../user-help/my-apps-portal-end-user-access.md). Ez a bővítmény segítséget nyújt a szervezet olyan felhőalapú alkalmazásainak elindításához, amelyekhez egyszeri bejelentkezéses folyamatot kell használni. |
| Alkalmazásproxy | Győződjön meg arról, hogy az összekötő fut, és hozzá van rendelve az alkalmazáshoz. További segítségért tekintse meg az [alkalmazásproxy hibaelhárítási útmutatóját](application-proxy-troubleshoot.md) . |

> [!NOTE]
> A régi AD FS környezetből származó cookie-k megmaradnak a felhasználói gépeken. Ezek a cookie-k problémákat okozhatnak az áttelepítés során, mivel a felhasználók a régi AD FS bejelentkezési környezethez és az új Azure AD-bejelentkezéshez is irányíthatók. Előfordulhat, hogy manuálisan kell törölnie a felhasználói böngésző cookie-jait, vagy parancsfájlt kell használnia. Használhatja a System Center Configuration Manager vagy egy hasonló platformot is.

### <a name="troubleshoot"></a>Hibaelhárítás

Ha az áttelepített alkalmazások tesztelése hibákba ütközik, a hibaelhárítás lehet az első lépés, mielőtt visszakerül a meglévő AD FS függő entitásokra. Lásd: az [SAML-alapú egyszeri bejelentkezések hibakeresése Azure Active Directory-alkalmazásokban](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Áttelepítés visszaállítása

Ha az áttelepítés sikertelen, javasoljuk, hogy hagyja meg a meglévő függő entitásokat a AD FS-kiszolgálókon, és távolítsa el a függő entitásokhoz való hozzáférést. Ez lehetővé teszi, hogy szükség esetén gyors tartalékot biztosítson a telepítés során.

### <a name="employee-communication"></a>Alkalmazotti kommunikáció

Míg maga a tervezett leállási időszak is minimális lehet, továbbra is meg kell terveznie, hogy az alkalmazottak proaktív módon kommunikálnak-e a AD FS az Azure AD-be való váltáskor. Győződjön meg arról, hogy az alkalmazás felhasználói felülete rendelkezik visszajelzési gombbal, vagy ha problémákba ütközik az ügyfélszolgálatnál.

Az üzembe helyezés befejezése után értesítheti a felhasználókat a sikeres telepítésről, és emlékeztetheti őket a szükséges lépések elvégzésére.

* Kérje meg a felhasználókat, hogy az [alkalmazások](https://myapps.microsoft.com) használatával férhessenek hozzá az összes áttelepített alkalmazáshoz.
* Emlékeztesse a felhasználókat, hogy frissíteniük kell az MFA-beállításait.
* Ha Self-Service jelszó-visszaállítást telepít, előfordulhat, hogy a felhasználóknak frissíteniük vagy ellenőriznie kell a hitelesítési módszereiket. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

### <a name="external-user-communication"></a>Külső felhasználói kommunikáció

A felhasználók ezen csoportja általában a legjelentősebb hatással van a problémák esetére. Ez különösen akkor igaz, ha a biztonsági helyzet a feltételes hozzáférési szabályok vagy kockázati profilok eltérő készletét diktálja külső partnerek számára. Győződjön meg arról, hogy a külső partnerek tisztában vannak a felhő áttelepítési időpontjával, és olyan időkerettel rendelkeznek, amelyben a rendszer azt javasolja, hogy vegyen részt egy olyan kísérleti környezetben, amely a külső együttműködésre jellemző összes folyamatot teszteli. Végezetül ellenőrizze, hogy van-e lehetőség a segélyszolgálat elérésére, ha problémák merülnek fel.

## <a name="next-steps"></a>Következő lépések

* Olvassa el  [az alkalmazás-hitelesítés áttelepítését az Azure ad-be](https://aka.ms/migrateapps/whitepaper).
* [Feltételes hozzáférés](../conditional-access/overview.md) és [MFA](../authentication/concept-mfa-howitworks.md)beállítása.
* Próbálja ki a Wise-kód minta:[AD FS az Azure ad Application Migration forgatókönyv fejlesztőknek](https://aka.ms/adfsplaybook)című szakaszát.
