---
title: Mi az az Azure Active Directory B2C?
description: Ismerje meg, hogyan használhatók a Azure Active Directory B2C a külső identitások támogatásához az alkalmazásokban, beleértve a Facebook, a Google és más identitás-szolgáltatókkal való közösségi bejelentkezést is.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29b34041042f00d72b8a9fe86e31bd80f8c77dc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953950"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az az Azure Active Directory B2C?

Azure Active Directory B2C a vállalat és az ügyfél identitását szolgáltatásként biztosítja. Az ügyfelek az előnyben részesített közösségi, vállalati vagy helyi fiók identitásokat használják az alkalmazások és API-k egyszeri bejelentkezéses hozzáférésének beszerzéséhez.

![A Azure AD B2C Identity Providers és az alsóbb rétegbeli alkalmazások infographic](./media/overview/azureadb2c-overview.png)

Az Azure Active Directory B2C (Azure AD B2C) egy ügyfélidentitás- és hozzáférés-kezelési (CIAM) megoldás, amely naponta több millió felhasználó és több milliárd hitelesítés támogatására képes. Gondoskodik a hitelesítési platform skálázásáról és biztonságáról, a fenyegetések figyeléséről és automatikus kezeléséről, például a szolgáltatásmegtagadási, a jelszó-vagy a találgatásos támadásokról.

## <a name="custom-branded-identity-solution"></a>Egyéni arculatú Identity megoldás

Azure AD B2C egy fehér címkés hitelesítési megoldás. Testre szabhatja a teljes felhasználói élményt a márkával, így zökkenőmentesen keveredik a webes és mobil alkalmazásaival.

Testreszabhatja a Azure AD B2C által megjelenített összes oldalt, amikor a felhasználók regisztrálnak, bejelentkeznek és módosíthatják a profiljuk adatait. Személyre szabhatja a HTML-, CSS-és JavaScript-funkciókat a felhasználói utazásokban, így az Azure AD B2C élmény úgy néz ki és úgy tűnik, mintha az alkalmazás natív része legyen.

