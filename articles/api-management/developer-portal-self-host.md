---
title: A fejlesztői portál saját gazdagépe
titleSuffix: Azure API Management
description: Ismerje meg, hogyan saját maga is API Management a fejlesztői portálon.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741714"
---
# <a name="self-host-the-api-management-developer-portal"></a>Az API Management fejlesztői portáljának saját gazdagépe

Ez az oktatóanyag azt ismerteti, hogyan lehet saját maga által API Management a fejlesztői [portálon.](api-management-howto-developer-portal.md) Az önkiszolgáló üzemeltetés rugalmasságot biztosít a fejlesztői portál olyan egyéni logikával és widgetekkel való kibővíthetőségéhez, amelyek dinamikusan szabják testre az oldalakat a futásidőben. A saját példánya számára több portált is API Management különböző funkciókkal. Amikor önkiszolgálóan ad meg egy portált, ön lesz a karbantartó, és Ön felelős a frissítéséért. 

A következő lépések azt mutatják be, hogyan állíthatja be a helyi fejlesztési környezetet, hogyan lehet módosításokat végezni a fejlesztői portálon, és hogyan lehet közzétenni és üzembe helyezni őket egy Azure-tárfiókban.

Ha már feltöltött vagy módosított médiafájlokat a [](#move-from-managed-to-self-hosted-developer-portal)felügyelt portálon, tekintse meg a cikk későbbi, Áthelyezés felügyeltről saját üzemeltetett szolgáltatásba cikkét.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

A helyi fejlesztési környezet beállításának a következőre van szüksége:

- Egy API Management szolgáltatáspéldány. Ha még nincs ilyen példánya, tekintse meg a Rövid [útmutató – Azure-beli virtuális API Management létrehozása.](get-started-create-service-instance.md)
- Egy Azure-tárfiók, engedélyezett [statikus webhelyek funkcióval.](../storage/blobs/storage-blob-static-website.md) Lásd: [Tárfiók létrehozása.](../storage/common/storage-account-create.md)
- Git a gépen. Telepítse ezt a [Git-oktatóanyagot követően.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Node.js (LTS vagy újabb `v10.15.0` verzió) és az npm a számítógépen. Lásd: [Downloading and installing Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)(Az Node.js és az npm letöltése és telepítése).
- Azure CLI. Kövesse [az Azure CLI telepítési lépéseit.](/cli/azure/install-azure-cli-windows)

## <a name="step-1-set-up-local-environment"></a>1. lépés: A helyi környezet beállítása

A helyi környezet beállításhoz klónoznia kell az adattárat, át kell váltania a fejlesztői portál legújabb kiadására, és telepítenie kell az npm-csomagokat.

1. Klónozza [az api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) adattárat a GitHubról:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Ugrás az adatok helyi példányára:

    ```console
    cd api-management-developer-portal
    ```

1. Tekintse meg a portál legújabb kiadását.

    A következő kód futtatása előtt ellenőrizze az aktuális kiadási címkét az adattár [Releases](https://github.com/Azure/api-management-developer-portal/releases) (Kiadások) szakaszában, és cserélje le az értéket a `<current-release-tag>` legújabb kiadási címkére.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Telepítse az elérhető npm-csomagokat:

    ```console
    npm install
    ```

> [!TIP]
> Mindig a [portál legújabb kiadását](https://github.com/Azure/api-management-developer-portal/releases) használja, és tartsa naprakészen az eltolt portált. A szoftvermérnökök az adattár ágát használják `master` napi fejlesztési célokra. A szoftver instabil verzióival rendelkezik.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>2. lépés: JSON-fájlok, statikus webhely és CORS-beállítások konfigurálása

A fejlesztői portál használatához API Management a REST API kell kezelnie.

### <a name="configdesignjson-file"></a>config.design.jsfájlban

Nyissa meg a `src` mappát, és nyissa meg `config.design.json` a fájlt.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Konfigurálja a fájlt:

1. Az értékben cserélje le a helyére a saját API Management `managementApiUrl` `<service-name>` nevét. Ha egyéni tartományt [konfigurált,](configure-custom-domain.md)használja helyette (például `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Manuálisan hozzon létre egy SAS-jogkivonatot,](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) amely lehetővé teszi a REST API hozzáférését a API Management példányhoz.

1. Másolja ki a létrehozott jogkivonatot, és illessze be `managementApiAccessToken` értékként.

1. Az értékben cserélje le a helyére a saját API Management `backendUrl` `<service-name>` nevét. Ha egyéni tartományt [konfigurált,](configure-custom-domain.md)használja helyette (például `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Ha engedélyezni szeretné a CAPTCHA-t a fejlesztői portálon, tekintse meg [a CAPTCHA engedélyezését.](#enable-captcha)

### <a name="configpublishjson-file"></a>config.publish.jsfájlban

Nyissa meg `src` a mappát, és nyissa meg `config.publish.json` a fájlt.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Konfigurálja a fájlt:

1. Másolja és illessze be az előző konfigurációs fájl `managementApiUrl` `managementApiAccessToken` és értékét.

1. Ha engedélyezni szeretné a CAPTCHA-t a fejlesztői portálon, tekintse meg [a CAPTCHA engedélyezését.](#enable-captcha)

### <a name="configruntimejson-file"></a>config.runtime.jsfájlban

Nyissa meg `src` a mappát, és nyissa meg `config.runtime.json` a fájlt.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Konfigurálja a fájlt:

1. Másolja és illessze `managementApiUrl` be az előző konfigurációs fájlban található értéket.

1. Az `backendUrl` értékben cserélje le a helyére a saját API Management `<service-name>` nevét. Ha egyéni tartományt [konfigurált,](configure-custom-domain.md)használja inkább azt (például: `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>A statikus webhely konfigurálása

Konfigurálja **a statikus webhely** funkciót a tárfiókban az index- és hibalapok útvonalának meg biztosításával:

1. A bal oldali menüben Azure Portal válassza  a Statikus webhely lehetőséget a bal oldali menüben.

1. A Statikus **webhely oldalon** válassza az Engedélyezve **lehetőséget.**

1. Az **Indexdokumentum neve mezőbe írja** be a *következőt:index.html.*

1. A Hiba **dokumentum elérési útja mezőbe** írja be a következőt: *404/index.html.*

1. Kattintson a **Mentés** gombra.

### <a name="configure-the-cors-settings"></a>A CORS-beállítások konfigurálása

Konfigurálja az eredetközi erőforrás-megosztás (CORS) beállításait:

1. A bal oldali menüben Azure Portal a **CORS** lehetőséget a tárfiókhoz.

1. A **Blob service** konfigurálja a következő szabályokat:

    | Szabály | Érték |
    | ---- | ----- |
    | Engedélyezett eredetek | * |
    | Engedélyezett metódusok | Válassza ki az összes HTTP-igealakot. |
    | Engedélyezett fejlécek | * |
    | Közzétett fejlécek | * |
    | Maximális életkor | 0 |

1. Kattintson a **Mentés** gombra.

## <a name="step-3-run-the-portal"></a>3. lépés: A portál futtatása

Most már fejlesztési módban is felépíthet és futtathat egy helyi portálpéldányt. Fejlesztői módban az összes optimalizálás ki van kapcsolva, a forrástérképek pedig be vannak kapcsolva.

Futtassa az alábbi parancsot:

```console
npm start
```

Rövid idő után az alapértelmezett böngésző automatikusan megnyílik a helyi fejlesztői portálon. Az alapértelmezett cím a , de a `http://localhost:8080` port változhat, `8080` ha már foglalt. A projekt kódbázisának bármilyen változtatása elindítja a böngészőablak újraépítését és frissítését.

## <a name="step-4-edit-through-the-visual-editor"></a>4. lépés: Szerkesztés a vizualizációszerkesztővel

A vizualizációszerkesztővel végezze el a következő feladatokat:

- A portál testreszabása
- Tartalom írása
- A webhely struktúrájának rendszerezése
- Megjelenésének stilizálása

Lásd: [Oktatóanyag: A fejlesztői portál elérése és testreszabása.](api-management-howto-developer-portal-customize.md) A rendszergazdai felhasználói felület alapjait és az alapértelmezett tartalom javasolt módosításait sorolja fel. Mentse a módosításokat a helyi környezetben, majd zárja be a **Ctrl+C** billentyűkombinációval.

## <a name="step-5-publish-locally"></a>5. lépés: Helyi közzététel

A portál adatai erős típusú objektumok formájában származnak. A következő parancs statikus fájlokra fordítja le őket, és a kimenetet a könyvtárba `./dist/website` írja:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>6. lépés: Statikus fájlok feltöltése blobba

Az Azure CLI használatával töltse fel a helyileg létrehozott statikus fájlokat egy blobba, és győződjön meg arról, hogy a látogatók el tudnak számukra jutni:

1. Nyissa meg a Windows-parancssort, a PowerShellt vagy más parancshéjat.

1. Futtassa a következő Azure CLI-parancsot.
   
    Cserélje `<account-connection-string>` le a helyére a tárfiókja kapcsolati sztringjét. Ezt a tárfiók  Hozzáférési kulcsok szakaszában kaphatja meg.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>7. lépés: Ugrás a webhelyre

A webhely most már az Azure Storage tulajdonságaiban megadott gazdanév **alatt** található (statikus webhelyek elsődleges **végpontja).**

## <a name="step-8-change-api-management-notification-templates"></a>8. lépés: A API Management módosítása

Cserélje le a fejlesztői portál URL-címét a API Management értesítési sablonokban, hogy a saját üzemeltetett portálra mutasson. Lásd: Értesítések és e-mail-sablonok [konfigurálása az Azure API Management.](api-management-howto-configure-notifications.md)

Végezze el a következő módosításokat az alapértelmezett sablonokon:

> [!NOTE]
> A következő frissített **szakaszokban** található értékek feltételezik, hogy a portált a **https-címen,/portal.contoso.com/. \/** 

### <a name="email-change-confirmation"></a>E-mail-módosítás megerősítése

Frissítse a fejlesztői portál URL-címét az **E-mail-módosítás megerősítő értesítési** sablonban:

**Eredeti tartalom**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Frissített**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Új fejlesztői fiók megerősítése

Frissítse a fejlesztői portál URL-címét az Új fejlesztői fiók megerősítő **értesítési** sablonban:

**Eredeti tartalom**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Frissített**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Felhasználó meghívása

Frissítse a fejlesztői portál URL-címét a Felhasználói **értesítés meghívása** sablonban:

**Eredeti tartalom**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Frissített**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Új előfizetés aktiválva

Frissítse a fejlesztői portál URL-címét az **Új előfizetés aktivált értesítési** sablonban:

**Eredeti tartalom**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Frissített**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Eredeti tartalom**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Frissített**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Eredeti tartalom**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Frissített**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Eredeti tartalom**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Frissített**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Jelszóváltozás megerősítése

Frissítse a fejlesztői portál URL-címét a **Jelszóváltozás-megerősítési** értesítés sablonban:

**Eredeti tartalom**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Frissített**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Minden sablon

Frissítse a fejlesztői portál URL-címét minden olyan sablonban, amely láblécében hivatkozás található:

**Eredeti tartalom**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Frissített**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Áthelyezés felügyeltről saját üzemeltetett fejlesztői portálra

Idővel az üzleti követelmények változhatnak. Ez olyan helyzethez is hozzátért, amikor a API Management fejlesztői portál felügyelt verziója már nem megfelelő az Ön igényeinek. Egy új követelmény például arra kényszerítheti, hogy olyan egyéni widgetet készítsen, amely integrálható egy külső adatszolgáltatóval. A kezelt verziótól eltérően a portál saját üzemeltetett verziója teljes rugalmasságot és nagyobb rugalmasságot biztosít.

### <a name="transition-process"></a>Átváltási folyamat

A felügyelt verzióról átválthat egy saját üzemeltetett verzióra ugyanazon a API Management szolgáltatáspéldányon belül. A folyamat megőrzi a portál felügyelt verziójában végrehajtott módosításokat. Győződjön meg arról, hogy előre biztonságimenti a portál tartalmát. A biztonsági mentési szkript a fejlesztői portál `scripts` GitHub API Management tár [mappájában található.](https://github.com/Azure/api-management-developer-portal)

Az átalakítási folyamat majdnem teljesen megegyezik egy általános, saját üzemeltethető portál beállításával, ahogy azt a cikk korábbi lépései is mutatják. A konfigurációs lépés egy kivételt jelent. A fájlban található tárfióknak meg kell egy lennie a portál felügyelt verziójának `config.design.json` tárfiókjának. Az SAS URL-cím lekérésével kapcsolatban lásd: Oktatóanyag: Linux VM-beli, rendszer által hozzárendelt identitás használata az [Azure Storage eléréséhez SAS-hitelesítő](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) adatokon keresztül.

> [!TIP]
> Javasoljuk, hogy a fájlban külön tárfiókot `config.publish.json` használ. Ez a megközelítés nagyobb irányítást biztosít, és leegyszerűsíti a portál üzemeltetési szolgáltatásának kezelését.

## <a name="enable-captcha"></a>CAPTCHA engedélyezése

A saját üzemeltetett portál beállításakor előfordulhat, hogy a beállítással letiltotta a `useHipCaptcha` CAPTCHA-t. A CAPTCHA-val való kommunikáció egy végponton keresztül történik, amely lehetővé teszi, hogy az eredetközi erőforrás-megosztás (CORS) csak a felügyelt fejlesztői portál gazdaneve esetén történjen meg. Ha a fejlesztői portál saját üzemeltetetű, akkor más gazdanevet használ, és a CAPTCHA nem engedélyezi a kommunikációt.

### <a name="update-the-json-config-files"></a>A JSON-konfigurációs fájlok frissítése

A CAPTCHA engedélyezése a saját üzemeltetett portálon:

1. Rendeljen hozzá egy egyéni tartományt (például : ) a fejlesztői portál végpontjának `api.contoso.com` API Management szolgáltatáshoz. 

    Ez a tartomány a portál felügyelt verziójára és a CAPTCHA-végpontra vonatkozik. A lépésekért [lásd: Egyéni tartománynév konfigurálása az Azure API Management példányhoz.](configure-custom-domain.md)

1. A saját `src` üzemeltetett portáljának [helyi](#step-1-set-up-local-environment) környezetében található mappában.

1. Frissítse a konfigurációs `json` fájlokat:

    | Fájl | Új érték | Megjegyzés |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Cserélje le a helyére az első lépésben `<custom-domain>` beállított egyéni tartományt. |
    |  | `"useHipCaptcha": true` | Módosítsa az értéket a következőre: `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Cserélje le a helyére az első lépésben `<custom-domain>` beállított egyéni tartományt. |
    |  | `"useHipCaptcha": true` | Módosítsa az értéket a következőre: `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Cserélje le a helyére az első lépésben `<custom-domain>` beállított egyéni tartományt. |

1. [Tegye közzé a](#step-5-publish-locally) portált.

1. [Töltse fel](#step-6-upload-static-files-to-a-blob) és töltse fel az újonnan közzétett portált.

1. Tegye elérhetővé a saját üzemeltetett portált egy egyéni tartományon keresztül.

A portál tartományának első és második szintjeinek meg kell egyezniük az első lépésben beállított tartománnyal. Például: `portal.contoso.com`. A pontos lépések a választott üzemeltetési platformtól függenek. Ha Azure-tárfiókot használt, az utasításokért tekintse meg az egyéni tartomány leképezés [Azure Blob Storage végpontra.](../storage/blobs/storage-custom-domain-name.md)

## <a name="next-steps"></a>Következő lépések

- Az [önkiszolgáló üzemeltetés alternatív megközelítésének megismerése](developer-portal-alternative-processes-self-host.md)
