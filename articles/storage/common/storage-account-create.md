---
title: Tárfiók létrehozása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre tárfiókot blobok, fájlok, üzenetsorok és táblák tárolásához. Az Azure Storage-fiók egyedi névteret biztosít a Microsoft Azure az adatok olvasásakor és írásában.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cb5caeb7f75834a317b222392c6e827185cfac00
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714350"
---
# <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, üzenetsorokat és táblákat. A tárfiók egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP- vagy HTTPS-kapcsolatokon keresztül. Az Azure Storage-fiókokkal kapcsolatos további információkért lásd: [Tárfiókok áttekintése.](storage-account-overview.md)

Ebből az útmutatóból megtudhatja, hogyan hozhat létre tárfiókot a [Azure Portal](https://portal.azure.com/), [Azure PowerShell,](/powershell/azure/) [Az Azure CLI](/cli/azure)vagy egy Azure Resource Manager [használatával.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nincsenek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha Azure Storage-fiókot hoz létre a PowerShell használatával, győződjön meg arról, hogy telepítette az [Az PowerShell-modul](https://www.powershellgallery.com/packages/Az)0.7-es vagy újabb verzióját. További információ: [Introducing the Azure PowerShell Az modul](/powershell/azure/new-azureps-module-az).

Az aktuális verzió megkereshez futtassa a következő parancsot:

```powershell
Get-InstalledModule -Name "Az"
```

Az alkalmazás telepítéséhez vagy Azure PowerShell lásd: Install Azure PowerShell module (A [Azure PowerShell telepítése).](/powershell/azure/install-az-ps)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure-ba való bejelentkezés és az Azure CLI-parancsok futtatása kétféleképpen lehetséges:

- A parancssori felület parancsai a Azure Portal a Azure Cloud Shell.
- Telepítheti a CLI-t, és helyileg futtathatja a CLI-parancsokat.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepítve van, és a fiókjával való használatra van konfigurálva. Kattintson **a Cloud Shell** jobb felső részén található menü Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb elindít egy interaktív rendszerhéjat, amely az ebben az útmutatóban ismertetett lépések futtatására használható:

[![Képernyőkép a Cloud Shell ablakról a portálon](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. A cikkben olvasható példákhoz az Azure CLI 2.0.4-es vagy újabb verziójára van szükség. A `az --version` telepített verzió megkereshez futtassa a (futtatás) futtatását. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

---

Ezután jelentkezzen be az Azure-ba.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Jelentkezzen be az Azure-előfizetésbe az paranccsal, és kövesse a képernyőn megjelenő `Connect-AzAccount` utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Azure Cloud Shell elindításához jelentkezzen be a [Azure Portal.](https://portal.azure.com)

A parancssori felület helyi telepítésére való bejelentkezéshez futtassa [az az login](/cli/azure/reference-index#az-login) parancsot:

```azurecli-interactive
az login
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A tárfiók egy Azure Resource Manager erőforrás. Resource Manager az Azure üzembe helyezési és felügyeleti szolgáltatása. További információt [Az Azure Resource Manager áttekintésében](../../azure-resource-manager/management/overview.md) találhat.

Minden Resource Manager erőforrásnak, beleértve az Azure Storage-fiókot is, egy Azure-erőforráscsoporthoz kell tartozni. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez az how-to-how to create a new resource group (Új erőforráscsoport létrehozása)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Azure Storage-fiók létrehozásához a Azure Portal kövesse az alábbi lépéseket:

1. A bal oldali portál menüjében válassza a **Tárfiókok** lehetőséget a tárfiókok listájának megjelenítéséhez.
1. A **Tárfiókok lapon** válassza az Új **lehetőséget.**

Az új tárfiók beállításai lapra vannak rendezve a Tárfiók **létrehozása lapon.** A következő szakaszok ismertetik az egyes lapokat és azok beállításait.

### <a name="basics-tab"></a>Alapvető beállítások lap

Az Alapvető **beállítások lapon** adja meg a tárfiók alapvető adatait. Az Alapvető  beállítások lap befejezése után dönthet úgy, hogy további beállításokat ad meg az új tárfiókhoz a többi lapon, vagy az Áttekintés **+** létrehozás lehetőség választásával elfogadhatja az alapértelmezett beállításokat, majd érvényesítheti és létrehozhatja a fiókot.

Az alábbi táblázat az Alapvető beállítások lapon található **mezőket** ismerteti.

| Section | Mező | Kötelező vagy választható | Description |
|--|--|--|--|
| Projekt részletei | Előfizetés | Kötelező | Válassza ki az új tárfiók előfizetését. |
| Projekt részletei | Erőforráscsoport | Kötelező | Hozzon létre egy új erőforráscsoportot ehhez a tárfiókhoz, vagy válasszon ki egy meglévőt. További információ: [Erőforráscsoportok.](../../azure-resource-manager/management/overview.md#resource-groups) |
| Példány adatai | Tárfiók neve | Kötelező | Válasszon egy egyedi nevet a tárfióknak. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. |
| Példány adatai | Region | Kötelező | Válassza ki a tárfiókhoz megfelelő régiót. További információ: Régiók és Availability Zones [Azure-ban.](../../availability-zones/az-overview.md)<br /><br />Nem minden régió támogatott minden típusú tárfiókhoz vagy redundanciakonfigurációhoz. További információ: [Azure Storage-redundancia](storage-redundancy.md).<br /><br />A régióválasztás hatással lehet a számlázásra. További információ: [Storage-fiók számlázása.](storage-account-overview.md#storage-account-billing) |
| Példány adatai | Teljesítmény | Kötelező | Válassza **a Standard** teljesítmény általános célú v2-tárfiókok esetében (alapértelmezett) lehetőséget. Ezt a fióktípust a Microsoft a legtöbb esetben ajánlott. További információ: [Tárfiókok típusai.](storage-account-overview.md#types-of-storage-accounts)<br /><br />Alacsony **késést** igénylő forgatókönyvek esetén válassza a Prémium lehetőséget. A Prémium **lehetőséget választva** válassza ki a létrehozni kívánt prémium szintű tárfiók típusát. A prémium szintű tárfiókok következő típusai érhetők el: <ul><li>[Blokkblobok](../blobs/storage-blob-performance-tiers.md)</li><li>[Fájlmegosztások](../files/storage-files-planning.md#management-concepts)</li><li>[Lapblobok](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Példány adatai | Redundancia | Kötelező | Válassza ki a kívánt redundanciakonfigurációt. Nem minden redundancia-beállítás érhető el minden tárfióktípushoz minden régióban. További információ a redundanciakonfigurációkról: [Azure Storage redundancia.](storage-redundancy.md)<br /><br />Ha georedundáns konfigurációt (GRS vagy GZRS) választ, a rendszer egy másik régióban található adatközpontba replikálja az adatokat. A másodlagos régióban található adatokhoz való olvasási hozzáféréshez válassza az Olvasási hozzáférés elérhetővé tenni az adatokhoz régiónkénti **elérhetetlenség esetén lehetőséget.** |

Az alábbi képen egy új tárfiók standard konfigurációja látható.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Új tárfiók szabványos konfigurációját bemutató képernyőkép – Alapvető beállítások lap":::

### <a name="advanced-tab"></a>Speciális lap

A Speciális **lapon** további beállításokat konfigurálhat, és módosíthatja az új tárfiók alapértelmezett beállításait. Ezen beállítások némelyike a tárfiók létrehozása után is konfigurálható, míg másokat a létrehozáskor kell konfigurálni.

Az alábbi táblázat a Speciális lapon található **mezőket** ismerteti.

| Section | Mező | Kötelező vagy választható | Description |
|--|--|--|--|
| Biztonság | Biztonságos átvitel engedélyezése | Választható | Engedélyezze a biztonságos átvitelt, hogy az ehhez a tárfiókhoz beérkező kérések csak HTTPS-kapcsolaton keresztül (alapértelmezés szerint) készülnek. Az optimális biztonság érdekében ajánlott. További információ: [Biztonságos átvitel megkövetelése a biztonságos kapcsolatok biztosításához.](storage-require-secure-transfer.md) |
| Biztonság | Infrastruktúra titkosításának engedélyezése | Választható | Alapértelmezés szerint az infrastruktúra titkosítása nincs engedélyezve. Engedélyezze az infrastruktúra titkosítását az adatok szolgáltatás- és infrastruktúraszinten való titkosításához. További információ: Tárfiók létrehozása olyan infrastruktúra-titkosítással, amelynél engedélyezve van [az adatok kettős titkosítása.](infrastructure-encryption-enable.md) |
| Biztonság | Nyilvános blobhozzáférés engedélyezése | Választható | Ha engedélyezve van, ez a beállítás lehetővé teszi a megfelelő engedélyekkel rendelkező felhasználók számára, hogy névtelen nyilvános hozzáférést engedélyezzenek a tárfiókban található tárolókhoz (alapértelmezés). A beállítás letiltása megakadályozza a tárfiók névtelen nyilvános hozzáférését. További információ: [Tárolók és blobok névtelen nyilvános](../blobs/anonymous-read-access-prevent.md)olvasási hozzáférésének megakadályozása.<br> <br> A blobok nyilvános hozzáférésének engedélyezése csak akkor teszi elérhetővé a blobadatokat, ha a felhasználó a további lépéssel explicit módon konfigurálja a tároló nyilvános hozzáférési beállítását. |
| Biztonság | Tárfiókkulcs-hozzáférés engedélyezése (előzetes verzió) | Választható | Ha ez a beállítás engedélyezve van, az ügyfelek engedélyezhetik a tárfiókra vonatkozó kéréseket a fiók hozzáférési kulcsával vagy Azure Active Directory (Azure AD) fiókkal (alapértelmezés). A beállítás letiltása megakadályozza a fiók hozzáférési kulcsokkal való hitelesítését. További információ: Az Azure Storage-fiók megosztott kulcsos [hitelesítésének megakadályozása (előzetes verzió)](shared-key-authorization-prevent.md). |
| Biztonság | TLS minimális verziója | Kötelező | Válassza ki a tárfiókhoz érkező Transport Layer Security (TLS) minimális verzióját. Az alapértelmezett érték a TLS 1.2-es verziója. Ha az alapértelmezett értékre van állítva, a rendszer elutasítja a TLS 1.0 vagy TLS 1.1 használatával érkező kérelmeket. További információkért lásd: A tárfiókra Transport Layer Security (TLS) minimálisan szükséges [verziójának kényszerítését.](transport-layer-security-configure-minimum-version.md) |
| Data Lake Storage Gen2 | Hierarchikus névtér engedélyezése | Választható | Ha ezt a tárfiókot számítási feladatokhoz Azure Data Lake Storage Gen2, konfigurálnia kell egy hierarchikus névteret. További információ: [Bevezetés](../blobs/data-lake-storage-introduction.md)a Azure Data Lake Storage Gen2. |
| Blob Storage | Hálózati fájlmegosztás (NFS) v3 (előzetes verzió) engedélyezése | Választható | Az NFS v3 linuxos fájlrendszer-kompatibilitást biztosít az objektumtárolás méretének megfelelően, lehetővé teszi a Linux-ügyfelek számára, hogy egy Azure-beli virtuális gépről (VM) vagy egy helyszíni számítógépről csatlakoztassanak egy tárolót a Blob Storage-ban. További információ: Hálózati fájlrendszer [(NFS) 3.0 protokoll támogatása az Azure Blob Storage-ban (előzetes verzió).](../blobs/network-file-system-protocol-support.md) |
| Blob Storage | Hozzáférési szint | Kötelező | A blob-hozzáférési szintek lehetővé teszik, hogy a használat alapján a legköltségköltségesebb módon tárolja a blobadatokat. A gyakran használt adatokhoz válassza a gyakori rétegű (alapértelmezett) lehetőséget. Válassza ki a ritkán használt adatokhoz használt ritka adatokat. További információ: Hozzáférési szintek a Azure Blob Storage [– elérésű, elérésű és archív.](../blobs/storage-blob-storage-tiers.md) |
| Azure Files | Nagy fájlok megosztásának engedélyezése | Választható | Csak a prémium szintű tárfiókok számára érhető el a fájlmegosztások esetében. További információ: Enable standard file shares to span up up 100 TiB ( Standard fájlmegosztások engedélyezése [legfeljebb 100 TiB-ra).](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) |
| Táblák és üzenetsorok | Ügyfél által kezelt kulcsok támogatásának engedélyezése | Választható | Ha engedélyezni szeretné az ügyfél által kezelt kulcsok támogatását a táblákhoz és üzenetsorokhoz, ezt a beállítást a tárfiók létrehozásakor kell kiválasztania. További információ: Táblák és üzenetsorok ügyfél által kezelt kulcsait támogató [fiók létrehozása.](account-encryption-key-create.md) |

### <a name="networking-tab"></a>Hálózat lap

A Hálózat **lapon** konfigurálhatja az új tárfiók hálózati kapcsolati és útválasztási beállítási beállításait. Ezek a beállítások a tárfiók létrehozása után is konfigurálhatóak.

Az alábbi táblázat a Hálózat lap **mezőit** ismerteti.

| Section | Mező | Kötelező vagy választható | Description |
|--|--|--|--|
| Hálózati kapcsolat | Kapcsolati mód | Kötelező | Alapértelmezés szerint a bejövő hálózati forgalom a tárfiók nyilvános végpontjára lesz irányítva. Megadhatja, hogy a forgalmat a nyilvános végpontra kell irányítva lennie egy Azure-beli virtuális hálózaton keresztül. Privát végpontokat is konfigurálhat a tárfiókhoz. További információ: [Privát végpontok használata az Azure Storage-hoz.](storage-private-endpoints.md) |
| Hálózati útválasztás | Útválasztási beállítás | Kötelező | A hálózati útválasztási beállítás határozza meg, hogyan lesz irányítva a hálózati forgalom a tárfiók nyilvános végpontjára az ügyfelektől az interneten keresztül. Alapértelmezés szerint egy új tárfiók Microsoft hálózati útválasztást használ. A hálózati forgalmat a tárfiókhoz legközelebbi pop-kapcsolaton keresztül is irányíthatja, ami csökkentheti a hálózati költségeket. További információ: Hálózati [útválasztási beállítások az Azure Storage-hoz.](network-routing-preference.md) |

### <a name="data-protection-tab"></a>Adatvédelem lap

Az Adatvédelem **lapon** konfigurálhatja az új tárfiókban található blobadatok adatvédelmi beállításait.  Ezek a beállítások a tárfiók létrehozása után is konfigurálhatóak. Az Azure Storage adatvédelmi lehetőségeinek áttekintését lásd: [Adatvédelem áttekintése.](../blobs/data-protection-overview.md)

Az alábbi táblázat az Adatvédelem lapon található **mezőket** ismerteti.

| Section | Mező | Kötelező vagy választható | Description |
|--|--|--|--|
| Helyreállítási | Időponthoz időben való visszaállítás engedélyezése tárolókhoz | Választható | Az időponthoz időben való visszaállítás védelmet nyújt a véletlen törléssel vagy sérüléssel szemben azáltal, hogy lehetővé teszi a blokkblobadatok korábbi állapotába való visszaállítását. További információ: Időponthoz időben való visszaállítás [blokkblobok számára.](../blobs/point-in-time-restore-overview.md)<br /><br />Az időponthoz időben való visszaállítás engedélyezése lehetővé teszi a blobok verziószámozását, a blobok helyreállítható törlését és a blobok változáscsatornáját is. Ezek az előfeltétel-funkciók hatással lehetnek a költségekre. További információ: [Az](../blobs/point-in-time-restore-overview.md#pricing-and-billing) időponthoz való visszaállítás díjszabása és számlázása. |
| Helyreállítási | Blobok helyreállítható törlésének engedélyezése | Választható | A blobok végleges törlése megvédi az egyes blobokat, pillanatképeket vagy verziókat a véletlen törléstől vagy felülírástól azáltal, hogy a törölt adatokat egy adott megőrzési időszakra megőrzi a rendszerben. A megőrzési időszak alatt visszaállíthat egy helyreállíthatóan törölt objektumot annak állapotába a törléskor. További információkért lásd: [Blobok soft delete (Blobok soft delete ) parancsa.](../blobs/soft-delete-blob-overview.md)<br /><br />A Microsoft azt javasolja, hogy a blobok esetében engedélyezni kell a blobok esetében a blobok törlését, és be kell tartani a minimális megőrzési megőrzési időszakot hét napban. |
| Helyreállítási | Tárolókhoz való soft delete (előzetes verzió) engedélyezése | Választható | A tárolók és a tárolók végleges törlése megvédi a tárolót és annak tartalmát a véletlen törléstől azáltal, hogy a törölt adatokat egy adott megőrzési időszakra megőrzi a rendszerben. A megőrzési időszak alatt helyreállíthatóan törölt tárolók visszaállíthatóak annak állapotába a törléskor. További információ: [Tárolók soft delete for containers (preview) (Tárolók soft delete for containers (előzetes verzió) ).](../blobs/soft-delete-container-overview.md)<br /><br />A Microsoft azt javasolja, hogy a tárfiókok esetében a tárolók esetében engedélyezni kell a tárolók esetében a törlést, és be kell tartani a minimális megőrzési megőrzési időszakot hét nap alatt. |
| Helyreállítási | Fájlmegosztások soft delete funkciónak engedélyezése | Választható | A fájlmegosztások soft delete parancsa megvédi a fájlmegosztásokat és azok tartalmát a véletlen törlésektől azáltal, hogy a törölt adatokat egy adott megőrzési időszakra megőrzi a rendszerben. A megőrzési időszak alatt helyreállíthatóan törölt fájlmegosztást visszaállíthat annak állapotába a törléskor. További információ: [Az Azure-fájlmegosztások véletlen törlésének megakadályozása.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />A Microsoft javasolja a fájlmegosztások Azure Files törlésének engedélyezését és a minimális megőrzési időtartam hét napos beállítását. |
| Nyomon követés | Blobok verziószámozásának engedélyezése | Választható | A blobok verziószámozása automatikusan menti a blob állapotát egy korábbi verzióban, amikor felülírja a blobot. További információ: [Blobok verziószámozása.](../blobs/versioning-overview.md)<br /><br />A Microsoft javasolja a blobok verziószámozásának engedélyezését a tárfiók optimális adatvédelme érdekében. |
| Nyomon követés | Blob változáscsatornája engedélyezése | Választható | A blob-változáscsatorna a tárfiók összes blobjának minden változását, valamint azok metaadatainak tranzakciónaplóit is biztosítja. További információ: [Változáscsatorna](../blobs/storage-blob-change-feed.md)támogatása a Azure Blob Storage. |

### <a name="tags-tab"></a>Címkék lap

A **Címkék lapon** megadhatja a Resource Manager címkéket az Azure-erőforrások rendszerezésének segítése érdekében. További információ: [Erőforrások, erőforráscsoportok](../../azure-resource-manager/management/tag-resources.md)és előfizetések címkézése logikai szervezet számára.

### <a name="review--create-tab"></a>Áttekintés és létrehozás lap

Amikor a Felülvizsgálat **+** létrehozás lapra navigál, az Azure érvényesítést futtat a kiválasztott tárfiók-beállításokon. Ha az érvényesítés megfelel, létrehozhatja a tárfiókot.

Ha az érvényesítés sikertelen, a portál jelzi, hogy mely beállításokat kell módosítani.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Általános célú v2-tárfiók PowerShell-lel való létrehozásához először hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs hívásával:

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Ha nem biztos abban, hogy melyik régiót kell megadnia `-Location` a paraméterhez, a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) paranccsal lekérheti az előfizetés támogatott régióinak listáját:

```azurepowershell-interactive
Get-AzLocation | select Location
```

Ezután hozzon létre egy általános célú v2-tárfiókot írási hozzáférésű georedundáns tárolással (RA-GRS) a [New-AzStorageAccount paranccsal.](/powershell/module/az.storage/new-azstorageaccount) Ne feledje, hogy a tárfiók nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értékét a saját egyedi értékére:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Ha engedélyezni szeretné egy hierarchikus névtér használatát a [tárfiók](https://azure.microsoft.com/services/storage/data-lake-storage/)számára a Azure Data Lake Storage paramétert a `-EnableHierarchicalNamespace $True` **New-AzStorageAccount** parancs hívásában.

Az alábbi táblázat bemutatja, hogy mely értékeket kell használni a és paraméterekhez egy adott típusú tárfiók létrehozásához `-SkuName` `-Kind` a kívánt redundanciakonfigurációval.

| Tárfiók típusa | Támogatott redundanciakonfigurációk | A -Kind paraméter értéke | A -SkuName paraméter lehetséges értékei | Támogatja a hierarchikus névteret |
|--|--|--|--|--|
| Standard általános célú v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Prémium szintű blokkblobok | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Yes |
| Prémium fájlmegosztások | LRS / ZRS | FileStorage (Fájltartó) | Premium_LRS / Premium_ZRS | No |
| Prémium lapblobok | LRS | StorageV2 | Premium_LRS | No |
| Örökölt általános célú általános célú v1 | LRS / GRS / RA-GRS | Tárolás | Standard_LRS / Standard_GRS / Standard_RAGRS | No |
| Örökölt Blob Storage | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | No |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Általános célú v2-tárfiók Azure CLI-vel való létrehozásához először hozzon létre egy új erőforráscsoportot [az az group create parancs hívásával.](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `--location` paraméterhez, az előfizetéséhez tartozó támogatott régiók listáját az [az account list-locations](/cli/azure/account#az_account_list) paranccsal kérheti le.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Ezután hozzon létre egy általános célú v2-tárfiókot írási hozzáférésű georedundáns tárolással az [az storage account create paranccsal.](/cli/azure/storage/account#az_storage_account_create) Ne feledje, hogy a tárfiók nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értékét a saját egyedi értékére:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Ha engedélyezni szeretné egy hierarchikus névtér használatát a tárfiók számára a [Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)foglalja bele a paramétert az `--enable-hierarchical-namespace true` az storage account create **parancs** hívásában. Hierarchikus névtér létrehozásához az Azure CLI 2.0.79-es vagy újabb verziójára van szükség.

Az alábbi táblázat bemutatja, hogy mely értékeket kell használni a és paraméterekhez egy adott típusú tárfiók létrehozásához `-sku` `-kind` a kívánt redundanciakonfigurációval.

| Tárfiók típusa | Támogatott redundanciakonfigurációk | Az -kind paraméter értéke | A -sku paraméter lehetséges értékei | Támogatja a hierarchikus névteret |
|--|--|--|--|--|
| Standard általános célú v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Prémium szintű blokkblobok | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Yes |
| Prémium fájlmegosztások | LRS /ZRS | FileStorage (Fájltartó) | Premium_LRS / Premium_ZRS | No |
| Prémium lapblobok | LRS | StorageV2 | Premium_LRS | No |
| Örökölt általános célú általános célú v1 | LRS / GRS / RA-GRS | Tárolás | Standard_LRS / Standard_GRS / Standard_RAGRS | No |
| Örökölt Blob Storage | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | No |

# <a name="template"></a>[Sablon](#tab/template)

A tárfiók létrehozásához Azure PowerShell azure cli használatával üzembe helyezhet egy Resource Manager-sablont. Az ebben az útmutatóban használt sablon a gyorsindítási [sablonokból Azure Resource Manager tartalmaz.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) A szkriptek futtatásához kattintson a **Try it (Próbálja ki) gombra** a Azure Cloud Shell. A szkript beillesztéshez kattintson a jobb gombbal a rendszerhéjra, majd válassza a Beillesztés **lehetőséget.**

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Ez a sablon csak példaként szolgál. Számos tárfiók-beállítás van, amely nem ennek a sablonnak a részeként van konfigurálva. Ha például a következőt szeretné [használni Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), akkor a sablont úgy módosíthatja, hogy az objektum tulajdonságát a következőre `isHnsEnabledad` `StorageAccountPropertiesCreateParameters` módosítja: `true` .

A sablon módosításáról vagy újak létrehozásáról a következő cikkből tudhat meg többet:

- [Azure Resource Manager dokumentáció.](../../azure-resource-manager/index.yml)
- [Storage-fiók sablonreferenciája.](/azure/templates/microsoft.storage/allversions)
- [További tárfióksablon-minták.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Tárfiók törlése

A tárfiók törlésével a teljes fiókot törli, a fiók összes adatát is beleértve, és nem vonható vissza.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Keresse meg a tárfiókot a [Azure Portal.](https://portal.azure.com)
1. Kattintson a **Törlés** gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A tárfiók törléséhez használja a [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiók törléséhez használja az [az storage account delete parancsot:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

A tárfiók törléséhez használja a következőt: Azure PowerShell Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Másik megoldásként törölheti az erőforráscsoportot, amely törli a tárfiókot és az erőforráscsoportban minden más erőforrást. További információ az erőforráscsoportok törléséről: [Erőforráscsoport és erőforrások törlése.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
>
> Ha egy Azure virtuális géppel társított tárfiókot próbál törölni, egy hibaüzenetet kaphat, amely szerint a tárfiók még használatban van. A hiba elhárításával kapcsolatos segítségért lásd: [Tárfiókok törlésekor előforduló hibák elhárítása.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)

## <a name="next-steps"></a>Következő lépések

- [Tárfiókok áttekintése](storage-account-overview.md)
- [Frissítés általános célú v2-tárfiókra](storage-account-upgrade.md)
- [Tárfiók áthelyezése másik régióba](storage-account-move.md)
- [Törölt tárfiók helyreállítása](storage-account-recover.md)