---
title: VMware virtuális gépek migrálása az Azure-ba kiszolgálóoldali titkosítással (SSE) és az ügyfél által felügyelt kulcsokkal (CMK) Azure Migrate kiszolgáló áttelepítésével
description: Ismerje meg, hogyan telepítheti át a VMware virtuális gépeket az Azure-ba kiszolgálóoldali titkosítással (SSE) és az ügyfél által felügyelt kulcsokkal (CMK) Azure Migrate kiszolgáló áttelepítésével
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751087"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>VMware virtuális gépek migrálása a kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal rendelkező Azure virtuális gépekre

Ez a cikk azt ismerteti, hogyan telepíthetők a VMware virtuális gépek az Azure Virtual Machines szolgáltatásba a kiszolgálóoldali titkosítással (SSE) titkosított, ügyfél által felügyelt kulcsokkal (CMK) rendelkező lemezekkel, Azure Migrate Server Migration (ügynök nélküli replikáció) használatával.

Az Azure Migrate Server Migration Portal felülete lehetővé teszi a [VMWare virtuális gépek áttelepítését az Azure-ba ügynök nélküli replikálással.](tutorial-migrate-vmware.md) A portál jelenleg nem teszi lehetővé az SSE bekapcsolását az Azure-ban lévő replikált lemezek CMK. Az SSE és a replikált lemezek CMK való bekapcsolásának lehetősége jelenleg csak REST APIon keresztül érhető el. Ebből a cikkből megtudhatja, hogyan hozhat létre és helyezhet üzembe egy [Azure Resource Manager-sablont](../azure-resource-manager/templates/overview.md) egy VMware virtuális gép replikálásához, és konfigurálja a replikált lemezeket az Azure-ban az SSE és a CMK használatával.

A cikkben szereplő példák a Resource Manager-sablon létrehozásához és üzembe helyezéséhez szükséges feladatok elvégzéséhez [Azure PowerShell](/powershell/azure/new-azureps-module-az) használnak.

[További](../virtual-machines/disk-encryption.md) információ a felügyelt lemezekkel rendelkező, ügyfél által felügyelt kulcsokkal (CMK) rendelkező kiszolgálóoldali titkosításról.

## <a name="prerequisites"></a>Előfeltételek

- [Tekintse át a](tutorial-migrate-vmware.md) VMWare virtuális gépek Azure-ba történő áttelepítéséről szóló oktatóanyagot az ügynök nélküli replikációval az eszközre vonatkozó követelmények megismeréséhez.
- Az [alábbi utasításokat követve](./create-manage-projects.md) hozzon létre egy Azure Migrate projektet, és adja hozzá a **Azure Migrate: Server áttelepítési** eszközt a projekthez.
- [Kövesse ezeket az utasításokat](how-to-set-up-appliance-vmware.md) a VMware-hez készült Azure Migrate berendezés beállításához a helyszíni környezetben, és fejezze be a felderítést.

## <a name="prepare-for-replication"></a>Felkészülés a replikálásra

Miután befejeződött a virtuális gép felderítése, a kiszolgáló áttelepítési csempén lévő felderített kiszolgálók sorban megjelennek a készülék által felderített VMware virtuális gépek száma.

A virtuális gépek replikálásának megkezdése előtt elő kell készíteni a replikációs infrastruktúrát.

1. Hozzon létre egy Service Bus példányt a célként megadott régióban. A helyszíni Azure Migrate berendezés a Service Bus használja a kiszolgáló áttelepítési szolgáltatásával való kommunikációra a replikáció és az áttelepítés koordinálásához.
2. Hozzon létre egy Storage-fiókot, amellyel átviheti a műveleti naplókat a replikációból.
3. Hozzon létre egy Storage-fiókot, amelyet a Azure Migrate berendezés a replikációs adatok feltöltésére.
4. Hozzon létre egy Key Vault, és konfigurálja a Key Vaultt a megosztott hozzáférési aláírási jogkivonatok kezeléséhez a blob-hozzáféréshez a 3. és 4. lépésben létrehozott Storage-fiókok esetében.
5. Hozzon létre egy közös hozzáférési aláírási tokent az 1. lépésben létrehozott Service Bus számára, és hozzon létre egy titkot a tokenhez az előző lépésben létrehozott Key Vault.
6. Hozzon létre egy Key Vault hozzáférési szabályzatot, amely a helyszíni Azure Migrate berendezést (a Appliance HRE alkalmazás használatával) és a kiszolgáló áttelepítési szolgáltatásának hozzáférést biztosít a Key Vaulthoz.
7. Hozzon létre egy replikációs házirendet, és konfigurálja a kiszolgáló áttelepítési szolgáltatását az előző lépésben létrehozott replikációs infrastruktúra részleteivel.

