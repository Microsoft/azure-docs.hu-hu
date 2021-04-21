---
title: Felhőbeli Azure File Sync szabályzatok kiválasztása | Microsoft Docs
description: Annak részletei, hogy mit kell szem előtt tartani a felhőbeli Azure File Sync szabályzatok kiválasztásakor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797057"
---
# <a name="choose-cloud-tiering-policies"></a>Felhőbeli rétegezés szabályzatának kiválasztása

Ez a cikk útmutatást nyújt a felhőbeli rétegezés szabályzatának kiválasztásához és módosításához. A cikk elolvasása előtt győződjön meg arról, hogy megértette a felhőbeli rétegezés működését. A felhőbeli rétegezés alapjaiért lásd: [A Azure File Sync rétegezésének alapjai.](file-sync-cloud-tiering-overview.md) A felhőbeli rétegezésre vonatkozó szabályzatok példákkal való részletes magyarázatát a [felhőbeli Azure File Sync szabályzatokat.](file-sync-cloud-tiering-policy.md)

## <a name="limitations"></a>Korlátozások
- A felhőbeli rétegezés nem támogatott a Windows rendszerköteten.

- Ha kötetszintű FSRM-kvótával van, továbbra is engedélyezheti a felhőbeli rétegezést. Az FSRM-kvóta beállítása után a hívható szabad terület lekérdezési API-k a kvótabeállításnak megfelelő automatikusan jelentik a köteten a szabad területet. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>A rétegzett fájlok minimális fájlmérete

A 9-es és újabb ügynökverziók esetében a rétegzett fájlok minimális fájlmérete a fájlrendszerfürt méretétől függ. A felhőbeli rétegezésre jogosult minimális fájlméret kiszámítása a fürt méretének 2-ére és legalább 8 KB-ra van kiszámítva. Az alábbi táblázat a kötetfürt mérete alapján rétegzett minimális fájlméreteket mutatja be:

