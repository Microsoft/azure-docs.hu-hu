---
title: Azure File Sync Cloud-rétegek házirendjei | Microsoft Docs
description: Részletes információk arról, hogy a dátum és a mennyiség szabad területére vonatkozó szabályzatok hogyan működnek együtt a különböző forgatókönyvek esetében.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204357"
---
# <a name="cloud-tiering-policies"></a>Felhő-rétegű házirendek

A Felhőbeli rétegek két házirenddel rendelkeznek, amelyek meghatározzák, hogy mely fájlok legyenek a felhőbe bontva: a **kötet szabad területére vonatkozó házirend** és a **dátum házirendje**.

A **kötet szabad területére vonatkozó házirend** biztosítja, hogy a helyi kötet megadott százaléka, amely a kiszolgálói végpontot tárolja, mindig ingyenes marad. 

A **dátum-és időszabályzatok** a fájlok utolsó x napja vagy később való elérését. A kötet szabad területére vonatkozó szabályzat mindig elsőbbséget élvez; Ha nincs elég szabad terület a köteten, hogy annyi napig érdemes fájlokat tárolni, ahogy azt a dátum házirend írja le, Azure File Sync felülbírálja a dátum-házirendet, és folytatja a leghidegebb fájlok kiválasztását, amíg a kötet szabad területének százalékos értéke nem teljesül.

## <a name="how-both-policies-work-together"></a>Hogyan működnek együtt mindkét szabályzat

Egy példát mutatunk be, hogy hogyan működnek ezek a szabályzatok: tegyük fel, hogy egy 500 GB-os helyi köteten konfigurálta Azure File Sync, és a felhő-rétegek létrehozása soha nem volt engedélyezve. Ezek a fájlmegosztás fájljai:

|Fájlnév |Utolsó hozzáférés időpontja  |Fájlméret  |Tárolás helye |
|----------|------------------|-----------|----------|
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló-és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló-és Azure-fájlmegosztás
|3. fájl    | 1 éve | 200 GB | Kiszolgáló-és Azure-fájlmegosztás
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Kiszolgáló-és Azure-fájlmegosztás
|5. fájl    | 2 év, 1 nappal ezelőtt | 140 GB | Kiszolgáló-és Azure-fájlmegosztás

**1. változás:** Engedélyezte a felhő-előállítók használatát, a kötet szabad területére vonatkozó szabályzatot 20%-ra állítja, és a házirendet letiltotta. Ezzel a konfigurációval a Felhőbeli rétegek 20%-ot biztosítanak (ebben az esetben a 100 GB-ot), és a helyi gépen ingyenesen elérhetővé válnak. Ennek eredményeképpen a helyi gyorsítótár teljes kapacitása 400 GB. Az 400 GB a legutóbb és a leggyakrabban használt fájlokat fogja tárolni a helyi köteten.

Ebben a konfigurációban csak az 1 – 4. fájlokat fogja tárolni a helyi gyorsítótárban, és az 5. fájlt a rendszer lépcsőzetesen tárolja. Ez csak az 400 GB-ot tartalmazó 370 GB lehet. Az 5. fájl 140 GB, és meghaladja a 400 GB-os korlátot, ha a helyi gyorsítótárba került. 

**2. változás:** Tegyük fel, hogy egy felhasználó hozzáfér az 5. fájlhoz. Így a fájl 5 a megosztásban legutóbb használt fájl. Ennek eredményeképpen az 5. fájl a helyi gyorsítótárban lesz tárolva, és az 400 GB-os korlát alá fér hozzá, a 4-es fájl pedig lépcsőzetesen fog megjelenni. Az alábbi táblázat a fájlok tárolásának helyét mutatja be a következő frissítésekkel:

|Fájlnév |Utolsó hozzáférés időpontja  |Fájlméret  |Tárolás helye |
|----------|------------------|-----------|----------|
|5. fájl    | 2 órával ezelőtt | 140 GB | Kiszolgáló-és Azure-fájlmegosztás
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló-és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló-és Azure-fájlmegosztás
|3. fájl    | 1 éve | 200 GB | Kiszolgáló-és Azure-fájlmegosztás
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Azure-fájlmegosztás, helyi szinten

**3. változás:** Tegyük fel, hogy frissítette a szabályzatokat, hogy a dátum-és a 60 nap, míg a kötet szabad területének házirendje 70%. Most csak legfeljebb 150 GB lehet a helyi gyorsítótárban tárolni. Bár a 2. fájl már kevesebb mint 60 nappal ezelőtt elérhető, a kötet szabad területének házirendje felülbírálja a dátum házirendet, a 2. fájl pedig az 70%-os helyi szabad terület fenntartására van kiválasztva.

**4. változás:** Ha a kötet szabad területére vonatkozó házirendet 20%-ra módosította, majd `Invoke-StorageSyncFileRecall` felidézi az összes olyan fájlt, amely a helyi meghajtón elfér, miközben a felhő-előfizetői házirendeket betartja, a táblázat a következőképpen fog kinézni:

|Fájlnév |Utolsó hozzáférés időpontja  |Fájlméret  |Tárolás helye |
|----------|------------------|-----------|----------|
|5. fájl    | 1 órával ezelőtt  | 140 GB | Kiszolgáló-és Azure-fájlmegosztás
|1. fájl    | 2 nappal ezelőtt  | 10 GB | Kiszolgáló-és Azure-fájlmegosztás
|2. fájl    | 10 nappal ezelőtt | 30 GB | Kiszolgáló-és Azure-fájlmegosztás
|3. fájl    | 1 éve | 200 GB | Azure-fájlmegosztás, helyi szinten
|4. fájl    | 1 év, 2 nappal ezelőtt | 130 GB | Azure-fájlmegosztás, helyi szinten

Ebben az esetben az 1., 2. és 5. fájlokat a helyi gyorsítótárba helyezi, a 3. és a 4. fájlokat pedig lépcsőzetesen kell megadnia. Mivel a Date Policy értéke 60 nap, a 3. és a 4. fájl a lépcsőzetesen van kiválasztva, bár a kötet szabad területének házirendje akár 400 GB-ot is lehetővé tesz a helyi használatra.

> [!NOTE] 
> A rendszer nem hívja fel automatikusan a fájlokat, ha az ügyfelek kisebb értékre változtatják meg a kötet szabad területére vonatkozó házirendet (például 20%-ról 10%-ra) vagy módosítsa a dátum házirendet nagyobb értékre (például 20 napról 50 napra).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Több kiszolgálói végpont egy helyi köteten

A felhő-rétegek több kiszolgálói végpont számára is engedélyezhetők egyetlen helyi köteten. Ebben a konfigurációban a kötet szabad területét ugyanarra a kötetre kell állítania az összes kiszolgálói végpontnál. Ha különböző mennyiségi szabad területekre vonatkozó házirendeket állít be több kiszolgálói végponthoz ugyanazon a köteten, akkor a legnagyobb mennyiségű szabad terület százalékos aránya elsőbbséget élvez. Ezt nevezzük a **kötet szabad területének érvényes házirendjének**. Ha például három kiszolgálói végpontja van ugyanazon a helyi köteten, egy 15%-ra, egy másik pedig 20%-ra, a harmadik pedig 30%-ra van állítva, akkor mind a leghidegebb fájlok szintjére kerülnek, ha kevesebb, mint 30% szabad terület áll rendelkezésre.

## <a name="next-steps"></a>Következő lépések
* [Felhőbeli rétegek figyelése](storage-sync-monitor-cloud-tiering.md)
