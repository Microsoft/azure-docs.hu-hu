---
title: 'Gyors útmutató: Kapcsolódás az Azure MySQL-hez a GitHub-műveletekkel'
description: Az Azure MySQL használata GitHub-műveletek munkafolyamatból
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: c4e21267c5eee9d86c05c51bc57bebfee699ef2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200590"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Rövid útmutató: a GitHub-műveletek használata az Azure MySQL-hez való kapcsolódáshoz

**A** következőkre vonatkozik: :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL – egyetlen kiszolgáló :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL – rugalmas kiszolgáló

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions) egy munkafolyamattal, amely az adatbázis-frissítések [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)való üzembe helyezésére használható.


## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség: 
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-adattár a mintaadatok ( `data.sql` ) szolgáltatással. Ha nem rendelkezik GitHub-fiókkal, [regisztráljon ingyenesen](https://github.com/join).  
- Egy Azure Database for MySQL-kiszolgáló.
    - [Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása a Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A GitHub-műveletek munkafolyamatát egy YAML-fájl (. YML) határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl két részből áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Telepítés** | 1. Telepítse az adatbázist. |

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

Cserélje le a helyőrzőket az Azure-ban `server-name` üzemeltetett MySQL-kiszolgáló nevére. Cserélje le a és a értékét `subscription-id` `resource-group` a MySQL-kiszolgálóhoz csatlakoztatott előfizetés-azonosítóra és erőforrás-csoportra.  

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

## <a name="copy-the-mysql-connection-string"></a>A MySQL-kapcsolatok karakterláncának másolása 

A Azure Portal lépjen a Azure Database for MySQL-kiszolgálóhoz, és nyissa meg a **Beállítások**  >  **kapcsolódási karakterláncokat**. Másolja az **ADO.NET** kapcsolati sztringet. Cserélje le a és a helyőrző értékét `your_database` `your_password` . A kapcsolódási karakterlánc ehhez hasonlóan fog kinézni. 

> [!IMPORTANT]
> - Egyetlen kiszolgáló esetén használja az **UID adminusername@servername =**. Vegye figyelembe **@servername** , hogy a szükséges.
> - Rugalmas kiszolgáló esetén használja a **UID = AdminUsername** -t a nélkül @servername . Vegye figyelembe, hogy a MySQL rugalmas kiszolgáló előzetes verzióban érhető el. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
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

1. Illessze be a kapcsolatok karakterlánc értékét a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet `AZURE_MYSQL_CONNECTION_STRING` .


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

1. Nevezze át a munkafolyamatot `MySQL for GitHub Actions` , és adja hozzá a pénztári és bejelentkezési műveleteket. Ezek a műveletek kiveszik a helykódot, és a korábban létrehozott GitHub-titok használatával hitelesítik magukat az Azure-ban `AZURE_CREDENTIALS` . 

    ```yaml
    name: MySQL for GitHub Actions

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

1. A MySQL-példányhoz való kapcsolódáshoz használja az Azure MySQL üzembe helyezési műveletét. Cserélje le a `MYSQL_SERVER_NAME` nevet a kiszolgáló nevére. Rendelkeznie kell egy nevű MySQL-adatfájllal `data.sql` a tárház legfelső szintjén. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Fejezze be a munkafolyamatot az Azure kijelentkezéséhez szükséges művelet hozzáadásával. Itt látható a befejezett munkafolyamat. Ekkor megjelenik a fájl a `.github/workflows` tárház mappájában.

    ```yaml
    name: MySQL for GitHub Actions

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

            - uses: azure/mysql@v1
                with:
                    server-name: MYSQL_SERVER_NAME
                    connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
                    sql-file: './data.sql'

            # Azure logout 
            - name: logout
                run: |
                    az logout
    ```

## <a name="review-your-deployment"></a>Az üzemelő példány áttekintése

1. Nyissa meg a GitHub-tárház **műveleteit** . 

1. Az első eredmény megnyitásával tekintheti meg a munkafolyamat futtatásának részletes naplóit. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="A GitHub-műveletek futtatásának naplója":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure MySQL-adatbázisra és-tárházra már nincs szükség, távolítsa el az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-tárház törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg az Azure-t és a GitHub-integrációt](/azure/developer/github/)