![Testreszabott regisztrációs és bejelentkezési lapok és háttérkép](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Egyszeri bejelentkezéses hozzáférés egy felhasználó által megadott identitással

A Azure AD B2C a szabványokon alapuló hitelesítési protokollok, például az OpenID Connect, az OAuth 2,0 és az SAML protokollt használja. Integrálható a legtöbb modern alkalmazással és kereskedelmi forgalomban lévő szoftverrel.

![Harmadik féltől származó identitások ábrája Azure AD B2C egyesítő](./media/overview/scenario-singlesignon.png)

A webalkalmazások, a mobileszközök és az API-k központi hitelesítési szolgáltatóként való kiszolgálásával Azure AD B2C lehetővé teszi az egyszeri bejelentkezési (SSO) megoldások készítését. Központosíthatja a felhasználói profil és a preferencia-információk összegyűjtését, és részletes elemzéseket rögzíthet a bejelentkezési viselkedésről és a regisztrálási konverzióról.

## <a name="integrate-with-external-user-stores"></a>Integrálás külső felhasználói tárolókkal

Azure AD B2C egy olyan könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot tud tárolni. Azonban a külső rendszerekkel is integrálható. Használhatja például a Azure AD B2Ct a hitelesítéshez, de a delegálást külső ügyfélkapcsolat-kezelési (CRM) vagy ügyfél-hűség adatbázisként, az ügyféladatok forrásaként.

Egy másik külső felhasználói tároló forgatókönyve, hogy Azure AD B2C kezeli az alkalmazás hitelesítését, de integrálja egy külső rendszerrel, amely a felhasználói profilt vagy a személyes adatait tárolja. Például az adattárolási követelmények kielégítéséhez, mint a regionális vagy a helyszíni adattárolási szabályzatok.

![A külső felhasználói tárolóval kommunikáló Azure AD B2C logikai diagramja](./media/overview/scenario-remoteprofile.png)

A Azure AD B2C lehetővé teheti az adatok gyűjtését a felhasználótól a regisztráció vagy a profil szerkesztése során, majd az adatok kikapcsolását a külső rendszerre. Ezután a későbbi hitelesítések során Azure AD B2C lekérheti az adatokat a külső rendszerből, és szükség esetén belefoglalhatja a hitelesítési jogkivonat válaszának részeként, amelyet az alkalmazásnak küld.

## <a name="progressive-profiling"></a>Progresszív profilkészítés

Egy másik felhasználói utazási lehetőség is magában foglalja a progresszív profilkészítést. A progresszív profilkészítés lehetővé teszi az ügyfelek számára, hogy az első tranzakciót egy minimális mennyiségű információ begyűjtésével gyorsan elvégezzék. Ezután fokozatosan gyűjtsön további profil-adatokat az ügyféltől a jövőbeli bejelentkezésekhez.

![A progresszív profilkészítés vizuális ábrázolása](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Harmadik féltől származó személyazonosság ellenőrzése és ellenőrzése

A Azure AD B2C használatával megkönnyítheti a felhasználói adatok összegyűjtését, a személyazonosság ellenőrzését és a próbanyomatot, és átadhatja azt egy harmadik féltől származó rendszernek az érvényesítés, a megbízhatósági pontozás és a felhasználói fiókok létrehozásához szükséges jóváhagyás végrehajtásához.

![A harmadik féltől származó személyazonosság-ellenőrzés felhasználói folyamatát ábrázoló diagram](./media/overview/scenario-idproofing.png)

Ez csak néhány dolog, amit a vállalat és az ügyfél identitási platformjának Azure AD B2C tehet. Az Áttekintés következő fejezetei végigvezetik egy Azure AD B2Ct használó bemutató alkalmazáson. A Azure AD B2C további részletes [technikai áttekintését](technical-overview.md)is szívesen fogadjuk.

## <a name="example-woodgrove-groceries"></a>Példa: WoodGrove-élelmiszer

A [WoodGrove élelmiszerboltok][woodgrove] egy, a Microsoft által létrehozott élő webalkalmazás, amely számos Azure ad B2C funkciót mutat be. A következő néhány szakaszban áttekintheti a Azure AD B2C által biztosított hitelesítési beállításokat a WoodGrove webhelyén.

### <a name="business-overview"></a>Üzleti áttekintés

A WoodGrove egy online élelmiszerbolt áruház, amely az egyéni fogyasztóknak és az üzleti ügyfeleknek egyaránt értékesít élelmiszereket. Üzleti ügyfeleink a vállalatuk nevében vagy az általuk felügyelt vállalkozásokban vásárolnak élelmiszereket.

### <a name="sign-in-options"></a>Bejelentkezési beállítások

A WoodGrove élelmiszerboltok számos bejelentkezési lehetőséget biztosítanak az ügyfelek által az áruházhoz fűződő kapcsolat alapján:

* Az **egyes** ügyfelek regisztrálhatnak vagy bejelentkezhetnek az egyes fiókokkal, például egy közösségi identitás-szolgáltatóval vagy egy e-mail-címmel és jelszóval.
* Az **üzleti** ügyfelek regisztrálhatnak és bejelentkezhetnek a vállalati hitelesítő adataikkal.
* A **partnerek** és a szállítók olyan személyek, akik a boltot a termékek eladásával látják el. A partner identitását a [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md)biztosíthatja.

![Egyéni (B2C), üzleti (B2C) és partneri (B2B) bejelentkezési lapok](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Egyéni ügyfelek hitelesítése

Amikor egy ügyfél kiválasztja a **személyes fiókjával való bejelentkezést**, a rendszer átirányítja a Azure ad B2C által üzemeltetett testreszabott bejelentkezési oldalra. A következő képen láthatja, hogy a felhasználói felület (UI) testreszabható a WoodGrove-élelmiszerboltok webhelyéhez hasonlóan. A WoodGrove ügyfelei nem tudják, hogy a hitelesítési élményt a Azure AD B2C üzemelteti és biztosítja.

![Azure AD B2C által üzemeltetett egyéni WoodGrove bejelentkezési oldal](./media/overview/sign-in.png)

A WoodGrove lehetővé teszi, hogy az ügyfelek a Google-, Facebook-vagy Microsoft-fiókjaikat személyazonossági szolgáltatóként regisztrálják és jelentkezzenek be. Vagy regisztrálhatnak az e-mail-címük és a jelszó segítségével, hogy létrehozzák a *helyi fiókokat*.

Amikor egy ügyfél kiválasztja a **személyes fiókjával való regisztrációt** , majd **regisztrálja magát**, egy egyéni regisztrációs oldalon jelennek meg.

![Az Azure AD B2C által üzemeltetett egyéni WoodGrove regisztrációs oldal](./media/overview/sign-up.png)

Miután megadta az e-mail-címet, és kiválasztja az **ellenőrző kód küldése** lehetőséget, Azure ad B2C elküldi őket a kódnak. Miután megtörtént a kód megadása, válassza a **kód ellenőrzése** lehetőséget, majd adja meg a többi információt az űrlapon, továbbá el kell fogadnia a szolgáltatási feltételeket is.

Ha a **Létrehozás** gombra kattint, a Azure ad B2C átirányítja a felhasználót a WoodGrove-élelmiszerboltok webhelyére. Amikor átirányítja, Azure AD B2C egy OpenID Connect hitelesítési tokent továbbít a WoodGrove webalkalmazásnak. A felhasználó most már be van jelentkezve, és készen áll arra, hogy a megjelenített nevük a jobb felső sarokban jelenjen meg, jelezve, hogy be van jelentkezve.

![A WoodGrove élelmiszerboltok webhely fejléce, amelyen a felhasználó be van jelentkezve](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Üzleti ügyfelek hitelesítése

Amikor az ügyfél kiválasztja az egyik lehetőséget az **üzleti ügyfelek** területen, a WoodGrove élelmiszerboltok webhely más Azure ad B2C szabályzatot hív meg, mint az egyes ügyfelek esetében.

Ez a szabályzat bemutatja a felhasználónak, hogy a regisztrációhoz és a bejelentkezéshez a vállalati hitelesítő adatait használja. A WoodGrove-példában a felhasználók a munkahelyi vagy iskolai fiókkal való bejelentkezést kérik. Ez a szabályzat egy [több-bérlős Azure ad-alkalmazást](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) és az `/common` Azure ad-végpontot használja, hogy a világ bármely Microsoft 365 ügyfelével összevonása Azure ad B2C.

### <a name="authenticate-partners"></a>Partnerek hitelesítése

A **Bejelentkezés a beszállítói fiók** hivatkozásával Azure Active Directory B2B's együttműködési funkciót használ. Az Azure AD B2B a Azure Active Directory funkcióinak családja a partneri identitások kezeléséhez. Ezek az identitások összevonhatók Azure Active Directoryból Azure AD B2C által védett alkalmazásokhoz való hozzáféréshez.

További információ az Azure AD B2B-ről: [Mi a vendég felhasználói hozzáférés Azure Active Directory B2B-ben?](../active-directory/external-identities/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Következő lépések

Most, hogy már van egy ötlete arról, hogy milyen Azure AD B2C és milyen forgatókönyvek segíthetnek a működésében, mélyebben megismerheti a funkcióit és a technikai aspektusait.

> [!div class="nextstepaction"]
> [Azure AD B2C technikai áttekintés >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo