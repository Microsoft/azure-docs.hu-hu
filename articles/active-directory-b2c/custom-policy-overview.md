---
title: Az egyéni szabályzatok Azure Active Directory B2C áttekintése | Microsoft Docs
description: A Azure Active Directory B2C egyéni szabályzatokkal és az identitás-élmény keretrendszerével kapcsolatos témakörök.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a8aabac6960909f2a3d90fcee01cebb0ad7a832
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256939"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Egyéni szabályzatok Azure AD B2C áttekintése

Az egyéni szabályzatok olyan konfigurációs fájlok, amelyekkel meghatározható az Azure Active Directory B2C- (Azure AD B2C-) bérlő viselkedése. Habár a [felhasználói folyamatok](user-flow-overview.md) előre vannak meghatározva a Azure ad B2C-portálon a leggyakoribb identitási feladatok elvégzéséhez, az egyéni szabályzatok teljes mértékben szerkeszthetők egy identitás-fejlesztővel, és számos különböző feladatot végezhetnek el.

Az egyéni szabályzatok teljes mértékben konfigurálhatók és házirend-vezéreltek. Az egyéni szabályzatok az entitások közötti megbízhatóságot hangolják össze a standard protokollokban. Például az OpenID Connect, a OAuth, az SAML és néhány nem standard, például REST API-alapú rendszer-rendszerbeli jogcímek cseréje. A keretrendszer felhasználóbarát, fehér címkével ellátott tapasztalatokat hoz létre.

Az egyéni szabályzatok egy vagy több XML-formátumú fájlként jelennek meg, amelyek egy hierarchikus láncban egymásra hivatkoznak. Az XML-elemek határozzák meg az építőelemeket, a felhasználóval való interakciót és a többi felet, valamint az üzleti logikát. 

## <a name="custom-policy-starter-pack"></a>Egyéni házirend alapszintű csomagja

Azure AD B2C a Custom Policy [Starter Pack](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) számos előre elkészített szabályzattal rendelkezik, amelyekkel gyorsan elvégezheti a munkát. Az alapszintű csomagok mindegyike a leírt forgatókönyvek eléréséhez szükséges technikai profilok és felhasználói utazások legkisebb számát tartalmazza:

- **LocalAccounts** – csak a helyi fiókok használatát engedélyezi.
- **SocialAccounts** – engedélyezi a közösségi (vagy összevont) fiókok használatát.
- **SocialAndLocalAccounts** – lehetővé teszi mind a helyi, mind a közösségi fiókok használatát. A minták többsége erre a szabályzatra hivatkozik.
- **SocialAndLocalAccountsWithMFA** – lehetővé teszi a közösségi, a helyi és a többtényezős hitelesítési lehetőségek használatát.

## <a name="understanding-the-basics"></a>Az alapvető tudnivalók 

### <a name="claims"></a>Jogcímek

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. Tárolhatja a felhasználóval kapcsolatos információkat, például a vezetéknevet, a vezetéknevet vagy a felhasználótól vagy más rendszerekből beszerzett egyéb jogcímeket (jogcím-cserék). A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. 

A házirend futtatásakor Azure AD B2C a belső és külső felektől érkező és onnan érkező jogcímeket küld és fogad, majd a jogcímek egy részhalmazát küldi el a függő entitás alkalmazásának a jogkivonat részeként. A jogcímek használata a következő módokon történik: 

- A rendszer a címtár felhasználói objektumán menti, olvassa vagy frissíti a jogcímeket.
- A rendszer egy külső személyazonossági szolgáltatótól kapott jogcímet.
- A jogcímek küldése vagy fogadása egy egyéni REST API szolgáltatás használatával történik.
- A rendszer az adatokat a felhasználó által a regisztrálás vagy a profil szerkesztése folyamat során kapott jogcímekként gyűjti.

### <a name="manipulating-your-claims"></a>A jogcímek módosítása

