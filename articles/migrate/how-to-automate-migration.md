---
title: Ügynök nélküli VMware-áttelepítés automatizálása Azure Migrate
description: Ismerteti, hogyan lehet parancsfájlokkal nagy mennyiségű VMware virtuális gépet áttelepíteni Azure Migrate
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754266"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware virtuális gépek áttelepítésének skálázása 

Ebből a cikkből megtudhatja, hogyan használhatók a parancsfájlok nagy számú VMware virtuális gép (VM) áttelepítésére az ügynök nélküli módszer használatával. A Migrálás méretezéséhez [Azure Migrate PowerShell-modult](./tutorial-migrate-vmware-powershell.md)használ. 

A Azure Migrate VMware Migration Automation-parancsfájlok letölthetők a GitHubon elérhető [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) -tárházban. A parancsfájlok használhatók a VMware virtuális gépek Azure-ba történő áttelepítéséhez az ügynök nélküli áttelepítési módszer használatával. Az ezekben a parancsfájlokban használt Azure Migrate PowerShell-parancsokat [itt](./tutorial-migrate-vmware-powershell.md)dokumentálja.

## <a name="current-limitations"></a>Aktuális korlátozások
- Ezek a parancsfájlok támogatják a VMware virtuális gépek összes lemezről történő áttelepítését. Frissítheti a parancsfájlokat, ha szelektíven szeretné replikálni a VMware virtuális géphez csatolt lemezeket. 
- A szkriptek az értékelési javaslatok használatát támogatják. Ha az értékelési javaslatok nem használatosak, akkor a VMware virtuális géphez csatolt összes lemez ugyanarra a felügyelt lemezre (standard vagy prémium) lesz áttelepítve. Frissítheti a parancsfájlokat, ha több típusú felügyelt lemezt szeretne használni ugyanazzal a virtuális géppel

## <a name="prerequisites"></a>Előfeltételek

- [Fejezze be a felderítési oktatóanyagot](tutorial-discover-vmware.md) az Azure és a VMware áttelepítésre való előkészítéséhez.
- Javasoljuk, hogy az Azure-ba való Migrálás előtt fejezze be a második oktatóanyagot a [VMWare virtuális gépek felméréséhez](./tutorial-assess-vmware-azure-vm.md) .
- A Azure PowerShell `Az` modul. Ha Azure PowerShell telepítésére vagy frissítésére van szüksége, kövesse ezt az [útmutatót a Azure PowerShell telepítéséhez és konfigurálásához](/powershell/azure/install-az-ps) .

## <a name="install-azure-migrate-powershell-module"></a>Azure Migrate PowerShell-modul telepítése

Azure Migrate PowerShell-modul előzetes verzióban érhető el. A PowerShell-modult a következő parancs használatával kell telepítenie. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV bemeneti fájl
Az összes előfeltétel befejezését követően létre kell hoznia egy CSV-fájlt, amely az összes áttelepíteni kívánt virtuális gép számára tartalmaz egy adatforrást. Minden parancsfájl úgy van kialakítva, hogy ugyanazon a CSV-fájlon működjön. A minta CSV-sablon a hivatkozáshoz tartozó Scripts mappában érhető el. A CSV-fájl konfigurálható úgy, hogy kiértékelési javaslatokat használjon, és még azt is, hogy bizonyos műveletek nem indíthatók el egy adott virtuális gép esetében. 

> [!NOTE]
> Ugyanaz a CSV-fájl használható a virtuális gépek több Azure Migrate projektben való áttelepítésére.

### <a name="csv-file-schema"></a>CSV-fájl sémája

