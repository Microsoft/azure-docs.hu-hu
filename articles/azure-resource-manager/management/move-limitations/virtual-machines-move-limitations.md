---
title: Azure-beli virtuális gépek áthelyezése új előfizetésre vagy erőforráscsoport-csoportba
description: A Azure Resource Manager használatával áthelyezheti a virtuális gépeket egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104909"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató a virtuális gépekhez

Ez a cikk a jelenleg nem támogatott forgatókönyveket és a virtuális gépek biztonsági mentéssel való áthelyezésének lépéseit ismerteti.

## <a name="scenarios-not-supported"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek még nem támogatottak:

* A standard SKU Load Balancer vagy standard SKU nyilvános IP-címmel rendelkező Virtual Machine Scale Sets nem helyezhető át.
* Meglévő virtuális hálózatban lévő virtuális gépek nem helyezhetők át új előfizetésbe, ha nem helyezi át a virtuális hálózatban lévő összes erőforrást.
* A Piactéri erőforrásokkal létrehozott virtuális gépek nem helyezhetők át az előfizetések között. A lehetséges megkerülő megoldásért lásd: [virtuális gépek Marketplace-csomagokkal](#virtual-machines-with-marketplace-plans).
* Az alacsony prioritású virtuális gépek és az alacsony prioritású virtuálisgép-méretezési csoportok nem helyezhetők át az erőforráscsoportok vagy az előfizetések között.
* Egy rendelkezésre állási csoportba tartozó virtuális gépek nem helyezhetők át egyenként.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

A Key vaulttal integrált virtuális gépek nem helyezhetők át [Azure Disk Encryption Linux](../../../virtual-machines/linux/disk-encryption-overview.md) rendszerű virtuális gépekhez vagy [Azure Disk Encryption Windows](../../../virtual-machines/windows/disk-encryption-overview.md)rendszerű virtuális gépekhez. A virtuális gép áthelyezéséhez le kell tiltania a titkosítást.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>Piactéri csomagokkal rendelkező virtuális gépek

A Piactéri erőforrásokkal létrehozott virtuális gépek nem helyezhetők át az előfizetések között. A korlátozás megkerülése érdekében kiépítheti a virtuális gépet a jelenlegi előfizetésben, és újra telepítheti az új előfizetésben. Az alábbi lépések segítségével újra létrehozhatja a virtuális gépet az új előfizetésben. Előfordulhat azonban, hogy az összes forgatókönyv esetében nem működnek. Ha a csomag már nem érhető el a piactéren, ezek a lépések nem fognak működni.

1. Másolja a tervre vonatkozó információkat.

1. Az operációsrendszer-lemez klónozása a cél előfizetésbe, vagy az eredeti lemez áthelyezése a virtuális gép forrás-előfizetésből való törlése után.

1. A cél előfizetésben fogadja el a piactér használati feltételeit. A feltételeket a következő PowerShell-parancs futtatásával fogadhatja el:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Másik lehetőségként létrehozhat egy virtuális gép új példányát a csomagon keresztül a portálon. Az új előfizetésben szereplő feltételek elfogadása után törölheti a virtuális gépet.

1. A cél előfizetésben hozza létre újra a virtuális gépet a klónozott operációsrendszer-lemezről a PowerShell, a CLI vagy egy Azure Resource Manager sablon használatával. A lemezhez csatolt Piactéri csomag belefoglalása. A tervre vonatkozó információnak meg kell egyeznie az új előfizetésben megvásárolt csomaggal.

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek Azure Backup

Azure Backuprel konfigurált virtuális gépek áthelyezéséhez törölnie kell a visszaállítási pontokat a tárból.

Ha a helyreállítható [Törlés](../../../backup/soft-delete-virtual-machines.md) engedélyezve van a virtuális gépen, akkor a virtuális gép nem helyezhető át a visszaállítási pontok megtartására. [Tiltsa le a nem kötelező törlést](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) , vagy várjon 14 nappal a visszaállítási pontok törlése után.

### <a name="portal"></a>Portál

1. Átmenetileg állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentést.
2. Azure Backup konfigurált virtuális gépek áthelyezéséhez hajtsa végre a következő lépéseket:

   1. Keresse meg a virtuális gép helyét.
   2. Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<VM location>_1` . A név például *AzureBackupRG_westus2_1* formátumú.
   3. A Azure Portalban tekintse meg a **rejtett típusok megjelenítése** részt.
   4. Keresse meg a **Microsoft. számítás/restorePointCollections** típusú erőforrást, amely az elnevezési mintával rendelkezik `AzureBackup_<VM name>_###########` .
   5. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
   6. A törlési művelet befejezése után áthelyezheti a virtuális gépet.

3. Helyezze át a virtuális gépet a célként megadott erőforrás-csoportba.
4. Folytassa a biztonsági mentést.

### <a name="powershell"></a>PowerShell

1. Keresse meg a virtuális gép helyét.

1. Keressen egy erőforráscsoportot az elnevezési mintával – `AzureBackupRG_<VM location>_1` . A név lehet például a következő: `AzureBackupRG_westus2_1` .

1. Ha csak egy virtuális gépet helyez át, szerezze be a visszaállítási pont gyűjteményét az adott virtuális géphez.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Ha az összes virtuális gépet ezen a helyen helyezi át a Back UPS-mel, szerezze be a visszaállítási pontok gyűjteményeit ezekhez a virtuális gépekhez.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Törölje az egyes erőforrásokat. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Keresse meg a virtuális gép helyét.

1. Keressen egy erőforráscsoportot az elnevezési mintával – `AzureBackupRG_<VM location>_1` . A név lehet például a következő: `AzureBackupRG_westus2_1` .

1. Ha csak egy virtuális gépet helyez át, szerezze be a visszaállítási pont gyűjteményét az adott virtuális géphez.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Ha az összes virtuális gépet ezen a helyen helyezi át a Back UPS-mel, szerezze be a visszaállítási pontok gyűjteményeit ezekhez a virtuális gépekhez.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Törölje az egyes erőforrásokat. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Következő lépések

* Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).

* További információ a helyreállítási tár biztonsági mentéséhez való áthelyezéséről: [Recovery Services korlátozások](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
