---
title: Alkalmazás-hitelesítés áthelyezése AD FSról Azure Active Directoryra
description: Ebből a cikkből megtudhatja, hogyan helyezheti át az alkalmazásokat a Azure Active Directoryba az összevont SaaS-alkalmazásokra összpontosítva.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/10/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: b8ad137389ac11a41872190122eae6405a126b5b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381239"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Alkalmazáshitelesítés áthelyezése az Active Directory Federation Servicesből az Azure Active Directoryba

[Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) univerzális identitási platformot kínál, amely a felhasználók, partnerek és ügyfelek számára egyetlen identitást biztosít az alkalmazások eléréséhez és a bármely platformról és eszközről való együttműködéshez. Az Azure AD [teljes körű Identitáskezelés-kezelési képességekkel](../fundamentals/active-directory-whatis.md)rendelkezik. Az alkalmazás (alkalmazás) hitelesítésének és az Azure AD-engedélyezésének egységesítése lehetővé teszi, hogy ezek a képességek milyen előnyöket biztosítanak.

> [!TIP]
> Ez a cikk fejlesztői közönség számára készült. Az alkalmazások Azure AD-ba való áthelyezését tervező projektmenedzserek és rendszergazdák megtekinthetik az [alkalmazás-hitelesítés Azure ad-ba való áttelepítésének](migrate-application-authentication-to-azure-active-directory.md) elolvasását.

## <a name="introduction"></a>Bevezetés

Ha olyan helyszíni címtárral rendelkezik, amely felhasználói fiókokat tartalmaz, valószínűleg sok alkalmazásra van szüksége, amelyhez a felhasználók hitelesítést végeznek. Ezek az alkalmazások úgy vannak konfigurálva, hogy a felhasználók identitásuk alapján férhessenek hozzájuk.


A felhasználók közvetlenül a helyszíni Active Directory is hitelesíthetők. A Active Directory összevonási szolgáltatások (AD FS) (AD FS) egy szabványon alapuló helyszíni Identitáskezelő szolgáltatás. AD FS kiterjeszti az egyszeri bejelentkezés (SSO) funkcióinak használatát a megbízható üzleti partnerek között anélkül, hogy a felhasználóknak külön kell bejelentkezniük az egyes alkalmazásokra. Ez az összevonás.

Számos szervezet rendelkezik olyan szoftverrel (SaaS) vagy egyéni üzletági (LOB) alkalmazásokkal, amelyek közvetlenül összevontak a AD FSba Microsoft 365 és az Azure AD-alapú alkalmazások mellett.

