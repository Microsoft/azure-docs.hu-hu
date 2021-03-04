---
title: A bérlői korlátozások használata az SaaS-alkalmazásokhoz való hozzáférés kezelésére – Azure AD
description: A bérlői korlátozások használata annak kezeléséhez, hogy mely felhasználók férhetnek hozzá az alkalmazásokhoz az Azure AD-bérlőn alapuló módon.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 2/23/2021
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdcdd387575faec87656430860e24fee56387775
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050902"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>A bérlői korlátozások használata a SaaS-Felhőbeli alkalmazásokhoz való hozzáférés kezelésére

Nagyvállalatok, amelyek hangsúlyozzák, hogy a biztonság szeretne áttérni a Felhőbeli szolgáltatásokra, például a Microsoft 365re, de tudnia kell, hogy a felhasználók csak a jóváhagyott erőforrásokhoz férhetnek hozzá. A vállalatok hagyományosan korlátozzák a tartományneveket vagy az IP-címeket, amikor a hozzáférést szeretnék kezelni. Ez a megközelítés egy olyan világban meghiúsul, ahol a szolgáltatott szoftverek (vagy SaaS-alkalmazások) nyilvános felhőben futnak, és megosztott tartományneveket futtatnak, például a [Outlook.Office.com](https://outlook.office.com/) és a [login.microsoftonline.com](https://login.microsoftonline.com/). Ezeknek a címeknek a blokkolása révén a felhasználók teljes mértékben hozzáférhetnek az Outlookhoz a világhálón ahelyett, hogy csak a jóváhagyott identitásokra és erőforrásokra korlátozzák őket.

A kihíváshoz tartozó Azure Active Directory (Azure AD) megoldás a bérlői korlátozások nevű szolgáltatás. A bérlői korlátozásokkal a szervezetek az Azure AD-bérlő alapján vezérelhetik a SaaS-Felhőbeli alkalmazásokhoz való hozzáférést az egyszeri bejelentkezéshez használt alkalmazások esetében. Előfordulhat például, hogy engedélyezni szeretné a szervezet Microsoft 365 alkalmazásaihoz való hozzáférést, és megakadályozza, hogy az azonos alkalmazások más szervezetek példányaihoz hozzáférjenek.  

A bérlői korlátozásokkal a szervezetek meghatározhatják azon bérlők listáját, amelyekhez a felhasználók hozzáférhetnek. Az Azure AD ezt követően csak az engedélyezett bérlők számára biztosít hozzáférést.

Ez a cikk a Microsoft 365 bérlői korlátozásait ismerteti, de a szolgáltatás védi az összes olyan alkalmazást, amely a felhasználót az Azure AD számára egyszeri bejelentkezésre küldi. Ha SaaS-alkalmazásokat használ a Microsoft 365 által használt bérlőtől eltérő Azure AD-Bérlővel, győződjön meg arról, hogy minden szükséges bérlő engedélyezett (például B2B együttműködési forgatókönyvekben). A SaaS Cloud apps szolgáltatással kapcsolatos további információkért tekintse meg a [Active Directory piactéren](https://azuremarketplace.microsoft.com/marketplace/apps).

Emellett a bérlői korlátozások funkció mostantól támogatja az [összes Microsoft fogyasztói alkalmazás](#blocking-consumer-applications-public-preview) (MSA-alkalmazás) használatát, mint például a OneDrive, a Hotmail és a Xbox.com.  Ez egy külön fejlécet használ a `login.live.com` végponthoz, és részletesen a dokumentum végén található.

## <a name="how-it-works"></a>Működés

A teljes megoldás a következő összetevőket tartalmazza:

1. **Azure ad**: Ha a `Restrict-Access-To-Tenants: <permitted tenant list>` fejléc jelen van, az Azure ad csak az engedélyezett bérlők biztonsági jogkivonatait bocsátja ki.

2. Helyszíni **proxykiszolgáló-infrastruktúra**: ez az infrastruktúra TRANSPORT Layer Security (TLS) ellenőrzésre alkalmas proxy eszköz. A proxyt úgy kell konfigurálni, hogy beszúrja az engedélyezett bérlők listáját tartalmazó fejlécet az Azure AD-ba irányuló forgalomba.

3. **Ügyfélszoftver**: a bérlői korlátozások támogatásához az ügyfélszoftvernek közvetlenül az Azure ad-ből kell kérnie a jogkivonatokat, hogy a proxy-infrastruktúra képes legyen a forgalom elfogására. A böngészőalapú Microsoft 365 alkalmazások jelenleg támogatják a bérlői korlátozásokat, mint a modern hitelesítést használó Office-ügyfeleket (például OAuth 2,0).

4. **Modern hitelesítés**: a Cloud servicesnek modern hitelesítést kell használnia a bérlői korlátozások használatához, és le kell tiltania az összes nem engedélyezett bérlő hozzáférését. Az Microsoft 365 Cloud Servicest úgy kell konfigurálni, hogy alapértelmezés szerint modern hitelesítési protokollokat használjanak. A modern hitelesítés Microsoft 365 támogatásával kapcsolatos legfrissebb információkért olvassa el a [frissített Office 365 modern hitelesítés](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)című témakört.

A következő ábra a magas szintű forgalmat mutatja be. A bérlői korlátozások csak az Azure AD-ra irányuló adatforgalomra vonatkozó TLS-vizsgálatot igényelnek, a Microsoft 365 Cloud Services esetében nem. Ez a különbség azért fontos, mert az Azure AD-hitelesítéshez használt forgalom mennyisége általában sokkal alacsonyabb, mint a forgalom mennyisége olyan SaaS-alkalmazásokhoz, mint az Exchange Online és a SharePoint Online.

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

- A bérlői korlátozások használatához prémium szintű Azure AD 1 licenc szükséges.

#### <a name="configuration"></a>Konfiguráció

A login.microsoftonline.com, a login.microsoft.com és a login.windows.net kimenő minden kérelemnél helyezzen be két HTTP-fejlécet: *korlátozza a hozzáférés-bérlők és a* *hozzáférés-kontextus korlátozását*.

> [!NOTE]
> Ne tartalmazzon altartományokat a `*.login.microsoftonline.com` proxy konfigurációjában. Ez magában foglalja a device.login.microsoftonline.com, és beavatkozik az ügyféltanúsítvány-alapú hitelesítéssel, amely az eszköz regisztrálása és az eszközökön alapuló feltételes hozzáférési forgatókönyvek esetében használatos. Konfigurálja úgy a proxykiszolgálót, hogy kizárja a TLS-device.login.microsoftonline.com és-ellenőrzésből származó és a fejléc-befecskendezést.

A fejléceknek tartalmazniuk kell a következő elemeket:

- A *hozzáférés és a bérlők korlátozásához* használja a értéket \<permitted tenant list\> , amely a bérlők vesszővel tagolt listája, amely számára engedélyezni szeretné a felhasználók számára a hozzáférést. A Bérlővel regisztrált bármely tartomány felhasználható a bérlő azonosítására ezen a listán, valamint magát a címtár-azonosítót is. A bérlők leírásának mindhárom módjára, a contoso, a fabrikam és a Microsoft számára a következőhöz hasonló név/érték párokat kell kinéznie: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- A *korlátozás-hozzáférés-kontextushoz* használjon egy egyedi CÍMTÁR-azonosító értékét, amely deklarálja, hogy melyik bérlő állítja be a bérlői korlátozásokat. Ha például a contoso-t a bérlői korlátozási szabályzatot beállító bérlőként szeretné deklarálni, a név/érték párok a következőképpen néznek ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Ebben a helyen a saját címtár-AZONOSÍTÓját **kell** használnia, hogy naplókat kapjon a hitelesítésekhez.

> [!TIP]
> A címtár-azonosítót a [Azure Active Directory-portálon](https://aad.portal.azure.com/)találhatja meg. Jelentkezzen be rendszergazdaként, válassza a **Azure Active Directory** lehetőséget, majd válassza a **Tulajdonságok** lehetőséget. 
>
> Annak ellenőrzéséhez, hogy egy címtár-azonosító vagy tartománynév ugyanarra a bérlőre hivatkozik-e, használja ezt az azonosítót vagy tartományt az <tenant> alábbi URL-cím helyett: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Ha az eredmények a tartománnyal és az AZONOSÍTÓval azonosak, ugyanarra a bérlőre hivatkoznak. 

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

3. Az Áttekintés lapon válassza a **bérlői korlátozások** lehetőséget.

A korlátozott hozzáférésű bérlőként megadott bérlő rendszergazdája a jelentés használatával megtekintheti a bérlői korlátozási szabályzat miatt letiltott bejelentkezéseket, beleértve a használt identitást és a cél könyvtár-azonosítót is. A bejelentkezések abban az esetben szerepelnek, ha a bérlőt a korlátozást a felhasználó bérlője vagy az erőforrás-bérlő állítja be a bejelentkezéshez.

Előfordulhat, hogy a jelentés korlátozott információt tartalmaz, például a célként megadott címtár-azonosítót, ha egy olyan felhasználó, aki nem a korlátozott hozzáférésű bérlőhöz tartozik Ebben az esetben a felhasználó által azonosítható információk, például a név és az egyszerű felhasználónév, a más bérlők felhasználói adatainak védelme érdekében ("{személyes eltávolítás} @domain.com " vagy a 00000000-0000-0000-0000-000000000000 a megfelelő Felhasználónév és objektumazonosítók helyett) vannak eltakarva. 

A Azure Portal többi jelentéséhez hasonlóan szűrőket is használhat a jelentés hatókörének megadásához. Szűrheti egy adott időintervallumot, felhasználót, alkalmazást, ügyfelet vagy állapotot. Ha az **oszlopok** gombot választja, a következő mezők tetszőleges kombinációjával választhat:

- **Felhasználó** – ebben a mezőben azonosíthatók a személyazonosításra alkalmas adatok, amelyek a következőre lesznek beállítva: `00000000-0000-0000-0000-000000000000` . 
- **Alkalmazás**
- **Állapot**
- **Date**
- **Dátum (UTC)** – az UTC egyezményes világidő
- **IP-cím**
- **Ügyfél**
- **Felhasználónév** – ebben a mezőben a személyazonosításra alkalmas adatok el lesznek távolítva, ahol a rendszer a következőre lesz beállítva: `{PII Removed}@domain.com`
- **Hely**
- **Cél bérlő azonosítója**

## <a name="microsoft-365-support"></a>Microsoft 365-támogatás

Microsoft 365 alkalmazásoknak két feltételnek kell megfelelniük a bérlői korlátozások teljes körű támogatásához:

1. A használt ügyfél támogatja a modern hitelesítést.
2. A modern hitelesítés engedélyezve van a felhőalapú szolgáltatás alapértelmezett hitelesítési protokollja.

Tekintse meg a [frissített office 365 modern hitelesítést](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) a legújabb információkra, amelyekkel az Office-ügyfelek jelenleg támogatják a modern hitelesítést. Az oldal hivatkozásokat is tartalmaz, amelyekkel a modern hitelesítés engedélyezhető az adott Exchange Online és a Skype vállalati online bérlők számára. A SharePoint Online alapértelmezés szerint már lehetővé teszi a modern hitelesítés használatát.

Microsoft 365 böngészőalapú alkalmazások (az Office-portál, a Yammer, a SharePoint-webhelyek, az Outlook a weben és még sok más) jelenleg is támogatja a bérlői korlátozásokat. A vastag ügyfelek (az Outlook, a Skype vállalati verzió, a Word, az Excel, a PowerPoint stb.) csak a modern hitelesítés használata esetén tudják kényszeríteni a bérlői korlátozásokat.  

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

   2. Adja hozzá a következő sorokat a függvény elejéhez `OnBeforeRequest` . Cserélje le a \<List of tenant identifiers\> -t a Bérlővel regisztrált tartományra (például: `contoso.onmicrosoft.com` ). Cserélje le a helyére a \<directory ID\> bérlő Azure ad GUID azonosítóját.  Meg **kell** adnia a helyes GUID azonosítót, hogy a naplók megjelenjenek a bérlőben. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

Ha több bérlőt is engedélyeznie kell, a vesszőt használva válassza el a bérlők nevét. Például:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárjuk be a CustomRules fájlt.

A Hegedűs konfigurálása után a forgalmat a **fájl** menüre, majd a **forgalom rögzítése** lehetőségre kattintva rögzítheti.

### <a name="staged-rollout-of-proxy-settings"></a>Proxybeállítások szakaszos bevezetése

A proxy-infrastruktúra képességeitől függően lehetősége lehet a beállítások bevezetését a felhasználók számára. Íme néhány magas szintű lehetőség a megfontoláshoz:

1. A PAC-fájlok segítségével tesztelheti a felhasználókat egy teszt proxy-infrastruktúrára, míg a normál felhasználók továbbra is használják az üzemi proxy infrastruktúráját.
2. Egyes proxykiszolgálók a csoportok használatával különböző konfigurációkat is támogatnak.

További részletekért tekintse meg a proxykiszolgáló dokumentációját.

## <a name="blocking-consumer-applications-public-preview"></a>Fogyasztói alkalmazások blokkolása (nyilvános előzetes verzió)

A Microsoft azon alkalmazásai, amelyek a felhasználói fiókokat és a szervezeti fiókokat, például a [OneDrive](https://onedrive.live.com/) vagy a [Microsoft Learn](https://docs.microsoft.com/learn/)is támogatják, esetenként ugyanazon az URL-címen futhatnak.  Ez azt jelenti, hogy azok a felhasználók, akiknek ehhez az URL-címhez hozzá kell férniük, személyes használatra is hozzá kell férniük, ami nem engedélyezett a működési irányelvekben.

Egyes szervezetek megpróbálják kijavítani ezt a blokkolással, `login.live.com` hogy letiltsák a személyes fiókok hitelesítését.  Ennek számos hátránya van:

1. A blokkoló `login.live.com` blokkolja a személyes fiókok használatát a B2B vendég forgatókönyvekben, ami behatolhat a látogatókra és az együttműködésre.
1. [Az Autopilot használatához `login.live.com` a](https://docs.microsoft.com/mem/autopilot/networking-requirements) a telepítéshez. Az Intune és az Autopilot forgatókönyvek sikertelenek lehetnek `login.live.com` , ha a blokkolva van.
1. A login.live.com szolgáltatásban az telemetria támaszkodó szervezeti és Windows-frissítések nem [fognak működni](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>A fogyasztói alkalmazások konfigurációja

Míg a `Restrict-Access-To-Tenants` fejléc engedélyezési listaként funkcionál, a Microsoft-fiók (MSA) blokk megtagadási jelként működik, és közli, hogy a Microsoft-fiók platform nem teszi lehetővé a felhasználók számára, hogy bejelentkezzenek a fogyasztói alkalmazásokba. A jel elküldéséhez a `sec-Restrict-Tenant-Access-Policy` fejlécet `login.live.com` a [fentiekkel](#proxy-configuration-and-requirements)megegyező vállalati proxy vagy tűzfal használatával kell beadni a forgalomnak. A fejléc értékének a számnak kell lennie `restrict-msa` . Ha a fejléc jelen van, és egy fogyasztói alkalmazás közvetlenül próbál bejelentkezni egy felhasználóba, a bejelentkezés le lesz tiltva.

Ekkor a felhasználói alkalmazások hitelesítése nem jelenik meg a [felügyeleti naplókban](#admin-experience), mivel a login.Live.com az Azure ad-től függetlenül fut.

### <a name="what-the-header-does-and-does-not-block"></a>A fejléc funkciója és letiltása

A `restrict-msa` szabályzat tiltja a fogyasztói alkalmazások használatát, de több más típusú forgalom és hitelesítés révén is lehetővé teszi a következőket:

1. Az eszközök felhasználó nélküli forgalma.  Ez magában foglalja az Autopilot, a Windows Update és a szervezeti telemetria adatforgalmát.
1. A fogyasztói fiókok B2B-hitelesítése. Azok a Microsoft-fiókkal rendelkező felhasználók, akik a [Bérlővel való együttműködésre kérték](https://docs.microsoft.com/azure/active-directory/external-identities/redemption-experience#invitation-redemption-flow) a login.Live.com az erőforrás-bérlő eléréséhez.
    1. Ezt a hozzáférést a fejléc használatával lehet `Restrict-Access-To-Tenants` engedélyezni vagy megtagadni az adott erőforrás-bérlőhöz való hozzáférés engedélyezéséhez vagy megtagadásához.
1. Az "áteresztő" hitelesítés, amelyet számos Azure-alkalmazás használ, valamint a Office.com, ahol az alkalmazások az Azure AD használatával jelentkeznek be a fogyasztói felhasználók felhasználói környezetben.
    1. Ezt a hozzáférést a fejléc használatával is szabályozhatja `Restrict-Access-To-Tenants` , hogy engedélyezze vagy megtagadja a hozzáférést a speciális "továbbító" bérlőhöz ( `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ).  Ha ez a bérlő nem jelenik meg az `Restrict-Access-To-Tenants` engedélyezett tartományok listájában, az Azure ad letiltja a fogyasztói fiókokat az alkalmazásba való bejelentkezéskor.

## <a name="next-steps"></a>Következő lépések

- További információ a [frissített Office 365 modern hitelesítésről](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Az [Office 365 URL-címeinek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) áttekintése
