---
title: Technikai és funkciók áttekintése – Azure Active Directory B2C
description: A Azure Active Directory B2C szolgáltatásainak és technológiáinak részletes bemutatása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77dab865b2b023f59e6776349b4045107bdb3a29
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095493"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>A Azure Active Directory B2C technikai és funkcióinak áttekintése

Ez a cikk a [Azure Active Directory B2Cával](overview.md)foglalkozó társa, amely részletesebben ismerteti a szolgáltatást. Itt láthatók azok az elsődleges erőforrások, amelyeket a szolgáltatásban, annak szolgáltatásaiban dolgozik, és ezek lehetővé teszik, hogy teljes körű identitást biztosítson ügyfeleinek az alkalmazásaiban.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C bérlő

Azure Active Directory B2C (Azure AD B2C) esetében a *bérlő* képviseli a szervezetét, és a felhasználók címtára. Mindegyik Azure AD B2C-bérlő önálló, és elkülönül a többi Azure AD B2C-bérlőtől. Egy Azure AD B2C bérlő nem más, mint egy Azure Active Directory bérlő, amelyre már rendelkezhet.

A Azure AD B2C-bérlőben használt elsődleges erőforrások a következők:

* **Könyvtár** – a *címtár* , ahol a Azure ad B2C a felhasználók hitelesítő adatait és profiljának adatait, valamint az alkalmazás regisztrációját tárolja.
* **Alkalmazás-regisztráció** – a webes, mobil-és natív alkalmazások regisztrálása Azure ad B2C az Identitáskezelés engedélyezéséhez. Továbbá minden olyan API-t, amelyet a Azure AD B2C védelemmel szeretne ellátni.
* **Felhasználói folyamatok** és **Egyéni házirendek** – a beépített (felhasználói folyamatok) és a teljes mértékben testreszabható (egyéni szabályzatok) identitások az alkalmazásokhoz.
  * *Felhasználói folyamatokat* használhat a gyors konfiguráláshoz és az olyan közös identitású feladatok engedélyezéséhez, mint például a regisztráció, a bejelentkezés és a profil szerkesztése.
  * *Egyéni házirendekkel* engedélyezheti a felhasználói élményt a közös identitási feladatokhoz, hanem a szervezet, az ügyfelek, az alkalmazottak, a partnerek és a polgárok számára egyedi összetett identitás-munkafolyamatok támogatásához is.
* **Identity Providers** – összevonási beállítások a következőhöz:
  * Olyan *közösségi* identitás-szolgáltatók, mint például a Facebook, a LinkedIn vagy a Twitter, amelyet támogatni kíván az alkalmazásaiban.
  * Olyan *külső* identitás-szolgáltatók, amelyek támogatják a szabványos azonosító protokollokat, például a OAuth 2,0, az OpenID Connect és más szolgáltatásokat.
  * *Helyi* fiókok, amelyek lehetővé teszik a felhasználók számára a regisztrációt és bejelentkezést a felhasználónévvel (vagy e-mail-címmel vagy más azonosítóval) és jelszóval.
* **Kulcsok** – titkosítási kulcsok hozzáadása és kezelése a tokenek, az ügyfél titkos kulcsainak, tanúsítványainak és jelszavainak aláírásához és érvényesítéséhez.

