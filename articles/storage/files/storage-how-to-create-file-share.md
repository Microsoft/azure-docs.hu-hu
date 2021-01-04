---
title: Azure-fájlmegosztás létrehozása
titleSuffix: Azure Files
description: Azure-fájlmegosztás létrehozása a Azure Portal, a PowerShell vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 705910a9e2f4ebc80a63ab22ac4edecc5ae03cd0
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724799"
---
# <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Azure-fájlmegosztás létrehozásához három kérdést kell megválaszolnia, hogy miként fogja használni:

- **Mik az Azure-fájlmegosztás teljesítményével kapcsolatos követelmények?**  
    A Azure Files a szabványos fájlmegosztást (beleértve a tranzakciós optimalizált, a gyakori és a ritka fájlmegosztást is), amelyek merevlemez-alapú (HDD-alapú) hardveren és prémium fájlmegosztáson futnak, amelyek SSD-alapú (SSD-alapú) hardveren futnak.

- **Milyen méretű fájlmegosztás szükséges?**  
    A standard fájlmegosztás akár 100 TiB-re is terjedhet, azonban ez a funkció alapértelmezés szerint nincs engedélyezve. Ha 5 TiB-nál nagyobb fájlmegosztás szükséges, engedélyeznie kell a nagyméretű fájlmegosztás szolgáltatást a Storage-fiókhoz. A prémium fájlmegosztás speciális beállítás nélkül akár 100 TiB-ra is kiépíthető, azonban a prémium szintű fájlmegosztás kiépítését nem kell fizetnie, mint a normál fájlmegosztás esetében. Ez azt jelenti, hogy a fájlmegosztást sokkal nagyobb mértékben kell kiépíteni, mint amennyire szüksége lesz a tárterület teljes költsége növelésére.

