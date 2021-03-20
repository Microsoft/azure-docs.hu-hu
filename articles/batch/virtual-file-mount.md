---
title: Virtuális fájlrendszer csatlakoztatása egy készlethez
description: Megtudhatja, hogyan csatlakoztathat egy virtuális fájlrendszert egy batch-készlethez.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145484"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Virtuális fájlrendszer csatlakoztatása batch-készlethez

Azure Batch mostantól támogatja a Mount Cloud Storage vagy egy külső fájlrendszer használatát a Batch-készletekben lévő Windows-vagy Linux-alapú számítási csomópontokon. Ha egy számítási csomópont egy készlethez csatlakozik, a rendszer csatlakoztatja a virtuális fájlrendszert, és az adott csomóponton helyi meghajtóként kezeli őket. Olyan fájlrendszerek csatlakoztatására van lehetőség, mint például a Azure Files, az Azure Blob Storage, a hálózati fájlrendszer (NFS), beleértve a [avere vFXT cache](../avere-vfxt/avere-vfxt-overview.md)-t vagy a Common Internet File System (CIFS) rendszert.

Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy virtuális fájlrendszert a számítási csomópontok készletéhez a [.net-hez készült batch Management Library](/dotnet/api/overview/azure/batch)használatával.

> [!NOTE]
> A virtuális fájlrendszer csatlakoztatása támogatott a 2019-08-19-on vagy azt követően létrehozott batch-készleteken. A 2019-08-19-et megelőzően létrehozott batch-készletek nem támogatják ezt a funkciót.
> 
> A számítási csomópontokon a fájlrendszerek csatlakoztatására szolgáló API-k a [Batch .net](/dotnet/api/microsoft.azure.batch) -könyvtár részét képezik.

## <a name="benefits-of-mounting-on-a-pool"></a>A készlethez való csatlakoztatás előnyei

A fájlrendszer a készlethez való csatlakoztatása ahelyett, hogy a feladatok egy nagyméretű adatkészletből beolvassák a saját adataikat, egyszerűbbé és hatékonyabbá teszi a szükséges adatok elérését.

Vegyünk például egy olyan forgatókönyvet, amelynek több feladata is van, amely hozzáférést igényel egy közös adathalmazhoz, például a mozgóképek megjelenítéséhez. Minden feladat egyszerre egy vagy több keretet jelenít meg a jelenet fájljaiból. A jelenet fájljait tartalmazó meghajtó csatlakoztatásával a számítási csomópontok könnyebben férhetnek hozzá a megosztott adatokhoz. Emellett az alapul szolgáló fájlrendszert egymástól függetlenül is kiválaszthatja és méretezheti, az adatokhoz egyidejűleg hozzáférő számítási csomópontok száma és mérete (átviteli és IOPS) alapján. Például egy avere- [vFXT](../avere-vfxt/avere-vfxt-overview.md) elosztott memórián belüli gyorsítótára használható több ezer egyidejű renderelési csomóponttal rendelkező nagyméretű mozgókép-méretezési renderelés támogatására, amely a helyszíni forrásokhoz fér hozzá. Azt is megteheti, hogy a felhőalapú blob-tárolóban már tárolt adat esetében az [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) helyi fájlrendszerként csatlakoztatható. A Blobfuse csak Linux-csomópontokon érhető el, azonban a [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) hasonló munkafolyamatot biztosít, és Windows és Linux rendszeren is elérhető.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Virtuális fájlrendszer csatlakoztatása egy készlethez  

Ha a készletben egy virtuális fájlrendszert csatlakoztat, a fájlrendszer elérhetővé válik a készlet összes számítási csomópontja számára. A fájlrendszer akkor van konfigurálva, amikor egy számítási csomópont egy készlethez csatlakozik, vagy amikor a csomópont újra van indítva vagy rendszerképbe van állítva.

Ha fájlrendszert szeretne csatlakoztatni egy készlethez, hozzon létre egy `MountConfiguration` objektumot. Válassza ki a virtuális fájlrendszerhez illő objektumot:,,, `AzureBlobFileSystemConfiguration` `AzureFileShareConfiguration` `NfsMountConfiguration` vagy `CifsMountConfiguration` .

