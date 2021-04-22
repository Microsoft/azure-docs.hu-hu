---
title: Az Azure HPC Cache névtér konfigurálása
description: Ügyféloldali elérési utak létrehozása háttértárhelyhez a Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870372"
---
# <a name="set-up-the-aggregated-namespace"></a>Az aggregált névtér beállítása

A céltárolók létrehozása után névtérútvonalat is létre kell hoznia hozzájuk. Az ügyfélgépek ezeket a virtuális elérési utakat használják a fájlok gyorsítótáron keresztüli eléréséhez ahelyett, hogy közvetlenül a háttértárhoz csatlakoznak. Ez a rendszer lehetővé teszi, hogy a gyorsítótár-rendszergazdák anélkül módosítják a háttértárrendszereket, hogy átírják az ügyfél utasításait.

A funkcióval kapcsolatos további [információkért olvassa](hpc-cache-namespace.md) el az Aggregált névtér megterve lehetőséget.

A **lap Névtér** Azure Portal megjeleníti azokat az elérési utakat, amelyek használatával az ügyfelek a gyorsítótáron keresztül férnek hozzá az adatokhoz. Ezen az oldalon névtér elérési útjait hozhatja létre, távolíthatja el vagy módosíthatja. A névtér elérési útjait az Azure CLI használatával is konfigurálhatja.

A gyorsítótárhoz definiált összes ügyféloldali elérési út megjelenik a **Névtér** lapon. Az olyan tárolócélok, amelyek még nem határoznak meg névtérútvonalat, nem jelennek meg a táblában.

Rendezheti a tábla oszlopait, hogy jobban át tudja érteni a gyorsítótár összesített névterét. Az elérési utak rendezéséhez kattintson az oszlopfejlécek nyilára.

