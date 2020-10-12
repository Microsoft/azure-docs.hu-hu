---
title: Az Azure HPC-gyorsítótár összesített névterének konfigurálása
description: Ügyfelek felé irányuló elérési utak létrehozása a háttérbeli tároláshoz az Azure HPC cache használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614580"
---
# <a name="set-up-the-aggregated-namespace"></a>Az összesített névtér beállítása

Miután létrehozta a tárolási célokat, létre kell hoznia a névtér elérési útját is. Az ügyfélszámítógépek ezeket a virtuális útvonalakat használják a fájloknak a gyorsítótárból való elérésére a háttérbeli tárolóhoz való közvetlen csatlakozás helyett. Ez a rendszer lehetővé teszi a gyorsítótár-rendszergazdák számára a háttérbeli tárolási rendszerek módosítását anélkül, hogy újra kellene írnia az ügyfél utasításait.

A szolgáltatással kapcsolatos további információkért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című témakört.

A Azure Portal **névtér** lapja megjeleníti azokat az elérési utakat, amelyeket az ügyfelek a gyorsítótáron keresztül férnek hozzá az adataihoz. Ezen a lapon lehet létrehozni, eltávolítani vagy módosítani a névtér elérési útját. A névtér elérési útját az Azure CLI használatával is konfigurálhatja.

Az összes meglévő ügyfél-elérési útvonal megjelenik a **névtér** lapon. Ha egy tárolási cél nem rendelkezik elérési úttal, nem jelenik meg a táblázatban.

A táblázat oszlopainak rendezéséhez kattintson a nyilakra, és jobban megismerheti a gyorsítótár összesített névterét.

