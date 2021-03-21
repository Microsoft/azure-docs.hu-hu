---
title: Azure-írásgyorsító
description: Dokumentáció a írásgyorsító engedélyezéséről és használatáról
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: 827643866c23583051bc290c2c50bed3f1bdd421
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737914"
---
# <a name="enable-write-accelerator"></a>írásgyorsító engedélyezése

Az írásgyorsító egy lemezes képesség az M-sorozatú Virtual Machines (VM) számára a Premium Storage Azure Managed Disks kizárólag. Ahogy a neve is jelzi, a funkció célja az, hogy javítsa az írások I/O-késését az Azure Premium Storage. A írásgyorsító ideális megoldás, ha a naplófájlok frissítései szükségesek a lemez nagy teljesítményű, modern adatbázisokhoz való megőrzéséhez.

A írásgyorsító általánosan elérhető az M-sorozatú virtuális gépek számára a nyilvános felhőben.

## <a name="planning-for-using-write-accelerator"></a>írásgyorsító használatának tervezése

Írásgyorsító kell használni azon kötetek esetében, amelyek tartalmazzák a tranzakciónaplót vagy az adatbázis-kezelői naplókat. Az adatbázis-kezelő adatköteteihez nem ajánlott írásgyorsító használni, mivel a szolgáltatás a naplózási lemezek használatára lett optimalizálva.

Írásgyorsító csak az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)szolgáltatással működik együtt.

> [!IMPORTANT]
> A virtuális gép operációsrendszer-lemezének írásgyorsítóának engedélyezése újraindítja a virtuális gépet.
>
> Ha engedélyezni szeretné a írásgyorsítót egy olyan meglévő Azure-lemezre, amely nem része több lemeznek a Windows lemez-vagy kötet-kezelők, a Windows-tárolóhelyek, a Windows kibővített fájlkiszolgáló (SOFS), a Linux LVM vagy a MDADM), az Azure-lemezhez hozzáférő munkaterhelést le kell állítani. Az Azure-lemezt használó adatbázis-alkalmazásokat le kell állítani.
>
> Ha engedélyezni vagy letiltani írásgyorsító a több Azure-beli Premium Storage lemezből kiépített kötetre, és a Windows lemez vagy a kötet-kezelők, a Windows Storage Spaces, a Windows kibővített fájlkiszolgáló (SOFS), a Linux LVM vagy a MDADM) szalagra van szükség, akkor a kötetet alkotó összes lemeznek engedélyezni vagy le kell tiltania a írásgyorsító külön lépésben. Az **ilyen konfigurációban lévő írásgyorsító engedélyezése vagy letiltása előtt állítsa le az Azure-beli virtuális gépet**.

Az operációsrendszer-lemezek írásgyorsítóának engedélyezése nem szükséges az SAP-hez kapcsolódó virtuálisgép-konfigurációkhoz.

### <a name="restrictions-when-using-write-accelerator"></a>Az írásgyorsító használatával kapcsolatos korlátozások

Ha Azure-lemez/VHD esetében írásgyorsítót használ, a következő korlátozások érvényesek:

- A prémium szintű lemezek gyorsítótárazását "None" vagy "read only" értékre kell beállítani. Az összes többi gyorsítótárazási mód nem támogatott.
- A pillanatképek jelenleg nem támogatottak írásgyorsító-kompatibilis lemezek esetében. A biztonsági mentés során a Azure Backup szolgáltatás automatikusan kizárja a virtuális géphez csatolt írásgyorsító-kompatibilis lemezeket.
- Csak kisebb I/O-méretek (<= 512 KiB) veszik a gyorsított útvonalat. Olyan számítási feladatok esetén, amelyekben az adatok tömeges betöltést kapnak, vagy ha a különböző adatbázis-kezelő tranzakciónapló-pufferei nagyobb mértékben vannak kitöltve a tárterület megőrzése előtt, előfordulhat, hogy a lemezre írt I/O nem a gyorsított útvonalat veszi figyelembe.

A írásgyorsító által támogatott Azure Premium Storage virtuális merevlemezek száma korlátozott. A jelenlegi korlátok a következők:

| Virtuális gép termékváltozata | írásgyorsító lemezek száma | írásgyorsító lemez IOPS virtuális gépenként |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms | 1 | 1250 |

A IOPS-korlátok száma virtuális gépenként és *nem* lemezenként történik. Minden írásgyorsító lemez ugyanazon a IOPS-korláton van, mint egy virtuális gépen. A csatlakoztatott lemezek nem haladhatják meg a virtuális gép írási gyorsító IOPS korlátját. Ha például a csatlakoztatott lemezek 30 000 IOPS, akkor a rendszer nem teszi lehetővé, hogy a lemezek a M416ms_v2 20 000 IOPS felett legyenek.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Írásgyorsító engedélyezése egy adott lemezen

A következő néhány szakasz leírja, hogyan engedélyezhető a írásgyorsító az Azure Premium Storage virtuális merevlemezeken.

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak a írásgyorsító használatára az adott időpontban:

- Azok a lemezek, amelyeken alkalmazni kívánja az Azure-írásgyorsító, az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) szolgáltatásnak kell lennie Premium Storageon.
- Az M sorozatú virtuális gépet kell használnia

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure-beli írásgyorsító engedélyezése az Azure PowerShell-lel

A 5.5.0 verziójának Azure-beli rendszerhéj-modulja tartalmazza az adott Azure Premium Storage-lemezek írásgyorsító engedélyezéséhez vagy letiltásához szükséges parancsmagok módosításait.
A írásgyorsító által támogatott lemezek engedélyezéséhez vagy telepítéséhez a következő rendszerhéj-parancsok módosultak, és ki lettek terjesztve a írásgyorsító paramétereinek elfogadására.