|Kötetfürt mérete (bájt) |Az ilyen vagy nagyobb méretű fájlok rétege is lehet  |
|----------------------------|---------|
|4 KB vagy kisebb (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

A 12-es Azure File Sync legfeljebb 2 MB méretű fürtök támogatottak, de nagyobb méretek esetén a felhőbeli rétegezés nem működik.

A Windows által használt összes fájlrendszer rendszerezze a merevlemezt a fürt mérete (más néven a foglalási egység mérete) alapján. A fürtméret a fájlhoz használható legkisebb lemezterületet jelöli. Ha a fájlméretek nem a fürtméret többszörösének halmazát használják, a fájlnak további helyet kell használnia – a fürtméret következő többszörösénekig.

Azure File Sync windows Server 2012 R2 és újabb rendszerű NTFS-köteteken támogatott. Az alábbi táblázat ismerteti az alapértelmezett fürtméreteket, amikor új NTFS-kötetet hoz létre a Windows Server 2019-ben.

|Kötet mérete    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 KB                |
|16 TB – 32 TB   | 8 KB                |
|32 TB – 64 TB   | 16 KB               |
|64 TB – 128 TB  | 32 KB               |
|128 TB – 256 TB | 64 KB (korábbi maximum) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB – 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (maximális méret)  |
|> 8 TB         | nem támogatott       |

Lehetséges, hogy a kötet létrehozásakor manuálisan formázta a kötetet más fürtmérettel. Ha a kötet a Windows egy régebbi verziójából ered, az alapértelmezett fürtméretek is eltérőek lehetnek. [Ez a cikk az alapértelmezett fürtméretekkel kapcsolatos további részleteket tartalmaz.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Még ha 4 KB-osnál kisebb fürtméretet is választ, a rendszer a 8 KB-os korlátot alkalmazza a rétegzett legkisebb fájlméretként. (Még akkor is, ha technikailag a 2x fürtméret 8 KB-osnál kisebb lenne.)

Az abszolút minimum oka a rendkívül kis méretű – 1–4 KB méretű – fájlok NTFS-tárolója. A kötet egyéb paramétereitől függően előfordulhat, hogy a kis méretű fájlok egyáltalán nem tárolódnak lemezfürtökben. Az ilyen fájlokat valószínűleg hatékonyabb közvetlenül a kötet fő fájltáblájában vagy az "MFT-rekordban" tárolni. A felhőbeli rétegezés újraelemzési pontja mindig lemezen van tárolva, és pontosan egy fürtöt vesz fel. Az ilyen kis fájlok rétegezése helymegtakarítás nélkül is végződhet. A szélsőséges esetek akár több helyet is kihasználnak, ha engedélyezve van a felhőbeli rétegezés. Ennek védelme érdekében a felhőbeli rétegezés által rétegzett fájl legkisebb mérete 8 KB 4 KB vagy kisebb fürtméret esetén. 

## <a name="selecting-your-initial-policies"></a>A kezdeti szabályzatok kiválasztása

Ha egy kiszolgálóvégponton engedélyezi a felhőbeli rétegezést, általában minden egyes kiszolgálóvégponthoz létre kell hoznia egy helyi virtuális meghajtót. A kiszolgálóvégpont el különülve könnyebben megértheti a felhőbeli rétegezés működését, és ennek megfelelően módosíthatja a szabályzatokat. A Azure File Sync azonban akkor is működik, ha több kiszolgálóvégpont található ugyanazon a meghajtón. Részletekért lásd a Több kiszolgálói végpont a helyi [köteten című szakaszt.](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) Azt is javasoljuk, hogy amikor először engedélyezi a felhőbeli rétegezést, tiltsa le a dátum szabályzatot, és a kötet szabad terület szabályzatát körülbelül 10–20%-ban tartsa meg. A legtöbb fájlkiszolgáló kötete esetén általában a 20%-os szabad kötetterület a legjobb megoldás.

Az egyszerűség kedvéért és annak érdekében, hogy egyértelműen megértsük az elemek rétegzettségét, javasoljuk, hogy elsősorban a köteten lévő szabad terület szabályzatát módosítsa, és szükség esetén tiltsa le a dátumra vonatkozó szabályzatot. Ezt azért javasoljuk, mert a legtöbb ügyfél értékesnek találja, hogy a helyi gyorsítótárat a lehető legtöbb gyors fájlba töltse ki, a többit pedig a felhőbe rétegzza. A dátum házirend azonban hasznos lehet, ha proaktív módon szeretne szabadít fel helyi lemezterületet, és tudja, hogy a kiszolgálóvégponton található fájlokat nem kell helyben tartani a dátum házirendben megadott napok száma után. A dátum házirend beállításával értékes helyi lemezkapacitást szabadít fel az ugyanazon a köteten található más végpontok számára, így több fájlt gyorsítótárazhat.

A szabályzatok beállítása után figyelje a ki- és lekért forgalomokat, és ennek megfelelően állítsa be a két szabályzatot. Javasoljuk, hogy  kifejezetten a felhőbeli  rétegezés visszahívási méretét és a felhőbeli rétegezés visszahívási méretét az alkalmazásmetrikák alapján a Azure Monitor. A bejövő forgalom monitorozásának elsajátítása: [Felhőbeli rétegezés monitorozása.](file-sync-monitor-cloud-tiering.md)

## <a name="adjusting-your-policies"></a>A szabályzatok beállítása

Ha az Azure-ból folyamatosan elő visszahívott fájlok mennyisége nagyobb, mint szeretné, akkor előfordulhat, hogy több gyors fájl van, mint amennyit a helyi kiszolgálóköteten menthet. Ha lehetséges, növelje a helyi kötet méretét, és/vagy csökkentse a kötet szabad területére vonatkozó házirend százalékos arányát kis növekményekben. A köteten túl sok szabad terület csökkentése negatív következményekkel is járhat. Az adatkészlet nagyobb lemorzsolódása több szabad helyet igényel – az új fájlokhoz és a "hideg" fájlok visszahívása. A rétegezés legfeljebb egy órás késéssel indul el, és feldolgozási időre van szükség, ezért mindig elegendő szabad területnek kell lennie a köteten.

A több adat helyi szinten való tárolása alacsonyabb bejövő adatmennyiséget jelent, mivel kevesebb fájl lesz visszahívva az Azure-ból, de nagyobb mennyiségű helyszíni tárterületre van szükség, ami saját költséggel jár. 

A kötet szabad területére vonatkozó szabályzat módosításával a helyi szinten tartandó adatok mennyiségét a következő tényezők határozzák meg: a sávszélesség, az adatkészlet hozzáférési mintája és a költségvetés. Alacsony sávszélességű kapcsolat esetén több helyi adatra lehet szükség, hogy minimális késést biztosítsunk a felhasználók számára. Ellenkező esetben a lemorzsolódási arányra alapozhatja az adott időszakban. Ha például tudja, hogy az 1 TB-os adatkészlet 10%-a változik vagy minden hónapban aktívan hozzáfér, akkor előfordulhat, hogy 100 GB helyi szinten szeretné tartani a fájlokat, hogy ne tudja gyakran visszahívni a fájlokat. Ha a kötet 2 TB, akkor 5%-ot (vagy 100 GB-ot) szeretne helyi szinten tartani, ami azt jelenti, hogy a fennmaradó 95% a kötet szabad területének százalékos aránya. Érdemes azonban puffert hozzáadni a nagyobb adatváltozású időszakokhoz , azaz nagyobb szabad kötetmennyiség-százalékkal kezdve, majd szükség esetén később módosítani.

## <a name="standard-operating-procedures"></a>Szabványos üzemeltetési eljárások

- A felhőbeli rétegzés az Azure Files való Azure File Sync a kezdeti feltöltéstől függ.
- A felhőbeli rétegezés 60 percenként ellenőrzi a kötet szabad területének és dátum-szabályzatának megfelelőségét
- Az /LFSM kapcsoló használata a Robocopyn a fájlok áttelepítése során lehetővé teszi a fájlok szinkronizálását, a felhőbeli rétegzés pedig lemezterületet biztosít a kezdeti feltöltés során 
- Ha a rétegezés még az hőtérkép létrejötte előtt történik, a fájlok az utolsó módosítás időbélyege szerint lesznek rétegzettek

## <a name="next-steps"></a>Következő lépések

* [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