[![képernyőkép a portál névtéroldalának két elérési útról egy táblázatban. Oszlopfejlécek: Névtér elérési útja, Tárolási cél, Elérési út exportálása, Exportálás alkönyvtára és Ügyfél-hozzáférési szabályzat. Az első oszlopban található elérési utak nevei kattintható hivatkozások. Felső gombok: Névtér elérési útjának hozzáadása, frissítés, törlés ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Névtér elérési útjának hozzáadása vagy szerkesztése

Létre kell hoznia legalább egy névtérútvonalat, mielőtt az ügyfelek hozzáférhetnek a tároló célhelyhez. (További [információ az ügyfél-Azure HPC Cache](hpc-cache-mount.md) csatlakoztatását.)

Ha a közelmúltban hozzáadott egy tárolóhelyet vagy testre szabott egy hozzáférési szabályzatot, a névtér elérési útjának létrehozása egy-két percet is igénybe vehet.

### <a name="blob-namespace-paths"></a>Blob névterének elérési útjai

Az Azure Blob Storage-célhely csak egy névtérútvonalral lehet.

Az elérési út beállításhoz vagy módosításhoz kövesse az alábbi utasításokat a Azure Portal vagy az Azure CLI használatával.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal töltse be a **Névtér** beállításai lapot. Ezen az oldalon hozzáadhat, módosíthat vagy törölhet névtérútvonalat.

* **Adjon hozzá egy új elérési utat:** Kattintson felül **a +** Hozzáadás gombra, és töltse ki az adatokat a szerkesztési panelen.

  ![Képernyőkép a névtérszerkesztés mezőinek hozzáadásáról egy kijelölt blobtároló cél használatával. Az exportálás és az alkönyvtár elérési útjai /- és nem szerkeszthetők.](media/namespace-add-blob.png)

  * Adja meg azt az elérési utat, amit az ügyfelek a tárolási cél eléréséhez használni fognak.

  * Válassza ki az elérési úthoz használni kívánt hozzáférési szabályzatot. További információ az ügyfél-hozzáférés testreszabásáról: [Ügyfél-hozzáférési házirendek használata.](access-policies.md)

  * Válassza ki a tárolócélt a legördülő listából. Ha egy blobtároló cél már rendelkezik névtérútvonalval, nem választható ki.

  * Azure Blob Storage-cél esetén az exportálás és az alkönyvtár elérési útjai automatikusan a következőre vannak beállítva: ``/`` .

* **Meglévő elérési út módosítása:** Kattintson a névtér elérési útjára. Megnyílik a Szerkesztés panel. Módosíthatja az elérési utat és a hozzáférési szabályzatot, de nem módosíthatja másik tárolási célhelyre.

* **Névtér elérési útjának törlése:** Jelölje be az elérési úttól balra található jelölőnégyzetet, és kattintson a **Törlés gombra.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az Azure CLI használata esetén hozzá kell adni egy névtér elérési útját a tárolási cél létrehozásakor. További [információ: Új Azure Blob Storage-cél](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) hozzáadása.

A cél névtérútvonalának frissítéséhez használja [az az hpc-cache blob-storage-target update parancsot.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) A frissítési parancs argumentumai hasonlóak a create parancs argumentumaihoz, azzal a kivételsel, hogy nem adja át a tároló nevét vagy a tárfiókot.

Az Azure CLI használatával nem törölhet névtérútvonalat a blobtároló célhelyről, de az elérési utat felülírhatja egy másik értékkel.

---

### <a name="nfs-namespace-paths"></a>NFS-névtér elérési útjai

Egy NFS-tároló célhelyének több virtuális elérési útja is lehet, ha minden elérési út egy adott tárolórendszer különböző exportálási vagy alkönyvtárát jelöli.

Az NFS-tárolóhely névterének tervezésekor ne feledje, hogy minden elérési útnak egyedinek kell lennie, és nem lehet más névtér elérési útjának alkönyvtára. Ha például van egy nevű névtérútvonala, nem hozhat létre olyan névtér-elérési utakat, mint a ``/parent-a`` ``/parent-a/user1`` és a ``/parent-a/user2`` . Ezek a könyvtárútvonalak már elérhetők a névtérben a alkönyvtáraiként. ``/parent-a``

Egy NFS-tárolórendszer összes névtér-elérési útja egyetlen tárolóhelyen jön létre. A legtöbb gyorsítótár-konfiguráció legfeljebb tíz névtérútvonalat támogat tárolócélonként, de a nagyobb konfigurációk legfeljebb 20-ast támogatnak.

Ez a lista a névtér elérési útjának konfigurációnkénti maximális számát mutatja.

* Legfeljebb 2 GB/s átviteli sebesség:

  * 3 TB-os gyorsítótár – 10 névtérútvonal
  * 6 TB-os gyorsítótár – 10 névtérútvonal
  * 12 TB-os gyorsítótár – 20 névtérútvonal

* Legfeljebb 4 GB/s átviteli sebesség:

  * 6 TB-os gyorsítótár – 10 névtérútvonal
  * 12 TB-os gyorsítótár – 10 névtérútvonal
  * 24 TB gyorsítótár –20 névtér elérési útja

* Legfeljebb 8 GB/s átviteli sebesség:

  * 12 TB gyorsítótár – 10 névtérútvonal
  * 24 TB gyorsítótár – 10 névtérútvonal
  * 48 TB-os gyorsítótár – 20 névtérútvonal

Minden NFS-névtér elérési útjaként adja meg az ügyféloldali elérési utat, a tárolórendszer exportálását és opcionálisan egy exportálási alkönyvtárat.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal töltse be a **Névtér** beállításai lapot. Ezen a lapon adhat hozzá, szerkeszthet vagy törölhet névtérútvonalat.

* **Új elérési út hozzáadása:** Kattintson felül **a +** Hozzáadás gombra, és töltse ki az adatokat a szerkesztési panelen.
* **Meglévő elérési út módosítása:** Kattintson a névtér elérési útjára. Megnyílik a szerkesztési panel, és módosíthatja az elérési utat.
* **Névtér elérési útjának törlése:** Jelölje be az elérési úttól balra található jelölőnégyzetet, és kattintson a **Törlés gombra.**

Adja meg az alábbi értékeket az egyes névterek elérési útjaihoz:

* **Névtér elérési útja** – Az ügyféloldali fájl elérési útja.

* **Ügyfél-hozzáférési házirend** – Válassza ki az elérési úthoz használni kívánt hozzáférési szabályzatot. További információ az ügyfél-hozzáférés testreszabásáról: [Ügyfél-hozzáférési házirendek használata.](access-policies.md)

* **Tárolási cél** – Új névtér elérési útjának létrehozásakor válasszon ki egy tárolási célt a legördülő menüből.

* **Exportálási útvonal** – Adja meg az NFS-exportálás elérési útját. Ügyeljen rá, hogy helyesen írja be az exportálás nevét – a portál érvényesíti a mező szintaxisát, de a módosítás elküldése előtt nem ellenőrzi az exportálást.

* **Alkönyvtár exportálása** – Ha azt szeretné, hogy ez az elérési út az exportálás egy adott alkönyvtárát csatlakoztassa, itt adhatja meg. Ha nem, hagyja üresen ezt a mezőt.

![képernyőkép a portál névtéroldalról, jobb oldalt megnyitva a szerkesztési oldallal. A szerkesztési űrlapon egy nfs-tároló célnévterének elérési útja látható](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az Azure CLI használata esetén legalább egy névtérútvonalat hozzá kell adni a tároló célhelyének létrehozásakor. További [információ: Új NFS-tároló hozzáadása.](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target)

A cél névtérútvonalának frissítéséhez vagy további elérési utak hozzáadásához használja [az az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) parancsot. A ``--junction`` kapcsolóval adhatja meg az összes kívánt névtérútvonalat.

A frissítési parancshoz használt beállítások hasonlóak a "create" parancshoz, azzal a kivételel, hogy nem adja meg a tárolórendszer adatait (IP-cím vagy állomásnév), és a használati modell megadása nem kötelező. A beállítás szintaxisával kapcsolatos további részletekért olvassa el [az Új NFS-tároló](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) hozzáadása ``--junction`` lehetőséget.

---

### <a name="adls-nfs-namespace-paths-preview"></a>ADLS-NFS-névtér elérési útjai (ELŐZETES VERZIÓ)

A normál Blob Storage-célhoz hasonló az ADLS-NFS tárolási cél csak egy exportálással rendelkezik, ezért csak egy névtérútvonala lehet.

Kövesse az alábbi utasításokat az elérési út beállításhoz vagy az elérési út Azure Portal.

Töltse be **a Névtér beállításai** lapot.

* **Adjon hozzá egy új elérési utat:** Kattintson felül **a +** Hozzáadás gombra, és töltse ki az adatokat a szerkesztési panelen.

  ![Képernyőkép a névtérszerkesztés mezőinek hozzáadásáról, az ADLS-NFS célhely kijelölve. Az exportálási és az alkönyvtár elérési útjai /-be vannak állítva, és nem szerkeszthetők.](media/namespace-add-adls.png)

  * Adja meg, hogy az ügyfelek mely elérési utat használják a tárolási cél eléréséhez.

  * Válassza ki az elérési úthoz használni kívánt hozzáférési szabályzatot. További információ az ügyfél-hozzáférés testreszabásáról: [Ügyfél-hozzáférési házirendek használata.](access-policies.md)

  * Válassza ki a tárolócélt a legördülő listából. Ha az ADLS-NFS tárolási cél már rendelkezik névtérútvonalval, nem választható ki.

  * ADLS-NFS tárolási cél esetén az exportálás és az alkönyvtár elérési útjai automatikusan a következőre vannak beállítva: ``/`` .

* **Meglévő elérési út módosítása:** Kattintson a névtér elérési útjára. Megnyílik a Szerkesztés panel. Módosíthatja az elérési utat és a hozzáférési szabályzatot, de nem módosíthatja másik tárolási célhelyre.

* **Névtér elérési útjának törlése:** Jelölje be az elérési úttól balra található jelölőnégyzetet, és kattintson a **Törlés gombra.**

## <a name="next-steps"></a>Következő lépések

Miután létrehozta az összesített névteret a tárolási célokhoz, ügyfeleket csatlakoztathat a gyorsítótárhoz. További információért olvassa el ezeket a cikkeket.

* [Az Azure HPC Cache csatlakoztatása](hpc-cache-mount.md)
* [Adatok áthelyezése az Azure Blob Storage-ba](hpc-cache-ingest.md)
