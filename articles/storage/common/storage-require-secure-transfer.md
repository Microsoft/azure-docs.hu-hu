---
title: Biztonságos átvitel szükséges a biztonságos kapcsolatok biztosításához
titleSuffix: Azure Storage
description: Megtudhatja, hogyan kell biztonságos átvitelt igényelni az Azure Storage-ba irányuló kérésekhez. Ha biztonságos átvitelre van szüksége egy Storage-fiókhoz, a rendszer visszautasítja a nem biztonságos kapcsolatoktól származó kérelmeket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555723"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Biztonságos átvitel szükséges a biztonságos kapcsolatok biztosításához

A Storage-fiók úgy is konfigurálható, hogy csak a biztonságos kapcsolatoktól érkező kéréseket fogadja el, a Storage-fiók **biztonságos átviteléhez szükséges** tulajdonságának beállításával. Ha biztonságos átvitelre van szüksége, a nem biztonságos kapcsolatoktól származó kérelmeket a rendszer elutasítja. A Microsoft azt javasolja, hogy minden Storage-fiókhoz mindig biztonságos átvitelt igényeljen.

Ha a biztonságos átvitelre van szükség, az Azure Storage-REST API műveletre irányuló hívást HTTPS-kapcsolaton keresztül kell végrehajtani. A HTTP-n keresztül kezdeményezett kérelmek elutasításra kerülnek.

Ha a Storage-fiók számára biztonságos átvitelre van szükség, az SMB-kapcsolaton keresztüli, titkosítás nélküli Azure-fájlmegosztás csatlakoztatása meghiúsul. A nem biztonságos kapcsolatok példái közé tartoznak például az SMB 2,1, az SMB 3,0 titkosítás nélkül vagy a Linux SMB-ügyfél néhány verziója.

Alapértelmezés szerint a **biztonságos átvitel szükséges** tulajdonsága engedélyezve van a Storage-fiók létrehozásakor.

> [!NOTE]
> Mivel az Azure Storage nem támogatja a HTTPS használatát az egyéni tartománynevek esetében, ez a beállítás nem lesz alkalmazva, ha egyéni tartománynevet használ. És a klasszikus Storage-fiókok nem támogatottak.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Biztonságos átvitel megkövetelése a Azure Portalban

A **biztonságos átvitel szükséges** tulajdonság bekapcsolható, amikor létrehoz egy Storage-fiókot a [Azure Portal](https://portal.azure.com). A meglévő Storage-fiókok esetében is engedélyezhető.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Biztonságos átvitel megkövetelése új Storage-fiókhoz

1. Nyissa meg a **Storage-fiók létrehozása** panelt a Azure Portal.
1. A **biztonságos átvitel szükséges** területen válassza az **engedélyezve** lehetőséget.

   ![Storage-fiók létrehozása panel](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Biztonságos átvitel megkövetelése egy meglévő Storage-fiókhoz

1. Válasszon ki egy meglévő Storage-fiókot a Azure Portal.
1. A Storage-fiók menü **Beállítások** területén válassza a **konfiguráció** elemet.
1. A **biztonságos átvitel szükséges** területen válassza az **engedélyezve** lehetőséget.

   ![A Storage-fiók menüje ablaktábla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Biztonságos átvitel megkövetelése a kódból

Ha programozottan szeretné megkövetelni a biztonságos átvitelt, állítsa a _enableHttpsTrafficOnly_ tulajdonságot _true_ értékre a Storage-fiókon. Ezt a tulajdonságot a Storage erőforrás-szolgáltató REST API, az ügyféloldali kódtárak vagy az eszközök használatával állíthatja be:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [Parancssori felület](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Biztonságos átvitel megkövetelése a PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a mintához az Azure PowerShell modul az 0,7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

 A beállítás megadásához használja a következő parancssort:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

A beállítás engedélyezéséhez használja a következő parancssort:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Biztonságos átvitel megkövetelése az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 A következő parancs használatával keresse meg a beállítást:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

A beállítás engedélyezéséhez használja a következő parancsot:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Következő lépések

[Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