**Oszlopfejléc** | **Leírás**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Adja meg Azure Migrate projekt-előfizetés AZONOSÍTÓját.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Adja meg Azure Migrate erőforráscsoport nevét.
AZMIGRATEPROJECT_NAME | Adja meg annak a Azure Migrate projektnek a nevét, amelyben át szeretné telepíteni a kiszolgálókat. 
SOURCE_MACHINE_NAME | Adja meg a felderített virtuális gép rövid nevét (megjelenítendő nevét) a Azure Migrate projektben.
AZMIGRATEASSESSMENT_NAME | Adja meg az áttelepítéshez használni kívánt értékelés nevét.
AZMIGRATEGROUP_NAME | Adja meg annak a csoportnak a nevét, amelyet a Azure Migrate értékeléshez használt.
TARGET_RESOURCE_GROUP_NAME | Adja meg annak az Azure-erőforráscsoportnak a nevét, amelyre a virtuális gépet át kell telepíteni.
TARGET_VNET_NAME| Adja meg az áttelepített virtuális gép által használandó Azure-Virtual Network nevét.
TARGET_SUBNET_NAME | Adja meg annak az alhálózatnak a nevét a cél virtuális hálózaton, amelyet az áttelepített virtuális gépnek használnia kell. Ha üresen hagyja, a rendszer az "alapértelmezett" alhálózatot használja.
TARGET_MACHINE_NAME | Adja meg azt a nevet, amelyet az áttelepített virtuális gépnek az Azure-ban használnia kell. Ha üresen hagyja, a rendszer a forrásoldali gép nevét fogja használni.  
TARGET_MACHINE_SIZE | Adja meg azt az SKU-t, amelyet a virtuális gépnek az Azure-ban használnia kell. Ha egy virtuális gépet át szeretne telepíteni D2_v2 virtuális gépre az Azure-ban, a mezőben a "Standard_D2_v2" értéket kell megadnia. Ha értékelést használ, akkor ez az érték az értékelési javaslat alapján lesz származtatva.
LICENSE_TYPE | Itt adhatja meg, hogy a Windows Server rendszerű virtuális gépekhez Azure Hybrid Benefit kíván-e használni. Használja a "WindowsServer" értéket, hogy kihasználhassa a Azure Hybrid Benefit előnyeit. Ellenkező esetben hagyja üresen, vagy használja a "NoLicenseType" értéket.
OS_DISK_ID | Adja meg az áttelepíteni kívánt virtuális gép operációsrendszer-lemezének AZONOSÍTÓját. A használni kívánt lemez a Get-AzMigrateServer parancsmag használatával beolvasott lemez egyedi azonosító (UUID) tulajdonsága. A parancsfájl a virtuális gép első lemezét fogja használni az operációsrendszer-lemezként, ha nincs megadva érték.
TARGET_DISKTYPE | Adja meg az Azure-beli virtuális gép összes lemezéhez használni kívánt lemez típusát. Szabványos HDD-lemezek használatához használja a prémium szintű Managed Disks, a "StandardSSD_LRS" a standard SSD-lemezekhez és a "Standard_LRS" Premium_LRS. Ha úgy dönt, hogy értékelést használ, a parancsfájl a virtuális gép minden lemezének ajánlott lemez-típusaival rangsorolja a-t. Ha nem használja az értékelést, és nem ad meg értéket, a parancsfájl alapértelmezés szerint szabványos HDD-lemezeket fog használni.    
AVAILABILITYZONE_NUMBER | Az áttelepített virtuális géphez használni kívánt rendelkezésre állási zóna számának megadása. Ezt üresen hagyhatja abban az esetben, ha nem szeretné használni a rendelkezésre állási zónákat. 
AVAILABILITYSET_NAME | Adja meg az áttelepített virtuális géphez használni kívánt rendelkezésre állási csoport nevét. Ezt üresen hagyhatja abban az esetben, ha nem szeretné használni a rendelkezésre állási készletet.
TURNOFF_SOURCESERVER | Ha az áttelepítés időpontjában ki szeretné kapcsolni a forrás virtuális gépet, akkor az "Y" értéket kell megadnia. Más esetben használja az "N" beállítást. Ha üresen hagyja, a parancsfájl "N" értékként feltételezi az értéket.
TESTMIGRATE_VNET_NAME | Adja meg az áttelepítés teszteléséhez használni kívánt virtuális hálózat nevét.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Ha szeretné frissíteni az Azure-ban az áttelepített virtuális gép által használt erőforráscsoportot, akkor adja meg az Azure-erőforráscsoport nevét, máskülönben hagyja üresen a mezőt. 
UPDATED_TARGET_VNET_NAME | Ha az Azure-ban az áttelepített virtuális gép által használt Virtual Network szeretné frissíteni, akkor adja meg az Azure-Virtual Network nevét, máskülönben hagyja üresen.
UPDATED_TARGET_MACHINE_NAME | Ha frissíteni szeretné a nevet az áttelepített virtuális gép számára az Azure-ban, akkor adja meg a használni kívánt új nevet, máskülönben hagyja üresen.
UPDATED_TARGET_MACHINE_SIZE | Ha szeretné frissíteni az áttelepített virtuális gép által az Azure-ban használt SKU-t, adja meg a használni kívánt új SKU-t, máskülönben hagyja üresen.
UPDATED_AVAILABILITYZONE_NUMBER | Ha frissíteni szeretné a rendelkezésre állási zónát, amelyet az áttelepített virtuális gép használ az Azure-ban, akkor adja meg a használni kívánt új rendelkezésre állási zónát, máskülönben hagyja üresen.
UPDATED_AVAILABILITYSET_NAME | Ha frissíteni szeretné a rendelkezésre állási készletet, amelyet az áttelepített virtuális gép használ az Azure-ban, akkor adja meg az új rendelkezésre állási készletet, amelyet a rendszer üresen hagy.
UPDATE_NIC1_ID | A frissítendő hálózati adapter AZONOSÍTÓjának meghatározása. Ha üresen hagyja, a parancsfájl feltételezi, hogy az érték a felderített virtuális gép első NIC-je. Ha nem szeretné frissíteni a virtuális gép hálózati adapterét, hagyja üresen a NIC-nevet tartalmazó mezőket. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Itt adhatja meg a hálózati adapterhez használandó értéket. Az "elsődleges", a "másodlagos" vagy a "DoNotCreate" beállítással adhatja meg, hogy a hálózati adapter elsődleges, másodlagos, vagy nem hozható létre az áttelepített virtuális gépen. A virtuális gép elsődleges hálózati adaptere csak egy hálózati adaptert adhat meg. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
UPDATED_TARGET_NIC1_SUBNET_NAME | Adja meg az áttelepített virtuális gépen található hálózati adapterhez használni kívánt alhálózat nevét. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
UPDATED_TARGET_NIC1_IP | Ha statikus IP-címet szeretne használni, az áttelepített virtuális gépen lévő hálózati adapter által használt IPv4-címet kell megadnia. Ha az IP-címet szeretné automatikusan hozzárendelni, használja az "automatikus" lehetőséget. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
UPDATE_NIC2_ID | A frissítendő hálózati adapter AZONOSÍTÓjának meghatározása. Ha üresen hagyja, a parancsfájl feltételezi, hogy az érték a felderített virtuális gép második hálózati adaptere. Ha nem szeretné frissíteni a virtuális gép hálózati adapterét, hagyja üresen a NIC-nevet tartalmazó mezőket.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Itt adhatja meg a hálózati adapterhez használandó értéket. Az "elsődleges", a "másodlagos" vagy a "DoNotCreate" beállítással adhatja meg, hogy a hálózati adapter elsődleges, másodlagos, vagy nem hozható létre az áttelepített virtuális gépen. A virtuális gép elsődleges hálózati adaptere csak egy hálózati adaptert adhat meg. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
UPDATED_TARGET_NIC2_SUBNET_NAME | Adja meg az áttelepített virtuális gépen található hálózati adapterhez használni kívánt alhálózat nevét. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
UPDATED_TARGET_NIC2_IP | Ha statikus IP-címet szeretne használni, az áttelepített virtuális gépen lévő hálózati adapter által használt IPv4-címet kell megadnia. Ha az IP-címet szeretné automatikusan hozzárendelni, használja az "automatikus" lehetőséget. Ha nem szeretné frissíteni, hagyja üresen a mezőt.
OK_TO_UPDATE | Az "Y" érték megadásával jelezheti, hogy a virtuális gép tulajdonságait frissíteni kell-e a AzMigrate_UpdateMachineProperties parancsfájl futtatásakor. Használja az "N" értéket, vagy hagyja üresen a mezőt.
OK_TO_MIGRATE | Az "Y" használatával jelezze, hogy a virtuális gépet át kell-e telepíteni az AzMigrate_StartMigration parancsfájl futtatásakor. Használja az "N" értéket, vagy hagyja üresen, ha nem szeretné áttelepíteni a virtuális gépet. 
OK_TO_USE_ASSESSMENT | Az "Y" érték megadásával jelezheti, hogy a virtuális gép a AzMigrate_StartReplication parancsfájl futtatásakor értékelési javaslatok használatával elindítja-e a replikálást. Ez a művelet felülírja a CSV-fájlban szereplő TARGET_MACHINE_SIZE és TARGET_DISKTYPE értékeket. Használja az "N" értéket, vagy hagyja üresen, ha nem szeretne értékelési javaslatokat használni.
OK_TO_TESTMIGRATE | Az "Y" érték megadásával jelezheti, hogy a virtuális gépet a AzMigrate_StartTestMigration parancsfájl futtatásakor kell-e áttelepíteni. Használja az "N" értéket, vagy hagyja üresen, ha nem szeretné tesztelni a virtuális gépet. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Az "Y" érték megadásával jelezheti, hogy a virtuális gép replikálási állapotát frissíteni kell-e a AzMigrate_ReplicationStatus parancsfájl futtatásakor. Használja az "N" értéket, vagy hagyja üresen, ha nem szeretné frissíteni a replikálási állapotot.
OK_TO_CLEANUP | Az "Y" érték megadásával jelezheti, hogy a virtuális gép replikálását a AzMigrate_StopReplication parancsfájl futtatásakor kell-e törölni. Használja az "N" értéket, vagy hagyja üresen a mezőt.
OK_TO_TESTMIGRATE_CLEANUP | Az "Y" érték megadásával jelezheti, hogy a virtuális gép tesztelésének áttelepítését a AzMigrate_CleanUpTestMigration parancsfájl futtatásakor kell-e törölni. Használja az "N" értéket, vagy hagyja üresen a mezőt.


