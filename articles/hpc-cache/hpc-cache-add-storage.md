---
title: Tároló hozzáadása egy Azure HPC-gyorsítótárhoz
description: Tárolási célok meghatározása úgy, hogy az Azure HPC-gyorsítótára használhassa a helyszíni NFS-rendszert vagy az Azure Blob-tárolókat a hosszú távú fájlok tárolásához
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: afb896100ea60c21aaf37890d7b520bf38c6ce18
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772722"
---
# <a name="add-storage-targets"></a>Céltárak hozzáadása

A *tárolási célok* az Azure HPC-gyorsítótáron keresztül elért fájlok háttérbeli tárolói. Hozzáadhat NFS-tárolót (például helyszíni hardverrendszer), vagy tárolhat adattárolást az Azure blobban.

Egy gyorsítótárhoz legfeljebb 20 különböző tárolási célt adhat meg. A gyorsítótár egy összesített névtérben jeleníti meg az összes tárolási célt.

A névtér elérési útjai külön vannak konfigurálva a tárolási célok hozzáadása után. Általánosságban elmondható, hogy egy NFS-tárolási cél akár tíz névtér elérési úttal is rendelkezhet, vagy akár több nagy konfiguráció esetén is. A részletek az [NFS-névtér elérési útjai](add-namespace-paths.md#nfs-namespace-paths) olvashatók.

Ne feledje, hogy a tárolók exportálásának elérhetőnek kell lennie a gyorsítótár virtuális hálózatáról. A helyszíni hardveres tároláshoz előfordulhat, hogy olyan DNS-kiszolgálót kell beállítania, amely képes az NFS-tároló elérésére szolgáló gazdagépek feloldására. További információk: [DNS-hozzáférés](hpc-cache-prerequisites.md#dns-access).

Adja hozzá a tárolási célokat a gyorsítótár létrehozása után. Kövesse ezt a folyamatot:

1. [A gyorsítótár létrehozása](hpc-cache-create.md)
1. Tárolási cél definiálása (a cikkben található információk)
1. [Az ügyfél felé irányuló elérési utak létrehozása](add-namespace-paths.md) (az [összesített névtérhez](hpc-cache-namespace.md))

A tárolási cél hozzáadására szolgáló eljárás némileg eltér attól függően, hogy milyen típusú tárolót használ. A részleteket az alábbiakban találja.

Az alábbi képre kattintva megtekintheti a gyorsítótár létrehozásának és tárolási céljának a Azure Portal való hozzáadásának [bemutató videóját](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) .

[![videó miniatűrje: Azure HPC cache: Setup (kattintson ide a videó oldal megtekintéséhez)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Új Azure Blob Storage-cél hozzáadása

Egy új blob Storage-célnak szüksége van egy üres blob-tárolóra vagy egy olyan tárolóra, amely az Azure HPC cache Cloud File System formátumában található adatokkal van feltöltve. További információ a blob-tárolók [Azure Blob Storage-ba való áthelyezésének](hpc-cache-ingest.md)előzetes betöltéséről.

A Azure Portal **tároló hozzáadása** lapon lehetőség van egy új blob-tároló létrehozására is, közvetlenül a hozzáadása előtt.

> [!NOTE]
> Az NFS-hez csatlakoztatott blob Storage esetében használja a [ADLS-NFS tárolási cél](#) típust.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótár-példányt, és kattintson a bal oldali oldalsávon található **tárolási célok** elemre.

![képernyőfelvétel a beállításokról > a tárolási cél lapon két meglévő tárolási cél szerepel egy táblában, és a táblázat fölé emelt a + tároló hozzáadása gomb](media/add-storage-target-button.png)

A **tárolási célok** lap felsorolja az összes meglévő célt, és egy hivatkozást ad hozzá egy újat.

Kattintson a **tárolási cél hozzáadása** gombra.

![képernyőfelvétel a Storage-cél hozzáadása oldalról, amely az új Azure Blob Storage-tárolóra vonatkozó információkkal van feltöltve](media/hpc-cache-add-blob.png)

Azure Blob-tároló definiálásához adja meg ezt az információt.

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.
* **Cél típusa** – válassza a **blob** lehetőséget.
* **Storage-fiók** – válassza ki a használni kívánt fiókot.

  Engedélyeznie kell a gyorsítótár-példányt a Storage-fiók eléréséhez a [hozzáférési Szerepkörök hozzáadása](#add-the-access-control-roles-to-your-account)című témakörben leírtak szerint.

  A használható Storage-fiókkal kapcsolatos információkért olvassa el a [blob Storage-követelmények](hpc-cache-prerequisites.md#blob-storage-requirements)című témakört.

* **Storage-tároló** – jelölje ki a cél blob-tárolóját, vagy kattintson az **új létrehozása** gombra.

  ![képernyőkép a párbeszédpanelről az új tárolóhoz tartozó név és hozzáférési szint (Private) megadásához](media/add-blob-new-container.png)

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

> [!NOTE]
> Ha a Storage-fiók tűzfala úgy van beállítva, hogy csak a "kiválasztott hálózatokra" korlátozza a hozzáférést, használja a [blob Storage-fiók tűzfalának](hpc-cache-blob-firewall-fix.md)használata című dokumentumban ismertetett ideiglenes megkerülő megoldást.

### <a name="add-the-access-control-roles-to-your-account"></a>Hozzáférés-vezérlési Szerepkörök hozzáadása a fiókhoz

Az Azure HPC cache az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/index.yml) használatával engedélyezi a gyorsítótár-szolgáltatás számára, hogy hozzáférjen az Azure Blob Storage-célokhoz tartozó Storage-fiókhoz.

A Storage-fiók tulajdonosának explicit módon hozzá kell adnia a roles [Storage-fiók közreműködőjét](../role-based-access-control/built-in-roles.md#storage-account-contributor) és a [Storage blob adatközreműködőit](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a "HPC cache erőforrás-szolgáltató" felhasználóhoz.

Ezt előre elvégezheti, vagy egy hivatkozásra kattintva megadhatja a blob Storage-tárolót tartalmazó oldalon. Ne feledje, hogy akár öt percet is igénybe vehet, hogy a szerepkör-beállítások továbbítsák az Azure-környezetet, ezért néhány percet várnia kell, miután hozzáadta a szerepköröket a tárolási cél létrehozása előtt.

Az Azure-szerepkörök hozzáadásának lépései:

1. Nyissa meg a Storage-fiókhoz tartozó **hozzáférés-vezérlés (iam)** lapot. (A **tároló hozzáadása** lapon lévő hivatkozás automatikusan megnyitja ezt a lapot a kiválasztott fiókhoz.)

1. Kattintson a **+** lap tetején található elemre, majd válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. Válassza ki a "Storage-fiók közreműködője" szerepkört a listából.

1. A **hozzáférés kiosztása** mezőben hagyja meg a kiválasztott alapértelmezett értéket ("Azure ad-felhasználó, csoport vagy szolgáltatásnév").  

1. A **Select (kiválasztás** ) mezőben keressen rá a "HPC" kifejezésre.  Ennek a karakterláncnak meg kell egyeznie egy "HPC cache erőforrás-szolgáltató" nevű egyszerű szolgáltatással. Kattintson erre a résztvevőre a kiválasztásához.

   > [!NOTE]
   > Ha a "HPC" kifejezés nem működik, próbálja meg helyette a "storagecache" karakterláncot használni. Előfordulhat, hogy az előzetes verzióban részt vevő felhasználóknak (a GA előtt) a szolgáltatásnév régebbi nevét kell használniuk.

1. Kattintson a lap alján található **Save (Mentés** ) gombra.

1. Ismételje meg ezt a folyamatot a "Storage blob-adatközreműködői" szerepkör hozzárendeléséhez.  

![a szerepkör-hozzárendelés grafikus felhasználói felületének hozzáadása képernyőkép](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Előfeltétel: a Storage-fiók elérése

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

BLOB Storage-cél hozzáadása előtt győződjön meg arról, hogy a gyorsítótár megfelelő szerepkörrel rendelkezik a Storage-fiók eléréséhez, és hogy a tűzfalbeállítások lehetővé teszik a tárolási cél létrehozását.

Az Azure HPC cache az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/index.yml) használatával engedélyezi a gyorsítótár-szolgáltatás számára, hogy hozzáférjen az Azure Blob Storage-célokhoz tartozó Storage-fiókhoz.

A Storage-fiók tulajdonosának explicit módon hozzá kell adnia a roles [Storage-fiók közreműködőjét](../role-based-access-control/built-in-roles.md#storage-account-contributor) és a [Storage blob adatközreműködőit](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a "HPC cache erőforrás-szolgáltató" felhasználóhoz.

A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem rendelkezik ezekkel a szerepkörökkel.

Akár öt percet is igénybe vehet, hogy a szerepkör-beállítások továbbítsák az Azure-környezetet, ezért néhány percet várnia kell, miután hozzáadta a szerepköröket a tárolási cél létrehozása előtt.

Részletes utasításokért olvassa el az [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával című részt](../role-based-access-control/role-assignments-cli.md) .

Tekintse meg a Storage-fiók tűzfalának beállításait is. Ha a tűzfal úgy van beállítva, hogy csak a "kiválasztott hálózatokra" korlátozza a hozzáférést, akkor előfordulhat, hogy a tárolási cél létrehozása meghiúsul. Használja a [blob Storage-fiók tűzfalának](hpc-cache-blob-firewall-fix.md)használata című dokumentumban ismertetett megkerülő megoldást.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>BLOB Storage-cél hozzáadása az Azure CLI-vel

Az Azure Blob Storage-tárolók definiálásához használja az az [HPC-cache blob-Storage-Target hozzáadási](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) felületet.

> [!NOTE]
> Az Azure CLI-parancsok esetében jelenleg a névtér elérési útjának létrehozására van szükség a tárolási cél hozzáadásakor. Ez eltér a Azure Portal felülettel használt folyamattól.

A szabványos erőforráscsoport és a gyorsítótár neve paraméterek mellett meg kell adnia ezeket a beállításokat a tárolási cél számára:

* ``--name`` -Adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.

* ``--storage-account`` – A fiók azonosítója a következő formában:/Subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name>*

  A használható Storage-fiókkal kapcsolatos információkért olvassa el a [blob Storage-követelmények](hpc-cache-prerequisites.md#blob-storage-requirements)című témakört.

* ``--container-name`` -Adja meg a tárolási célhoz használandó tároló nevét.

* ``--virtual-namespace-path`` – Állítsa be a tárolási cél ügyféloldali elérési útját. Útvonalak befoglalása idézőjelek közé. A virtuális névtér szolgáltatással kapcsolatos további információkért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című témakört.

Példa parancs:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Új NFS-tárolási cél hozzáadása

Egy NFS-tárolási cél különböző beállításokkal rendelkezik a blob Storage-tárolóban. A használati modell beállítása segít a gyorsítótárban, hogy hatékonyan gyorsítótárazza az adatokat ebből a tárolási rendszerből.

![Képernyőfelvétel a Storage-cél hozzáadása oldalról a megadott NFS-célként](media/add-nfs-target.png)

> [!NOTE]
> Az NFS-tárolási cél létrehozása előtt győződjön meg arról, hogy a tárolási rendszer elérhető az Azure HPC-gyorsítótárból, és megfelel az engedélyek követelményeinek. A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem fér hozzá a tárolási rendszerhez. A részletekért olvassa el az [NFS-tárolási követelmények](hpc-cache-prerequisites.md#nfs-storage-requirements) , valamint a [NAS-konfiguráció és az NFS-tárolási cél hibaelhárítása](troubleshoot-nas.md) című cikk

### <a name="choose-a-usage-model"></a>Használati modell kiválasztása
<!-- referenced from GUI by aka.ms link -->

Amikor olyan tárolási célt hoz létre, amely az NFS-t használja a tárolási rendszer eléréséhez, ki kell választania a cél használati modelljét. Ez a modell határozza meg, hogyan gyorsítótárazza az adatait.

A beállításokkal kapcsolatos további információkért olvassa el a [használati modellek megismerése](cache-usage-models.md) című témakört.

A beépített használati modellek segítségével kiválaszthatja, hogyan egyenlítheti ki a gyors választ az elavult adatok beszerzésének kockázatával. Ha optimalizálni szeretné a fájlok olvasásának sebességét, előfordulhat, hogy nem biztos benne, hogy a gyorsítótárban lévő fájlok be vannak-e jelölve a háttérbeli fájlokban. Ha azonban azt szeretné, hogy a fájlok mindig naprakészek legyenek a távoli tárterülettel, válasszon olyan modellt, amely gyakran ellenőrzi a fájlokat.

Ez a három lehetőség a legtöbb esetben a következőket fedi le:

* **Gyakori, ritka írások olvasása** – felgyorsítja a statikus vagy ritkán módosított fájlok olvasási hozzáférését.

  Ezzel a beállítással a rendszer gyorsítótárazza a fájlokat az ügyfél olvasásai között, de azonnal továbbítja az ügyfeleket a háttérbeli tárolóba. A gyorsítótárban tárolt fájlok nincsenek automatikusan összehasonlítva az NFS-tároló kötetén található fájlokkal.

  Ne használja ezt a beállítást, ha fennáll a kockázata annak, hogy egy fájl közvetlenül a tárolási rendszeren módosul, anélkül, hogy először a gyorsítótárba kellene írni. Ha ez történik, a fájl gyorsítótárazott verziója nem lesz szinkronizálva a háttér-fájllal.

* **15%-nál nagyobb írások** – ez a beállítás az olvasási és írási teljesítményt is felgyorsítja.

  A rendszer gyorsítótárazza az ügyfél olvasását és az ügyfél írását. A gyorsítótárban lévő fájlok újabbak, mint a háttér-tárolási rendszer fájljai. A gyorsítótárazott fájlok csak a háttérbeli tároló fájljain, nyolc óránként lesznek automatikusan bejelölve. A gyorsítótárban lévő módosított fájlokat a rendszer a háttérbeli tárolóba írja, miután a gyorsítótárban 20 percen belül megtörtént a további módosítások nélkül.

  Ne használja ezt a beállítást, ha bármelyik ügyfél közvetlenül csatlakoztatja a háttérbeli tároló kötetét, mert fennáll a kockázata annak, hogy elavult fájlokkal fog rendelkezni.

* Az **ügyfelek az NFS-célhelyre írhatnak, és megkerülik a gyorsítótárat** – ezt a beállítást akkor válassza, ha a munkafolyamatban lévő bármelyik ügyfél közvetlenül a tárolási rendszerbe írja az adatait anélkül, hogy először a gyorsítótárba írna, vagy ha az adatkonzisztenciát szeretné optimalizálni.

  Az ügyfelek által igényelt fájlok gyorsítótárazva vannak, de a fájloknak az ügyfélről történő módosításai azonnal átkerülnek a háttérrendszer-tároló rendszerbe. A gyorsítótárban lévő fájlokat a rendszer gyakran ellenőrzi a frissítések háttérbeli verzióin. Ez az ellenőrzés fenntartja az adatkonzisztenciaot, ha a fájlok közvetlenül a tárolási rendszeren változnak a gyorsítótáron keresztül.

A többi lehetőségről a [használati modellek](cache-usage-models.md)ismertetése című témakörben olvashat bővebben.

Ez a táblázat a használati modellek közötti különbségeket foglalja össze:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> A **háttér-ellenőrzési** érték azt jelzi, hogy a gyorsítótár automatikusan összehasonlítja-e a fájljait a távoli tárolóban lévő forrásfájlokat. Az összehasonlítást azonban elindíthatja egy olyan ügyfélalkalmazás elküldésével, amely readdirplus műveletet tartalmaz a háttér-tárolási rendszeren. A Readdirplus egy szabványos NFS API (más néven kiterjesztett olvasás), amely a címtár metaadatait adja vissza, ami miatt a gyorsítótár összehasonlítja és frissíti a fájlokat.

### <a name="create-an-nfs-storage-target"></a>NFS-tárolási cél létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótár-példányt, és kattintson a bal oldali oldalsávon található **tárolási célok** elemre.

![képernyőfelvétel a beállításokról > a tárolási cél lapon két meglévő tárolási cél szerepel egy táblában, és a táblázat fölé emelt a + tároló hozzáadása gomb](media/add-storage-target-button.png)

A **tárolási célok** lap felsorolja az összes meglévő célt, és egy hivatkozást ad hozzá egy újat.

Kattintson a **tárolási cél hozzáadása** gombra.

![Képernyőfelvétel a Storage-cél hozzáadása oldalról a megadott NFS-célként](media/add-nfs-target.png)

Adja meg ezt az információt egy NFS-alapú tárolási cél számára:

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.

* **Cél típusa** – válassza az **NFS** lehetőséget.

* **Állomásnév** – adja meg az NFS-tárolási rendszer IP-címét vagy teljesen minősített tartománynevét. (Csak akkor használjon tartománynevet, ha a gyorsítótár egy olyan DNS-kiszolgálóhoz fér hozzá, amely fel tudja oldani a nevet.)

* **Használati modell** – válassza ki az egyik adatgyorsítótárazási profilt a munkafolyamat alapján, a fenti [használati modell kiválasztása](#choose-a-usage-model) című részben leírtak szerint.

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

A tárolási cél létrehozásához használja az Azure CLI-parancsot az [HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) paranccsal.

> [!NOTE]
> Az Azure CLI-parancsok esetében jelenleg a névtér elérési útjának létrehozására van szükség a tárolási cél hozzáadásakor. Ez eltér a Azure Portal felülettel használt folyamattól.

Adja meg ezeket az értékeket a gyorsítótár neve és a gyorsítótár-erőforráscsoport mellett:

* ``--name`` -Adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.
* ``--nfs3-target`` – Az NFS-tárolási rendszerek IP-címe. (Itt teljes tartománynevet használhat, ha a gyorsítótár egy olyan DNS-kiszolgálóhoz fér hozzá, amely fel tudja oldani a nevet.)
* ``--nfs3-usage-model`` – Az egyik adatgyorsítótárazási profil, amely a fenti [használati modell kiválasztása](#choose-a-usage-model)című részben található.

  Ellenőrizze a használati modellek nevét a parancs az [HPC-cache használati-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)paranccsal.

* ``--junction`` – A Junction paraméter összekapcsolja az ügyfélhez kapcsolódó virtuális fájl elérési útját a tárolási rendszeren lévő exportálási útvonallal.

  Egy NFS-tárolási cél több virtuális útvonallal is rendelkezhet, ha az egyes elérési utak ugyanazon a tárolási rendszeren eltérő exportálási vagy alkönyvtárat jelölnek. Hozzon létre egy tárolási rendszer összes elérési útját egy tárolási célra.

  A [névtér elérési útját bármikor hozzáadhatja és szerkesztheti](add-namespace-paths.md) a tárolási célra.

  A ``--junction`` paraméter a következő értékeket használja:

  * ``namespace-path`` – Az ügyfélre irányuló virtuális fájl elérési útja
  * ``nfs-export`` – A tárolásirendszer exportálja az ügyfél felé irányuló elérési úttal való hozzárendeléshez
  * ``target-path`` (nem kötelező) – szükség esetén az Exportálás alkönyvtára

  Például: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  A virtuális névtér szolgáltatással kapcsolatos további tudnivalókért olvassa el az [összesített névtér konfigurálása](hpc-cache-namespace.md) című témakört.

Példa parancs:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Kimenet:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Új ADLS-NFS Storage-cél hozzáadása (előzetes verzió)

A ADLS-NFS tárolási célok olyan Azure Blob-tárolókat használnak, amelyek támogatják a hálózati fájlrendszer (NFS) 3,0-es protokollját.

> [!NOTE]
> Az NFS 3,0 protokoll támogatása az Azure Blob Storage-hoz nyilvános előzetes verzióban érhető el. A rendelkezésre állás korlátozott, és előfordulhat, hogy a funkciók mostantól változnak, és a szolgáltatás általánosan elérhetővé válik. Az előnézeti technológiákat ne használja éles rendszerekben.
>
> A legfrissebb információkért olvassa el az [NFS 3,0 protokoll támogatása](../storage/blobs/network-file-system-protocol-support.md) című témakört.

A ADLS-NFS tárolási célpontok némelyike hasonlóságot mutat a blob Storage-célokkal és néhány NFS-tárolási célokkal. Például:

* A blob Storage-tárolóhoz hasonlóan meg kell adnia az Azure HPC cache engedélyt a [Storage-fiók eléréséhez](#add-the-access-control-roles-to-your-account).
* Az NFS Storage-tárolóhoz hasonlóan a gyorsítótár [használati modelljét](#choose-a-usage-model)is be kell állítania.
* Mivel az NFS-t támogató blob-tárolók NFS-kompatibilis hierarchikus struktúrával rendelkeznek, nem kell a gyorsítótárat használnia az adat betöltéséhez, és a tárolókat más NFS-rendszerek is olvasni tudják. Az adatok előre tölthetők be egy ADLS-NFS-tárolóba, majd hozzáadhatók egy HPC-gyorsítótárhoz tárolási célként, majd később is elérhetők az adatok a HPC-gyorsítótáron kívülről. Ha standard BLOB-tárolót használ HPC cache Storage-tárolóként, az adatok tulajdonosi formában vannak megírva, és csak más Azure HPC cache-kompatibilis termékekből érhetők el.

A ADLS-NFS tárolási cél létrehozása előtt létre kell hoznia egy NFS-kompatibilis Storage-fiókot. Kövesse az [Azure HPC cache előfeltételeinek](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) és a [blob Storage csatlakoztatása az NFS használatával](../storage/blobs/network-file-system-protocol-support-how-to.md)című témakör útmutatásait. A Storage-fiók beállítása után létrehozhat egy új tárolót a tárolási cél létrehozásakor.

ADLS-NFS tárolási cél létrehozásához nyissa meg a **tároló hozzáadása** lapot a Azure Portal. (A további módszerek fejlesztés alatt állnak.)

![Képernyőfelvétel a tárolási cél hozzáadása oldalról a ADLS-NFS-célhoz definiált](media/add-adls-target.png)

Adja meg ezt az információt.

* **Tárolási cél neve** – adjon meg egy nevet, amely azonosítja ezt a tárolási célt az Azure HPC cache-ben.
* **Cél típusa** – válassza a **ADLS – NFS** elemet.
* **Storage-fiók** – válassza ki a használni kívánt fiókot. Ha az NFS-kompatibilis Storage-fiók nem jelenik meg a listában, ellenőrizze, hogy az megfelel-e az előfeltételeknek, és hogy a gyorsítótár el tudja-e érni.

  Engedélyeznie kell a gyorsítótár-példányt a Storage-fiók eléréséhez a [hozzáférési Szerepkörök hozzáadása](#add-the-access-control-roles-to-your-account)című témakörben leírtak szerint.

* **Storage-tároló** – válassza ki az NFS-kompatibilis BLOB-tárolót ehhez a célhoz, vagy kattintson az **új létrehozása** lehetőségre.

* **Használati modell** – válassza ki az egyik adatgyorsítótárazási profilt a munkafolyamat alapján, a fenti [használati modell kiválasztása](#choose-a-usage-model) című részben leírtak szerint.

Ha elkészült, kattintson az **OK** gombra a tárolási cél hozzáadásához.

<!-- **** -->

## <a name="view-storage-targets"></a>Tárolási célok megtekintése

A gyorsítótárhoz már definiált tárolási célok megjelenítéséhez használhatja a Azure Portal vagy az Azure CLI-t is.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótár-példányt, és kattintson a **tárolási célok** elemre, amely a bal oldali oldalsávon a beállítások fejléc alatt található. A tárolási célok lap felsorolja az összes meglévő célt és vezérlőelemet a hozzáadáshoz vagy a törléshez.

Kattintson a tárolási cél nevére a Részletek lap megnyitásához.

További információért olvassa el a [tárolási célok szerkesztése](hpc-cache-edit-storage.md) című témakört.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Használja az az [HPC-cache Storage-Target List](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) kapcsolót a gyorsítótár meglévő tárolási céljainak megjelenítéséhez. Adja meg a gyorsítótár nevét és az erőforráscsoportot (kivéve, ha globálisan beállította).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Az [az HPC-cache Storage-Target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) használatával megtekintheti egy adott tárolási cél részleteit. (Adja meg a tárolási célt név szerint.)

Példa:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Következő lépések

A tárolási célok létrehozása után folytassa ezeket a feladatokat, hogy a gyorsítótár használatra készen álljon:

* [Az összesített névtér beállítása](add-namespace-paths.md)
* [Az Azure HPC Cache csatlakoztatása](hpc-cache-mount.md)
* [Az Azure Blob Storage-ba irányuló adatáthelyezés](hpc-cache-ingest.md)

Ha frissítenie kell a beállításokat, [szerkesztheti a tárolási célt](hpc-cache-edit-storage.md).
