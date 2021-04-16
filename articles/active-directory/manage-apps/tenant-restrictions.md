---
title: Bérlői korlátozások használata az SaaS-alkalmazásokhoz való hozzáférés kezeléséhez – Azure AD
description: Bérlői korlátozások használata annak kezelésére, hogy mely felhasználók férhetnek hozzá az alkalmazásokhoz az Azure AD-bérlőjük alapján.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376579"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Bérlői korlátozások használata az SaaS-felhőalkalmazások hozzáférésének kezeléséhez

A biztonságot hangsúlyozó nagyvállalatok szeretnék áthelyezni az olyan felhőszolgáltatásokat, mint Microsoft 365, de fontos tudni, hogy a felhasználók csak a jóváhagyott erőforrásokhoz férhetnek hozzá. A vállalatok hagyományosan korlátozzák a tartományneveket vagy IP-címeket, amikor a hozzáférést szeretnék kezelni. Ez a megközelítés olyan világban meghiúsul, ahol a szolgáltatott szoftvereket (vagy SaaS-alkalmazásokat) nyilvános felhőben üzemeltetik, és olyan megosztott tartományneveken futnak, mint a outlook.office.com [vagy](https://outlook.office.com/) [login.microsoftonline.com.](https://login.microsoftonline.com/) Ezeknek a címeknek a blokkolásával a felhasználók nem férhetnek hozzá teljes mértékben az Outlookhoz a weben ahelyett, hogy csak a jóváhagyott identitások és erőforrások korlátozására korlátozná őket.

A Azure Active Directory (Azure AD) megoldás erre a feladatra a bérlői korlátozások nevű funkció. A bérlői korlátozásokkal a szervezetek az egyszeri bejelentkezéshez használt Azure AD-bérlő alapján szabályozni lehet az SaaS-felhőalkalmazások hozzáférését. Előfordulhat például, hogy engedélyezni szeretné a hozzáférést a szervezet Microsoft 365- és alkalmazásainak, ugyanakkor meg szeretné akadályozni, hogy más szervezetek példányai is hozzáférjenek ugyanannak az alkalmazásnak a példányaihoz.  

Bérlői korlátozásokkal a szervezetek megadhatják azon bérlők listáját, amelyekhez a felhasználók hozzáférhetnek. Az Azure AD ezután csak ezekhez az engedélyezett bérlőkhöz biztosít hozzáférést.

Ez a cikk a felhasználók bérlői korlátozásaival Microsoft 365, de ez a funkció minden olyan alkalmazás számára védelmet nyújt, amely egyszeri bejelentkezésre elküldi a felhasználót az Azure AD-be. Ha az SaaS-alkalmazásokat a Microsoft 365 által használt bérlőtől eltérő Azure AD-bérlővel használja, győződjön meg arról, hogy az összes szükséges bérlő engedélyezett (például B2B együttműködési forgatókönyvek esetén). További információ a SaaS-felhőalkalmazásokkal kapcsolatban: Active Directory [Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps)

Emellett a bérlőkorlátozási funkció mostantól támogatja az összes [Fogyasztói Microsoft-alkalmazás](#blocking-consumer-applications-public-preview) (MSA-alkalmazás), például a OneDrive, a Hotmail és a Xbox.com.  Ez egy külön fejlécet használ a végponthoz, és a dokumentum végén található `login.live.com` részletes leírás.

## <a name="how-it-works"></a>Működés

A teljes megoldás a következő összetevőkből áll:

1. **Azure AD:** Ha a fejléc jelen van, az Azure AD csak az engedélyezett bérlők számára ad ki `Restrict-Access-To-Tenants: <permitted tenant list>` biztonsági jogkivonatokat.

2. **Helyszíni proxykiszolgáló-infrastruktúra:** Ez az infrastruktúra egy olyan proxyeszköz, amely képes Transport Layer Security (TLS) vizsgálatra. A proxyt úgy kell konfigurálnia, hogy beszúrja az engedélyezett bérlők listáját tartalmazó fejlécet az Azure AD-be szánt forgalomba.

3. **Ügyfélszoftver:** A bérlői korlátozások támogatásához az ügyfélszoftvernek közvetlenül az Azure AD-től kell jogkivonatokat kérnie, hogy a proxyinfra infrastruktúra elfogja a forgalmat. A böngészőalapú Microsoft 365 alkalmazások jelenleg támogatják a bérlői korlátozásokat, ahogy a modern hitelesítést (például az OAuth 2.0-t) felhasználó Office-ügyfelek is.

4. **Modern hitelesítés:** A felhőszolgáltatásoknak modern hitelesítést kell használniuk a bérlők korlátozásai és a nem engedélyezett bérlők hozzáférésének blokkolása érdekében. A felhőszolgáltatásokat Microsoft 365 konfigurálnia, hogy alapértelmezés szerint modern hitelesítési protokollokat használjanak. A modern hitelesítés Microsoft 365 legfrissebb információkért olvassa el a frissített [Office 365 modern hitelesítést.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

Az alábbi ábrán a magas szintű forgalom látható. A bérlői korlátozások csak az Azure AD-be való forgalomra vonatkozó TLS-ellenőrzést igénylik, a felhőszolgáltatások Microsoft 365 nem. Ez a különbség azért fontos, mert az Azure AD-hez való hitelesítés adatforgalma általában sokkal alacsonyabb, mint az Olyan SaaS-alkalmazások forgalmának mennyisége, mint az Exchange Online és a SharePoint Online.

![Bérlői korlátozások forgalomfolyama – ábra](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

A bérlőkorlátozások használatának első lépései két lépésből állnak. Először győződjön meg arról, hogy az ügyfelek a megfelelő címekhez tudnak csatlakozni. Másodszor konfigurálja a proxy-infrastruktúrát.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

A bérlői korlátozások alkalmazáshoz az ügyfeleknek csatlakozniuk kell a következő Azure AD URL-címekhez a hitelesítéshez: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), és [login.windows.net.](https://login.windows.net/) Emellett az Office 365 eléréséhez az ügyfeleknek csatlakozniuk kell az [Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)URL-címeiben és IP-címtartományaiban meghatározott teljes tartománynevekhez (FQDN-hez), URL-címekhez és IP-címekhez is. 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguráció és követelmények

Az alábbi konfiguráció szükséges a bérlői korlátozások proxyinfra infrastruktúrán keresztüli engedélyezéséhez. Ez az útmutató általános, ezért a konkrét megvalósítási lépésekért tekintse meg a proxy gyártójának dokumentációját.

#### <a name="prerequisites"></a>Előfeltételek

- A proxynak képesnek kell lennie TLS-elfogásra, HTTP-fejlécek beszúrására és célhely szűrésére FQDN-ek/URL-címek használatával.

- Az ügyfeleknek meg kell bízniuk a proxy által a TLS-kommunikációhoz bemutatott tanúsítványláncban. Ha például egy belső nyilvános kulcsú infrastruktúrából [(PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) származó tanúsítványokat használ, a belső kiállító főtanúsítvány megbízhatónak kell lennie.

- prémium szintű Azure AD bérlői korlátozások alkalmazásához 1 licenc szükséges.

#### <a name="configuration"></a>Konfiguráció

A bérlőkhöz login.microsoftonline.com, login.microsoft.com és login.windows.net minden kimenő kérelemhez szúrjon be két *HTTP-fejlécet: Restrict-Access-to-Tenants* (Bérlők hozzáférésének korlátozása) és *Restrict-Access-Context*(Hozzáférés korlátozása) környezetet.

> [!NOTE]
> A proxykonfigurációba ne foglalja `*.login.microsoftonline.com` bele az altartományokat. Ez hatással lesz a device.login.microsoftonline.com és zavarhatja az ügyfél-tanúsítvány hitelesítését, amely az eszközregisztrációs és eszközalapú feltételes hozzáférési forgatókönyvekben használatos. Konfigurálja úgy a proxykiszolgálót, hogy device.login.microsoftonline.com ki a TLS-törés- és vizsgálat- és fejlécinjektálásból.

A fejlécek a következő elemeket tartalmazzák:

- A *Restrict-Access-To-Tenants*(Bérlők hozzáférésének korlátozása) beállításnál használja a értéket, amely azon bérlők vesszővel elválasztott listája, amelyekhez hozzáférést szeretne \<permitted tenant list\> engedélyezni a felhasználóknak. A bérlővel regisztrált bármely tartomány használható a bérlő és maga a címtár-azonosító azonosítására ebben a listában. A bérlő leírásának mindhárom módját bemutató példa a Contoso, a Fabrikam és a Microsoft számára lehetővé tetsző név/érték pár, amely így néz ki: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- A *Restrict-Access-Context*(Hozzáférés korlátozása) környezetben egyetlen címtár-azonosító értékét használja, és deklaratív, hogy melyik bérlő korlátozza a bérlőt. Ha például a Contoso bérlőként szeretné deklarálva a bérlőkorlátozási szabályzatot, a név/érték pár a következő lesz: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Az **ilyen hitelesítések** naplóihoz saját címtár-azonosítót kell használnia.

> [!TIP]
> A címtár-azonosítót a Azure Active Directory [találja.](https://aad.portal.azure.com/) Jelentkezzen be rendszergazdaként, **válassza** a Azure Active Directory, majd a **Tulajdonságok lehetőséget.** 
>
> Annak ellenőrzéséhez, hogy egy címtár-azonosító vagy tartománynév ugyanannak a bérlőnek a nevére hivatkozik-e, használja ezt az azonosítót vagy tartományt az <tenant> URL-cím helyén: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Ha a tartományra és az azonosítóra vonatkozó eredmények azonosak, akkor ugyanannak a bérlőnek a neve. 

Annak érdekében, hogy a felhasználók ne szúrják be a saját HTTP-fejlécüket nem jóváhagyott bérlőkbe, a proxynak le kell cserélnie a *Restrict-Access-To-Tenants* fejlécet, ha az már jelen van a bejövő kérésben.

Az ügyfeleket arra kell kényszeríteni, hogy a proxyt használják az összes login.microsoftonline.com, login.microsoft.com és login.windows.net. Ha például PAC-fájlokkal irányítják az ügyfeleket a proxy használatára, a végfelhasználók nem szerkeszthetik vagy tilthatják le a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz a végfelhasználók és a rendszergazdák élményét ismerteti.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Egy példafelhasználó a Contoso hálózatán van, de egy megosztott SaaS-alkalmazás, például az Outlook Online Fabrikam-példányát próbálja elérni. Ha a Fabrikam a Contoso-példány nem engedélyezett bérlője, a felhasználó egy hozzáférés-megtagadó üzenetet kap, amely szerint Ön olyan erőforráshoz próbál hozzáférni, amely az IT-részleg által nem jóváhagyott szervezethez tartozik.

![Bérlői korlátozások hibaüzenete, 2021. április](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Rendszergazdai élmény

Bár a bérlői korlátozások konfigurálása a vállalati proxyinfra infrastruktúrán történik, a rendszergazdák közvetlenül hozzáférhetnek a bérlőkorlátozási jelentésekhez a Azure Portal alatt. A jelentések megtekintése:

1. Jelentkezzen be a Azure Active Directory [portálra.](https://aad.portal.azure.com/) Megjelenik **Azure Active Directory Felügyeleti központ irányítópultja.**

2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. Megjelenik Azure Active Directory áttekintési lapja.

3. Az Áttekintés lapon válassza a **Bérlői korlátozások lehetőséget.**

A korlátozott hozzáférésű környezet bérlőjeként megadott bérlő rendszergazdája ezzel a jelentéssel láthatja, hogy a bejelentkezések blokkolva vannak a bérlőkorlátozási szabályzat miatt, beleértve a használt identitást és a célcímtár-azonosítót. A bejelentkezéseket akkor is tartalmazza a rendszer, ha a bérlői beállítás a felhasználói bérlő vagy az erőforrás-bérlő a bejelentkezéshez.

A jelentés korlátozott információkat tartalmazhat, például a célcímtár-azonosítót, ha egy olyan felhasználó, aki nem a Korlátozott hozzáférésű-környezet bérlőben van, bejelentkezik. Ebben az esetben a felhasználóazonosításra alkalmas adatok, például a név és az egyszerű felhasználónév maszkolása a más bérlőkben található felhasználói adatok védelme érdekében ("{PII Removed} " vagy @domain.com 00000000-0000-0000-00000000000000 a megfelelő felhasználónevek és objektum-azonosítók helyett). 

A jelentés hatókörét a Azure Portal jelentésekhez hasonló szűrőkkel adhatja meg. Szűrhet egy adott időintervallumra, felhasználóra, alkalmazásra, ügyfélre vagy állapotra. Ha az Oszlopok **gombot választja,** az alábbi mezők bármilyen kombinációjával megjeleníthet adatokat:

- **Felhasználó** – ebben a mezőben személyes adatokat törölhet, ahol az lesz `00000000-0000-0000-0000-000000000000` beállítva. 
- **Alkalmazás**
- **Állapot**
- **Date**
- **Dátum (UTC)** – az UTC szerinti egyezményes világidő
- **IP-cím**
- **Ügyfél**
- **Felhasználónév** – ebben a mezőben személyes adatokat törölhet, ahol az lesz beállítva `{PII Removed}@domain.com`
- **Hely**
- **Célbérlő azonosítója**

## <a name="microsoft-365-support"></a>Microsoft 365-támogatás

Microsoft 365 alkalmazásoknak két feltételnek kell megfelelniük a bérlőkorlátozások teljes körű támogatásához:

1. A használt ügyfél támogatja a modern hitelesítést.
2. A modern hitelesítés a felhőszolgáltatás alapértelmezett hitelesítési protokollja.

Az Office-ügyfelek által jelenleg a modern hitelesítést támogató legfrissebb információkért tekintse meg a frissített [Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) modern hitelesítést. Ez az oldal a modern hitelesítés adott Exchange Online- és Skype Vállalati online verzió-bérlőkben való engedélyezésére vonatkozó utasításokra mutató hivatkozásokat is tartalmaz. A SharePoint Online alapértelmezés szerint már engedélyezi a modern hitelesítést.

Microsoft 365 böngészőalapú alkalmazások (az Office Portál, a Yammer, SharePoint-webhelyek, webes Outlook stb.) jelenleg támogatják a bérlői korlátozásokat. A vastag ügyfelek (Outlook, Skype Vállalati verzió, Word, Excel, PowerPoint stb.) csak modern hitelesítés esetén kényszerítik a bérlők korlátozásait.  

A modern hitelesítést támogató Outlook- és Skype Vállalati verzió-ügyfelek továbbra is használhatnak örökölt protokollokat olyan bérlők esetében, amelyeken nincs engedélyezve a modern hitelesítés, így megkerülhetik a bérlői korlátozásokat. A bérlői korlátozások letilthatnak olyan alkalmazásokat, amelyek régi protokollokat használnak, ha login.microsoftonline.com, login.microsoft.com vagy login.windows.net a hitelesítés során.

A Windows Outlook esetében az ügyfelek dönthetnek úgy, hogy olyan korlátozásokat vezetnek be, amelyek megakadályozzák, hogy a végfelhasználók nem jóváhagyott e-mail fiókokat adjanak hozzá a profiljukhoz. Lásd például a Nem alapértelmezett [Exchange-fiókok](https://gpsearch.azurewebsites.net/default.aspx?ref=1) hozzáadásának megakadályozása csoportházirend-beállítást.

## <a name="testing"></a>Tesztelés

Ha ki szeretné próbálni a bérlői korlátozásokat, mielőtt a teljes szervezet számára elérhető lenne, két lehetőség közül választhat: gazdagépalapú megközelítéssel, amely a Fiddlert vagy a proxybeállítások szakaszos használatát használja.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler gazdagépalapú megközelítéshez

A Fiddler egy ingyenes webes hibakeresési proxy, amely a HTTP/HTTPS-forgalom rögzítésére és módosítására használható, beleértve a HTTP-fejlécek beszúrást is. Ha a Fiddlert a bérlőkorlátozások tesztelésére konfigurálja, hajtsa végre a következő lépéseket:

1. [Töltse le és telepítse a Fiddlert.](https://www.telerik.com/fiddler)

2. Konfigurálja a Fiddlert a HTTPS-forgalom visszafejtéséhez a [Fiddler súgódokumentációja alapján.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Konfigurálja a Fiddlert úgy, hogy egyéni szabályokkal beszúrja a *Restrict-Access-To-Tenants* és *a Restrict-Access-Context* fejléceket:

   1. A Fiddler Web Debugger eszközben válassza a **Szabályok menüt,** majd a **Szabályok testreszabása... lehetőséget.** a CustomRules fájl megnyitásához.

   2. Adja hozzá a következő sorokat a függvény `OnBeforeRequest` elejéhez. Cserélje \<List of tenant identifiers\> le a helyére a bérlőben regisztrált tartományt (például). `contoso.onmicrosoft.com` Cserélje \<directory ID\> le a helyére a bérlő Azure AD GUID-azonosítóját.  Meg **kell** jelennie a megfelelő GUID azonosítónak, hogy a naplók megjelenjenek a bérlőben. 

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

   Ha több bérlőt is engedélyeznie kell, használjon vesszőt a bérlőnevek elkülönítéséhez. Például:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárja be a CustomRules fájlt.

A Fiddler konfigurálása után a File (Fájl) menü **Capture** Traffic (Forgalom rögzítése) lehetőségének kiválasztásával rögzítheti **a forgalmat.**

### <a name="staged-rollout-of-proxy-settings"></a>Proxybeállítások szakaszos bevezetés

A proxyinfra infrastruktúra képességeitől függően előfordulhat, hogy képes lesz a beállítások felhasználók számára történő bevezetésére. Íme néhány magas szintű megfontolni kívánt lehetőség:

1. PAC-fájlokkal a tesztfelhasználókat egy tesztproxy-infrastruktúrára mutathatja, míg a normál felhasználók továbbra is az éles proxy infrastruktúrát használják.
2. Egyes proxykiszolgálók különböző konfigurációkat támogathatnak csoportok használatával.

További részletekért tekintse meg a proxykiszolgáló dokumentációját.

## <a name="blocking-consumer-applications-public-preview"></a>Fogyasztói alkalmazások blokkolása (nyilvános előzetes verzió)

A Microsofttól származó, fogyasztói és szervezeti fiókokat egyaránt támogató alkalmazások, például a [OneDrive](https://onedrive.live.com/) [vagy](/learn/)Microsoft Learn , néha ugyanazon az URL-címen is üzemeltetve vannak.  Ez azt jelenti, hogy azok a felhasználók, akik munkahelyi célokból hozzá kell férni az URL-címhez, személyes használatra is hozzáférhetnek, ami az üzemeltetési irányelvek értelmében nem engedélyezett.

Egyes szervezetek letiltásokkal megkísérlik kijavítani ezt a problémát, hogy blokkolják a személyes fiókok `login.live.com` hitelesítését.  Ennek több hátránya is van:

1. A blokkolás letiltja a személyes fiókok használatát a B2B vendégforgatókönyvek esetében, ami megzavarhatja a látogatókat és `login.live.com` az együttműködést.
1. [Az Autopilot használatához `login.live.com` a következő szükséges:](/mem/autopilot/networking-requirements) az üzembe helyezéshez. Az Intune és az Autopilot forgatókönyvei meghiúsulnak, ha `login.live.com` le van tiltva.
1. Az eszköz-login.live.com szolgáltatásra támaszkodó szervezeti telemetria- és Windows-frissítések [megszűnnek.](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)

### <a name="configuration-for-consumer-apps"></a>Konfiguráció fogyasztói alkalmazásokhoz

Bár a fejléc engedélyezési listaként működik, a Microsoft-fiók(MSA) blokk megtagadási jelként működik, és arra mondja az Microsoft-fiók-platformnak, hogy ne engedélyezze a felhasználóknak a felhasználói bejelentkezést a fogyasztói `Restrict-Access-To-Tenants` alkalmazásokba. Ennek a jelnek a elküldése érdekében a fejlécet a látogatott forgalomba injektálja a rendszer ugyanazokkal a vállalati proxyval vagy `sec-Restrict-Tenant-Access-Policy` tűzfallal, mint `login.live.com` [fent.](#proxy-configuration-and-requirements) A fejléc értékének a következőnek kell lennie: `restrict-msa` . Ha a fejléc jelen van, és egy fogyasztói alkalmazás megpróbál közvetlenül bejelentkezni egy felhasználóba, a bejelentkezés le lesz tiltva.

A fogyasztói alkalmazások hitelesítése jelenleg nem [](#admin-experience)jelenik meg a rendszergazdai naplókban, mivel a login.live.com az Azure AD-től külön üzemelnek.

### <a name="what-the-header-does-and-does-not-block"></a>Mit csinál a fejléc, és mit nem?

A szabályzat letiltja a fogyasztói alkalmazások használatát, de számos más típusú forgalmat és `restrict-msa` hitelesítést is lehetővé tesz:

1. Az eszközök felhasználó nélküli forgalma.  Ide tartozik az AutoPilot, a Windows Update és a szervezeti telemetria forgalma.
1. A fogyasztói fiókok B2B-hitelesítése. Azok a Microsoft-fiókkal rendelkező felhasználók, akik a bérlővel való együttműködésre vannak meghívva, login.live.com egy erőforrás-bérlő eléréséhez. [](../external-identities/redemption-experience.md#invitation-redemption-flow)
    1. Ez a hozzáférés a fejléc használatával szabályozható az adott erőforrás-bérlőhöz való `Restrict-Access-To-Tenants` hozzáférés engedélyezése vagy megtagadása érdekében.
1. "Passthrough" hitelesítés, amelyet számos Azure-alkalmazás és Office.com használ, ahol az alkalmazások az Azure AD-t használják a felhasználói felhasználók bejelentkeztető felhasználóinak felhasználói környezetben.
    1. Ez a hozzáférés a fejléc használatával is szabályozható, amely engedélyezi vagy megtagadja a speciális `Restrict-Access-To-Tenants` "áthaladó" bérlőhöz ( ) való `f8cdef31-a31e-4b4a-93e4-5f571e91255a` hozzáférést.  Ha ez a bérlő nem jelenik meg az engedélyezett tartományok listájában, az Azure AD blokkolni fogja a fogyasztói fiókokat az `Restrict-Access-To-Tenants` alkalmazásokba való bejelentkezésben.

## <a name="next-steps"></a>Következő lépések

- További információ a [frissített, modern Office 365-hitelesítésről](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Az [Office 365 URL-címeinek és IP-címtartományának áttekintése](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