## <a name="script-execution"></a>Parancsfájl-végrehajtás

Miután a CSV elkészült, végrehajthatja a következő lépéseket a helyszíni VMware virtuális gépek áttelepítéséhez.

**. Lépés #** | **Parancsfájl neve** | **Leírás**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Engedélyezze a replikálást a CSV-ben felsorolt összes virtuális gépen, a szkript létrehoz egy CSV-kimenetet és egy naplófájlt a hibaelhárításhoz.
2 | AzMigrate_ReplicationStatus.ps1 | A replikáció állapotának ellenőrzését, a parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépek állapotával, valamint egy naplófájlt a hibaelhárításhoz.
3 | AzMigrate_UpdateMachineProperties.ps1 | Miután a virtuális gépek elvégezték a kezdeti replikálást, ezzel a parancsfájllal frissíthetik a virtuális gép céljának tulajdonságait (a számítási és hálózati tulajdonságok). A parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
4 | AzMigrate_StartTestMigration.ps1 |  Indítsa el a feladatátvételi tesztet az összes olyan virtuális gépen, amely az áttelepítéshez van konfigurálva a CSV-ben. A parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
5 | AzMigrate_CleanUpTestMigration.ps1 | Ha manuálisan érvényesíti azokat a virtuális gépeket, amelyek feladatátvételt végeztek, ezzel a szkripttel törölheti a fürtben lévő összes olyan virtuális gép feladatátvételi tesztjét, amely az áttelepítési teszthez van konfigurálva. A parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
6 | AzMigrate_StartMigration.ps1 | Az áttelepítéshez konfigurált CSV-fájlban felsorolt összes virtuális gép áttelepítésének elindítása. A parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.
7 | AzMigrate_StopReplication.ps1 | Leállítja a virtuális gép replikálását a sikeres áttelepítés után, vagy ha más okok miatt meg szeretné szakítani a replikálást. A parancsfájl egy CSV-kimenetet hoz létre az egyes virtuális gépekhez tartozó feladatok részleteivel.


