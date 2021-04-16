---
title: Fájlok és mappák lassú biztonsági mentésének hibaelhárítása
description: Hibaelhárítási útmutatást nyújt a teljesítményproblémák Azure Backup diagnosztizálásához
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518524"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása

Ez a cikk hibaelhárítási útmutatást nyújt a fájlok és mappák lassú biztonsági mentési teljesítményének diagnosztizálásához a fájlok és mappák Azure Backup. Ha a Azure Backup használja a fájlok biztonsági mentésére, a biztonsági mentési folyamat a vártnál hosszabb időt is igénybe vehet. Ezt a késést a következők valamelyike okozhatja:

* [Teljesítménybeli szűk keresztmetszetek vannak a biztonságimentett számítógépen.](#cause1)
* [Egy másik folyamat vagy víruskereső szoftver is zavarja a Azure Backup folyamatában.](#cause2)
* [A Backup ügynök egy Azure-beli virtuális gépen (VM) fut.](#cause3)  
* [Nagy számú (millió) fájlról biztonsági mentése van.](#cause4)

Mielőtt elkezdené a hibaelhárítást, javasoljuk, hogy töltse le és telepítse a legújabb [Azure Backup ügynököt.](https://aka.ms/azurebackup_agent) Gyakran frissítik a Backup ügynököt, hogy kijavítsuk a különböző problémákat, funkciókat adjunk hozzá, és javítsuk a teljesítményt.

Javasoljuk továbbá, hogy tekintse át a [Azure Backup](backup-azure-backup-faq.yml) szolgáltatás gyakori kérdéseit, és győződjön meg arról, hogy nem tapasztalja a gyakori konfigurációs problémákat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Ok: A biztonsági mentési feladat nem optimalizált módban fut

* A MARS-ügynök az USN (frissítési sorszám) módosítási naplójának  vagy nem optimalizált módjának használatával futtathatja a biztonsági mentési feladatot a könyvtárak vagy fájlok változásainak ellenőrzéséhez a teljes kötet vizsgálatával. 
* Az optimalizált mód lassú, mert az ügynöknek a kötet minden egyes fájlját be kell olvasnia, és össze kell hasonlítani a metaadatokkal a módosított fájlok meghatározásához.
* Ennek ellenőrzéséhez  nyissa meg a Feladat részleteit a MARS-ügynökkonzolon, és ellenőrizze az állapotát, hogy az Adatok átvitele **(nem optimalizált,** több időt is vegyen majd) állapot jelenik-e meg az alábbi módon:

    ![Futtatás nem optimalizált módban](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Az alábbi feltételek azt okozhatják, hogy a biztonsági mentési feladat nem optimalizált módban fut:
  * Az első biztonsági mentés (más néven kezdeti replikáció) mindig nem optimalizált módban fog futni
  * Ha az előző biztonsági mentési feladat sikertelen, akkor a következő ütemezett biztonsági mentési feladat nem optimalizáltként fog futni.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Ok: Teljesítménybeli szűk keresztmetszetek a számítógépen

A biztonságimentés alatt található számítógép szűk keresztmetszetei késéseket okozhatnak. A számítógép lemezírási vagy olvasási képessége, illetve az adatok hálózaton keresztüli küldhető sávszélessége szűk keresztmetszetet okozhat.

A Windows beépített Teljesítményfigyelő [(Teljesítményfigyelő)](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) nevű eszközt biztosít a szűk keresztmetszetek észleléséhez.

Íme néhány teljesítményszámláló és tartomány, amelyek hasznosak lehetnek az optimális biztonsági mentések szűk keresztmetszeteinek diagnosztizálásában.

| Számláló | Állapot |
| --- | --- |
| Logikai lemez (fizikai lemez) – %tétlen |<li> 100% tétlen és 50% tétlen = Kifogástalan</br><li> 49% tétlen és 20% tétlen = Figyelmeztetés vagy figyelő</br><li> 19% tétlen és 0% tétlen = Kritikus vagy Nincs meg a specifikáció |
| Logikai lemez (fizikai lemez)- %Avg. Disk Sec Read or Write |<li> 0,001 ms és 0,015 ms között = Kifogástalan</br><li> 0,015 ms és 0,025 ms között = Figyelmeztetés vagy Figyelő</br><li> 0,026 ms vagy több = Kritikus vagy Nincs specifikáció |
| Logikai lemez (fizikai lemez) – A lemezsor aktuális hossza (minden példányhoz) |6 percnél több mint 80 kérés |
| Memória – Készlet , nem lapzó bájtok |<li> A felhasznált készlet kevesebb mint 60%-a = Kifogástalan<br><li> A felhasznált készlet 61–80%-a = Figyelmeztetés vagy figyelő</br><li> 80%-nál nagyobb felhasznált készlet = Kritikus vagy Nincs meg a specifikáció |
| Memória – Készlet lapszámba bájtja |<li> A felhasznált készlet kevesebb mint 60%-a = Kifogástalan</br><li> A felhasznált készlet 61–80%-a = Figyelmeztetés vagy figyelő</br><li> 80%-nál nagyobb felhasznált készlet = Kritikus vagy Nincs meg a specifikáció |
| Memória – Rendelkezésre álló megabájtok |<li> A rendelkezésre álló vagy több szabad memória 50%-a = Kifogástalan</br><li> A rendelkezésre álló szabad memória 25%-a = Figyelő</br><li>A rendelkezésre álló szabad memória 10%-a = Figyelmeztetés</br><li> Kevesebb, mint 100 MB vagy a rendelkezésre álló szabad memória 5%-a = Kritikus vagy Nincs megszabadulva |
| Processzor – \% Processzoridő (minden példány) |<li> Kevesebb mint 60% felhasználva = Kifogástalan</br><li> 61%–90% felhasználva = Figyelés vagy figyelem</br><li> 91% és 100% között felhasználva = Kritikus |

> [!NOTE]
> Ha úgy dönt, hogy az infrastruktúra a felelős, javasoljuk, hogy a jobb teljesítmény érdekében rendszeresen töredezettségmentessé teszi a lemezeket.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Ok: Egy másik folyamat vagy víruskereső szoftver zavarja a Azure Backup

Több olyan példányt is láttunk, ahol a Windows rendszer más folyamatai negatívan befolyásolták az ügynökfolyamat Azure Backup teljesítményét. Ha például az Azure Backup-ügynököt és egy másik programot is használ az adatok biztonsági mentése során, vagy ha a víruskereső szoftver fut, és zárolja a biztonsági mentéset, a fájlok több zárolása is okozhatja a problémát. Ebben az esetben a biztonsági mentés meghiúsulhat, vagy a feladat a vártnál tovább tarthat.

Ebben a forgatókönyvben a legjobb javaslat az, hogy kapcsolja ki a másik biztonsági mentési programot, és nézze meg, hogy változik-e Azure Backup biztonsági mentési ideje. Ahhoz, hogy ne egyszerre több biztonsági mentési feladat is futjon, az általában elegendő ahhoz, hogy ne legyen hatással egymásra.

Víruskereső programokhoz javasoljuk, hogy zárja ki a következő fájlokat és helyeket:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappák
* Az új hely (ha nem a szokásos helyet használja)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Ok: Egy Azure-beli virtuális gépen futó biztonsági mentési ügynök

Ha a Backup ügynököt egy virtuális gépen futtatja, a teljesítmény lassabb lesz, mint amikor fizikai gépen futtatja. Ez az IOPS-korlátozások miatt várható.  Azonban optimalizálhatja a teljesítményt, ha az Azure-beli virtuális gépre átkapcsolja a biztonsági Premium Storage. Dolgozunk a probléma megoldásán, és a javítás egy későbbi kiadásban lesz elérhető.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Ok: Nagy számú (több millió) fájl biztonsági mentése

Nagy mennyiségű adat mozgatása hosszabb időt fog igénybe venni, mint egy kisebb adatmennyiség mozgatása. Bizonyos esetekben a biztonsági mentés ideje nem csupán az adatok méretéhez, hanem a fájlok vagy mappák számhoz is kapcsolódik. Ez különösen akkor igaz, ha több millió kis fájlról (néhány bájtról néhány kilobájtra) készül biztonsági mentése.

Ez a viselkedés azért fordul elő, mert az adatok biztonsági mentése és Azure-ba való áthelyezése során az Azure egyidejűleg katalogizálja a fájlokat. Bizonyos ritka esetekben a katalógusművelet a vártnál tovább tarthat.

Az alábbi mutatók segíthetnek megérteni a szűk keresztmetszetet, és ennek megfelelően dolgozhatnak a következő lépéseken:

* **A felhasználói felület az adatátvitel előrehaladását mutatja.** Az adatok átvitele még folyamatban van. A hálózati sávszélesség vagy az adatok mérete késést okozhat.
* **A felhasználói felület nem mutatja az adatátvitel előrehaladását.** Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp mappában található naplókat, majd ellenőrizze a FileProvider::EndData bejegyzést a naplókban. Ez a bejegyzés azt jelzi, hogy az adatátvitel befejeződött, és a katalógusművelet zajlik. Ne szakítsa meg a biztonsági mentési feladatokat. Ehelyett várjon egy kicsit, amíg a katalógusművelet befejeződik. Ha a probléma továbbra is fennáll, lépjen kapcsolatba a [Azure-támogatás.](https://portal.azure.com/#create/Microsoft.Support)

Ha nagy méretű lemezekről próbál biztonsági másolatot készíteni, ajánlott a Azure Data Box az első biztonsági mentéshez [(kezdeti](./offline-backup-azure-data-box.md) replikáció).  Ha nem tudja használni a Data Box, akkor a hálózaton keresztüli hosszú adatátvitel során a környezetben esetlegesen átmeneti hálózati problémák biztonsági mentési hibákat okozhatnak.  A hibák elleni védelem érdekében hozzáadhat néhány mappát a kezdeti biztonsági mentéshez, és növekményesen további mappákat adhat hozzá, amíg az összes mappáról sikeresen biztonsági másolatot nem készít az Azure-ba.  A további növekményes biztonsági mentések viszonylag gyorsabbak lesznek.

## <a name="next-steps"></a>Következő lépések

* [Gyakori kérdések a fájlok és mappák biztonsági mentése ről](backup-azure-file-folder-backup-faq.yml)