Az összes csatlakoztatási konfigurációs objektumnak a következő alapparaméterekre van szüksége. Néhány csatlakoztatási konfigurációhoz a használt fájlrendszerhez tartozó paraméterek tartoznak, amelyeket a példákban talál részletesebben.

- **Fiók neve vagy forrása**: virtuális fájlmegosztás csatlakoztatásához szüksége lesz a Storage-fiók vagy a forrásának nevére.
- **Relatív csatlakoztatási útvonal vagy forrás**: a számítási csomóponthoz csatlakoztatott fájlrendszer helye, a `fsmounts` csomóponton keresztül elérhető standard könyvtárhoz viszonyítva `AZ_BATCH_NODE_MOUNTS_DIR` . A pontos hely a csomóponton használt operációs rendszertől függően változhat. Például egy Ubuntu-csomópont fizikai helye van leképezve `mnt\batch\tasks\fsmounts` , és egy CentOS csomóponton, amelyhez hozzá van rendelve `mnt\resources\batch\tasks\fsmounts` .
- **Csatlakoztatási beállítások vagy blobfuse-beállítások**: ezek a beállítások a fájlrendszer csatlakoztatásának konkrét paramétereit írják le.

Az `MountConfiguration` objektum létrehozása után rendelje hozzá az objektumot a `MountConfigurationList` tulajdonsághoz, amikor létrehozza a készletet. A fájlrendszer akkor van csatlakoztatva, amikor egy csomópont egy készlethez csatlakozik, vagy amikor a csomópont újra lett indítva vagy alaphelyzetbe áll.

