---
title: A GitHub Actions használata statikus hely üzembe helyezéséhez az Azure Storage-ben
description: Statikus Azure Storage-webhely üzemeltetése GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ae0904eda2608026ad09ba8b8993008380725f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788528"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>A statikus webhely Azure Storage GitHub Actions ban való üzembe helyezéséhez GitHub Actions munkafolyamat beállítása

A GitHub Actions [használatának](https://docs.github.com/en/actions) első lépésekéhez egy munkafolyamattal üzembe helyezhet egy statikus helyet egy Azure Storage-fiókban. Miután beállította a GitHub Actions munkafolyamatot, automatikusan üzembe helyezheti a webhelyet az Azure-ban a GitHubról, amikor módosítja a webhely kódját.

> [!NOTE]
> Ha [a(Azure Static Web Apps,](../../static-web-apps/index.yml)akkor nem kell manuálisan beállítania egy GitHub Actions munkafolyamatot.
> Azure Static Web Apps automatikusan létrehoz egy GitHub Actions munkafolyamatot. 

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés és GitHub-fiók. 

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-adattár a statikus webhely kódával. Ha még nincs GitHub-fiókja, [regisztráljon ingyenesen.](https://github.com/join)  
- Az Azure Storage-ban üzemeltetett, működő statikus webhely. Megtudhatja, hogyan [lehet statikus webhelyet az Azure Storage-ban üzembe stb.](storage-blob-static-website-how-to.md) A példa bevetéséhez a következőt is üzembe [Azure CDN:](static-website-content-delivery-network.md).

> [!NOTE]
> Gyakran használják a tartalomkom kézbesítő hálózatot (CDN) a felhasználók késésének csökkentésére világszerte, valamint a tárfiók tranzakcióinak számának csökkentésére. A statikus tartalom felhőalapú tárolási szolgáltatásban való üzembe helyezése csökkentheti a potenciálisan drága számítási példányok igényét. További információ: [Statikus tartalom üzemeltetési mintája.](/azure/architecture/patterns/static-content-hosting)

## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Szolgáltatásnév az [](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a **Kipróbálom gombra kattintva.**

Cserélje le a helyőrzőt `myStaticSite` az Azure Storage-ban üzemeltetett webhely nevére. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-azonosítójára és az erőforráscsoport nevére. A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek az alábbihoz hasonló hozzáférést biztosítanak a tárfiókhoz. Másolja ezt a JSON-objektumot későbbire.

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
> Mindig jó eljárás a minimális hozzáférés megadása. Az előző példában a hatókör nem a teljes erőforráscsoportra, App Service alkalmazásra van korlátozva.

## <a name="configure-the-github-secret"></a>A GitHub titkos kód konfigurálása

1. A [GitHubon](https://github.com/)tallózhat az adattárban.

1. Válassza **a Beállítások > Titkos kulcsok és > lehetőséget.**

1. Illessze be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. Adjon a titkos nak egy nevet, `AZURE_CREDENTIALS` például: .

    Amikor később konfigurálja a munkafolyamat-fájlt, a titkos adatokat használja az `creds` Azure Login művelet bemenetéhez. Például:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>A munkafolyamat hozzáadása

1. Az **Actions** for your GitHub repository (Műveletek a GitHub-adattárhoz) oldalon. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="A GitHub Actions menüeleme":::

1. Válassza **a Munkafolyamat saját beállítása lehetőséget.** 

1. Töröljön mindent a `on:` munkafolyamat-fájl szakasza után. A fennmaradó munkafolyamat például az alábbihoz hasonló lehet. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Nevezze át a munkafolyamatot, és adja hozzá a `Blob storage website CI` kijelentkezés és a bejelentkezési műveleteket. Ezek a műveletek le fogják ellenőrizni a webhely kódját, és a korábban létrehozott GitHub-titkos kóddal hitelesítik magukat az `AZURE_CREDENTIALS` Azure-ban. 

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

1. Az Azure CLI-művelettel feltöltheti a kódot a Blob Storage-ba, és kiürítheti a CDN-végpontot. A `az storage blob upload-batch` fájlban cserélje le a helyőrzőt a tárfiók nevére. A szkript feltölti a fájlt a `$web` tárolóba. A `az cdn endpoint purge` fájlban cserélje le a helyőrzőket a CDN-profil nevére, a CDN-végpont nevére és az erőforráscsoportra.

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

1. A munkafolyamat befejezéséhez egy műveletet kell hozzáadnia az Azure-ba való kijelentkezéshez. Itt a kész munkafolyamat. A fájl megjelenik az `.github/workflows` adattár mappájában.

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

1. A **GitHub-adattárhoz** ugrás az Actions (Műveletek) útjára. 

1. Nyissa meg az első eredményt a munkafolyamat futtatásának részletes naplói megtekintéséhez. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="A futtatott GitHub-műveletek naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a statikus webhelyre és a GitHub-adattárra már nincs szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a Azure Static Web Apps](../../static-web-apps/index.yml)
