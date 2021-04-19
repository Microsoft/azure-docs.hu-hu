---
title: Az Azure Blob Storage csatlakoztatása az NFS 3.0 protokoll (előzetes verzió) | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatja a blobtárolóban lévő tárolót egy Azure-beli virtuális gépről (VM) vagy egy helyszínen futó ügyfélről az NFS 3.0 protokoll használatával.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: bf6b06ba7cc7f547f752ffa7877fca186ba4465e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713785"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Blob Storage csatlakoztatása a hálózati fájlrendszer (NFS) 3.0 protokoll (előzetes verzió) használatával

A Blob Storage-ban egy tárolót csatlakoztathat Linux-alapú Azure-beli virtuális gépről vagy egy helyszínen futó Linux-rendszerről az NFS 3.0 protokoll használatával. Ez a cikk részletes útmutatást nyújt. Az NFS 3.0 protokoll Blob Storage-ban való támogatásával kapcsolatos további információkért lásd: Network [File System (NFS) 3.0 protocol support in Azure Blob Storage (preview) (Hálózati fájlrendszer (NFS) 3.0 protokoll](network-file-system-protocol-support.md)támogatása az Azure Blob Storage-ban (előzetes verzió) ).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>1. lépés: Az NFS 3.0 protokollszolgáltatás regisztrálása az előfizetésben

1. Nyisson meg egy PowerShell-parancsablakot. 

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

4. Regisztrálja `AllowNFSV3` a szolgáltatást a következő paranccsal.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Regisztrálja az erőforrás-szolgáltatót a következő paranccsal.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>2. lépés: A funkció regisztrációja ellenőrzése 

A regisztráció jóváhagyása akár egy óráig is tart. A regisztráció befejezésének ellenőrzéséhez használja a következő parancsokat.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>3. lépés: Azure-Virtual Network létrehozása (VNet)

A tárfióknak egy virtuális hálózatban kell lennie. A VNet lehetővé teszi az ügyfelek számára, hogy biztonságosan csatlakozzon a tárfiókhoz. A virtuális hálózatról és annak létrehozásáról a következő dokumentációban talál [további Virtual Network:](../../virtual-network/index.yml).

> [!NOTE]
> Az azonos virtuális hálózatban található ügyfelek tárolókat csatlakoztatnak a fiókjához. Helyszíni hálózaton futó ügyfélről is csatlakoztathat tárolót, de először csatlakoztatnia kell a helyszíni hálózatot a virtuális hálózathoz. Lásd: [Támogatott hálózati kapcsolatok.](network-file-system-protocol-support.md#supported-network-connections)

## <a name="step-4-configure-network-security"></a>4. lépés: A hálózati biztonság konfigurálása

A fiókban található adatok biztonságának egyetlen módja a virtuális hálózat és más hálózati biztonsági beállítások használata. Az adatok biztonságának biztosítása érdekében használt egyéb eszközök, például a fiókkulcsok hitelesítése, az Azure Active Directory(AD) biztonsága és a hozzáférés-vezérlési listák (ACL-ek) még nem támogatottak az olyan fiókokban, amelyeken engedélyezve van az NFS 3.0 protokoll támogatása.

A fiókban lévő adatok biztonságának biztosítása érdekében tekintse meg a következő javaslatokat: Hálózati biztonsági [javaslatok a Blob Storage-hoz.](security-recommendations.md#networking)

## <a name="step-5-create-and-configure-a-storage-account"></a>5. lépés: Tárfiók létrehozása és konfigurálása

A tároló NFS 3.0-val való csatlakoztatásához  létre kell hoznia egy tárfiókot, miután regisztrálta a funkciót az előfizetésében. A szolgáltatás regisztrációja előtt nem engedélyezheti a meglévő fiókokat.

A szolgáltatás előzetes kiadásában az NFS 3.0 protokoll a standard általános célú v2-tárfiókok és a prémium szintű blokkblob-tárfiókok esetében támogatott. Az ilyen típusú tárfiókokkal kapcsolatos további információkért lásd: [Tárfiókok áttekintése.](../common/storage-account-overview.md)

A fiók konfigurálásakor válassza az alábbi értékeket:

|Beállítás | Prémium teljesítmény | Standard teljesítmény  
|----|---|---|
|Hely|Minden elérhető régió |Az alábbi régiók egyike: Ausztrália keleti régiója, Korea középső régiója, AZ USA keleti régiója és az USA déli középső régiója   
|Teljesítmény|Prémium| Standard
|Fiók altípusa|BlockBlobStorage| Általános célú V2
|Replikáció|Helyileg redundáns tárolás (LRS)| Helyileg redundáns tárolás (LRS)
|Kapcsolati mód|Nyilvános végpont (kiválasztott hálózatok) vagy Privát végpont |Nyilvános végpont (kiválasztott hálózatok) vagy Privát végpont
|Biztonságos átvitelre van szükség|Disabled (Letiltva)|Disabled (Letiltva)
|Hierarchikus névtér|Engedélyezve|Engedélyezve
|NFS V3|Engedélyezve |Engedélyezve 

Minden más beállításnál elfogadhatja az alapértelmezett értékeket. 

## <a name="step-6-create-a-container"></a>6. lépés: Tároló létrehozása

Hozzon létre egy tárolót a tárfiókban az alábbi eszközök vagy az SDK-k bármelyikével:

|Eszközök|SDK-k|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>7. lépés: A tároló csatlakoztatása

Hozzon létre egy könyvtárat a Linux rendszeren, majd csatlakoztassa a tárolót a tárfiókban.

1. Linux rendszeren hozzon létre egy könyvtárat.

   ```
   mkdir -p /mnt/test
   ```

2. Csatlakoztassa a tárolót a következő paranccsal.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Cserélje le `<storage-account-name>` a parancsban megjelenő helyőrzőt a tárfiók nevére.  

   - Cserélje le `<container-name>` a helyőrzőt a tároló nevére.

---

## <a name="resolve-common-issues"></a>Gyakori problémák megoldása

|Probléma/hiba | Feloldás|
|---|---|
|`Access denied by server while mounting`|Győződjön meg arról, hogy az ügyfél támogatott alhálózatot futtat. Lásd: [Támogatott hálózati helyek.](network-file-system-protocol-support.md#supported-network-connections)|
|`No such file or directory`| Győződjön meg arról, hogy a csatlakoztatni kívánt tároló a funkció regisztrálásának ellenőrzése után jött létre. Lásd: [2. lépés: Annak ellenőrzése, hogy a szolgáltatás regisztrálva van-e.](#step-2-verify-that-the-feature-is-registered) Ügyeljen arra is, hogy begépelje a csatlakoztatási parancsot és annak paramétereit közvetlenül a terminálba. Ha a parancs bármely részét egy másik alkalmazásból másolja és illeszti be a terminálba, a beillesztett információ rejtett karakterei is okozhatják ezt a hibát.|

## <a name="see-also"></a>Lásd még

[Hálózati fájlrendszer (NFS) 3.0 protokoll támogatása az Azure Blob Storage-ban (előzetes verzió)](network-file-system-protocol-support.md)