A replikációs infrastruktúrát létre kell hozni a cél Azure-régióban az áttelepítéshez és azon a célként megadott Azure-előfizetésben, amelyben a virtuális gépek migrálva vannak.

A kiszolgálói áttelepítési portál felülete megkönnyíti a replikációs infrastruktúra előkészítését azáltal, hogy automatikusan elvégzi ezt a műveletet, amikor egy projektben először replikálja a virtuális gépet. Ez a cikk azt feltételezi, hogy már replikált egy vagy több virtuális gépet a portál felületén, és a replikációs infrastruktúra már létrejött. Megvizsgáljuk, hogyan derítheti fel a meglévő replikációs infrastruktúra részleteit, és hogyan használhatja ezeket a részleteket olyan Resource Manager-sablon bemenetként, amelyet a CMK-vel történő replikáció beállításához fog használni.

### <a name="identifying-replication-infrastructure-components"></a>Replikációs infrastruktúra összetevőinek azonosítása

1. A Azure Portal lépjen az erőforráscsoportok lapra, és válassza ki azt az erőforráscsoportot, amelyben a Azure Migrate projektet létrehozták.
2. Válassza a bal oldali menüben a **központi telepítések** elemet, majd keressen rá a *"Microsoft. MigrateV2. VMwareV2EnableMigrate"* karakterlánccal kezdődő központi telepítési névre. Megjelenik a portál felhasználói felülete által létrehozott Resource Manager-sablonok listája, amelyekkel beállíthatja a virtuális gépek replikálását ebben a projektben. Egy ilyen sablont töltünk le, amelyet a sablonnak a CMK-vel való replikáláshoz való előkészítéséhez használunk.
3. A sablon letöltéséhez jelölje ki az előző lépésben szereplő karakterlánc-mintának megfelelő központi telepítést > válassza a bal oldali menüben a **sablon** kiválasztása lehetőséget > kattintson a felső menüben a **Letöltés** lehetőségre. Mentse helyileg a template.jsfájlt. Ez a sablonfájl az utolsó lépésben lesz módosítva.

## <a name="create-a-disk-encryption-set"></a>Lemezes titkosítási csoport létrehozása

A lemezes titkosítási készlet objektum Managed Diskst képez le egy olyan Key Vaultra, amely az SSE-hoz használandó CMK tartalmazza. A virtuális gépek CMK való replikálásához létre kell hoznia egy lemezes titkosítási készletet, és bemenetként kell továbbítania a replikálási művelethez.

[A következő példa alapján](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) hozzon létre egy lemezes titkosítási készletet Azure PowerShell használatával. Győződjön meg arról, hogy a lemez titkosítási készlete a cél előfizetésben jön létre, amelyben a virtuális gépek migrálva vannak, illetve az áttelepítéshez a cél Azure-régióban.

A lemezes titkosítási csoport úgy konfigurálható, hogy a felügyelt lemezeket egy ügyfél által felügyelt kulccsal titkosítsa, vagy a kettős titkosításhoz az ügyfél által felügyelt kulccsal és egy platform kulccsal. Ha a kettős titkosítást szeretné használni a REST beállításnál, konfigurálja a lemez titkosítási készletét az [itt](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)leírtak szerint.

A lemezes titkosítási csoport alatt látható példában az ügyfél által felügyelt kulcs használatára van konfigurálva.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Az áttelepíteni kívánt VMware virtuális gép részleteinek beolvasása

Ebben a lépésben Azure PowerShell fogja használni az áttelepíteni kívánt virtuális gép részleteit. Ezeket a részleteket a Resource Manager-sablon a replikáláshoz való létrehozásához fogjuk használni. A két érdekes tulajdonság a következők egyike:

