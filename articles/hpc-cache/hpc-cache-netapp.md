---
title: Az Azure HPC cache és a Azure NetApp Files használata
description: Az Azure HPC cache használata a Azure NetApp Filessal tárolt adathozzáférések javítására
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86497012"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Az Azure HPC cache használata Azure NetApp Files

Az Azure HPC-gyorsítótárhoz [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) is használhat tárolási célként. Ez a cikk azt ismerteti, hogy a két szolgáltatás hogyan működhet együtt, és tippeket ad a beállításához.

Azure NetApp Files a Microsoft Azure méretezhetőségével és sebességével ötvözi a ONTAP operációs rendszert. Ez a kombináció lehetővé teszi, hogy a felhasználók a kód újraírása nélkül váltsanak munkafolyamatokat a felhőbe.

Egy Azure HPC cache-összetevő hozzáadásával javíthatja a fájlok elérését azáltal, hogy több Azure NetApp Files kötetet mutat be egy összesített névtérben. Megadhatja a peremhálózati gyorsítótárazást a különböző szolgáltatási régiókban található kötetek esetében. Emellett az alacsonyabb szintű szolgáltatási szinteken létrehozott kötetek igény szerinti teljesítményének növelésével növelheti a költségeket.

## <a name="overview"></a>Áttekintés

Ha egy Azure NetApp Files rendszer használatát háttérbeli tárolóként szeretné használni az Azure HPC cache használatával, kövesse ezt a folyamatot.

1. Hozza létre a Azure NetApp Files-rendszerfolyamatot és-köteteket a [séma megtervezése című](#plan-your-azure-netapp-files-system)témakör útmutatása alapján.
1. Hozza létre az Azure HPC-gyorsítótárat abban a régióban, ahol fájl-hozzáférésre van szüksége. (Használja az [Azure HPC cache létrehozása](hpc-cache-create.md)című témakör utasításait.)
1. [Adja meg a tárolási célokat](#create-storage-targets-in-the-cache) a gyorsítótárban, amely a Azure NetApp Files kötetekre mutat. Hozzon létre egy gyorsítótár-tárolási célt a kötetek eléréséhez használt egyes egyedi IP-címekhez.
1. A Azure NetApp Files kötetek közvetlen csatlakoztatása helyett [az ügyfelek csatlakoztatják az Azure HPC-gyorsítótárat](#mount-storage-targets) .

## <a name="plan-your-azure-netapp-files-system"></a>A Azure NetApp Files-rendszerek megtervezése

A Azure NetApp Filesrendszer megtervezésekor ügyeljen arra, hogy az ebben a szakaszban szereplő elemekkel zökkenőmentesen integrálható legyen az Azure HPC cache használatával.

A kötetek Azure HPC cache-vel való használatához való létrehozása előtt olvassa el a [Azure NetApp Files dokumentációját](../azure-netapp-files/index.yml) is.

### <a name="nfs-client-access-only"></a>Csak NFS-ügyfél-hozzáférés

Az Azure HPC cache jelenleg csak az NFS-hozzáférést támogatja. Nem használható SMB ACL-sel vagy POSIX módú bites kötetekkel.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files kizárólagos alhálózata

Azure NetApp Files egyetlen delegált alhálózatot használ a köteteihez. Más erőforrások nem használhatják ezt az alhálózatot. Emellett a virtuális hálózatokban csak egy alhálózat használható Azure NetApp Fileshoz. További információ: [Azure NetApp Files hálózati tervezéssel kapcsolatos irányelvek](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Delegált alhálózat mérete

Az Azure HPC cache-vel való használatra Azure NetApp Files rendszer létrehozásakor használja a delegált alhálózat minimális méretét.

A hálózati maszk/28 értékkel megadott minimális méret 16 IP-címet biztosít. A gyakorlatban Azure NetApp Files a mennyiségi hozzáféréshez csak három elérhető IP-címet használ. Ez azt jelenti, hogy az Azure HPC-gyorsítótárban csak három tárolási célt kell létrehoznia, hogy az összes kötetre vonatkozzon.

Ha a delegált alhálózat túl nagy, lehetséges, hogy a Azure NetApp Files kötetek több IP-címet is használhatnak, mint amennyit egyetlen Azure HPC cache-példány képes kezelni. Egyetlen gyorsítótár legfeljebb 10 tárolási célt tartalmazhat.

Az Azure NetApp Files dokumentációjának rövid útmutatója a 10.7.0.0/16 protokollt használja a delegált alhálózathoz, ami túl nagy méretű alhálózatot biztosít.

### <a name="capacity-pool-service-level"></a>Kapacitási készlet szolgáltatási szintje

A kapacitási készlet szolgáltatási szintjének kiválasztásakor vegye figyelembe a munkafolyamatot. Ha gyakran ír vissza adatmennyiséget a Azure NetApp Files kötetre, akkor a gyorsítótár teljesítménye korlátozható, ha a visszaírási idő lassú. Válasszon magas szolgáltatási szintet a gyakori írási értékekkel rendelkező kötetek számára.

Az alacsony szolgáltatási szinttel rendelkező kötetek a feladatok elején bizonyos késéseket is jelezhetnek, miközben a gyorsítótár előre kitölti a tartalmat. Ha a gyorsítótár megfelelően működik, és a fájlok megfelelő munkakészlete fut, a késésnek észrevehetetlen kell lennie.

Fontos, hogy előre megtervezze a kapacitási készlet szolgáltatási szintjét, mert azt a létrehozás után nem lehet módosítani. Új kötetet kell létrehozni egy másik kapacitási készletben, és az átmásolt adat.

Vegye figyelembe, hogy a kötet tárolási kvótáját és a kapacitás-készlet méretét a hozzáférés megszakítása nélkül módosíthatja.

## <a name="create-storage-targets-in-the-cache"></a>Tárolási célok létrehozása a gyorsítótárban

Miután beállította Azure NetApp Files rendszerét, és létrehozta az Azure HPC cache-t, adja meg a tárolási célokat a fájlrendszer köteteire mutató gyorsítótárban.

Hozzon létre egy tárolási célt a Azure NetApp Files kötetek által használt minden IP-címhez. Az IP-cím megjelenik a kötet csatlakoztatási utasításai oldalon.

Ha több kötet is ugyanazzal az IP-címmel rendelkezik, használhatja az egyik tárolási célt.  

A használni kívánt IP-címek megkereséséhez kövesse a [Azure NetApp Files dokumentációjának csatlakoztatási utasításait](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .

Emellett az Azure CLI-vel is megtalálhatja az IP-címeket:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

A Azure NetApp Files rendszerbeli nevek exportálása egyetlen elérésiút-összetevővel rendelkezik. Ne próbálkozzon olyan tárolási cél létrehozásával, amely a ``/`` Azure NetApp Files, mert az exportálás nem biztosít a fájlokhoz való hozzáférést.

Nincsenek különleges korlátozások a virtuális névtér elérési útjaihoz a tárolási célokhoz.

## <a name="mount-storage-targets"></a>Tárolási célok csatlakoztatása

Az ügyfélszámítógépeknek csatlakoztatnia kell a gyorsítótárat a Azure NetApp Files kötetek közvetlen csatlakoztatása helyett. Kövesse az [Azure HPC cache csatlakoztatása](hpc-cache-mount.md)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure NetApp Files](../azure-netapp-files/index.yml) beállításáról és használatáról
* Az Azure HPC cache-rendszer Azure NetApp Files használatára való megtervezéséhez és beállításához [forduljon az ügyfélszolgálathoz](hpc-cache-support-ticket.md).
