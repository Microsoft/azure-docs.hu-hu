---
title: Azure HPC cache tárolási célok frissítése
description: Az Azure HPC cache tárolási céljainak szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 0c505937d4adbe2596e91ed7269676e60ada8253
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772583"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

A tárolási célokat a Azure Portal vagy az Azure CLI használatával távolíthatja el vagy módosíthatja.

A tároló típusától függően módosíthatja a tárolási célértékek értékét:

* A blob Storage-célok esetében módosíthatja a névtér elérési útját és a hozzáférési házirendet.

* Az NFS-tárolási célok esetében a következő értékeket módosíthatja:

  * Névtér elérési útjai
  * Hozzáférési szabályzat
  * A névtér elérési útjához társított tároló exportálása vagy exportálása
  * Használati modell

* A ADLS-NFS tárolási célok esetében módosíthatja a névtér elérési útját, a hozzáférési házirendet és a használati modellt.

Nem szerkesztheti a tárolási cél nevét, típusát vagy háttérbeli tárolási rendszerét (blob-tároló vagy NFS-állomásnév/IP-cím). Ha módosítania kell ezeket a tulajdonságokat, törölje a tárolási célt, és hozzon létre egy cserét az új értékkel.

> [!TIP]
> Az [Azure HPC cache-gyorsítótárának kezelése videó](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) bemutatja, hogyan szerkesztheti a tárolási célt a Azure Portal.

## <a name="remove-a-storage-target"></a>Tárolási cél eltávolítása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Tárolási cél eltávolításához nyissa meg a **tárolási célok** lapot. Válassza ki a tárolási célt a listából, és kattintson a **Törlés** gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Az [az HPC-cache Storage-Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) paranccsal törölheti a tárolási célt a gyorsítótárból.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

A tárolási cél törlése eltávolítja a tárolási rendszer társítását ezzel az Azure HPC cache rendszerrel, de nem változtatja meg a háttérrendszer tárolási rendszerét. Ha például egy Azure Blob Storage-tárolót használ, a tároló és annak tartalma továbbra is fennáll, miután törölte a gyorsítótárból. A tárolót hozzáadhatja egy másik Azure HPC-gyorsítótárhoz, hozzáadhatja újra a gyorsítótárhoz, vagy törölheti a Azure Portal.

A gyorsítótárban tárolt összes változás a tárolási cél eltávolítása előtt a háttérrendszer tárolási rendszerébe íródik. Ez a folyamat akár egy órát is igénybe vehet, ha a módosult adatmennyiségek nagy része a gyorsítótárban van.

## <a name="change-a-blob-storage-targets-namespace-path"></a>BLOB Storage-cél névtérbeli elérési útjának módosítása

A névtér elérési útjai az ügyfelek által a tárolási cél csatlakoztatásához használt elérési utak. (További információért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) és [az összesített névtér beállítása](add-namespace-paths.md)című témakört.

A névtér elérési útja az egyetlen, amely az Azure Blob Storage-tárolón végezhető el. A Azure Portal vagy az Azure CLI használatával módosíthatja.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Használja az Azure HPC-gyorsítótár **névter** lapját. A névtér lap részletes ismertetését az [összesített névtér beállítása](add-namespace-paths.md)című cikkben találja.

Kattintson a módosítani kívánt elérési út nevére, és hozza létre az új elérési utat a megjelenő szerkesztési ablakban.

![Képernyőkép a névtér oldaláról a blob-névtér elérési útjára való kattintás után – a szerkesztési mezők a jobb oldali ablaktáblán jelennek meg.](media/update-namespace-blob.png)

A módosítások végrehajtása után kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Ha módosítani szeretné a blob Storage-tároló névterét az Azure CLI-vel, használja az parancsot az [HPC-cache blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update)paranccsal. Csak az `--virtual-namespace-path` érték módosítható.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS-tárolási cél frissítése