- A felderített virtuális gépekhez tartozó gépi erőforrás azonosítója.
- A virtuális gép lemezeinek és a lemezük azonosítóinak listája.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Másolja a virtuális gép által felderített Azure Migrate berendezésnek megfelelő SiteId karakterlánc értékét. A fenti példában látható, hogy a SiteId *"/Subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* .

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Másolja az áttelepíteni kívánt gép ResourceId, nevének és lemezének UUID-értékeit.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Resource Manager-sablon létrehozása a replikáláshoz

- Nyissa meg az Ön által választott szerkesztőben a **replikációs infrastruktúra összetevőinek azonosítása** lépésben letöltött Resource Manager-sablonfájlt.
- Távolítsa el az összes erőforrás-definíciót a sablonból, kivéve a *"Microsoft. recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"* típusú erőforrásokat.
- Ha a fenti típus több erőforrás-definícióval is rendelkezik, távolítsa el az összeset, de csak egyet. Távolítsa el az összes **dependsOn** tulajdonság-definíciót az erőforrás-definícióból.
- Ennek a lépésnek a végén egy olyan fájlnak kell lennie, amely az alábbi példához hasonlóan néz ki, és ugyanazokkal a tulajdonságokkal rendelkezik.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Szerkessze a **Name** tulajdonságot az erőforrás-definícióban. Cserélje le azt a karakterláncot, amely az utolsó "/" értéket követi a Name (név) tulajdonságban $machine értékével *. Név*(az előző lépésből).
- Módosítsa a **Properties. providerSpecificDetails. vmwareMachineId** tulajdonság értékét $Machine értékkel *. ResourceId*(az előző lépésből).
- Állítsa be a **targetResourceGroupId**, a **targetNetworkId**, a **targetSubnetName** értéket a cél erőforráscsoport-azonosítóra, a CÉLKÉNT megadott virtuális hálózati erőforrás-azonosítóra, illetve a célként megadott alhálózat nevére.
- Állítsa a **licenseType** értékét "windowsserver" értékre a virtuális gép Azure Hybrid Benefit alkalmazásához. Ha a virtuális gép nem jogosult Azure Hybrid Benefitre, állítsa a **licenseType** értékét NoLicenseType értékre.
- Módosítsa a **targetVmName** tulajdonság értékét az áttelepített virtuális gép kívánt Azure-beli virtuálisgép-nevére.
- Opcionálisan hozzáadhat egy **targetVmSize** nevű tulajdonságot a **targetVmName** tulajdonság alatt. Állítsa a **targetVmSize** tulajdonság értékét az áttelepített virtuális gép kívánt Azure-beli virtuálisgép-méretére.
- A **disksToInclude** tulajdonság a replikáláshoz használt lemezek listája, amely egy helyszíni lemezt jelképező minden listaelemet jelöl. Hozzon létre több listaelemet a helyszíni virtuális gépen lévő lemezek számának megfelelően. Cserélje le a listaelemben lévő **Beskid** tulajdonságot az előző lépésben azonosított lemezek UUID-ra. Állítsa a **isOSDisk** értékét "true" értékre a virtuális gép operációsrendszer-lemezén, és a "false" értéket az összes többi lemez esetében. A **logStorageAccountId** és a **logStorageAccountSasSecretName** tulajdonságok változatlanok maradnak. Állítsa a **lemeztípus** értéket a lemezhez használni kívánt Azure felügyelt lemez típusára (*Standard_LRS, Premium_LRS, StandardSSD_LRS*). A CMK-mel titkosítani kívánt lemezekhez vegyen fel egy **diskEncryptionSetId** nevű tulajdonságot, és állítsa be az értéket a létrehozott lemezes titkosítási csoport erőforrás-azonosítójához (**$des. Azonosító**) a *lemez titkosítási készletének létrehozása* lépésben
- Mentse a szerkesztett sablonfájl fájlt. A fenti példában a szerkesztett sablonfájl a következőképpen néz ki:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Replikáció beállítása

Most már üzembe helyezheti a szerkesztett Resource Manager-sablont a projekt erőforráscsoporthoz a virtuális gép replikálásának beállításához. Megtudhatja, hogyan [helyezhet üzembe erőforrást Azure Resource Manager sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Következő lépések

A [replikációs állapot figyelése](tutorial-migrate-vmware.md#track-and-monitor) a portálon keresztül, valamint a tesztek áttelepítése és áttelepítése.