A következő szkripteket más parancsfájlok is meghívja az összes olyan Azure Migrate művelethez, mint például a replikáció engedélyezése, a teszt áttelepítésének indítása, a virtuális gép tulajdonságainak frissítése és így tovább. Győződjön meg arról, hogy az összes parancsfájl ugyanabban a mappában/elérési úton található. 

**. Lépés #** | **Parancsfájl neve** | **Leírás**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Az értékelési tulajdonságok (API-n keresztül), a felderített virtuális gépek és a replikáló virtuális gépek beolvasására szolgáló függvényeket tartalmazó általános parancsfájl. 
2 | AzMigrate_CSV_Processor.ps1 | A CSV-fájl műveleteihez használt függvényeket tartalmazó közös parancsfájl, beleértve a naplók betöltését, olvasását és nyomtatását. 
3 | AzMigrate_Logger.ps1 | A naplófájl Azure Migrate Automation-műveletekhez való generálásához meghívott általános parancsfájl. A naplófájl formátuma log.Scriptname.Datetime.txt.

A fentiek mellett a mappa olyan AzMigrate_Template.ps1 is tartalmaz, amely tartalmazza a csontváz-keretrendszert az egyéni parancsfájlok különböző Azure Migrate műveletekhez való létrehozásához. 

### <a name="script-execution-syntax"></a>Parancsfájl-végrehajtás szintaxisa

A parancsfájlok letöltése után a parancsfájlok a következőképpen hajthatók végre.

Ha végre szeretné hajtani a parancsfájlt a virtuális gépek replikálásának elindításához a Input.csv fájl használatával, használja a következő szintaxist. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Ha többet szeretne megtudni a VMware virtuális gépek Azure Migrate használatával történő áttelepítéséről Azure PowerShell használatáról, kövesse az [oktatóanyagot](./tutorial-migrate-vmware-powershell.md).