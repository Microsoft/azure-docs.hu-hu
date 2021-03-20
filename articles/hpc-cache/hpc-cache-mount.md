---
title: Azure HPC-gyorsítótár csatlakoztatása
description: Ügyfelek összekötése az Azure HPC cache szolgáltatással
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651428"
---
# <a name="mount-the-azure-hpc-cache"></a>Az Azure HPC Cache csatlakoztatása

A gyorsítótár létrehozása után az NFS-ügyfelek egy egyszerű parancs használatával férhetnek hozzá `mount` . A parancs egy adott tárolási cél elérési utat csatlakoztat az Azure HPC-gyorsítótárból egy helyi könyvtárba az ügyfélszámítógépen.

A csatlakoztatási parancs az alábbi elemekből áll:

* A gyorsítótár csatlakoztatási címeinek egyike (a gyorsítótár áttekintése oldalon látható)
* A tárolási célhoz beállított virtuális névtér elérési útja (a gyorsítótár-névtér oldalon látható)
* Az ügyfélen használandó helyi elérési út
* Az ilyen típusú NFS-csatlakoztatások sikerességét optimalizáló parancs paraméterei

A gyorsítótár **csatlakoztatási utasítások** lapja összegyűjti az adatokat és az ajánlott beállításokat, és létrehoz egy másolásra szolgáló prototípus-csatlakoztatási parancsot. A részletekért olvassa el [a csatlakoztatási utasítások segédprogramot](#use-the-mount-instructions-utility) .

## <a name="prepare-clients"></a>Ügyfelek előkészítése

Az ebben a szakaszban található irányelvek alapján győződjön meg arról, hogy az ügyfelek csatlakoztatni tudják az Azure HPC-gyorsítótárat.

### <a name="provide-network-access"></a>Hálózati hozzáférés biztosítása

Az ügyfélgépeknek hálózati hozzáféréssel kell rendelkezniük a gyorsítótár virtuális hálózatához és a magánhálózati alhálózathoz.

Hozzon létre például egy virtuális hálózaton belül az ügyfél virtuális gépeket, vagy használjon végpontot, átjárót vagy más megoldást a virtuális hálózaton kívülről való hozzáféréshez. (Ne feledje, hogy a gyorsítótár alhálózatán belül semmi más nem, mint maga a gyorsítótár.)

### <a name="install-utilities"></a>Segédprogramok telepítése

Telepítse a megfelelő Linux-segédprogramot az NFS-csatlakoztatási parancs támogatásához:

* Red Hat Enterprise Linux vagy SuSE esetén: `sudo yum install -y nfs-utils`
* Ubuntu vagy Debian esetén: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Helyi elérési út létrehozása

Hozzon létre egy helyi könyvtár elérési utat az egyes ügyfeleken a gyorsítótárhoz való kapcsolódáshoz. Hozza létre a csatlakoztatni kívánt névtér elérési útját.

Például: `sudo mkdir -p /mnt/hpc-cache-1/target3`

A Azure Portal [csatlakoztatási utasítások](#use-the-mount-instructions-utility) lapja egy olyan prototípus-parancsot tartalmaz, amelyet másolhat.

Amikor az ügyfélszámítógépet a gyorsítótárhoz kapcsolja, társítja ezt az elérési utat egy olyan virtuális névtér elérési útjával, amely a tárolási cél exportálását jelöli. Hozzon létre címtárakat minden olyan virtuális névtér elérési útjához, amelyet az ügyfél használni fog.

## <a name="use-the-mount-instructions-utility"></a>A csatlakoztatási utasítások segédprogram használata

Másolható csatlakoztatási parancs létrehozásához a Azure Portal **csatlakoztatási utasítások** lapja használható. A portálon nyissa meg a lap gyorsítótár nézetének **configure (Konfigurálás** ) szakaszát.

A parancsnak az ügyfélen való használata előtt győződjön meg arról, hogy az ügyfél megfelel az előfeltételeknek, és a szoftver szükséges az NFS `mount` -parancs használatához az [ügyfelek előkészítése](#prepare-clients)című cikkben leírtak szerint.

![képernyőkép egy Azure HPC cache-példányról a portálon, a configure > Mount utasítások lapon betöltve](media/mount-instructions.png)

A csatlakoztatási parancs létrehozásához kövesse az alábbi eljárást.

1. Szabja testre az **ügyfél elérési útja** mezőt. Ez a mező egy példaként szolgáló parancsot biztosít, amellyel helyi elérési utat hozhat létre az ügyfélen. Az ügyfél ezen a címtáron helyileg fér hozzá a tartalomhoz az Azure HPC-gyorsítótárból.

   Kattintson a mezőre, és szerkessze a parancsot, hogy tartalmazza a kívánt könyvtárnév nevét. A név a karakterlánc végén jelenik meg, a következő után: `sudo mkdir -p`

   ![képernyőfelvétel: az ügyfél elérési útja mező a végén található kurzorral](media/mount-edit-client.png)

   Miután befejezte a mező szerkesztését, az oldal alján lévő mount parancs az új ügyfél elérési úttal frissül.

1. Válassza ki a **gyorsítótár-csatlakoztatási címeket** a listából. Ez a menü felsorolja a gyorsítótár összes [ügyfél-csatlakoztatási pontját](#find-mount-command-components).

   Az ügyfelek terhelésének elosztása az összes rendelkezésre álló csatlakoztatási cím között a gyorsítótár jobb teljesítményének növelése érdekében.

   ![képernyőfelvétel a gyorsítótár-csatlakoztatási cím mezőről, amelyben a választó három IP-címet jelenít meg](media/mount-select-ip.png)

1. Válassza ki az ügyfélhez használni kívánt **virtuális névtér elérési útját** . Ezek az elérési utak a háttér-tárolási rendszeren lévő exportálásokra mutató hivatkozással rendelkeznek.

   ![A "virtuális névtér elérési útja" mezőt megjelenítő képernyőkép, a választóval megnyitva.](media/mount-select-target.png)

   A virtuális névtér elérési útjai a **névtér** -portál lapon tekinthetők meg és módosíthatók. Olvassa el [az összesített névtér beállítása](add-namespace-paths.md) című témakört.

   Ha többet szeretne megtudni az Azure HPC cache összesített névtér-szolgáltatásáról, olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md)című témakört.

1. A 3. lépésben szereplő **Mount Command** mező automatikusan feltölti a testreszabott csatlakoztatási paranccsal, amely a csatlakoztatási, a virtuális névtér elérési útját és az előző mezőkben beállított ügyfél elérési útját használja.

   Kattintson a mező jobb oldalán található másolási szimbólumra, hogy automatikusan átmásolja a vágólapra.

   ![képernyőkép a prototípus csatlakoztatási parancs mezőjéről, amely a "másolás a vágólapra" gomb szövegét jeleníti meg](media/mount-command-copy.png)

1. A csatlakoztatott mount parancs használatával csatlakoztassa az ügyfélszámítógépet az Azure HPC-gyorsítótárhoz. A parancsot közvetlenül az ügyfél parancssorából is kiállíthatja, vagy belefoglalhatja a csatlakoztatás parancsot egy ügyfél-telepítési parancsfájlba vagy sablonba.

## <a name="understand-mount-command-syntax"></a>A csatlakoztatási parancs szintaxisának ismertetése

A csatlakoztatási parancs formátuma a következő:

> sudo Mount {*Options*} *cache_mount_address*:/*namespace_path* *local_path*

Példa:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

A parancs sikeres végrehajtása után a tároló exportálásának tartalma látható lesz az ``hpccache`` ügyfél címtárában.

### <a name="mount-command-options"></a>Csatlakoztatási parancs beállításai

Robusztus ügyfél csatlakoztatása esetén adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

> Mount-o Hard, proto = TCP, mountproto = TCP, újrapróbálkozás = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Javasolt csatlakoztatási parancs beállításai | Description |
--- | ---
``hard`` | Az Azure HPC cache-hez való Soft mounts az alkalmazás hibáival és az esetleges adatvesztéssel jár.
``proto=tcp`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=tcp`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=<value>`` | Állítsa be ``retry=30`` az átmeneti csatlakoztatási hibák elkerülését. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

### <a name="find-mount-command-components"></a>Csatlakoztatási parancs összetevőinek keresése

Ha a csatlakoztatási **utasítások** lap használata nélkül szeretné létrehozni a csatlakoztatási parancsot, megkeresheti a csatlakoztatási címeket a gyorsítótár **áttekintése** lapon és a virtuális névtér elérési útjain a **névtér** lapon.

![képernyőkép az Azure HPC cache-példány áttekintő oldaláról, amely a jobb alsó sarokban lévő csatlakoztatási címek listája körüli kiemelési lista](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Egy erőforráscsoport esetében ezek a hálózati adapterek a (z `-cluster-nic-` ) és egy szám végződésű névvel vannak felsorolva. Ne módosítsa vagy törölje ezeket a csatolókat, vagy a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai a gyorsítótár **névtér** -beállítások lapján jelennek meg.

![képernyőkép a portál beállításairól > a névtér oldaláról a tábla első oszlopában található kiemelési mezővel: "névtér elérési útja"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Következő lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el az [új Azure Blob Storage feltöltése](hpc-cache-ingest.md)című cikkét.