A Azure AD B2C bérlő az első olyan erőforrás, amelyet a Azure AD B2C megkezdéséhez létre kell hoznia. Útmutató [: Azure Active Directory B2C-bérlő létrehozása az oktatóanyagban](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Fiókok Azure AD B2C

Azure AD B2C különböző típusú felhasználói fiókokat határoz meg. Azure Active Directory, Azure Active Directory B2B és Azure Active Directory B2C megoszthatja ezeket a fiókokat.

* **Munkahelyi fiók** – a munkahelyi fiókkal rendelkező felhasználók kezelhetik a bérlők erőforrásait, és rendszergazdai szerepkörrel is kezelhetik a bérlőket. A munkahelyi fiókkal rendelkező felhasználók új felhasználói fiókokat hozhatnak létre, jelszavakat állíthatnak vissza, letilthatják vagy letilthatják a fiókokat, valamint engedélyeket állíthatnak be, vagy hozzárendelhet egy fiókot egy biztonsági csoporthoz.
* **Vendég fiók** – a külső felhasználók, akiket vendégként hívnak meg a Bérlőnek. Egy tipikus példa arra, hogy a vendég felhasználókat meghívja a Azure AD B2C bérlőre, hogy megossza az adminisztrációs feladatokat.
* **Fogyasztói fiók** – a fogyasztói fiókok a Azure ad B2C címtárban létrehozott fiókok, amikor a felhasználók a bérlőben regisztrált alkalmazásban teljesítik a regisztrációs felhasználói utat.

![A Azure Portal Azure AD B2C felhasználó-felügyeleti lapja](media/technical-overview/portal-01-users.png)<br/>*Ábra: a Azure AD B2C-bérlőn belüli felhasználói könyvtár a Azure Portal*

### <a name="consumer-accounts"></a>Fogyasztói fiókok

*Fogyasztói* fiókkal a felhasználók bejelentkezhetnek az Azure ad B2C-mel védett alkalmazásokba. A fogyasztói fiókkal rendelkező felhasználók azonban nem férhetnek hozzá az Azure-erőforrásokhoz, például a Azure Portalhoz.

A következő identitási típusokhoz rendelhető fogyasztói fiók:

* **Helyi** identitás a Azure ad B2C könyvtárban helyileg tárolt felhasználónévvel és jelszóval. Gyakran hivatkozunk ezekre az identitásokra "helyi fiókokként".
* **Közösségi** vagy **vállalati** identitások, ahol a felhasználó identitását egy összevont identitás-szolgáltató felügyeli, például Facebook, Microsoft, ADFS vagy Salesforce.

A felhasználói fiókkal rendelkező felhasználók több identitással is bejelentkezhetnek, például Felhasználónév, e-mail, alkalmazotti azonosító, kormányzati azonosító és egyebek. Egyetlen fióknak több identitása is lehet, helyi és közösségi is.

![Fogyasztói fiókok identitásai](media/technical-overview/identities.png)<br/>*Ábra: egyetlen fogyasztói fiók több identitással a Azure AD B2C*

Azure AD B2C lehetővé teszi a fogyasztói fiókok profiljainak, például a megjelenítendő név, a vezetéknév, az utónév, a város és mások közös jellemzőinek kezelését. Az Azure AD-sémát kiterjesztve további információkat is tárolhat a felhasználókról. Például országa/régiója vagy tartózkodási helye, előnyben részesített nyelve és beállításai, például hogy szeretne-e előfizetni egy hírlevélre, vagy engedélyezze a többtényezős hitelesítést.

További információ a felhasználói fiókok típusairól Azure AD B2C a [Azure Active Directory B2C felhasználói fiókjainak áttekintésében](user-overview.md).

## <a name="external-identity-providers"></a>Külső identitásszolgáltatók

A Azure AD B2C konfigurálható úgy, hogy a felhasználók a külső közösségi vagy vállalati identitás-szolgáltatóktól (identitásszolgáltató) származó hitelesítő adatokkal jelentkezzenek be az alkalmazásba. Azure AD B2C támogatja a külső identitás-szolgáltatókat, mint például a Facebook, a Microsoft-fiók, a Google, a Twitter, valamint a OAuth 1,0, OAuth 2,0, OpenID Connect és SAML protokollokat támogató identitás-szolgáltatókat.

![Külső identitásszolgáltatók](media/technical-overview/external-idps.png)

A külső identitás-szolgáltató összevonásával lehetővé teheti a felhasználók számára, hogy a meglévő közösségi vagy vállalati fiókjával jelentkezzenek be anélkül, hogy új fiókot kellene létrehoznia az alkalmazáshoz.

A regisztrációs vagy bejelentkezési oldalon Azure AD B2C megjeleníti azon külső identitás-szolgáltatók listáját, amelyeket a felhasználó kiválaszthat a bejelentkezéshez. Miután kiválasztotta az egyik külső identitás-szolgáltatót, a rendszer a kiválasztott szolgáltató webhelyére helyezi (átirányítja) a bejelentkezési folyamat befejezéséhez. Ha a felhasználó sikeresen bejelentkezett, a rendszer visszaküldi Azure AD B2C az alkalmazásban lévő fiók hitelesítéséhez.

![Mobil bejelentkezési példa közösségi fiókkal (Facebook)](media/technical-overview/external-idp.png)

Ha szeretné megtekinteni, hogyan adhat hozzá identitás-szolgáltatókat a Azure AD B2Cban, tekintse [meg az identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](add-identity-provider.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Személyazonossági tapasztalatok: felhasználói folyamatok vagy egyéni házirendek

A Azure AD B2C bővíthető házirend-keretrendszere a fő erőssége. A szabályzatok leírják a felhasználók személyazonosságának élményét, például a regisztrációt, a bejelentkezést és a profil szerkesztését.

Azure AD B2C két elsődleges elérési út áll rendelkezésre, amelyek segítségével megadhatja ezeket az identitási tapasztalatokat: felhasználói folyamatok és egyéni házirendek.

* A **felhasználói folyamatok** előre definiált, beépített, konfigurálható szabályzatokat biztosítanak, így percek alatt létrehozhatók a regisztráció, a bejelentkezés és a házirend-szerkesztési élmény.

* Az **Egyéni szabályzatok** lehetővé teszik, hogy saját felhasználói utazásokat hozzon létre az összetett identitási élményhez.

A felhasználói folyamatokat és az egyéni házirendeket a *személyazonossági élmény keretrendszere*, az Azure ad B2C's házirend-kezelő motorja látja el.

### <a name="user-flow"></a>Felhasználói folyamat

A leggyakoribb identitás-feladatok gyors beállításához a Azure Portal számos előre definiált és konfigurálható szabályzatot tartalmaz, amelyeket *felhasználói folyamatoknak* nevezünk.

A felhasználói folyamatok beállításait, például az alábbi beállításokkal konfigurálhatja az identitások viselkedését az alkalmazásokban:

* A bejelentkezéshez használt fióktípus, például olyan közösségi fiókok, mint például a Facebook vagy a bejelentkezéshez e-mail-címet és jelszót használó helyi fiókok
* A fogyasztótól begyűjtött attribútumok, például Utónév, postai kód vagy lakhely szerinti ország/régió
* Azure AD-Multi-Factor Authentication (MFA)
* A felhasználói felület testreszabása
* Jogcímek készlete egy jogkivonatban, amelyet az alkalmazás akkor kap, miután a felhasználó befejezte a felhasználói folyamatot
* Munkamenet-kezelés
* ... és még sok minden más.

A mobil-, web-és egyoldalas alkalmazások többségének leggyakoribb identitási forgatókönyvei hatékonyan meghatározhatók és alkalmazhatók a felhasználói folyamatokkal. Javasoljuk, hogy a beépített felhasználói folyamatokat csak akkor használja, ha olyan összetett felhasználói utazási forgatókönyvekkel rendelkezik, amelyek az egyéni házirendek teljes rugalmasságát igénylik.

További információ a [Azure Active Directory B2C felhasználói folyamatainak](user-flow-overview.md)felhasználói folyamatairól.

### <a name="custom-policy"></a>Egyéni szabályzat

Egyéni szabályzatok az Identity Experience Framework-(IEF-) összehangoló motor teljes teljesítményéhez való hozzáférés feloldására szolgálnak. Az egyéni szabályzatok segítségével szinte bármilyen hitelesítést, felhasználói regisztrációt vagy profil-szerkesztési folyamatot hozhat létre a IEF használatával.

Az identitási élmény keretrendszer lehetővé teszi a felhasználói útvonalak összeállítását a lépések tetszőleges kombinációjával. Például:

* Összevonása más identitás-szolgáltatókkal
* Az első és a harmadik féltől származó többtényezős hitelesítés (MFA) kihívásai
* Bármilyen felhasználói bevitel összegyűjtése
* Integráció külső rendszerekkel REST API kommunikáció használatával

Az egyes felhasználói útvonalakat szabályzatok határozzák meg, és tetszőleges számú szabályzatot építhet ki, amelyet a szervezete legjobb felhasználói élményének engedélyezéséhez kell létrehoznia.

![A IEF által engedélyezett összetett felhasználói út példáját bemutató ábra](media/technical-overview/custom-policy.png)

Az egyéni házirendeket több XML-fájl határozza meg, amelyek egy hierarchikus láncban egymásra hivatkoznak. Az XML-elemek meghatározzák a jogcímek sémáját, a jogcímek átalakítását, a tartalmi definíciókat, a jogcímeket, a technikai profilokat, a felhasználói útvonalak előkészítésének lépéseit és az identitással kapcsolatos egyéb szempontokat

Az egyéni szabályzatok nagy rugalmassága a legmegfelelőbb, ha összetett identitás-forgatókönyveket kell létrehoznia. Az egyéni házirendeket konfiguráló fejlesztőknek alapos részletességgel kell megadniuk a megbízható kapcsolatokat, hogy tartalmazzák a metaadatok végpontját, a pontos jogcím-definíciókat, valamint a titkok, kulcsok és tanúsítványok konfigurálását az egyes identitás-szolgáltatók igényei szerint.

További információ az egyéni házirendekről [Azure Active Directory B2Cban](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokollok és tokenek

- Alkalmazások esetén a Azure AD B2C támogatja a [OAuth 2,0](protocols-overview.md), [OpenID Connect](openid-connect.md)és [SAML protokollt](saml-service-provider.md) a felhasználói útvonalakhoz. Az alkalmazás megkezdi a felhasználói utazást a Azure AD B2C felé irányuló hitelesítési kérések kiállításával. Az Azure AD B2Cre irányuló kérelem eredménye biztonsági jogkivonat, például [azonosító token, hozzáférési jogkivonat](tokens-overview.md)vagy SAML-jogkivonat. Ez a biztonsági jogkivonat határozza meg a felhasználó identitását az alkalmazáson belül.

- Külső identitások esetén a Azure AD B2C támogatja a OAuth 1,0, OAuth 2,0, OpenID Connect és SAML Identity szolgáltatók összevonását.

Az alábbi ábra azt mutatja be, hogy a Azure AD B2C hogyan tud kommunikálni különböző protokollok használatával ugyanazon a hitelesítési folyamaton belül:

![Az SAML-alapú identitásszolgáltató rendelkező, OIDC-alapú ügyfélalkalmazások egyesítő ábrája](media/technical-overview/protocols.png)

1. A függő entitás alkalmazás a Azure AD B2C OpenID Connect használatával kezdeményezi az engedélyezési kérelmet.
1. Ha az alkalmazás egy felhasználója úgy dönt, hogy egy SAML protokollt használó külső Identitáskezelő használatával jelentkezik be, Azure AD B2C az SAML protokollt hívja meg az identitás-szolgáltatóval való kommunikációra.
1. Miután a felhasználó befejezte a bejelentkezési műveletet a külső identitás-szolgáltatóval, Azure AD B2C ezt követően visszaadja a tokent a függő entitás alkalmazásnak az OpenID Connect használatával.

## <a name="application-integration"></a>Alkalmazásintegráció

Ha egy felhasználó be szeretne jelentkezni az alkalmazásba, legyen szó webes, mobil-, asztali vagy egyoldalas alkalmazásról (SPA), akkor az alkalmazás egy felhasználói folyamathoz vagy egyéni házirend által megadott végponthoz kezdeményezi az engedélyezési kérelmet. A felhasználói folyamat vagy az egyéni házirend határozza meg és szabályozza a felhasználó élményét. Amikor elvégeznek egy felhasználói folyamatot, például a *regisztrálási vagy bejelentkezési* folyamatot, Azure ad B2C létrehoz egy jogkivonatot, majd visszairányítja a felhasználót az alkalmazáshoz.

![Mobil alkalmazás Azure AD B2C bejelentkezési oldal közötti folyamattal](media/technical-overview/app-integration.png)

Több alkalmazás is használhatja ugyanazt a felhasználói folyamatot vagy egyéni házirendet. Egyetlen alkalmazás több felhasználói folyamatot vagy egyéni szabályzatot is használhat.

Ha például be szeretne jelentkezni egy alkalmazásba, az alkalmazás a *regisztrációs vagy bejelentkezési* felhasználói folyamatot használja. Miután a felhasználó bejelentkezett, érdemes lehet szerkeszteni a profilt, így az alkalmazás egy másik engedélyezési kérelmet indít el, ezúttal a felhasználói folyamat *szerkesztése* lehetőséget használva.

## <a name="seamless-user-experiences"></a>Zökkenőmentes felhasználói élmény

Azure AD B2C a felhasználók személyazonosságának élményét is elvégezheti, hogy a megjelenített lapok zökkenőmentesen jelenjenek meg a márka megjelenésével és működésével. Szinte teljes körűen vezérelheti a felhasználók számára megjelenített HTML-és CSS-tartalmakat, amikor azok az alkalmazás személyazonossági útvonalán haladnak át. Ezzel a rugalmassággal megőrizheti az alkalmazás és a Azure AD B2C között a márka és a vizualizáció egységességét.

![A Brand-alapú regisztrációs bejelentkezési oldal képernyőképei](media/technical-overview/seamless-ux.png)

A felhasználói felület testreszabásával kapcsolatos információkért lásd: [Tudnivalók a felhasználói felület testreszabásáról a Azure Active Directory B2C](customize-ui-with-html.md).

## <a name="localization"></a>Honosítás

A Azure AD B2C nyelvi testreszabása lehetővé teszi, hogy az ügyfelek igényeinek megfelelően különböző nyelveket is kielégítse. A Microsoft a 36 nyelvekhez biztosít fordításokat, de bármilyen nyelven saját fordításokat is megadhat. Ha a felhasználói élmény csak egyetlen nyelven érhető el, testreszabhatja a lapokon található bármely szöveget.

![Három regisztrációs bejelentkezési oldal, amely a felhasználói felület szövegét jeleníti meg különböző nyelveken](media/technical-overview/localization.png)

Megtudhatja, hogyan működik a honosítás a [nyelvi testreszabásban Azure Active Directory B2Cban](language-customization.md).

## <a name="add-your-own-business-logic"></a>Saját üzleti logika hozzáadása

Ha egyéni szabályzatokat szeretne használni, a felhasználói úton található REST API-val integrálhatja a saját üzleti logikáját az utazáshoz. A Azure AD B2C például átcserélhetik az adatok REST-szolgáltatással:

* Egyéni felhasználóbarát hibaüzenetek megjelenítése.
* Érvényesítse a felhasználói adatokat, hogy megakadályozza a helytelenül formázott adatok megőrzését a felhasználói címtárban. Módosíthatja például a felhasználó által megadott adatbevitelt, például kihasználhatja az első nevüket, ha az összes kisbetűs mezőben szerepel.
* Gazdagítsa a felhasználói adatait a vállalati üzletági alkalmazással való további integrálással.
* A REST-alapú hívások használatával leküldéses értesítéseket küldhet, vállalati adatbázisokat frissíthet, felhasználói áttelepítési folyamatot futtathat, kezelheti az engedélyeket, a naplózási adatbázisokat stb.

A hűségprogramok a REST API-k meghívására szolgáló Azure AD-B2C's támogatásának egy másik forgatókönyve. A REST-szolgáltatás például fogadhatja a felhasználó e-mail-címét, lekérdezheti az ügyfél adatbázisát, majd visszaküldheti a felhasználó hűségi számát Azure AD B2C. A visszaadott információ a felhasználó címtárbeli fiókjában tárolható Azure AD B2Cban, majd a házirend későbbi lépéseiben, illetve a hozzáférési jogkivonatban is megtalálható.

![Üzletági integráció egy mobil alkalmazásban](media/technical-overview/lob-integration.png)

Az egyéni házirend által definiált felhasználói út bármely lépésében hozzáadhat egy REST API hívást. Meghívhat például egy REST API:

* Bejelentkezéskor közvetlenül a Azure AD B2C érvényesíti a hitelesítő adatokat
* Közvetlenül a bejelentkezés után
* Mielőtt a Azure AD B2C új fiókot hoz létre a címtárban
* Miután Azure AD B2C létrehoz egy új fiókot a címtárban
* Mielőtt Azure AD B2C kiadja a hozzáférési jogkivonatot

Ha szeretné megtudni, hogyan használhatók az egyéni házirendek a REST API-integrációhoz a Azure AD B2Cban, tekintse [meg a REST API jogcím-csere integrálása a Azure ad B2C egyéni házirendben](custom-policy-rest-api-intro.md)című témakört

## <a name="protect-customer-identities"></a>Ügyfelek identitásának biztosítása

A Azure AD B2C megfelel a [Microsoft Azure Adatvédelmi központban](https://www.microsoft.com/trustcenter/cloudservices/azure)leírt biztonsági, adatvédelmi és egyéb kötelezettségeknek.

A munkamenetek titkosított adatként vannak modellezve, és a visszafejtési kulcs csak a Azure AD B2C biztonsági jogkivonat szolgáltatás számára ismert. Az AES-192 erős titkosítási algoritmust használ. A kommunikációs útvonalak védelme a TLS protokollal történik a titkosítás és az integritás érdekében. A biztonsági jogkivonat szolgáltatás kiterjesztett ellenőrzési (EV) tanúsítványt használ a TLS-hez. Általánosságban elmondható, hogy a biztonsági jogkivonat szolgáltatás a nem megbízható bemenetek megjelenítésével csökkenti a helyközi parancsfájlok (XSS) elleni támadásokat.

![Az átvitel és a nyugalmi állapotban lévő biztonságos adatok diagramja](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Hozzáférés a felhasználói adatbázisokhoz

Azure AD B2C bérlők számos jellemzővel rendelkeznek, amelyek az alkalmazottak és a partnerek számára használt vállalati Azure Active Directory Bérlővel rendelkeznek. A közös aspektusok közé tartoznak a felügyeleti szerepkörök megtekintésére, a szerepkörök hozzárendelésére és a naplózási tevékenységekre vonatkozó mechanizmusok.

Szerepkörök hozzárendelésével szabályozhatja, hogy ki végezhet el bizonyos felügyeleti műveleteket a Azure AD B2Cban, beleértve a következőket:

* A felhasználói folyamatok összes aspektusának létrehozása és kezelése
* Az összes felhasználói folyamat számára elérhető attribútum-séma létrehozása és kezelése
* Identitás-szolgáltatók konfigurálása közvetlen összevonás esetén való használatra
* Megbízhatósági keretrendszer-szabályzatok létrehozása és kezelése az Identity Experience Framework-ben (egyéni szabályzatok)
* Az összevonási és titkosítási titkok kezelése az identitás-kezelési keretrendszerben (egyéni szabályzatok)

További információ az Azure AD-szerepkörökről, többek között a Azure AD B2C adminisztrációs szerepkör támogatásáról: [rendszergazdai szerepkör engedélyei Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-factor authentication (MFA)

Azure AD B2C a többtényezős hitelesítés (MFA) segíti az adat-és alkalmazásokhoz való hozzáférés védelmét, miközben a felhasználók számára egyszerűséget biztosít. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést biztosít a könnyen használható hitelesítési módszerek széles skáláját kínálva. A felhasználók a rendszergazdaként felhasználható konfigurációs döntések alapján esetleg nem vitatják meg az MFA-t.

Lásd: az MFA engedélyezése a felhasználói folyamatokban a [többtényezős hitelesítés engedélyezése Azure Active Directory B2Cban](multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Intelligens fiók zárolása

Ha meg szeretné akadályozni a találgatásos támadási kísérleteket, a Azure AD B2C kifinomult stratégia használatával zárolja a fiókokat a kérés IP-címe, a megadott jelszavak és számos más tényező alapján. A zárolás időtartama a kockázat és a kísérletek száma alapján automatikusan növekszik.

![Fiók intelligens zárolása](media/technical-overview/smart-lockout1.png)

A jelszavas védelem beállításainak kezelésével kapcsolatos további információkért lásd: [veszélyforrások kezelése az erőforrásokra és az adatokra Azure Active Directory B2Cban](threat-management.md).

### <a name="password-complexity"></a>Jelszó bonyolultsága

A regisztráció vagy a jelszó alaphelyzetbe állítása során a felhasználóknak meg kell adnia egy jelszót, amely megfelel a bonyolultsági szabályoknak. Alapértelmezés szerint a Azure AD B2C kényszeríti az erős jelszóházirend alkalmazását. A Azure AD B2C konfigurációs beállításokat is biztosít az ügyfelek által használt jelszavak összetettségi követelményeinek megadásához.

A jelszó bonyolultságára vonatkozó követelményeket a [felhasználói folyamatokban](password-complexity.md) és az [Egyéni házirendekben](password-complexity.md)is konfigurálhatja.

## <a name="auditing-and-logs"></a>Naplózás és naplók

A Azure AD B2C az erőforrásaival, a kiadott jogkivonatokkal és a rendszergazdai hozzáféréssel kapcsolatos tevékenységeket tartalmazó naplókat bocsát ki. Ezekkel a naplókkal megismerheti a platform tevékenységeit és diagnosztizálhatja a problémákat. A naplózási naplóbejegyzések az eseményt generáló tevékenység után hamarosan elérhetők.

A Azure AD B2C bérlőhöz vagy egy adott felhasználóhoz elérhető naplózási naplóban többek között a következőkre vonatkozó információkat találhat:

* A B2C-erőforrások elérésére irányuló felhasználók engedélyezésével kapcsolatos tevékenységek (például egy, a B2C-szabályzatok listáját elérő rendszergazda)
* Címtár-attribútumokkal kapcsolatos tevékenységek, amelyek akkor jelentkeznek le, amikor egy rendszergazda bejelentkezik a Azure Portal használatával
* A B2C-alkalmazásokhoz tartozó létrehozási, olvasási, frissítési és törlési (szifilisz-) műveletek
* A B2C-kulcstárolóban tárolt kulcsokra vonatkozó szifilisz-műveletek
* A B2C-erőforrásokkal kapcsolatos szifilisz-műveletek (például házirendek és identitás-szolgáltatók)
* A felhasználói hitelesítő adatok és a jogkivonatok kiállításának érvényesítése

![A Azure Portalban látható egyéni felhasználói napló](media/technical-overview/audit-log.png)

További részletek a naplókról: [Azure ad B2C naplók elérése](view-audit-logs.md).

### <a name="usage-insights"></a>Használati adatok

Azure AD B2C segítségével felfedezheti, hogy mikor regisztrálhatnak vagy jelentkezhetnek be a webalkalmazásba, hol találhatók a felhasználók, és milyen böngészőket és operációs rendszereket használnak. Ha egyéni szabályzatokkal integrálja az Azure Application Insightst a Azure AD B2Cba, betekintést nyerhet a felhasználók regisztrálására, a bejelentkezésre, a jelszavuk visszaállítására vagy a profil szerkesztésére. Ilyen ismerettel a közelgő fejlesztési ciklusok esetében adatvezérelt döntéseket hozhat.

További információ a használati elemzésekről: a [felhasználói viselkedés nyomon követése Azure Active Directory B2C a Application Insights használatával](analytics-with-application-insights.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy mélyebben megtekintette a Azure Active Directory B2C funkcióit és technikai aspektusait, ismerkedjen meg a [Azure Active Directory B2C-bérlő létrehozásához szükséges oktatóanyaggal](tutorial-create-tenant.md).
