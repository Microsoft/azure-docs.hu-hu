---
title: Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
description: Hibaelhárítási útmutató segít az Azure Backup teljesítménybeli problémák okának biztosít
services: backup
author: genlin
manager: cshepard
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f24a60ab9bdcf1231085de4edeeb89ce1edf4e80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337629"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
Ez a cikk segítségével diagnosztizálhatja a fájlok és mappák lassú biztonsági mentési teljesítmény okának, amikor az Azure Backup használata a hibaelhárítási útmutatót. Fájlok biztonsági mentése az Azure Backup-ügynök használatakor a biztonsági mentési folyamat a vártnál hosszabb vehet igénybe. Ez a késleltetés oka lehet egy vagy több, a következők:

* [Nincsenek a teljesítmény szűk azon a számítógépen, a másolat készül.](#cause1)
* [Egy másik folyamat vagy víruskereső zavarja az Azure biztonsági mentési folyamat.](#cause2)
* [A Backup szolgáltatás ügynökének futása egy Azure virtuális gépen (VM).](#cause3)  
* [Biztonsági másolatot egy nagy mennyiségű (több millió) fájlt.](#cause4)

Mielőtt elkezdené kapcsolatos hibák elhárítása, azt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup szolgáltatás ügynökének](https://aka.ms/azurebackup_agent). Gyakori frissítések különböző kapcsolatos problémák megoldása, a szolgáltatások hozzáadása és a teljesítmény javítása a biztonsági mentési ügynök VÁLLALUNK.

Emellett javasoljuk, hogy tekintse át a [Azure Backup szolgáltatás – gyakori kérdések](backup-azure-backup-faq.md) , hogy nem oldják a gyakori konfigurációs hibáinak bármelyikét.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>OK: A számítógépen a teljesítmény szűk
Azon a számítógépen, a másolat készül a szűk keresztmetszetek késéseket okozhat. Például a számítógép képes olvasni vagy írni lemezre vagy adatokat küldeni a hálózaton keresztül a rendelkezésre álló sávszélesség az szűk keresztmetszeteket okozhat.

Windows beépített eszközt, amelynek a neve biztosít [Teljesítményfigyelő](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Teljesítményfigyelő) észleli ezeket a szűk keresztmetszeteket.

Íme, néhány teljesítményszámlálók és az optimális biztonsági mentésekhez szűk diagnosztizálásakor hasznos lehet,-címtartományokat.

| A számláló | Állapot |
| --- | --- |
| Logikai lemez (a fizikai lemez) – üresjárati % |• 100 %-os üresjárati 50 %-os üresjárati = kifogástalan</br>• 49 % 20 %-os üresjárati üresjárati = figyelmeztetés vagy a figyelő</br>• 19: % 0 % üresjárati üresjárati = kritikus vagy kívüli specifikációja |
| Logikai lemez (a fizikai lemez) – % átlagos száma Lemez mp Olvasás vagy írás |• 0,015 MS 0,001 ms = kifogástalan</br>• 0,025 MS 0,015 ms = figyelmeztetés vagy a figyelő</br>• 0.026 ms vagy kritikus vagy kívüli specifikációja már = |
| Logikai lemez (a fizikai lemez) – a Lemezvárólista jelenlegi hossza (az összes példányra) |80 kérelem 6 percnél hosszabb ideig |
| Memória – nem készlet mérete (bájt) |• Legfeljebb 60 %-ban felhasznált készlet = kifogástalan<br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint 80 %-os készlet felhasznált = kritikus vagy kívüli specifikációja |
| Memória – készlet mérete (bájt) |• Legfeljebb 60 %-ban felhasznált készlet = kifogástalan</br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint 80 %-os készlet felhasznált = kritikus vagy kívüli specifikációja |
| Memória – rendelkezésre álló hely MB-ban |• 50 %-a rendelkezésre álló szabad memória vagy további = kifogástalan</br>• 25 %-a rendelkezésre álló szabad memória = figyelője</br>• 10 %-a rendelkezésre álló szabad memória figyelmeztetés =</br>• Kevesebb mint 100 MB-os vagy 5 %-a rendelkezésre álló szabad memória = kritikus vagy kívüli specifikációja |
| Processzor –\%processzor kihasználtsága (összes) |• 60 %-nál kisebb felhasznált = kifogástalan</br>• 61 90 %-felhasznált = figyelő vagy figyelmeztetés</br>• 91 – 100 % felhasznált kritikus = |

> [!NOTE]
> Ha azt állapítja meg, hogy az infrastruktúra képes legyen a probléma oka, azt javasoljuk, hogy a lemezek, a jobb teljesítmény érdekében rendszeresen töredezettségmentesítése.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>OK: Egy másik folyamat vagy víruskereső zavarja a folyamatot az Azure Backup szolgáltatással
Megtudtuk, hogy több példányt, ahol más folyamatok, a Windows rendszerben rendelkezik az zavarhatja az Azure Backup ügynök folyamat teljesítményét. Például ha az Azure Backup ügynök és a egy másik program használja, a biztonsági másolatokat, vagy ha víruskereső fut, és zárolta a fájlok biztonsági mentése, az a többszörös zárolja a fájlok okozhat a versengés. Ebben a helyzetben a biztonsági mentés sikertelen lehet, vagy a feladat a vártnál hosszabb ideig tarthat.

Ebben a forgatókönyvben a legjobb javaslat, hogy kapcsolja ki a másik biztonsági mentési program meg, hogy az Azure Backup-ügynök biztonsági mentésének ideje változik. Általában gondoskodik róla, hogy több biztonsági mentési feladat nem fut egyszerre is használhatók, nehogy azok egymással.

A víruskeresők azt javasoljuk, hogy a következő fájlok és helyek kizárása:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappák
* Ideiglenes hely (Ha nem használ a szokásos helyre)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>OK: Egy Azure virtuális gépen futó biztonsági mentési ügynök
A Backup szolgáltatás ügynökét a virtuális gép futtatja, ha a teljesítmény lassabb, mint ha is futtat, a fizikai gép lesz. Ez várható IOPS-korlátozások miatt.  Azonban optimalizálhatja a teljesítményt az adatmeghajtók, amely éppen készül biztonsági másolat az Azure Premium Storage között. Dolgozunk a probléma javítása, és a javítás egy későbbi kiadásban elérhető lesz.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>OK: Nagyszámú (több millió) fájlok biztonsági mentésével
Nagy mennyiségű adat áthelyezését-nál kisebb mennyiségű adatot áthelyezése hosszabb ideig tart. Bizonyos esetekben a biztonsági mentés időpontja nem csupán az adatok, hanem a fájlok és mappák száma méretének kapcsolódik. Ez akkor különösen igaz olyankor, amikor a kisméretű fájlok (és a néhány kilobájt néhány bájt) több millió éppen készül biztonsági másolat.

Ez az oka, hogy közben az adatok biztonsági mentése és áthelyezi az Azure-ba, Azure egyidejűleg katalogizálni a fájlokat. Egyes ritka esetekben a katalógus műveletet is igénybe vehet a vártnál hosszabb.

A következő mutatók segítségével megismerheti a szűk keresztmetszetet, és ennek megfelelően működjön a következő lépéseket:

* **Felhasználói felület folyamatban láthatók az adatátvitel**. Az adatok átkerülnek még mindig folyamatban van. A hálózati sávszélességet vagy az adatok mérete, előfordulhat, hogy lehet késedelmet okoz.
* **Felhasználói felület folyamat nem jelenik a adatátvitel**. Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp található naplókat, és ellenőrizze a naplókat a FileProvider::EndData bejegyzést. Ez a bejegyzés azt jelzi, hogy, hogy az adatátvitel befejeződött, és a katalógus művelet történik. A biztonsági mentési feladatok ne szakítsa meg. Inkább várja meg, már egy kicsit a katalógus művelet befejeződik. Ha a probléma tartósan fennáll, forduljon a [az Azure-támogatás](https://portal.azure.com/#create/Microsoft.Support).
