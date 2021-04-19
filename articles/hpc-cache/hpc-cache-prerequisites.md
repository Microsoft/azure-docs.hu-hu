---
title: Azure HPC Cache előfeltételek
description: A Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717584"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>A Azure HPC Cache

Mielőtt új Azure Portal hoz létre a Azure HPC Cache, győződjön meg arról, hogy a környezet megfelel ezeknek a követelményeknek.

## <a name="video-overviews"></a>Videók áttekintése

Ezekben a videókban gyorsan áttekintheti a rendszer összetevőit, és hogy mire van szükségük az együttes munkához.

(Kattintson a videóképre vagy a megtekintésre mutató hivatkozásra.)

* [A működése](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – Ismerteti, hogyan Azure HPC Cache a tárolással és az ügyfelekkel

  [![videó miniatűrje kép: Azure HPC Cache: A működése (kattintson ide a videóoldal megjelenítéséhez)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Előfeltételek](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) – Ismerteti a NAS-tárolókra, az Azure Blob Storage-ra, a hálózati hozzáférésre és az ügyfél-hozzáférésre vonatkozó követelményeket

  [![videó miniatűrje kép: Azure HPC Cache: Előfeltételek (kattintson ide a videóoldal megjelenítéséhez)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

A cikk további részében konkrét javaslatokat talál.

## <a name="azure-subscription"></a>Azure-előfizetés

Fizetős előfizetés használata ajánlott.

## <a name="network-infrastructure"></a>Hálózati infrastruktúra

A gyorsítótár használata előtt két hálózattal kapcsolatos előfeltételt kell beállítani:

* Dedikált alhálózat a Azure HPC Cache számára
* DNS-támogatás, hogy a gyorsítótár hozzáfér a tárolóhoz és más erőforrásokhoz

### <a name="cache-subnet"></a>Gyorsítótár-alhálózat

A Azure HPC Cache egy dedikált alhálózatra van szüksége, amely a következő tulajdonságokat tulajdonságokkal rendelkezik:

* Az alhálózatnak legalább 64 elérhető IP-címmel kell lennie.
* Az alhálózat nem képes más virtuális gépekre, még a kapcsolódó szolgáltatások, például az ügyfélszámítógépek számára sem.
* Ha több virtuális Azure HPC Cache használ, mindegyiknek saját alhálózatra van szüksége.

Az ajánlott eljárás egy új alhálózat létrehozása minden gyorsítótárhoz. A gyorsítótár létrehozása során létrehozhat egy új virtuális hálózatot és alhálózatot.

### <a name="dns-access"></a>DNS-hozzáférés

A gyorsítótárnak DNS-sel kell hozzáférnie a virtuális hálózaton kívüli erőforrásokhoz. Attól függően, hogy milyen erőforrásokat használ, előfordulhat, hogy be kell állítania egy testreszabott DNS-kiszolgálót, és konfigurálnia kell az adott kiszolgáló és a Azure DNS közötti továbbítást:

* Az Azure Blob Storage-végpontok és más belső erőforrások eléréséhez az Azure-alapú DNS-kiszolgálóra van szükség.
* A helyszíni tároló eléréséhez konfigurálnia kell egy egyéni DNS-kiszolgálót, amely fel tudja oldani a tároló állomásneveit. Ezt a gyorsítótár létrehozása előtt kell megtennie.

Ha csak a Blob Storage-et használja, használhatja az alapértelmezett Azure-beli DNS-kiszolgálót a gyorsítótárhoz. Ha azonban tárterülethez vagy más, az Azure-n kívüli erőforrásokhoz való hozzáférésre van szüksége, hozzon létre egy egyéni DNS-kiszolgálót, és konfigurálja úgy, hogy továbbítsa az Azure-specifikus feloldási kéréseket az Azure DNS kiszolgálóra.

Egyéni DNS-kiszolgáló használata esetén a gyorsítótár létrehozása előtt végre kell hoznia ezeket a telepítési lépéseket:

* Hozza létre a virtuális hálózatot, amely a Azure HPC Cache.
* Hozza létre a DNS-kiszolgálót.
* Adja hozzá a DNS-kiszolgálót a gyorsítótár virtuális hálózatához.

  A DNS-kiszolgáló virtuális hálózathoz való hozzáadásához kövesse az alábbi Azure Portal:

  1. Nyissa meg a virtuális hálózatot a Azure Portal.
  1. Az oldalsáv Beállítások menüjében válassza a DNS-kiszolgálók lehetőséget.
  1. Egyéni kiválasztása
  1. Adja meg a DNS-kiszolgáló IP-címét a mezőben.

Egy egyszerű DNS-kiszolgáló is használható az ügyfélkapcsolatok terheléselosztására az összes rendelkezésre álló gyorsítótár-csatlakoztatási pont között.

További információ az Azure-beli virtuális hálózatokról és a DNS-kiszolgálók konfigurációiról: Az Azure-beli virtuális hálózatok [erőforrásainak névfeloldása.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="permissions"></a>Engedélyek

A gyorsítótár létrehozása előtt ellenőrizze ezeket az engedélyekkel kapcsolatos előfeltételeket.

* A gyorsítótárpéldánynak képesnek kell lennie virtuális hálózati adapterek (HÁLÓZATI adapterek) létrehozására. A gyorsítótárat létrehozó felhasználónak megfelelő jogosultságokkal kell rendelkezik az előfizetésben a NIC-k létrehozásához.

* Blob Storage használata esetén Azure HPC Cache engedélyre van szüksége a tárfiók eléréséhez. Az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) hozzáférést adhat a gyorsítótárnak a Blob Storage-hoz. Két szerepkör szükséges: a Tárfiók közreműködője és a Storage-blobadatok közreműködője.

  A szerepkörök hozzáadásához kövesse a [Tárolócélok](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) hozzáadása útmutatását.

## <a name="storage-infrastructure"></a>Tárolási infrastruktúra
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

A gyorsítótár támogatja az Azure Blob-tárolókat, az NFS hardveres tárolóexportokat és az NFS-hez csatlakoztatott ADLS-blobtárolókat (jelenleg előzetes verzióban érhető el). A gyorsítótár létrehozása után adjon hozzá céltárolókat.

Minden tárolótípushoz adott előfeltételek vonatkoznak.

### <a name="blob-storage-requirements"></a>A Blob Storage követelményei

Ha az Azure Blob Storage-et a gyorsítótárral szeretné használni, szüksége lesz egy kompatibilis tárfiókra és egy üres Blob-tárolóra vagy egy olyan tárolóra, amely Azure HPC Cache Adatok áthelyezése az Azure Blob Storage-ba leírás szerint formázott adatokkal van [feltöltve.](hpc-cache-ingest.md)

> [!NOTE]
> Az NFS által csatlakoztatott blobtárolókra különböző követelmények vonatkoznak. A részletekért olvassa el az [ADLS-NFS](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) tárolási követelményeit.

Tárterület hozzáadása előtt hozza létre a fiókot. A cél hozzáadásakor létrehozhat egy új tárolót.

Kompatibilis tárfiók létrehozásához használja az alábbi kombinációk valamelyikét:

| Teljesítmény | Típus | Replikáció | Hozzáférési szint |
|--|--|--|--|
| Standard | StorageV2 (általános célú v2)| Helyileg redundáns tárolás (LRS) vagy zónaredundáns tárolás (ZRS) | Gyakori |
| Prémium | Blokkblobok | Helyileg redundáns tárolás (LRS) | Gyakori |

A tárfióknak elérhetőnek kell lennie a gyorsítótár privát alhálózatán. Ha a fiókja privát vagy adott virtuális hálózatokra korlátozott nyilvános végpontot használ, engedélyezze a gyorsítótár alhálózatának hozzáférését. (Nyílt nyilvános végpont használata nem ajánlott.)

A gyorsítótárral azonos Azure-régióban lévő tárfiók használata jó gyakorlat.

Emellett hozzáférést kell adni a gyorsítótárazási alkalmazásnak az Azure Storage-fiókhoz a [fenti Engedélyekben](#permissions)említettek szerint. Kövesse a [Tárolócélok hozzáadása eljárását,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) hogy a gyorsítótárnak megadja a szükséges hozzáférési szerepköröket. Ha nem Ön a tárfiók tulajdonosa, tegye meg a tulajdonossal ezt a lépést.

### <a name="nfs-storage-requirements"></a>NFS-tárolókövetelmények
<!-- linked from configuration.md -->

NFS-tárolórendszer (például helyszíni hardverES NAS-rendszer) használata esetén győződjön meg arról, hogy megfelel ezeknek a követelményeknek. Előfordulhat, hogy a beállítások ellenőrzéséhez a tárolórendszer (vagy az adatközpont) hálózati rendszergazdáival vagy tűzfalkezelőivel kell dolgoznia.

> [!NOTE]
> A tároló célhelyének létrehozása sikertelen lesz, ha a gyorsítótár nem rendelkezik megfelelő hozzáféréssel az NFS-tárolórendszerhez.

További információ: NAS-konfigurációval és [NFS-tárolók célproblémáival kapcsolatos problémák elhárítása.](troubleshoot-nas.md)

* Hálózati kapcsolat: A Azure HPC Cache nagy sávszélességű hálózati hozzáférést igényel a gyorsítótár-alhálózat és az NFS-rendszer adatközpontja között. [Az ExpressRoute](../expressroute/index.yml) vagy hasonló hozzáférés ajánlott. HA VPN-t használ, előfordulhat, hogy úgy kell konfigurálnia, hogy a TCP MSS-t 1350-re állítsa, hogy a nagy csomagok ne legyen blokkolva. A [VPN-beállítások hibaelhárításában](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) további segítségért olvassa el a VPN-csomagméret-korlátozásokat.

* Port elérése: A gyorsítótárnak hozzá kell férni a tárolórendszer adott TCP-/UDP-portjaihoz. A különböző tárolótípusok különböző portkövetelményekkel rendelkezik.

  A tárolórendszer beállításainak ellenőrzéséhez kövesse ezt az eljárást.

  * A szükséges portok ellenőrzéséhez parancsot kell kiadni a `rpcinfo` tárolórendszernek. Az alábbi parancs felsorolja a portokat, és táblázatba formázja a releváns eredményeket. (Használja a rendszer IP-címét a <storage_IP>helyén.)

    Ezt a parancsot bármely olyan Linux-ügyfélről kihasználhatja, amely rendelkezik telepített NFS-infrastruktúrával. Ha a fürt alhálózatán belül használ ügyfelet, az segíthet az alhálózat és a tárolórendszer közötti kapcsolat ellenőrzésében is.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Győződjön meg arról, hogy a lekérdezés által visszaadott összes port engedélyezi a Azure HPC Cache alhálózatának ``rpcinfo`` korlátlan forgalmát.

  * Ha nem tudja használni a parancsot, győződjön meg arról, hogy ezek a gyakran használt portok engedélyezik a bejövő és `rpcinfo` kimenő forgalmat:

    | Protokoll | Port  | Szolgáltatás  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | csatlakoztatva   |
    | TCP/UDP  | 4047  | status   |

    Egyes rendszerek különböző portszámokat használnak ezekhez a szolgáltatásokhoz – ebben a tárolórendszer dokumentációjában talál további információt.

  * Ellenőrizze a tűzfalbeállításokat, és győződjön meg arról, hogy engedélyezik a forgalmat az összes szükséges porton. Ellenőrizze az Azure-ban használt tűzfalakat, valamint az adatközpontban található helyszíni tűzfalakat.

* Gyökér hozzáférés (olvasási/írási): A gyorsítótár a 0 felhasználói azonosítóval csatlakozik a háttérrendszerhez. Ellenőrizze az alábbi beállításokat a tárolórendszeren:
  
  * Engedélyezze az `no_root_squash` engedélyezését. Ez a beállítás biztosítja, hogy a távoli gyökérfelhasználó hozzáfér a gyökér tulajdonában lévő fájlokhoz.

  * Ellenőrizze az exportálási szabályzatokat, és győződjön meg arról, hogy nem tartalmaznak korlátozásokat a gyorsítótár alhálózatának gyökér hozzáférésére.

  * Ha a tárolóban vannak olyan exportálások, amelyek egy másik exportálás alkönyvtárában vannak, győződjön meg arról, hogy a gyorsítótár gyökér szintű hozzáféréssel rendelkezik az elérési út legalsó szegmensében. További [részleteket az](troubleshoot-nas.md#allow-root-access-on-directory-paths) NFS-tároló céljának hibaelhárítási cikkében, a könyvtárelérési útvonalak gyökérelérési útvonalán talál.

* Az NFS-háttértárnak kompatibilis hardver-/szoftverplatformnak kell lennie. Részletekért forduljon Azure HPC Cache csapathoz.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>NFS-hez csatlakoztatott blob (ADLS-NFS) tárolási követelményei (ELŐZETES VERZIÓ)

Azure HPC Cache NFS protokollal csatlakoztatott blobtárolót is használhat tárolási célként.

> [!NOTE]
> Az Azure Blob Storage NFS 3.0 protokoll támogatása nyilvános előzetes verzióban érhető el. A rendelkezésre állás korlátozott, és a funkciók változhatnak a funkció általános elérhetővé válnak. Ne használjon előzetes verziójú technológiát éles rendszerekben.
>
> További információ az [NFS 3.0](../storage/blobs/network-file-system-protocol-support.md)protokolltámogatás előzetes verziójú funkcióról az Azure Blob Storage-ban.

A tárfiókra vonatkozó követelmények eltérnek az ADLS-NFS blobtárolási cél és a standard blobtároló célhelyei között. Kövesse a Blob Storage csatlakoztatása a hálózati fájlrendszer [(NFS) 3.0](../storage/blobs/network-file-system-protocol-support-how-to.md) protokoll használatával cikk utasításait az NFS-kompatibilis tárfiók létrehozásához és konfigurálásához.

Ez a lépések általános áttekintése. Ezek a lépések változhatnak, ezért az aktuális részletekért mindig tekintse meg az [ADLS-NFS](../storage/blobs/network-file-system-protocol-support-how-to.md) utasításait.

1. Győződjön meg arról, hogy a szükséges funkciók elérhetők abban a régióban, ahol dolgozni tervez.

1. Engedélyezze az NFS protokollszolgáltatást az előfizetéséhez. Ezt a *tárfiók* létrehozása előtt tegye meg.

1. Hozzon létre egy biztonságos virtuális hálózatot (VNet) a tárfiókhoz. Ugyanazt a virtuális hálózatot kell használnia az NFS-kompatibilis tárfiókhoz és a Azure HPC Cache. (Ne használja ugyanazt az alhálózatot, mint a gyorsítótár.)

1. Hozza létre a tárfiókot.

   * Ahelyett, hogy egy standard Blob Storage-fiókhoz használnál tárfiók-beállításokat, kövesse az [útmutatóban található utasításokat.](../storage/blobs/network-file-system-protocol-support-how-to.md) A támogatott tárfiók típusa Azure-régiónként eltérő lehet.

   * A Hálózat szakaszban válasszon egy privát végpontot a létrehozott biztonságos virtuális hálózaton (ajánlott), vagy válasszon egy korlátozott hozzáférésű nyilvános végpontot a biztonságos virtuális hálózatról.

   * Ne felejtse el befejezni a Speciális szakaszt, amelyben engedélyezi az NFS-hozzáférést.

   * Adjon hozzáférést a gyorsítótárazási alkalmazásnak az Azure Storage-fiókhoz a [fenti Engedélyek (](#permissions)) dokumentumban említettek szerint. Ezt az első tárolási cél létrehozásakor is meg lehet tenni. Kövesse a Tárolócélok [hozzáadása eljárását,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) hogy megadja a gyorsítótárnak a szükséges hozzáférési szerepköröket.

     Ha nem Ön a tárfiók tulajdonosa, ezt a lépést a tulajdonossal kell megtennie.

Az ADLS-NFS-tárolók és -tárolók Azure HPC Cache [az NFS-hez](nfs-blob-considerations.md)csatlakoztatott blobtároló használata a Azure HPC Cache.

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI-hozzáférés beállítása (nem kötelező)

Ha az Azure parancssori felületéről (Azure CLI Azure HPC Cache szeretné létrehozni vagy kezelni a parancssori felületet, telepítenie kell a CLI-szoftvert és a hpc-cache bővítményt. Kövesse az Azure CLI beállítása a következő [szolgáltatáshoz: Azure HPC Cache.](az-cli-prerequisites.md)

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre Azure HPC Cache-példányt](hpc-cache-create.md) a Azure Portal
