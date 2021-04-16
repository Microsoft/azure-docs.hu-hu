---
title: Üzembe helyezés Azure Active Directory alkalmazásproxy meg
description: Egy végpontok között található útmutató az alkalmazásproxy szervezeten belüli üzembe helyezésének megtervezéséhez
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: b0a3653d2cc840d745b1bb5788406b8d374c76d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533778"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD alkalmazás-proxy telepítési útmutató

Azure Active Directory (Azure AD) alkalmazásproxy egy biztonságos és költséghatékony távelérési megoldás a helyszíni alkalmazásokhoz. Azonnali áttérési útvonalat biztosít a "Cloud First" szervezetek számára a régi helyszíni alkalmazásokhoz való hozzáférés kezeléséhez, amelyek még nem képesek modern protokollok használatával. További bevezető információkért lásd: [Mi a alkalmazásproxy.](./application-proxy.md)

alkalmazásproxy ajánlott a távoli felhasználóknak hozzáférést adni a belső erőforrásokhoz. alkalmazásproxy helyett VPN-re vagy fordított proxyra van szükség ezekhez a távelérési esetekhez. Nem a vállalati hálózaton keresztüli felhasználók számára tervezték. Az intranetes hozzáféréshez alkalmazásproxy felhasználók nem kívánt teljesítménybeli problémákat tapasztalhatnak.

Ez a cikk az Azure AD-szolgáltatások tervezéséhez, működtetéséhez és kezeléséhez szükséges alkalmazásproxy.

## <a name="plan-your-implementation"></a>Az implementáció megterve

A következő szakasz átfogó áttekintést nyújt a hatékony üzembe helyezéshez szükséges fő tervezési elemekről.

### <a name="prerequisites"></a>Előfeltételek

Az implementáció megkezdése előtt teljesítenie kell az alábbi előfeltételeket. A környezet beállításával kapcsolatos további információkat ( beleértve ezeket az előfeltételeket) ebben az [oktatóanyagban láthatja.](application-proxy-add-on-premises-application.md)

* **Összekötők:** Az összekötők egyszerűsített ügynökök, amelyekre telepíthetők:
   * Fizikai hardver a helyszínen
   * Bármely hipervizormegoldásban üzemeltetett virtuális gép
   * Az Azure-ban üzemeltetett virtuális gép, amely lehetővé teszi a kimenő kapcsolatot alkalmazásproxy szolgáltatáshoz.

