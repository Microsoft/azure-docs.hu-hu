---
title: Azure Files számlázás ismertetése | Microsoft Docs
description: Ismerje meg, hogyan értelmezheti az Azure-fájlmegosztás kiépített és utólagos elszámolású számlázási modelljeit.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226641"
---
# <a name="understand-azure-files-billing"></a>Azure Files számlázás ismertetése
Azure Files két különböző számlázási modellt biztosít: kiépített és utólagos elszámolású. A kiépített modell csak a prémium szintű fájlmegosztás esetében érhető el, amelyek a **FileStorage** -fiókban üzembe helyezett fájlmegosztás. Az utólagos elszámolású modell csak a standard fájlmegosztás esetében érhető el, amelyek az **általános célú 2-es verziójú (GPv2)** Storage-fiókban üzembe helyezett fájlmegosztás. Ez a cikk azt ismerteti, hogyan működik mindkét modell a havi Azure Files számla megismerése érdekében.

Azure Files díjszabási információkért lásd: [Azure Files díjszabási oldal](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Tárolóegységek    
A Azure Files Base-2 mértékegységet használ a tárolási kapacitás (KiB, MiB, GiB és TiB) ábrázolására. Előfordulhat, hogy az operációs rendszer nem használja ugyanazt a mértékegységet vagy leltározási rendszert.

### <a name="windows"></a>Windows

A Windows operációs rendszer és a Azure Files a tárolási kapacitás mértéke a Base-2 számlálási rendszerrel együtt, de az egységek címkézése eltérő. Azure Files megcímkézi a tárolási kapacitását a 2. alap mértékegységgel, miközben a Windows felcímkézi a tárolási kapacitását az alapszintű 10 mértékegységben. A tárolási kapacitás jelentésekor a Windows nem alakítja át a tárolási kapacitását a Base-2 értékről a 10-es verzióra.

|Betűszó  |Definíció  |Unit (Egység)  |A Windows a következőképpen jelenik meg  |
|---------|---------|---------|---------|
|KiB     |1 024 bájt         |Bináris prefixum         |KB (kilobájt)         |
|MiB     |1 024 KiB (1 048 576 bájt)         |Bináris prefixum         |MB (megabájt)         |
|GiB     |1024 MiB (1 073 741 824 bájt)         |gibibájtnak         |GB (gigabájt)         |
|TiB     |1024 GiB (1 099 511 627 776 bájt)         |tebibyte         |TB (terabájt)         |

### <a name="macos"></a>macOS

Ismerje meg, hogy az [iOS és a MacOS hogyan jelent adattárolási kapacitást](https://support.apple.com/HT201402) az Apple webhelyén annak meghatározásához, hogy melyik leltározási rendszert használják.

### <a name="linux"></a>Linux

Az egyes operációs rendszerek vagy szoftverek külön leltározási rendszert is felhasználhatnak. Tekintse meg a dokumentációt annak meghatározásához, hogyan jelentik a tárolási kapacitást.

## <a name="provisioned-model"></a>Kiépített modell
Azure Files a prémium szintű fájlmegosztás számára kiépített modellt használ. Egy kiépített üzleti modellben proaktív módon megadhatja a Azure Files szolgáltatásnak, hogy a tárolási követelményei milyenek, és nem a használatuk alapján történik. Ez hasonló a helyszíni hardveres vásárláshoz, abban az esetben, ha egy bizonyos mennyiségű tárterülettel rendelkező Azure-fájlmegosztást kiépít, a tárterületért kell fizetnie, függetlenül attól, hogy használja-e, vagy sem, ugyanúgy, mint a helyszíni fizikai adathordozók költségeinek kifizetését. A helyszíni fizikai adathordozótól eltérően a kiosztott fájlmegosztás a tárterülettől és az i/o-teljesítménytől függően dinamikusan méretezhető.

Prémium fájlmegosztás kiépítésekor meg kell adnia, hogy hány GiBs van szükség a munkaterheléshez. Az Ön által kiépített összes GiB egy rögzített arányú további IOPS és átviteli sebességre jogosítja fel Önt. Az Ön által garantált alapszintű IOPS mellett minden prémium fájlmegosztás támogatja a lehető legjobb adatmennyiséget. A IOPS és az átviteli sebességre vonatkozó képletek a következők:

- Alapterv IOPS = 400 + 1 * kiépített GiB. (Max. 100 000 IOPS).
- Burst Limit = MAX (4000, 3 * alapterv IOPS).
- Kimenő forgalom aránya = 60 MiB/s + 0,06 * kiépített GiB.
- Bejövő forgalom sebessége = 40 MiB/s + 0,04 * kiépített GiB.

A fájlmegosztás kiépített mérete bármikor növelhető, de csak a legutóbbi növekedés óta 24 óra elteltével csökkenthető. Ha a várakozás után 24 órán keresztül nem növekszik a kvóta, a megosztási kvótát tetszőleges számú időpontra csökkentheti, amíg újra nem növelné. A IOPS/átviteli sebesség változása néhány percen belül érvénybe lép a kiépített méret megváltozása után.

Lehetséges, hogy csökkentse a kiosztott megosztás méretét a használt GiB alatt. Ha ezt teszi, nem veszíti el az adatvesztést, de továbbra is a felhasznált méret után kell fizetnie, és meg kell kapnia a kiépített megosztás teljesítményét (alapkonfiguráció IOPS, átviteli sebességét és burst IOPS), nem a felhasznált méretet.

Az alábbi táblázat néhány példát mutat be a kiosztott megosztási méretekre:

|Kapacitás (GiB) | Alapterv IOPS | Burst IOPS | Kimenő forgalom (MiB/s) | Bejövő forgalom (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Akár 4 000     | 66   | 44   |
|500         | 900     | Akár 4 000  | 90   | 60   |
|1 024       | 1 424   | Akár 4 000   | 122   | 81   |
|5 120       | 5 520   | Akár 15 360  | 368   | 245   |
|10 240      | 10 640  | Akár 30 720  | 675   | 450   |
|33 792      | 34 192  | Akár 100 000 | 2 088 | 1 392   |
|51 200      | 51 600  | Akár 100 000 | 3 132 | 2 088   |
|102 400     | 100.000 | Akár 100 000 | 6 204 | 4 136   |

A fájlmegosztás tényleges teljesítményének a számítógép hálózati korlátai, a rendelkezésre álló hálózati sávszélesség, az IO-méretek, a párhuzamosságok számos más tényezőtől függenek. Például a 8 KiB írási/olvasási IO-méretekkel rendelkező belső tesztelésen alapuló, egyetlen Windowsos virtuális gép, amely nem engedélyezett többcsatornás SMB-t használ, a *Standard F16s_v2*, a prémium szintű fájlmegosztás SMB-vel való csatlakoztatása 20000 olvasási IOPS és 15 000 FORDULAT írási IOPS. Az 512 MiB-írási/írási IO-méretekkel ugyanez a virtuális gép elérheti a 1,1 GiB/s kimenő forgalmat és a 370 MiB/s adatátviteli sebességet. Ugyanaz az ügyfél akár 3x-os teljesítményt is elérhet, \~ Ha a többcsatornás SMB engedélyezve van a prémium szintű megosztásokon. A maximális teljesítmény érdekében engedélyezze a [többcsatornás SMB](storage-files-enable-smb-multichannel.md) -t, és terjessze a terhelést több virtuális gép között. Tekintse át a [többcsatornás SMB-teljesítményt](storage-files-smb-multichannel-performance.md) és a [hibaelhárítási útmutatót](storage-troubleshooting-files-performance.md) a gyakori teljesítménnyel kapcsolatos problémákról és a megkerülő megoldásokról.

### <a name="bursting"></a>Tele
Ha a számítási feladatnak a maximális igény kielégítése érdekében további teljesítményre van szüksége, akkor a megosztás a megosztási alapkonfiguráció IOPS korlátján keresztül is felhasználhatja, hogy az igényeinek megfelelő megosztási teljesítményt nyújtson. A prémium szintű fájlmegosztás akár 4 000-ig, akár három tényezővel is feltörte a IOPS, attól függően, hogy melyik a magasabb érték. A bursás automatizált, és kreditrendszer alapján működik. Az adatbontás a legjobb megoldás, és a burst-korlát nem garantálható, a *fájlmegosztás legfeljebb 60* perces korlátot vehet igénybe.

A kreditek felhalmozódnak egy burst gyűjtőben, amikor a fájlmegosztás forgalma az alapszintű IOPS alatt van. Egy 100 GiB-megosztás például 500 alapterv-IOPS rendelkezik. Ha a megosztás tényleges forgalma 100 IOPS volt egy adott 1 másodperces intervallumhoz, akkor a 400 fel nem használt IOPS jóváírásra kerül egy burst gyűjtőn. Ehhez hasonlóan az 1. inaktív 1 TiB-megosztás is feltörte a 1 424 IOPS. Ezeket a krediteket később akkor fogjuk használni, amikor a műveletek túllépik az alapkonfiguráció IOPS.

Ha egy megosztás túllépi az alapszintű IOPS, és a kreditek egy burst gyűjtőben vannak, akkor a maximálisan megengedett legmagasabb terhelési arányban lesz kitört. A megosztások továbbra is lemerülhetnek, amíg a kreditek megmaradnak, akár legfeljebb 60 percet is igénybe vehet, de ez a felhalmozott burst-kreditek számától függ. Az alapkonfiguráció IOPS túli minden egyes IO egy kreditet használ, és ha az összes kredit felhasználható, a megosztás visszatér az alapkonfiguráció IOPS.

A megosztási kreditek három állapottal rendelkeznek:

- Ha a fájlmegosztás az alapkonfigurációnál kisebb IOPS használja.
- Csökken, ha a fájlmegosztás több, mint az alapszintű IOPS és a burst módban használja.
- Állandó, ha a fájlmegosztás pontosan az alapkonfiguráció IOPS használja, akkor nincsenek felhalmozva vagy felhasználva kreditek.

Az új fájlmegosztás a teljes számú Kredittel kezdődik a burst gyűjtőben. A burst kreditek nem lesznek felhalmozva, ha a megosztás IOPS az alapszintű IOPS alá esik a kiszolgáló általi szabályozás miatt.

## <a name="pay-as-you-go-model"></a>Utólagos elszámolású modell
A Azure Files a szabványos fájlmegosztás esetében az utólagos elszámolású üzleti modellt használja. Az utólagos elszámolású üzleti modellekben a fizetendő összeget a ténylegesen felhasznált mennyiség határozza meg, nem pedig kiosztott mennyiség alapján. Magas szinten a lemezen tárolt adatok mennyiségére, majd az adatok használata alapján egy további tranzakcióra számítunk fel díjat. Az utólagos elszámolású modellek költséghatékonyak lehetnek, mivel nem kell túlzott mértékben kiépíteni a jövőbeli növekedési vagy teljesítménnyel kapcsolatos követelményeket, vagy ha a számítási feladatnak az adatlábnyoma az idő múlásával változik. Másfelől az utólagos elszámolású modelleket is nehéz lehet megtervezni a költségvetési folyamat részeként, mivel az utólagos elszámolású számlázási modellt a végfelhasználók általi használat vezérli.

### <a name="differences-in-standard-tiers"></a>Különbségek a standard szinteken
Szabványos fájlmegosztás létrehozásakor a rendszer az optimalizált, a gyors és a lassú elérésű szint között választ. A rendszer mindhárom szintet pontosan ugyanazon a szabványos tárolási hardveren tárolja. Ebben a három szinten a fő különbség az, hogy az adatok a REST-alapú tárolás árai, amelyek alacsonyabbak a hűvösebb szinteken, és a tranzakciós árak, amelyek magasabbak a hűvösebb szinteken. Ez a következőkkel jár:

- A tranzakció optimalizált, ahogy a név is jelenti, optimalizálja a nagy tranzakciós számítási feladatok árát. Az optimalizált tranzakció a legmagasabb adattárolási ár, de a legalacsonyabb tranzakciós árak.
- A gyakran használt olyan aktív munkaterhelések esetében, amelyek nem tartalmaznak nagy számú tranzakciót, és valamivel alacsonyabb adattárolási díjszabással, de valamivel magasabb tranzakciós árral rendelkeznek, mint az optimalizált tranzakciók. Képzelje el, hogy az optimalizált és a ritka elérésű szint között a legközelebb van.
- A ritkán használt munkaterhelések ára optimalizálja a nagy mennyiségű tevékenységgel nem rendelkező számítási feladatokat, és a legalacsonyabb adattárolási árat, de a legmagasabb tranzakciós árakat is kínálja.

Ha a tranzakció optimalizált szintjében ritkán használt munkaterhelést helyez üzembe, akkor a havonta néhány alkalommal, amikor tranzakciókat hajt végre a megosztáson, akkor az adattárolási költségek nagy hányadát kell fizetnie. Ha ugyanezt a megosztást a lassú szintre szeretné áthelyezni, akkor is szinte semmit sem kell fizetnie a tranzakciós költségekért, egyszerűen azért, mert nagyon ritkán végez tranzakciókat ehhez a számítási feladathoz, de a lassú elérési szint sokkal olcsóbb adattárolási árral rendelkezik. A használati eset megfelelő szintjének kiválasztása lehetővé teszi a költségek jelentős csökkentését. A használati eset megfelelő szintjének kiválasztása lehetővé teszi a költségek jelentős csökkentését.

Hasonlóképpen, ha a ritkán használt munkaterhelést a hűvös szinten helyezi el, a tranzakciós költségek sokkal többet fizetnek, de kevesebbet az adattárolási költségekért. Ez olyan helyzetet eredményezhet, amelynél a tranzakciós díjak megnövekedett költségei megnövelik a megtakarítást a csökkent adattárolási ár miatt, így több pénzért kell fizetnie, mint amennyire a tranzakció optimalizálva lenne. Bizonyos használati szintek esetében előfordulhat, hogy a gyors elérési szint a leggazdaságosabb szint, a lassú elérésű szint pedig drágább lesz, mint a tranzakciós optimalizálva.

A számítási feladatok és a tevékenységek szintje határozza meg a normál fájlmegosztás legköltséghatékonyabb szintjét. A gyakorlatban a leghatékonyabb csomag kiválasztásának legjobb módja a megosztás tényleges erőforrás-felhasználásának megvizsgálása (tárolt adatok, írási tranzakciók stb.).

### <a name="what-are-transactions"></a>Mik azok a tranzakciók?
A tranzakciók Azure Files a fájlmegosztás tartalmának feltöltésére, letöltésére vagy egyéb módon történő módosítására irányuló műveletek vagy kérelmek. A fájlmegosztás során végrehajtott minden művelet egy vagy több tranzakcióra, valamint az utólagos elszámolású számlázási modellt használó standard megosztásokra fordítja le a tranzakciós költségeket.

Öt alapvető tranzakciós kategória létezik: írás, Listázás, olvasás, egyéb és törlés. Az REST API vagy SMB használatával végzett összes művelet a következő 4 kategóriába kerül, az alábbiak szerint:

| Művelettípus | Írási tranzakciók | Tranzakciók listázása | Olvasási tranzakciók | Egyéb tranzakciók | Tranzakciók törlése |
|-|-|-|-|-|-|
| Felügyeleti műveletek | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Adatműveletek | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> Az NFS 4,1 csak a prémium szintű fájlmegosztás esetében érhető el, amelyek a kiépített számlázási modellt használják, a tranzakciók nem befolyásolják a prémium szintű fájlmegosztás számlázását.

## <a name="see-also"></a>Lásd még
- [Azure Files díjszabási oldala](https://azure.microsoft.com/pricing/details/storage/files/).
- [Azure Files központi telepítésének megtervezése](./storage-files-planning.md) és [a Azure file Sync üzembe helyezésének tervezése](./storage-sync-files-planning.md).
- [Hozzon létre egy fájlmegosztást](./storage-how-to-create-file-share.md) , és [telepítse a Azure file Sync](./storage-sync-files-deployment-guide.md).