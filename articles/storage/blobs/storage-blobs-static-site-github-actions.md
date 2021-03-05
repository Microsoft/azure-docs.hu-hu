---
title: A GitHub Actions használata statikus hely üzembe helyezéséhez az Azure Storage-ben
description: Azure Storage – statikus webhely üzemeltetése GitHub-műveletekkel
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 2192cdb3072edba2e5597a697feef99ba4d2070d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210257"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>GitHub-műveletek munkafolyamatának beállítása a statikus webhely üzembe helyezéséhez az Azure Storage-ban

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions) egy olyan munkafolyamat használatával, amely statikus helyet telepít egy Azure Storage-fiókba. A GitHub-műveletek munkafolyamatának beállítása után automatikusan üzembe helyezheti a helyet az Azure-ban a GitHubról, ha módosítja a webhely kódját.

> [!NOTE]
> Ha [Azure statikus Web Apps](../../static-web-apps/index.yml)használ, nem kell manuálisan beállítania egy GitHub-művelet munkafolyamatot.
> Az Azure statikus Web Apps automatikusan létrehoz egy GitHub-műveletek munkafolyamatot. 

## <a name="prerequisites"></a>Előfeltételek

Egy Azure-előfizetés és egy GitHub-fiók. 

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-tárház a statikus webhely kódjával. Ha nem rendelkezik GitHub-fiókkal, [regisztráljon ingyenesen](https://github.com/join).  
- Az Azure Storage-ban üzemeltetett, működő statikus webhely. Ismerje meg, hogyan [tárolhat statikus webhelyeket az Azure Storage-ban](storage-blob-static-website-how-to.md). Ennek a példának a követéséhez [Azure CDN](static-website-content-delivery-network.md)is telepítenie kell.

> [!NOTE]
> Gyakori, hogy a Content Delivery Network (CDN) használatával csökkentse a késést a felhasználók számára az egész világon, és csökkentse a tranzakciók számát a Storage-fiókba. A statikus tartalom felhőalapú tárolási szolgáltatásba történő üzembe helyezése csökkentheti a költséges számítási példányok szükségességét. További információ: [statikus tartalom üzemeltetési mintája](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

Cserélje le a helyőrzőt az `myStaticSite` Azure Storage-ban üzemeltetett webhely nevére. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra és az erőforráscsoport nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, és hozzáférést biztosít a Storage-fiókhoz az alábbihoz hasonló módon. A JSON-objektum másolása később.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

1. A [githubon](https://github.com/)tallózzon a tárházban.

1. Válassza a **beállítások > titkok > új titok** lehetőséget.

1. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adjon egy nevet a titoknak `AZURE_CREDENTIALS` .

    Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Munkafolyamat hozzáadása

1. Nyissa meg a GitHub-tárház **műveleteit** . 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub-műveletek menüelem":::

1. Válassza **a saját munkafolyamat beállítása** lehetőséget. 

1. Töröljön mindent a `on:` munkafolyamat-fájl szakasza után. Előfordulhat például, hogy a hátralévő munkafolyamat így néz ki. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Nevezze át a munkafolyamatot `Blob storage website CI` , és adja hozzá a pénztári és bejelentkezési műveleteket. Ezek a műveletek kiveszik a helykódot, és a korábban létrehozott GitHub-titok használatával hitelesítik magukat az Azure-ban `AZURE_CREDENTIALS` . 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Az Azure CLI művelettel feltöltheti a kódot a blob Storage-ba, és törölheti a CDN-végpontot. A (z) esetében `az storage blob upload-batch` cserélje le a helyőrzőt a Storage-fiók nevére. A szkript feltölti a `$web` tárolóba. A esetében `az cdn endpoint purge` cserélje le a helyőrzőket a CDN-profil nevére, a CDN-végpont nevére és az erőforráscsoporthoz.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Fejezze be a munkafolyamatot az Azure kijelentkezéséhez szükséges művelet hozzáadásával. Itt látható a befejezett munkafolyamat. Ekkor megjelenik a fájl a `.github/workflows` tárház mappájában.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Az üzemelő példány áttekintése

1. Nyissa meg a GitHub-tárház **műveleteit** . 

1. Az első eredmény megnyitásával tekintheti meg a munkafolyamat futtatásának részletes naplóit. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="A GitHub-műveletek futtatásának naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a statikus webhelyre és a GitHub-tárházra már nincs szükség, távolítsa el az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-tárház törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure statikus Web Apps](../../static-web-apps/index.yml)
