---
title: 'Rövid útmutató: Csatlakozás Azure SQL Database GitHub Actions'
description: A Azure SQL munkafolyamatból származó GitHub Actions használata
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ceb9f0f9ef2a88532d5af16a03fcfd0282da84f8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787286"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Csatlakozás GitHub Actions a Azure SQL Database

Az GitHub Actions [használatának](https://docs.github.com/en/actions) első lépésekéhez egy munkafolyamattal telepítse az adatbázis-frissítéseket a [Azure SQL Database.](../azure-sql-iaas-vs-paas-what-is-overview.md) 

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség: 
- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-adattár dacpac csomaggal ( `Database.dacpac` ). Ha még nincs GitHub-fiókja, [regisztráljon ingyenesen.](https://github.com/join)  
- Egy Azure SQL Database.
    - [Rövid útmutató: Azure SQL Database létrehozása](single-database-create-quickstart.md)
    - [Dacpac-csomag létrehozása meglévő adatbázisból SQL Server adatbázisból](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A GitHub Actions munkafolyamatot egy YAML- (.yml-) fájl definiálja az `/.github/workflows/` adattár elérési útján. Ez a definíció a munkafolyamatot felhozó különböző lépéseket és paramétereket tartalmazza.

A fájl két szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév meghatározása. <br /> 2. Hozzon létre egy titkos GitHub-et. |
|**Telepítés** | 1. Az adatbázis üzembe helyezése. |

## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Szolgáltatásnév az [](../../active-directory/develop/app-objects-and-service-principals.md) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a **Kipróbálom gombra kattintva.**

Cserélje le a helyőrzőket `server-name` az Azure-ban üzemeltetett SQL-kiszolgáló nevére. Cserélje le a és a helyére az SQL-kiszolgálóhoz csatlakoztatott előfizetés-azonosítót `subscription-id` `resource-group` és erőforráscsoportot.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek az alábbi példához hasonló hozzáférést biztosítanak az adatbázishoz. Másolja a kimeneti JSON-objektumot későbbire.

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
> Mindig jó gyakorlat a minimális hozzáférés megadása. Az előző példában a hatókör az adott kiszolgálóra korlátozódik, nem a teljes erőforráscsoportra.

## <a name="copy-the-sql-connection-string"></a>Az SQL-kapcsolati sztring másolása 

A Azure Portal nyissa meg a saját Azure SQL Database a **Beállítások**  >  **kapcsolati sztringek gombra.** Másolja az **ADO.NET** kapcsolati sztringet. Cserélje le a és a `your_database` helyőrzőértékét. `your_password` A kapcsolati sztring ehhez a kimenethez hasonlóan fog kinézni. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

A kapcsolati sztringet GitHub-titokként fogja használni. 

## <a name="configure-the-github-secrets"></a>A GitHub titkos kód konfigurálása

1. A [GitHubon](https://github.com/)keresse meg az adattárat.

1. Válassza **a Beállítások > Titkos kulcsok és > lehetőséget.**

1. Illessze be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. Adja a titkos nak a `AZURE_CREDENTIALS` nevet.

    Amikor később konfigurálja a munkafolyamat-fájlt, a titkos adatokat kell használnia az `creds` Azure Login művelet bemenetéhez. Például:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Válassza **ismét a New secret (Új titkos stb.)** lehetőséget. 

1. Illessze be a kapcsolati sztring értékét a titkos gombra. Adja a titkos nak a `AZURE_SQL_CONNECTION_STRING` nevet.


## <a name="add-your-workflow"></a>A munkafolyamat hozzáadása

1. Az **Actions** for your GitHub repository (Műveletek a GitHub-adattárhoz) oldalon. 

2. Válassza **a Munkafolyamat saját beállítása lehetőséget.** 

2. Töröljön mindent a `on:` munkafolyamat-fájl szakasza után. A fennmaradó munkafolyamat például az alábbihoz hasonló lehet. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Nevezze át a munkafolyamatot, és adja hozzá a `SQL for GitHub Actions` kijelentkezés és a bejelentkezési műveleteket. Ezek a műveletek le fogják ellenőrizni a webhely kódját, és a korábban létrehozott GitHub-titkos kóddal hitelesítik magukat az `AZURE_CREDENTIALS` Azure-ban. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Az SQL Azure SQL példányhoz való csatlakozáshoz használja a Azure SQL Deploy (Üzembe helyezés) műveletet. Cserélje `SQL_SERVER_NAME` le a helyére a kiszolgáló nevét. Az adattár gyökérszintén egy dacpac csomagnak ( `Database.dacpac` ) kell lennie. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. A munkafolyamat befejezéséhez egy műveletet kell hozzáadnia az Azure-kijelentkezéshez. Itt a befejezett munkafolyamat. A fájl az adattár `.github/workflows` mappájában fog megjelenni.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Az üzemelő példány áttekintése

1. Az **Actions** for your GitHub repository (Műveletek a GitHub-adattárhoz) oldalon. 

1. Nyissa meg az első eredményt a munkafolyamat futtatásának részletes naplói megtekintéséhez. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Futtatott GitHub-műveletek naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure SQL és az adattárra már nincs szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az Azure és a GitHub integrációjáról](/azure/developer/github/)