* Részletesebb [áttekintést Azure AD alkalmazás a Proxy-összekötők](application-proxy-connectors.md) Azure AD alkalmazás ismertető témakörben talál.

     * Az összekötők telepítése előtt az összekötő gépeket engedélyezni kell a [TLS 1.2-ben.](application-proxy-add-on-premises-application.md)

     * Ha lehetséges, az összekötőket ugyanabban a hálózatban és szegmensben telepítse, mint a háttér-webalkalmazás-kiszolgálókat. [](application-proxy-network-topology.md) Az alkalmazások felderítésének befejezése után ajánlott összekötőket üzembe helyezni.
     * Javasoljuk, hogy minden összekötőcsoport legalább két összekötővel rendelkezik a magas rendelkezésre állás és a méretezés érdekében. A három összekötő optimális arra az esetre, ha bármikor szervizelnünk kell egy gépet. Tekintse át [az összekötő kapacitástábláját,](./application-proxy-connectors.md#capacity-planning) amely segít eldönteni, hogy milyen típusú gépre telepítse az összekötőket. Minél nagyobb a gép, annál nagyobb lesz a puffer, és annál nagyobb lesz az összekötő.

* **Hálózati hozzáférési beállítások:** Az Azure AD alkalmazásproxy összekötői [HTTPS-kapcsolaton (443-as TCP-port) és HTTP-n (80-as TCP-port)](application-proxy-add-on-premises-application.md)keresztül csatlakoznak az Azure-hoz.

   * Az összekötőK TLS-forgalmának megszakítása nem támogatott, és megakadályozza, hogy az összekötők biztonságos csatornát létesítsen a megfelelő Azure-alkalmazásproxy-végpontjaikkal.

   * Kerülje az összekötők és az Azure közötti kimenő TLS-kommunikáción végzett beágyazott vizsgálat minden formáját. Az összekötők és a háttéralkalmazások közötti belső vizsgálat lehetséges, de ronthatja a felhasználói élményt, ezért nem ajánlott.

   * Maguk az összekötők terheléselosztása szintén nem támogatott, és nem is szükséges.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Fontos szempontok az Azure AD-alkalmazásproxy

Az Azure AD-szolgáltatások konfigurálásának és megvalósításához a következő alapvető követelményeknek kell alkalmazásproxy.

*  **Azure-beli bevetés:** Az alkalmazásproxy üzembe helyezése előtt a felhasználói identitásokat szinkronizálni kell egy helyszíni címtárból, vagy közvetlenül az Azure AD-bérlőkben kell létrehozni. identitásszinkronizálás lehetővé teszi, hogy az Azure AD előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést ad nekik az alkalmazásproxy által közzétett alkalmazásokhoz, és hogy rendelkezik az egyszeri bejelentkezés (SSO) végrehajtásához szükséges felhasználói azonosító adatokkal.

* **Feltételes hozzáférési követelmények:** Nem javasoljuk a alkalmazásproxy használata intranetes hozzáféréshez, mert ez olyan késést ad hozzá, amely hatással lesz a felhasználókra. Javasoljuk, hogy alkalmazásproxy előhitelesítési és feltételes hozzáférési házirendekkel használja az internetről történő táveléréshez.  A feltételes hozzáférés intranetes használatra való biztosítása az alkalmazások modernizálása, hogy azok közvetlenül hitelesíthetők legyenek az AAD-val. További [információ: Erőforrások alkalmazások AAD-be](./migration-resources.md) való áttelepítéséhez.

* **Szolgáltatáskorlátok:** Az erőforrások egyéni bérlők által túltervesztése elleni védelem érdekében alkalmazásonként és bérlőnként szabályozási korlátok vannak beállítva. A korlátokat az Azure AD szolgáltatási korlátozásait [és korlátozásait lásd:](../enterprise-users/directory-service-limits-restrictions.md). Ezek a szabályozási korlátok a tipikus használati mennyiséghez jóval nagyobb teljesítményteszten alapulnak, és elegendő puffert kínálnak az üzemelő példányok többségéhez.

* **Nyilvános tanúsítvány:** Ha egyéni tartományneveket használ, TLS-/SSL-tanúsítványt kell beszereznie. A szervezeti követelményektől függően a tanúsítvány lekérhet egy kis időt, és azt javasoljuk, hogy a lehető leghamarabb útjára ássa a folyamatot. Az Azure alkalmazásproxy támogatja a szabványos, [helyettesítő vagy](application-proxy-wildcard.md)SAN-alapú tanúsítványokat. További részletekért lásd: [Egyéni tartományok konfigurálása az Azure AD-alkalmazásproxy.](application-proxy-configure-custom-domain.md)

* **Tartományi követelmények:** A Kerberos által korlátozott delegálással (KCD) közzétett alkalmazásokba való egyszeri bejelentkezéshez az összekötőt és az alkalmazást futtató kiszolgálót tartományhoz kell csatlakoztatni, és ugyanannak a tartománynak vagy megbízhatósági tartománynak kell lennie.
A témával kapcsolatos részletes információkért lásd: [KCD egyszeri](application-proxy-configure-single-sign-on-with-kcd.md) bejelentkezéshez alkalmazásproxy. Az összekötő szolgáltatás a helyi rendszer környezetében fut, és nem konfigurálható egyéni identitás használatára.

* **URL-címek DNS-rekordjai**

   * Mielőtt egyéni tartományokat használ alkalmazásproxy létre kell hoznia egy CNAME rekordot a nyilvános DNS-ben, amely lehetővé teszi az ügyfelek számára, hogy az egyéni meghatározott külső URL-címet az előre meghatározott alkalmazásproxy feloldják. Ha nem hoz létre CNAME rekordot egy egyéni tartományt használó alkalmazáshoz, a távoli felhasználók nem fognak tudni csatlakozni az alkalmazáshoz. A CNAME-rekordok hozzáadásához szükséges lépések DNS-szolgáltatónként eltérőek lehetnek, ezért ismerje meg, hogyan kezelheti a DNS-rekordokat [és -rekordhalmazokat](../../dns/dns-operations-recordsets-portal.md)a Azure Portal.

   * Hasonlóképpen, az összekötő gazdagépek képeseknek kell lennie feloldani a közzétett alkalmazások belső URL-címét.

* **Rendszergazdai jogosultságok és szerepkörök**

   * **Az összekötő telepítéséhez** helyi rendszergazdai jogosultságokra van szükség ahhoz a Windows-kiszolgálóhoz, amelyre telepítve van. Emellett legalább egy alkalmazás-rendszergazdai szerepkörre van szükség az összekötőpéldány hitelesítéséhez és az Azure AD-bérlőben való regisztrálásához. 

   * **Az alkalmazások közzétételéhez és felügyelethez** *alkalmazás-rendszergazdai szerepkör szükséges.* Az alkalmazás-rendszergazdák a címtárban található összes alkalmazást kezelhetik, beleértve a regisztrációkat, az SSO-beállításokat, a felhasználói és csoport-hozzárendeléseket és a licencelést, alkalmazásproxy beállításokat és jóváhagyásokat. Nem biztosít lehetőséget a feltételes hozzáférés kezelésére. A *felhőalkalmazás-rendszergazda* szerepkör az alkalmazás-rendszergazda összes képességével rendelkezik, azzal a kivételt leszámítva, hogy nem engedélyezi az alkalmazásbeállítások alkalmazásproxy felügyeletét.

* **Licencelés:** alkalmazásproxy előfizetésen keresztül prémium szintű Azure AD érhető el. A [licencelési lehetőségek Azure Active Directory és](https://azure.microsoft.com/pricing/details/active-directory/) funkciók teljes listájáért tekintse meg a díjszabási Azure Active Directory oldalon.

### <a name="application-discovery"></a>Alkalmazásfelderítés

Állítson össze egy leltárt az összes olyan hatókörön belül, amelyet a alkalmazásproxy közzé a következő információk gyűjtésével:

| Információ típusa| Gyűjthető információk |
|---|---|
| Szolgáltatástípus| Például: SharePoint, SAP, CRM, egyéni webalkalmazás, API |
| Alkalmazásplatform | Például: Windows IIS, Apache On Linux, Tomcat, NGINX |
| Tartományi tagság| Webkiszolgáló teljes tartományneve (FQDN) |
| Alkalmazás helye | Hol található a webkiszolgáló vagy a farm az infrastruktúrában |
| Belső hozzáférés | Az alkalmazás belső eléréséhez használt pontos URL-cím. <br> Farm esetén milyen típusú terheléselosztást használ? <br> Azt határozza meg, hogy az alkalmazás nem saját forrásból származó tartalmat rajzol-e.<br> Állapítsa meg, hogy az alkalmazás WebSocketen keresztül működik-e. |
| Külső hozzáférés | Az a szállítói megoldás, amely számára az alkalmazás már elérhető külsőleg. <br> A külső hozzáféréshez használni kívánt URL-cím. Ha a SharePointot, győződjön meg arról, hogy az alternatív hozzáférés-leképezések az [útmutatónak megfelelően vannak konfigurálva.](/SharePoint/administration/configure-alternate-access-mappings) Ha nem, meg kell határoznia külső URL-címeket. |
| Nyilvános tanúsítvány | Ha egyéni tartományt használ, beszerez egy tanúsítványt a megfelelő tulajdonosnévvel. Ha létezik tanúsítvány, jegyezze fel a sorozatszámot és a helyet, ahonnan beszerezhető. |
| Hitelesítéstípus| Az alkalmazás által támogatott hitelesítési típus, például alapszintű, Windows-integrációs hitelesítés, űrlapalapú, fejlécalapú és jogcímek. <br>Ha az alkalmazás úgy van konfigurálva, hogy egy adott tartományi fiók alatt fusson, jegyezze fel a szolgáltatásfiók teljes tartománynevét.<br> SamL-alapúak az azonosítók és a válasz URL-címek. <br> Ha fejlécalapú, a szállítói megoldás és a hitelesítési típus kezelésének konkrét követelményei. |
| Összekötőcsoport neve | Annak az összekötőcsoportnak a logikai neve, amely a háttéralkalmazás számára biztosítja a conduitot és az SSO-t. |
| Felhasználók/csoportok hozzáférése | Azok a felhasználók vagy felhasználói csoportok, akik külső hozzáférést kapnak az alkalmazáshoz. |
| További követelmények | Jegyezze fel a további távelérési vagy biztonsági követelményeket, amelyek figyelembe lesznek véve az alkalmazás közzétételében. |

Az alkalmazások [leltározásához letöltheti](https://aka.ms/appdiscovery) ezt az alkalmazásleltár-táblázatot.

### <a name="define-organizational-requirements"></a>Szervezeti követelmények meghatározása

Az alábbiakban azokat a területeket érdemes meghatározni, amelyekhez meg kell határoznia a szervezet üzleti követelményeit. Mindegyik terület példákat tartalmaz a követelményekre

 **Hozzáférés**

* A tartományhoz vagy Azure AD-hez csatlakozott eszközökkel a távoli felhasználók biztonságosan, zökkenőmentes egyszeri bejelentkezéssel (SSO) férhetnek hozzá a közzétett alkalmazásokhoz.

* A jóváhagyott személyes eszközökkel dolgozó távoli felhasználók biztonságosan hozzáférhetnek a közzétett alkalmazásokhoz, ha regisztrálva vannak az MFA-ban, és a Microsoft Authenticator-alkalmazást regisztrálták a mobiltelefonjukon hitelesítési módszerként.

**Szabályozás**

* A rendszergazdák meghatározhatják és figyelhetik a felhasználói hozzárendelések életciklusát a alkalmazásproxy.

**Biztonság**

* Csak az alkalmazásokhoz csoporttagságon keresztül vagy egyénileg hozzárendelt felhasználók férhetnek hozzá ezekhez az alkalmazásokhoz.

**Teljesítmény**

* Az alkalmazásteljesítmény nem romlása a belső hálózatról való alkalmazás-hozzáféréshez képest.

**Felhasználói élmény**

* A felhasználók tisztában vannak azzal, hogyan férhetnek hozzá az alkalmazásaikhoz ismerős vállalati URL-címekkel bármilyen eszközplatformon.

**Naplózás**
* A rendszergazdák naplólhatják a felhasználói hozzáférési tevékenységeket.


### <a name="best-practices-for-a-pilot"></a>Teszthez ajánlott eljárások

Állapítsa meg, hogy mennyi időre és erőfeszítésre van szükség ahhoz, hogy egyetlen alkalmazás teljes mértékben be tudja azt tenni a táveléréshez egyszeri bejelentkezéssel (SSO). Ezt egy olyan próba futtatásával tegye meg, amely a kezdeti felderítést, közzétételt és általános tesztelést veszi figyelembe. Egy egyszerű, az integrált Windows-hitelesítés-hez (IWA) előre konfigurált IIS-alapú webalkalmazás használata segíthet az alapkonfiguráció megállapításában, mivel ehhez a beállításhoz minimális erőfeszítésre van szükség a távelérés és az SSO sikeres próbatelepítéséhez.

A következő tervezési elemeknek közvetlenül éles bérlőben kell növelniük a próbaterv sikeres végrehajtását.

**Összekötők kezelése:**

* Az összekötők kulcsfontosságú szerepet játszanak abban, hogy helyszíni összekötőket biztosítsunk az alkalmazásoknak. Az Alapértelmezett **összekötő** csoport használata elegendő a közzétett alkalmazások kezdeti próbatesztjéhez, mielőtt éles környezetbe csatlakoztatja őket. A sikeresen tesztelt alkalmazások ezután áthelyezhatók az éles összekötők csoportjaiba.

**Alkalmazáskezelés:**

* A munkaerő nagy valószínűséggel emlékszik a külső URL-címekre, amelyek ismerősek és relevánsak. Ne közzétenné az alkalmazást az előre definiált msappproxy.net vagy onmicrosoft.com használatával. Ehelyett adjon meg egy jól ismert legfelső szintű ellenőrzött tartományt, amely előtagja egy logikai állomásnév, például *intranet.<customers_domain>.com.*

* Az Azure MyApps portálon elrejtve korlátozhatja a próbaalkalmazás ikonjának láthatóságát egy próbacsoportra. Ha készen áll az éles környezetben való használatra, az alkalmazás hatókörét a megfelelő célközönségre is kiveheti, akár ugyanabban az éles üzem előtti bérlőben, akár az éles bérlőben való közzétételével.

**Egyszeri bejelentkezési beállítások:** Egyes egyszeri bejelentkezési beállítások meghatározott függőségekkel is vannak, amelyek beállítása időt is vegyen majd, ezért a függőségek időben való kezelése által elkerülheti a változásvezérlési késéseket. Ez magában foglalja az összekötő gazdagépek tartományhoz való csatlakozását az SSO Kerberos által korlátozott delegálás (KCD) használatával való végrehajtásához, valamint az egyéb időigényes tevékenységek elvégzéséhez. Például ping-hozzáférési példány beállítása fejlécalapú SSO-hoz.

**TLS az összekötő gazdagépe és** a célalkalmazás között: A biztonság alapvető fontosságú, ezért az összekötő gazdagépe és a célalkalmazások közötti TLS-t mindig érdemes használni. Különösen akkor, ha a webalkalmazás űrlapalapú hitelesítésre (FBA) van konfigurálva, mivel a felhasználói hitelesítő adatokat a rendszer ezután hatékonyan, tiszta szövegként továbbítja.

**Implementálja növekményesen, és tesztelje az egyes lépéseket.**
Egy alkalmazás közzététele után végezzen alapvető funkcionális tesztelést, hogy az alábbi utasítások szerint biztosítsa az összes felhasználói és üzleti követelménynek való megfelelést:

1. Tesztelje és ellenőrizze a webalkalmazás általános hozzáférését az előhitelesítés letiltásával.
2. Ha ez sikeres, engedélyezze az előhitelesítést, és rendeljen hozzá felhasználókat és csoportokat. A hozzáférés tesztelése és ellenőrzése.
3. Ezután adja hozzá az SSO metódust az alkalmazáshoz, és tesztelje újra a hozzáférés érvényesítéséhez.
4. Feltételes hozzáférési és MFA-szabályzatok alkalmazása szükség szerint. A hozzáférés tesztelése és ellenőrzése.

**Hibaelhárítási eszközök:** Hibaelhárításkor először mindig ellenőrizze a közzétett alkalmazáshoz való hozzáférést az összekötő gazdagépének böngészőjében, és ellenőrizze, hogy az alkalmazás a várt módon működik-e. Annál egyszerűbb a beállítás, annál könnyebben meghatározható a kiváltó ok, ezért érdemes lehet minimális konfigurációval reprodukálni a problémákat, például egyetlen összekötőt használni, egyszeri bejelentkezés nélkül. Bizonyos esetekben az olyan webes hibakeresési eszközök, mint a Telerik Fiddler, bizonyítják a proxyn keresztül elért alkalmazások hozzáférési vagy tartalomproblémáinak elhárítását. A Fiddler proxyként is használható a mobilplatformok, például az iOS és az Android forgalmának nyomon követéséhez és hibakereséshez, és gyakorlatilag bármihez, amely proxyn keresztüli útválasztásra konfigurálható. További [információért tekintse meg a](application-proxy-troubleshoot.md) hibaelhárítási útmutatót.

## <a name="implement-your-solution"></a>A megoldás megvalósítása

### <a name="deploy-application-proxy"></a>Üzembe alkalmazásproxy

A virtuális gép üzembe helyezésének alkalmazásproxy a következő oktatóanyagban található, amely egy helyszíni alkalmazás hozzáadását teszi lehetővé [táveléréshez.](application-proxy-add-on-premises-application.md) Ha a telepítés nem sikerült, válassza a hibaelhárítási alkalmazásproxy a portálon, **vagy** használja a hibaelhárítási útmutatót a Következő hibaelhárítási útmutatóban: Az [alkalmazásproxy-összekötő telepítése.](application-proxy-connector-installation-problem.md)

### <a name="publish-applications-via-application-proxy"></a>Alkalmazások közzététele alkalmazásproxy

A közzétételi alkalmazások feltételezik, hogy az összes előfeltétel teljesült, és hogy több összekötő is regisztráltként és aktívként jelenik meg a alkalmazásproxy lapon.

Az alkalmazásokat a [PowerShell használatával is közzéteheti.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)

Az alábbiakban néhány ajánlott gyakorlatot olvashat az alkalmazások közzétételekor:

* **Összekötőcsoportok használata:** Az egyes alkalmazások közzétételére kijelölt összekötőcsoport hozzárendelése. Javasoljuk, hogy minden összekötőcsoport legalább két összekötővel rendelkezik a magas rendelkezésre állás és a méretezés érdekében. A három összekötő optimális arra az esetre, ha bármikor ki kell szervizelni egy gépet. Emellett az [](application-proxy-connector-groups.md) Alkalmazások közzététele külön hálózatokon és helyeken összekötőcsoportok használatával szakaszból is láthatja, hogyan szegmentálhatja az összekötőket hálózat vagy hely alapján összekötőcsoportokkal.

* **Háttéralkalmazás** időtúllépésének beállítása: Ez a beállítás olyan esetekben lehet hasznos, amikor az alkalmazásnak több mint 75 másodpercre van szüksége egy ügyféltranzakció feldolgozásához. Például amikor egy ügyfél lekérdezést küld egy olyan webalkalmazásnak, amely előoldalként működik egy adatbázis számára. Az előtere elküldi ezt a lekérdezést a háttéradatbázis-kiszolgálójának, és megvárja a választ, de mire választ kap, a beszélgetés ügyféloldala időkorrelkul. A hosszabb tranzakciók befejezéséhez az időtúllépés hosszúra 180 másodpercet biztosít.

* **A megfelelő cookie-típusok használata**

   * **Csak HTTP-alapú cookie:** További biztonságot nyújt, alkalmazásproxy a HTTPOnly jelzőt a set-cookie HTTP-válaszfejlécekbe. Ezzel a beállítással csökkenthetők az olyan biztonsági rések, mint a webhelyközi parancsfájlok használata (XSS). Ne állítsa a Nem beállításra a munkamenet-cookie-hoz hozzáférést igénylő ügyfelek/felhasználói ügynökök esetében. Például egy alkalmazásproxyn keresztül közzétett távoli asztali/MTSC Távoli asztali átjáró csatlakozik egy alkalmazáshoz.

   * **Biztonságos cookie:** Ha egy cookie Biztonságos attribútummal van beállítva, a felhasználói ügynök (ügyféloldali alkalmazás) csak akkor foglalja bele a cookie-t a HTTP-kérésekbe, ha a kérésTLS által védett csatornán keresztül van továbbítva. Ez segít csökkenteni annak kockázatát, hogy egy cookie-t feltörnek a tiszta szöveges csatornákon keresztül, ezért engedélyezni kell.

   * **Perzisztens** cookie: Lehetővé teszi, alkalmazásproxy munkamenet-cookie a böngésző bezárásai között is megmaradjon, amíg az érvényesség nem jár le vagy törölve nem lesz. Olyan forgatókönyvekhez használatos, ahol egy gazdag alkalmazás, például az office egy közzétett webalkalmazáson belül fér hozzá egy dokumentumhoz anélkül, hogy a felhasználónak újra meg kell adnia a hitelesítést. Legyen óvatos, mivel az állandó cookie-k végső soron a szolgáltatást jogosulatlan hozzáférés veszélyének tehetik ki, ha nem használják más kompenzáló vezérlőkkel együtt. Ezt a beállítást csak olyan régebbi alkalmazásokhoz szabad használni, amelyek nem tudnak cookie-kat megosztani a folyamatok között. A beállítás használata helyett inkább frissítse az alkalmazást úgy, hogy kezelje a cookie-k folyamatok közötti megosztását.

* **URL-címek** fordítása a fejlécben: Ezt olyan forgatókönyveknél engedélyezheti, amikor a belső DNS nem konfigurálható úgy, hogy megfeleljen a szervezet nyilvános névterének (azaz a Split DNS-nek). Ha az alkalmazásnak nincs szüksége az eredeti állomásfejlécre az ügyfélkérésben, hagyja ezt az értéket Igen értékre állítva. Másik lehetőségként az összekötő a belső URL-címben található teljes tartománynévvel útválasztást használ a tényleges forgalomhoz, a külső URL-címben pedig a teljes tartománynévvel, gazdafejlécként. A legtöbb esetben ennek az alternatívának lehetővé kell tennie, hogy az alkalmazás a megszokott módon működn távolról való hozzáférés esetén, de a felhasználók elveszítik a külső URL-címen belüli egyeztetés & előnyeit.

* **Url-címek fordítása az alkalmazás törzsében:** Kapcsolja be az alkalmazás törzsének hivatkozásfordítását, ha azt szeretné, hogy az alkalmazás hivatkozásait lefordítsa a rendszer az ügyfélnek adott válaszokban. Ha engedélyezve van, ez a függvény mindent megtesz annak érdekében, hogy lefordítsa az alkalmazásproxy által HTML-ben és CSS-ben talált összes belső hivatkozást, és visszaadja az ügyfeleknek. Ez akkor hasznos, ha olyan alkalmazásokat ad közzé, amelyek nem kódolt abszolút vagy NetBIOS rövid név hivatkozásokat tartalmaznak a tartalomban, vagy olyan alkalmazások közzétételekor, amelyek más helyszíni alkalmazásokra mutató tartalmakra mutató hivatkozásokat tartalmaznak.

Olyan forgatókönyvek esetén, ahol egy közzétett alkalmazás más közzétett alkalmazásokra hivatkozik, engedélyezze a hivatkozásfordítást minden alkalmazáshoz, hogy alkalmazásonként szabályozni tudja a felhasználói élményt.

Tegyük fel például, hogy három alkalmazást tett közzé az alkalmazásproxy használatával, amelyek mind egymásra hivatkoznak: Előnyök, Kiadások és Utazás, valamint egy negyedik alkalmazás, a Visszajelzés, amely nem a alkalmazásproxy.

![1. kép](media/App-proxy-deployment-plan/link-translation.png)

Ha engedélyezi az Előnyök alkalmazás hivatkozásfordítását, a Költségek és az Utazás hivatkozásokat a rendszer átirányítja az alkalmazások külső URL-címére, így a vállalati hálózaton kívülről hozzáférő felhasználók elérhetik azokat. A Költségek és az Előnyökre való visszaút hivatkozásai nem működnek, mert a hivatkozásfordítás nincs engedélyezve a két alkalmazáshoz. A visszajelzésre mutató hivatkozás nem lesz átirányítva, mert nincs külső URL-cím, így az Előnyök alkalmazást használó felhasználók nem fognak tudni hozzáférni a visszajelzési alkalmazáshoz a vállalati hálózaton kívülről. Tekintse meg a [hivatkozásfordítással és az egyéb átirányítási lehetőségekkel kapcsolatos részletes információkat.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Az alkalmazás elérése

Számos lehetőség létezik az alkalmazásproxy által közzétett erőforrásokhoz való hozzáférés kezelésére, ezért válassza ki az adott forgatókönyvnek és skálázhatósági igényeknek leginkább megfelelőt. Gyakori megközelítések a következők: az Azure AD Connect-n keresztül szinkronizált helyszíni csoportok használata, dinamikus csoportok létrehozása az Azure AD-ben felhasználói attribútumok alapján, erőforrás-tulajdonos által felügyelt önkiszolgáló csoportok használata, vagy ezek kombinációja. Az egyes erőforrások előnyeiért tekintse meg a csatolt erőforrásokat.

A felhasználók alkalmazás-hozzáférésének hozzárendelésének legátlottabb  módja a közzétett alkalmazás bal oldali paneljére kattintva a Felhasználók és csoportok lehetőségek megnyitása, és a csoportok vagy személyek közvetlen hozzárendelése.

![24. kép](media/App-proxy-deployment-plan/add-user.png)

Önkiszolgáló hozzáférést is engedélyezhet a felhasználóknak az alkalmazáshoz, ha olyan csoportot rendel hozzá, amely jelenleg nem tagja, és konfigurálja az önkiszolgáló beállításokat.

![25. kép](media/App-proxy-deployment-plan/allow-access.png)

Ha engedélyezve van, a felhasználók bejelentkeznek a MyApps portálra, és hozzáférést kérhetnek, és automatikusan jóváhagyhatják és hozzáadhatják őket a már engedélyezett önkiszolgáló csoporthoz, vagy jóváhagyást kérhetnek egy kijelölt jóváhagyótól.

A vendégfelhasználók az [Azure AD B2B-n](../external-identities/add-users-information-worker.md)keresztül alkalmazásproxy belső alkalmazások eléréséhez is meghívhatóak.

Az olyan helyszíni alkalmazások esetében, amelyek általában névtelenül érhetők el, és nincs szükség hitelesítésre, inkább tiltsa le az alkalmazás Tulajdonságok területén **található beállítást.**

![26. kép](media/App-proxy-deployment-plan/assignment-required.png)


Ha ezt a beállítást a Nem lehetőségre hagyja, a felhasználók engedély Azure AD alkalmazás proxyn keresztül férhetnek hozzá a helyszíni alkalmazáshoz, ezért körültekintően járjon el.

Az alkalmazást a közzététele után elérhetővé kell tenni úgy, hogy beírja a külső URL-címét egy böngészőbe vagy az ikonját a következő helyen: [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Előhitelesítés engedélyezése

Ellenőrizze, hogy az alkalmazás elérhető-alkalmazásproxy a külső URL-címen keresztül.

1. Lépjen a **Azure Active Directory** alkalmazások Minden alkalmazás  >  **lapra,** és  >   válassza ki a kezelni kívánt alkalmazást.

2. Válassza a **alkalmazásproxy** lehetőséget.

3. Az **Előhitelesítés mezőben** a legördülő listából válassza ki a Azure Active Directory, majd válassza **a** **Mentés lehetőséget.**

Ha az előhitelesítés engedélyezve van, az Azure AD először a felhasználók hitelesítését fogja igényelni, és ha az egyszeri bejelentkezés konfigurálva van, akkor a háttéralkalmazás az alkalmazáshoz való hozzáférés engedélyezése előtt a felhasználót is ellenőrzi. Az előhitelesítési mód passthrough módról Azure AD-re való váltása a külső URL-címet is HTTPS-kapcsolaton keresztül konfigurálja, így a KEZDETBEN HTTP-hez konfigurált alkalmazások https-kapcsolaton keresztül lesznek biztosítva.

### <a name="enable-single-sign-on"></a>Egyetlen Sign-On

Az SSO a lehető legjobb felhasználói élményt és biztonságot biztosítja, mivel a felhasználóknak csak egyszer kell bejelentkezniük az Azure AD elérésekor. Ha a felhasználó már rendelkezik előzetes hitelesítéssel, az SSO-t a alkalmazásproxy-összekötő végzi el, amely a helyszíni alkalmazásban hitelesíti magát a felhasználó nevében. A háttéralkalmazás úgy dolgozza fel a bejelentkezést, mintha maga a felhasználó lenne.

Az **Áttűnés lehetőség választásával** a felhasználók anélkül férhetnek hozzá a közzétett alkalmazáshoz, hogy hitelesíteniük kell magukat az Azure AD-ban.

Az SSO végrehajtása csak akkor lehetséges, ha az Azure AD azonosítani tudja az erőforráshoz hozzáférést kérő felhasználót, ezért az alkalmazást úgy kell konfigurálni, hogy előre hitelesítse a felhasználókat az Azure AD-val az SSO működéséhez való hozzáféréskor, különben az SSO-beállítások le lesznek tiltva.

Olvassa [el az Azure AD-alkalmazásokba](what-is-single-sign-on.md) való egyszeri bejelentkezést, hogy segítsen kiválasztani a legmegfelelőbb egyszeri bejelentkezési módszert az alkalmazások konfigurálásakor.

###  <a name="working-with-other-types-of-applications"></a>Más típusú alkalmazásokkal való munka

Az Azure AD alkalmazásproxy a [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md)használatára fejlesztett alkalmazásokat is támogat. Támogatja a natív ügyfélalkalmazásokat azáltal, hogy az ügyfélkérés fejlécinformációiban kapott Azure AD-jogkivonatokat használja az előhitelesítés végrehajtásához a felhasználók nevében.

A [natív és mobil ügyfélalkalmazások](./application-proxy-configure-native-client-application.md) és [jogcímalapú](./application-proxy-configure-for-claims-aware-applications.md) alkalmazások közzétételével kapcsolatos további információért olvassa el a alkalmazásproxy.

### <a name="use-conditional-access-to-strengthen-security"></a>A biztonság megerősítése feltételes hozzáféréssel

Az alkalmazásbiztonság olyan fejlett biztonsági képességeket igényel, amelyek a helyszínen és a felhőben is védelmet nyújtanak az összetett fenyegetések ellen, és reagálnak ezekre. A támadók leggyakrabban gyenge, alapértelmezett vagy ellopott felhasználói hitelesítő adatokkal szereznek vállalati hálózati hozzáférést.  A Microsoft identitásalapú biztonsága mind a kiemelt, mind a nem kiemelt identitások kezelésével és védelmével csökkenti az ellopott hitelesítő adatok használatát.

Az Alábbi képességek használhatók az Azure AD-alkalmazásproxy:

* Felhasználó- és helyalapú feltételes hozzáférés: A bizalmas adatok védelme érdekében földrajzi hely vagy IP-cím alapján korlátozza a felhasználói hozzáférést helyalapú feltételes [hozzáférési szabályzatokkal.](../conditional-access/location-condition.md)

* Eszközalapú feltételes hozzáférés: Az eszközalapú feltételes hozzáféréssel biztosíthatja, hogy csak a regisztrált, jóváhagyott és megfelelő eszközök férnek hozzá a [vállalati adatokhoz.](../conditional-access/require-managed-devices.md)

* Alkalmazásalapú feltételes hozzáférés: A munkát nem kell leállítani, ha egy felhasználó nem a vállalati hálózaton van. [Biztonságos hozzáférés a vállalati felhőhöz és helyszíni alkalmazásokhoz,](../conditional-access/app-based-conditional-access.md) és a feltételes hozzáféréssel tarthatja fenn az irányítást.

* Kockázatalapú feltételes hozzáférés: Megvédheti adatait [a](https://www.microsoft.com/cloud-platform/conditional-access) rosszindulatú támadóktól egy kockázatalapú feltételes hozzáférési szabályzat segítségével, amely minden alkalmazásra és minden felhasználóra alkalmazható, akár a helyszínen, akár a felhőben.

* Azure AD Saját alkalmazások: Az alkalmazásproxy-szolgáltatás üzembe helyezése és az alkalmazások biztonságos közzététele után a felhasználók egy egyszerű központot kínálnak az összes alkalmazás felderítésére és elérésére. Növelheti az önkiszolgáló képességek hatékonyságát, például új alkalmazásokhoz és csoportokhoz való hozzáférést kérhet, vagy kezelheti az erőforrásokhoz való hozzáférést mások nevében a [Saját alkalmazások.](https://aka.ms/AccessPanelDPDownload)

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

### <a name="required-roles"></a>Szükséges szerepkörök

A Microsoft azt az elvet javasolja, hogy a lehető legkevesebb jogosultságot adja meg a szükséges feladatok végrehajtásához az Azure AD-val. [Tekintse át a különböző elérhető Azure-szerepköröket,](../roles/permissions-reference.md) és válassza ki az egyes személyek igényeinek megfelelőt. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és az üzembe helyezés befejezése után el kell távolítani őket.

| Üzleti szerepkör| Üzleti feladatok| Azure AD-szerepkörök |
|---|---|---|
| Ügyfélszolgálati rendszergazda | Jellemzően a végfelhasználók által jelentett problémák minősítésére és korlátozott feladatok elvégzésére, például a felhasználók jelszavának módosítására, a frissítési jogkivonatok érvénytelenítésére és a szolgáltatás állapotának figyelésére korlátozódik. | Helpdesk Administrator |
| Identitás-rendszergazda| Az alkalmazásproxyval kapcsolatos hibák hibakereséséhez olvassa el az Azure AD bejelentkezési jelentéseit és auditnaplóit.| Biztonsági olvasó |
| Alkalmazástulajdonos| A vállalati alkalmazások, alkalmazásregisztrációk és alkalmazásproxy-beállítások minden aspektusát létrehozhatja és kezelheti.| Alkalmazás-rendszergazda |
| Infrastruktúra-rendszergazda | Tanúsítványváltás tulajdonosa | Alkalmazás-rendszergazda |

A biztonságos információkhoz vagy erőforrásokhoz hozzáféréssel rendelkező személyek számának minimalizálása segít csökkenteni annak esélyét, hogy egy rosszindulatú szereplő jogosulatlan hozzáférést szerezzen, vagy hogy egy jogosult felhasználó véletlenül hatással legyen egy bizalmas erőforrásra.

A felhasználóknak azonban továbbra is napi szintű emelt szintű műveleteket kell végezniük, ezért az Azure-erőforrásokhoz és az Azure AD-hez igény szerinti emelt szintű hozzáférés érdekében igény szerinti (JIT [Privileged Identity Management)](../privileged-identity-management/pim-configure.md) szabályzatok kényszerítése javasolt a felügyeleti hozzáférés és a naplózás hatékony kezeléséhez.

### <a name="reporting-and-monitoring"></a>Jelentés és monitorozás

Az Azure AD auditnaplók és jelentések segítségével további betekintést nyújt a szervezet alkalmazáshasználatába és működési [állapotába.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) alkalmazásproxy az Azure AD portálról és a Windows eseménynaplóiból is rendkívül egyszerűen figyelheti az összekötőket.

#### <a name="application-audit-logs"></a>Alkalmazások auditnaplói

Ezek a naplók részletes információkat nyújtanak a alkalmazásproxy konfigurált alkalmazásokba való bejelentkezésről, valamint az eszközről és az alkalmazást elfelhasználóról. [Az auditnaplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) a naplózási Azure Portal az Audit API for export [(Exportálási API naplózása)](/graph/api/resources/directoryaudit) alatt találhatók. Emellett a használati [és elemzési jelentések](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) is elérhetők az alkalmazáshoz.

#### <a name="application-proxy-connector-monitoring"></a>alkalmazásproxy összekötő monitorozása

Az összekötők és a szolgáltatás gondoskodik az összes magas rendelkezésre állási feladatról. Az összekötők állapotát az Azure AD alkalmazásproxy lapján követheti nyomon. További információ az összekötők karbantartásról: [Az Azure AD alkalmazásproxy összekötői.](./application-proxy-connectors.md#maintenance)

![Példa: Azure AD alkalmazásproxy összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-eseménynaplók és teljesítményszámlálók

Az összekötők rendszergazdai és munkamenet-naplókkal is rendelkezik. A rendszergazdai naplók tartalmazzák a kulcsfontosságú eseményeket és azok hibáit. A munkamenetnaplók tartalmazzák az összes tranzakciót és azok feldolgozási részleteit. A naplók és számlálók a Windows eseménynaplóiban találhatók további információkért lásd: [Az Azure AD alkalmazásproxy összekötői.](./application-proxy-connectors.md#under-the-hood) Kövesse ezt az [oktatóanyagot az eseménynapló-adatforrások a](../../azure-monitor/agents/data-sources-windows-events.md)Azure Monitor.

### <a name="troubleshooting-guide-and-steps"></a>Hibaelhárítási útmutató és lépések

A gyakori problémákról és azok megoldásáról a hibaüzenetek hibaelhárítását [bemutató](application-proxy-troubleshoot.md) útmutatónkban olvashat bővebben.

Az alábbi cikkek gyakori forgatókönyveket tartalmaznak, amelyek hibaelhárítási útmutatók létrehozására is használhatók a támogatási szervezet számára.

* [Probléma az alkalmazáslap megjelenítésekor](application-proxy-page-appearance-broken-problem.md)
* [Az alkalmazás betöltési ideje túl hosszú](application-proxy-page-load-speed-problem.md)
* [Az alkalmazáslap hivatkozásai nem működnek](application-proxy-page-links-broken-problem.md)
* [Milyen portokat nyissak meg az alkalmazáshoz?](application-proxy-add-on-premises-application.md)
* [Az alkalmazás összekötőcsoportjában nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)
* [Konfigurálás a felügyeleti portálon](application-proxy-config-how-to.md)
* [Egyszeri bejelentkezés beállítása az alkalmazáshoz](application-proxy-config-sso-how-to.md)
* [Probléma egy alkalmazás felügyeleti portálon való létrehozásakor](application-proxy-config-problem.md)
* [A Kerberos által korlátozott delegálás konfigurálása](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurálás a PingAccess segítségével](./application-proxy-ping-access-publishing-guide.md)
* [„Can't Access this Corporate Application” (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](application-proxy-connector-installation-problem.md)
* [Bejelentkezéssel kapcsolatos probléma](application-sign-in-problem-on-premises-application-proxy.md)
