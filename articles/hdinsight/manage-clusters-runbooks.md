---
title: 'Oktatóanyag: fürtök létrehozása Azure Automation runbookok használatával – Azure HDInsight'
description: Megtudhatja, hogyan hozhat létre és törölhet Azure HDInsight-fürtöket a felhőben futó parancsfájlokkal Azure Automation runbookok használatával.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864788"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Oktatóanyag: Azure HDInsight-fürtök létrehozása Azure Automation

Azure Automation lehetővé teszi a felhőben futó parancsfájlok létrehozását, illetve az Azure-erőforrások igény szerinti felügyeletét, vagy ütemezés alapján. Ez a cikk bemutatja, hogyan hozhat létre és törölhet PowerShell-runbookok Azure HDInsight-fürtök létrehozásához és törléséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Telepítse a HDInsight való interakcióhoz szükséges modulokat.
> * A fürt létrehozása során szükséges hitelesítő adatok létrehozása és tárolása.
> * Hozzon létre egy új Azure Automation runbook egy HDInsight-fürt létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Egy meglévő [Azure Automation-fiók](../automation/automation-quickstart-create-account.md).
* Egy meglévő [Azure Storage-fiók](../storage/common/storage-account-create.md), amelyet a rendszer fürtös tárolóként fog használni.

## <a name="install-hdinsight-modules"></a>HDInsight-modulok telepítése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. Válassza ki a Azure Automation fiókokat.
1. Válassza a **modulok tárat** a **megosztott erőforrások** területen.
1. Írja be a **AzureRM. profil** kifejezést a mezőbe, és nyomja le az ENTER billentyűt a kereséshez. Válassza ki a rendelkezésre álló találatokat.
1. Az **AzureRM. profil** képernyőn válassza az **Importálás** lehetőséget. Jelölje be a jelölőnégyzetet az Azure-modulok frissítéséhez, majd kattintson **az OK gombra**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="AzureRM. Profile modul importálása" border="false":::

1. Térjen vissza a modulok galériához a **megosztott erőforrások** területen a **modulok gyűjtemény** kiválasztásával.
1. Írja be a következőt: **HDInsight**. Válassza a **AzureRM. HDInsight** elemet.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="HDInsight-modulok tallózása" border="true":::

1. Az **AzureRM. HDInsight** panelen válassza az **Importálás** és **az OK** elemet.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="AzureRM. HDInsight modul importálása" border="true":::

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

1. A **megosztott erőforrások** területen válassza a **hitelesítő adatok** lehetőséget.
1. Válassza **a hitelesítő adat hozzáadása** elemet.
1. Adja meg a szükséges információkat az **új hitelesítő adatok** panelen. Ez a hitelesítő adat a fürt jelszavának tárolására szolgál, amely lehetővé teszi a Ambari való bejelentkezést.

    | Tulajdonság | Érték |
    | --- | --- |
    | Név | `cluster-password` |
    | Felhasználónév | `admin` |
    | Jelszó | `SECURE_PASSWORD` |
    | Jelszó megerősítése | `SECURE_PASSWORD` |

1. Válassza a **Létrehozás** lehetőséget.
1. Ismételje meg ugyanezt a folyamatot egy új hitelesítő adathoz a `ssh-password` felhasználónévvel `sshuser` és az Ön által választott jelszóval. Válassza a **Létrehozás** lehetőséget. Ez a hitelesítő adat a fürt SSH-jelszavának tárolása.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="hitelesítő adat létrehozása" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Runbook létrehozása fürt létrehozásához

1. Válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
1. Válassza **a Runbook létrehozása** lehetőséget.
1. A **Runbook létrehozása** panelen adja meg a runbook nevét, például: `hdinsight-cluster-create` . Válassza a **PowerShell** lehetőséget a **Runbook típusa** legördülő listából.
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="runbook létrehozása" border="true":::

1. Adja meg a következő kódot a **PowerShell-Runbook szerkesztése** képernyőn, és válassza a **Közzététel** lehetőséget:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="runbook közzététele" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Runbook létrehozása fürt törléséhez

1. Válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
1. Válassza **a Runbook létrehozása** lehetőséget.
1. A **Runbook létrehozása** panelen adja meg a runbook nevét, például: `hdinsight-cluster-delete` . Válassza a **PowerShell** lehetőséget a **Runbook típusa** legördülő listából.
1. Válassza a **Létrehozás** lehetőséget.
1. Adja meg a következő kódot a **PowerShell-Runbook szerkesztése** képernyőn, és válassza a **Közzététel** lehetőséget:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbookok végrehajtása

### <a name="create-a-cluster"></a>Fürt létrehozása

1. Tekintse meg az Automation-fiók Runbookok listáját úgy, hogy kiválasztja a **runbookok** elemet a **folyamat automatizálása** alatt.
1. Válassza ki `hdinsight-cluster-create` vagy a fürt létrehozási runbook létrehozásakor használt nevet.
1. Válassza az **Indítás** lehetőséget a runbook azonnali végrehajtásához. A runbookok rendszeres időközönként is ütemezhetők. Lásd: [Runbook ütemezése Azure Automationban](../automation/shared-resources/schedules.md)
1. Adja meg a parancsfájl kötelező paramétereit, majd kattintson **az OK gombra**. Ez egy új HDInsight-fürtöt hoz létre a **CLUSTERNAME** paraméterben megadott névvel.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="fürt létrehozási runbook végrehajtása" border="true":::

### <a name="delete-a-cluster"></a>Fürt törlése

Törölje a fürtöt a létrehozott runbook kiválasztásával `hdinsight-cluster-delete` . Kattintson a **Start** gombra, írja be a **CLUSTERNAME** paramétert, és sselect **az OK gombot**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a létrehozott Azure Automation fiókot a nem kívánt költségek elkerülése érdekében. Ehhez navigáljon a Azure Portalhoz, válassza ki azt az erőforráscsoportot, amelyben létrehozta a Azure Automation fiókot, válassza ki az Automation-fiókot, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Apache Hadoop-fürtök kezelése a HDInsight-ben Azure PowerShell használatával](hdinsight-administer-use-powershell.md)