Ha a fájlrendszer csatlakoztatva van, a rendszer létrehoz egy környezeti változót, amely a csatlakoztatott fájlrendszerek, valamint a `AZ_BATCH_NODE_MOUNTS_DIR` naplófájlok helyére mutat, amelyek a hibaelhárításhoz és a hibakereséshez hasznosak. A naplófájlok részletes ismertetését a [csatlakoztatási hibák diagnosztizálása](#diagnose-mount-errors) című szakaszban találja.  

> [!IMPORTANT]
> A készleten lévő csatlakoztatott fájlrendszerek maximális száma 10. A részleteket és egyéb korlátokat a [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#other-limits) című részben tekintheti meg.

## <a name="examples"></a>Példák

Az alábbi kódrészletek különböző fájlmegosztást mutatnak be a számítási csomópontok készletéhez.

### <a name="azure-files-share"></a>Azure Files megosztás

Azure Files a standard Azure Cloud File System ajánlat. Ha többet szeretne megtudni arról, hogyan kérheti le a paraméterek bármelyikét a csatlakoztatási konfigurációs kód mintájában, tekintse meg a [Azure Files megosztás használata](../storage/files/storage-how-to-use-files-windows.md)című témakört.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure-Blob fájlrendszer

Egy másik lehetőség az Azure Blob Storage használata a [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)-on keresztül. A blob-fájlrendszer csatlakoztatásához a `AccountKey` vagy `SasKey` a Storage-fiók szükséges. A kulcsok beszerzésével kapcsolatos információkért lásd: a [Storage-fiók hozzáférési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md)vagy [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-sas-overview.md). A blobfuse használatával kapcsolatos további információkért tekintse meg a blobfuse- [hibákkal kapcsolatos gyakori kérdések](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)című témakört. A blobfuse csatlakoztatott könyvtár alapértelmezett hozzáférésének lekéréséhez futtassa **rendszergazdaként** a feladatot. A Blobfuse csatlakoztatja a könyvtárat a felhasználói tárhelyen, a készlet létrehozásakor pedig root-ként van csatlakoztatva. A Linux rendszerben minden **rendszergazdai** feladat a root. A biztosítéki modul összes beállítását a [biztosítéki útmutató lapon](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)találja.

A hibaelhárítási útmutatón kívül a blobfuse-tárház GitHub-problémái is hasznosak lehetnek az aktuális blobfuse problémák és megoldások kereséséhez. További információ: blobfuse- [problémák](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Hálózati fájlrendszer

A hálózati fájlrendszer (NFS) olyan készlet-csomópontokhoz is csatlakoztatható, amelyek lehetővé teszik, hogy a hagyományos fájlrendszerek könnyen hozzáférhessenek Azure Batch csomópontok számára. Ez lehet a felhőben üzembe helyezett egyetlen NFS-kiszolgáló, vagy egy virtuális hálózaton keresztül elért helyszíni NFS-kiszolgáló. Azt is megteheti, hogy kihasználja a [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) elosztott memóriabeli gyorsítótárazási megoldását, amely zökkenőmentesen kapcsolódik a helyszíni tárolóhoz, az adatok igény szerinti beolvasása a gyorsítótárba, valamint nagy teljesítményű és méretezhető felhőalapú számítási csomópontokra.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

A Common Internet File Systems (CIFS) olyan készlet-csomópontokhoz is csatlakoztatható, amelyek lehetővé teszik, hogy a hagyományos fájlrendszerek könnyen hozzáférhessenek Azure Batch csomópontok számára. A CIFS egy fájlmegosztási protokoll, amely egy nyílt és platformfüggetlen mechanizmust biztosít a hálózati kiszolgáló fájljainak és szolgáltatásainak igényléséhez. A CIFS a Microsoft kiszolgáló-üzenetblokk (SMB) protokolljának továbbfejlesztett verziója az internetes és az intranetes fájlmegosztás számára, és a külső fájlrendszerek csatlakoztatására szolgál a Windows-csomópontokon. További információ az SMB-ről: [fájlkiszolgáló és SMB](/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Csatlakoztatási hibák diagnosztizálása

Ha a csatlakoztatási konfiguráció meghiúsul, a készlet számítási csomópontja sikertelen lesz, és a csomópont állapota használhatatlanná válik. A csatlakoztatási konfigurációs hibák diagnosztizálásához tekintse [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) meg a tulajdonságot a hibával kapcsolatos részletekért.

A naplófájlok hibakereséshez való beszerzéséhez használja a [OutputFiles](batch-task-output-files.md) a `*.log` fájlok feltöltéséhez. A `*.log` fájlok a helyhez tartozó fájlrendszer csatlakoztatásával kapcsolatos információkat tartalmaznak `AZ_BATCH_NODE_MOUNTS_DIR` . A csatlakoztatási naplófájlok formátuma a `<type>-<mountDirOrDrive>.log` következő: minden egyes csatlakoztatáshoz. Például egy nevű csatlakoztatási `cifs` könyvtárhoz a következő `test` nevű csatlakozási naplófájl lesz: `cifs-test.log` .

## <a name="supported-skus"></a>Támogatott SKU-i

| Publisher | Ajánlat | Termékváltozat | Azure Files megosztás | Blobfuse | NFS-csatlakoztatás | CIFS-csatlakoztatás |
|---|---|---|---|---|---|---|
| kötegelt | renderelés – centos73 | renderelési | :heavy_check_mark: <br>Megjegyzés: kompatibilis a CentOS 7,7-mel</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04 – LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | x | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft – ADS | Linux – adattudomány – virtuális gép | linuxdsvm | :heavy_check_mark: <br>Megjegyzés: kompatibilis a CentOS 7,4-mel. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-batch | CentOS – tároló | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-batch | CentOS-Container-RDMA | 7,4 | :heavy_check_mark: <br>Megjegyzés: a A_8 vagy a 9 tárolót támogatja</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-batch | Ubuntu – kiszolgáló – tároló | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft – dsvm | Linux – adattudomány – virtuális gép – Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS – HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | x | x | x | x |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | x | x | x |

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Files-megosztás Windows vagy Linux [rendszeren](../storage/files/storage-how-to-use-files-windows.md) való [](../storage/files/storage-how-to-use-files-linux.md)csatlakoztatásáról.
- Tudnivalók a [blobfuse](https://github.com/Azure/azure-storage-fuse) virtuális fájlrendszerek használatáról és csatlakoztatásáról.
- A [hálózati fájlrendszer áttekintése](/windows-server/storage/nfs/nfs-overview) című témakörben MEGISMERHETI az NFS-t és annak alkalmazásait.
- A CIFS-vel kapcsolatos további tudnivalókért tekintse meg a [Microsoft SMB protokoll és a CIFS protokoll áttekintése](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) című témakört.
