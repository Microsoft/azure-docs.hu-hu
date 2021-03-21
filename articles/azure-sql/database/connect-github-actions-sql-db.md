---
title: 'Gyors útmutató: Kapcsolódás a Azure SQL Databasehoz GitHub-műveletekkel'
description: Az Azure SQL használata GitHub-műveletek munkafolyamatból
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 335879af93834665985fe2c14ce3cbd827387920
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172139"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>A GitHub-műveletek használata a Azure SQL Databasehoz való kapcsolódáshoz

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions) egy munkafolyamattal, amely az adatbázis-frissítések [Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md)való üzembe helyezésére használható. 

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség: 
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-adattár dacpac-csomaggal ( `Database.dacpac` ). Ha nem rendelkezik GitHub-fiókkal, [regisztráljon ingyenesen](https://github.com/join).  
- Egy Azure SQL Database.
    - [Rövid útmutató: Azure SQL Database önálló adatbázis létrehozása](single-database-create-quickstart.md)
    - [Dacpac-csomag létrehozása meglévő SQL Server adatbázisból](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A GitHub-műveletek munkafolyamatát egy YAML-fájl (. YML) határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl két részből áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Telepítés** | 1. Telepítse az adatbázist. |

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../../active-directory/develop/app-objects-and-service-principals.md) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

Cserélje le a helyőrzőket `server-name` Az Azure-ban üzemeltetett SQL-kiszolgáló nevére. Cserélje le az és az értékét `subscription-id` `resource-group` az SQL Serverhez csatlakoztatott előfizetés-azonosítóval és erőforráscsoporthoz.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A kimenet egy JSON-objektum, amelynek a szerepkör-hozzárendelési hitelesítő adatai megadják a hozzáférést az adatbázishoz ehhez a példához hasonló módon. Másolja a kimeneti JSON-objektumot később.

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
> Mindig jó gyakorlat a minimális hozzáférés megadására. Az előző példában szereplő hatókör az adott kiszolgálóra korlátozódik, nem a teljes erőforráscsoporthoz.

## <a name="copy-the-sql-connection-string"></a>Az SQL-alapú kapcsolatok karakterláncának másolása 

A Azure Portal lépjen a Azure SQL Database, és nyissa meg a **Beállítások**  >  **kapcsolódási karakterláncok** lehetőséget. Másolja az **ADO.NET** kapcsolati sztringet. Cserélje le a és a helyőrző értékét `your_database` `your_password` . A kapcsolódási karakterlánc a kimenethez hasonlóan fog kinézni. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

A kapcsolatok sztringjét GitHub-titokként fogja használni. 

## <a name="configure-the-github-secrets"></a>A GitHub-titkok konfigurálása

1. A [githubon](https://github.com/)tallózzon a tárházban.

1. Válassza a **beállítások > titkok > új titok** lehetőséget.

1. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adja meg a titkot a nevet `AZURE_CREDENTIALS` .

    Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Válassza újra az **új titkos kulcsot** . 

1. Illessze be a kapcsolatok karakterlánc értékét a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet `AZURE_SQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Munkafolyamat hozzáadása

1. Nyissa meg a GitHub-tárház **műveleteit** . 

2. Válassza **a saját munkafolyamat beállítása** lehetőséget. 

2. Töröljön mindent a `on:` munkafolyamat-fájl szakasza után. Előfordulhat például, hogy a hátralévő munkafolyamat így néz ki. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Nevezze át a munkafolyamatot `SQL for GitHub Actions` , és adja hozzá a pénztári és bejelentkezési műveleteket. Ezek a műveletek kiveszik a helykódot, és a korábban létrehozott GitHub-titok használatával hitelesítik magukat az Azure-ban `AZURE_CREDENTIALS` . 

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

1. Az SQL-példányhoz való kapcsolódáshoz használja az Azure SQL Deploy műveletet. Cserélje le a `SQL_SERVER_NAME` nevet a kiszolgáló nevére. A tárház legfelső szintjén kell lennie egy dacpac-csomagnak ( `Database.dacpac` ). 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Fejezze be a munkafolyamatot az Azure kijelentkezéséhez szükséges művelet hozzáadásával. Itt látható a befejezett munkafolyamat. Ekkor megjelenik a fájl a `.github/workflows` tárház mappájában.

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

1. Nyissa meg a GitHub-tárház **műveleteit** . 

1. Az első eredmény megnyitásával tekintheti meg a munkafolyamat futtatásának részletes naplóit. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="A GitHub-műveletek futtatásának naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure SQL Database-adatbázisra és-tárházra már nincs szükség, távolítsa el az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-tárház törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg az Azure-t és a GitHub-integrációt](/azure/developer/github/)