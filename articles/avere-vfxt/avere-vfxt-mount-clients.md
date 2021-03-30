---
title: A avere-vFXT csatlakoztatása – Azure
description: Megtudhatja, hogyan csatlakoztathatja az ügyfeleket a vFXT-fürthöz az Azure avere-vFXT, és hogyan lehet terheléselosztást létesíteni a fürtcsomópontok között.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272571"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt csatlakoztatása

Kövesse az alábbi lépéseket az ügyfélszámítógépek vFXT-fürthöz való összekapcsolásához.

1. Döntse el, hogyan egyenlítheti ki az ügyfelek forgalmát a fürtcsomópontok között. A részletekért olvassa el az [Egyenleg-ügyfél terhelését](#balance-client-load)alább.
1. Azonosítsa a csatlakoztatni kívánt IP-címet és elágazási útvonalat.
1. Adja ki a [csatlakoztatási parancsot](#mount-command-arguments)a megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Ügyfél terhelésének elosztása

Az ügyfelek kéréseinek a fürt összes csomópontja közötti egyensúlyának biztosításához az ügyfeleket az ügyfél által elérhető IP-címek teljes tartományához kell csatlakoztatnia. A feladat automatizálásának számos egyszerű módja van.

> [!TIP]
> A többi terheléselosztási módszer a nagy vagy bonyolult rendszerek esetében is megfelelő lehet. [Nyisson meg egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) segítségért.)
>
> Ha inkább DNS-kiszolgálót szeretne használni az automatikus kiszolgálóoldali terheléselosztáshoz, be kell állítania és kezelnie kell a saját DNS-kiszolgálóját az Azure-on belül. Ebben az esetben a vFXT-fürt ciklikus multiplexelés DNS-t konfigurálhatja a következő dokumentum szerint: [avere-fürt DNS-konfigurációja](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Kiegyensúlyozott ügyfél-csatlakoztatási parancsfájl

Ez a példa az ügyfél IP-címeit használja véletlenszerű elemként, amellyel az ügyfeleket az összes vFXT-fürt elérhető IP-címeire terjesztheti.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

A fenti függvény a [avere vFXT-példák](https://github.com/Azure/Avere#tutorials) helyén elérhető batch-példa részét képezi.

## <a name="create-the-mount-command"></a>A csatlakoztatási parancs létrehozása

> [!NOTE]
> Ha nem hozott létre új BLOB-tárolót a avere vFXT-fürt létrehozásakor, adja hozzá a Storage-rendszereket a [tároló konfigurálása](avere-vfxt-add-storage.md) az ügyfelek csatlakoztatása előtt című témakörben leírtak szerint.

Az ügyféltől a ``mount`` parancs leképezi a virtuális kiszolgálót (VServer) a vFXT-fürtön a helyi fájlrendszer egyik elérési útjára. A formátum ``mount <vFXT path> <local path> {options}``

A csatlakoztatási parancsnak három eleme van:

* vFXT elérési út – az IP-cím és a névtér csatlakozási útvonalának kombinációja az alábbi fürt 9described
* helyi elérési út – az ügyfél elérési útja
* csatlakoztatási parancs beállításai – a [csatlakoztatási parancs argumentumai](#mount-command-arguments)

### <a name="junction-and-ip"></a>Elágazás és IP

A VServer elérési útja az *IP-címe* és a *névtér-elágazás* elérési útja kombinációja. A névtér-összekapcsolás egy virtuális elérési út, amely a tárolási rendszer hozzáadásakor lett meghatározva.

Ha a fürt blob Storage-sel lett létrehozva, a tárolóhoz tartozó névtér elérési útja `/msazure`

Például: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Ha a fürt létrehozása után hozzáadta a tárolót, a névtér-elágazás elérési útja az elágazás létrehozásakor a **névtér elérési** útja mezőben megadott érték. Ha például ``/avere/files`` a névtér elérési útjaként használta, az ügyfelek csatlakoztatni *IP_address*:/avere/Files a helyi csatlakoztatási ponthoz.

!["Új elágazás hozzáadása" párbeszédpanel a/avere/Files a névtér elérési útja mezőben](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Az IP-cím a VServer definiált ügyféloldali IP-címek egyike. Az ügyféloldali IP-címek tartományát két helyen találja a avere Vezérlőpultján:

* **VServers** -tábla (irányítópult lap) –

  ![A avere Vezérlőpultjának irányítópult lapján a diagram alatti adattáblában kiválasztott VServer lapon, az IP-cím szakaszban pedig a kör alakú](media/avere-vfxt-ip-addresses-dashboard.png)

* **Ügyféloldali hálózati** beállítások lap –

  ![Beállítások > VServer > ügyfél hálózati konfigurációjának lapja, amely egy adott VServer tartozó táblázat címtartomány szakaszának körét tartalmazza](media/avere-vfxt-ip-addresses-settings.png)

Az elérési utakon kívül az egyes ügyfelek csatlakoztatásakor az alább ismertetett [csatlakoztatási parancs argumentumait](#mount-command-arguments) is meg kell adni.

### <a name="mount-command-arguments"></a>Csatlakoztatási parancs argumentumai

A zökkenőmentes ügyfél-csatlakoztatás biztosításához adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Szükséges beállítások | Description |
--- | ---
``hard`` | A vFXT-fürthöz való Soft-csatlakoztatások az alkalmazás hibáival és az esetleges adatvesztéssel vannak társítva.
``proto=netid`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa be ``retry=30`` az átmeneti csatlakoztatási hibák elkerülését. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

## <a name="next-steps"></a>Következő lépések

Miután csatlakoztatta az ügyfeleket, felhasználhatja őket az új blob Storage-tárolóba való másolásához a fürtön. Ha nem kell új tárterületet kitöltenie, olvassa el a további telepítési feladatokkal kapcsolatos további hivatkozásokat:

* [Adatok áthelyezése a cluster Core filerbe](avere-vfxt-data-ingest.md) – a több ügyfél és szál használata az adatok hatékony feltöltéséhez egy új Core Filer-be
* [Fürt testreszabása](avere-vfxt-tuning.md) – a fürt beállításainak személyre szabása a munkaterhelésnek megfelelően
* [A fürt kezelése](avere-vfxt-manage-cluster.md) – a fürt elindítása és leállítása, valamint a csomópontok kezelése
