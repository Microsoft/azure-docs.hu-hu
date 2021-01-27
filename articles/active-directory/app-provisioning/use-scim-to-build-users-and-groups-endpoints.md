---
title: SCIM-végpont létrehozása az Azure AD-ből származó alkalmazásokhoz való felhasználói üzembe helyezéshez
description: A rendszer a tartományok közötti Identitáskezelés (SCIM) esetében szabványosítja a felhasználók automatikus kiépítési folyamatát. Ismerje meg, hogyan fejleszthet SCIM-végpontokat, hogyan integrálhatja a SCIM API-t a Azure Active Directoryval, és megkezdheti a felhasználók és csoportok kiépítésének automatizálását a felhőalapú alkalmazásokba.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900162"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Oktatóanyag: minta SCIM-végpont fejlesztése

Senki nem szeretne teljesen új végpontot létrehozni, ezért létrehoztunk egy [hivatkozási kódot](https://aka.ms/scimreferencecode) , amellyel megkezdheti a [scim](https://aka.ms/scimoverview). Ez az oktatóanyag leírja, hogyan helyezheti üzembe a SCIM hivatkozási kódját az Azure-ban, és hogyan tesztelheti a Poster használatával vagy az Azure AD SCIM-ügyfél integrálásával. A SCIM-végpontot akár 5 percen belül kód nélkül is elérheti és futtathatja. Ez az oktatóanyag olyan fejlesztők számára készült, akik megkezdik a SCIM vagy a SICM-végpontok tesztelését. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A hivatkozási kód letöltése
> * A SCIM-végpont üzembe helyezése az Azure-ban
> * SCIM-végpont tesztelése

A végponti képességek a következők:

|Végpont|Leírás|
|---|---|
|`/User`|SZIFILISZ-műveletek végrehajtása felhasználói erőforráson: **Létrehozás**, **frissítés**, **Törlés**, **beolvasás**, **Listázás**, **szűrés**|
|`/Group`|SZIFILISZ-műveletek végrehajtása csoportos erőforráson: **Létrehozás**, **frissítés**, **Törlés**, **beolvasás**, **Listázás**, **szűrés**|
|`/Schemas`|Egy vagy több támogatott séma beolvasása.<br/><br/>Az egyes szolgáltatók által támogatott erőforrások attribútumai eltérőek lehetnek, például A "név", "title" és "e-mailek", míg a B szolgáltató támogatja a "Name", a "title" és a "phoneNumbers" attribútumot a felhasználók számára.|
|`/ResourceTypes`|A támogatott erőforrástípusok beolvasása.<br/><br/>Az egyes szolgáltatók által támogatott erőforrások száma és típusai eltérőek lehetnek, például A szolgáltató támogatja A felhasználókat, miközben A B szolgáltató támogatja A felhasználókat és a csoportokat.|
|`/ServiceProviderConfig`|A szolgáltató SCIM-konfigurációjának beolvasása<br/><br/>Az egyes szolgáltatók által támogatott SCIM funkciók változhatnak, például A szolgáltató támogatja A javítási műveleteket, míg A B szolgáltató támogatja A javítási műveleteket és A séma-felderítést.|

## <a name="download-the-reference-code"></a>A hivatkozási kód letöltése

A letöltött [hivatkozási kód](https://github.com/AzureAD/SCIMReferenceCode) a következő projekteket tartalmazza:

- **Microsoft.SystemForCrossDomainIdentityManagement**, a .net Core MVC webes API egy scim API létrehozásához és kiépítéséhez
- **Microsoft. scim. WebHostSample**, egy scim-végpont működő példája

A projektek a következő mappákat és fájlokat tartalmazzák:

|Fájl/mappa|Description|
|-|-|
|**Sémák** mappa| A **felhasználói** és **csoportos** erőforrások modelljei, valamint néhány absztrakt osztály, például a sematikus a megosztott funkciókhoz.<br/><br/> **Attribútumok** mappa, amely a **felhasználók** és **csoportok** , például címek összetett attribútumainak osztály-definícióit tartalmazza.|
|**Szolgáltatás** mappája | Az erőforrások lekérdezésének és frissítésének módját érintő műveletek logikáját tartalmazza.<br/><br/> A hivatkozási kód olyan szolgáltatásokat tartalmaz, amelyek a felhasználókat és a csoportokat adják vissza.<br/><br/>A **vezérlők** mappa a különböző scim-végpontokat tartalmazza. Az erőforrás-vezérlők olyan HTTP-műveleteket tartalmaznak, amelyek az erőforráson végzett szifilisz-műveletek végrehajtásához szükségesek (**Get**, **post**, **put**, **patch**, **delete**). A vezérlők a szolgáltatásokra támaszkodva végrehajtják a műveleteket.|
|**Protokoll** mappája|Az erőforrásoknak a SCIM RFC-ben (például:) történő visszaadására szolgáló műveletek logikáját tartalmazza.<br/><ul><li>Több erőforrás visszaadása listaként.</li><li>Egy szűrő alapján csak bizonyos erőforrásokat ad vissza.</li><li>Lekérdezés bekapcsolása egyetlen szűrőhöz tartozó csatolt listák listájára.</li><li>Egy javítási kérelem bekapcsolása egy, az érték elérési útra vonatkozó attribútumokkal rendelkező műveletre.</li><li>Az erőforrás-objektumok módosításainak alkalmazásához használható művelet típusának meghatározása.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Minta forráskódja|
|`Microsoft.SCIM.WebHostSample`| Példa a SCIM könyvtárának megvalósítására.|
|*.gitignore*|Határozza meg, hogy mit kell figyelmen kívül hagyni a véglegesítés ideje alatt.|
|*CHANGELOG.md*|A minta változásainak listája.|
|*CONTRIBUTING.md*|Útmutató a mintához való hozzájáruláshoz.|
|*README.md*|Ez a **readme** fájl.|
|*ENGEDÉLY*|A minta licence.|

> [!NOTE]
> Ez a kód segít megkezdeni a SCIM-végpont kiépítését, **és a következőként van megadva:**. A hivatkozásokhoz nem tartozik garancia az aktív maintainence vagy támogatásra.
>
> A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. A Közösség ilyen [hozzájárulásai](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) szívesen segítenek a tárház kiépítésében és fenntartásában, valamint más nyílt forráskódú hozzájárulásokhoz is, Ön elfogadja a közreműködő licencszerződést (CLA). A jelen Szerződés kijelenti, hogy Ön rendelkezik a hozzájárulás használatára vonatkozó jogosultságokkal, és megadta a jogot a [Microsoft nyílt forráskódja](https://cla.opensource.microsoft.com)című témakörben.
>
> További információkért keresse fel a [Viselkedési szabályzattal kapcsolatos gyakori kérdések](https://opensource.microsoft.com/codeofconduct/faq/) oldalát, illetve küldje el kérdéseit vagy észrevételeit a következő címre: [opencode@microsoft.com](mailto:opencode@microsoft.com).

###  <a name="use-multiple-environments"></a>Több környezet használata

A mellékelt SCIM-kód egy ASP.NET Core környezetet használ a fejlesztéshez és az üzembe helyezéshez szükséges engedélyek vezérléséhez: [több környezet használata a ASP.net Coreban](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>A SCIM-végpont üzembe helyezése az Azure-ban

Az itt ismertetett lépések a SCIM-végpontot a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) és az [Azure app Services](https://docs.microsoft.com/azure/app-service/)használatával helyezik üzembe a szolgáltatásban. A SCIM-hivatkozási kód helyileg futtatható, helyszíni kiszolgáló által üzemeltetett, vagy egy másik külső szolgáltatásban is üzembe helyezhető. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Megoldás megnyitása és üzembe helyezése Azure App Service

1. Nyissa meg a [hivatkozási kódot](https://github.com/AzureAD/SCIMReferenceCode) a githubon, és válassza a **klónozás vagy a letöltés** lehetőséget.

1. Válassza a **Megnyitás az asztalon** lehetőséget, vagy másolja a hivatkozást, nyissa meg a **Visual studiót**, és válassza a **klónozás vagy a kód kijelentkezése** lehetőséget a másolt hivatkozás megadásához, és készítsen helyi másolatot.

1. A **Visual Studióban** ügyeljen arra, hogy jelentkezzen be abba a fiókba, amely hozzáfér az üzemeltetési erőforrásaihoz.

1. A **megoldáskezelő** nyissa meg a *Microsoft. scim. SLN* fájlt, és kattintson a jobb gombbal a *Microsoft. scim. WebHostSample* fájlra. Válassza a **Közzététel** lehetőséget.

    ![Felhőbeli közzététel](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > A megoldás helyi futtatásához kattintson duplán a projektre, és válassza a **IIS Express** lehetőséget a projekt elindításához a helyi gazdagép URL-címét tartalmazó weblapként.

1. Válassza a **profil létrehozása** lehetőséget, és győződjön meg arról, hogy a **app Service** és az **új létrehozása** lehetőség ki van választva.

    ![Cloud publish 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Lépjen át a párbeszédpanel beállításai között, és nevezze át az alkalmazást egy tetszőleges névre. Ezt a nevet az alkalmazás és a SCIM végpont URL-címe is használja.

    ![Cloud publish 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Válassza ki a használni kívánt erőforráscsoportot, és válassza a **Közzététel** lehetőséget.

1. Navigáljon az alkalmazáshoz az **Azure app Services**  >  **konfigurációban** , és válassza az **új Alkalmazásbeállítás** lehetőséget a *Token__TokenIssuer* beállítás értékkel való hozzáadásához `https://sts.windows.net/<tenant_id>/` . Cserélje le az `<tenant_id>` értéket az Azure ad-tenant_idra, és ha a [Poster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)használatával szeretné tesztelni az scim-végpontot, adjon hozzá egy *ASPNETCORE_ENVIRONMENT* -beállítást is az értékkel `Development` . 

   ![appservice-beállítások](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Ha a végpontot a Azure Portal vállalati alkalmazásával teszteli, válassza a környezet megtartását, `Development` és adja meg a végpont által generált jogkivonatot, `/scim/token` hogy tesztelje vagy módosítsa a környezetet, `Production` és hagyja üresen a jogkivonat mezőt a [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)vállalati alkalmazásban. 

Ennyi az egész! A SCIM-végpont már közzé lett téve, és lehetővé teszi az Azure App Service URL-cím használatát a SCIM-végpont teszteléséhez.

## <a name="test-your-scim-endpoint"></a>SCIM-végpont tesztelése

Az SCIM-végpontra irányuló kérések engedélyezést igényelnek, és a SCIM standard több lehetőséget is biztosít a hitelesítéshez és az engedélyezéshez, mint például a cookie-k, az alapszintű hitelesítés, a TLS-ügyfél hitelesítése vagy az [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)-ben felsorolt

Ügyeljen arra, hogy a nem biztonságos metódusokat (például a felhasználónevet és a jelszót) a biztonságosabb módszer, például a OAuth helyett ne tegye elérhetővé. Az Azure AD támogatja a hosszú élettartamú tulajdonosi jogkivonatokat (a katalógusok és a nem katalógusos alkalmazások esetében) és a OAuth engedélyezési támogatását (az alkalmazás-katalógusban közzétett alkalmazások esetében).

> [!NOTE]
> A tárházban megadott engedélyezési módszerek kizárólag tesztelésre szolgálnak. Az Azure AD-vel való integráció során áttekintheti az engedélyezési útmutatót: [scim-végpont létesítésének megtervezése](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

A fejlesztési környezet lehetővé teszi, hogy a funkciók nem biztonságosak az éles környezetben, például a hivatkozási kód a biztonsági jogkivonat ellenőrzésének viselkedését. A jogkivonat-érvényesítési kód önaláírt biztonsági jogkivonat használatára van konfigurálva, és az aláíró kulcs a konfigurációs fájlban tárolódik, lásd a **token: IssuerSigningKey** paramétert a fájl *appsettings.Development.js* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> A **Get** kérés küldése a `/scim/token` végpontnak a megadott kulccsal egy jogkivonatot állít ki, és a későbbi engedélyezéshez tulajdonosi jogkivonatként is használható.

Az alapértelmezett jogkivonat-ellenőrzési kód úgy van konfigurálva, hogy Azure Active Directory által kiállított jogkivonatot használjon, és megköveteli, hogy a kiállító bérlő a fájl *appsettings.js* **token: TokenIssuer** paraméterrel legyen konfigurálva.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>A Poster használata a végpontok teszteléséhez

Az SCIM-végpont üzembe helyezése után ellenőrizheti, hogy a SCIM RFC-kompatibilis-e. Ez a példa a **Poster** -teszteket biztosít a felhasználók és csoportok szifilisz-műveleteinek, szűrésének, a csoporttagság frissítéseinek és a felhasználók letiltásának ellenőrzéséhez.

A végpontok a `{host}/scim/` címtárban találhatók, és a szabványos HTTP-kérelmek használatával is használhatók. Az útvonal módosításához `/scim/` tekintse meg a *ControllerConstant.cs* a **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**-  >  **vezérlőkben** című témakört.

> [!NOTE]
> A helyi tesztekhez csak HTTP-végpontokat használhat, mivel az Azure AD kiépítési szolgáltatásához a végpont támogatásához HTTPS szükséges.

#### <a name="open-postman-and-run-tests"></a>A Poster megnyitása és a tesztek futtatása

1. A [Poster](https://www.getpostman.com/downloads/) letöltése és az alkalmazás elindítása.
1. Másolja a hivatkozást [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) , és illessze be a Poster-be a tesztelési gyűjtemény importálásához.

    ![Poster-gyűjtemény](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Hozzon létre egy tesztelési környezetet az alábbi változókkal:

   |Környezet|Változó|Érték|
   |-|-|-|
   |Projekt helyi futtatása IIS Express használatával|||
   ||**Kiszolgáló**|`localhost`|
   ||**Port**|`:44359`*(ne felejtse el a következőt **:**)*|
   ||**API**|`scim`|
   |Projekt helyi futtatása a vércse használatával|||
   ||**Kiszolgáló**|`localhost`|
   ||**Port**|`:5001`*(ne felejtse el a következőt **:**)*|
   ||**API**|`scim`|
   |A végpont üzemeltetése az Azure-ban|||
   ||**Kiszolgáló**|*(adja meg a SCIM URL-címét)*|
   ||**Port**|*(hagyja üresen)*|
   ||**API**|`scim`|

1. A Poster gyűjtemény **kulcsának beolvasása** parancsával küldjön le egy **Get** -kérést a jogkivonat-végpontnak, és kérje le a **jogkivonat** -változóban tárolni kívánt biztonsági jogkivonatot a további kérésekhez. 

   ![Poster Get kulcs](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Ahhoz, hogy a SCIM-végpontok biztonságosak legyenek, a csatlakozás előtt biztonsági jogkivonatra van szükség, és az oktatóanyag a `{host}/scim/token` végpontot használja egy önaláírt jogkivonat létrehozásához.

Ennyi az egész! Most már futtathatja a **Poster** -gyűjteményt a scim-végpont funkciójának teszteléséhez.

## <a name="next-steps"></a>Következő lépések

Ha egy SCIM-kompatibilis felhasználói és csoportos végpontot szeretne létrehozni egy ügyfél együttműködésével, tekintse meg a [scim-ügyfél implementációját](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Oktatóanyag: scim-végpont](use-scim-to-provision-users-and-groups.md) 
>  létesítésének fejlesztése és tervezése [Oktatóanyag: a kiépítés konfigurálása egy Gallery-alkalmazáshoz](configure-automatic-user-provisioning-portal.md)