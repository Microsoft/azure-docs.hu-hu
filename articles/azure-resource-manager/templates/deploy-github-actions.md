---
title: Resource Manager-sablonok üzembe helyezése GitHub-műveletek használatával
description: Útmutatás Azure Resource Manager sablonok (ARM-sablonok) a GitHub-műveletek használatával történő üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 243316b32d5b0cf62f03ae77d8a9fb919743ace1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172830"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>ARM-sablonok üzembe helyezése GitHub-műveletek használatával

A [GitHub-műveletek](https://docs.github.com/en/actions) a GitHub szolgáltatásainak egyik csomagja, amellyel automatizálható a szoftverfejlesztői munkafolyamatok ugyanazon a helyen, mint a kód tárolása és a lekéréses kérelmek és problémák közös használata.

A [Azure Resource Manager sablon központi telepítése művelettel](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) automatizálhatja Azure Resource Manager-sablon (ARM-sablon) Azure-ba való telepítését.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).
    - Egy GitHub-tárház, amely a Resource Manager-sablonokat és a munkafolyamat-fájlokat tárolja. A létrehozáshoz tekintse meg az [új Tárház létrehozása](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)című témakört.


## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl két részből áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Telepítés** | 1. Telepítse a Resource Manager-sablont. |

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása


Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

Hozzon létre egy erőforráscsoportot, ha még nem rendelkezik ilyennel.

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

Cserélje le a helyőrzőt az `myApp` alkalmazás nevére.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra és az erőforráscsoport nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később. Csak a `clientId` , `clientSecret` , `subscriptionId` és `tenantId` értékekkel rendelkező szakaszt kell megadnia.

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
> Mindig jó gyakorlat a minimális hozzáférés megadására. Az előző példában szereplő hatókör az erőforráscsoporthoz korlátozódik.



## <a name="configure-the-github-secrets"></a>A GitHub-titkok konfigurálása

Létre kell hoznia egy titkos kulcsot az Azure-beli hitelesítő adataihoz, erőforráscsoporthoz és előfizetéséhez.

1. A [githubon](https://github.com/)tallózzon a tárházban.

1. Válassza a **beállítások > titkok > új titok** lehetőséget.

1. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adja meg a titkot a nevet `AZURE_CREDENTIALS` .

1. Hozzon létre egy másik nevű titkot `AZURE_RG` . Adja hozzá az erőforráscsoport nevét a titkos kulcs értéke mezőhöz (például: `myResourceGroup` ).

1. Hozzon létre egy nevű további titkos kulcsot `AZURE_SUBSCRIPTION` . Adja hozzá az előfizetés-azonosítót a titkos kulcs értéke mezőhöz (például: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Resource Manager-sablon hozzáadása

Vegyen fel egy Resource Manager-sablont a GitHub-tárházba. Ez a sablon létrehoz egy Storage-fiókot.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

A fájlt bárhová is elhelyezheti a tárházban. A következő szakaszban szereplő munkafolyamat-minta azt feltételezi, hogy a sablonfájl neve **azuredeploy.json**, és a tárház gyökerében tárolódik.

## <a name="create-workflow"></a>Munkafolyamat létrehozása

A munkafolyamat-fájlt a tárház gyökerében található **. GitHub/munkafolyamatok** mappában kell tárolni. A munkafolyamat-fájlkiterjesztés lehet **. YML** vagy **. YAML**.

1. A GitHub-adattárból válassza a felső menü **műveletek** elemét.
1. Válassza az **Új munkafolyamat** lehetőséget.
1. Válassza **a munkafolyamat beállítása saját maga** lehetőséget.
1. Nevezze át a munkafolyamat-fájlt, ha a **Main. YML** helyett más nevet szeretne adni. Például: **deployStorageAccount. YML**.
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
    > Az ARM üzembe helyezési műveletben megadhat egy JSON formátumú paramétereket (például: `.azuredeploy.parameters.json` ).

    A munkafolyamat-fájl első szakasza a következőket tartalmazza:

    - **Name (név**): a munkafolyamat neve.
    - **bekapcsolva**: a munkafolyamatot kiváltó GitHub-események neve. A munkafolyamat akkor aktiválódik, ha van egy leküldéses esemény a fő ágban, amely módosítja a két megadott fájl legalább egyikét. A két fájl a munkafolyamat-fájl és a sablonfájl.

1. Válassza a **Start commit** (Véglegesítés indítása) lehetőséget.
1. Válassza **a végrehajtás közvetlenül a fő ágat** lehetőséget.
1. Válassza az **új fájl véglegesítés** (vagy a **módosítások elvégzése**) lehetőséget.

Mivel a munkafolyamatot úgy konfigurálták, hogy a munkafolyamat-fájl vagy a frissítendő sablonfájl aktiválja, a munkafolyamat a módosítások véglegesítése után azonnal elindul.

## <a name="check-workflow-status"></a>Munkafolyamat állapotának megtekintése

1. Válassza a **műveletek** fület. Ekkor megjelenik a **create deployStorageAccount. YML** munkafolyamat lista. A munkafolyamat futtatása 1-2 percet vesz igénybe.
1. Válassza ki a munkafolyamatot a megnyitásához.
1. Az üzembe helyezés ellenőrzéséhez válassza az **ARM-telepítés futtatása** lehetőséget a menüből.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha az erőforráscsoport és a tárház már nem szükséges, a telepített erőforrások tisztításához törölje az erőforráscsoportot és a GitHub-tárházat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első ARM-sablon létrehozása](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [A modul ismertetése: ARM-sablonok telepítésének automatizálása GitHub-műveletek használatával](/learn/modules/deploy-templates-command-line-github-actions/)
