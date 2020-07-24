---
title: A bérlői korlátozások használata az SaaS-alkalmazásokhoz való hozzáférés kezelésére – Azure AD
description: A bérlői korlátozások használata annak kezeléséhez, hogy mely felhasználók férhetnek hozzá az alkalmazásokhoz az Azure AD-bérlőn alapuló módon.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae90a682ea2d1abb8159ec28ed02ed122494f512
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019250"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>A bérlői korlátozások használata a SaaS-Felhőbeli alkalmazásokhoz való hozzáférés kezelésére

Azok a nagyméretű szervezetek, amelyek hangsúlyozzák a biztonságot, hogy a Felhőbeli szolgáltatásokra, például az Office 365-re kívánnak áttérni, de tudniuk kell, hogy a felhasználók csak a jóváhagyott erőforrásokat férhetnek hozzá A vállalatok hagyományosan korlátozzák a tartományneveket vagy az IP-címeket, amikor a hozzáférést szeretnék kezelni. Ez a megközelítés egy olyan világban meghiúsul, ahol a szolgáltatott szoftverek (vagy SaaS-alkalmazások) nyilvános felhőben futnak, és megosztott tartományneveket futtatnak, például a [Outlook.Office.com](https://outlook.office.com/) és a [login.microsoftonline.com](https://login.microsoftonline.com/). Ezeknek a címeknek a blokkolása révén a felhasználók teljes mértékben hozzáférhetnek az Outlookhoz a világhálón ahelyett, hogy csak a jóváhagyott identitásokra és erőforrásokra korlátozzák őket.

A kihíváshoz tartozó Azure Active Directory (Azure AD) megoldás a bérlői korlátozások nevű szolgáltatás. A bérlői korlátozásokkal a szervezetek az Azure AD-bérlő alapján vezérelhetik a SaaS-Felhőbeli alkalmazásokhoz való hozzáférést az egyszeri bejelentkezéshez használt alkalmazások esetében. Előfordulhat például, hogy engedélyezni szeretné a szervezet Office 365-alkalmazásaihoz való hozzáférést, és megakadályozza, hogy az azonos alkalmazások más szervezetek példányaihoz hozzáférjenek.  

A bérlői korlátozásokkal a szervezetek meghatározhatják azon bérlők listáját, amelyekhez a felhasználók hozzáférhetnek. Az Azure AD ezt követően csak az engedélyezett bérlők számára biztosít hozzáférést.

Ez a cikk az Office 365 bérlői korlátozásait ismerteti, de a szolgáltatásnak működnie kell minden olyan SaaS Cloud-alkalmazással, amely modern hitelesítési protokollokat használ az Azure AD-vel az egyszeri bejelentkezéshez. Ha SaaS-alkalmazásokat használ az Office 365 által használt bérlőtől eltérő Azure AD-Bérlővel, győződjön meg arról, hogy az összes szükséges bérlő engedélyezett. A SaaS Cloud apps szolgáltatással kapcsolatos további információkért tekintse meg a [Active Directory piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

## <a name="how-it-works"></a>Működés

A teljes megoldás a következő összetevőket tartalmazza:

1. **Azure ad**: Ha a `Restrict-Access-To-Tenants: <permitted tenant list>` jelen van, az Azure ad csak az engedélyezett bérlők biztonsági jogkivonatait bocsátja ki.

2. Helyszíni **proxykiszolgáló-infrastruktúra**: ez az infrastruktúra TRANSPORT Layer Security (TLS) ellenőrzésre alkalmas proxy eszköz. A proxyt úgy kell konfigurálni, hogy beszúrja az engedélyezett bérlők listáját tartalmazó fejlécet az Azure AD-ba irányuló forgalomba.

3. **Ügyfélszoftver**: a bérlői korlátozások támogatásához az ügyfélszoftvernek közvetlenül az Azure ad-ből kell kérnie a jogkivonatokat, hogy a proxy-infrastruktúra képes legyen a forgalom elfogására. A böngészőalapú Office 365-alkalmazások jelenleg támogatják a bérlői korlátozásokat, mint a modern hitelesítést használó Office-ügyfeleket (például OAuth 2,0).

4. **Modern hitelesítés**: a Cloud servicesnek modern hitelesítést kell használnia a bérlői korlátozások használatához, és le kell tiltania az összes nem engedélyezett bérlő hozzáférését. Az Office 365 Cloud Servicest úgy kell konfigurálni, hogy alapértelmezés szerint modern hitelesítési protokollokat használjanak. A modern hitelesítést támogató Office 365-támogatással kapcsolatos legfrissebb információkért olvassa el a [frissített Office 365 – modern hitelesítés](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)című témakört.

A következő ábra a magas szintű forgalmat mutatja be. A bérlői korlátozások csak az Azure AD-ra irányuló adatforgalomra, az Office 365 Cloud Servicesre nem vonatkoznak. Ez a különbség azért fontos, mert az Azure AD-hitelesítéshez használt forgalom mennyisége általában sokkal alacsonyabb, mint a forgalom mennyisége olyan SaaS-alkalmazásokhoz, mint az Exchange Online és a SharePoint Online.

![Bérlői korlátozások – forgalom folyamatábrája](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

A bérlői korlátozások megkezdésének két lépése van. Először is győződjön meg arról, hogy az ügyfelek csatlakozhatnak a megfelelő címekhez. Másodszor konfigurálja a proxy-infrastruktúrát.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

A bérlői korlátozások használatához az ügyfeleknek képesnek kell lenniük a következő Azure AD URL-címekhez való kapcsolódásra a hitelesítéshez: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)és [login.Windows.net](https://login.windows.net/). Emellett az Office 365 eléréséhez az ügyfeleknek képesnek kell lenniük az [office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)által definiált teljes tartománynevek, URL-címek és IP-címek összekapcsolására is. 

### <a name="proxy-configuration-and-requirements"></a>Proxybeállítások és követelmények

A következő konfiguráció szükséges a bérlői korlátozások a proxy-infrastruktúrán keresztüli engedélyezéséhez. Ez az útmutató általános jellegű, ezért érdemes áttekinteni a proxy gyártójának dokumentációját az egyes megvalósítási lépésekhez.

#### <a name="prerequisites"></a>Előfeltételek

- A proxynak képesnek kell lennie TLS-lehallgatásra, HTTP-fejléc beszúrására és a célhelyek szűrésére a teljes tartománynevek/URL-címek használatával.

- Az ügyfeleknek meg kell bízniuk a proxy által a TLS-kommunikációhoz benyújtott tanúsítványlánc számára. Ha például egy belső [nyilvános kulcsokra épülő infrastruktúra (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) tanúsítványait használja, a belső kiállító főtanúsítvány-hitelesítésszolgáltató tanúsítványának megbízhatónak kell lennie.

- Ez a funkció az Office 365-előfizetések részét képezi, de ha bérlői korlátozásokkal szeretné szabályozni a más SaaS-alkalmazásokhoz való hozzáférést, akkor prémium szintű Azure AD 1 licenc szükséges.

#### <a name="configuration"></a>Konfiguráció

A login.microsoftonline.com, login.microsoft.com és login.windows.net minden bejövő kérelméhez helyezzen be két HTTP-fejlécet: *korlátozza a hozzáférés-bérlők* és a *hozzáférés-kontextus*korlátozását.

A fejléceknek tartalmazniuk kell a következő elemeket:

- A *hozzáférés és a bérlők korlátozásához*használja a értéket \<permitted tenant list\> , amely a bérlők vesszővel tagolt listája, amely számára engedélyezni szeretné a felhasználók számára a hozzáférést. A Bérlővel regisztrált bármely tartomány felhasználható a bérlő azonosítására ezen a listán. Például a contoso és a fabrikam bérlők elérésének engedélyezéséhez a név/érték párok a következőképpen néznek ki: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- A *korlátozás-hozzáférés-kontextushoz*használjon egy egyedi CÍMTÁR-azonosító értékét, amely deklarálja, hogy melyik bérlő állítja be a bérlői korlátozásokat. Ha például a contoso-t a bérlői korlátozási szabályzatot beállító bérlőként szeretné deklarálni, a név/érték párok a következőképpen néznek ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A címtár-azonosítót a [Azure Active Directory-portálon](https://aad.portal.azure.com/)találhatja meg. Jelentkezzen be rendszergazdaként, válassza a **Azure Active Directory**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget.

Annak megakadályozása érdekében, hogy a felhasználók beillesszenak saját HTTP-fejlécet nem jóváhagyott bérlők számára, a proxynak le kell cserélnie a *korlátozás-hozzáférés – bérlői* fejlécet, ha az már szerepel a bejövő kérelemben.

Az ügyfeleknek kényteleneknek kell lenniük arra, hogy a proxyt használják a login.microsoftonline.com, a login.microsoft.com és a login.windows.net vonatkozó összes kéréshez. Ha például a PAC-fájlok használatával irányítja az ügyfeleket a proxy használatára, a végfelhasználók nem tudják szerkeszteni vagy letiltani a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz a végfelhasználók és a rendszergazdák felhasználói élményét ismerteti.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Egy példa felhasználó a contoso hálózaton, de megpróbál hozzáférni egy megosztott SaaS-alkalmazás Fabrikam-példányához, például az Outlook online-hoz. Ha a fabrikam nem engedélyezett bérlő a contoso-példányhoz, a felhasználó egy hozzáférés-megtagadási üzenetet kap, amely azt mondja, hogy olyan erőforráshoz próbál hozzáférni, amely az IT-részleg által nem jóváhagyott szervezethez tartozik.

### <a name="admin-experience"></a>Rendszergazdai felület

Míg a bérlői korlátozások konfigurálása a vállalati proxy infrastruktúráján történik, a rendszergazdák közvetlenül is hozzáférhetnek a bérlői korlátozások jelentéseihez a Azure Portal. A jelentések megtekintése:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/). Megjelenik a **Azure Active Directory felügyeleti központ** irányítópultja.

2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. Megjelenik a Azure Active Directory áttekintés oldal.

3. Az Áttekintés lapon válassza a **bérlői korlátozások**lehetőséget.

A korlátozott hozzáférésű bérlőként megadott bérlő rendszergazdája a jelentés használatával megtekintheti a bérlői korlátozási szabályzat miatt letiltott bejelentkezéseket, beleértve a használt identitást és a cél könyvtár-azonosítót is. A bejelentkezések abban az esetben szerepelnek, ha a bérlőt a korlátozást a felhasználó bérlője vagy az erőforrás-bérlő állítja be a bejelentkezéshez.

> [!NOTE]
> Előfordulhat, hogy a jelentés korlátozott információt tartalmaz, például a célként megadott címtár-azonosítót, ha egy olyan felhasználó, aki nem a korlátozott hozzáférésű bérlőhöz tartozik Ebben az esetben a felhasználó által azonosítható információk, például a név és az egyszerű felhasználónév, a más bérlők felhasználói adatainak védelme érdekében vannak eltakarva.

A Azure Portal többi jelentéséhez hasonlóan szűrőket is használhat a jelentés hatókörének megadásához. Szűrheti egy adott időintervallumot, felhasználót, alkalmazást, ügyfelet vagy állapotot. Ha az **oszlopok** gombot választja, a következő mezők tetszőleges kombinációjával választhat:

- **Felhasználó**
- **Alkalmazás**
- **Állapot**
- **Date**
- **Dátum (UTC)** (az UTC egyezményes világidő)
- **MFA hitelesítési módszer** (többtényezős hitelesítési módszer)
- **MFA** -hitelesítés részletei (többtényezős hitelesítés részletei)
- **MFA-eredmény**
- **IP-cím**
- **Ügyfél**
- **Felhasználónév**
- **Hely**
- **Cél bérlő azonosítója**

## <a name="office-365-support"></a>Office 365-támogatás

Az Office 365-alkalmazásoknak két feltételnek kell megfelelniük a bérlői korlátozások teljes körű támogatásához:

1. A használt ügyfél támogatja a modern hitelesítést.
2. A modern hitelesítés engedélyezve van a felhőalapú szolgáltatás alapértelmezett hitelesítési protokollja.

Tekintse meg a [frissített office 365 modern hitelesítést](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) a legújabb információkra, amelyekkel az Office-ügyfelek jelenleg támogatják a modern hitelesítést. Az oldal hivatkozásokat is tartalmaz, amelyekkel a modern hitelesítés engedélyezhető az adott Exchange Online és a Skype vállalati online bérlők számára. A SharePoint Online alapértelmezés szerint már lehetővé teszi a modern hitelesítés használatát.

Az Office 365 böngésző-alapú alkalmazások (az Office-portál, a Yammer, a SharePoint-webhelyek, az Outlook a weben és egyebek) jelenleg is támogatja a bérlői korlátozásokat. A vastag ügyfelek (az Outlook, a Skype vállalati verzió, a Word, az Excel, a PowerPoint stb.) csak a modern hitelesítés használata esetén tudják kényszeríteni a bérlői korlátozásokat.  

A modern hitelesítést támogató Outlook és Skype vállalati ügyfelek továbbra is használhatnak örökölt protokollokat a bérlők esetében, ahol nincs engedélyezve a modern hitelesítés, ami gyakorlatilag megkerüli a bérlői korlátozásokat. A bérlői korlátozások az örökölt protokollokat használó alkalmazásokat letilthatják, ha a hitelesítés során kapcsolatba kerülnek a login.microsoftonline.com, a login.microsoft.com vagy a login.windows.net.

A Windowsban az Outlook esetében az ügyfelek dönthetnek úgy, hogy korlátozásokat vezetnek be a végfelhasználók számára a nem jóváhagyott levelezési fiókok profilokhoz való hozzáadásának megakadályozása érdekében. Tekintse meg például a [nem alapértelmezett Exchange-fiókok hozzáadásának tiltása](https://gpsearch.azurewebsites.net/default.aspx?ref=1) csoportházirend-beállítást.

## <a name="testing"></a>Tesztelés

Ha a teljes szervezete előtt szeretné kipróbálni a bérlői korlátozásokat, két lehetőség közül választhat: egy gazdagépen alapuló megközelítés, például a Hegedűs használata, vagy a proxybeállítások fokozatos bevezetése.

### <a name="fiddler-for-a-host-based-approach"></a>Hegedűs a gazdagép-alapú megközelítéshez

A Hegedűs egy ingyenes webes hibakeresési proxy, amely a HTTP/HTTPS-forgalom rögzítésére és módosítására használható, beleértve a HTTP-fejlécek beszúrását is. A Hegedűs a bérlői korlátozások teszteléséhez való konfigurálásához hajtsa végre a következő lépéseket:

1. [A Hegedűs letöltése és telepítése](https://www.telerik.com/fiddler).

2. Állítsa be a hegedűst a HTTPS-forgalom visszafejtéséhez a [Hegedűs súgójának dokumentációjában](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. A Hegedűs konfigurálása a *korlátozás-hozzáférés – bérlők* és a *hozzáférés-kontextusú* fejlécek egyéni szabályokkal történő beszúrásához:

   1. A Hegedűs webes hibakereső eszközében válassza a **szabályok** menüt, és válassza a **szabályok testreszabása...** lehetőséget. a CustomRules-fájl megnyitásához.

   2. Adja hozzá a következő sorokat a függvény elejéhez `OnBeforeRequest` . Cserélje le a \<tenant domain\> -t a Bérlővel regisztrált tartományra (például: `contoso.onmicrosoft.com` ). Cserélje le a helyére a \<directory ID\> bérlő Azure ad GUID azonosítóját.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Ha több bérlőt is engedélyeznie kell, a vesszőt használva válassza el a bérlők nevét. Például:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárjuk be a CustomRules fájlt.

A Hegedűs konfigurálása után a forgalmat a **fájl** menüre, majd a **forgalom rögzítése**lehetőségre kattintva rögzítheti.

### <a name="staged-rollout-of-proxy-settings"></a>Proxybeállítások szakaszos bevezetése

A proxy-infrastruktúra képességeitől függően lehetősége lehet a beállítások bevezetését a felhasználók számára. Íme néhány magas szintű lehetőség a megfontoláshoz:

1. A PAC-fájlok segítségével tesztelheti a felhasználókat egy teszt proxy-infrastruktúrára, míg a normál felhasználók továbbra is használják az üzemi proxy infrastruktúráját.
2. Egyes proxykiszolgálók a csoportok használatával különböző konfigurációkat is támogatnak.

További részletekért tekintse meg a proxykiszolgáló dokumentációját.

## <a name="next-steps"></a>További lépések

- További információ a [frissített Office 365 modern hitelesítésről](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Az [Office 365 URL-címeinek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) áttekintése