A [jogcím-átalakítások](claimstransformations.md) előre definiált függvények, amelyek segítségével egy adott jogcím átalakítható egy másikra, kiértékelheti a jogcímeket, vagy beállíthatja a jogcím értékét. Például hozzáadhat egy elemeket egy karakterlánc-gyűjteményhez, megváltoztathatja a karakterláncok esetét, vagy kiértékelheti a dátumot és az időt. A jogcím-átalakítás egy átalakítási metódust határoz meg. 

### <a name="customize-and-localize-your-ui"></a>Felhasználói felület testreszabása és honosítása

Ha adatokat szeretne gyűjteni a felhasználóktól, ha egy oldalt mutat be a böngészőben, használja az [önjelölt technikai profilt](self-asserted-technical-profile.md). A saját maga által vezérelt technikai profilt szerkesztheti [jogcímek hozzáadásához és a felhasználói bevitel testreszabásához](./configure-user-input.md).

Az önérvényesített technikai profil [felhasználói felületének testreszabásához](customize-ui-with-html.md) megadhat egy URL-címet a tartalom- [definíciós](contentdefinitions.md) elemben testreszabott HTML-tartalommal. Az önérvényesített technikai profilban erre a tartalom-definíciós AZONOSÍTÓra mutat.

A nyelvspecifikus karakterláncok testreszabásához használja a [honosítási](localization.md) elemet. A tartalom definíciója olyan [honosítási](localization.md) referenciát tartalmazhat, amely a betöltendő honosított erőforrások listáját határozza meg. Azure AD B2C egyesíti a felhasználói felület elemeit az URL-címről betöltött HTML-tartalommal, majd megjeleníti a lapot a felhasználónak. 

## <a name="relying-party-policy-overview"></a>Függő entitások házirendjének áttekintése

Egy függő entitás alkalmazása, amely az SAML protokollban szolgáltatóként ismert, meghívja a függő [entitás házirendjét](relyingparty.md) egy adott felhasználói út végrehajtásához. A függő entitás házirendje meghatározza a végrehajtandó felhasználói elérési utat, és felsorolja a jogkivonat által biztosított jogcímeket. 

![A házirend végrehajtási folyamatát ábrázoló diagram](./media/custom-policy-overview/custom-policy-execution.png)

Minden olyan függő entitás, amely ugyanazt a házirendet használja, ugyanazokat a jogcímeket fogja fogadni, és a felhasználó ugyanazon a felhasználói úton halad.

### <a name="user-journeys"></a>Felhasználói utazások

