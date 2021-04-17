---
title: Az Oracle-hez szükséges BareMetal Ethernet-konfigurációja
description: Ismerje meg az Ethernet-interfészek Konfigurálást Az Oracle számítási feladatokhoz szükséges BareMetal-példányok esetében.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588887"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Az Oracle-hez szükséges BareMetal Ethernet-konfigurációja

Ebben a cikkben az Oracle számítási feladatokHoz szükséges BareMetal-példányok Ethernet-csatolóinak konfigurációját fogjuk átfedni.

Az Oracle minden kiépített BareMetal-példánya előre konfigurálva van Ethernet-csatolókészletekkel. Az Ethernet-interfészek négy típusba vannak besorolva:

- Ügyfél-hozzáféréshez vagy az által használt.
- Csomópontok közötti kommunikációhoz használatos. Ez az interfész a kért topológiától függetlenül minden kiszolgálón konfigurálva van. Csak felskálásos forgatókönyvekhez használható.
- Csomópontok és tárolók összekapcsolhatósága.
- Vészhelyreállítás (DR) beállításához és globális elérésű kapcsolatokhoz használatos a régiók közötti kapcsolatokhoz.

## <a name="architecture"></a>Architektúra

Az alábbi ábra az Előre konfigurált Ethernet-felületek architektúráját mutatja be. 

[![Az Oracle számítási feladatokhoz előre konfigurált Ethernet-interfészek architektúráját bemutató ábra.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Az alapértelmezett konfigurációhoz egy ügyfél-IP-felület (eth1) csatlakozik az Azure Virtual Network -ről (VNET), amellyel a Secure Shell (SSH) használatával hozzáférhet egy BareMetal-példányhoz.

> [!NOTE]
> Egy másik Azure VNET-ről származó másik ügyféloldali felület (eth10) esetén forduljon a Microsoft CSA-hoz egy szolgáltatáskérés elküldését. Ha például fejlesztési/tesztelési, valamint éles/dr. környezeteket szeretne.

| **Hálózati adapter logikai felülete** | **Név RHEL operációs rendszerrel** | **Használati eset** |
| --- | --- | --- |
| A | net1.tenant | Ügyfélről BareMetal-példányra |
| C | net2.tenant | Csomópontok és tárolók között; támogatja a tárolóvezérlők koordinációját és hozzáférését a tárolási környezet felügyeletéhez. |
| B | net3.tenant | Csomópontok közötti (privát összekapcsolás) |
| C | net4.tenant | Fenntartott/iSCSI |
| C | net5.tenant | Fenntartott/naplók biztonsági mentése |
| C | net6.tenant | Csomópontok között storage_Data biztonsági mentés (RMAN, pillanatkép) |
| C | net7.tenant | Csomópontok között storage_dNFS-Pri; A kapcsolatot biztosít a NetApp-tárolótömbhöz. |
| C | net8.tenant | Csomópontok és csomópontok storage_dNFS másodpercenként; A kapcsolatot biztosít a NetApp-tárolótömbhöz. |
| T | net9.tenant | DR-kapcsolat a Global Reach beállításához a BMI másik régióban való eléréséhez. |
| A | \*net10.tenant | \* Ügyfélről BareMetal-példányra
 |

Szükség esetén több hálózati adaptert (NIC) is meghatározhat. A meglévő hálózati konfigurációk azonban nem *módosíthatók.*

## <a name="usage-rules"></a>Használati szabályok

BareMetal-példányok esetén az alapértelmezett érték kilenc hozzárendelt IP-címmel rendelkezik a négy logikai hálózati npcén. A következő használati szabályok érvényesek:

- Az "A" Ethernetnek olyan hozzárendelt IP-címmel kell lennie, amely kívül esik a Kiszolgáló IP-címkészletének a Microsoftnak elküldött címtartományán. Ezt az IP-címet nem szabad az operációs rendszer etc/hosts könyvtárában fenntartani.
- A "B" Ethernetet kizárólag az etc/hosts könyvtárban kell fenntartani a különböző példányok közötti kommunikációhoz. Ezeket az IP-címeket a csomópontok közötti konfigurációhoz használt IP-címként használja felskálás oracle real application clusters (RAC) konfigurációkban.
- A "C" Ethernetnek egy hozzárendelt IP-címmel kell lennie, amely az NFS-tárolóval való kommunikációra használatos. Ez a címtípus nem tartható fenn az etc/hosts könyvtárban.
- Az Ethernet "D" kizárólag a globális elérésű beállításhoz használható a DR-régióban található BareMetal-példányok eléréséhez.

## <a name="next-step"></a>Következő lépés

További információ az Oracle-architektúrához szükséges BareMetal-infrastruktúráról.

> [!div class="nextstepaction"]
> [Az Oracle BareMetal-infrastruktúrájának architektúrája](oracle-baremetal-architecture.md)
