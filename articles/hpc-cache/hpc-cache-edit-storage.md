---
title: Céltárolók Azure HPC Cache frissítése
description: Céltárolók Azure HPC Cache szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862632"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

A céltárolókat eltávolíthatja vagy módosíthatja a Azure Portal vagy az Azure CLI használatával.

A tároló típusától függően a következő célértékeket módosíthatja:

* Blob Storage-célokhoz módosíthatja a névtér elérési útját és a hozzáférési szabályzatot.

* NFS-tárolócélok esetén a következő értékeket módosíthatja:

  * Névtér elérési útjai
  * Hozzáférési szabályzat
  * A névtér elérési úthoz társított storage export vagy export alkönyvtár
  * Használati modell

* Az ADLS-NFS-tárolók esetében módosíthatja a névtér elérési útját, a hozzáférési szabályzatot és a használati modellt.

A tárolási cél neve, típusa vagy háttértárrendszere (Blob-tároló vagy NFS-állomásnév/IP-cím) nem szerkeszthető. Ha módosítania kell ezeket a tulajdonságokat, törölje a tárolóhelyet, és hozzon létre egy új értéket helyettesítő adatokat.

> [!TIP]
> A [Azure HPC Cache kezelése videó bemutatja,](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) hogyan szerkeszthet egy tárolóhelyet a Azure Portal.

## <a name="remove-a-storage-target"></a>Tároló célhelyének eltávolítása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Tároló célhelyének eltávolításához nyissa meg a **Tárolási célok** lapot. Válassza ki a tárolócélt a listából, és kattintson **a Törlés gombra.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

A [tároló célhelyét az az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) parancs használatával törölheti a gyorsítótárból.

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

A tárolóhely törlése eltávolítja a tárolórendszer és a Azure HPC Cache társítását, de nem módosítja a háttértárrendszert. Ha például Egy Azure Blob Storage-tárolót használt, a tároló és annak tartalma továbbra is létezik, miután törölte azt a gyorsítótárból. A tárolót hozzáadhatja egy másik Azure HPC Cache, újra hozzáadhatja ehhez a gyorsítótárhoz, vagy törölheti a Azure Portal.

A rendszer a gyorsítótárban tárolt fájlváltozásokat a háttértárrendszerbe írja a tároló célhelyének eltávolítása előtt. Ha sok módosított adat található a gyorsítótárban, ez a folyamat akár egy órát vagy többet is el is vehet.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Blob Storage-cél névtér elérési útjának módosítása

A névtér elérési útjai azok az elérési utak, amelyek használatával az ügyfelek csatlakoztatják ezt a tárolási célt. (További információ: Az összesített névtér [megterve és](hpc-cache-namespace.md) Az összesített névtér [beállítása).](add-namespace-paths.md)

A névtér elérési útja az egyetlen frissítés, amely egy Azure Blob Storage-célon frissítve lesz. A Azure Portal vagy az Azure CLI használatával módosíthatja azt.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Használja a **névtér** lapját a Azure HPC Cache. A névtér lapját részletesebben az Összesített névtér beállítása cikkben [ismertetjük.](add-namespace-paths.md)

Kattintson a módosítani kívánt elérési út nevére, és hozza létre az új elérési utat a megjelenő szerkesztési ablakban.

![Képernyőkép a névtér oldalról, miután a blobnévtér elérési útjára kattintott – a szerkesztési mezők a jobb oldali panelen jelennek meg](media/update-namespace-blob.png)

