---
title: Tároló hozzáadása Azure HPC Cache
description: Tárolási célok meghatározása, hogy a Azure HPC Cache a helyszíni NFS-rendszert vagy Azure Blob-tárolókat használva hosszú távú fájltárolásra
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862236"
---
# <a name="add-storage-targets"></a>Céltárak hozzáadása

*A céltárolók* a tárfiókon keresztül elért fájlok háttértárhelyi Azure HPC Cache. Hozzáadhat NFS-tárolókat (például helyszíni hardverrendszereket), vagy tárolhat adatokat az Azure Blobban.

Egy gyorsítótárhoz legfeljebb 20 különböző tárolási célt definiálhat. A gyorsítótár egyetlen összesített névtérben mutatja be az összes tárolási célt.

A névtér elérési útjai külön vannak konfigurálva a céltárolók hozzáadása után. Az NFS-tárolók általában legfeljebb tíz névtérútvonalat, vagy néhány nagyobb konfiguráció esetén több elérési utat is kínálnak. Részletekért olvassa el [az NFS-névterek elérési](add-namespace-paths.md#nfs-namespace-paths) útjait.

Ne feledje, hogy a tárolóexportok exportálásának elérhetőnek kell lennie a gyorsítótár virtuális hálózatán. Helyszíni hardveres tárolás esetén előfordulhat, hogy be kell állítania egy DNS-kiszolgálót, amely fel tudja oldani az NFS-tárelérés állomásneveit. További információ: [DNS-hozzáférés.](hpc-cache-prerequisites.md#dns-access)

A gyorsítótár létrehozása után adjon hozzá céltárolókat. Kövesse az alábbi folyamatot:

1. [A gyorsítótár létrehozása](hpc-cache-create.md)
1. Tárolási cél meghatározása (ebben a cikkben található információk)
1. [Hozza létre az ügyféloldali elérési utakat](add-namespace-paths.md) (az [összesített névtérhez)](hpc-cache-namespace.md)

A tárolóhely hozzáadásának eljárása az adott tároló típusától függően némileg eltérő. Az egyes részleteket alább olvashatja.

Kattintson az alábbi [képre,](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) és tekintse meg a gyorsítótár létrehozását és a tároló célhelyének hozzáadását bemutató videót a Azure Portal.

[![video thumbnail: Azure HPC Cache: Beállítás (kattintson ide a videó oldalának megjelenítéséhez)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Új Azure Blob Storage-cél hozzáadása

Egy új Blob Storage-célhelyhez szükség van egy üres Blob-tárolóra vagy egy olyan tárolóra, amely a felhőalapú fájlrendszer Azure HPC Cache adatokkal van feltöltve. További információ a blobtárolók előzetes betöltéséről: [Adatok áthelyezése az Azure Blob Storage-ba.](hpc-cache-ingest.md)

A Azure Portal **Tárolócél hozzáadása** oldalon lehetősége van létrehozni egy új blobtárolót, mielőtt hozzáadja.

> [!NOTE]
> NfS-hez csatlakoztatott blobtárolókhoz használja az [ADLS-NFS](#) tároló céltípust.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótárpéldányt, és kattintson a **storage targets (Tárolási** célok) elemre a bal oldali oldalsávon.

![képernyőkép a tárolócél > lapról, amely két meglévő tárolócélt ad meg egy táblában, valamint egy kiemelést a + Tárolócél hozzáadása gomb körül a táblázat felett](media/add-storage-target-button.png)

A **Tárolócélok** lap felsorolja az összes meglévő célt, és egy hivatkozást ad egy új hozzáadásához.

Kattintson a **Tárolócél hozzáadása gombra.**

![képernyőkép a tároló célhelyének hozzáadása lapról, amely egy új Azure Blob Storage-cél információival van feltöltve](media/hpc-cache-add-blob.png)

Az Azure Blob-tároló meghatározásához adja meg ezt az információt.

* **Tároló célneve** – Állítson be egy nevet, amely azonosítja ezt a tárolócélt a Azure HPC Cache.
* **Cél típusa –** Válassza a **Blob lehetőséget.**
* **Tárfiók** – Válassza ki a használni kívánt fiókot.

  A tárfiók eléréséhez engedélyeznie kell a gyorsítótárpéldányt a [Hozzáférési szerepkörök hozzáadása.](#add-the-access-control-roles-to-your-account)

  A használható tárfiókok fajtájára vonatkozó információkért olvassa el a [Blob Storage követelményeit.](hpc-cache-prerequisites.md#blob-storage-requirements)

* **Storage-tároló** – Válassza ki a cél blobtárolóját, vagy kattintson az **Új létrehozása elemre.**

  ![képernyőkép az új tároló nevének és hozzáférési szintjének (privát) megadására szolgáló párbeszédpanelről](media/add-blob-new-container.png)

Ha végzett, kattintson az **OK gombra** a tároló célhelyének hozzáadásához.

> [!NOTE]
> Ha a tárfiók tűzfala úgy van beállítva, hogy csak a "kiválasztott hálózatokra" korlátozza a hozzáférést, használja a Blob Storage-fiók tűzfalbeállításait megkerülő cikkben leírt ideiglenes [áthidaló megoldást.](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>Hozzáférés-vezérlési szerepkörök hozzáadása a fiókhoz

Azure HPC Cache [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/index.yml) engedélyezi a gyorsítótár-szolgáltatásnak, hogy hozzáférjen a tárfiókhoz az Azure Blob Storage-célokhoz.

A tárfiók tulajdonosának explicit [](../role-based-access-control/built-in-roles.md#storage-account-contributor) módon hozzá [](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) kell adnia a Tárfiók közreműködője és a Storage-blobadatok közreműködője szerepkört a "HPC Cache erőforrás-szolgáltató" felhasználóhoz.

Ezt előre is megteheti, vagy kattintson arra a hivatkozásra az oldalon, ahol hozzáad egy Blob Storage-célt. Vegye figyelembe, hogy akár öt percig is eltarthat, amíg a szerepkör-beállítások propagálnak az Azure-környezetben, ezért néhány percet várnia kell a szerepkörök hozzáadása után, mielőtt létrehozná a tárolóhelyet.

Az Azure-szerepkörök hozzáadásának lépései:

1. Nyissa meg a tárfiók **Hozzáférés-vezérlés (IAM)** lapját. (A Tárolási cél hozzáadása oldalon található **hivatkozás** automatikusan megnyitja ezt az oldalt a kiválasztott fiókhoz.)

1. Kattintson a **+** lap tetején található elemre, és válassza a **Szerepkör-hozzárendelés hozzáadása lehetőséget.**

1. Válassza ki a "Tárfiók közreműködője" szerepkört a listából.

1. A Hozzáférés **hozzárendelése mezőben** hagyja bejelölve az alapértelmezett értéket ("Azure AD-felhasználó, -csoport vagy -szolgáltatásnév").  

1. A Select **(Kijelölés)** mezőben keressen a "hpc" kifejezésre.  Ennek a sztringnek egyeznie kell egy "HPC Cache erőforrás-szolgáltató" nevű szolgáltatásnévvel. Kattintson erre a rendszerbiztonsági tagra a kiválasztásához.

   > [!NOTE]
   > Ha a "hpc" keresés nem működik, próbálja inkább a "storagecache" sztringet használni. Előfordulhat, hogy az előzetes verziókban részt vett felhasználóknak (az ga ga előtt) a szolgáltatásnévnek a régebbi nevet kell használnia.

1. Kattintson **az** alul található Mentés gombra.

1. Ismételje meg ezt a folyamatot a "Storage-blobadatok közreműködője" szerepkör hozzárendeléséhez.  

![képernyőkép a szerepkör-hozzárendelési grafikus felhasználói felület hozzáadásáról](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Előfeltétel: Tárfiók-hozzáférés

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Blob Storage-cél hozzáadása előtt ellenőrizze, hogy a gyorsítótár megfelelő szerepkörökkel rendelkezik-e a tárfiók eléréséhez, és hogy a tűzfalbeállítások lehetővé teszik-e a tároló célhelyének létrehozását.

Azure HPC Cache [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/index.yml) engedélyezi a gyorsítótár-szolgáltatásnak, hogy hozzáférjen a tárfiókhoz az Azure Blob Storage-célokhoz.

A tárfiók tulajdonosának explicit [](../role-based-access-control/built-in-roles.md#storage-account-contributor) módon hozzá kell adni a Tárfiók közreműködője és a [Storage-blobadatok](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) közreműködője szerepkört a "HPC Cache erőforrás-szolgáltatóhoz".

A tároló célhelyének létrehozása sikertelen lesz, ha a gyorsítótár nem rendelkezik ezekkel a szerepkörökkel.

A szerepkör-beállítások Azure-környezetben való propagálása akár öt percet is igénybe vehet, ezért a tárolóhely létrehozása előtt néhány percet várnia kell a szerepkörök hozzáadása után.

Részletes útmutatásért olvassa el az [Azure-beli szerepkör-hozzárendelések hozzáadása](../role-based-access-control/role-assignments-cli.md) vagy eltávolítása az Azure CLI használatával cikkeket.

Ellenőrizze a tárfiók tűzfalbeállítását is. Ha a tűzfal úgy van beállítva, hogy csak a "kiválasztott hálózatokra" korlátozza a hozzáférést, a tároló célhelyének létrehozása sikertelen lehet. Használja a Blob Storage-fiók tűzfalbeállításának [megkerülő megoldását.](hpc-cache-blob-firewall-fix.md)

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Blob Storage-cél hozzáadása az Azure CLI használatával

Az [az hpc-cache blob-storage-target add interfész](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) használatával definiálhat egy Azure Blob Storage-célt.

> [!NOTE]
> Az Azure CLI-parancsokhoz jelenleg létre kell hoznia egy névtér elérési útját egy tárolási cél hozzáadásakor. Ez eltér a felhasználói felületen használt Azure Portal folyamattól.

A standard erőforráscsoport- és gyorsítótárnév-paraméterek mellett ezeket a beállításokat is meg kell adnia a tárolási célhelyhez:

* ``--name`` – Állítson be egy nevet, amely azonosítja ezt a tárolócélt a Azure HPC Cache.

* ``--storage-account`` – A fiókazonosító, ebben a formában: /subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  A használható tárfiókok fajtájára vonatkozó információkért olvassa el a [Blob Storage követelményeit.](hpc-cache-prerequisites.md#blob-storage-requirements)

* ``--container-name`` – Adja meg az ehhez a tárolóhoz használni kívánt tároló nevét.

* ``--virtual-namespace-path`` – Állítsa be az ügyféloldali fájl elérési útját ehhez a tárolási célhoz. Az elérési utakat idézőjelek közé kell tenni. A [virtuális névtér funkcióval](hpc-cache-namespace.md) kapcsolatos további információkért olvassa el az Aggregált névtér megterve lehetőséget.

Példaparancs:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Új NFS-tárolóhely hozzáadása

Az NFS-tároló célhelyének beállításai eltérnek a Blob Storage-cél beállításaitól. A használati modell beállításával a gyorsítótár hatékonyan gyorsítótárazza az ebből a tárolórendszerből származó adatokat.

![Képernyőkép a tároló célhelyének hozzáadásáról az NFS-cél definiálva](media/add-nfs-target.png)

> [!NOTE]
> Mielőtt létrehoz egy NFS-tárolóhelyet, győződjön meg arról, hogy a tárolórendszer elérhető a Azure HPC Cache és megfelel az engedélykövetelményeknek. A tároló célhelyének létrehozása sikertelen lesz, ha a gyorsítótár nem fér hozzá a tárolórendszerhez. Részletekért olvassa el [az NFS tárolási követelményeit](hpc-cache-prerequisites.md#nfs-storage-requirements) és [a NAS-konfigurációval és AZ NFS-tároló](troubleshoot-nas.md) célhibáival kapcsolatos hibák elhárítása témakört.

### <a name="choose-a-usage-model"></a>Használati modell kiválasztása
<!-- referenced from GUI by aka.ms link -->

Amikor olyan tárolóhelyet hoz létre, amely NFS-t használ a tárolórendszerének eléréséhez, ki kell választania egy használati modellt a célhoz. Ez a modell határozza meg, hogyan vannak gyorsítótárazva az adatok.

Az [ezekkel a beállításokkal](cache-usage-models.md) kapcsolatos további részletekért olvassa el a használati modelleket.

A beépített használati modellekkel kiválaszthatja, hogyan egyensúlyba hozza a gyors reagálást az elavult adatok kieső kockázatával. Ha optimalizálni szeretné a fájlok olvasásának sebességét, nem biztos, hogy a rendszer ellenőrzi a gyorsítótárban lévő fájlokat a háttérfájlok között. Ha azonban azt szeretné, hogy a fájlok mindig naprakészek a távoli tárolóval, válasszon olyan modellt, amely gyakran ellenőrzi a fájlokat.

Ez a három lehetőség a legtöbb helyzetet lefedi:

* **Nagy írási terhelésű,** ritkán használt írások – Felgyorsítja a statikus vagy ritkán módosított fájlok olvasási hozzáférését.

  Ez a beállítás gyorsítótárazza a fájlokat az ügyfél olvasásaiból, de az ügyfél írási funkcióját azonnal átadja a háttértárnak. A rendszer nem hasonlítja össze automatikusan a gyorsítótárban tárolt fájlokat az NFS-tárolóköteten lévő fájlokkal.

  Ne használja ezt a beállítást, ha fennáll annak a kockázata, hogy egy fájl közvetlenül a tárolórendszeren, anélkül módosítható, hogy először a gyorsítótárba kellene írnia. Ha ez történik, a fájl gyorsítótárazott verziója nem lesz szinkronizálva a háttérfájllal.

* **15%-nál nagyobb írási arány** – Ez a beállítás mind az olvasási, mind az írási teljesítményt felgyorsítja.

  Az ügyfél-olvasások és az ügyfélírások egyaránt gyorsítótárazva vannak. A rendszer feltételezi, hogy a gyorsítótárban lévő fájlok újabbak, mint a háttértárrendszer fájljai. A gyorsítótárazott fájlokat a rendszer csak 8 óránként ellenőrzi automatikusan a háttértárban lévő fájlokon. A gyorsítótárban lévő módosított fájlokat a rendszer a háttértárrendszerbe írja, miután 20 percig a gyorsítótárban voltak, további módosítások nélkül.

  Ne használja ezt a beállítást, ha bármelyik ügyfél közvetlenül csatlakoztatja a háttértárkötetet, mert fennáll a veszélye, hogy elavult fájlokat fog tartalmazni.

* **Az** ügyfelek az NFS-célhelyre írnak, megkerülve a gyorsítótárat – Akkor válassza ezt a beállítást, ha a munkafolyamat bármely ügyfele közvetlenül ír adatokat a tárolórendszerbe anélkül, hogy először a gyorsítótárba írna, vagy ha optimalizálni szeretné az adatkonzisztenciát.

  A rendszer gyorsítótárazza az ügyfelek által kért fájlokat, de az ügyfélről ezen fájlokon végrehajtott módosításokat a rendszer azonnal a háttértárrendszernek is továbbkűszi. A gyorsítótárban lévő fájlokat gyakran ellenőrzik a frissítéseket a háttérverziókban. Ez az ellenőrzés fenntartja az adatkonzisztenciát, ha a fájlokat közvetlenül a tárolórendszeren, és nem a gyorsítótáron keresztül változtatják meg.

A többi lehetőséggel kapcsolatos részletekért olvassa el [a használati modelleket.](cache-usage-models.md)

Ez a táblázat az összes használati modell közötti különbségeket foglalja össze:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> A **háttér-ellenőrzési** érték azt mutatja, hogy a gyorsítótár mikor hasonlítja össze automatikusan a fájljait a távoli tárolóban lévő forrásfájlokkal. Összehasonlítást azonban aktiválhat, ha egy olyan ügyfélkérést küld, amely egy readdirplus műveletet tartalmaz a háttértárrendszeren. A Readdirplus egy szabványos NFS API (más néven bővített olvasás), amely visszaadja a könyvtár metaadatait, ami miatt a gyorsítótár összehasonlítja és frissíti a fájlokat.

### <a name="create-an-nfs-storage-target"></a>NFS-tárolóhely létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótárpéldányt, és kattintson a bal oldali **oldalsávon a** Tárolási célok elemre.

![képernyőkép a tárolási céloldal > beállításokról, két meglévő tárolócéllal a táblázatban, valamint egy kiemelés a tábla fölötti + Tárolócél hozzáadása gomb körül](media/add-storage-target-button.png)

A **Tárolási célok lap** felsorolja az összes meglévő célt, és egy hivatkozást biztosít egy új hozzáadásához.

Kattintson a **Tárolócél hozzáadása gombra.**

![Képernyőkép a tároló célhelyének hozzáadásáról az NFS-cél definiálva](media/add-nfs-target.png)

Adja meg ezt az információt egy NFS-alapú tárolócélhoz:

* **Tárolási cél neve** – Adja meg a tároló célhelyét azonosító nevet a Azure HPC Cache.

* **Cél típusa –** Válassza az **NFS lehetőséget.**

* **Állomásnév** – Adja meg az NFS-tárolórendszer IP-címét vagy teljes tartománynevét. (Csak akkor használjon tartománynevet, ha a gyorsítótárnak hozzáférése van egy DNS-kiszolgálóhoz, amely fel tudja oldani a nevet.)

* **Használati modell** – Válasszon egy adat-gyorsítótárazó profilt [a](#choose-a-usage-model) munkafolyamata alapján a fenti Használati modell kiválasztása szakasz alapján.

Ha végzett, kattintson az **OK gombra** a tároló célhelyének hozzáadásához.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

A tárolási cél létrehozásához használja [az az hpc-cache nfs-storage-target azure CLI-parancsot.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add)

> [!NOTE]
> Az Azure CLI-parancsokhoz jelenleg szükség van egy névtér elérési útjának létrehozására egy tárolóhely hozzáadásakor. Ez eltér a felhasználói felületen használt Azure Portal folyamattól.

A gyorsítótár neve és a gyorsítótár erőforráscsoportja mellett a következő értékeket is meg kell adni:

* ``--name`` – Állítson be egy nevet, amely azonosítja ezt a tárolócélt a Azure HPC Cache.
* ``--nfs3-target`` – Az NFS-tárolórendszer IP-címe. (Itt teljes tartománynevet is használhat, ha a gyorsítótárnak hozzáférése van egy DNS-kiszolgálóhoz, amely fel tudja oldani a nevet.)
* ``--nfs3-usage-model`` – Az egyik adat-gyorsítótárazó profil, [amelyről a fenti Használati modell kiválasztása leírásban](#choose-a-usage-model)van leírás.

  Ellenőrizze a használati modellek nevét az [az hpc-cache usage-model list paranccsal.](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list)

* ``--junction`` – A csomópontparaméter az ügyféloldali virtuális fájl elérési útját egy exportálási útvonallal összeköti a tárolórendszeren.

  Egy NFS-tároló célhelyének több virtuális elérési útja is lehet, ha minden elérési út egy adott tárolórendszer különböző exportálási vagy alkönyvtárát jelöli. Egy tárolórendszer összes elérési útjának létrehozása egyetlen tárolóhelyen.

  A [tárolóhelyen bármikor](add-namespace-paths.md) hozzáadhat és szerkeszthet névtérútvonalat.

  A ``--junction`` paraméter a következő értékeket használja:

  * ``namespace-path`` – Az ügyféloldali virtuális fájl elérési útja
  * ``nfs-export`` – Az ügyféloldali elérési úthoz társítható tárolórendszer exportálása
  * ``target-path`` (nem kötelező) – Az exportálás alkönyvtára, ha szükséges

  Például: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  További [információ a](hpc-cache-namespace.md) virtuális névtér funkcióról: Összesített névtér konfigurálása.

Példaparancs:

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

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Új ADLS-NFS tárolási cél hozzáadása (ELŐZETES VERZIÓ)

Az ADLS-NFS tárolási célok olyan Azure Blob-tárolókat használnak, amelyek támogatják a hálózati fájlrendszer (NFS) 3.0 protokollt.

> [!NOTE]
> Az Azure Blob Storage NFS 3.0 protokoll támogatása nyilvános előzetes verzióban érhető el. A rendelkezésre állás korlátozott, és a funkciók változhatnak a funkció általános elérhetővé válnak. Éles rendszerekben ne használjon előzetes verziójú technológiát.
>
> A legfrissebb információkért olvassa el az [NFS 3.0](../storage/blobs/network-file-system-protocol-support.md) protokolltámogatást.

Az ADLS-NFS tárolási célok hasonlóak a Blob Storage-célokkal és néhány NFS-céltárolóval. Például:

* A Blob Storage-tárolókhoz Azure HPC Cache engedélyt kell adni a [tárfiók eléréséhez.](#add-the-access-control-roles-to-your-account)
* Az NFS-tárolókhoz hasonló gyorsítótár-használati modellt [kell beállítania.](#choose-a-usage-model)
* Mivel az NFS-kompatibilis blobtárolók NFS-kompatibilis hierarchikus struktúrával rendelkezik, nincs szükség a gyorsítótár használatára az adatok betöltéséhez, és a tárolók más NFS-rendszerek számára is olvashatók. Előre betöltheti az adatokat egy ADLS-NFS-tárolóba, hozzáadhatja őket egy HPC Cache-tárolóhoz tárolási célként, majd később hozzáférhet az adatokhoz a HPC Cache. Ha standard blobtárolót használ HPC Cache tárolási célként, az adatok saját formátumban lesznek megírva, és csak más, Azure HPC Cache kompatibilis termékekből érhetők el.

Mielőtt létrehozhat egy ADLS-NFS-tárolót, létre kell hoznia egy NFS-kompatibilis tárfiókot. Kövesse az [Előfeltételek](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) a blobok Azure HPC Cache és a [Blob Storage csatlakoztatása NFS használatával cikk utasításait.](../storage/blobs/network-file-system-protocol-support-how-to.md) A tárfiók beállítása után létrehozhat egy új tárolót a tároló célhelyének létrehozásakor.

A konfigurációval kapcsolatos további információkért olvassa el az [NFS-hez](nfs-blob-considerations.md) csatlakoztatott blobtároló és Azure HPC Cache használatával kapcsolatos további tudnivalókat.

ADLS-NFS tárolási cél létrehozásához  nyissa meg a tároló célhelyének hozzáadása oldalt a Azure Portal. (További metódusok fejlesztés alatt állnak.)

![Képernyőkép a tároló céloldalának hozzáadásáról az ADLS-NFS-cél definiálva](media/add-adls-target.png)

Adja meg ezt az információt.

* **Tárolási cél neve** – Adja meg a tároló célhelyét azonosító nevet a Azure HPC Cache.
* **Cél típusa** – Válassza az **ADLS-NFS lehetőséget.**
* **Tárfiók** – Válassza ki a használni kívánt fiókot. Ha az NFS-kompatibilis tárfiók nem jelenik meg a listában, ellenőrizze, hogy megfelel-e az előfeltételeknek, és hogy a gyorsítótár hozzáfér-e hozzá.

  A tárfiók eléréséhez engedélyeznie kell a gyorsítótárpéldányt a [Hozzáférési szerepkörök hozzáadása.](#add-the-access-control-roles-to-your-account)

* **Storage-tároló** – Válassza ki az NFS-kompatibilis blobtárolót ehhez a célhoz, vagy kattintson az **Új létrehozása elemre.**

* **Használati modell** – Válasszon egy adat-gyorsítótárazó profilt [a](#choose-a-usage-model) munkafolyamata alapján, a fenti Használati modell kiválasztása szakasz alapján.

Ha végzett, kattintson az **OK gombra** a tároló célhelyének hozzáadásához.

## <a name="view-storage-targets"></a>Céltárolók megtekintése

A gyorsítótárhoz Azure Portal vagy az Azure CLI használatával is meg tudja mutatni a gyorsítótárhoz már meghatározott tárolási célokat.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal nyissa meg a gyorsítótárpéldányt, és kattintson a Storage **targets**(Tárolási célok) elemre, amely a bal oldali oldalsáv Beállítások fejléce alatt található. A Tárolócélok lap felsorolja az összes meglévő célhelyet és vezérlőt azok hozzáadásához vagy törléséhez.

Kattintson egy tárolóhely nevére a részletek oldalának megnyitásához.

További [információ: Tárolócélok](hpc-cache-edit-storage.md) szerkesztése.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az [az hpc-cache storage-target list lehetőséggel](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) a gyorsítótár meglévő tárolási céljait mutatjuk be. Adja meg a gyorsítótár nevét és az erőforráscsoportot (kivéve, ha globálisan beállította).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Egy adott tárolási cél részleteinek megtekintése [az az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) használatával. (Adja meg a céltárolót név alapján.)

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

A tárolócélok létrehozása után folytassa a következő feladatokkal, hogy a gyorsítótár használatra kész:

* [Az összesített névtér beállítása](add-namespace-paths.md)
* [Az Azure HPC Cache csatlakoztatása](hpc-cache-mount.md)
* [Adatok áthelyezése az Azure Blob Storage-ba](hpc-cache-ingest.md)

Ha bármilyen beállítást frissítenie kell, szerkesztheti [a tárolócélt.](hpc-cache-edit-storage.md)
