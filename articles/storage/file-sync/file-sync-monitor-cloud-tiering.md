---
title: Felhőbeli Azure File Sync rétegezésének monitorozása | Microsoft Docs
description: A felhőbeli rétegezés szabályzatának figyelése során használt metrikák részletei.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796861"
---
# <a name="monitor-cloud-tiering"></a>Felhőbeli rétegezés monitorozása
A felhőbeli rétegezés szabályzatának figyelése kétféleképpen lehetséges: a kiszolgálóvégpont tulajdonságainak panelje és a Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Monitorozás kiszolgálóvégponton keresztül

Jelentkezzen be a [Azure Portal,](https://portal.azure.com/)majd navigáljon a monitorozni szeretne kiszolgálói végpont kiszolgálóvégpont-tulajdonságainak paneljére, és görgessen le a felhőbeli rétegezés szakaszhoz.  

![Képernyőkép a kiszolgálóvégpont tulajdonságainak felhőbeli rétegezés szakaszról.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**A helymegtakarítás** a felhőbeli rétegezés engedélyezésével megtakarított terület. Ha a helyi kötet mérete elég nagy az összes adathoz, 0%-os helymegtakarítást biztosít. Ha 0%-os vagy alacsony lemezterület-megtakarítással jár, az azt jelezheti, hogy a felhőbeli rétegezés nem előnyös a helyi gyorsítótár jelenlegi méretével. 

**A gyorsítótár találati aránya** a helyi gyorsítótárban megnyitott fájlok százalékos aránya. A gyorsítótár találati aránya a közvetlenül a gyorsítótárból megnyitott fájlok/a megnyitott fájlok teljes száma alapján számítható ki. A 100%-os gyorsítótár-találati arány azt jelenti, hogy az elmúlt órában megnyitott összes fájl már a helyi gyorsítótárban volt. Ha a cél a bejövő forgalom csökkentése, az azt jelezné, hogy a jelenlegi szabályzat megfelelően működik.

> [!NOTE]
> A véletlenszerű hozzáférési mintákkal használt számítási feladatok általában alacsonyabb gyorsítótár-találati arányokkal rendelkezik. 

**A teljes méret (felhő)** a felhőbe szinkronizált fájlok mérete. 

**A gyorsítótárazott méret (kiszolgáló)** a kiszolgálón lévő fájlok teljes mérete( letöltött és rétegzett). Előfordulhat, hogy a gyorsítótárazott méret nagyobb, mint a fájlok teljes mérete a felhőben. Ezt olyan változók okozhatják, mint a kötet fürtmérete a kiszolgálón. Ha a gyorsítótárazott méret nagyobb a kívántnál, érdemes lehet növelni a kötet szabad területére vonatkozó házirendet. 

**A kötetek a hatályos** kötet-Azure File Sync határozzák meg, hogy mennyi szabad terület hagyja el a kötetet, amelyen a kiszolgálóvégpont található. Ha több kiszolgálóvégpont található ugyanazon a köteten, a kiszolgálóvégpont közötti legnagyobb kötet szabad hely házirend lesz a köteten található összes kiszolgálóvégpontra érvényes kötet-házirend. Ha például két kiszolgálóvégpont található ugyanazon a köteten, az egyik 30%-os szabad kötetterületet, a másik pedig 50%-os szabad kötetterületet, akkor a kötet szabad területére vonatkozó szabályzat mindkét kiszolgálóvégpontra 50%.

**Az aktuális szabad kötetterület** a helyszíni kiszolgálón jelenleg elérhető szabad kötetterület. Ha magas a bejövő forgalom, de több szabad hely áll rendelkezésre a köteten a szabad terület szabályzatának útjára való lépés előtt, tiltsa le a dátum-szabályzatot. Egy másik probléma lehet, hogy a jelenleg rétegzett fájlokból a legutóbb elért fájl nagyobb, mint a kötet szabad lemezterülete, mielőtt a házirend útjára indul. Ebben az esetben érdemes lehet növelni a helyi kötet méretét. 

## <a name="monitoring-via-azure-monitor"></a>Monitorozás Azure Monitor

Az oldalnavigációs **menüben válassza** a **Metrikák** lehetőséget a Társzinkronizálási szolgáltatásban. 

Három különböző metrika használhatja a bejövő forgalom figyelése kifejezetten a felhőbeli rétegezésből:

- Felhőbeli rétegezés visszahívási mérete
    - Ez a bájtban visszahívott adatok teljes mérete, amely alapján azonosítható a visszahívott adatok száma.
- Felhőbeli rétegezés visszahívási mérete alkalmazás szerint
    - Ez az alkalmazás által bájtban visszahívott adatok teljes mérete, és a segítségével azonosítható, hogy mi idézi elő az adatokat.
- Felhőbeli rétegezés visszahívási átviteli sebesség
    - Ez azt méri, hogy milyen gyorsan történik az adatok visszahívása bájtban, és akkor érdemes használni őket, ha aggodalmai vannak a teljesítménnyel kapcsolatban. 

Ha pontosabban szeretné, hogy mit jelenítsen meg a gráfok, fontolja meg a **Szűrő** hozzáadása és a **Felosztás alkalmazása stb.**
 
A különböző típusú metrikákról és azok Azure File Sync a Monitor [Azure File Sync.](file-sync-monitoring.md)

A metrikák használatával kapcsolatos részletekért lásd: Ismerkedés az [Azure Metrikaböngésző.](../../azure-monitor/essentials/metrics-getting-started.md).

Ha módosítani szeretné a felhőbeli rétegezés szabályzatát, tekintse meg a felhőbeli rétegezésre vonatkozó [szabályzatok választását.](file-sync-choose-cloud-tiering-policies.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)