---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179150"
---
| **Korlátazonosító** | **Korlátot** | **Megjegyzések** |
| --- | --- | --- |
| Teljes kapacitás (beleértve a felhőt is) |Akár 64 TB/virtuális eszköz |A teljes StorSimple virtuális tömbök feladatátvétele egy másik üres tömbre is felhasználható. Ha ugyanarra az eszközre kísérli meg a visszaállítást, győződjön meg arról, hogy elegendő hely van az eszközön a művelet végrehajtásához. Ha túllépte a 32 TB-ot, nem állíthatja vissza ugyanarra az eszközre. |
| A Storage-fiók hitelesítő adatainak maximális száma eszközönként |1 | |
| Kötetek/megosztások maximális száma |16 | |
| Többszintű megosztás minimális mérete |500 GB | |
| Egy többszintű kötet minimális mérete |500 GB | |
| Lépcsőzetes megosztás maximális mérete |20 TB | |
| Egy rétegű kötet maximális mérete |5 TB | |
| Egy helyileg rögzített megosztás minimális mérete |50 GB | |
| Egy helyileg rögzített kötet minimális mérete |50 GB | |
| Egy helyileg rögzített megosztás maximális mérete |2 TB | |
| Egy helyileg rögzített kötet maximális mérete |200 GB | |
| A kezdeményezők által létesített iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| A virtuális eszköz által a *. Backups* mappában megőrzött biztonsági másolatok maximális száma a fájlkiszolgálón |5 |Ez magában foglalja a legutóbbi ütemezett (az alapértelmezett biztonsági mentési házirend által generált) és a manuális biztonsági mentéseket. |
| Az eszköz által megőrzött ütemezett biztonsági mentések maximális száma |55 |30 napi biztonsági mentés<br>12 havi biztonsági mentés<br>13 éves biztonsági mentések |
| Az eszköz által megőrzött manuális biztonsági másolatok maximális száma |45 | |
| Fájlmegosztás maximális száma a fájlkiszolgálón |1 millió |Az eszköz visszaállításakor a visszaállítási idő arányos a fájlok számával az eszközön található összes megosztáson. |
| Fájlok maximális száma köteten egy iSCSI-kiszolgáló esetében |1 millió | |
| Fájlok maximális száma virtuális tömbben |4 000 000 | |
| Visszaállítási idő helyreállítása |Gyors visszaállítás |A visszaállítás a Heat térképen alapul, és a kötet méretétől függ.<br>A biztonsági mentési műveletek akkor fordulhatnak elő, ha folyamatban van egy visszaállítási művelet. |