![képernyőkép a portál névteréről a tábla két útvonalával. Oszlopfejlécek: névtér elérési útja, tárolási cél, exportálási útvonal és alkönyvtár exportálása. Az első oszlop elemei kattintható hivatkozások. Leggyakoribb gombok: névtér elérési útjának hozzáadása, frissítés, törlés](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Ügyféloldali névtér elérési útjának hozzáadása vagy szerkesztése

Legalább egy névtér elérési útját létre kell hoznia ahhoz, hogy az ügyfelek hozzáférhessenek a tárolási célhoz. (Az ügyfelek hozzáféréséről [Az Azure HPC cache csatlakoztatása](hpc-cache-mount.md) című cikkből tájékozódhat.)

### <a name="blob-namespace-paths"></a>BLOB-névtér elérési útjai

Az Azure Blob Storage-tárolóhoz csak egy névtér elérési útja tartozhat.

Az alábbi útmutatást követve állíthatja be vagy módosíthatja az elérési utat a Azure Portal vagy az Azure CLI használatával.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal töltse be a **névtér** -beállítások lapot. Ezen a lapon adhatja hozzá, módosíthatja vagy törölheti a névtér elérési útját.

* **Új elérési út hozzáadása:** Kattintson a felül található **+ Hozzáadás** gombra, és adja meg az adatokat a szerkesztési panelen.

  * Válassza ki a tárolási célt a legördülő listából. (Ebben a képernyőfelvételben nem lehet kiválasztani a blob Storage-célt, mert már van névtérbeli elérési útja.)

    ![Képernyőkép az új névtér-szerkesztési mezőkről a tárolók kiválasztásával](media/namespace-select-storage-target.png)

  * Az Azure Blob Storage-tárolók esetében a rendszer automatikusan beállítja az exportálási és alkönyvtári elérési utakat ``/`` .

* **Meglévő elérési út módosítása:** Kattintson a névtér elérési útjára. Megnyílik a szerkesztési panel, és módosíthatja az elérési utat.

  ![Képernyőkép a névtér oldaláról a blob-névtér elérési útjára való kattintás után – a szerkesztési mezők a jobb oldali ablaktáblán jelennek meg.](media/edit-namespace-blob.png)

* **Névtér elérési útjának törlése:** Jelölje be az elérési út bal oldalán található jelölőnégyzetet, majd kattintson a **Törlés** gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Az Azure CLI használatakor a tárolási cél létrehozásakor hozzá kell adnia egy névtér elérési útját. További részletekért olvassa el az [új Azure Blob Storage-tároló hozzáadása](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) című leírást.

A cél névtérbeli elérési útjának frissítéséhez használja az az [HPC-cache blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) parancsot. A frissítési parancs argumentumai hasonlóak a Create parancs argumentumai, azzal a különbséggel, hogy nem adja át a tároló nevét vagy a Storage-fiókot.

A névtér elérési útja nem törölhető egy blob Storage-tárolóból az Azure CLI-vel, de az elérési utat más értékkel is felülírhatja.

---

### <a name="nfs-namespace-paths"></a>NFS-névtér elérési útjai

Egy NFS-tárolási cél több virtuális útvonallal is rendelkezhet, ha az egyes elérési utak ugyanazon a tárolási rendszeren eltérő exportálási vagy alkönyvtárat jelölnek.

Amikor megtervezi a névteret egy NFS-tárolási cél számára, ne feledje, hogy minden elérési útnak egyedinek kell lennie, és nem lehet más névtérbeli elérési út alkönyvtára. Ha például névtérbeli elérési úttal rendelkezik, a és a névtér elérési útja ``/parent-a`` nem hozható létre ``/parent-a/user1`` ``/parent-a/user2`` . Ezek a címtár-elérési utak már elérhetők a névtérben a () alkönyvtáraként ``/parent-a`` .

Egy NFS-tárolási rendszer összes névtér-elérési útja egyetlen tárolási célra van létrehozva. A gyorsítótár-konfigurációk többsége legfeljebb tíz névtér-elérési utat tud támogatni tárolási célra, de a nagyobb konfigurációk akár 20-ot is támogatnak.

Ez a lista a névtérbeli elérési utak maximális számát jeleníti meg konfiguráció alapján.

* Legfeljebb 2 GB/s átviteli sebesség:

  * 3 TB gyorsítótár – 10 névtér elérési útja
  * 6 TB gyorsítótár – 10 névtér elérési útja
  * 23 TB gyorsítótár – 20 névtér elérési útja

* Legfeljebb 5 GB/s átviteli sebesség:

  * 6 TB gyorsítótár – 10 névtér elérési útja
  * 12 TB gyorsítótár – 10 névtér elérési útja
  * 24 TB gyorsítótár – 20 névtér elérési útja

* Legfeljebb 8 GB/s átviteli sebesség:

  * 12 TB gyorsítótár – 10 névtér elérési útja
  * 24 TB gyorsítótár – 10 névtér elérési útja
  * 48 TB gyorsítótár – 20 névtér elérési útja

Minden NFS-névtér elérési útján adja meg az ügyfél felé irányuló elérési utat, a tárolási rendszer exportálását és opcionálisan az Exportálás alkönyvtárát.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal töltse be a **névtér** -beállítások lapot. Ezen a lapon adhatja hozzá, szerkesztheti vagy törölheti a névtér elérési útját.

* **Új elérési út hozzáadása:** Kattintson a felül található **+ Hozzáadás** gombra, és adja meg az adatokat a szerkesztési panelen.
* **Meglévő elérési út módosítása:** Kattintson a névtér elérési útjára. Megnyílik a szerkesztési panel, és módosíthatja az elérési utat.
* **Névtér elérési útjának törlése:** Jelölje be az elérési út bal oldalán található jelölőnégyzetet, majd kattintson a **Törlés** gombra.

Adja meg ezeket az értékeket az egyes névterek elérési útjához:

* **Névtér elérési útja** – az ügyfél felé irányuló fájl elérési útja.

* **Tárolási cél** – új névtér elérési útjának létrehozásakor válasszon ki egy tárolási célt a legördülő menüből.

* **Exportálási útvonal** – adja meg az NFS-exportálás elérési útját. Ügyeljen arra, hogy helyesen írja be az Exportálás nevét – a portál érvényesíti a mező szintaxisát, de nem ellenőrzi az exportálást, amíg el nem küldi a módosítást.

* **Alkönyvtár exportálása** – ha azt szeretné, hogy ez az elérési út egy adott alkönyvtárat csatoljon az exportáláshoz, adja meg a következőt:. Ha nem, hagyja üresen ezt a mezőt.

![képernyőkép a portál névtér oldaláról a jobb oldalon megnyíló frissítés oldalon](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Az Azure CLI használatakor hozzá kell adnia legalább egy névtér elérési útját a tárolási cél létrehozásakor. További részletekért olvassa el az [új NFS-tárolási cél hozzáadása](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) című leírást.

A cél névtérbeli elérési útjának frissítéséhez vagy további elérési utak hozzáadásához használja az az [HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) parancsot. A ``--junction`` lehetőséggel megadhatja az összes használni kívánt névtér-elérési utat.

A frissítési parancshoz használt beállítások hasonlóak a "Create" parancshoz, azzal a különbséggel, hogy nem adja át a tárolási rendszer adatait (IP-cím vagy állomásnév), és a használati modell nem kötelező. A beállítás szintaxisával kapcsolatos további információkért olvassa el az [új NFS-tárolási cél hozzáadása](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) címűt ``--junction`` .

---

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a tárolási célok összesített névterét, csatlakoztathatja az ügyfeleket a gyorsítótárhoz. További információért olvassa el ezeket a cikkeket.

* [Az Azure HPC Cache csatlakoztatása](hpc-cache-mount.md)
* [Az Azure Blob Storage-ba irányuló adatáthelyezés](hpc-cache-ingest.md)
