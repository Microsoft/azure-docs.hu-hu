---
title: 'Rövid útmutató: Csatlakozás az Azure PostgreSQL-hez GitHub Actions'
description: Az Azure PostgreSQL használata GitHub Actions munkafolyamatból
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 7fc59c0d9036a2e83c742f51fc17750d40e057fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791426"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Rövid útmutató: GitHub Actions Azure PostgreSQL-hez való csatlakozáshoz

<Token>**A KÖVETKEZŐKRE VONATKOZIK:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Egykiszolgálós Azure Database for PostgreSQL – Rugalmas kiszolgáló</Token>

A GitHub Actions [használatának](https://docs.github.com/en/actions) első lépésekéhez egy munkafolyamattal telepítse az adatbázis-frissítéseket a [Azure Database for PostgreSQL.](https://azure.microsoft.com/services/postgresql/)

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség:
- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub-adattár mintaadatokkal ( `data.sql` ). Ha még nincs GitHub-fiókja, [regisztráljon ingyenesen.](https://github.com/join)
- Egy Azure Database for PostgreSQL kiszolgáló.
    - [Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure Portalon](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A GitHub Actions munkafolyamatot egy YAML- (.yml-) fájl definiálja az `/.github/workflows/` adattár elérési útján. Ez a definíció a munkafolyamatot felhozó különböző lépéseket és paramétereket tartalmazza.

A fájl két szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév meghatározása. <br /> 2. Hozzon létre egy titkos GitHub-et. |
|**Telepítés** | 1. Az adatbázis üzembe helyezése. |

## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Szolgáltatásnév az [](../active-directory/develop/app-objects-and-service-principals.md) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a Próbálja **ki gombot** választva.

Cserélje le a helyőrzőket `server-name` az Azure-ban üzemeltetett PostgreSQL-kiszolgáló nevére. Cserélje le a `subscription-id` és a helyére a PostgreSQL-kiszolgálóhoz csatlakoztatott előfizetés-azonosítót `resource-group` és erőforráscsoportot.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek az alábbihoz hasonló hozzáférést biztosítanak az adatbázishoz. Másolja ezt a kimeneti JSON-objektumot későbbire.

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

## <a name="copy-the-postgresql-connection-string"></a>A PostgreSQL kapcsolati sztring másolása

A Azure Portal nyissa meg a saját Azure Database for PostgreSQL, és nyissa meg **a Settings** Connection strings (Beállítások  >  **kapcsolati sztringek) et.** Másolja az **ADO.NET** kapcsolati sztringet. Cserélje le a és a `your_database` helyőrzőértékét. `your_password` A kapcsolati sztring ehhez hasonlóan fog kinézni.

> [!IMPORTANT]
> - Egyetlen kiszolgálóhoz használja a ```user=adminusername@servername```  et. Vegye figyelembe, ```@servername``` hogy a kötelező.
> - Rugalmas kiszolgálóhoz használja a ```user= adminusername``` et a  ```@servername``` nélkül.

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

A kapcsolati sztringet GitHub-titkos kódként fogja használni.

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

1. Illessze be a kapcsolati sztring értékét a titkos gombra. Adja a titkos nak a `AZURE_POSTGRESQL_CONNECTION_STRING` nevet.


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

1. Nevezze át a munkafolyamatot, és adja hozzá a `PostgreSQL for GitHub Actions` kijelentkezés és a bejelentkezési műveleteket. Ezek a műveletek le fogják ellenőrizni a webhely kódját, és a korábban létrehozott GitHub-titkos kóddal hitelesítik magukat az `AZURE_CREDENTIALS` Azure-ban.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. A PostgreSQL-példányhoz való csatlakozáshoz használja az Azure PostgreSQL Üzembe helyezés műveletét. Cserélje `POSTGRESQL_SERVER_NAME` le a helyére a kiszolgáló nevét. Az adattár gyökérszintén egy nevű PostgreSQL-adatfájlnak `data.sql` kell lennie.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. A munkafolyamat befejezéséhez egy műveletet kell hozzáadnia az Azure-kijelentkezéshez. Itt a befejezett munkafolyamat. A fájl az adattár `.github/workflows` mappájában fog megjelenni.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Az üzemelő példány áttekintése

1. Az **Actions** for your GitHub repository (Műveletek a GitHub-adattárhoz) oldalon.

1. Nyissa meg az első eredményt a munkafolyamat futtatásának részletes naplói megtekintéséhez.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Futtatott GitHub-műveletek naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure PostgreSQL-adatbázisra és -adattárra már nincs szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az Azure és a GitHub integrációjáról](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Útmutató a kiszolgálóhoz való csatlakozáshoz](how-to-connect-query-guide.md)
