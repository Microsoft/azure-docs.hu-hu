---
title: Üzembe Resource Manager sablonokat a GitHub Actions
description: Leírja, hogyan helyezhet üzembe Azure Resource Manager sablonokat (ARM-sablonokat) a GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781972"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>ARM-sablonok üzembe helyezése a GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) a GitHub egy olyan szolgáltatáscsomagja, amely ugyanazon a helyen automatizálja a szoftverfejlesztési munkafolyamatokat, ahol a kódot tárolja, és együttműködik a lekéréses kérelmeken és problémákon.

A [Deploy Azure Resource Manager Template Action](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) (Sablon üzembe helyezése) művelettel automatizálhatja egy Azure Resource Manager-sablon (ARM-sablon) Azure-ban való üzembe helyezését.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nincs ilyen szolgáltatása, regisztráljon [ingyenesen.](https://github.com/join)
    - Egy GitHub-adattár, amely tárolja a Resource Manager sablonokat és a munkafolyamat-fájlokat. Ennek létrehozásához lásd: [Új adattár létrehozása.](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)


## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A munkafolyamatokat az adattár elérési útján található YAML- (.yml-) fájl `/.github/workflows/` határozza meg. Ez a definíció a munkafolyamatot felhozó különböző lépéseket és paramétereket tartalmazza.

A fájl két szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév meghatározása. <br /> 2. Hozzon létre egy titkos GitHub-et. |
|**Telepítés** | 1. A Resource Manager üzembe helyezése. |

## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása


Szolgáltatásnév az [](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a Próbálja **ki gombot** választva.

Hozzon létre egy erőforráscsoportot, ha még nem rendelkezik ilyen erőforrással.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Cserélje le a helyőrzőt `myApp` az alkalmazás nevére.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-azonosítójára és az erőforráscsoport nevére. A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek hozzáférést biztosítanak App Service alkalmazáshoz az alábbihoz hasonlóan. Másolja ezt a JSON-objektumot későbbire. Csak a , , és értékekkel megadott `clientId` `clientSecret` `subscriptionId` szakaszokra lesz `tenantId` szüksége.

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
> Mindig jó gyakorlat a minimális hozzáférés megadása. Az előző példában a hatókör az erőforráscsoportra korlátozódik.



## <a name="configure-the-github-secrets"></a>A GitHub titkos kód konfigurálása

Titkos adatokat kell létrehoznia az Azure-beli hitelesítő adataihoz, az erőforráscsoporthoz és az előfizetéséhez.

1. A [GitHubon](https://github.com/)keresse meg az adattárat.

1. Válassza **a Beállítások > Titkos kulcsok és > lehetőséget.**

1. Illessze be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. Adja a titkos nak a `AZURE_CREDENTIALS` nevet.

1. Hozzon létre egy másik titkos `AZURE_RG` gombra. Adja hozzá az erőforráscsoport nevét a titkos gombra vonatkozó értékmezőhöz (például: `myResourceGroup` ).

1. Hozzon létre egy nevű további titkos `AZURE_SUBSCRIPTION` gombra. Adja hozzá az előfizetés-azonosítóját a titkos kód értékmezőjéhez (például: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Sablon Resource Manager hozzáadása

Adjon hozzá Resource Manager sablont a GitHub-adattárhoz. Ez a sablon létrehoz egy tárfiókot.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

A fájlt bárhol az adattárban lehet eltenni. A következő szakaszban található munkafolyamat-minta feltételezi, hogy a sablonfájl neve **azuredeploy.js** a fájlban, és az adattár gyökerében van tárolva.

## <a name="create-workflow"></a>Munkafolyamat létrehozása

A munkafolyamat-fájlt az adattár gyökerében, **a .github/workflows** mappában kell tárolni. A munkafolyamat fájlkiterjesztése **.yml** vagy **.yaml lehet.**

1. A GitHub-adattárban válassza a **felső menü Műveletek** parancsát.
1. Válassza **az Új munkafolyamat lehetőséget.**
1. Válassza **a Munkafolyamat saját beállítása lehetőséget.**
1. Nevezze át a munkafolyamat-fájlt, ha a **main.yml** névtől eltérő nevet szeretne. Például: **deployStorageAccount.yml**.
1. Az .yml-fájl tartalmát írja felül a következővel:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Ehelyett megadhat egy JSON-formátumparaméter-fájlt az ARM-üzembe helyezési műveletben (például: `.azuredeploy.parameters.json` ).

    A munkafolyamat-fájl első szakasza a következőket tartalmazza:

    - **name**: A munkafolyamat neve.
    - **on**: A munkafolyamatot aktiváló GitHub-események neve. A munkafolyamat akkor aktiválódik, ha leküldéses esemény van a főágon, amely a megadott két fájl közül legalább egyet módosít. A két fájl a munkafolyamat-fájl és a sablonfájl.

1. Válassza a **Start commit** (Véglegesítés indítása) lehetőséget.
1. Válassza **a Commit directly to the main branch (Véglegesítés közvetlenül a főágba) lehetőséget.**
1. Válassza **az Új fájl véglegesítése** (vagy Módosítások **véglegesítése) lehetőséget.**

Mivel a munkafolyamat úgy van konfigurálva, hogy a munkafolyamat-fájl vagy a frissített sablonfájl aktiválja, a munkafolyamat a módosítások véglegesítését követően azonnal elindul.

## <a name="check-workflow-status"></a>Munkafolyamat állapotának ellenőrzése

1. Válassza a **Műveletek** lapot. Megjelenik egy **Create deployStorageAccount.yml munkafolyamat.** A munkafolyamat futtatása 1–2 percet vesz igénybe.
1. Válassza ki a megnyitni kívánt munkafolyamatot.
1. Válassza **az ARM üzembe helyezésének** futtatása lehetőséget a menüben az üzembe helyezés ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha az erőforráscsoportra és az adattárra már nincs szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első ARM-sablon létrehozása](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Learn-modul: ARM-sablonok üzembe helyezésének automatizálása a GitHub Actions](/learn/modules/deploy-templates-command-line-github-actions/)
