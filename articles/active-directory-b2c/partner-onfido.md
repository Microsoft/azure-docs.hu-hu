---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Onfido használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a Onfido és az arc biometriai ellenőrzésével
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928630"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Oktatóanyag a Onfido konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure AD B2C és a [Onfido](https://onfido.com/)integrálását ismertető útmutató nyújt útmutatást. A Onfido egy dokumentum-azonosító és egy arc biometria-ellenőrző alkalmazás. Lehetővé teszi a vállalatok számára, hogy valós időben tudják kielégíteni *az ügyfél* és az identitás követelményeit. A Onfido kifinomult mesterséges intelligencia-alapú személyazonosság-ellenőrzést használ, amely először ellenőrzi a fénykép AZONOSÍTÓját, majd megfelel az arc-biometrikus adatoknak. Ez a megoldás egy digitális identitást köt valós személyével, és biztonságos bevezetési élményt biztosít a csalás csökkentése érdekében.

Ebben a példában a Onfido szolgáltatását a regisztrációs vagy bejelentkezési folyamat során a személyazonosság ellenőrzéséhez kapcsolja. Tájékozott döntések arról, hogy a felhasználó mely termékkel és szolgáltatásokkal férhet hozzá, a Onfido eredményei alapján történik.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- Egy Onfido [próbaverziós fiók](https://onfido.com/signup/).

## <a name="scenario-description"></a>Forgatókönyv leírása

A Onfido-integráció a következő összetevőket tartalmazza:

- Azure AD B2C bérlő – az engedélyezési kiszolgáló, amelynek feladata a felhasználó hitelesítő adatainak ellenőrzése a bérlőben definiált egyéni házirendek alapján. Más néven identitás-szolgáltató. A Onfido-ügyfélalkalmazás tárolja, amely összegyűjti a felhasználói dokumentumokat, és továbbítja azt a Onfido API szolgáltatásnak.

- Onfido Client – egy konfigurálható JavaScript-ügyfél dokumentum-gyűjtési segédprogram, amely más weblapokon is üzembe helyezhető. Gyűjti a dokumentumokat, és olyan előzetes ellenőrzéseket végez, mint például a dokumentumok mérete és minősége.

- Köztes REST API – végpontokat biztosít a Azure AD B2C bérlő számára a Onfido API szolgáltatással folytatott kommunikációhoz, az adatfeldolgozás kezeléséhez és mindkét biztonsági követelmény betartásához.

- Onfido API Service – a Onfido által biztosított háttér-szolgáltatás, amely elmenti és ellenőrzi a felhasználó által megadott dokumentumokat.

A következő architektúra-diagram a megvalósítást mutatja be.

![képernyőkép a onfido-Architecture-diagramról](media/partner-onfido/onfido-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználó feliratkozik egy új fiók létrehozására, és adatokat helyez el az oldalra. A Azure AD B2C a felhasználói attribútumokat gyűjti. A Azure AD B2Cben üzemeltetett Onfido-ügyfélalkalmazás előzetesen ellenőrzi a felhasználói adatokat.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja a Onfido API-t használó formátumba. Ezt követően küldje el a Onfido.
| 4. | A Onfido felhasználja az adatokat, és a felhasználó azonosításának ellenőrzéséhez dolgozza fel azokat. Ezután visszaadja az eredményt a középső rétegbeli API-nak.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és a megfelelő JSON formátumban küldi el a kapcsolódó információkat a Azure AD B2C.
| 6. | Azure AD B2C adatokat fogad a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba.

## <a name="onboard-with-onfido"></a>Onfido

1. Onfido-fiók létrehozásához forduljon a [Onfidohoz](https://onfido.com/signup/).

2. Egy fiók létrehozása után hozzon létre egy [API-kulcsot](https://documentation.onfido.com/). Az élő kulcsok számlázandóek, a megoldás teszteléséhez azonban használhatja a [homokozó kulcsait](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) . A homokozó kulcsai ugyanazt az eredményt használják, mint az élő kulcsok, azonban az eredmények mindig előre vannak meghatározva. A dokumentumok nincsenek feldolgozva vagy mentve.

>[!NOTE]
> Később szüksége lesz a kulcsra.

További információ a Onfido: [ONFIDO API-dokumentáció](https://documentation.onfido.com) és [Onfido fejlesztői központ](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Azure AD B2C konfigurálása a Onfido

### <a name="part-1---deploy-the-api"></a>1. rész – az API üzembe helyezése

- A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) üzembe helyezése egy Azure-szolgáltatásban. A kód a Visual studióból is közzétehető, ezeket az [utasításokat](/visualstudio/deployment/quickstart-deploy-to-azure)követve.
- CORS beállítása, **engedélyezett forrás** hozzáadása https://{your_tenant_name}. b2clogin. com

>[!NOTE]
>Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

#### <a name="adding-sensitive-configuration-settings"></a>Bizalmas konfigurációs beállítások hozzáadása

Az Alkalmazásbeállítások konfigurálhatók az [Azure app Service-ben](../app-service/configure-common.md#configure-app-settings). Az App Service lehetővé teszi a beállítások biztonságos konfigurálását anélkül, hogy azokat egy adattárba kellene bejelentkeznie. A REST API-nak a következő beállításokra van szüksége:

| Alkalmazásbeállítás neve | Forrás | Jegyzetek |
|:-------------------------|:-------|:-------|
|OnfidoSettings: aad| Onfido-fiók |

### <a name="part-2---deploy-the-ui"></a>2. rész – a felhasználói felület üzembe helyezése

#### <a name="configure-your-storage-location"></a>A tárolási hely konfigurálása

1. [Blob Storage-tároló beállítása a Storage-fiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Tárolja a felhasználói felületi fájlokat a [felhasználói felület mappájából](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) a blob-tárolóba.

3. A CORS hozzáférés engedélyezése a létrehozott tárolóhoz a következő utasítások követésével:

   a. Lépjen a **Beállítások**  > **engedélyezett forráshoz**, majd írja be a következőt: `https://{your_tenant_name}.b2clogin.com` . Cserélje le a-bérlő nevét a Azure AD B2C bérlő nevére. Például: https://fabrikam.b2clogin.com . A bérlő nevének megadásakor használja az összes kisbetűt.

   b. Az **engedélyezett módszereknél** válassza a és a lehetőséget `GET` `PUT` .

   c. Válassza a **Mentés** lehetőséget.

#### <a name="update-ui-files"></a>FELHASZNÁLÓIFELÜLET-fájlok frissítése

1. A felhasználói felület fájljaiban nyissa meg a mappát [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Nyissa meg az egyes HTML-fájlokat.

3. Keresse meg és cserélje le {a-UI-blob-Container-URL}-t annak a URL-címével, ahol a felhasználói felület **ocean_blue**, a **dist** és az **assets** mappák találhatók

4. Keresse meg és cserélje le a {Your-Intermediate-API-URL} címet a köztes API app Service URL-címére.

#### <a name="upload-your-files"></a>Fájlok feltöltése

1. Tárolja a felhasználói felületi fájlokat a felhasználói felület mappájából a blob-tárolóba.

2. A fájlok és a hozzáférési engedélyek kezeléséhez használja a [Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) .

### <a name="part-3---configure-azure-ad-b2c"></a>3. rész – Azure AD B2C konfigurálása

#### <a name="replace-the-configuration-values"></a>A konfigurációs értékek cseréje

A megadott [Egyéni szabályzatokban](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)keresse meg a következő helyőrzőket, és cserélje le a elemet a példány megfelelő értékeire.

| Helyőrző | Csere értékkel | Példa  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | A bérlő rövid neve |  "yourtenant" a yourtenant.onmicrosoft.com |
| {your_tenantID} | Azure AD B2C bérlő TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Az Azure AD B2C-bérlőben konfigurált IdentityExperienceFramework-alkalmazás alkalmazás-azonosítója      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Az Azure AD B2C-bérlőben konfigurált ProxyIdentityExperienceFramework-alkalmazás alkalmazás-azonosítója | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | A bérlő tárolási alkalmazásának alkalmazás-azonosítója                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | A bérlő Storage-alkalmazásának objektum-azonosítója                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | Az alkalmazás-bepillantási példány kialakítási kulcsa *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| Annak a helynek az URL-címe, ahol a felhasználói felület **ocean_blue**, a **dist** és az **eszközök** mappája található | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | A beállított app Service URL-címe                                             | `https://yourapp.azurewebsites.net`          |

* Az alkalmazás-felismerések egy másik bérlőn is lehetnek. Ez a lépés nem kötelező. Ha nincs rá szükség, távolítsa el a megfelelő TechnicalProfiles és OrchestrationSteps.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4. rész – a Azure AD B2C házirend konfigurálása

Tekintse át ezt a [dokumentumot](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , amely útmutatást nyújt a Azure ad B2C bérlő beállításához és a házirendek konfigurálásához.

>[!NOTE]
> Ajánlott eljárásként Azt javasoljuk, hogy az ügyfelek az attribútumok gyűjteménye lapon vegyenek fel beleegyező értesítéseket. Értesítse a felhasználókat arról, hogy az adatok a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából lesznek elküldve.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az **identitási élmény keretrendszere** elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A Onfido szolgáltatás a folyamat során lesz meghívva, a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
