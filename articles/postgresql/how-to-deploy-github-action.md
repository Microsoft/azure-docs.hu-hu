---
title: 'Gyors útmutató: Kapcsolódás az Azure PostgreSQL-hez a GitHub-műveletekkel'
description: Az Azure PostgreSQL használata GitHub-műveletek munkafolyamatból
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97365115"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Rövid útmutató: a GitHub-műveletek használata az Azure PostgreSQL-hez való kapcsolódáshoz

<Token>**a következőkre vonatkozik:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – egyetlen kiszolgáló :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – rugalmas kiszolgáló</Token>

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions) egy munkafolyamattal, amely az adatbázis-frissítések [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/)való üzembe helyezésére használható.

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség:
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-adattár a mintaadatok ( `data.sql` ) szolgáltatással. Ha nem rendelkezik GitHub-fiókkal, [regisztráljon ingyenesen](https://github.com/join).
- Egy Azure Database for PostgreSQL-kiszolgáló.
    - [Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure Portalon](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A GitHub-műveletek munkafolyamatát egy YAML-fájl (. YML) határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl két részből áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Telepítés** | 1. Telepítse az adatbázist. |

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

Cserélje le a helyőrzőket `server-name` Az Azure-ban üzemeltetett PostgreSQL-kiszolgáló nevére. Cserélje le a és a értékét `subscription-id` `resource-group` a PostgreSQL-kiszolgálóhoz csatlakoztatott előfizetés-azonosítóval és erőforráscsoporthoz.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek az alábbihoz hasonló módon biztosítanak hozzáférést az adatbázishoz. Másolja ezt a kimeneti JSON-objektumot később.

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

## <a name="copy-the-postgresql-connection-string"></a>A PostgreSQL-kapcsolatok karakterláncának másolása

A Azure Portal lépjen a Azure Database for PostgreSQL-kiszolgálóhoz, és nyissa meg a **Beállítások**  >  **kapcsolódási karakterláncokat**. Másolja az **ADO.NET** kapcsolati sztringet. Cserélje le a és a helyőrző értékét `your_database` `your_password` . A kapcsolódási karakterlánc ehhez hasonlóan fog kinézni.

> [!IMPORTANT]
> - Egyetlen kiszolgáló használata esetén ```user=adminusername@servername```  . Vegye figyelembe ```@servername``` , hogy a szükséges.
> - Rugalmas kiszolgáló esetén használja a következőt: ```user= adminusername```  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

A kapcsolatok karakterláncát GitHub-titokként fogja használni.

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

1. Illessze be a kapcsolatok karakterlánc értékét a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet `AZURE_POSTGRESQL_CONNECTION_STRING` .


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

1. Nevezze át a munkafolyamatot `PostgreSQL for GitHub Actions` , és adja hozzá a pénztári és bejelentkezési műveleteket. Ezek a műveletek kiveszik a helykódot, és a korábban létrehozott GitHub-titok használatával hitelesítik magukat az Azure-ban `AZURE_CREDENTIALS` .

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

2. A PostgreSQL-példányhoz való kapcsolódáshoz használja az Azure PostgreSQL üzembe helyezési műveletét. Cserélje le a `POSTGRESQL_SERVER_NAME` nevet a kiszolgáló nevére. A tárház legfelső szintjén található PostgreSQL-adatfájllal kell rendelkeznie `data.sql` .

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Fejezze be a munkafolyamatot az Azure kijelentkezéséhez szükséges művelet hozzáadásával. Itt látható a befejezett munkafolyamat. Ekkor megjelenik a fájl a `.github/workflows` tárház mappájában.

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

1. Nyissa meg a GitHub-tárház **műveleteit** .

1. Az első eredmény megnyitásával tekintheti meg a munkafolyamat futtatásának részletes naplóit.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="A GitHub-műveletek futtatásának naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure PostgreSQL-adatbázisra és-tárházra már nincs szükség, távolítsa el az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-tárház törlésével.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg az Azure-t és a GitHub-integrációt](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Útmutató a kiszolgálóhoz való kapcsolódáshoz](how-to-connect-query-guide.md)
