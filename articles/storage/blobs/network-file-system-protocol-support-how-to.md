---
title: Az Azure Blob Storage csatlakoztatása az NFS 3,0 protokoll (előzetes verzió) használatával | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathat tárolót a blob Storage-ban egy Azure-beli virtuális gépről (VM) vagy egy, a helyszínen futó ügyfélről az NFS 3,0 protokoll használatával.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 8ed63a508447104f9073c986debfae73ba7de89f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428643"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>BLOB Storage csatlakoztatása a hálózati fájlrendszer (NFS) 3,0 protokoll (előzetes verzió) használatával

A blob Storage-tárolót egy Linux-alapú Azure-beli virtuális gépről (VM) vagy egy, a helyszínen futó Linux rendszerről is csatlakoztathatja az NFS 3,0 protokoll használatával. Ez a cikk lépésről lépésre haladó útmutatót tartalmaz. Ha többet szeretne megtudni az NFS 3,0 protokoll támogatásáról a blob Storage-ban, tekintse meg a következőt: [hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban (előzetes verzió)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>1. lépés: az NFS 3,0 protokoll szolgáltatás regisztrálása az előfizetéssel

1. Nyisson meg egy PowerShell-parancssori ablakot. 

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

4. Regisztrálja a `AllowNFSV3` szolgáltatást a következő parancs használatával.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Regisztrálja az erőforrás-szolgáltatót a következő parancs használatával.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>2. lépés: annak ellenőrzése, hogy a szolgáltatás regisztrálva van-e 

A regisztráció jóváhagyása akár egy órát is igénybe vehet. A regisztráció befejezésének ellenőrzéséhez használja a következő parancsokat.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>3. lépés: Azure-Virtual Network létrehozása (VNet)

A VNet belül szerepelnie kell a Storage-fióknak. A VNet lehetővé teszi az ügyfelek számára, hogy biztonságosan kapcsolódjanak a Storage-fiókhoz. Ha többet szeretne megtudni a VNet, és arról, hogyan hozhat létre egyet, tekintse meg a [Virtual Network dokumentációját](../../virtual-network/index.yml).

> [!NOTE]
> Az azonos VNet lévő ügyfelek csatlakoztatni tudják a fiókban lévő tárolókat. Egy helyszíni hálózaton futó ügyfélről is csatlakoztathat tárolót, de először csatlakoztatnia kell a helyszíni hálózatot a VNet. Lásd: [támogatott hálózati kapcsolatok](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>4. lépés: a hálózati biztonság konfigurálása

A fiókban tárolt adatvédelem egyetlen módja a VNet és más hálózati biztonsági beállítások használata. Az adatvédelmet, például a Azure Active Directory (AD) biztonságot és a hozzáférés-vezérlési listákat (ACL-eket) használó más eszközök még nem támogatják azokat a fiókokat, amelyeken engedélyezve van az NFS 3,0 protokoll támogatása. 

A fiókban lévő információk védelme érdekében tekintse meg a következő ajánlásokat: [hálózati biztonsági javaslatok a blob Storage](security-recommendations.md#networking)-hoz.

## <a name="step-5-create-and-configure-a-storage-account"></a>5. lépés: Storage-fiók létrehozása és konfigurálása

Ha az NFS 3,0 használatával szeretne tárolót csatlakoztatni, létre kell hoznia egy Storage-fiókot, **miután** regisztrálta a szolgáltatást az előfizetésében. A szolgáltatás regisztrálása előtt már nem engedélyezheti a fiókokat. 

A szolgáltatás előzetes kiadásában az NFS 3,0 protokoll támogatott a [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) és az [általános célú v2-](../common/storage-account-overview.md#general-purpose-v2-accounts) fiókok esetében.

A fiók konfigurálásakor válassza ki a következő értékeket:

|Beállítás | Prémium szintű teljesítmény | Standard teljesítmény  
|----|---|---|
|Hely|Az összes elérhető régió |A következő régiók egyike: Kelet-Ausztrália, Korea középső régiója és az USA déli középső régiója   
|Teljesítmény|Prémium| Standard
|Fiók altípusa|BlockBlobStorage| Általános célú v2
|Replikáció|Helyileg redundáns tárolás (LRS)| Helyileg redundáns tárolás (LRS)
|Kapcsolati mód|Nyilvános végpont (kiválasztott hálózatok) vagy magánhálózati végpont |Nyilvános végpont (kiválasztott hálózatok) vagy magánhálózati végpont
|Biztonságos átvitelre van szükség|Disabled (Letiltva)|Disabled (Letiltva)
|Hierarchikus névtér|Engedélyezve|Engedélyezve
|NFS V3|Engedélyezve |Engedélyezve 

Elfogadhatja az összes többi beállítás alapértelmezett értékeit is. 

## <a name="step-6-create-a-container"></a>6. lépés: Tároló létrehozása

Hozzon létre egy tárolót a Storage-fiókban ezen eszközök vagy SDK-k bármelyikének használatával:

|Eszközök|SDK-k|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>7. lépés: a tároló csatlakoztatása

Hozzon létre egy könyvtárat a linuxos rendszeren, majd csatlakoztatjon egy tárolót a Storage-fiókhoz.

1. Linux rendszeren hozzon létre egy könyvtárat.

   ```
   mkdir -p /mnt/test
   ```

2. Csatoljon egy tárolót a következő parancs használatával.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Cserélje le a `<storage-account-name>` parancsban megjelenő helyőrzőt a Storage-fiók nevére.  

   - Cserélje le a `<container-name>` helyőrzőt a tároló nevére.

---

## <a name="resolve-common-issues"></a>Gyakori problémák megoldása

|Probléma/hiba | Feloldás|
|---|---|
|`Access denied by server while mounting`|Győződjön meg arról, hogy az ügyfél támogatott alhálózatot futtat. Tekintse meg a [támogatott hálózati telephelyeket](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Győződjön meg arról, hogy a csatlakoztatni kívánt tároló a funkció regisztrálásának ellenőrzése után jött létre. Lásd: [2. lépés: annak ellenőrzése, hogy a szolgáltatás regisztrálva van-e](#step-2-verify-that-the-feature-is-registered). Győződjön meg arról is, hogy a mount parancsot írja be, és a paramétereket közvetlenül a terminálon adja meg. Ha a parancs bármely részét egy másik alkalmazásból másolja és illeszti be a terminálba, a beillesztett információ rejtett karakterei is okozhatják ezt a hibát.|

## <a name="see-also"></a>Lásd még

[Hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban (előzetes verzió)](network-file-system-protocol-support.md)