A módosításokat követően kattintson az **OK gombra** a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Ha módosítania kell egy Blob Storage-cél névterét az Azure CLI-vel, használja az [az hpc-cache blob-storage-target update parancsot.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Csak `--virtual-namespace-path` az érték módosítható.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS-tárolóhely frissítése

NfS-tárolócélok esetén módosíthatja vagy hozzáadhatja a virtuális névtér elérési útját, módosíthatja az NFS exportálási vagy alkönyvtárértékeket, amelyekre a névtér elérési útja mutat, és módosíthatja a használati modellt.

Bizonyos típusú egyéni DNS-beállításokkal a gyorsítótárak tárolócéljai az IP-címük frissítésére is lehetőséget kínálnak. (Ez a konfiguráció ritka.)

A részletek alább olvashatók:

* [Összesített névtérértékek módosítása](#change-aggregated-namespace-values) (virtuális névtér elérési útja, hozzáférési szabályzat, exportálás és exportálás alkönyvtára)
* [A használati modell módosítása](#change-the-usage-model)
* [DNS frissítése](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Összesített névtérértékek módosítása

Az ügyféloldali névtér Azure Portal vagy az Azure CLI használatával módosíthatja az ügyféloldali névtér elérési útját, a tároló exportálását és az exportálás alkönyvtárát (ha van).

Olvassa el [](add-namespace-paths.md#nfs-namespace-paths) az NFS-névterek elérési útjának hozzáadása útmutatót, ha emlékeztetőt szeretne arról, hogyan hozhat létre több érvényes elérési utat egy tárolóhelyen.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A **névtérértékek frissítéséhez** használja a Azure HPC Cache lapját. Ezt az oldalt részletesebben az Összesített névtér beállítása cikkben [ismertetjük.](add-namespace-paths.md)

![képernyőkép a portál névtéroldalról, jobb oldalt megnyitva az NFS frissítési oldalával](media/update-namespace-nfs.png)

1. Kattintson a módosítani kívánt elérési út nevére.
1. A szerkesztési ablakban begépelheti az új virtuális elérési utat, az exportálást vagy az alkönyvtár értékeit, vagy választhat egy másik hozzáférési szabályzatot.
1. A módosításokat követően kattintson az **OK** gombra a tárolási cél frissítéséhez, vagy a **Mégse** gombra a módosítások elvetéhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az ``--junction`` [az hpc-cache nfs-storage-target update parancs](/cli/azure/hpc-cache/nfs-storage-target) kapcsolóját használva módosíthatja a névtér elérési útját, az NFS-exportálást vagy az export alkönyvtárat.

A ``--junction`` paraméter a következő értékeket használja:

* ``namespace-path`` – Az ügyféloldali virtuális fájl elérési útja
* ``nfs-export`` - Az ügyféloldali elérési úthoz társítható tárolórendszer exportálása
* ``target-path`` (nem kötelező) – Az exportálás alkönyvtára, ha szükséges

Például: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

A utasításban minden elérési úthoz mindhárom értéket meg kell ``--junction`` adnunk. A meglévő értékeket minden olyan értékhez használhatja, amit nem szeretne módosítani.

A gyorsítótár neve, a tároló célneve és az erőforráscsoport is szükséges az összes frissítési parancsban.

Példaparancs:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>A használati modell módosítása

A használati modell befolyásolja, hogy a gyorsítótár hogyan őrzi meg az adatokat. További [információ: A gyorsítótár-használati modellek](cache-usage-models.md) megismerése.

> [!NOTE]
> Ha módosítja a használati modelleket, előfordulhat, hogy újra kell csatolnia az ügyfeleket az NLM-hibák elkerülése érdekében. További [információ: Know when to remount clients](cache-usage-models.md#know-when-to-remount-clients-for-nlm) (Tudja meg, mikor kell újra leválasztani az ügyfeleket).

Az NFS-tárolóhely használati modelljének a módosítása az alábbi módszerek egyikével létezik.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Módosítsa a használati  modellt a tárterület céloldalán Azure Portal. Kattintson a módosítania kell a tároló célhelyének nevére.

![az NFS-tároló célhely szerkesztési oldalának képernyőképe](media/edit-storage-nfs.png)

Új használati modell kiválasztásához használja a legördülő választót. Kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Használja az [az hpc-cache nfs-storage-target update parancsot.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)

A frissítési parancs majdnem teljesen megegyezik az NFS-tárolóhely hozzáadásához használt paranccsal. További részletekért és példákért tekintse meg [az NFS-tároló célhelyének](hpc-cache-add-storage.md#create-an-nfs-storage-target) létrehozása.

A használati modell beállításának ``--nfs3-usage-model`` módosítása. Például: ``--nfs3-usage-model WRITE_WORKLOAD_15``

A gyorsítótár neve, a tároló célneve és az erőforráscsoport értékei is szükségesek.

Ha ellenőrizni szeretné a használati modellek nevét, használja az [az hpc-cache usage-model list parancsot.](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list)

Ha a gyorsítótár le van állítva vagy nem kifogástalan állapotban van, a frissítés a gyorsítótár kifogástalan állapota után lesz alkalmazva.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP-cím frissítése (csak egyéni DNS-konfigurációk esetén)

Ha a gyorsítótár nem alapértelmezett DNS-konfigurációt használ, az NFS-tároló céljának IP-címe megváltozhat a háttérbeli DNS-módosítások miatt. Ha a DNS-kiszolgáló módosítja a háttértárrendszer IP-címét, a Azure HPC Cache hozzáférhet a tárolórendszerhez.

Ideális esetben érdemes a gyorsítótár egyéni DNS-rendszerének kezelőjével együtt tervezni a frissítéseket, mivel ezek a módosítások elérhetetlenné teszik a tárterületet.

Ha frissítenie kell egy tárolási cél DNS által megadott IP-címét, a Tárolócélok listában megjelenik egy gomb. Kattintson **a DNS frissítése** elemre az egyéni DNS-kiszolgáló új IP-címének lekérdezéséhez.

![A tárolási céllista képernyőképe. Egy tárolóhelyen a "..." A jobb oldali oszlopban lévő menü meg van nyitva, és két lehetőség jelenik meg: Delete (Törlés) és Refresh DNS (DNS frissítése).](media/refresh-dns.png)

Ha a frissítés sikeres, a frissítés kevesebb mint két percet fog igénybe venni. Egyszerre csak egy céltárolót frissíthet; várjon, amíg az előző művelet befejeződik, mielőtt próbálkozik egy másikkal.

## <a name="update-an-adls-nfs-storage-target-preview"></a>ADLS-NFS tárolási cél frissítése (ELŐZETES VERZIÓ)

Az NFS-célokhoz hasonlóan módosíthatja az ADLS-NFS-tárolók névtérútvonalát és használati modelljét.

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS-névtér elérési útjának módosítása

A **névtérértékek frissítéséhez** használja a Azure HPC Cache lapját. Ezt az oldalt részletesebben az Összesített névtér beállítása cikkben [ismertetjük.](add-namespace-paths.md)

![képernyőkép a portál névtéroldalról, jobb oldalt megnyitott ADS-NFS frissítési oldallal](media/update-namespace-adls.png)

1. Kattintson a módosítani kívánt elérési út nevére.
1. A szerkesztési ablakban begépelheti az új virtuális útvonalat, vagy frissítheti a hozzáférési szabályzatot.
1. A módosításokat követően kattintson az **OK gombra** a tároló célhelyének frissítéséhez, vagy a **Mégse** gombra a módosítások elvetéséhez.

### <a name="change-adls-nfs-usage-models"></a>Az ADLS-NFS használati modellek módosítása

Az ADLS-NFS használati modellek konfigurációja megegyezik az NFS-használati modell kiválasztásával. Olvassa el a fenti NFS-szakaszban [található Használati](#change-the-usage-model) modell módosítása című szakaszban található portálra vonatkozó utasításokat. Az ADLS-NFS-tárolócélok frissítéséhez további eszközök is fejlesztés alatt állnak.


## <a name="next-steps"></a>Következő lépések

* További [információ ezekről](hpc-cache-add-storage.md) a lehetőségekről: Tárolócélok hozzáadása.
* A [virtuális elérési utak használatával](hpc-cache-namespace.md) kapcsolatos további tippekért olvassa el Az összesített névtér megterve.