Egy új switch paraméter, a **-WriteAccelerator** a következő parancsmagokhoz lett hozzáadva:

- [Set-AzVMOsDisk](/powershell/module/az.compute/set-azvmosdisk)
- [Add-AzVMDataDisk](/powershell/module/az.compute/Add-AzVMDataDisk)
- [Set-AzVMDataDisk](/powershell/module/az.compute/Set-AzVMDataDisk)
- [Add-AzVmssDataDisk](/powershell/module/az.compute/Add-AzVmssDataDisk)

Ha nem adja meg a paraméter értékét hamis értékre, és a írásgyorsító által nem támogatott lemezeket telepít.

Egy új switch paraméter, a **-OsDiskWriteAccelerator** a következő parancsmagokhoz lett hozzáadva:

- [Set-AzVmssStorageProfile](/powershell/module/az.compute/Set-AzVmssStorageProfile)

Ha nem adja meg a paramétert, a rendszer alapértelmezés szerint False értékre állítja be a tulajdonságot, és nem használja a írásgyorsító használó lemezeket.

Új opcionális logikai (nem üres) paraméter, a **-OsDiskWriteAccelerator** a következő parancsmagokhoz lett hozzáadva:

- [Update-AzVM](/powershell/module/az.compute/Update-AzVM)
- [Frissítés – AzVmss](/powershell/module/az.compute/Update-AzVmss)

$True vagy $false megadásával szabályozhatja az Azure-írásgyorsító lemezek támogatását.

Például a következő parancsokra nyílik példa:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

A következő részekben látható módon két fő forgatókönyvet lehet megtekinteni.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>írásgyorsító által támogatott új lemez hozzáadása a PowerShell használatával

Ezt a szkriptet használva hozzáadhat egy új lemezt a virtuális géphez. Az ezzel a parancsfájllal létrehozott lemez írásgyorsítót használ.

Cserélje le a,, `myVM` `myWAVMs` , és a lemez `log001` méretét, valamint a lemez LunID az adott telepítésnek megfelelő értékekkel.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>írásgyorsító engedélyezése meglévő Azure-lemezen a PowerShell használatával

Ezt a parancsfájlt használhatja a írásgyorsító meglévő lemezeken való engedélyezéséhez. Cserélje le a `myVM` , `myWAVMs` , és `test-log001` értékeket az adott telepítéshez megfelelő értékekre. A parancsfájl írásgyorsítót hoz létre egy meglévő lemezhez, ahol a **$newstatus** értéke "$true". A (z) "$false" érték használatával a rendszer letiltja az adott lemezen lévő írásgyorsító.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> A fenti szkript végrehajtásával leválasztja a megadott lemezt, engedélyezi írásgyorsító a lemezre, majd újra csatolja a lemezt.

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Írásgyorsító engedélyezése az Azure Portalon

Írásgyorsító engedélyezheti a portálon, ahol megadhatja a lemez gyorsítótárazási beállításait:

![írásgyorsító a Azure Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Írásgyorsító engedélyezése az Azure CLI-vel

Az [Azure CLI](/cli/azure/) használatával engedélyezheti a írásgyorsító.

Ha egy meglévő lemezen szeretné engedélyezni a írásgyorsítót, használja az [az VM Update szolgáltatást](/cli/azure/vm#az_vm_update), ha a diskName, a VMName és a ResourceGroup a saját értékeire cseréli le: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Ha írásgyorsító-kompatibilis lemezt szeretne csatolni [az az VM Disk Attach](/cli/azure/vm/disk#az_vm_disk_attach)paranccsal, akkor a következő példát használhatja, ha a saját értékeit helyettesíti: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Írásgyorsító letiltásához használja az [az VM Update](/cli/azure/vm#az_vm_update)parancsot, és állítsa a tulajdonságokat hamis értékre: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>írásgyorsító engedélyezése a REST API-k használatával

Az Azure REST API-n keresztül történő üzembe helyezéshez telepítenie kell az Azure-armclient.

### <a name="install-armclient"></a>A armclient telepítése

A armclient futtatásához a chocolatey használatával kell telepítenie. A cmd.exe vagy a PowerShell használatával telepítheti. Emelt szintű jogosultságok használata ezekre a parancsokra ("Futtatás rendszergazdaként").

A cmd.exe használatával futtassa a következő parancsot: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Futtassa a következő parancsot a Power Shell használatával: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Most már telepítheti a armclient a következő paranccsal a cmd.exe vagy a PowerShell használatával `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>A virtuális gép aktuális konfigurációjának beolvasása

Ha módosítani szeretné a lemez konfigurációjának attribútumait, először le kell kérnie az aktuális konfigurációt egy JSON-fájlban. Az aktuális konfigurációt a következő parancs végrehajtásával érheti el: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Cserélje le a "<<   >>" kifejezésben lévő feltételeket az adataira, beleértve a JSON-fájl fájlnevét.

A kimenet az alábbihoz hasonló:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Ezután frissítse a JSON-fájlt, és engedélyezze írásgyorsító a "log1" nevű lemezen. Ezt úgy teheti meg, hogy hozzáadja ezt az attribútumot a JSON-fájlhoz a lemez gyorsítótár-bejegyzése után.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Ezután frissítse a meglévő telepítést a következő paranccsal: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A kimenetnek az alábbihoz hasonlóan kell kinéznie. Láthatja, hogy az írásgyorsító engedélyezve van egy lemezre.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Ha elvégezte ezt a módosítást, írásgyorsító támogatja a meghajtót.
