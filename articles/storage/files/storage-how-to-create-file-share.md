---
title: Azure-fájlmegosztás létrehozása
titleSuffix: Azure Files
description: Azure-fájlmegosztás létrehozása a Azure Portal, a PowerShell vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 9caabb8dc7f09e4ef3852d9269d178c086744779
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789806"
---
# <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Azure-fájlmegosztás létrehozásához három kérdést kell megválaszolni a használatával kapcsolatban:

- **Mik az Azure-fájlmegosztás teljesítménykövetelményei?**  
    Azure Files merevlemez-alapú (HDD-alapú) hardveren üzemeltetett standard fájlmegosztásokat, valamint SSD-alapú hardveren üzemeltetett prémium fájlmegosztásokat kínál.

- **Mik az Azure-fájlmegosztás redundanciakövetelményei?**  
    A standard fájlmegosztások helyileg redundáns (LRS), zónaredundáns (ZRS), georedundáns (GRS) vagy geo-zónaredundáns (GZRS) tárolást kínálnak, a nagyméretű fájlmegosztási funkció azonban csak a helyileg redundáns és zónaredundáns fájlmegosztások esetében támogatott. A prémium fájlmegosztások semmilyen georedundaniát nem támogatnak.

    A prémium fájlmegosztások helyi redundanciával és zónaredundanával érhetők el a régiók egy részében. Ha meg kell tudni, hogy a prémium szintű [](https://azure.microsoft.com/global-infrastructure/services/?products=storage) fájlmegosztások jelenleg elérhetők-e az Ön régiójában, tekintse meg az Azure régiónként elérhető termékeit. További információ a ZRS-t támogató régiókról: [Azure Storage redundancia.](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

- **Milyen méretű fájlmegosztásra van szüksége?**  
    A helyi és zónaredundáns tárfiókok esetében az Azure-fájlmegosztások akár 100 TiB-ra is átívelnek, a geo- és geozónaredundáns tárfiókok esetében azonban az Azure-fájlmegosztások legfeljebb 5 TiB-ra is átívelnek. 

További információ a három lehetőségről: [Planning for an Azure Files deployment](storage-files-planning.md)..

## <a name="prerequisites"></a>Előfeltételek
- Ez a cikk feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.
- Ha a legújabb verziót Azure PowerShell, [telepítse a legújabb verziót.](/powershell/azure/install-az-ps)
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
Az Azure-fájlmegosztások tárfiókban *vannak üzembe osztva,* amelyek a tárolók közös készletét képviselő legfelső szintű objektumok. Ez a tárolókészlet több fájlmegosztás üzembe helyezésére is használható. 

Az Azure többféle tárfióktípust támogat az ügyfelek különböző tárolási forgatókönyvei esetében, de a tárfiókok két fő típusa Azure Files. A létrehozni kívánt tárfiók típusa attól függ, hogy standard vagy prémium szintű fájlmegosztást szeretne létrehozni: 

- **Általános célú 2- (GPv2-)** tárfiókok: A GPv2-tárfiókok lehetővé teszik az Azure-fájlmegosztások standard/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztások tárolása mellett a GPv2-tárfiókok más tárolási erőforrásokat, például blobtárolókat, üzenetsorokat vagy táblákat is tárolnak. A fájlmegosztások üzembe helyezhetők a tranzakcióoptimalált (alapértelmezett), a forró vagy a hűtési szinten.

- **FileStorage-tárfiókok:** A FileStorage-tárfiókok lehetővé teszik az Azure-fájlmegosztások prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. A FileStorage-fiókok csak Azure-fájlmegosztások tárolására használhatók; Más tárolási erőforrások (blobtárolók, üzenetsorok, táblák stb.) nem helyezhetők üzembe a FileStorage-fiókban.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Tárfiók létrehozásához a Azure Portal válassza az **+ Erőforrás** létrehozása lehetőséget az irányítópulton. Az eredményül kapott Azure Marketplace keressen rá a **tárfiók** kifejezésre, és válassza ki az eredményül kapott keresési eredményt. Ez a tárfiókok áttekintő oldalára vezet; Válassza **a Létrehozás** lehetőséget a tárfiók-létrehozási varázslóval való folytatáshoz.

![Képernyőkép a tárfiók gyors létrehozási lehetőségről a böngészőben](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Alapvető beállítások
A tárfiók létrehozásához szükséges első szakasz a **Basics (Alapvető alapok) címkével van megcímkézve.** Ez tartalmazza a tárfiók létrehozásához szükséges összes mezőt. GPv2-tárfiók létrehozásához győződjön  meg arról, hogy a  Teljesítmény választógomb *standard,* a Fiók aljának legördülő listája pedig *StorageV2 (általános célú v2)*.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Képernyőkép a teljesítmény választógombról a standard és a fiók fajtája beállítással, a storagev2 elem kijelölve.":::

FileStorage tárfiók létrehozásához győződjön  meg arról, hogy a Teljesítmény választógomb *a Prémium,* a **Fájlmegosztások** pedig a **Prémium** fiók típusa legördülő listában van kiválasztva.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Képernyőkép a teljesítmény választógombról, a prémium elem és a fiók fajtája a kiválasztott filestorage elemekkel.":::

A többi alapvető mező független a választott tárfióktól:
- **Tárfiók neve:** A létrehozni szükséges tárfiók-erőforrás neve. Ennek a névnek globálisan egyedinek kell lennie, de más esetben bármilyen nevet meg tud majd. Amikor SMB-n keresztül csatlakoztat egy Azure-fájlmegosztást, a rendszer a tárfiók nevét használja kiszolgálónévként.
- **Hely:** A tárfiók üzembe helyezési régiója. Ez lehet az erőforráscsoporthoz társított régió vagy bármely más elérhető régió.
- **Replikáció:** Bár ez a replikáció címkével van megcímkézve, ez a mező valójában **redundanciát jelent;** Ez a kívánt redundanciaszint: helyi redundancia (LRS), zónaredundania (ZRS), georedundania (GRS) és geo-zónaredundania (GZRS). Ez a legördülő lista tartalmazza az írási hozzáférésű georedundaniát (RA-GRS) és az írási hozzáférésű geozóna-redundanciát (RA-GZRS), amelyek nem vonatkoznak az Azure-fájlmegosztásra; A kiválasztott tárfiókban létrehozott fájlmegosztások valójában georedundánsak vagy geo-zónaredundánsak lesznek. 

#### <a name="networking"></a>Hálózatkezelés
A hálózat szakaszban konfigurálhatja a hálózati beállításokat. Ezek a beállítások nem kötelezők a tárfiók létrehozásához, és szükség esetén később is konfigurálhatóak. További információ ezekről a lehetőségekről: Azure Files [megfontolandó szempontok.](storage-files-networking-overview.md)

#### <a name="data-protection"></a>Adatvédelem
Az adatvédelemről szóló szakaszban konfigurálhatja a tárfiókban található Azure-fájlmegosztások törlési szabályzatát. A blobok, tárolók, tárolók helyreállítható törlésével, a tárolók időponthoz időben való visszaállításával, a verziószámozással és a változáscsatornával kapcsolatos egyéb beállítások csak az Azure Blob Storage-ban érvényesek.

#### <a name="advanced"></a>Felsőfokú
A speciális szakasz az Azure-fájlmegosztások számos fontos beállítását tartalmazza:

- **Biztonságos átvitelre van** szükség: Ez a mező azt jelzi, hogy a tárfiók titkosítást igényel-e az átvitel során a tárfiókkal való kommunikációhoz. Ha SMB 2.1-támogatásra van szüksége, ezt le kell tiltania.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Képernyőkép a tárfiók speciális beállításaiban engedélyezett biztonságos átvitelről.":::

- **Nagyméretű fájlmegosztások:** Ez a mező engedélyezi a legfeljebb 100 TiB méretű fájlmegosztások tárfiókját. A funkció engedélyezésével a tárfiók csak helyileg redundáns és zónaredundáns tárolási lehetőségekre lesz korlátozva. Miután engedélyezte a GPv2-tárfiókot a nagyméretű fájlmegosztások számára, nem tilthatja le a nagyméretű fájlmegosztási képességet. A FileStorage tárfiókok (a prémium fájlmegosztások tárfiókja) nem használhatja ezt a lehetőséget, mivel az összes prémium fájlmegosztás akár 100 TiB-ra is skálázhat. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Képernyőkép a nagyméretű fájlmegosztás beállításról a tárfiók speciális panelen.":::

A speciális lapon elérhető egyéb beállítások (hierarchikus névtér az Azure Data Lake Storage Gen2-hez, alapértelmezett blobszint, NFSv3 blobtárolókhoz stb.) nem vonatkoznak a Azure Files.

> [!Important]  
> A blob-hozzáférési szint kiválasztása nincs hatással a fájlmegosztás rétegére.

#### <a name="tags"></a>Címkék
A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését azáltal, hogy ugyanazt a címkét több erőforrásra és erőforráscsoportra is alkalmazza. Ezek nem kötelezőek, és a tárfiók létrehozása után alkalmazhatók.

#### <a name="review--create"></a>Ellenőrzés és létrehozás
A tárfiók létrehozásához utolsó lépésként  válassza a Létrehozás gombot az **Áttekintés + létrehozás lapon.** Ez a gomb nem lesz elérhető, ha a tárfiók összes kötelező mezője nincs kitöltve.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Tárfiók PowerShell használatával való létrehozásához a `New-AzStorageAccount` parancsmagot fogjuk használni. Ez a parancsmag számos beállítási lehetőséget kínál; csak a szükséges beállítások jelennek meg. A speciális beállításokkal kapcsolatos további információkért tekintse meg a [ `New-AzStorageAccount` parancsmagok dokumentációját.](/powershell/module/az.storage/new-azstorageaccount)

A tárfiók és az azt követő fájlmegosztás létrehozásának egyszerűsítése érdekében számos paramétert tárolunk változókban. A változó tartalmát bármilyen értékre lecserélheti, azonban vegye figyelembe, hogy a tárfiók nevének globálisan egyedinek kell lennie.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

A standard Azure-fájlmegosztások tárolására alkalmas tárfiók létrehozásához a következő parancsot fogjuk használni. A paraméter a kívánt redundanciatípushoz kapcsolódik. Ha georedundáns vagy geozónaredundáns tárfiókot szeretne, a paramétert is el kell `-SkuName` `-EnableLargeFileShare` távolítania.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

A prémium szintű Azure-fájlmegosztások tárolására alkalmas tárfiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a paraméter úgy módosult, hogy tartalmazza a helyileg redundáns ( ) kívánt `-SkuName` `Premium` redundanciaszintet `LRS` is. A paraméter helyett a prémium fájlmegosztásokat kell létrehozni a FileStorage tárfiókban `-Kind` `FileStorage` `StorageV2` GPv2-tárfiók helyett.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Tárfiók Azure CLI használatával való létrehozásához az az storage account create parancsot fogjuk használni. Ez a parancs számos beállítási lehetőséget kínál; csak a szükséges beállítások jelennek meg. A speciális beállításokkal kapcsolatos további információkért tekintse meg a [ `az storage account create` parancsdokumentációt.](/cli/azure/storage/account)

A tárfiók és az azt követő fájlmegosztás létrehozásának egyszerűsítése érdekében számos paramétert tárolunk változókban. A változó tartalmát bármilyen értékre lecserélheti, azonban vegye figyelembe, hogy a tárfiók nevének globálisan egyedinek kell lennie.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

A standard Azure-fájlmegosztások tárolására alkalmas tárfiók létrehozásához a következő parancsot fogjuk használni. A paraméter a kívánt redundanciatípushoz kapcsolódik. Ha georedundáns vagy geozónaredundáns tárfiókot szeretne, a paramétert is el kell `--sku` `--enable-large-file-share` távolítania.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

A prémium szintű Azure-fájlmegosztások tárolására alkalmas tárfiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a paraméter úgy módosult, hogy tartalmazza a helyileg redundáns ( ) kívánt `--sku` `Premium` redundanciaszintet `LRS` is. A paraméter helyett a prémium fájlmegosztásokat kell létrehozni a FileStorage tárfiókban `--kind` `FileStorage` `StorageV2` GPv2-tárfiók helyett.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
A tárfiók létrehozása után már csak létre kell hoznia a fájlmegosztást. Ez a folyamat többnyire ugyanaz, függetlenül attól, hogy prémium vagy standard fájlmegosztást használ. Vegye figyelembe a következő különbségeket.

A standard fájlmegosztások a standard szintek egyikében helyezhetők üzembe: tranzakcióoptimalált (alapértelmezett), forró vagy hűtési szinten. Ez egy fájlmegosztási szintenkénti szint, amelyet a tárfiók **blob-hozzáférési** rétege nem érint (ez a tulajdonság csak az Azure Blob Storage-hoz kapcsolódik – egyáltalán nem Azure Files kapcsolódik). Az üzembe helyezés után bármikor módosíthatja a megosztás rétegét. A prémium szintű fájlmegosztások nem konvertálhatók közvetlenül standard szintre.

> [!Important]  
> A fájlmegosztásokat áthelyezheti a GPv2-tárfióktípusok különböző rétegei között (tranzakcióoptimalált, gyors és hideg). A rétegek közötti megosztások között tranzakciók lépnek fel: a magasabb rétegű szintről egy hűtési szintre való áthelyezés esetén a megosztásban található egyes fájlok írási tranzakcióinak díjára kerül majd a rétegben, míg a hűtési szintről a hűtési szintre való áthelyezés esetén a hűtési szint olvasási tranzakciós díjának felszámít a megosztás minden fájlja esetében.

A **kvóta** tulajdonság némileg eltérőt jelent a prémium és a standard fájlmegosztások között:

- A standard fájlmegosztások esetében ez az Azure-fájlmegosztás felső határa, amelyen túl a végfelhasználók nem tudnak átmenni. Ha nincs kvóta megadva, a standard fájlmegosztás legfeljebb 100 TiB-ra (vagy 5 TiB-ra is vonatkozhat, ha a nagyméretű fájlmegosztások tulajdonság nincs beállítva a tárfiókhoz).

- Prémium fájlmegosztások esetében a kvóta a **kiépített méretet jelenti.** A kiépített méret az az összeg, amely után a tényleges használattól függetlenül ki lesz számlázva. A prémium szintű fájlmegosztások tervezésével kapcsolatos további információkért lásd: [Prémium fájlmegosztások kiépítése.](understanding-billing.md#provisioned-model)

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha most hozta létre a tárfiókot, az üzembe helyezési képernyőről az Erőforráshoz ugrás lehetőség kiválasztásával **navigálhat hozzá.** A tárfiókban válassza  ki a fájlmegosztásokat a tárfiók tartalomjegyzékében.

A fájlmegosztások listában látnia kell az ebben a tárfiókban korábban létrehozott fájlmegosztásokat; egy üres táblát, ha még nem jött létre fájlmegosztás. Új **fájlmegosztás létrehozásához** válassza a + Fájlmegosztás lehetőséget.

Az új fájlmegosztás panelnek meg kell jelenni a képernyőn. Töltse ki az új fájlmegosztás panel mezőit egy fájlmegosztás létrehozásához:

- **Név:** a létrehozni kívánt fájlmegosztás neve.
- **Kvóta:** a fájlmegosztás kvótája standard fájlmegosztások esetében; a prémium fájlmegosztások fájlmegosztásának kiépített mérete.
- **Szintek:** a fájlmegosztás kiválasztott rétege. Ez a mező csak általános célú **(GPv2) tárfiókban érhető el.** Választhat a tranzakcióoptimalált, a gyors vagy a gyors hűtési lehetőséget is. A megosztás rétege bármikor módosítható. Javasoljuk, hogy a migrálás során a lehető legkombosabb szintet válassza a tranzakciós költségek minimalizálása érdekében, majd váltson át egy alacsonyabb szintre, ha a migrálás befejeződött.

Az **új megosztás** létrehozásának befejezéséhez válassza a Létrehozás lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az Azure-fájlmegosztást a [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) parancsmaggal hozhatja létre. A következő PowerShell-parancsok feltételezik, hogy beállította a és a változókat a tárfiók létrehozását a Azure PowerShell szakaszban `$resourceGroupName` `$storageAccountName` leírtak szerint. 

Az alábbi példa egy explicit szintű fájlmegosztás létrehozását mutatja be a `-AccessTier` paraméterrel. Ehhez a példában látható előzetes verziójú Az.Storage modult kell használni. Ha nincs megadva szint, vagy azért, mert az általánosan rendelkezésre áll az Az.Storage modult használja, vagy mert nem tartalmazta ezt a parancsot, a standard fájlmegosztások alapértelmezett csomagja tranzakcióoptimal van optimalizálva.

> [!Important]  
> Prémium fájlmegosztások esetében a paraméter a fájlmegosztás `-QuotaGiB` kiépített méretére utal. A fájlmegosztás kiépített mérete a használattól függetlenül a kiszámlázható mennyiség. A standard fájlmegosztások számlázása a használat alapján, és nem a kiépített méret alapján van kiszámlázva.

```powershell
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure-fájlmegosztást az paranccsal hozhat [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) létre. A következő Azure CLI-parancsok feltételezik, hogy beállította a és a változókat a tárfiók Azure CLI-val való létrehozásával szakaszban fentebb `$resourceGroupName` `$storageAccountName` leírtak szerint.

> [!Important]  
> Prémium fájlmegosztások esetében a paraméter a fájlmegosztás `--quota` kiépített méretét jelenti. A fájlmegosztás kiépített mérete a használattól függetlenül a kiszámlázható mennyiség. A standard fájlmegosztások számlázása a használat alapján, és nem a kiépített méret alapján van kiszámlázva.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és fájlok elnevezésével kapcsolatos részletes információkért lásd: [Megosztások, könyvtárak,](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)fájlok és metaadatok elnevezése és hivatkozása.

### <a name="change-the-tier-of-an-azure-file-share"></a>Azure-fájlmegosztás rétegének módosítása
Az általános célú **v2-tárfiókban (GPv2)** üzembe helyezett fájlmegosztások a tranzakcióoptimalált, a forró vagy a hűtési szinten is lehet. Az Azure-fájlmegosztás tárolási rétege bármikor megváltoztatható a fent leírt tranzakciós költségek alapján.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A tárfiók főoldalán  válassza a Fájlmegosztások lehetőséget, majd a  Fájlmegosztások címkét **(a** tárfiók tartalomjegyzékében is navigálhat a Fájlmegosztások lapra).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Képernyőkép a tárfiók panelről, kiválasztott fájlmegosztásokkal.":::

A fájlmegosztások táblázatos listájában válassza ki azt a fájlmegosztást, amelynek a rétegét módosítani szeretné. A fájlmegosztás áttekintési oldalán válassza a **menü Réteg** módosítása parancsát.

![A fájlmegosztás áttekintési oldalának képernyőképe a kiemelt Szint módosítása gombbal](media/storage-how-to-create-file-share/change-tier-0.png)

Az eredményül kapott párbeszédpanelen válassza ki a kívánt szintet: tranzakcióoptimalált, forró vagy cool.

![Képernyőkép a rétegváltási párbeszédpanelről](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancsmag feltételezi, hogy beállította a , változókat a dokumentum korábbi `$resourceGroupName` `$storageAccountName` `$shareName` szakaszaiban leírtak szerint.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A következő Azure CLI-parancs feltételezi, hogy beállította a , és változókat a dokumentum korábbi `$resourceGroupName` `$storageAccountName` `$shareName` szakaszaiban leírtak szerint.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Következő lépések
- [Tervezze meg a Azure Files](storage-files-planning.md) vagy a Terv a központi [telepítésének Azure File Sync.](../file-sync/file-sync-planning.md) 
- [Hálózat – áttekintés.](storage-files-networking-overview.md)
- Fájlmegosztás csatlakoztatása [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)és [Linux rendszeren.](storage-how-to-use-files-linux.md)