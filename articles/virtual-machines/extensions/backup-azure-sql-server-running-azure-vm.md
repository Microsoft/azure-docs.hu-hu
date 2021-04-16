---
title: Azure Backup Azure-beli SQL Server futó virtuális gépekhez
description: Ebből a cikkből megtudhatja, hogyan regisztrálhat Azure Backup azure SQL Server virtuális gépen futó virtuális gépeken.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: c10be941206dd60887c9d82025506d1ea15c51a2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517249"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup Azure-beli SQL Server futó virtuális gépekhez

Azure Backup ajánlatok között a támogatja az Azure-beli virtuális gépeken futó SQL Server számítási feladatok biztonságimentését. Mivel az SQL-alkalmazás egy Azure-beli virtuális gépen fut, a biztonsági mentési szolgáltatásnak engedéllyel kell rendelkeznie az alkalmazás eléréséhez és a szükséges adatok lekéréséhez.
Ennek érdekében a Azure Backup telepíti az **AzureBackupWindowsWorkload** bővítményt arra a virtuális gépre, amelyen az SQL Server fut, a felhasználó által indított regisztrációs folyamat során.

## <a name="prerequisites"></a>Előfeltételek

A támogatott forgatókönyvek listájáért tekintse [](../../backup/sql-support-matrix.md#scenario-support) meg a támogatott forgatókönyvek által támogatott támogatási Azure Backup.

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup támogatja az NSG-címkéket, a proxykiszolgálók vagy a felsorolt IP-tartományok üzembe helyezését; Az egyes metódusokkal kapcsolatos részletekért tekintse meg ezt a [cikket.](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="extension-schema"></a>Bővítményséma

A bővítménysémák és a tulajdonságértékek a szolgáltatás által a CRP API-nak megadott konfigurációs értékek (futásidejű beállítások). Ezeket a konfigurációs értékeket a rendszer a regisztráció és a frissítés során használja. **Az AzureBackupWindowsWorkload** bővítmény is ezt a sémát használja. A séma előre be van állítva; az objectStr mezőben új paraméter is hozzáadható

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Az alábbi JSON a WorkloadBackup bővítmény sémáját mutatja be.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Tulajdonságértékek

Name | Érték/példa | Adattípus
 --- | --- | ---
területi beállítás | hu-hu  |  sztring
taskId (feladatazonosító) | "1c0ae461-9d3b-418c-a505-1dfe2095d"  | sztring
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdYTJNDZlM2ZkiwiSWRNZ210Q29udGFpbmMySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIwiU3Vic2AcXB0aW9uSWQiOiJkNGEzOTliNy1iYJAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTLCJVbmlxdWVBmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhhYjg3OTcyIn0sInN0YW1w1wTGlzdCI6W3siU2MicdmzimZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVulbCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | sztring
commandStartTimeUTCTicks | "636967192566036845"  | sztring
vmType (virtuális gép típusa)  | "microsoft.compute/virtualmachines"  | sztring
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdYTJNDZlM2ZkiwiSWRNZ210Q29udGFpbmMySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIwiU3Vic2AcXB0aW9uSWQiOiJkNGEzOTliNy1iYJAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTLCJVbmlxdWVBmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhhYjg3OTcyIn0sInN0YW1w1wTGlzdCI6W3siU2MicdmzimZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVulbCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | sztring
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sztring
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sztring

## <a name="template-deployment"></a>Sablonalapú telepítés

Javasoljuk, hogy az AzureBackupWindowsWorkload bővítményt úgy adja hozzá egy virtuális géphez, hogy engedélyezi SQL Server biztonsági mentést a virtuális gépen. Ez a biztonsági mentés automatizálására [tervezett](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) Resource Manager érhető el a SQL Server VM.

## <a name="powershell-deployment"></a>A PowerShell telepítése

Regisztrálnia kell az SQL-alkalmazást tartalmazó Azure-beli virtuális gépet egy Recovery Services-tárolóval. A regisztráció során az AzureBackupWindowsWorkload bővítmény telepítve lesz a virtuális gépen. A virtuális gép regisztráláshoz használja a [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) parancsmagot.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

A parancs egy **biztonsági mentési tárolót ad** vissza erről az erőforrásról, és az állapot **regisztrálva lesz.**

## <a name="next-steps"></a>Következő lépések

- [További információ a](../../backup/backup-sql-server-azure-troubleshoot.md) Azure SQL virtuális gépek biztonsági mentésének hibaelhárítási útmutatóiról
- [Gyakori kérdések](../../backup/faq-backup-sql-server.yml) az Azure-SQL Server virtuális gépeken futó és az Azure Backup szolgáltatást Azure Backup adatbázisok biztonsági mentése ről.
