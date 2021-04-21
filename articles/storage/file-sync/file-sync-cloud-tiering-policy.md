---
title: Azure File Sync rétegezésére vonatkozó szabályzatok | Microsoft Docs
description: Részletek arról, hogy a dátum- és kötetterület-szabadító szabályzatok hogyan működnek együtt a különböző forgatókönyvekben.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797039"
---
# <a name="cloud-tiering-policies"></a>Felhőbeli rétegezés szabályzatok

A felhőbeli rétegezés két szabályzatot tartalmaz, amelyek  meghatározzák, hogy mely fájlok rétegzettek a felhőbe: a kötet szabad területére vonatkozó szabályzatot és a **dátum szabályzatot.**

A **kötet szabad területére vonatkozó házirend** gondoskodik arról, hogy a kiszolgálóvégpontot tároló helyi kötet egy meghatározott százaléka mindig szabad marad. 

A **dátum-szabályzatszintek** fájljainak legutóbbi elérése x nappal ezelőtt vagy később történt. A kötet szabad terület házirendje mindig elsőbbséget élvez; ha nincs elegendő szabad terület a köteten a dátumházi szabályzatban leírt mennyiségű fájl tárolására, a Azure File Sync felülírja a dátumházi szabályzatot, és a köteten lévő szabad terület százalékos arányának teljes végéig folytatja a leghűbb fájlok rétegezését.

## <a name="how-both-policies-work-together"></a>A két szabályzat együttesen

Példaként bemutatjuk, hogyan működnek ezek a szabályzatok: Tegyük fel, hogy az Azure File Sync-t egy 500 GB-os helyi köteten konfigurálta, és a felhőbeli rétegezés soha nem volt engedélyezve. A fájlmegosztás a következő fájlokat tartalmazza:

|Fájlnév |Utolsó hozzáférés ideje  |Fájlméret  |Tárolt |
|----------|------------------|-----------|----------|
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló- és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló- és Azure-fájlmegosztás
|3. fájl    | 1 évvel ezelőtt | 200 GB | Kiszolgáló- és Azure-fájlmegosztás
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Kiszolgáló- és Azure-fájlmegosztás
|5. fájl    | 2 év, 1 nappal ezelőtt | 140 GB | Kiszolgáló- és Azure-fájlmegosztás

**1. módosítás:** Engedélyezte a felhőbeli rétegezést, beállított egy 20%-os kötet szabadterület-szabályzatot, és letiltotta a dátum szabályzatot. Ezzel a konfigurációval a felhőbeli rétegezés 20%(ebben az esetben 100 GB) szabad helyet biztosít, és elérhető a helyi gépen. Ennek eredményeképpen a helyi gyorsítótár teljes kapacitása 400 GB. Ez a 400 GB fogja tárolni a legutóbb és leggyakrabban használt fájlokat a helyi köteten.

Ezzel a konfigurációval a rendszer csak az 1–4. fájlt tárolja a helyi gyorsítótárban, az 5. fájlt pedig rétegzetten. Ez csak 370 GB a használható 400 GB-os kapacitásból. Az 5. fájl 140 GB, és meghaladná a helyi gyorsítótárazott 400 GB-os korlátot. 

**2. módosítás:** Mondja, hogy egy felhasználó hozzáfér az 5. fájlhoz. Így az 5. fájl lesz a megosztásban legutóbb elért fájl. Ennek eredményeképpen a rendszer a helyi gyorsítótárban tárolja az 5. fájlt, és a 400 GB-os korlát alá fér el, a 4. fájlt rétegzettre. Az alábbi táblázat bemutatja, hol tárolja a rendszer a fájlokat a következő frissítésekkel:

|Fájlnév |Utolsó hozzáférés ideje  |Fájlméret  |Tárolt |
|----------|------------------|-----------|----------|
|5. fájl    | 2 órával ezelőtt | 140 GB | Kiszolgáló- és Azure-fájlmegosztás
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló- és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló- és Azure-fájlmegosztás
|3. fájl    | 1 évvel ezelőtt | 200 GB | Kiszolgáló- és Azure-fájlmegosztás
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Helyi szinten rétegzett Azure-fájlmegosztás

**3. módosítás:** Tegyük fel, hogy frissítette a szabályzatokat, így a dátumalapú rétegezés szabályzata 60 nap, a kötet szabad területére vonatkozó szabályzat pedig 70%. Mostantól legfeljebb 150 GB tárolható a helyi gyorsítótárban. Bár a 2. fájlhoz kevesebb, mint 60 napja fértek hozzá, a kötet szabad területének szabályzata felülírja a dátum-szabályzatot, és a 2. fájl rétegzett, hogy fenntartsa a 70%-os helyi szabad területet.

**4. módosítás:** Ha a kötet szabad területére vonatkozó szabályzatot 20%-ra módosította, majd a segítségével visszahívta a helyi meghajtón elférő összes fájlt a felhőbeli rétegző szabályzatok használata közben, a táblázat a következőhöz hasonló `Invoke-StorageSyncFileRecall` lesz:

|Fájlnév |Utolsó hozzáférés ideje  |Fájlméret  |Tárolt |
|----------|------------------|-----------|----------|
|5. fájl    | 1 órával ezelőtt  | 140 GB | Kiszolgáló- és Azure-fájlmegosztás
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló- és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló- és Azure-fájlmegosztás
|3. fájl    | 1 évvel ezelőtt | 200 GB | Helyi szinten rétegzett Azure-fájlmegosztás
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Helyi szinten rétegzett Azure-fájlmegosztás

Ebben az esetben az 1., 2. és 5. fájlok helyileg lesznek gyorsítótárazva, a 3. és a 4. fájlok pedig rétegezettek. Mivel a dátum házirend 60 nap, a 3. és a 4. fájl rétegzett, még akkor is, ha a kötet szabad területére vonatkozó házirend akár 400 GB-ot is lehetővé tesz helyileg.

> [!NOTE] 
> A fájlok nem lesznek automatikusan visszahívva, ha az ügyfelek kisebb értékre módosítják a kötet szabad területére vonatkozó házirendet (például 20%-tól 10%-ig). vagy módosítsa a dátum házirendet egy nagyobb értékre (például 20 napról 50 napra).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Több kiszolgálóvégpont egy helyi köteten

A felhőbeli rétegezés több kiszolgálóvégponthoz is engedélyezhető egyetlen helyi köteten. Ehhez a konfigurációhoz azonos mennyiségű szabad kötetet kell beállítania az ugyanazon a köteten található összes kiszolgálóvégpont számára. Ha különböző kötet-szabadhely-házirendeket ad meg több kiszolgálóvégponthoz ugyanazon a köteten, a legnagyobb szabad kötetterület százalékos aránya élvez elsőbbséget. Ezt a kötet szabad **területének hatályos szabályzatának nevezzük.** Ha például három kiszolgálóvégpont található ugyanazon a helyi köteten, az egyik 15%-ra, a másik 20%-ra, a harmadik pedig 30%-ra van állítva, akkor mindegyik a leghűébb fájlok rétegbe való rétegét veszi fel, ha 30%-nál kevesebb szabad terület áll rendelkezésre.

## <a name="next-steps"></a>Következő lépések

* [Felhőbeli rétegezés monitorozása](file-sync-monitor-cloud-tiering.md)
