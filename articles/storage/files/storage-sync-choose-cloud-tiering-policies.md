---
title: Azure File Sync felhőalapú rétegek kiválasztására vonatkozó szabályzatok kiválasztása | Microsoft Docs
description: Tekintse át, hogy mit kell szem előtt tartani Azure File Sync a felhőre vonatkozó szabályzatok kiválasztásakor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2ed1b8162c49ccc26cb98dd02897a9c40f809d14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204382"
---
# <a name="choose-cloud-tiering-policies"></a>Válassza a felhő szintű szabályzatok lehetőséget.

Ez a cikk útmutatást nyújt azokhoz a felhasználókhoz, akik kiválasztják és módosítják a felhőalapú rétegű házirendeket. A cikk elolvasása előtt győződjön meg róla, hogy tisztában van a felhő-rétegek működésével. A felhő-rétegek alapjaival kapcsolatban lásd: [Azure file Sync felhőalapú rétegek megismerése](storage-sync-cloud-tiering-overview.md). A példákkal ellátott felhőalapú rétegbeli szabályzatok részletes ismertetését itt találja: [Azure file Sync Cloud rétegű házirendek](storage-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Korlátozások
- A felhő-rétegek nem támogatottak a Windows rendszerköteten.

- Ha mennyiségi szintű FSRM kvóta van, akkor továbbra is engedélyezheti a Felhőbeli rétegeket. Miután beállította a FSRM-kvótát, a meghívott szabad terület lekérdezési API-k automatikusan jelentik a kötet szabad területét a kvóta beállítása alapján. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Fájl minimális mérete a szinthez

Az ügynök 9-es és újabb verzióiban a fájl minimális fájlmérete a fájlrendszer fürtjének méretétől függ. A Felhőbeli rétegek minimális mérete a fürt méretének és a minimális 8 KB-os értéknek a kiszámítására alkalmas. A következő táblázat a mennyiségi fürt méretétől függően a minimálisan felhasználható fájlméretet mutatja be:

|Kötet fürtjének mérete (bájt) |Ennek a méretnek vagy nagyobb méretű fájloknak lépcsőzetesen kell lenniük  |
|----------------------------|---------|
|4 KB vagy kisebb (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |

A legfeljebb 64 KB méretű szektorcsoportok jelenleg támogatottak, de nagyobb méretek esetén a Felhőbeli rétegek nem működnek.

A Windows által használt összes fájlrendszer, a fürt méretétől függően rendezi a merevlemezt (más néven a foglalási egység mérete). A fürt mérete a fájl tárolására használható legkisebb lemezterületet jelöli. Ha a fájlméretek nem jönnek létre a fürt méretének még többszörösére, a fájlnak a fürt következő többszörösére való tárolásához további helyet kell használni.

A Azure File Sync a Windows Server 2012 R2 és újabb operációs rendszert futtató NTFS-kötetek esetében támogatott. A következő táblázat az alapértelmezett szektorcsoport-méreteket ismerteti, amikor új NTFS-kötetet hoz létre a Windows Server 2019-mel.

|Kötet mérete    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 KB                |
|16TB – 32 TB   | 8 KB                |
|32 TB TÁRTERÜLETET – 64 TB   | 16 KB               |
|64TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (korábbi max.) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB – 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (maximális méret)  |
|8 TB >         | nem támogatott       |

Lehetséges, hogy a kötet létrehozásakor manuálisan formázta a kötetet egy másik szektorcsoport-mérettel. Ha a kötet a Windows egy korábbi verziójából ered, akkor az alapértelmezett szektorcsoportok mérete is eltérő lehet. [Ez a cikk további részleteket tartalmaz az alapértelmezett fürtök méretéről.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Még akkor is, ha 4 KB-nál kisebb méretű fürtöt választ, egy 8 KB-os korlátot, amely a minimálisan felhasználható fájlméretet eredményezi, továbbra is érvényes. (Akkor is, ha a műszakilag 2x fürt mérete nem éri el a 8 KB-ot.)

Az abszolút minimum oka az, hogy az NTFS tárolja a rendkívül kis méretű fájlokat – 1 KB 4 KB méretű fájlokat. A kötet más paramétereinek függvényében előfordulhat, hogy a kis fájlok nem tárolódnak a lemezen lévő fürtben. Előfordulhat, hogy az ilyen fájlokat közvetlenül a kötet főfájl-táblájában vagy az "MFT-rekord" tárolja. A felhő-réteg újraelemzési pontja mindig lemezen van tárolva, és pontosan egy fürtöt vesz igénybe. Az ilyen kis méretű fájlok megtakarítása nem lehetséges. A szélsőséges esetek akár több helyet is használhatnak a felhőalapú rétegek engedélyezésével. Ennek elleni védelem érdekében a Felhőbeli többméretű fájl legkisebb mérete 8 KB, 4 KB-os vagy kisebb méretű fürt esetén. 

## <a name="selecting-your-initial-policies"></a>A kezdeti szabályzatok kiválasztása

Ha egy kiszolgálói végponton engedélyezi a Felhőbeli rétegeket, akkor minden egyes kiszolgálói végponthoz létre kell hoznia egy helyi virtuális meghajtót. A kiszolgálói végpont elkülönítése megkönnyíti a felhő-rétegek működésének és a szabályzatok megfelelő módosításának megértését. A Azure File Sync azonban akkor is működik, ha ugyanazon a meghajtón több kiszolgálói végpont is található, a részletekért lásd: [több kiszolgálói végpont a helyi kötet](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) szakaszban. Azt is javasoljuk, hogy amikor először engedélyezi a felhő-rétegek használatát, a szabályzat letiltását és a kötet szabad területére vonatkozó szabályzatot 10%-ról 20%-ra kell megőrizni. A legtöbb fájlkiszolgáló-kötet esetében a 20%-os mennyiségű szabad terület általában a legjobb megoldás.

Az egyszerűség kedvéért, valamint az elemek rétegek szerinti méretezésének egyértelmű megismeréséhez javasoljuk, hogy a kötet szabad területére vonatkozó házirendet elsődlegesen módosítsa, és ha szükséges, tartsa letiltva a dátum-házirendet. Ezt azért javasoljuk, mert a legtöbb ügyfelünk értékesnek találta a helyi gyorsítótárat a lehető legtöbb gyors fájllal, a többi pedig a felhőbe. A dátumra vonatkozó házirend azonban hasznos lehet, ha proaktívan szeretné felszabadítani a helyi lemezterületet, és tudja, hogy a megadott kiszolgálói végponton lévő fájlok nem szükségesek helyileg tárolni. A Date (dátum) házirend beállítása további hasznos helyi lemezterületet szabadít fel más végpontok számára ugyanazon a köteten, hogy több fájlt is gyorsítótárazjon.

A szabályzatok beállítása után figyelje a kimenő forgalmat, és ennek megfelelően állítsa be mindkét szabályzatot. Azt javasoljuk, hogy a **Felhőbeli rétegek felidézésének mérete** és a **Felhőbeli rétegek felidézésének mérete** a Azure monitor alkalmazás metrikái alapján történjen. A kimenő forgalom figyelésével kapcsolatos további információkért lásd: a [Felhőbeli rétegek figyelése](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>A szabályzatok módosítása

Ha az Azure-ból folyamatosan visszahívott fájlok mennyisége nagyobb a kívántnál, akkor lehet, hogy több, a helyi kiszolgálói kötetre menteni kívánt tárterülettel rendelkezik. Ha lehetséges, növelje a helyi kötetek méretét, és/vagy csökkentse a kötet szabad területének százalékos arányát kis léptékben. A kötet szabad területének százalékos arányának csökkenése túl sok negatív következményekkel is járhat. Az adatkészletek nagyobb forgalmához több szabad terület szükséges – az új fájlokhoz és a "hideg" fájlok visszahívásához. A rétegek beolvasása akár egy óráig is eltarthat, majd feldolgozási időt igényel, ezért mindig elegendő szabad területtel kell rendelkeznie a köteten.

A további adatok helyi megtartása alacsonyabb kimenő költségekkel jár, mivel az Azure-ból kevesebb fájl lesz meghívva, de a helyszíni tárterület nagyobb mennyiségét igényli, amely a saját költségén is elérhető. 

A kötet szabad területére vonatkozó házirend módosításakor a helyi adatok mennyiségét a következő tényezők határozzák meg: a sávszélesség, az adatkészlet hozzáférési mintája és a költségvetés. Alacsony sávszélességű kapcsolat esetén előfordulhat, hogy további helyi adatmennyiséget szeretne használni a felhasználók minimális késése érdekében. Ellenkező esetben egy adott időszakon belül elvégezheti azt a kiindulási arányban. Például, ha tudja, hogy az 1 TB-os adatkészlet 10%-a módosul vagy aktívan aktív a hónapban, akkor érdemes megtartania a 100 GB-ot a helyi számítógépen, hogy ne kelljen gyakran újrahívni a fájlokat. Ha a kötet 2 TB-os, akkor az 5%-ot (vagy 100 GB-ot) helyben kell tartania, ami azt jelenti, hogy a fennmaradó 95% a kötet szabad területének százalékos aránya. A magasabb adatváltozási időszakok esetében azonban puffert kell hozzáadnia, vagyis a nagyobb mennyiségű szabad terület százalékos arányát, majd később szükség esetén módosítania kell.

## <a name="standard-operating-procedures"></a>Szabványos működési eljárások

- Ha az első áttelepítését Azure Files Azure File Sync keresztül végzi el, a felhőalapú rétegek a kezdeti feltöltéstől függenek
- A felhő-rétegek a 60 percenként ellenőrzi a kötet szabad területének és a dátum-szabályzatoknak való megfelelést
- A fájlok áttelepítésekor a Robocopy/LFSM kapcsolójának használata lehetővé teszi a fájlok szinkronizálását és a Felhőbeli rétegek közötti térközt a kezdeti feltöltés során. 
- Ha a hő megkezdése előtt történik a rétegek létrehozása, a fájlok az utolsó módosítás időbélyegével lesznek feldolgozva.

## <a name="next-steps"></a>Következő lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
