---
title: VM-pillanatkép linuxos bővítménye Azure Backup
description: Az alkalmazás-konzisztens biztonsági másolat készítése a virtuális gépről a Azure Backup a VM Snapshot Linux bővítmény használatával.
services: backup, virtual-machines
documentationcenter: ''
author: trinadhkotturu
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: ea984fdc4abeb08f4b080e913a0c34b99c59d93c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561109"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>VM-pillanatkép linuxos bővítménye Azure Backup



Azure Backup támogatja a helyszíni rendszerből a felhőbe irányuló munkaterhelések biztonsági mentését és a Felhőbeli erőforrások biztonsági mentését Recovery Services-tárolóba. A Azure Backup a VM Snapshot bővítmény használatával gondoskodik az Azure-beli virtuális gépek alkalmazásának konzisztens biztonsági mentéséről anélkül, hogy le kellene állítania a virtuális gépet. A virtuális gép pillanatképének linuxos bővítményét a Microsoft a Azure Backup szolgáltatás részeként teszi közzé és támogatja. Azure Backup telepíti a bővítményt az első ütemezett biztonsági mentés által aktivált, a biztonsági mentést engedélyező bejegyzés részeként. Ez a dokumentum a virtuális gép pillanatkép-bővítményének támogatott platformokat, konfigurációkat és telepítési lehetőségeket ismerteti.

Az VMSnapshot bővítmény csak a nem felügyelt virtuális gépekhez tartozó Azure Portal jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer
A támogatott operációs rendszerek listáját a [Azure Backup által támogatott operációs rendszerek](../../backup/backup-azure-arm-vms-prepare.md#before-you-start) című témakörben tekintheti meg.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a virtuális gép pillanatkép-bővítményének sémáját mutatja be. A bővítmény igényli a feladat AZONOSÍTÓját – ez azonosítja azt a biztonsági mentési feladatot, amely pillanatfelvételt váltott ki a virtuális gépen, az állapot blob URI-ját, a pillanatkép-művelet állapotát, a pillanatkép ütemezett kezdési időpontját, a blob URI-ját, ahol a pillanatképekhez tartozó naplók íródnak, objstr a virtuálisgép-lemezek és metaadatok.  Mivel ezeket a beállításokat bizalmas adatokként kell kezelni, a védett beállítási konfigurációban kell tárolni. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve. Vegye figyelembe, hogy ezeket a beállításokat csak a biztonsági mentési feladatok részeként ajánlott átadni Azure Backup szolgáltatásból.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | sztring |
| commandStartTimeUTCTicks | 6.36458 e + 17 | sztring |
| területi beállítás | hu-hu | sztring |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | sztring |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |



## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A virtuális gép pillanatkép-bővítményének a virtuális géphez való hozzáadásának ajánlott módja azonban a biztonsági mentés engedélyezése a virtuális gépen. Ez egy Resource Manager-sablonon keresztül érhető el.  A virtuális gépek biztonsági mentését lehetővé tevő Resource Manager-sablon az [Azure gyorskonfigurálás](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)-katalógusban található.


## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával engedélyezheti a biztonsági mentést egy virtuális gépen. A biztonsági mentés engedélyezése után az első ütemezett biztonsági mentési feladata telepíti a virtuális gép pillanatkép-bővítményét a virtuális gépen.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentéseik

A hibaelhárítási információk az [Azure virtuális gépek biztonsági mentésének hibaelhárítási útmutatójában](../../backup/backup-azure-vms-troubleshoot.md)találhatók.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
