---
title: 'Rövid útmutató: szinapszis-munkaterület létrehozása az Azure CLI használatával'
description: Hozzon létre egy Azure szinapszis-munkaterületet az Azure CLI használatával az útmutató lépéseit követve.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 8a56b325dd5e1180b1229465965167241fab76a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676463"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Rövid útmutató: Azure szinapszis-munkaterület létrehozása az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le és telepítse a [jQ](https://stedolan.github.io/jq/download/)-t, amely egy egyszerű és rugalmas parancssori JSON-processzor
- [Azure Data Lake Storage Gen2 Storage-fiók](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Az Azure szinapszis-munkaterületnek képesnek kell lennie olvasni és írni a kiválasztott ADLS Gen2 fiókot. Továbbá minden olyan Storage-fiókhoz, amelyet elsődleges Storage-fiókként csatol, a Storage-fiók [létrehozásához](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) engedélyezni kell a **hierarchikus névteret** a Storage-fiók létrehozása lapon leírtak szerint. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Azure-beli szinapszis-munkaterület létrehozása az Azure CLI használatával

1. Adja meg a szükséges környezeti változókat az Azure szinapszis-munkaterület erőforrásainak létrehozásához.

    | Környezeti változó neve | Leírás |
    |---|---|---|
    |StorageAccountName| A meglévő ADLS Gen2 Storage-fiók neve.|
    |StorageAccountResourceGroup| A meglévő ADLS Gen2 Storage-fiók erőforráscsoport neve. |
    |FileShareName| A meglévő Storage-fájlrendszer neve.|
    |SynapseResourceGroup| Válassza ki az Azure szinapszis-erőforráscsoport új nevét. |
    |Region| Válasszon egy Azure- [régiót](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Válasszon egyedi nevet az új Azure szinapszis-munkaterülethez. |
    |SqlUser| Válasszon egy értéket egy új felhasználónévhez.|
    |SqlPassword| Válasszon biztonságos jelszót.|
    |||

1. Erőforráscsoport létrehozása tárolóként az Azure szinapszis-munkaterülethez:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```

1. Azure szinapszis-munkaterület létrehozása:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

1. Webes és fejlesztői URL-cím beszerzése az Azure szinapszis-munkaterülethez:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

1. Hozzon létre egy tűzfalszabály, amely lehetővé teszi az Azure szinapszis-munkaterület elérését a gépről:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

1. Nyissa meg a munkaterület eléréséhez a környezeti változóban tárolt Azure szinapszis-munkaterület webes URL-címét `WorkspaceWeb` :

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Azure szinapszis-munkaterület webes ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) felülete](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure szinapszis-munkaterület törléséhez kövesse az alábbi lépéseket.
> [!WARNING]
> Az Azure szinapszis-munkaterület törlésével a rendszer eltávolítja az elemzési motorokat és a tárolt SQL-készletek és munkaterület-metaadatok adatbázisában tárolt adatokat. A továbbiakban nem lesz lehetséges az SQL-vagy Apache Spark-végpontokhoz való kapcsolódás. Minden kódrészlet törölve lesz (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok).
>
> A munkaterület törlése **nem** befolyásolja a munkaterülethez csatolt Data Lake Store Gen2 lévő adatműveleteket.

Ha törölni szeretné az Azure szinapszis munkaterületet, hajtsa végre a következő parancsot:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ezután [LÉTREHOZHAT SQL-készleteket](quickstart-create-sql-pool-studio.md) , vagy [létrehozhat Apache Spark készleteket](quickstart-create-apache-spark-pool-studio.md) az adatok elemzésének és vizsgálatának megkezdéséhez.