---
title: 'Gyors útmutató: szinapszis-munkaterület létrehozása Azure PowerShell használatával'
description: Az útmutató lépéseinek követésével hozzon létre egy Azure szinapszis-munkaterületet a Azure PowerShell használatával.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675766"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Rövid útmutató: Azure szinapszis-munkaterület létrehozása Azure PowerShell

Azure PowerShell az Azure-erőforrások közvetlenül a PowerShellből való kezelésére szolgáló parancsmagok halmaza. Használhatja a böngészőjében az Azure Cloud Shell-lel. Azt is megteheti, hogy macOS, Linux vagy Windows rendszeren is telepíthető.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet a Azure PowerShell használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

- [Azure Data Lake Storage Gen2 Storage-fiók](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Az Azure szinapszis-munkaterületnek képesnek kell lennie olvasni és írni a kiválasztott ADLS Gen2 fiókot. Minden olyan Storage-fiókhoz, amelyet elsődleges Storage-fiókként csatol, engedélyeznie kell a **hierarchikus névteret** a Storage-fiók létrehozásakor a Storage-fiók [létrehozása](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)című témakörben leírtak szerint.

Ha a Cloud Shell használatát választja, további információt [a Azure Cloud Shell áttekintése](../cloud-shell/overview.md) című témakörben talál.

### <a name="install-the-azure-powershell-module-locally"></a>A Azure PowerShell modul helyi telepítése

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

A Azure PowerShell-hitelesítéssel kapcsolatos további információkért tekintse [meg a bejelentkezés a Azure PowerShellkal](/powershell/azure/authenticate-azureps)című témakört.

### <a name="install-the-azure-synapse-powershell-module"></a>Az Azure szinapszis PowerShell-modul telepítése

> [!IMPORTANT]
> Míg az az **. szinapszis** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Azure-beli szinapszis-munkaterület létrehozása Azure PowerShell használatával

1. Adja meg a szükséges környezeti változókat az Azure szinapszis-munkaterület erőforrásainak létrehozásához.

   |        Változó neve        |                                                 Leírás                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | A meglévő ADLS Gen2 Storage-fiók neve.                                                           |
   | StorageAccountResourceGroup | A meglévő ADLS Gen2 Storage-fiók erőforráscsoport neve.                                             |
   | FileShareName               | A meglévő Storage-fájlrendszer neve.                                                                  |
   | SynapseResourceGroup        | Válassza ki az Azure szinapszis-erőforráscsoport új nevét.                                                    |
   | Region                      | Válasszon egy Azure- [régiót](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Válasszon egyedi nevet az új Azure szinapszis-munkaterülethez.                                                  |
   | SqlUser                     | Válasszon egy értéket egy új felhasználónévhez.                                                                          |
   | SqlPassword                 | Válasszon biztonságos jelszót.                                                                                   |
   | Ügyfélip                    | Annak a rendszernek a nyilvános IP-címe, amelyen a PowerShellt futtatja.                                             |
   |                             |                                                                                                             |

1. Erőforráscsoport létrehozása tárolóként az Azure szinapszis-munkaterülethez:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Azure szinapszis-munkaterület létrehozása:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Webes és fejlesztői URL-cím beszerzése az Azure szinapszis-munkaterülethez:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Hozzon létre egy tűzfalszabály, amely lehetővé teszi az Azure szinapszis-munkaterület elérését a gépről:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Nyissa meg a munkaterület eléréséhez a környezeti változóban tárolt Azure szinapszis-munkaterület webes URL-címét `WorkspaceWeb` :

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure szinapszis-munkaterület webes felülete](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure szinapszis-munkaterület törléséhez kövesse az alábbi lépéseket.

> [!WARNING]
> Az Azure szinapszis-munkaterület törlésével a rendszer eltávolítja az elemzési motorokat és a tárolt SQL-készletek és munkaterület-metaadatok adatbázisában tárolt adatokat. A továbbiakban nem lesz lehetséges az SQL-vagy Apache Spark-végpontokhoz való kapcsolódás. Minden kódrészlet törölve lesz (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok). A munkaterület törlése **nem** befolyásolja a munkaterülethez csatolt Data Lake Store Gen2 lévő adatműveleteket.

Ha az ebben a cikkben létrehozott Azure szinapszis munkaterület nem szükséges, a következő példa futtatásával törölheti.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ezután [LÉTREHOZHAT SQL-készleteket](quickstart-create-sql-pool-studio.md) , vagy [létrehozhat Apache Spark készleteket](quickstart-create-apache-spark-pool-studio.md) az adatok elemzésének és vizsgálatának megkezdéséhez.