Az NFS-tárolási célok esetében megváltoztathatja vagy hozzáadhatja a virtuális névtér elérési útját, módosíthatja az NFS-exportálás vagy alkönyvtár értékeit, amelyeket a névtér elérési útja mutat, és megváltoztatja a használati modellt.

Az egyes egyéni DNS-beállításokkal rendelkező gyorsítótárban tárolt tárolási célokhoz az IP-címek frissítésére szolgáló vezérlő is tartozik. (Az ilyen típusú konfiguráció ritka.)

A részletek a következők:

* [Összesített névtér értékeinek](#change-aggregated-namespace-values) (virtuális névtér elérési útja, hozzáférési házirend, exportálás és exportálás alkönyvtár) módosítása
* [A használati modell módosítása](#change-the-usage-model)
* [DNS frissítése](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Összesített névtér értékeinek módosítása

A Azure Portal vagy az Azure CLI segítségével módosíthatja az ügyfél felé irányuló névtér elérési útját, a tároló exportálását és az alkönyvtár exportálását (ha használatban van).

Ha emlékeztetőt szeretne arról, hogyan hozható létre több érvényes elérési út egy tárolási célra, olvassa el az [NFS-névtér elérési útjának hozzáadása](add-namespace-paths.md#nfs-namespace-paths) című témakör útmutatását.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A névtér értékeinek frissítéséhez használja az Azure HPC-gyorsítótár **névter** lapját. A lap részletes ismertetését az [összesített névtér beállítása](add-namespace-paths.md)című cikkben találja.

![képernyőkép a portál névtér oldaláról az NFS-frissítési oldal megnyitásával a jobb oldalon](media/update-namespace-nfs.png)

1. Kattintson a módosítani kívánt elérési út nevére.
1. A szerkesztési ablak használatával beírhatja az új virtuális elérési utat, az exportálást vagy az alkönyvtár értékeit, vagy kijelölhet egy másik hozzáférési házirendet.
1. A módosítások elvégzése után kattintson **az OK** gombra a tárolási cél frissítéséhez vagy a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Használja az az ``--junction`` [HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) paranccsal a névtér elérési útjának, az NFS-exportálás vagy az Exportálás alkönyvtárának módosítására szolgáló lehetőséget.

A ``--junction`` paraméter a következő értékeket használja:

* ``namespace-path`` – Az ügyfélre irányuló virtuális fájl elérési útja
* ``nfs-export`` – A tárolásirendszer exportálja az ügyfél felé irányuló elérési úttal való hozzárendeléshez
* ``target-path`` (nem kötelező) – szükség esetén az Exportálás alkönyvtára

Például: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Az utasításban szereplő összes elérési úthoz mindhárom értéket meg kell adnia ``--junction`` . Használja a meglévő értékeket bármely olyan értékhez, amelyet nem szeretne módosítani.

A gyorsítótár neve, a tárolási cél neve és az erőforráscsoport is szükséges az összes frissítési parancsban.

Példa parancs:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>A használati modell módosítása

A használati modell befolyásolja, hogy a gyorsítótár hogyan őrizze meg az adatokat. További információért olvassa el [a használati modell kiválasztása](hpc-cache-add-storage.md#choose-a-usage-model) című témakört.

Ha módosítani szeretné egy NFS-tárolási cél használati modelljét, használja az alábbi módszerek egyikét.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Módosítsa a használati modellt a Azure Portal **tárolási célok** lapján. Kattintson a módosítani kívánt tárolási cél nevére.

![az NFS-tárolási cél szerkesztési oldalának képernyőképe](media/edit-storage-nfs.png)

Új használati modell kiválasztásához használja a legördülő választót. A tárolási cél frissítéséhez kattintson **az OK** gombra, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Használja az az [HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) parancsot.

A frissítési parancs közel azonos ahhoz a parancshoz, amelyet az NFS-tárolási cél hozzáadásához használ. A részletekért és példákért tekintse meg az [NFS Storage-tároló létrehozása](hpc-cache-add-storage.md#create-an-nfs-storage-target) című témakört.

A használati modell módosításához frissítse a ``--nfs3-usage-model`` beállítást. Például: ``--nfs3-usage-model WRITE_WORKLOAD_15``

A gyorsítótár neve, a tárolási cél neve és az erőforráscsoport értékei is szükségesek.

Ha ellenőrizni szeretné a használati modellek nevét, használja az parancsot az [HPC-cache use-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Ha a gyorsítótár leáll, vagy nem kifogástalan állapotban van, a frissítés a gyorsítótár Kifogástalan állapotba helyezése után fog vonatkozni.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP-cím frissítése (csak egyéni DNS-konfigurációk esetén)

Ha a gyorsítótár nem alapértelmezett DNS-konfigurációt használ, lehetséges, hogy az NFS-tárolási cél IP-címe megváltozik a háttérbeli DNS-változások miatt. Ha a DNS-kiszolgáló megváltoztatja a háttérrendszer IP-címét, akkor az Azure HPC cache elveszítheti a hozzáférést a tárolási rendszerhez.

Ideális esetben a gyorsítótár egyéni DNS-rendszerének kezelőjével együttműködve tervezze meg a frissítéseket, mert ezek a módosítások nem teszik elérhetővé a tárterületet.

Ha frissítenie kell egy tárolási cél DNS-beli IP-címét, akkor a tárolási célok listában megjelenik egy gomb. A **DNS frissítése** lehetőségre kattintva lekérdezheti az egyéni DNS-kiszolgálót az új IP-címekre.

![Képernyőfelvétel a tárolási célhelyek listájáról. Egy tárolási cél esetén a "..." a jobb szélső oszlopban lévő menü megnyílik, és két lehetőség jelenik meg: delete és refresh DNS.](media/refresh-dns.png)

Ha a művelet sikeres, a frissítés kevesebb, mint két percet vesz igénybe. Egyszerre csak egy tárolási cél frissítése végezhető el. Várjon, amíg az előző művelet befejeződik, mielőtt próbálkozik egy másikkal.

## <a name="update-an-adls-nfs-storage-target-preview"></a>ADLS-NFS tárolási cél frissítése (előzetes verzió)

Az NFS-célokhoz hasonlóan a névtér elérési útja és a ADLS-NFS tárolási célok használati modellje is módosítható.

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS-névtér elérési útjának módosítása

A névtér értékeinek frissítéséhez használja az Azure HPC-gyorsítótár **névter** lapját. A lap részletes ismertetését az [összesített névtér beállítása](add-namespace-paths.md)című cikkben találja.

![képernyőkép a portál névtér oldaláról a jobb oldalon található ADS-NFS-frissítési oldal megnyitásával](media/update-namespace-adls.png)

1. Kattintson a módosítani kívánt elérési út nevére.
1. A szerkesztési ablak használatával beírhatja az új virtuális elérési utat, vagy frissítheti a hozzáférési házirendet.
1. A módosítások elvégzése után kattintson **az OK** gombra a tárolási cél frissítéséhez vagy a **Mégse** gombra a módosítások elvetéséhez.

### <a name="change-adls-nfs-usage-models"></a>ADLS módosítása – NFS-használati modellek

A ADLS-NFS-használati modellek konfigurációja megegyezik az NFS használati modell kiválasztásával. Olvassa el a portálon megjelenő utasításokat a [használati modell módosítása](#change-the-usage-model) a fenti NFS-címen című részben. A ADLS-NFS tárolási célok frissítésének további eszközei fejlesztés alatt állnak.


## <a name="next-steps"></a>Következő lépések

* A beállításokkal kapcsolatos további információkért olvassa el a [tárolási célok hozzáadása](hpc-cache-add-storage.md) című témakört.
* A virtuális elérési utak használatával kapcsolatos további tippekért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című cikkét.