- **Milyen redundancia-követelményeket támaszt az Azure-fájlmegosztás?**  
    A standard fájlmegosztás a helyileg redundáns (LRS), a Zone redundáns (ZRS), a Geo-redundáns (GRS) vagy a Geo-zóna-redundáns (GZRS) tárolókat kínálja, azonban a nagyméretű fájlmegosztás funkció csak a helyileg redundáns és a zóna redundáns fájlmegosztás esetén támogatott. A prémium szintű fájlmegosztás nem támogatja a Geo-redundancia bármely formáját.

    A prémium szintű fájlmegosztás helyi redundanciával érhető el a legtöbb régióban, amely a Storage-fiókokat és a zóna-redundanciát kínálja a régiók kisebb részhalmazában. Annak megállapításához, hogy a prémium fájlmegosztás jelenleg elérhető-e az Ön régiójában, tekintse meg az Azure-ban [elérhető termékek területét](https://azure.microsoft.com/global-infrastructure/services/?products=storage) . További információ a ZRS támogató régiókkal kapcsolatban: [Azure Storage redundancia](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

A három lehetőségről további információt a [Azure Files központi telepítésének tervezése](storage-files-planning.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek
- Ez a cikk azt feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.
- Ha Azure PowerShell szeretne használni, [telepítse a legújabb verziót](/powershell/azure/install-az-ps).
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
Az Azure-fájlmegosztás *tárolási fiókokra* van telepítve, amelyek a tárolók megosztott készletét képviselő legfelső szintű objektumok. Ez a tárterület több fájlmegosztás üzembe helyezésére is használható. 

Az Azure több típusú Storage-fiókot támogat a különböző tárolási forgatókönyvekhez, de a Azure Files két fő típusa van. A létrehozandó Storage-fiók típusa attól függ, hogy szabványos fájlmegosztást vagy prémium szintű fájlmegosztást szeretne-e létrehozni: 

- **Általános célú 2-es verziójú (GPv2) Storage-fiókok**: a GPv2 Storage-fiókok lehetővé teszik az Azure-fájlmegosztás szabványos/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztás tárolásán kívül a GPv2 más tárolási erőforrásokat is tárolhat, például blob-tárolókat, várólistákat vagy táblákat. A fájlmegosztás üzembe helyezhető a tranzakcióra optimalizált (alapértelmezett), a gyors vagy a lassú elérésű szinteken.

- **FileStorage Storage-fiókok**: a FileStorage Storage-fiókok lehetővé teszik az Azure-fájlmegosztás prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. FileStorage-fiókokat csak az Azure-fájlmegosztás tárolására lehet használni; egy FileStorage-fiókban nem helyezhetők üzembe más tárolási erőforrások (blob-tárolók, várólisták, táblák stb.).

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha Storage-fiókot szeretne létrehozni a Azure Portalon keresztül, válassza az **+ erőforrás létrehozása** lehetőséget az irányítópulton. Az eredményül kapott Azure Marketplace keresési ablakban keresse meg a **Storage-fiók** elemet, és válassza ki a találatok eredményét. Ez egy, a Storage-fiókok áttekintő lapjára fog vezetni; Válassza a **Létrehozás** lehetőséget a Storage-fiók létrehozása varázsló folytatásához.

![A Storage-fiók gyors létrehozási lehetőségének képernyőképe egy böngészőben](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Az alapismeretek szakasz
A Storage-fiók létrehozásához szükséges első szakasz az **alapok** címkéje. Ez tartalmazza az összes kötelező mezőt a Storage-fiók létrehozásához. GPv2-fiók létrehozásához győződjön meg arról, hogy a **teljesítmény** választógomb a *standard* értékre van beállítva, és a **Fiók típusa** legördülő lista *StorageV2 (általános célú v2)* van kiválasztva.

![Képernyőfelvétel a teljesítmény választógombról a szabványos kiválasztott és a StorageV2 kiválasztott fiókkal](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage-fiók létrehozásához győződjön meg arról, hogy a **teljesítmény** választógomb a *prémium* értékre van beállítva, és a **Fiók típusa** legördülő lista *FileStorage* van kiválasztva.

![Képernyőkép a teljesítmény választógombról a prémium kiválasztott és a FileStorage kiválasztott fiókkal](media/storage-how-to-create-file-share/create-storage-account-2.png)

A többi alapvető mező független a választható Storage-fióktól:
- **Előfizetés**: a szolgáltatásba telepítendő Storage-fiók előfizetése. 
- **Erőforráscsoport**: az az erőforráscsoport, amelybe telepíteni kívánja a Storage-fiókot. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévő erőforráscsoportot is. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot.
- **Storage-fiók neve**: a létrehozandó Storage-fiók erőforrásának neve. Ennek a névnek globálisan egyedinek kell lennie, de bármilyen más nevet is megadhat. Az Azure-fájlmegosztás SMB-n keresztüli csatlakoztatásakor a rendszer a Storage-fiók nevét fogja használni.
- **Hely**: az-ban telepítendő Storage-fiók régiója. Ez lehet az erőforráscsoporthoz vagy bármely más elérhető régióhoz társított régió.
- **Replikáció**: bár ez a replikáció címkével van ellátva, ez a mező valójában **redundanciát** jelent. Ez a kívánt redundancia szintje: helyileg redundancia (LRS), Zone redundancia (ZRS), Geo-redundancia (GRS) és geo-Zone-redundancia. Ez a legördülő lista olvasási hozzáférésű geo-redundancia (RA-GRS) és olvasási hozzáférésű geo-Zone redundancia (RA-GZRS) is tartalmaz, amelyek nem vonatkoznak az Azure-fájlmegosztás; a kiválasztott Storage-fiókban létrehozott fájlmegosztás valójában geo-redundáns vagy geo-Zone-redundáns lesz. A régiótól vagy a kiválasztott Storage-fiók típusától függően előfordulhat, hogy bizonyos redundancia-beállítások nem engedélyezettek.
- **Blob hozzáférési szintje**: Ez a mező nem vonatkozik a Azure Filesre, így választhatja ki a választógombok egyikét sem. 

> [!Important]  
> A blob-hozzáférési szintek kiválasztásakor nincs hatással a fájlmegosztás szintjére.

#### <a name="the-networking-blade"></a>A hálózatkezelés panel
A hálózatkezelés szakasz a hálózatkezelési beállítások konfigurálását teszi lehetővé. Ezek a beállítások nem kötelezőek a Storage-fiók létrehozásához, és szükség esetén később is konfigurálhatók. További információ ezekről a lehetőségekről: [Azure Files hálózatkezelési megfontolások](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>A speciális panel
A speciális szakasz számos fontos beállítást tartalmaz az Azure-fájlmegosztás számára:

- **Biztonságos átvitel szükséges**: ebben a mezőben azt jelzi, hogy a Storage-fiókhoz szükség van-e a Storage-fiókkal folytatott kommunikáció titkosítására. Javasoljuk, hogy ezt a beállítást ne engedélyezze, ha azonban SMB 2,1-támogatásra van szüksége, le kell tiltania ezt. Azt javasoljuk, hogy tiltsa le a titkosítást, amely korlátozza a Storage-fiók elérését a szolgáltatási végpontokkal és/vagy privát végpontokkal rendelkező virtuális hálózathoz.
- **Nagyméretű fájlmegosztás**: ebben a mezőben engedélyezhető a fájlmegosztást a 100 TiB-ra felölelő fájlmegosztás. Ha engedélyezi ezt a funkciót, a Storage-fiók csak a helyileg redundáns és a zónák redundáns tárolási lehetőségeire lesz korlátozva. Ha a GPv2 Storage-fiók engedélyezve lett a nagyméretű fájlmegosztás esetében, nem tilthatja le a nagyméretű fájlmegosztás funkciót. A FileStorage Storage-fiókok (Storage-fiókok prémium fájlmegosztás esetén) nem rendelkeznek ezzel a lehetőséggel, mivel az összes prémium fájlmegosztás akár 100 TiB-ra is méretezhető. 

![Képernyőkép a Azure Files vonatkozó fontos speciális beállításokról](media/storage-how-to-create-file-share/create-storage-account-3.png)

A Speciális lapon elérhető egyéb beállítások (blob Soft-DELETE, Azure Data Lake Storage Gen 2 és a blob Storage NFSv3) nem vonatkoznak a Azure Filesra.

#### <a name="tags"></a>Címkék
A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. Ezek opcionálisak, és a Storage-fiók létrehozása után is alkalmazhatók.

#### <a name="review--create"></a>Ellenőrzés és létrehozás
A Storage-fiók létrehozásának utolsó lépése a **Létrehozás** gomb kijelölése a **felülvizsgálat + létrehozás** lapon. Ez a gomb nem érhető el, ha a Storage-fiók összes kötelező mezője nincs kitöltve.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha a PowerShell használatával hoz létre egy Storage-fiókot, a `New-AzStorageAccount` parancsmagot fogjuk használni. Ennek a parancsmagnak számos lehetősége van; csak a szükséges beállítások jelennek meg. A speciális beállításokkal kapcsolatos további tudnivalókért tekintse meg a [ `New-AzStorageAccount` parancsmag dokumentációját](/powershell/module/az.storage/new-azstorageaccount).

A Storage-fiók és a további fájlmegosztás létrehozásának egyszerűbbé tételéhez több paramétert is tárolunk a változókban. A változó tartalmait tetszés szerinti értékre cserélheti, azonban vegye figyelembe, hogy a Storage-fiók nevének globálisan egyedinek kell lennie.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Szabványos Azure-fájlmegosztás tárolására alkalmas Storage-fiók létrehozásához a következő parancsot fogjuk használni. A `-SkuName` paraméter a kívánt redundancia típusára vonatkozik; ha földrajzilag redundáns vagy földrajzilag redundáns tárolási fiókot szeretne használni, akkor a paramétert is el kell távolítania `-EnableLargeFileShare` .

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Prémium szintű Azure-fájlmegosztás tárolására alkalmas Storage-fiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a `-SkuName` paraméter úgy módosult, hogy mindkettőt `Premium` és a helyileg redundáns () kívánt redundancia szintjét is tartalmazza `LRS` . A `-Kind` paraméter azért van, `FileStorage` mert a `StorageV2` prémium fájlmegosztás FileStorage-fiókban kell létrehozni a GPv2 helyett.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha a Storage-fiókot az Azure CLI használatával szeretné létrehozni, az az Storage Account Create parancsot fogjuk használni. Ennek a parancsnak számos lehetősége van; csak a szükséges beállítások jelennek meg. A speciális beállításokkal kapcsolatos további tudnivalókért tekintse meg a [ `az storage account create` parancs dokumentációját](/cli/azure/storage/account).

A Storage-fiók és a további fájlmegosztás létrehozásának egyszerűbbé tételéhez több paramétert is tárolunk a változókban. A változó tartalmait tetszés szerinti értékre cserélheti, azonban vegye figyelembe, hogy a Storage-fiók nevének globálisan egyedinek kell lennie.

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Szabványos Azure-fájlmegosztás tárolására alkalmas Storage-fiók létrehozásához a következő parancsot fogjuk használni. A `--sku` paraméter a kívánt redundancia típusára vonatkozik; ha földrajzilag redundáns vagy földrajzilag redundáns tárolási fiókot szeretne használni, akkor a paramétert is el kell távolítania `--enable-large-file-share` .

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Prémium szintű Azure-fájlmegosztás tárolására alkalmas Storage-fiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a `--sku` paraméter úgy módosult, hogy mindkettőt `Premium` és a helyileg redundáns () kívánt redundancia szintjét is tartalmazza `LRS` . A `--kind` paraméter azért van, `FileStorage` mert a `StorageV2` prémium fájlmegosztás FileStorage-fiókban kell létrehozni a GPv2 helyett.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Fájlmegosztás létrehozása
Miután létrehozta a Storage-fiókját, az összes marad a fájlmegosztás létrehozása. Ez a folyamat többnyire azonos, függetlenül attól, hogy prémium szintű fájlmegosztást vagy szabványos fájlmegosztást használ-e. Vegye figyelembe a következő különbségeket.

A standard fájlmegosztás a standard szintekre telepíthető: a tranzakció optimalizált (alapértelmezett), a gyors vagy a ritka elérésű. Ez egy olyan fájlmegosztási réteg, amelyet a Storage-fiók blob- **hozzáférési rétege** nem érint (ez a tulajdonság csak az Azure Blob Storage-hoz kapcsolódik – nem kapcsolódik a Azure Fileshoz). A megosztás szintjét bármikor megváltoztathatja a telepítése után. A prémium fájlmegosztás nem alakítható át közvetlenül standard szintű fájlmegosztást semmilyen standard szinten.

> [!Important]  
> A fájlmegosztást a GPv2 belül is áthelyezheti (a tranzakció optimalizált, gyors és lassú). Megosztható a csomagok közötti váltások tranzakció: a melegebb szintről a hűvösebb szintre való áttéréskor a hűvösebb réteg írási tranzakciós díja a megosztás minden egyes fájljánál, míg a hűvösebb szintről a melegebb szintre való áttérés a ritka réteg olvasási tranzakciós díját terheli a megosztás összes fájlja esetében.

A **kvóta** tulajdonság azt jelenti, hogy némileg eltérő a prémium és a standard fájlmegosztás között:

- A standard fájlmegosztás esetében ez az Azure-fájlmegosztás felső határa, amelyen kívül a végfelhasználók nem mehetnek el. A standard fájlmegosztás kvótájának elsődleges célja a költségvetés: "nem szeretném, hogy ez a fájlmegosztás ne növelje ezt a pontot". Ha nincs megadva kvóta, a normál fájlmegosztás akár 100 TiB-ra (vagy 5 TiB-ra) is terjedhet, ha a nagyméretű fájlmegosztás tulajdonság nincs beállítva a Storage-fiókhoz.

- A prémium fájlmegosztás esetében a kvóta túl van terhelve a **kiépített mérethez**. A kiosztott méret az a mennyiség, amelyet a rendszer a tényleges használattól függetlenül számláz. A prémium fájlmegosztás kiépítésekor két tényezőt érdemes figyelembe venni: 1.) a megosztás jövőbeli növekedése a terület kihasználtsága szempontjából, és 2) a számítási feladatok IOPS szükséges. Minden kiépített GiB felhatalmazza Önt további fenntartott és burst IOPS. A prémium szintű fájlmegosztás tervezésével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás](understanding-billing.md#provisioned-billing)kiosztása című témakört.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha most hozta létre a Storage-fiókját, akkor az **erőforrás keresése** lehetőségre kattintva megnyithatja azt a telepítési képernyőről. Ha korábban már létrehozta a Storage-fiókot, megnyithatja azt az azt tartalmazó erőforráscsoport használatával. Miután a Storage-fiókban kijelöli a **fájlmegosztás** címkét (a **fájlmegosztást** a Storage-fiók tartalomjegyzékében is megnyithatja).

![Képernyőfelvétel a fájlmegosztás csempéről](media/storage-how-to-create-file-share/create-file-share-1.png)

A fájlmegosztás listájában látnia kell minden olyan fájlmegosztást, amelyet korábban hozott létre ebben a Storage-fiókban. üres tábla, ha még nincs fájlmegosztás létrehozva. Válassza a **+ fájlmegosztás** lehetőséget egy új fájlmegosztás létrehozásához.

Az új fájlmegosztás panelnek a képernyőn kell megjelennie. Fájlmegosztás létrehozásához hajtsa végre az új fájlmegosztás panelen található mezőket:

- **Name (név**): a létrehozandó fájlmegosztás neve.
- **Kvóta**: a fájlmegosztás kvótája a szabványos fájlmegosztás esetében; a prémium fájlmegosztás esetén a fájlmegosztás kiosztott mérete.
- **Rétegek**: a fájlmegosztás kiválasztott szintje. Ez a mező csak **általános célú (GPv2) Storage-fiókban** érhető el. Kiválaszthatja az optimalizált, a gyors vagy a lassú tranzakciót is. A megosztás szintjei bármikor megváltoztathatók. Javasoljuk, hogy az áttelepítés során a lehető legnagyobb szintet válassza, a tranzakciós költségek csökkentése érdekében, majd az áttelepítés befejezése után az alacsonyabb szintre váltson.

Válassza a **Létrehozás** lehetőséget az új megosztás létrehozásának befejezéséhez. Vegye figyelembe, hogy ha a Storage-fiókja virtuális hálózatban található, akkor nem fog tudni sikeres Azure-fájlmegosztást létrehozni, kivéve, ha az ügyfél a virtuális hálózatban is szerepel. Az időponthoz kötött korlátozásokat az Azure PowerShell parancsmaggal is elvégezheti `New-AzRmStorageShare` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Létrehozhat egy Azure-fájlmegosztást a [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) parancsmaggal. A következő PowerShell-parancsok feltételezik, hogy beállította a változókat, a `$resourceGroupName` `$storageAccountName` Storage-fiók létrehozása Azure PowerShell szakaszban megadott módon. 

A következő példa egy explicit szintű fájlmegosztás létrehozását mutatja be a `-AccessTier` paraméter használatával. Ehhez az előnézet az. Storage modult kell használnia, ahogy az a példában is látható. Ha nincs megadva egy szint, vagy azért, mert a GA az. Storage modult használja, vagy mert nem adta meg ezt a parancsot, a normál fájlmegosztás alapértelmezett szintje a tranzakcióra optimalizált.

> [!Important]  
> A prémium fájlmegosztás esetén a `-QuotaGiB` paraméter a fájlmegosztás kiépített méretére hivatkozik. A fájlmegosztás kiosztott mérete az a mennyiség, amelyet a rendszer a használattól függetlenül számláz. A standard fájlmegosztás számlázása a használat alapján történik, nem pedig kiosztott méret.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

> [!Note]  
> A szintek a PowerShell használatával történő beállításának és módosításának lehetősége az előnézet az. Storage PowerShell-modulban található. Ezek a parancsmagok vagy a kimenetük megváltozhatnak az általánosan elérhető az az. Storage PowerShell-modulban való kiadása előtt, ezért a parancsfájlok létrehozásával vegye figyelembe.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Létrehozhat egy Azure-fájlmegosztást a [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) paranccsal. A következő Azure CLI-parancsok feltételezik, hogy a `$resourceGroupName` `$storageAccountName` Storage-fiók létrehozása az Azure CLI-vel című szakaszban leírt változókat beállította, és a fentiek szerint van megadva.

A fájlmegosztás adott szinten való létrehozásához vagy áthelyezéséhez szükséges funkciók az Azure CLI legújabb frissítésében érhetők el. Az Azure CLI frissítése az Ön által használt operációs rendszerre/Linux-disztribúcióra vonatkozik. Az Azure CLI a rendszeren való frissítésével kapcsolatos útmutatásért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

> [!Important]  
> A prémium fájlmegosztás esetén a `--quota` paraméter a fájlmegosztás kiépített méretére hivatkozik. A fájlmegosztás kiosztott mérete az a mennyiség, amelyet a rendszer a használattól függetlenül számláz. A standard fájlmegosztás számlázása a használat alapján történik, nem pedig kiosztott méret.

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> A (z) paraméterrel rendelkező rétegek beállításának lehetősége a `--access-tier` legújabb Azure CLI-csomag előzetes verziójában érhető el. Ez a parancs vagy a kimenete változhat az általánosan elérhetőként való jelölés előtt, ezért hozzon létre parancsfájlokat ezt szem előtt tartva.

---

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Azure-fájlmegosztás rétegének módosítása
Az **általános célú v2-(GPv2-) Storage-fiókban** üzembe helyezett fájlmegosztás lehet a tranzakciós optimalizált, a gyors vagy a lassú elérésű szint. Az Azure-fájlmegosztás szintje bármikor módosítható, a fentiekben leírtak szerint a tranzakciós költségekre is.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A fő Storage-fiók **lapon válassza a fájlmegosztás lehetőséget,**  válassza ki **a fájlmegosztás címkét (a** **fájlmegosztást** a Storage-fiók tartalomjegyzékének használatával is megnyithatja).

![Képernyőfelvétel a fájlmegosztás csempéről](media/storage-how-to-create-file-share/create-file-share-1.png)

A fájlmegosztás táblázat listájában válassza ki azt a fájlmegosztást, amelynek a szintjét módosítani szeretné. A fájlmegosztás áttekintése lapon válassza a menü **szintek módosítása** elemét.

![A fájlmegosztás áttekintő oldalának képernyőképe a kijelölt szintet gomb kiemelésével](media/storage-how-to-create-file-share/change-tier-0.png)

Az eredményül kapott párbeszédpanelen válassza ki a kívánt szintet: a tranzakció optimalizált, a gyors vagy a lassú.

![A szintek módosítása párbeszédpanel képernyőképe](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancsmag feltételezi, hogy beállította a ( `$resourceGroupName` `$storageAccountName` `$shareName` ) változót a jelen dokumentum korábbi szakaszaiban leírtak szerint.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A következő Azure CLI-parancs feltételezi, hogy a `$resourceGroupName` , a `$storageAccountName` és a `$shareName` változót a jelen dokumentum korábbi szakaszaiban ismertetett módon állította be.

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>További lépések
- [Tervezze meg Azure Files telepítését,](storage-files-planning.md) vagy [tervezze meg Azure file Sync központi telepítését](storage-sync-files-planning.md). 
- [Hálózatkezelés – áttekintés](storage-files-networking-overview.md).
- Windows, [MacOS](storage-how-to-use-files-mac.md)és [Linux](storage-how-to-use-files-linux.md) [rendszerű](storage-how-to-use-files-windows.md)fájlmegosztás csatlakoztatása és csatlakoztatása.