---
title: Az Azure HPC cache NFS tárolási céljaival kapcsolatos hibák megoldása
description: Tippek a konfigurációs hibák és egyéb olyan problémák elkerüléséhez és javításához, amelyek meghibásodást okozhatnak az NFS-tárolási cél létrehozásakor
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775697"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>A NAS-konfiguráció és az NFS-tárolási cél problémáinak elhárítása

Ez a cikk néhány gyakori konfigurációs hibát és más olyan problémát tartalmaz, amelyek megakadályozhatják, hogy az Azure HPC-gyorsítótár egy NFS Storage-tárolót adjon hozzá tárolási célként.

Ez a cikk részletesen ismerteti a portok ellenőrzését, valamint azt, hogyan engedélyezhető a hozzáférés a NAS rendszerhez. Részletes információkat is tartalmaz a kevésbé gyakori problémákról, amelyek miatt előfordulhat, hogy az NFS-tárolási cél létrehozása sikertelen lesz.

> [!TIP]
> Az útmutató használata előtt olvassa el [az NFS-tárolási célokra vonatkozó előfeltételek](hpc-cache-prerequisites.md#nfs-storage-requirements)című részt.

Ha a probléma megoldására nem kerül sor, [Nyisson meg egy támogatási jegyet](hpc-cache-support-ticket.md) , hogy a Microsoft szolgáltatás és támogatás a probléma kivizsgálásához és megoldásához is működjön.

## <a name="check-port-settings"></a>Portbeállítások keresése

Az Azure HPC-gyorsítótárnak olvasási/írási hozzáférésre van szüksége a háttérbeli NAS-tárolási rendszeren található több UDP/TCP-porthoz. Győződjön meg arról, hogy ezek a portok elérhetők a NAS rendszeren, valamint azt is, hogy a portok a tárolási rendszer és a gyorsítótár-alhálózat közötti tűzfalakon keresztül engedélyezettek-e. Előfordulhat, hogy az adatközponthoz tartozó tűzfal-és hálózati rendszergazdákkal kell dolgoznia a konfiguráció ellenőrzéséhez.

A portok eltérőek a különböző gyártóktól származó tárolási rendszerek esetében, ezért a tárolási cél beállításakor ellenőriznie kell a rendszer követelményeit.

Általánosságban elmondható, hogy a gyorsítótárnak hozzá kell férnie a következő portokhoz:

| Protokoll | Port  | Szolgáltatás  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | status   |

A rendszerhez szükséges adott portok megismeréséhez használja az alábbi ``rpcinfo`` parancsot. Az alábbi parancs felsorolja a portokat, és formázza a kapcsolódó eredményeket egy táblában. (A *<storage_IP>* kifejezés helyett használja a számítógép IP-címét.)

Ezt a parancsot bármely olyan Linux-ügyfélről kiállíthatja, amelyen telepítve van az NFS-infrastruktúra. Ha a fürt alhálózatán belül használ ügyfelet, az az alhálózat és a tárolási rendszer közötti kapcsolat ellenőrzéséhez is segítséget nyújt.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Győződjön meg arról, hogy a lekérdezés által visszaadott összes port ``rpcinfo`` engedélyezi az Azure HPC cache alhálózatának korlátozás nélküli forgalmát.

Ezeket a beállításokat a hálózati házirend-kiszolgáló és a tárolási rendszer és a gyorsítótár-alhálózat közötti összes tűzfalon is megtekintheti.

## <a name="check-root-access"></a>Rendszergazdai hozzáférés bejelölése

Az Azure HPC cache-nek hozzá kell férnie a tárolási rendszerek exportálásához a tárolási cél létrehozásához. Pontosabban a 0. felhasználói AZONOSÍTÓként csatlakoztatja az exportálást.

A különböző tárolási rendszerek különböző módszereket használnak a hozzáférés engedélyezéséhez:

* A Linux-kiszolgálók általában ``no_root_squash`` az exportált elérési útra lesznek hozzáadva ``/etc/exports`` .
* A NetApp és az EMC rendszerek jellemzően a megadott IP-címekhez vagy hálózatokhoz kötött exportálási szabályokhoz való hozzáférést vezérlik.

Exportálási szabályok használata esetén ne feledje, hogy a gyorsítótár több különböző IP-címet is használhat a gyorsítótár alhálózatáról. Engedélyezi a hozzáférést a lehetséges alhálózati IP-címek teljes tartományából.

> [!NOTE]
> Bár a gyorsítótárnak rendszergazdai hozzáférésre van szüksége a háttér-tárolási rendszerhez, korlátozhatja a hozzáférést a gyorsítótáron keresztül csatlakozó ügyfelek számára. Részletekért olvassa el az [ügyfél-hozzáférés vezérlése](access-policies.md#root-squash) című leírást.

A NAS-tároló gyártójával együttműködve engedélyezze a megfelelő szintű hozzáférést a gyorsítótárhoz.

### <a name="allow-root-access-on-directory-paths"></a>Elérés engedélyezése a gyökérkönyvtár elérési útjain
<!-- linked in prereqs article -->

A hierarchikus könyvtárakat exportáló NAS-rendszerek esetében az Azure HPC cache-nek rendszergazdai hozzáférésre van szüksége az egyes exportálási szintekhez.

Előfordulhat például, hogy egy rendszer három olyan exportálást mutat be, mint a következő:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Az Exportálás a ``/ifs/accounting/payroll`` gyermeke ``/ifs/accounting`` , és ``/ifs/accounting`` maga a gyermeke ``/ifs`` .

Ha az ``payroll`` exportálást HPC cache Storage-tárolóként adja hozzá, a gyorsítótár ténylegesen csatlakoztatja ``/ifs/`` és hozzáfér a bérszámfejtési címtárhoz. Így az Azure HPC-gyorsítótárnak rendszergazdai hozzáférésre van szüksége ahhoz, ``/ifs`` hogy hozzáférhessen az ``/ifs/accounting/payroll`` exportáláshoz.

Ez a követelmény ahhoz kapcsolódik, ahogy a gyorsítótár indexeli a fájlokat, és elkerüli a fájlok ütközéseit a tárolási rendszer által biztosított fájlleíró használatával.

A hierarchikus exportálást biztosító NAS-rendszer különböző fájlkezelőket biztosíthat ugyanahhoz a fájlhoz, ha a fájl különböző exportálásokból származik. Például egy ügyfél csatlakoztathatja ``/ifs/accounting`` és elérheti a fájlt ``payroll/2011.txt`` . Egy másik ügyfél csatlakoztatja ``/ifs/accounting/payroll`` és hozzáfér a fájlhoz ``2011.txt`` . Attól függően, hogy a tárolási rendszer hogyan rendeli hozzá a fájlkezelőket, ez a két ügyfél ugyanazt a fájlt kapja, amely különböző fájlkezelőket tartalmaz (egyet ``<mount2>/payroll/2011.txt`` és egyet ``<mount3>/2011.txt`` ).

A háttérbeli tárolórendszer belső aliasokat tart fenn a Fájlkezelőben, de az Azure HPC-gyorsítótár nem tudja megállapítani, hogy az indexében melyik fájlkezelő hivatkozik ugyanarra az elemre. Így előfordulhat, hogy a gyorsítótár különböző írási műveleteket hajt végre ugyanazon a fájlon, és helytelenül alkalmazza a módosításokat, mert nem tudja, hogy ugyanaz a fájl.

Ha el szeretné kerülni a fájlok ütközését több exportálási fájl esetében, az Azure HPC cache automatikusan csatlakoztatja a legsekélyebb elérhető exportálást az elérési útban ( ``/ifs`` a példában), és az adott exportálásból származó fájlleíró-t használja. Ha több exportálás ugyanazt az alapútvonalat használja, az Azure HPC-gyorsítótárnak rendszergazdai hozzáférésre van szüksége ehhez az elérési úthoz.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>A VPN-csomagok méretére vonatkozó korlátozások módosítása
<!-- link in prereqs article and configuration article -->

Ha VPN-kapcsolattal rendelkezik a gyorsítótár és a NAS-eszköz között, akkor a VPN blokkolhatja a teljes méretű, 1500 bájtos Ethernet-csomagokat. Lehet, hogy ez a probléma akkor fordulhat elő, ha a NAS és az Azure HPC cache-példány közötti nagy adatcsere nem fejeződik be, de a kisebb frissítések a várt módon működnek.

Nincs egyszerű módszer annak megadására, hogy a rendszer rendelkezik-e ezzel a problémával, kivéve, ha ismeri a VPN-konfiguráció részleteit. Íme néhány módszer, amelyek segíthetnek a probléma vizsgálatában.

* A VPN mindkét oldalán található Packet-szippantás segítségével azonosíthatja, hogy mely csomagok átvitele sikerült.
* Ha a VPN lehetővé teszi a ping parancsok használatát, tesztelheti a teljes méretű csomag küldését.

  Futtassa a ping parancsot a VPN-en keresztül a hálózati hozzáférés-vezérlési lehetőségekkel. (A tárolásirendszer IP-címét a *<storage_IP>* érték helyett használja.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  A parancsban a következő lehetőségek érhetők el:

  * ``-M do`` -Ne legyen töredék
  * ``-c 1`` – Csak egy csomagot küldjön
  * ``-s 1472`` -A hasznos adatok méretének beállítása 1472 bájtra. Ez a maximális méret a 1500 bájtos csomag esetében az Ethernet-terhelés elszámolása után.

  A sikeres válasz így néz ki:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Ha a pingelés 1472 bájttal meghiúsul, valószínűleg egy csomagméret okoz problémát.

A probléma megoldásához konfigurálnia kell a MSS-befogást a VPN-en, hogy a távoli rendszer megfelelően észlelje a keret maximális méretét. További információért olvassa el az [VPN Gateway IPSec/IKE-paraméterek dokumentációját](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) .

Bizonyos esetekben az Azure HPC-gyorsítótár 1400-es értékének módosítása segíthet. Ha azonban korlátozza az MTU-t a gyorsítótárban, akkor a gyorsítótárral kommunikáló ügyfelek és háttérbeli tárolási rendszerek MTU-beállításait is korlátoznia kell. További részletekért olvassa el az [Azure HPC gyorsítótár további beállításainak konfigurálása](configuration.md#adjust-mtu-value) című leírást.

## <a name="check-for-acl-security-style"></a>ACL biztonsági stílusának keresése

Egyes NAS-rendszerek hibrid biztonsági stílust használnak, amely a hozzáférés-vezérlési listákat (ACL-eket) ötvözi a hagyományos POSIX-vagy UNIX-alapú biztonsággal.

Ha a rendszer az "ACL" betűszó nélkül jelenti a biztonsági stílusát UNIX vagy POSIX-ként, akkor ez a probléma nem érinti Önt.

Az ACL-eket használó rendszerek esetében az Azure HPC cache-nek további felhasználóspecifikus értékeket kell követnie a fájlokhoz való hozzáférés szabályozása érdekében. Ezt a hozzáférési gyorsítótár engedélyezésével teheti meg. Nincs egy felhasználó felé irányuló vezérlőelem a hozzáférési gyorsítótár bekapcsolásához, de egy támogatási jegyet is megnyitva kérheti, hogy engedélyezze az érintett tárolási célokat a gyorsítótár-rendszeren.

## <a name="next-steps"></a>Következő lépések

Ha problémája van, amely nem szerepel ebben a cikkben, [Nyisson meg egy támogatási jegyet](hpc-cache-support-ticket.md) a szakértői segítség kéréséhez.