A [felhasználói útvonalak](userjourneys.md) lehetővé teszik, hogy meghatározza az üzleti logikát az elérési úttal, amelyen keresztül a felhasználó az alkalmazáshoz való hozzáférést fogja követni. A felhasználó az alkalmazáshoz benyújtandó jogcímek beolvasásához a felhasználói úton történik. A felhasználói út előkészítési [lépésekből](userjourneys.md#orchestrationsteps)épül fel. A felhasználónak el kell érnie az utolsó lépést a jogkivonat beszerzéséhez. 

Az alábbi utasítások azt írják le, hogyan adhat hozzá előkészítési lépéseket a [közösségi és a helyi fiók Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) házirendjéhez. Íme egy példa egy REST API-hívásra, amely hozzá lett adva.

![testreszabott felhasználói út](media/custom-policy-overview/user-journey-flow.png)


### <a name="orchestration-steps"></a>Az előkészítés lépései

A koordinációs lépés olyan metódusra hivatkozik, amely megvalósítja a kívánt célt vagy funkciót. Ezt a módszert [technikai profilnak](technicalprofiles.md)nevezzük. Ha a felhasználói utazás az üzleti logikát jobban reprezentáló elágazási igényeket igényli, a koordinálási lépés az [alárendelt útra](subjourneys.md)hivatkozik. A sub Journey a saját előkészítési lépéseit tartalmazza.

A felhasználónak el kell érnie a felhasználói út utolsó előkészítési lépését a token beszerzéséhez. Előfordulhat azonban, hogy a felhasználóknak nem kell átutazniuk az összes előkészítési lépéssel. A koordináló lépések feltételesen hajthatók végre a előkészítési lépésben meghatározott [Előfeltételek](userjourneys.md#preconditions) alapján. 

Egy előkészítési lépés befejezése után a Azure AD B2C a **jogcímek táskájában** tárolja a kiállított jogcímeket. A jogcímek táskájában lévő jogcímeket a felhasználói út bármely további előkészítési lépése használhatja.

Az alábbi ábra bemutatja, hogyan érheti el a felhasználói út előkészítési lépései a jogcímek táskáját.

![Felhasználói út Azure AD B2C](media/custom-policy-overview/user-journey-diagram.png)

### <a name="technical-profile"></a>Technikai profil

A technikai profil egy felületet biztosít a különböző típusú felekkel való kommunikációhoz. A felhasználói út a technikai profilok meghívását az üzleti logikájának meghatározására szolgáló lépések segítségével ötvözi.

A műszaki profilok minden típusa ugyanazzal a fogalommal rendelkezik. A bemeneti jogcímeket, a jogcímek átalakítását és a konfigurált féllel folytatott kommunikációt kell elküldenie. A folyamat elvégzése után a technikai profil visszaadja a jogcím-táska kimeneti jogcímeit. További információ: [technikai profilok – áttekintés](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Ellenőrzési technikai profil

Ha a felhasználó kommunikál a felhasználói felülettel, érdemes lehet érvényesíteni az összegyűjtött adatokat. A felhasználóval folytatott kommunikációhoz [saját maga által vezérelt technikai profilt](self-asserted-technical-profile.md) kell használni.

A felhasználói bevitel ellenőrzéséhez egy [érvényesítési műszaki profilt](validation-technical-profile.md) kell meghívni az önérvényesített technikai profilból. Az érvényesítési technikai profil bármely olyan metódus, amely nem interaktív technikai profilt hív meg. Ebben az esetben a technikai profil kimeneti jogcímeket vagy hibaüzenetet adhat vissza. A hibaüzenet a felhasználó képernyőjén jelenik meg, így a felhasználó újra próbálkozik.

Az alábbi ábra azt szemlélteti, hogyan használja a Azure AD B2C egy érvényesítési technikai profilt a felhasználói hitelesítő adatok ellenőrzéséhez.

![Ellenőrzési technikai profil diagramja](media/custom-policy-overview/validation-technical-profile.png)

## <a name="inheritance-model"></a>Öröklési modell

Minden kezdő csomag a következő fájlokat tartalmazza:

- A definíciók többségét tartalmazó **alapfájl.** A házirendek hibaelhárításával és hosszú távú karbantartásával kapcsolatos segítségért próbálja meg csökkenteni a fájl módosításainak számát.
- Egy **kiterjesztési** fájl, amely a bérlő egyedi konfigurációs módosításait tárolja. Ez a házirend-fájl az alapfájlból származik. Ezzel a fájllal új funkciókat adhat hozzá, vagy felülbírálhatja a meglévő funkciókat. Használja például ezt a fájlt, hogy összevonása az új identitás-szolgáltatókkal.
- Egy **függő entitást (RP)** tartalmazó fájl, amely egy, a függő entitás alkalmazás által közvetlenül meghívott, egyetlen feladat által irányított fájl, például a web-, mobil-vagy asztali alkalmazások. Az egyes egyedi feladatok, például a regisztráció, a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése a saját függő entitások házirendjének fájlját igénylik. Ez a házirendfájl a kiterjesztések fájljából származik.

Az öröklési modell a következő:

- A gyermek házirend bármilyen szinten örökölhető a szülő házirendtől, és új elemek hozzáadásával bővíthető.
- Összetettebb forgatókönyvek esetén több öröklési szintet is hozzáadhat (összesen legfeljebb 10).
- További függő entitásokra vonatkozó szabályzatokat adhat hozzá. Például törölje a saját fiókját, változtassa meg a telefonszámot, az SAML-függő entitások házirendjét és egyebeket.

Az alábbi ábrán a házirend-fájlok és a függő entitások alkalmazásai közötti kapcsolat látható.

![A megbízhatósági keretrendszer házirendjének öröklési modelljét bemutató ábra](media/custom-policy-overview/policies.png)


## <a name="guidance-and-best-practices"></a>Útmutatás és ajánlott eljárások

### <a name="best-practices"></a>Ajánlott eljárások

Egy Azure AD B2C egyéni szabályzaton belül integrálhatja saját üzleti logikáját, hogy felépítse a felhasználói élményt, és bővítse a szolgáltatás funkcióit. Az első lépésekhez az ajánlott eljárások és javaslatok állnak rendelkezésére.

- Hozza létre a logikát a **bővítmény házirendjében** vagy a **függő entitások házirendjében**. Hozzáadhat új elemeket is, amelyek felülírják az alapházirendet ugyanarra az AZONOSÍTÓra hivatkozva. Ez lehetővé teszi a projekt vertikális felskálázását, miközben a Microsoft új kezdő csomagokat bocsát ki.
- Az **alapházirenden** belül kifejezetten javasoljuk, hogy elkerülje a módosítások elvégzését. Ha szükséges, megjegyzéseket fűzhet a módosítások elvégzéséhez.
- Ha felülbírál egy elemet, például a technikai profil metaadatait, ne másolja a teljes technikai profilt az alapszabályzatból. Ehelyett csak az elem szükséges szakaszát másolja. A módosítás elvégzéséhez tekintse meg az [e-mailek ellenőrzésének letiltása](./disable-email-verification.md) című témakört.
- Ha csökkenteni szeretné a technikai profilok ismétlődését, ahol a központi funkciók megoszlik, használja a [technikai profilokat](technicalprofiles.md#include-technical-profile).
- A bejelentkezés során Kerülje az Azure AD-címtárba való írást, ami a problémák szabályozásához vezethet.
- Ha a házirend külső függőségekkel rendelkezik (például REST API-k), akkor győződjön meg arról, hogy a szabályzat nagyon elérhető.
- A jobb felhasználói élmény érdekében győződjön meg arról, hogy az egyéni HTML-sablonok globálisan üzembe helyezhetők az [online tartalom-továbbítás](../cdn/index.yml)használatával. Az Azure Content Delivery Network (CDN) segítségével csökkentheti a betöltési időt, sávszélességet takaríthat meg, és javíthatja a válaszadás sebességét.
- Ha módosítani szeretné a felhasználói utazást, másolja a teljes felhasználói utat az alapszabályzatból a kiterjesztési szabályzatba. Adjon meg egyedi felhasználói útvonal-azonosítót a másolt felhasználói útra. Ezután a [függő entitás házirendjében](relyingparty.md)módosítsa az [alapértelmezett felhasználói útvonal](relyingparty.md#defaultuserjourney) elemet úgy, hogy az új felhasználói útra mutasson.

## <a name="troubleshooting"></a>Hibaelhárítás

Azure AD B2C házirendekkel való fejlesztéskor hibák vagy kivételek jelenhetnek meg a felhasználói utazás végrehajtása során. A Application Insights használatával is megvizsgálható.

- Application Insights integrálása Azure AD B2C a [kivételek diagnosztizálásához](troubleshoot-with-application-insights.md).
- A [Visual Studio Code Azure ad B2C-bővítménye](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) segíthet a naplók elérésében és [megjelenítésében](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) a szabályzat neve és időpontja alapján.
- Az egyéni házirendek beállításának leggyakoribb hibája az XML-fájl helytelen formázása. Az XML- [séma érvényesítésével](troubleshoot-custom-policies.md) azonosíthatja a hibákat, mielőtt feltölti az XML-fájlt.

## <a name="continuous-integration"></a>Folyamatos integráció

Az Azure-folyamatokban beállított folyamatos integrációs és kézbesítési (CI/CD) folyamat használatával a szoftveres kézbesítés és a kód-vezérlés automatizálása [során a Azure ad B2C egyéni szabályzatokat](deploy-custom-policies-devops.md) is megadhatja. A különböző Azure AD B2C környezetekben történő üzembe helyezéskor, például a fejlesztési, tesztelési és éles üzemi környezetben javasoljuk, hogy távolítsa el a manuális folyamatokat, és végezzen automatikus tesztelést az Azure-folyamatok használatával.

## <a name="prepare-your-environment"></a>A környezet előkészítése

Első lépések Azure AD B2C egyéni szabályzattal:

1. [Azure AD B2C bérlő létrehozása](tutorial-create-tenant.md)
1. [Regisztráljon egy webalkalmazást](tutorial-register-applications.md) a Azure Portal használatával, hogy tesztelni tudja a szabályzatot.
1. Adja hozzá a szükséges [házirend-kulcsokat](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-signing-and-encryption-keys) , és [regisztrálja az Identity Experience Framework-alkalmazásokat](tutorial-create-user-flows.md?pivots=b2c-custom-policy#register-identity-experience-framework-applications).
1. [Szerezze be a Azure ad B2C Policy starter csomagot](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) , és töltse fel a bérlőbe. 
1. Az alapszintű csomag feltöltése után [tesztelje a regisztrálási vagy bejelentkezési szabályzatot](tutorial-create-user-flows.md?pivots=b2c-custom-policy#test-the-custom-policy).
1. Javasoljuk, hogy töltse le és telepítse a [Visual Studio Code](https://code.visualstudio.com/) -ot (vs Code). A Visual Studio Code egy egyszerű, de hatékony forráskód-szerkesztő, amely az asztalon fut, és Windows, macOS és Linux rendszereken is elérhető. A VS Code használatával gyorsan navigálhat és szerkesztheti a Azure AD B2C egyéni házirend XML-fájljait a [vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) -hoz készült Azure ad B2C-bővítmény telepítésével
 
## <a name="next-steps"></a>Következő lépések

A Azure AD B2C szabályzat beállítása és tesztelése után megkezdheti a szabályzat testreszabását. A következő cikkekben megismerheti a következőket:

- [Jogcímek hozzáadása és felhasználói bevitel testreszabása](./configure-user-input.md) egyéni szabályzatok használatával. Megtudhatja, hogyan határozhat meg jogcímeket, és hogyan adhat hozzá jogcímeket a felhasználói felülethez az alapszintű csomagok egyes technikai profiljainak testreszabásával.
- Egyéni házirend használatával [testreszabhatja az alkalmazás felhasználói felületét](customize-ui-with-html.md) . Megtudhatja, hogyan hozhat létre saját HTML-tartalmakat, és hogyan szabhatja testre a tartalom definícióját.
- Az alkalmazás [felhasználói felületének honosítása](./language-customization.md) egyéni házirend használatával. Megtudhatja, hogyan állíthatja be a támogatott nyelvek listáját, és hogyan adhat meg nyelvspecifikus címkéket a honosított erőforrások elem hozzáadásával.
- A szabályzat fejlesztése és tesztelése során [letilthatja az e-mailek ellenőrzését](./disable-email-verification.md). Útmutató a technikai profil metaadatainak felülírásához.
- Egyéni házirendeket használó [Google-fiókkal történő bejelentkezés beállítása](./identity-provider-google.md) . Megtudhatja, hogyan hozhat létre új jogcím-szolgáltatót a OAuth2 technikai profiljával. Ezután szabja testre a felhasználói utat, hogy belefoglalja a Google bejelentkezési lehetőséget.
- Az egyéni szabályzatokkal kapcsolatos problémák diagnosztizálásához [összegyűjthet Azure Active Directory B2C naplókat Application Insights](troubleshoot-with-application-insights.md)használatával. Ismerje meg, hogyan adhat hozzá új technikai profilokat, és hogyan konfigurálhatja a függő entitások házirendjét.