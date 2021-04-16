---
title: A Microsoft identitásplatform fejlesztői szószedete | Azure
description: A Microsoft identitásplatformján gyakran használt fejlesztői fogalmak és funkciók kifejezései.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 930341b60f785c2c618be4ee235225519a08aaa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530065"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>A Microsoft identitásplatform fejlesztői szószedete

Ez a cikk néhány alapvető fejlesztői fogalom és terminológia definícióit tartalmazza, amelyek hasznosak lehetnek a Microsoft identitásplatformot használó alkalmazásfejlesztés elsajátításában.

## <a name="access-token"></a>hozzáférési jogkivonat

Egy engedélyezési kiszolgáló által [](#authorization-server) [kiadott](#security-token) és egy ügyfélalkalmazás [által](#client-application) a védett erőforrás-kiszolgáló eléréséhez használt biztonsági [jogkivonat típusa.](#resource-server) A jogkivonat általában [JSON-webtoken (JWT)][JWT]formájában testesíti meg az erőforrás tulajdonosa [](#resource-owner)által az ügyfélnek adott engedélyt a kért hozzáférési szinthez. A jogkivonat [](#claim) tartalmazza a tárgyra vonatkozó összes vonatkozó jogcímet, így az ügyfélalkalmazás hitelesítő adatként használhatja azt egy adott erőforrás elérésekor. Ez azt is szükségtelenné teszi, hogy az erőforrás tulajdonosa tegye elérhetővé a hitelesítő adatokat az ügyfél számára.

A hozzáférési jogkivonatok csak rövid ideig érvényesek, és nem vonhatók vissza. Az engedélyezési kiszolgáló frissítési jogkivonatot is [kibocsáthat](#refresh-token) a hozzáférési jogkivonat kiállításakor. A frissítési jogkivonatok általában csak bizalmas ügyfélalkalmazások számára biztosítanak.

A hozzáférési jogkivonatokat néha "Felhasználó+Alkalmazás" vagy "Csak alkalmazás" néven is nevezik, a hitelesítő adatoktól függően. Ha például egy ügyfélalkalmazás a következőt használja:

* ["Engedélyezési kód" engedélyezési engedély,](#authorization-grant)a végfelhasználó először az erőforrás tulajdonosaként hitelesíti magát, delegálva az engedélyezést az ügyfélnek az erőforrás eléréséhez. Az ügyfél ezt követően hitelesíti magát a hozzáférési jogkivonat beszerzésekor. A jogkivonatra néha pontosabban "User+App" tokenként hivatkozunk, mivel az ügyfélalkalmazást engedélyező felhasználót és az alkalmazást is képviseli.
* [Az "Ügyfél](#authorization-grant)hitelesítő adatai" engedélyezési engedély, az ügyfél biztosítja az egyetlen hitelesítést, amely az erőforrás-tulajdonos hitelesítése/engedélyezése nélkül működik, így a jogkivonatot néha "Csak alkalmazás" jogkivonatnak is nevezik.

További részletekért tekintse meg a [Microsoft identitásplatformján a jogkivonatok][AAD-Tokens-Claims] referenciáját.

## <a name="application-id-client-id"></a>alkalmazásazonosító (ügyfél-azonosító)

Az Azure AD egyedi azonosító egy adott alkalmazást és a társított konfigurációkat azonosító alkalmazásregisztrációval kapcsolatos problémákat tartalmaz. Ez az alkalmazásazonosító[(ügyfél-azonosító)](https://tools.ietf.org/html/rfc6749#page-15)a hitelesítési kérések végrehajtásakor használatos, és a hitelesítési kódtárakhoz ad meg a fejlesztés során. Az alkalmazásazonosító (ügyfél-azonosító) nem titkos.

## <a name="application-manifest"></a>alkalmazásjegyzék

A Azure Portal által [][AZURE-portal]biztosított szolgáltatás, amely az alkalmazás identitáskonfigurációjának JSON-ábrázolást hoz létre, amely a társított [Application][Graph-App-Resource] és [ServicePrincipal][Graph-Sp-Resource] entitások frissítésének mechanizmusa. További [részletekért Azure Active Directory az alkalmazásjegyzék][AAD-App-Manifest] ismertetése.

## <a name="application-object"></a>alkalmazásobjektum

Amikor regisztrál/frissít egy alkalmazást a Azure Portal [portál][AZURE-portal]létrehoz/frissít egy alkalmazásobjektumot [](#service-principal-object) és egy megfelelő szolgáltatásnév-objektumot is az adott bérlőhöz. Az alkalmazásobjektum globálisan határozza meg az alkalmazás identitáskonfigurációját (az összes olyan bérlőn, amelyhez hozzáféréssel  rendelkezik), és egy olyan sablont biztosít, amelyből a megfelelő szolgáltatásnév-objektum(oka)t származtatják helyi használatra futásidőben (egy adott bérlőben). 

További információ: [Alkalmazás- és szolgáltatásnév-objektumok.][AAD-App-SP-Objects]

## <a name="application-registration"></a>alkalmazásregisztráció

Ahhoz, hogy egy alkalmazás integrálható legyen az Identitás- és hozzáférés-kezelési funkciókkal, és [](#tenant)delegálható legyen az Azure AD-nek, regisztrálni kell egy Azure AD-bérlővel. Amikor regisztrálja az alkalmazást az Azure AD-ban, identitáskonfigurációt biztosít az alkalmazáshoz, így integrálható az Azure AD-val, és a következő funkciókat használhatja:

* Az egyetlen Sign-On kezelése az Azure AD Identity Management és a [OpenID Connect][OpenIDConnect] protokoll implementációja használatával
* Közvetített hozzáférés a védett [erőforrásokhoz](#resource-server) [ügyfélalkalmazások által,](#client-application)OAuth 2.0 engedélyezési [kiszolgálón keresztül](#authorization-server)
* [Hozzájárulási keretrendszer](#consent) a védett erőforrásokhoz való ügyfél-hozzáférés erőforrás-tulajdonosi engedélyezés alapján való kezeléséhez.

További [részletekért lásd: Alkalmazások integrálása Azure Active Directory][AAD-Integrating-Apps] alkalmazásokkal.

## <a name="authentication"></a>hitelesítés

Egy fél szabályos hitelesítő adatainak behívója, amely alapul biztosít egy identitás- és hozzáférés-vezérlésre használható rendszerbiztonsági tag létrehozását. Az [OAuth2](#authorization-grant) engedélyezési engedély megadása során például a hitelesítést [](#resource-owner) töltő [](#client-application)fél tölti ki az erőforrás-tulajdonos vagy az ügyfélalkalmazás szerepkörét a felhasznált engedélytől függően.

## <a name="authorization"></a>engedélyezés

Hitelesített rendszerbiztonsági tag engedélyének megadása egy dologra. Az Azure AD programozási modellben két alapvető használati eset létezik:

* [OAuth2](#authorization-grant) engedélyezési folyamat során: [](#resource-owner) amikor az erőforrás tulajdonosa engedélyt ad az ügyfélalkalmazásnak, [](#client-application)lehetővé teszi, hogy az ügyfél hozzáférjen az erőforrás-tulajdonos erőforrásaihoz.
* Az ügyfél erőforrás-hozzáférése során: az erőforrás-kiszolgáló [](#claim) által megvalósított [](#access-token) módon, a hozzáférési jogkivonatban található jogcímértékek használatával, amelyek alapján a hozzáférés-vezérléssel kapcsolatos döntéseket hozhat. [](#resource-server)

## <a name="authorization-code"></a>engedélyezési kód

Az engedélyezési végpont által az [](#client-application) ügyfélalkalmazás [](#authorization-endpoint)számára biztosított rövid életű "jogkivonat" az engedélyezési kód folyamatának részeként a négy OAuth2 engedélyezési folyamat [egyikének ad engedélyt.](#authorization-grant) A rendszer visszaadja [a](#resource-owner)kódot az ügyfélalkalmazásnak az erőforrás-tulajdonos hitelesítésére válaszul, ami azt jelzi, hogy az erőforrás tulajdonosa delegálta a hozzáférést a kért erőforrásokhoz. A folyamat részeként a kódot később beváltjuk egy [hozzáférési jogkivonatért.](#access-token)

## <a name="authorization-endpoint"></a>engedélyezési végpont

Az engedélyezési kiszolgáló által megvalósított egyik [végpont,](#authorization-server)amely [](#resource-owner) az erőforrás tulajdonosával [](#authorization-grant) való kommunikációra szolgál az OAuth2 engedélyezési folyamat során engedélyezési engedély megadásához. A ténylegesen megadott engedély a használt engedélyezési folyamattól [](#authorization-code) függően változhat, beleértve az engedélyezési kódot vagy a [biztonsági jogkivonatot](#security-token)is.

További részletekért tekintse meg [][OAuth2-AuthZ-Grant-Types] az OAuth2 specifikáció engedélyezési engedélyezési típusait és az engedélyezési végpont szakaszait, valamint az [OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] specifikációt. [][OAuth2-AuthZ-Endpoint]

## <a name="authorization-grant"></a>engedélyezés megadása

Egy hitelesítő adat, amely az erőforrás [tulajdonosának](#resource-owner) [](#authorization) a védett erőforrások elérésére vonatkozó engedélyét képviseli, amely az [ügyfélalkalmazásnak van meg adni.](#client-application) Az ügyfélalkalmazások az [OAuth2][OAuth2-AuthZ-Grant-Types] engedélyezési keretrendszer által meghatározott négy engedélytípus egyikének használatával kaphatják meg a hozzáférést az ügyfél típusától/követelményeitől függően: "engedélyezési kód megadása", "ügyfél-hitelesítő adatok megadása", "implicit engedélyezés" és "erőforrás-tulajdonosi jelszóhitelesítmény-engedély". Az ügyfélnek visszaadott hitelesítő adat egy hozzáférési jogkivonat [,](#access-token)vagy egy engedélyezési kód [(ezt](#authorization-code) később cserélik le hozzáférési jogkivonatra), a használt engedélyezési engedély típusától függően.

## <a name="authorization-server"></a>engedélyezési kiszolgáló

Az [OAuth2][OAuth2-Role-Def]engedélyezési keretrendszerében meghatározottak szerint a kiszolgáló felelős [](#client-application) a hozzáférési jogkivonatok [](#resource-owner) az ügyfélnek való kiadásáért az erőforrás tulajdonosának sikeres hitelesítése és az engedélyezés beszerzése után. Az [ügyfélalkalmazások](#client-application) futásidőben lépnek kapcsolatba [](#authorization-endpoint) az [](#token-endpoint) engedélyezési kiszolgálóval annak engedélyezési és jogkivonatvégpontjaival, az OAuth2 által meghatározott engedélyezési [engedélyekkel összhangban.](#authorization-grant)

A Microsoft identitásplatformjának alkalmazásintegrációja esetén a Microsoft identitásplatformja implementálja az engedélyezési kiszolgálói szerepkört az Azure AD-alkalmazásokhoz és a Microsoft szolgáltatási API-khoz, például a Microsoft Graph [API-khoz.][Microsoft-Graph]

## <a name="claim"></a>Azt állítják

A [biztonsági jogkivonat jogcímeket](#security-token) tartalmaz, amelyek helyességi [](#client-application) adatokat biztosítanak egy entitásról (például egy ügyfélalkalmazásról vagy erőforrás-tulajdonosról) egy másik entitásnak (például az [](#resource-owner) [erőforrás-kiszolgálónak).](#resource-server) A jogcímek olyan név/érték párok, amelyek a jogkivonat tulajdonosával (például az engedélyezési kiszolgáló által hitelesített rendszerbiztonsági taggal) kapcsolatos tényeket [továbbítják.](#authorization-server) Az adott jogkivonatban jelen van jogcímek számos változótól függenek, beleértve a jogkivonat típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatok típusát, az alkalmazás konfigurációját stb.

További részletekért tekintse meg a [Microsoft identitásplatform jogkivonatának][AAD-Tokens-Claims] referenciáját.

## <a name="client-application"></a>ügyfélalkalmazás

Az [OAuth2][OAuth2-Role-Def]engedélyezési keretrendszere által meghatározottak szerint egy alkalmazás, amely a erőforrás tulajdonosa nevében védett [erőforrás-kérelmeket tesz lehetővé.](#resource-owner) Az "ügyfél" kifejezés nem utal konkrét hardver-megvalósítási jellemzőkre (például arra, hogy az alkalmazás egy kiszolgálón, asztali számítógépen vagy más eszközökön fut-e).

Az ügyfélalkalmazás [](#authorization) engedélyt kér egy erőforrás-tulajdonostól az [OAuth2](#authorization-grant) engedélyezési folyamatában való részvételhez, és hozzáférhet az erőforrás tulajdonosának nevében az API-khoz/adatokhoz. Az OAuth2 engedélyezési keretrendszer két ügyféltípust határoz meg [,][OAuth2-Client-Types]a "bizalmas" és a "nyilvános" típust, az alapján, hogy az ügyfél fenn tudja tartani a hitelesítő adatainak bizalmas kezelését. Az alkalmazások megvalósíthatnak egy webes [ügyfelet (bizalmas),](#web-client) amely egy webkiszolgálón, egy eszközön telepített [natív ügyfélen (nyilvános)](#native-client) vagy egy felhasználóügynök-alapú [(nyilvános)](#user-agent-based-client) ügyfélen fut, amely egy eszköz böngészőjében fut.

## <a name="consent"></a>Beleegyezés

Az a folyamat, [amely](#resource-owner) során egy erőforrás-tulajdonos engedélyt ad [](#permissions)egy ügyfélalkalmazásnak [a](#client-application)védett erőforrások adott engedélyekkel való elérésére az erőforrás-tulajdonos nevében. Az ügyfél által kért engedélyektől függően egy rendszergazdától vagy felhasználótól jóváhagyást kell kérni a szervezeti/egyéni adatokhoz való hozzáféréshez. Vegye figyelembe, hogy [több-bérlős](#multi-tenant-application) forgatókönyvekben az alkalmazás egyszerű szolgáltatása is rögzítve van a hozzájáruló felhasználó bérlője számára. [](#service-principal-object)

További [információért tekintse meg](consent-framework.md) a hozzájárulási keretrendszert.

## <a name="id-token"></a>Azonosító jogkivonat

Egy [OpenID Connect][OpenIDConnect-ID-Token] [kiszolgáló](#security-token) engedélyezési végpontja által biztosított biztonsági jogkivonatot, amely a végfelhasználói erőforrás tulajdonosának [hitelesítésére vonatkozó jogcímeket tartalmaz.](#resource-owner) [](#authorization-server) [](#authorization-endpoint) [](#claim) A hozzáférési jogkivonathoz hasonló az azonosító jogkivonatok digitálisan aláírt JSON-webtoken [(JWT) is.][JWT] A hozzáférési jogkivonatokkal ellentétben azonban az azonosító jogkivonat jogcímeit a rendszer nem használja az erőforrás-hozzáféréssel és különösen a hozzáférés-vezérléssel kapcsolatos célokra.

További részletekért tekintse meg a [Microsoft identitásplatform jogkivonatának][AAD-Tokens-Claims] referenciáját.

## <a name="microsoft-identity-platform"></a>Microsoft-identitásplatform

A Microsoft identitásplatformja a Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. Ez egy teljes funkcionalitású platform, amely egy hitelesítési szolgáltatásból, kódtárakból, alkalmazásregisztrációból és -konfigurációból, teljes fejlesztői dokumentációból, kódmintákból és más fejlesztői tartalmakból áll. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet.

## <a name="multi-tenant-application"></a>több-bérlős alkalmazás

Olyan alkalmazásosztály, amely lehetővé [](#consent) teszi a bármely Azure AD-bérlőben üzembe adott felhasználók bejelentkezését és beleegyezését, beleértve az ügyfél regisztrációját nem használó bérlőket is. [](#tenant) [A natív](#native-client) ügyfélalkalmazások alapértelmezés szerint [](#web-client) több-bérlősek, míg a webes ügyfél- és webes [erőforrás-/API-alkalmazások](#resource-server) képesek egy- vagy több-bérlős alkalmazások közül választani. Ezzel szemben az egybérlősként regisztrált webalkalmazások csak olyan felhasználói fiókokból engedélyezik a bejelentkezéseket, amelyek ugyanabban a bérlőben vannak kiépítve, mint az alkalmazás regisztrálva van.

További részletekért lásd: [Azure AD-felhasználók bejelentkezése a több-bérlős][AAD-Multi-Tenant-Overview] alkalmazásminta használatával.

## <a name="native-client"></a>natív ügyfél

Az [eszközre natív](#client-application) módon telepített ügyfélalkalmazás-típus. Mivel minden kód egy eszközön van végrehajtva, "nyilvános" ügyfélnek számít, mert nem tárol bizalmasan/privát módon hitelesítő adatokat. További részletekért [lásd: OAuth2-ügyféltípusok][OAuth2-Client-Types] és -profilok.

## <a name="permissions"></a>engedélyek

Az [ügyfélalkalmazások](#client-application) az [](#resource-server) engedélykérések deklarálása által hozzáférést nyernek egy erőforrás-kiszolgálóhoz. Két típus érhető el:

* A "Delegált" engedélyek, amelyek hatóköralapú hozzáférést határoznak meg [](#resource-owner)a bejelentkezett erőforrás tulajdonosától származó delegált engedélyezéssel, futásidőben ["scp"](#claim) jogcímként jelennek meg az ügyfél hozzáférési [](#scopes) jogkivonatában. [](#access-token)
* Az "Alkalmazás" engedélyek, [](#roles) amelyek az ügyfélalkalmazás hitelesítő adataival/identitásával határozzák meg a szerepköralapú hozzáférést, futásidőben [szerepkörökként](#claim) jelennek meg az erőforrás számára az ügyfél hozzáférési jogkivonatában.

A hozzájárulási [](#consent) folyamat során a rendszergazdák vagy az erőforrás-tulajdonos számára is lehetőséget adva arra, hogy hozzáférést adjanak vagy megtagadják az ügyfél számára a bérlőben található erőforrásokhoz.

Az engedélykérések a [Azure Portal][AZURE-portal]alkalmazásban az **API-engedélyek** oldalán konfigurálhatóak a kívánt "Delegált engedélyek" és "Alkalmazásengedélyek" kiválasztásával (az utóbbihoz a globális rendszergazdai szerepkör tagsága szükséges). Mivel a [nyilvános ügyfelek](#client-application) nem képesek biztonságosan karbantartani a hitelesítő adatokat, csak delegált engedélyeket kérhetnek, [míg](#client-application) a bizalmas ügyfelek delegált és alkalmazásengedélyeket is kérhetnek. Az ügyfél [alkalmazásobjektuma](#application-object) a [requiredResourceAccess][Graph-App-Resource]tulajdonságában tárolja a deklarált engedélyeket.

## <a name="refresh-token"></a>frissítési jogkivonat

Egy engedélyezési kiszolgáló által [](#authorization-server)kiadott biztonsági [jogkivonattípus,](#security-token) amelyet egy ügyfélalkalmazás [](#access-token) használ egy új hozzáférési jogkivonat lekéréséhez a hozzáférési jogkivonat lejárta előtt. [](#client-application) Általában egy új [(JWT JSON-webtoken formájában.][JWT]

A hozzáférési jogkivonatokkal ellentétben a frissítési jogkivonatok visszavonhatóak. Ha egy ügyfélalkalmazás visszavont frissítési jogkivonattal próbál új hozzáférési jogkivonatot igényelni, az engedélyezési kiszolgáló megtagadja a [](#resource-server) kérést, és az ügyfélalkalmazás többé nem fog engedéllyel hozzáférni az erőforrás-kiszolgálóhoz az erőforrás-tulajdonos [nevében.](#resource-owner)

## <a name="resource-owner"></a>erőforrás-tulajdonos

Az [OAuth2][OAuth2-Role-Def]engedélyezési keretrendszere által meghatározottak szerint egy olyan entitás, amely hozzáférést adhat egy védett erőforráshoz. Ha az erőforrás tulajdonosa egy személy, végfelhasználónak nevezzük. Ha például egy [](#client-application) ügyfélalkalmazás hozzá szeretne férni egy felhasználó postaládájához a [Microsoft Graph API-n][Microsoft-Graph]keresztül, akkor engedélyre van szüksége a postaláda erőforrás-tulajdonosától.

## <a name="resource-server"></a>erőforrás-kiszolgáló

Az [OAuth2][OAuth2-Role-Def]engedélyezési keretrendszere által meghatározottak szerint egy olyan kiszolgáló, amely védett erőforrásokat biztosít, és amely képes a hozzáférési jogkivonatot bemutató ügyfélalkalmazások által védett erőforráskérések elfogadására és az ezekre való [válaszadásra.](#access-token) [](#client-application) Más néven védett erőforrás-kiszolgáló vagy erőforrás-alkalmazás.

Az erőforrás-kiszolgáló API-kat biztosít, és az [](#scopes) OAuth [](#roles)2.0 engedélyezési keretrendszer használatával kényszeríti a hozzáférést a védett erőforrásaihoz hatókörök és szerepkörök használatával. Ilyen például a [Microsoft Graph API,][Microsoft-Graph] amely hozzáférést biztosít az Azure AD-bérlő adataihoz, valamint a Microsoft 365 API-k, amelyek hozzáférést biztosítanak az adatokhoz, például a levelezéshez és a naptárhoz.

Az ügyfélalkalmazáshoz hasonló esetben az erőforrás-alkalmazás [](#application-registration) identitáskonfigurációja is egy Azure AD-bérlőben való regisztrációval van megsziva, biztosítva az alkalmazás és a szolgáltatásnév objektumát is. Egyes Microsoft által biztosított API-k, például a Microsoft Graph API, előre regisztrált szolgáltatásnévvel rendelkezik, amelyek minden bérlőben elérhetővé vannak téve a kiépítés során.

## <a name="roles"></a>szerepkörök

A [hatókörökhöz](#scopes)hasonló módon a szerepkörök is lehetőséget biztosítanak az [erőforrás-kiszolgálók számára](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozására. Két típus létezik: a "felhasználói" szerepkör szerepköralapú hozzáférés-vezérlést valósít meg az erőforráshoz hozzáférést igénylő felhasználók/csoportok számára, míg az "alkalmazás" szerepkör ugyanezt a hozzáférést igénylő ügyfélalkalmazások esetében valósítja meg. [](#client-application)

A szerepkörök erőforrás által definiált sztringek (például "Költség jóváhagyó", "Csak olvasható", "Directory.ReadWrite.All"), amelyek az [Azure Portal-ben][AZURE-portal] az erőforrás alkalmazásjegyzékén keresztül vannak tárolva, [](#application-manifest)és az erőforrás [appRoles][Graph-Sp-Resource]tulajdonságában vannak tárolva. A Azure Portal a felhasználók "felhasználói" szerepkörökhöz való hozzárendelésére, [](#permissions) valamint az ügyfélalkalmazások "alkalmazás" szerepkör elérésére vonatkozó engedélyeinek konfigurálásához is használható.

A Microsoft Graph API által elérhetővé Graph API részletes [leírásért][Graph-Perm-Scopes]lásd: . Részletes megvalósítási példa: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása [a Azure Portal.][AAD-RBAC]

## <a name="scopes"></a>hatókörök

A [szerepkörökhöz](#roles)hasonló hatókörök lehetőséget biztosítanak az [erőforrás-kiszolgálók számára](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozására. A hatókörök hatóköralapú [hozzáférés-vezérlés][OAuth2-Access-Token-Scopes] megvalósításához [](#client-application) használhatók olyan ügyfélalkalmazások, amelyekhez a tulajdonosa delegált hozzáférést adott az erőforráshoz.

A hatókörök erőforrás által meghatározott sztringek (például "Mail.Read", "Directory.ReadWrite.All"), amelyek az [](#application-manifest) [Azure Portal-ban][AZURE-portal] vannak az erőforrás alkalmazásjegyzékén keresztül, és az erőforrás [oauth2Permissions][Graph-Sp-Resource]tulajdonságában vannak tárolva. A Azure Portal az ügyfélalkalmazás delegált [](#permissions) engedélyeinek konfigurálásához is használható a hatókörök eléréséhez.

Az ajánlott elnevezési konvenció a "resource.operation.constraint" formátum használata. A Microsoft Graph API által elérhetővé Graph API [részletes leírásért][Graph-Perm-Scopes]lásd: . A Microsoft 365 által elérhetővé Microsoft 365 [API-engedélyekkel kapcsolatos referencia.][O365-Perm-Ref]

## <a name="security-token"></a>biztonsági jogkivonat

Jogcímeket, például OAuth2-jogkivonatot vagy SAML 2.0 helyességi feltételt tartalmazó aláírt dokumentum. OAuth2 engedélyezési [](#authorization-grant)engedély, hozzáférési jogkivonat (OAuth2), [](#refresh-token)frissítési [](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) jogkivonat és azonosító jogkivonat a biztonsági jogkivonatok típusai, amelyek mind JSON-webtoken -ként [(JWT)][JWT]vannak megvalósítva. [](#access-token)

## <a name="service-principal-object"></a>szolgáltatásnév-objektum

Amikor regisztrál/frissít egy alkalmazást a Azure Portal [portál][AZURE-portal]létrehoz/frissít [](#application-object) egy alkalmazásobjektumot és egy megfelelő szolgáltatásnév-objektumot is az adott bérlőhöz. Az alkalmazásobjektum globálisan meghatározza az alkalmazás identitáskonfigurációját (minden olyan bérlőn, amelyhez a társított alkalmazás hozzáférést kapott),  és az a sablon, amelyből a megfelelő szolgáltatásnév-objektum(oka)t a rendszer helyileg, futásidőben (egy adott bérlőben) való helyi használatra származtatja. 

További információ: [Alkalmazás- és szolgáltatásnév-objektumok.][AAD-App-SP-Objects]

## <a name="sign-in"></a>sign-in

A végfelhasználói hitelesítés kezdeményezésének és [a](#client-application) kapcsolódó állapot rögzítésének folyamata egy [](#security-token) biztonsági jogkivonat beszerzése és az alkalmazás-munkamenet adott állapotra való hatókörének meghatározása céljából. Az állapot tartalmazhat olyan összetevőket, mint a felhasználói profil adatai és a jogkivonat jogcímeiből származó információk.

Az alkalmazások bejelentkezési funkcióját általában egyszeri bejelentkezés (SSO) implementálja. Egy "regisztráció" függvény is megelőzheti, amely belépési pontként lehetővé teszi a végfelhasználó számára, hogy hozzáférést szerezzen egy alkalmazáshoz (az első bejelentkezéskor). A sign-up függvény a felhasználóra jellemző további állapotok gyűjtésére és megőrzésére használható, és felhasználói [hozzájárulásra lehet szükség.](#consent)

## <a name="sign-out"></a>kijelentkezés

A végfelhasználó hitelesítésének megszaválása, az ügyfélalkalmazás-munkamenethez [](#client-application) társított felhasználói állapot leválasztása a [bejelentkezés során](#sign-in)

## <a name="tenant"></a>Bérlő

Az Azure AD-címtár egy példányát Azure AD-bérlőnek nevezzük. Számos funkciót biztosít, többek között a következőket:

* egy beállításjegyzék-szolgáltatás integrált alkalmazásokhoz
* felhasználói fiókok és regisztrált alkalmazások hitelesítése
* A különböző protokollok (például az OAuth2 és az SAML) támogatásához szükséges REST-végpontok, beleértve az engedélyezési végpontot, a jogkivonatvégpontot és a több-bérlős alkalmazások által használt "közös" [végpontot.](#multi-tenant-application) [](#authorization-endpoint) [](#token-endpoint)

Az Azure AD-bérlők a regisztráció során létrejönnek/társítva vannak az Azure-ral és Microsoft 365-előfizetésekkel, és identity & Access Management-funkciókat szolgáltatásokat szolgáltatásokat biztosítva az előfizetéshez. Az Azure-előfizetések rendszergazdái további Azure AD-bérlőket is létrehozhatnak a Azure Portal. A [bérlőhöz való hozzáférés különböző módjairól Azure Active Directory][AAD-How-To-Tenant] bérlők lekért adataiért lásd: How to get an Azure Active Directory tenant (Bérlői bérlők lekérte). Az előfizetések és az Azure AD-bérlők közötti kapcsolat részleteiért és az előfizetésEk [Azure AD-bérlőhöz][AAD-How-Subscriptions-Assoc] való társításával vagy az előfizetések Azure AD-bérlőhöz való hozzáadásával kapcsolatos útmutatásért lásd: Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz.

## <a name="token-endpoint"></a>jogkivonat végpontja

Az OAuth2-hitelesítés [](#authorization-server) támogatásához az engedélyezési kiszolgáló által megvalósított [végpontok egyike ad engedélyt.](#authorization-grant) A támogatástól függően használható hozzáférési jogkivonat [(és](#access-token) a kapcsolódó "frissítési" jogkivonat) lekért ügyfélhez, vagy azonosító jogkivonathoz, ha az OpenID Connect [protokollal][OpenIDConnect] használja. [](#client-application) [](#id-token)

## <a name="user-agent-based-client"></a>Felhasználóügynök-alapú ügyfél

Olyan [ügyfélalkalmazás-típus,](#client-application) amely kódot tölt le egy webkiszolgálóról, és egy felhasználói ügynökön (például egy webböngészőn) belül fut, például egy egyoldalas alkalmazáson (SPA). Mivel minden kód egy eszközön van végrehajtva, az "nyilvános" ügyfélnek számít, mivel nem tárol bizalmasan/privát módon hitelesítő adatokat. További információ: [OAuth2-ügyféltípusok és -profilok.][OAuth2-Client-Types]

## <a name="user-principal"></a>egyszerű felhasználó

Ahhoz hasonlóan, ahogyan egy szolgáltatásnév-objektum egy alkalmazáspéldányt képvisel, a rendszerbiztonsági tag egy másik típusa, amely egy felhasználót képvisel. A Microsoft Graph [][Graph-User-Resource] felhasználó erőforrástípus határozza meg a felhasználói objektum sémáját, beleértve a felhasználóhoz kapcsolódó tulajdonságokat, például a vezeték- és vezetéknevet, az egyszerű felhasználónevet, a címtár-szerepkörtagságot stb. Ez biztosítja az Azure AD felhasználói identitáskonfigurációját a felhasználói rendszerbiztonsági tag futásidőben való létrehozására. A rendszerbiztonsági tag egy hitelesített felhasználót képvisel az egyszeri [](#consent) bejelentkezéshez, a hozzájárulás delegálásának rögzítéséhez, hozzáférés-vezérlési döntések meghozatalához stb.

## <a name="web-client"></a>webes ügyfél

Egyfajta [ügyfélalkalmazás,](#client-application) amely minden kódot végrehajt egy webkiszolgálón, és képes "bizalmas" ügyfélként működni azáltal, hogy a hitelesítő adatait biztonságosan tárolja a kiszolgálón. További információ: [OAuth2-ügyféltípusok és -profilok.][OAuth2-Client-Types]

## <a name="next-steps"></a>Következő lépések

A [Microsoft identitásplatform][AAD-Dev-Guide] fejlesztői útmutatója a Microsoft identitásplatform fejlesztésével kapcsolatos összes [][AAD-How-To-Integrate] témakör kezdőlapja, beleértve az alkalmazásintegráció áttekintését, valamint a Microsoft identitásplatform-hitelesítésének alapjait és a támogatott hitelesítési [forgatókönyveket.][AAD-Auth-Scenarios] A [GitHubon](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)& oktatóanyagokban kódmintákat is talál.

A következő megjegyzések szakaszban visszajelzést adhat, és segíthet finomítani és formázni ezt a tartalmat, beleértve az új definíciókra vagy a meglévők frissítésére vonatkozó kéréseket is.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/rfc7519
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
