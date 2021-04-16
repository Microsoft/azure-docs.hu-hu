---
title: 'Rövid útmutató: Synapse-munkaterület létrehozása Azure PowerShell'
description: Hozzon létre Azure Synapse munkaterületet Azure PowerShell az útmutató lépéseit követve.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f02833ef7497c34b72db6b858a51c6046bbf3df
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567570"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Rövid útmutató: Azure Synapse-munkaterület létrehozása Azure PowerShell

Azure PowerShell egy parancsmagkészlet, amely közvetlenül a PowerShellből kezeli az Azure-erőforrásokat. Használhatja a böngészőjében az Azure Cloud Shell-lel. MacOS, Linux vagy Windows rendszeren is telepítheti.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Synapse-munkaterületet a Azure PowerShell.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

- [Azure Data Lake Storage Gen2 tárfiók létrehozása](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > A Azure Synapse munkaterületnek képesnek kell lennie olvasni és írni a kiválasztott ADLS Gen2 fiókba. Az elsődleges tárfiókként összekapcsolt tárfiókok számára engedélyeznie kell a **hierarchikus** névteret a tárfiók létrehozásakor a [Tárfiók létrehozása alatt](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)leírtak szerint.

Ha úgy dönt, hogy a Cloud Shell használja, további információt [a](../cloud-shell/overview.md) Azure Cloud Shell áttekintésében talál.

### <a name="install-the-azure-powershell-module-locally"></a>Az Azure PowerShell modul helyi telepítése

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps).

További információ az Azure PowerShell való hitelesítésről: [Bejelentkezés a Azure PowerShell.](/powershell/azure/authenticate-azureps)

### <a name="install-the-azure-synapse-powershell-module"></a>Az Azure Synapse PowerShell-modul telepítése

> [!IMPORTANT]
> Bár az **Az.Synapse** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Munkaterület Azure Synapse a Azure PowerShell

1. Definiálja a szükséges környezeti változókat az erőforrások létrehozásához Azure Synapse munkaterülethez.

   |        Változó neve        |                                                 Description                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | A meglévő tárfiók ADLS Gen2 neve.                                                           |
   | StorageAccountResourceGroup | A meglévő tárfiók ADLS Gen2 erőforráscsoport neve.                                             |
   | FileShareName (Fájlmegosztás neve)               | A meglévő tároló fájlrendszerének neve.                                                                  |
   | SynapseResourceGroup        | Válasszon egy új nevet a Azure Synapse erőforráscsoportnak.                                                    |
   | Region                      | Válasszon egyet az [Azure-régiók közül.](https://azure.microsoft.com/global-infrastructure/geographies/#overview) |
   | SynapseWorkspaceName        | Válasszon egyedi nevet az új munkaterület Azure Synapse számára.                                                  |
   | SqlUser                     | Válasszon egy értéket egy új felhasználónévhez.                                                                          |
   | SqlPassword                 | Válasszon egy biztonságos jelszót.                                                                                   |
   | ClientIP                    | Annak a rendszernek a nyilvános IP-címe, amelyről a PowerShellt futtatja.                                             |
   |                             |                                                                                                             |

1. Hozzon létre egy erőforráscsoportot tárolóként a Azure Synapse számára:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Hozzon létre egy Azure Synapse munkaterületet:

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

1. Webes és fejlesztői URL-cím le Azure Synapse munkaterülethez:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Hozzon létre egy tűzfalszabályt, amely engedélyezi a Azure Synapse gépről a munkaterülethez való hozzáférést:

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

1. Nyissa meg Azure Synapse munkaterület környezeti változóban tárolt webes URL-címét a `WorkspaceWeb` munkaterület eléréséhez:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse webes megnyitása](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kövesse az alábbi lépéseket a munkaterület Azure Synapse törléséhez.

> [!WARNING]
> A munkaterület Azure Synapse eltávolítja a tárolt SQL-készletek és munkaterület-metaadatok adatbázisában tárolt elemzési motorokat és adatokat. Többé nem lehet majd csatlakozni az SQL- vagy Apache Spark végpontjaihoz. Minden kód-összetevő (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok) törlődik. A munkaterület törlése nincs **hatással** a munkaterülethez Data Lake Store Gen2-ben található adatokra.

Ha a Azure Synapse létrehozott munkaterületre nincs szükség, az alábbi példa futtatásával törölheti.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A következő lépés az [SQL-készletek](quickstart-create-sql-pool-studio.md) vagy a Apache Spark [létrehozása](quickstart-create-apache-spark-pool-studio.md) az adatok elemzéséhez és felderítéséhez.