![Közvetlenül a helyszínen csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

Az **alkalmazások biztonságának növeléséhez a cél az, hogy egyetlen hozzáférés-vezérlést és szabályzatot hozzon létre a helyszíni és a felhőalapú környezetekben**.

![Az Azure AD-n keresztül csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Az áttelepítendő alkalmazások típusai

Az összes alkalmazás-hitelesítés az Azure AD-be való migrálása optimális, mivel egyetlen vezérlési síkot biztosít az identitás-és hozzáférés-kezeléshez.

Alkalmazásai használhatnak modern vagy örökölt protokollokat a hitelesítéshez. Érdemes megfontolni a modern hitelesítési protokollt használó alkalmazások (például SAML és Open ID-kapcsolat) áttelepítését. Ezek az alkalmazások Újrakonfigurálhatók az Azure AD-vel való hitelesítéshez az alkalmazás-katalógus beépített összekötője vagy az alkalmazás Azure AD-ben való regisztrálása révén. A régebbi protokollokat használó alkalmazások a [alkalmazásproxy](./what-is-application-proxy.md)használatával integrálhatók.

További információ: [milyen típusú alkalmazásokat tud integrálni az Azure ad-vel](./what-is-application-management.md)?

Ha [Azure ad Connect Health engedélyezve](../hybrid/how-to-connect-health-adfs.md)van, a [AD FS alkalmazás tevékenységi jelentés használatával áttelepítheti az alkalmazásokat az Azure ad-be](./migrate-adfs-application-activity.md) .

### <a name="the-migration-process"></a>Az áttelepítési folyamat

Az alkalmazás Azure AD-hitelesítésre való áthelyezése során az alkalmazások és a konfiguráció megfelelő tesztelését. Javasoljuk, hogy az éles környezetbe való áttéréshez a meglévő tesztelési környezeteket továbbra is használja. Ha a tesztkörnyezet jelenleg nem érhető el, az alkalmazás architektúrája alapján beállíthatja [Azure app Service](https://azure.microsoft.com/services/app-service/) vagy az [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)használatával.

Dönthet úgy, hogy külön Azure AD-bérlőt állít be, amelyet az alkalmazás konfigurációjának fejlesztésekor használ.

Az áttelepítési folyamat így néz ki:

**1. fázis – aktuális állapot: az üzemi alkalmazás hitelesítése AD FS**

![1. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**2. fázis – nem kötelező: az alkalmazás tesztelési példánya, amely az Azure-bérlő tesztelésére mutat**

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztelési példányát egy teszt Azure AD-bérlőre mutassa, és végezze el a szükséges módosításokat. Az alkalmazás a teszt Azure AD-bérlőben található felhasználókkal tesztelhető. A fejlesztési folyamat során olyan eszközöket használhat, mint például a [Hegedűs](https://www.telerik.com/fiddler) a kérelmek és válaszok összehasonlításához és ellenőrzéséhez.

Ha egy különálló tesztelési bérlő beállítása nem valósítható meg, ugorja át ezt a szakaszt, és állítsa be az alkalmazás egy tesztelési példányát, és mutasson rá az éles Azure AD-bérlőre az alábbi 3. szakaszban leírtak szerint.

![2. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**3. fázis – az Azure-bérlő éles üzemre mutató alkalmazásának tesztelése**

Frissítse a konfigurációt úgy, hogy az alkalmazás tesztelési példányát az Azure éles példányára mutassa. Mostantól tesztelheti az éles példányban lévő felhasználókat. Ha szükséges, tekintse át a cikk szakaszát a felhasználók átváltásához.

![3. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**4. fázis – üzemi AD-bérlőre mutató éles Üzembeli alkalmazás**

Frissítse a termelési alkalmazás konfigurációját, hogy az éles Azure-bérlőre mutasson.

![4. áttelepítési fázis ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 A AD FS hitelesítő alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. A Migrálás megkezdése előtt a [Azure ad Connect Sync](../hybrid/how-to-connect-sync-whatis.md) használatával szinkronizálhatja a helyszíni környezet és az Azure ad-beli identitás adatait. Az áttelepítés előtt ellenőrizze ezeket a csoportokat és tagságot, hogy az alkalmazás áttelepítésekor ugyanazokhoz a felhasználókhoz is hozzáférést biztosíthat.

### <a name="line-of-business-lob-apps"></a>Üzletági (LOB) alkalmazások

A LOB-alkalmazásokat a szervezet belsőleg fejleszti, vagy az adatközpontba telepített szabványos csomagolt termékként is elérhetővé válik. Ilyenek például a Windows Identity Foundation és a SharePoint-alkalmazások (nem a SharePoint Online) szolgáltatásokra épülő alkalmazások.

Az OAuth 2,0, OpenID Connect vagy WS-Federationt használó LOB-alkalmazások az Azure AD-ben is integrálhatók az alkalmazások [regisztrálásával](../develop/quickstart-register-app.md). Integrálhatja az SAML 2,0-et vagy WS-Federationt használó [egyéni alkalmazásokat a](./add-application-portal.md) [Azure Portal](https://portal.azure.com/)vállalati alkalmazások lapján.

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés

Az SAML 2,0 hitelesítést használó alkalmazások az [SAML-alapú egyszeri bejelentkezéshez](./what-is-single-sign-on.md) (SAML-alapú SSO) konfigurálhatók. Az [SAML-alapú egyszeri bejelentkezés](./what-is-single-sign-on.md)használata esetén a felhasználókat az SAML-jogcímek által meghatározott szabályok alapján rendelheti hozzá bizonyos alkalmazási szerepkörökhöz.

Az SAML-alapú egyszeri bejelentkezéshez használható SaaS-alkalmazás konfigurálásával kapcsolatban lásd: [SAML-alapú egyszeri bejelentkezés konfigurálása](./view-applications-portal.md).

![SSO SAML felhasználói képernyőképek ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Számos SaaS-alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal](../saas-apps/tutorial-list.md) , amely VÉGIGVEZETI az SAML-alapú egyszeri bejelentkezés konfigurációjának lépésein.

![alkalmazás-oktatóanyag](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Egyes alkalmazások migrálása egyszerű. Az összetettebb követelményekkel rendelkező, például egyedi jogcímeket igénylő alkalmazások esetében azonban további konfigurálásra lehet szükség az Azure AD-ben és/vagy az Azure AD Connectben. További információ a támogatott jogcímek hozzárendeléséről: [jogcím-hozzárendelés Azure Active Directoryban](../develop/active-directory-claims-mapping.md).

Vegye figyelembe a következő korlátozásokat az attribútumok leképezése során:

* Nem minden AD FS kiállítható attribútum jelenik meg az Azure AD-ben az SAML-tokenekre való kibocsátáshoz, még akkor is, ha ezek az attribútumok szinkronizálva vannak. Amikor szerkeszti az attribútumot, az érték legördülő lista megjeleníti az Azure AD-ben elérhető különböző attribútumokat. Ellenőrizze [Azure ad Connect szinkronizálási](../hybrid/how-to-connect-sync-whatis.md) konfigurációját, hogy a szükséges attribútumok – például a sAMAccountName – szinkronizálva legyenek az Azure ad-val. A bővítmény attribútumaival olyan jogcímeket hozhat létre, amelyek nem részei az Azure AD általános felhasználói sémájának.

* A leggyakoribb forgatókönyvekben csak a NameID jogcím és egyéb általános felhasználóazonosító jogcímek szükségesek az alkalmazáshoz. Annak megállapításához, hogy szükség van-e további jogcímek elvégzésére, vizsgálja meg, milyen jogcímeket kell kiadnia AD FS

* Nem minden jogcím lehet probléma, mivel egyes jogcímek védelme az Azure AD-ben történik.

* A titkosított SAML-tokenek használatának lehetősége már előzetes verzióban érhető el. További információ [: a vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>Szolgáltatott szoftver (SaaS) alkalmazások

Ha a felhasználó SaaS-alkalmazásokba, például Salesforce, ServiceNow vagy munkanapokba jelentkezik be, és integrálva van AD FS, akkor összevont bejelentkezést használ az SaaS-alkalmazásokhoz.

A legtöbb SaaS-alkalmazás már konfigurálható az Azure AD-ben. A Microsoft számos előre konfigurált kapcsolattal rendelkezik az SaaS-alkalmazásokhoz az  [Azure ad-alkalmazás galériájában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), amely egyszerűbbé teszi az áttérést. Az SAML 2,0-alkalmazások az Azure AD-katalógusban vagy nem katalógusbeli [alkalmazásokban](./add-application-portal.md)is integrálhatók az Azure ad-be.

Az OAuth 2.0 vagy OpenID Connect hitelesítést használó alkalmazások hasonló módon, [alkalmazásregisztrációként](../develop/quickstart-register-app.md) integrálhatók az Azure AD-be. Az örökölt protokollokat használó alkalmazások az [azure ad Application proxy](./application-proxy.md) használatával hitelesíthetők az Azure ad-vel.

Az SaaS-alkalmazások bevezetésével kapcsolatos esetleges problémákért forduljon a [SaaS Application Integration support aliashoz](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**SAML-aláíró tanúsítványok az egyszeri** bejelentkezéshez: a tanúsítványok aláírása fontos részét képezi az SSO-telepítésnek. Az Azure AD létrehozza az aláíró tanúsítványokat, hogy SAML-alapú összevont SSO-t hozzon létre SaaS-alkalmazásaihoz. A katalógus vagy a nem katalógusbeli alkalmazások hozzáadása után a hozzáadott alkalmazást az összevont egyszeri bejelentkezés lehetőséggel állíthatja be. Lásd: a [tanúsítványok kezelése összevont egyszeri bejelentkezéshez Azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>A ma áthelyezhető alkalmazások és konfigurációk

Az egyszerűen áthelyezhető alkalmazások közé tartoznak az SAML 2,0-alkalmazások, amelyek a konfigurációs elemek és jogcímek szabványos készletét használják:

* Felhasználó egyszerű neve

* E-mail-cím

* Utónév

* vezetéknév;

* Az SAML **NameID**-ként használt alternatív attribútum, például az Azure AD Mail attribútum, a Mail előtag, az alkalmazott azonosítója, az 1-15 bővítményattribútumok vagy a helyszíni **SamAccountName**. További információkat itt talál: A [NameIdentifier](../develop/active-directory-saml-claims-customization.md) jogcím szerkesztése.

* Egyéni jogcímek.

Az Azure AD-ba való Migrálás további konfigurációs lépéseinek megkövetelése:

* Egyéni engedélyezési vagy Multi-Factor Authentication (MFA) szabályok a AD FSban. Ezeket az [Azure ad feltételes hozzáférési](../conditional-access/overview.md) funkciója segítségével konfigurálhatja.

* Több válasz URL-végponttal rendelkező alkalmazások. Ezeket az Azure AD-ben a PowerShell vagy a Azure Portal felület használatával konfigurálhatja.

* A WS-Federationt használó alkalmazások, például a SharePoint-alkalmazások, amelyek SAML 1.1-es verziójú jogkivonatot igényelnek. Ezeket manuálisan is konfigurálhatja a PowerShell használatával. Hozzáadhat egy előre integrált általános sablont is a SharePoint és SAML 1,1-alkalmazásokhoz a katalógusból. Az SAML 2,0 protokollt támogatjuk.

* Az összetett jogcímek kiállítása átalakítja a szabályokat. A támogatott jogcímek hozzárendelésével kapcsolatos további információkért lásd:
   *  [Jogcímtársítások az Azure Active Directoryban](../develop/active-directory-claims-mapping.md)
   * [Az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz Azure Active Directory](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Az Azure AD-ben jelenleg nem támogatott alkalmazások és konfigurációk

A következő képességeket igénylő alkalmazások nem telepíthetők át még ma.

**Protokoll képességei**

* Az WS-Trust ActAs minta támogatása

* Az SAML-összetevő felbontása

* Az aláírt SAML-kérések aláírásának ellenőrzése megjegyzi, hogy az aláírt kérések elfogadva lettek, de a rendszer nem ellenőrzi az aláírást.
Mivel az Azure AD csak a jogkivonatot adja vissza az alkalmazásban előre konfigurált végpontoknak, az aláírás-ellenőrzés valószínűleg nem szükséges a legtöbb esetben.

**Jogkivonatokkal kapcsolatos jogcímek**

* Nem az Azure AD-címtárból származó attribútum-tárolók jogcímei, kivéve, ha az adatok szinkronizálva vannak az Azure AD-vel. További információ: az [Azure ad SYNCHRONIZATION API áttekintése](/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Több értékkel rendelkező címtár-attribútumok kiállítása. Például nem lehet kiadni többértékű jogcímet a proxy címeihez.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Alkalmazásbeállítások leképezése AD FSról az Azure AD-be

A migrálás első lépéseként mérje fel az alkalmazás helyszíni konfigurációját, majd képezze le az Azure AD-be. A AD FS és az Azure AD hasonlóan működik, így a megbízhatóság, a bejelentkezési és a kijelentkezési URL-címek, valamint az azonosítók konfigurálásának fogalmai mindkét esetben érvényesek. Dokumentálja az alkalmazások AD FS konfigurációs beállításait, így egyszerűen konfigurálhatja őket az Azure AD-ben.

### <a name="map-app-configuration-settings"></a>Az alkalmazás konfigurációs beállításainak leképezése

Az alábbi táblázat egy AD FS függő entitás megbízhatósága Azure AD Enterprise-alkalmazáshoz való beállításainak leggyakoribb leképezéseit ismerteti:

* AD FS – megkeresheti a beállítást az alkalmazáshoz tartozó AD FS függő entitás megbízhatóságában. Kattintson a jobb gombbal a függő entitásra, és válassza a tulajdonságok lehetőséget.

* Azure AD – a beállítás az egyes alkalmazások egyszeri bejelentkezési tulajdonságaiban [Azure Portal](https://portal.azure.com/) belül van konfigurálva.

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ben| SAML-jogkivonat |
| - | - | - | - |
| **Alkalmazás bejelentkezési URL-címe** <p>Az alkalmazásba a szolgáltató (SP) által kezdeményezett SAML-folyamatba bejelentkező felhasználó URL-címe.| N/A| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| N/A |
| **Alkalmazás válasz URL-címe** <p>Az alkalmazás URL-címe az identitás-szolgáltató (identitásszolgáltató) szemszögéből. A identitásszolgáltató elküldi a felhasználót és a tokent, miután a felhasználó bejelentkezett a identitásszolgáltató.  Ezt az SAML-jogcímek **fogyasztói végpontjának** is nevezzük.| Válassza a **végpontok** fület.| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| A cél elem az SAML-jogkivonatban. Példaérték: `https://contoso.my.salesforce.com` |
| **Alkalmazás kijelentkezési URL-címe** <p>Ezt az URL-címet kell elküldeni a "kijelentkezési tisztítási" kérések elküldésekor, amikor egy felhasználó kijelentkezik az alkalmazásból. A identitásszolgáltató elküldi a kérést, hogy kijelentkezzen a felhasználótól az összes többi alkalmazásból is.| Válassza a **végpontok** fület.| Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| N/A |
| **Alkalmazásazonosító** <p>Ez az alkalmazás azonosítója a identitásszolgáltató szemszögéből. A bejelentkezési URL-cím értéke gyakran használatos az azonosítóhoz (de nem mindig).  Néha az alkalmazás meghívja ezt az "Entity ID"-t.| Az **azonosítók** lap kijelölése|Alapszintű SAML-konfiguráció megnyitása SAML-alapú bejelentkezéssel| Leképezi az SAML-jogkivonat **célközönség** elemét. |
| **Alkalmazás összevonási metaadatai** <p>Ez az alkalmazás összevonási metaadatainak helye. Az identitásszolgáltató használja egyes konfigurációs beállítások, például a végpontok vagy a titkosítási tanúsítványok automatikus frissítéséhez.| A **figyelés** lap kiválasztása| N/A. Az Azure AD nem támogatja az alkalmazások összevonási metaadatainak közvetlen felhasználását. Az összevonási metaadatokat manuálisan is importálhatja.| N/A |
| **Felhasználói azonosító/név azonosítója** <p>A felhasználó identitását az Azure AD-ből vagy az AD FS-ből az alkalmazás felé egyértelműen azonosító attribútum.  Ez az attribútum általában a felhasználó egyszerű felhasználóneve vagy e-mail-címe.| Jogcím-szabályok. A legtöbb esetben a jogcím szabály a NameIdentifier végződő típussal rendelkező jogcímet bocsát ki.| Az azonosítót a **felhasználói attribútumok és jogcímek** fejléce alatt találja. Alapértelmezés szerint a rendszer az UPN-t használja| Leképezi az SAML-token **NameID** elemét. |
| **Egyéb jogcímek** <p>A identitásszolgáltató és az alkalmazás között gyakran küldött egyéb jogcím-információk közé tartozik például az utónév, a vezetéknév, az E-mail cím és a csoporttagság.| Az AD FS-ben ez a függő entitásra vonatkozó egyéb jogcímszabályokként található meg.| Az azonosító a fejléc **felhasználói attribútumok & jogcímek** alatt található. Válassza ki az **Egyéb felhasználói attribútumok megtekintése** és szerkesztése elemet.| N/A |


### <a name="map-identity-provider-idp-settings"></a>A Térkép identitás-szolgáltatója (identitásszolgáltató) beállításai

Konfigurálja úgy az alkalmazásokat, hogy az egyszeri bejelentkezéshez az Azure AD-t és a AD FS mutassanak. Itt az SAML protokollt használó SaaS-alkalmazásokra fogunk összpontosítani. Ez a koncepció azonban az egyéni LOB-alkalmazásokra is kiterjed.

> [!NOTE]
> A konfigurációs értékek az Azure AD-ben azt a mintát követik, ahol az Azure-bérlő azonosítója {bérlő-azonosító}, az alkalmazás azonosítója pedig {Application-ID} értékre vált. Ezeket az információkat a [Azure Portal](https://portal.azure.com/) Azure Active Directory > tulajdonságok területén találja:

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

Ha áthelyezi az alkalmazást az Azure AD-ba, hozzon létre leképezéseket a meglévő biztonsági házirendekről az Azure AD-ben elérhető egyenértékű vagy alternatív változatokra. Annak biztosítása, hogy ezek a leképezések az alkalmazás tulajdonosai által megkövetelt biztonsági szabványok teljesítése közben is megvalósíthatók legyenek, az alkalmazások áttelepítésének a többi része lényegesen egyszerűbb lesz.

Az egyes szabálytípus és a hozzájuk tartozó példák esetében azt javasoljuk, hogy a szabály hogyan néz ki a AD FSban, a AD FS szabály nyelvének megfelelő kódja, és hogyan jelennek meg a Térkép az Azure AD-ben.

### <a name="map-authorization-rules"></a>Engedélyezési szabályok leképezése

Az alábbiakban példákat talál a AD FS engedélyezési szabályaira, valamint arról, hogy miként képezhetők le az Azure AD-be:

#### <a name="example-1-permit-access-to-all-users"></a>1. példa: az összes felhasználó hozzáférésének engedélyezése

Az összes felhasználóhoz való hozzáférés engedélyezése AD FSban:

![A képernyőképen az egyszeri Sign-On beállítása az SAML-vel párbeszédpanel jelenik meg.](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Ez az alábbi módokon mutat az Azure AD-re:

A [Azure Portalban](https://portal.azure.com/):
* 1. lehetőség: a nem kötelező felhasználói hozzárendelés beállítása ![az SaaS-alkalmazások hozzáférés-vezérlési házirendjének szerkesztése ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Vegye figyelembe, hogy a felhasználói hozzárendelés szükséges kapcsolójának Igen értékre való beállítása megköveteli, hogy a felhasználók hozzá legyenek rendelve az alkalmazáshoz, hogy hozzáférjenek. Ha a nem értékre van állítva, minden felhasználó rendelkezik hozzáféréssel. Ez a kapcsoló nem szabályozza, hogy a felhasználók mit láthatnak a saját alkalmazások felületén.


* 2. lehetőség: a felhasználók és csoportok lapon rendelje hozzá az alkalmazást a "minden felhasználó" automatikus csoporthoz. <p>
Engedélyeznie kell a [dinamikus csoportokat](../enterprise-users/groups-create-rule.md) az Azure ad-bérlőben az alapértelmezett "minden felhasználó" csoport számára.

   ![Saját SaaS-alkalmazások az Azure AD-ben ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>2. példa: explicit módon egy csoport engedélyezése

Explicit csoportos hitelesítés AD FSban:


![Képernyőfelvétel: a szabály szerkesztése párbeszédpanel a Tartománygazdák engedélyezése jogcím-szabályhoz.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Ez a szabály az Azure AD-hez való leképezése:

A [Azure Portal](https://portal.azure.com/)először [létre kell hoznia egy felhasználói csoportot](../fundamentals/active-directory-groups-create-azure-portal.md) , amely megfelel a AD FS felhasználói csoportjának, majd az alkalmazás engedélyeit a következő csoporthoz rendeli hozzá:

![Hozzárendelés hozzáadása ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>3. példa: egy adott felhasználó engedélyezése

Explicit felhasználói hitelesítés AD FSban:

![A képernyőképen a szabály szerkesztése párbeszédpanel jelenik meg, amely lehetővé teszi egy adott felhasználói jogcím szabályának engedélyezését az elsődleges S I D típusú bejövő jogcím-típussal.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Ez a szabály az Azure AD-hez való leképezése:

A [Azure Portal](https://portal.azure.com/)adjon hozzá egy felhasználót az alkalmazáshoz az alkalmazás hozzárendelés hozzáadása lapján az alábbi ábrán látható módon:

![Saját SaaS-alkalmazások az Azure-ban ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Térkép Multi-Factor Authentication szabályok

[Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) és AD FS helyszíni telepítése továbbra is az áttelepítés után fog működni, mert AD FS összevonták. Azonban érdemes lehet áttelepíteni az Azure-ba az Azure AD feltételes hozzáférési munkafolyamataihoz kötött beépített MFA-képességeket.

Az alábbiakban példákat talál a AD FS MFA-szabályok típusaira, és azt, hogy miként képezhetők le az Azure AD-be különböző feltételek alapján:

MFA-szabályok beállításai a AD FSban:

![Képernyőfelvétel: az Azure a D-vel kapcsolatos feltételek a Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>1. példa: az MFA érvényesítése felhasználók/csoportok alapján

A felhasználó/csoport választó egy olyan szabály, amely lehetővé teszi, hogy az MFA-t csoportonként (csoport SID) vagy felhasználónkénti (elsődleges SID) alapon kényszerítse ki. A felhasználók/csoportok hozzárendelésein kívül a AD FS MFA konfigurációs felhasználói felületének minden további jelölőnégyzete a felhasználó/csoport szabály érvényesítése után kiértékelt további szabályok.


MFA-szabályok megadása egy felhasználó vagy egy csoport számára az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Válassza a **hozzárendelések** lehetőséget. Adja hozzá azokat a felhasználó (ka) t vagy csoportot, amelyeken az MFA-t szeretné kikényszeríteni.

3. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:

![Képernyőfelvétel: az engedélyezési ablaktábla, ahol hozzáférést adhat meg.](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>2. példa: az MFA kikényszerítés a nem regisztrált eszközökre

MFA-szabályok megadása a nem regisztrált eszközökhöz az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Állítsa be a **hozzárendeléseket** az **összes felhasználóra**.

3. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:

![Képernyőfelvétel: az engedélyezési ablaktábla, ahol hozzáférést biztosíthat, és egyéb korlátozásokat is megadhat.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Ha úgy állítja be a több vezérlőt, hogy megkövetelje a kiválasztott vezérlők valamelyikét, az azt jelenti, hogy ha a felhasználó a jelölőnégyzetben megadott feltételek valamelyikét betölti, akkor hozzáférést kap az alkalmazáshoz.

#### <a name="example-3-enforce-mfa-based-on-location"></a>3. példa: MFA érvényesítése hely alapján

A felhasználó Azure AD-beli helye alapján megadott MFA-szabályok meghatározása:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. Állítsa be a **hozzárendeléseket** az **összes felhasználóra**.

1. A [nevesített helyek konfigurálása az Azure ad-ben](../reports-monitoring/quickstart-configure-named-locations.md) , máskülönben a vállalati hálózaton belülről történő összevonás megbízható.

1. Adja meg a **feltételek szabályait** , hogy megadja azokat a helyszíneket, amelyeknek az MFA-t ki szeretné kényszeríteni.

![Képernyőfelvétel: a feltételek szabályai a helyszínek ablaktáblán láthatók.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Konfigurálja a **hozzáférés-vezérlési** beállításokat az alább látható módon:


![Hozzáférés-vezérlési házirendek leképezése](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Attribútumok kibocsátása jogcím szabályként

Íme egy példa arra, hogy az attribútumok hogyan vannak leképezve a AD FSban:

![Képernyőfelvétel: a szabály szerkesztése párbeszédpanel, amely az attribútumok jogcímként való kibocsátását mutatja be.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Ez a szabály az Azure AD-hez való leképezése:

A [Azure Portal](https://portal.azure.com/)válassza a **vállalati alkalmazások**, az **egyszeri bejelentkezés** lehetőséget, és adja hozzá az **SAML-jogkivonat attribútumait** az alábbi ábrán látható módon:

![A képernyőképen a vállalati alkalmazás egyszeri bejelentkezési lapja látható.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Beépített hozzáférés-vezérlési szabályzatok leképezése

A AD FS 2016 számos beépített hozzáférés-vezérlési szabályzattal rendelkezik, amelyek közül választhat:

![Az Azure AD beépített hozzáférés-vezérléssel rendelkezik](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


A beépített szabályzatok Azure AD-ben történő megvalósításához használhat [új feltételes hozzáférési házirendet](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) , és konfigurálhatja a hozzáférés-vezérlést, vagy használhatja az egyéni házirend-tervezőt AD FS 2016-ben a hozzáférés-vezérlési házirendek konfigurálásához. A szabály szerkesztőjének teljes listája lehetővé teszi az engedélyezést, és kivéve azokat a lehetőségeket, amelyek segítségével bármilyen típusú permutációt készíthet.

![Azure AD-hozzáférés-vezérlési szabályzatok](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Ebben a táblázatban néhány hasznos engedély szerepel, de a lehetőségek és az Azure AD-hez való leképezésük is.


| Beállítás | Az engedélyezési lehetőség konfigurálása az Azure AD-ben| Az Azure AD-ban való kivétel beállítása |
| - | - | - |
| Adott hálózatról| Leképezi az Azure AD-ben [elnevezett helyre](../reports-monitoring/quickstart-configure-named-locations.md)| A [megbízható helyek](../conditional-access/location-condition.md) **kizárási** beállításának használata |
| Adott csoportoktól| [Felhasználó/csoport hozzárendelésének beállítása](./assign-user-or-group-access-portal.md)| A **kizárás** lehetőség használata a felhasználók és csoportok számára |
| Megadott megbízhatósági szinttel rendelkező eszközökről| Állítsa be ezt az "Eszközállapot" vezérlőelemből a hozzárendelések – > feltételek szakaszban| Használja a **kizárás** lehetőséget az eszköz állapota feltételnél, és adja meg az **összes eszközt** |
| A kérelemben megadott jogcímek| Ez a beállítás nem telepíthető át| Ez a beállítás nem telepíthető át |


Példa a megbízható helyek kizárási beállításának konfigurálására a Azure Portalban:

![A hozzáférés-vezérlési szabályzatok leképezése képernyőkép](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Felhasználók átváltása AD FSról az Azure AD-be

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS csoportok szinkronizálása az Azure AD-ben

Az engedélyezési szabályok leképezése esetén a AD FS hitelesítő alkalmazások Active Directory csoportokat használhatnak az engedélyekhez. Ilyen esetben a [Azure ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771) használatával szinkronizálja ezeket a csoportokat az Azure ad-vel az alkalmazások áttelepítése előtt. Győződjön meg arról, hogy az áttelepítés előtt ellenőrzi a csoportokat és a tagságot, hogy az alkalmazás áttelepítésekor ugyanazokhoz a felhasználókhoz is hozzáférést biztosíthat.

További információ: [Active Directoryról szinkronizált Group attributes használatának előfeltételei](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="setup-user-self-provisioning"></a>Felhasználó önálló kiépítés beállítása

Egyes SaaS-alkalmazások támogatják a felhasználók önálló kiépítését, amikor először jelentkeznek be az alkalmazásba. Azure Active Directory (Azure AD) esetében az alkalmazás üzembe helyezésének kifejezése arra a felhasználói identitások és szerepkörök automatikus létrehozására utal, amelyekhez[](https://azure.microsoft.com/overview/what-is-saas/)a felhasználóknak hozzá kell férniük. Az áttelepített felhasználók már rendelkeznek fiókkal az SaaS-alkalmazásban. Az áttelepítés után hozzáadott összes új felhasználót ki kell építeni. Az [SaaS-alkalmazások üzembe](../app-provisioning/user-provisioning.md) helyezésének tesztelése az alkalmazás migrálása után.

### <a name="sync-external-users-in-azure-ad"></a>Külső felhasználók szinkronizálása az Azure AD-ben

A meglévő külső felhasználók két fő módon állíthatók be AD FSon belül:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>A szervezeten belüli helyi fiókkal rendelkező külső felhasználók

Továbbra is használhatja ezeket a fiókokat ugyanúgy, ahogy a belső felhasználói fiókok működnek. Ezeknek a külső felhasználói fiókoknak van egy alapelve a szervezeten belül, bár a fiók e-mail-címe külsőleg is mutathat. Az áttelepítés folyamata során kihasználhatja az [Azure ad B2B](../external-identities/what-is-b2b.md) által kínált előnyök előnyeit azáltal, hogy áttelepíti ezeket a felhasználókat a saját vállalati identitásának használatára, ha az identitás elérhető. Ez egyszerűbbé teszi a felhasználók bejelentkezési folyamatát, mivel azok gyakran be vannak jelentkezve a saját vállalati bejelentkezéssel. A szervezet adminisztrációja is megenyhül, mivel a továbbiakban nem kell a külső felhasználók fiókjait kezelni.

#### <a name="federated-external-identities"></a>Összevont külső identitások

Ha jelenleg egyesítő egy külső szervezettel, néhány módszer áll rendelkezésére a következők elvégzéséhez:

* [Vegye fel Azure Active Directory B2B együttműködési felhasználókat a Azure Portal](../external-identities/add-users-administrator.md). Proaktív módon küldhet VÁLLALATKÖZI együttműködést az Azure AD felügyeleti portálról a partnerszervezet számára az egyes tagok számára, hogy továbbra is használhassa azokat az alkalmazásokat és eszközöket, amelyekhez a használatban van.

* [Hozzon létre egy önkiszolgáló vállalatközi regisztrációs munkafolyamatot](../external-identities/self-service-portal.md) , amely a vállalatközi MEGHÍVó API-val kérelmet készít a partnervállalat egyes felhasználói számára.

Függetlenül attól, hogy a meglévő külső felhasználók hogyan vannak konfigurálva, valószínűleg rendelkeznek a fiókjához társított engedélyekkel a csoporttagság vagy a konkrét engedélyek szerint. Ellenőrizze, hogy az engedélyeket át kell-e telepíteni vagy törölni kell-e. A szervezeten belüli, külső felhasználót képviselő fiókokat le kell tiltani, ha a felhasználót áttelepítették egy külső identitásra. Az áttelepítési folyamatot az üzleti partnerekkel kell megvitatni, mivel előfordulhat, hogy a rendszer megszakítja az erőforrásokhoz való kapcsolódás lehetőségét.

## <a name="migrate-and-test-your-apps"></a>Alkalmazások migrálása és tesztelése

Kövesse a cikkben részletezett áttelepítési folyamatot.

Ezután lépjen a [Azure Portalra](https://aad.portal.azure.com/) , és ellenőrizze, hogy sikeres volt-e az áttelepítés. Kövesse az alábbi utasításokat:
1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget, és keresse meg az alkalmazást a listából.

1. Válassza   >  a **felhasználók és csoportok** kezelése lehetőséget, ha legalább egy felhasználót vagy csoportot szeretne hozzárendelni az alkalmazáshoz.

1. Válassza a  >  **feltételes hozzáférés** kezelése lehetőséget. Tekintse át a szabályzatok listáját, és ellenőrizze, hogy nem blokkolja-e az alkalmazáshoz való hozzáférést [feltételes hozzáférési szabályzattal](../conditional-access/overview.md).

Az alkalmazás konfigurálásának módjától függően ellenőrizze, hogy az SSO megfelelően működik-e.

| Hitelesítéstípus| Tesztelés |
| - | - |
| OAuth/OpenID Connect| Válassza a **vállalati alkalmazások > engedélyek** lehetőséget, és győződjön meg arról, hogy hozzájárult a szervezetében az alkalmazás felhasználói beállításaiban való használathoz.
‎ |
| SAML-alapú egyszeri bejelentkezés| Használja az **egyszeri bejelentkezés** alatt található [SAML-beállítások tesztelése](./debug-saml-sso-issues.md) gombot.
‎ |
| EGYSZERI BEJELENTKEZÉS Password-Based| Töltse le és telepítse a [MyApps biztonságos bejelentkezési](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [bővítményét](../user-help/my-apps-portal-end-user-access.md). Ez a bővítmény segítséget nyújt a szervezet olyan felhőalapú alkalmazásainak elindításához, amelyekhez egyszeri bejelentkezéses folyamatot kell használni.
‎ |
| Alkalmazásproxy| Győződjön meg arról, hogy az összekötő fut, és hozzá van rendelve az alkalmazáshoz. További segítségért tekintse meg az [alkalmazásproxy hibaelhárítási útmutatóját](./application-proxy-troubleshoot.md) .
‎ |

> [!NOTE]
> A régi AD FS környezetből származó cookie-k továbbra is tartósak lesznek a felhasználó számítógépén. Ezek a cookie-k problémákat okozhatnak az áttelepítés során, mivel a felhasználók a régi AD FS bejelentkezési környezethez, illetve az új Azure AD-bejelentkezéshez irányíthatók. Előfordulhat, hogy manuálisan kell törölnie a felhasználói böngésző cookie-jait, vagy parancsfájlt kell használnia. Használhatja a System Center Configuration Manager vagy egy hasonló platformot is.

### <a name="troubleshoot"></a>Hibaelhárítás

Ha az áttelepített alkalmazások tesztelése hibákba ütközik, akkor előfordulhat, hogy az első lépés a meglévő AD FS függő entitásokhoz való visszatérés előtt. Lásd: az [SAML-alapú egyszeri bejelentkezések hibakeresése Azure Active Directory-alkalmazásokban](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Áttelepítés visszaállítása

Ha az áttelepítés sikertelen, javasoljuk, hogy hagyja meg a meglévő függő entitásokat a AD FS-kiszolgálókon, és távolítsa el a függő entitásokhoz való hozzáférést. Ez lehetővé teszi, hogy a telepítés során szükség esetén gyors tartalékot biztosítson.

### <a name="end-user-communication"></a>Végfelhasználói kommunikáció

Míg maga a tervezett leállási időszak is minimális lehet, továbbra is meg kell terveznie, hogy az alkalmazottak proaktív módon kommunikálnak-e az alkalmazottakkal, miközben a AD FS az Azure AD-be. Győződjön meg arról, hogy az alkalmazás felhasználói felülete rendelkezik visszajelzési gombbal, vagy ha problémákba ütközik az ügyfélszolgálatnál.

Az üzembe helyezés befejezése után a kommunikációt a sikeres üzembe helyezést követően tájékoztathatja a felhasználókat, és emlékeztetheti őket a szükséges új lépésekre.

* Kérje meg a felhasználókat, hogy az [alkalmazások](https://myapps.microsoft.com) használatával férhessenek hozzá az összes áttelepített alkalmazáshoz.

* Emlékeztesse a felhasználókat, hogy frissíteniük kell az MFA-beállításait.

* Ha Self-Service jelszó-visszaállítást telepít, előfordulhat, hogy a felhasználóknak frissíteniük vagy ellenőriznie kell a hitelesítési módszereiket. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

Külső felhasználókkal folytatott kommunikáció: a felhasználók ezen csoportja általában a legjelentősebb hatással van a probléma esetére. Ez különösen akkor igaz, ha a biztonsági helyzet a feltételes hozzáférési szabályok vagy kockázati profilok eltérő készletét diktálja külső partnerek számára. Győződjön meg arról, hogy a külső partnerek tisztában vannak a felhő áttelepítési időpontjával, és olyan időkerettel rendelkeznek, amelyben a rendszer azt javasolja, hogy vegyen részt egy olyan kísérleti környezetben, amely a külső együttműködésre jellemző összes folyamatot teszteli. Végezetül ellenőrizze, hogy van-e lehetőség az ügyfélszolgálat elérésére a problémák elhárítása esetén.

## <a name="next-steps"></a>Következő lépések

[Az alkalmazás hitelesítésének áttelepítése az Azure ad-be](https://aka.ms/migrateapps/whitepaper)<p>
[Feltételes hozzáférés](../conditional-access/overview.md) és [MFA](../authentication/concept-mfa-howitworks.md) beállítása

Próbálja ki a Wise-kód példáját:[AD FS az Azure ad Application Migration forgatókönyv fejlesztőknek](https://aka.ms/adfsplaybook)
