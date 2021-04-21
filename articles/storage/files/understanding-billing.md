---
title: A Azure Files számlázási | Microsoft Docs
description: Megtudhatja, hogyan értelmezheti az Azure-fájlmegosztások kiépített és használat alapú számlázási modelljeit.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788384"
---
# <a name="understand-azure-files-billing"></a>A Azure Files számlázásának
Azure Files két különböző számlázási modellt biztosít: kiépítve és fizetéssel. A kiépített modell csak prémium fájlmegosztások esetében érhető el, amelyek a **FileStorage** tárfióktípusban üzembe helyezett fájlmegosztások. A pay-as-you-go modell csak a standard fájlmegosztások esetében érhető el, amelyek az általános célú **2- (GPv2-)** tárfióktípusban üzembe helyezett fájlmegosztások. Ez a cikk bemutatja, hogyan működnek a modellek a havi havi Azure Files érdekében.

További Azure Files a díjszabási oldalon [Azure Files oldalon.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="storage-units"></a>Tárolóegységek    
Azure Files a tárolási kapacitást base-2 mértékegységben adja meg: KiB, MiB, GiB és TiB. Előfordulhat, hogy az operációs rendszer nem ugyanazt a mértékegységet vagy számlálórendszert használja.

### <a name="windows"></a>Windows

A Windows operációs rendszer és a Azure Files a base-2 számlálórendszer használatával mérik a tárolókapacitást, de az egységek címkézésekor van különbség. Azure Files a tárolási kapacitását base-2 mértékegységekkel, a Windows pedig 10 egység egységben címkével címkéi a tárolókapacitást. A tárolási kapacitás jelentésére a Windows nem konvertálja a tárolási kapacitását base-2-ről base-10-re.

|Betűszó  |Definíció  |Unit (Egység)  |A Windows a következőként jelenik meg:  |
|---------|---------|---------|---------|
|KiB     |1024 bájt         |kibibájt         |KB (kilobájt)         |
|Mib     |1024 KiB (1 048 576 bájt)         |mebibájt         |MB (megabájt)         |
|Gib     |1024 MiB (1 073 741 824 bájt)         |gibibyte         |GB (gigabájt)         |
|Tib     |1024 GiB (1 099 511 627 776 bájt)         |tebibájt         |TB (terabájt)         |

### <a name="macos"></a>macOS

A használt számlálórendszer meghatározásához tekintse meg az iOS és [a macOS](https://support.apple.com/HT201402) jelentés tárolási kapacitását az Apple webhelyén.

### <a name="linux"></a>Linux

Az egyes operációs rendszerek vagy egyes szoftverek különböző számlálórendszereket is használni tudnának. Tekintse meg a dokumentációt, hogy megállapítsa, hogyan jelentik a tárolási kapacitást.

## <a name="provisioned-model"></a>Kiépített modell
Azure Files a prémium fájlmegosztások esetében kiépített modellt használ. Egy kiépített üzleti modellben proaktívan kell megadnia a Azure Files szolgáltatásnak, hogy mik a tárolási követelmények, ahelyett, hogy a használat alapján kellene fizetni. Ez hasonlít a helyszíni hardvervásárláshoz, mert ha egy Azure-fájlmegosztást egy bizonyos tárterületen oszt ki, akkor a tárhelyért akkor is fizetnie kell, függetlenül attól, hogy használja-e vagy sem, mintha nem fizeti a helyszíni fizikai adathordozó költségeit, amikor elkezdi használni a tárhelyet. A fizikai adathordozó helyszíni megvásárlásával ellentétben a kiépített fájlmegosztások a tárolási és I/O-teljesítményjellemzőktől függően dinamikusan skálázhatóak felfelé vagy lefelé.

Prémium fájlmegosztás kiépítésekor meg kell adnia, hogy a számítási feladat hány giBs-t igényel. Minden kiépített GiB további IOPS-értékeket és átviteli sebességet biztosít rögzített arányban. Az alap IOPS mellett, amelyhez garantált, minden prémium fájlmegosztás támogatja az adatlelőadást az ajánlott költségek mellett. Az IOPS és az átviteli sebesség képletei a következők:

- Alapérték IOPS = 400 + 1 * kiépítve GiB. (Legfeljebb 100 000 IOPS).)
- Burst Limit = MAX(4000, 3 * Baseline IOPS).
- Bejövő forgalom sebessége = 60 MiB/s + 0,06 * kiépítve GiB.
- Bejövő forgalom sebessége = 40 MiB/s + 0,04 * kiépítve GiB.

A fájlmegosztás létesített mérete bármikor növelhető, de az utolsó növekedés óta csak 24 óra után csökkenthető. A kvóta növelése nélküli 24 órás várakozás után a kvóta növeléséig a lehető legtöbb alkalommal csökkentheti a megosztási kvótát. Az IOPS/átviteli sebesség skálázható változásai a kiépített méret módosítása után néhány percen belül hatnak.

A kiépített megosztás mérete a használt GiB alatt is csökkenhet. Ha így történik, nem veszíti el az adatokat, de továbbra is kiszámlázjuk a felhasznált méretet, és a kiépített megosztás teljesítményét (alap IOPS, átviteli sebesség és burst IOPS) kapja meg, nem pedig a felhasznált méretet.

Az alábbi táblázat néhány példát mutat be ezekre a képletekre a kiépített megosztásméretekhez:

|Kapacitás (GiB) | Alap IOPS | Burst IOPS | Bejövő forgalom (MiB/s) | Bejövő forgalom (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Legfeljebb 4000     | 66   | 44   |
|500         | 900     | Legfeljebb 4000  | 90   | 60   |
|1,024       | 1,424   | Legfeljebb 4000   | 122   | 81   |
|5,120       | 5,520   | Legfeljebb 15 360  | 368   | 245   |
|10,240      | 10,640  | Legfeljebb 30 720  | 675   | 450   |
|33,792      | 34,192  | Legfeljebb 100 000 | 2,088 | 1,392   |
|51,200      | 51,600  | Legfeljebb 100 000 | 3,132 | 2,088   |
|102,400     | 100.000 | Legfeljebb 100 000 | 6,204 | 4,136   |

A fájlmegosztások hatékony teljesítménye többek között a gép hálózati korlátaitól, a rendelkezésre álló hálózati sávszélességtől, az I/O-méretektől és a párhuzamosságtól függ. Például egy 8 KiB olvasási/írási I/O-mérettel rendelkező belső teszt alapján egyetlen, többcsatornás SMB-t nem engedélyező Windows rendszerű virtuális gép, a *Standard F16s_v2,* amely SMB-n keresztül csatlakozik a prémium fájlmegosztáshoz, 20 000 olvasási IOPS-t és 15 000 írási IOPS-t érhet el. Az 512 MiB olvasási/írási I/O-méret esetén ugyanaz a virtuális gép 1,1 GiB/s bejövő és 370 MiB/s bejövő átviteli sebességet érhet el. Ugyanaz az ügyfél akár 3-szor nagyobb teljesítményt érhet el, ha a prémium szintű megosztáson engedélyezve van a többcsatornás \~ SMB. A maximális teljesítményskála eléréséhez engedélyezze a [többcsatornás SMB-t,](storage-files-enable-smb-multichannel.md) és terjessze a terhelést több virtuális gép között. A gyakori [teljesítményproblémákért](storage-files-smb-multichannel-performance.md) [](storage-troubleshooting-files-performance.md) és megkerülő megoldásokért tekintse meg a többcsatornás SMB teljesítmény- és hibaelhárítási útmutatóját.

### <a name="bursting"></a>Tele
Ha a számítási feladatnak további teljesítményre van szüksége a csúcsterhelés kielégítése érdekében, a megosztási kreditek használatával túllépheti az alap IOPS-korlátot, hogy az igénynek megfelelő megosztási teljesítményt nyújtsa. A prémium fájlmegosztások IOPS-értéke akár 4000-re, vagy akár három tényezőre is felfelé, amelyik magasabb érték. Az adatlökés automatizált, és kreditrendszeren alapul. Az adatlokozás az elérhető legjobb erőfeszítés alapján működik, és  a hirtelen időtartamot nem garantáljuk, a fájlmegosztások akár 60 perces maximális időtartamra is fellelődhet a korlátig.

A kreditek akkor gyűlnek össze egy adatlöket-gyűjtőben, ha a fájlmegosztás forgalma az alap IOPS-érték alatt van. Egy 100 GiB-os megosztás például 500 alap IOPS-t mutat. Ha a megosztás tényleges forgalma 100 IOPS volt egy adott 1 másodperces időszakban, akkor a 400 fel nem használt IOPS egy adatlöket-gyűjtőbe lesz jóváírva. Hasonlóképpen, egy tétlen 1 TiB-os megosztás 1424 IOPS-os kreditet halm ad eredményül. Ezeket a krediteket később fogja használni, amikor a műveletek túllépik az alap IOPS-t.

Ha egy megosztás túllépi az alap IOPS-t, és krediteket ad egy burst gyűjtőben, akkor az a maximálisan engedélyezett csúcsos kilöketi sebességgel fog megnökkenni. A megosztások a kreditek fennmaradó részében, legfeljebb 60 perces időtartamig továbbra is tovább haladnak, de ez a felhalmozott hitelllelések számán alapul. Az alapkonfiguráción túli IOPS-érték egy kreditet használ fel, és ha felhasználta az összes kreditet, a megosztás visszatér az alap IOPS-értékhez.

A kreditek megosztásának három állama van:

- Ez akkor lép fel, ha a fájlmegosztás az alapkonfiguráció IOPS-értéknél kevesebbet használ.
- Degresszáció, ha a fájlmegosztás az alapkonfiguráció IOPS-értéknél többet használ, és adatlokozási módban van.
- Konstans, ha a fájl megosztása pontosan az alap IOPS-t használja, vagy nincsenek felhalmozott vagy felhasznált kreditek.

Az új fájlmegosztások az adatlöket-gyűjtőben található kreditek teljes számával kezdődnek. A kieső kreditek nem lesznek felhalmozottak, ha a megosztás IOPS-érték a kiszolgáló szabályozása miatt az alap IOPS alá esik.

## <a name="pay-as-you-go-model"></a>A pay-as-you-go modell
Azure Files használat alapján fizetett üzleti modellt használ a standard fájlmegosztások esetében. A használatalapú fizetéses üzleti modellben a fizetendő összeget a ténylegesen használt mennyiség határozza meg, nem a kiépített összeg alapján. Magas szinten fizet a lemezen tárolt adatmennyiségért, majd egy további tranzakciókészletet az adatok használatának alapján. A használat alapján fizetett modell költséghatékony lehet, mert nem kell túlterheltnek lennie ahhoz, hogy figyelembe tudja venni a jövőbeli növekedést vagy teljesítménybeli követelményeket, illetve megszüntetni, ha a számítási feladat adatigénye változó az idő függvényében. A használatalapú fizetéses modellt azonban nehéz lehet a költségvetési folyamat részeként tervezni, mert a használatalapú számlázási modellt a végfelhasználói használat alapján kell tervezni.

### <a name="differences-in-standard-tiers"></a>A standard szintek közötti különbségek
Ha standard fájlmegosztást hoz létre, választhat a tranzakcióoptimalált, a gyors és a hűtési szint között. Mindhárom szint pontosan ugyanazon a standard tárolóhardveren van tárolva. E három szint esetében a fő különbség az, hogy az adataik az alacsonyabb, alacsonyabb tárolási szinten vannak, és a tranzakciós árak, amelyek magasabbak a hűtők által tartalmazott tárolási szinteken. Ez a következőkkel jár:

- Ahogy a neve is mutatja, a tranzakcióoptimalált szolgáltatás optimalizálja a nagy tranzakciós számítási feladatok árát. A tranzakcióoptimalált adatok a legmagasabbak az alacsony tárolási árakkal, de a legalacsonyabb tranzakciós árakkal.
- A hot (gyors) olyan aktív számítási feladatokhoz való, amelyek nem foglalnak magukban nagy számú tranzakciót, és valamivel alacsonyabbak az adatok az aktívan nem elérhető tárolási árakon, de valamivel magasabb tranzakciós árakkal, mint a tranzakcióoptimaláltak. Gondoljon rá úgy, mint a tranzakcióoptimalált és a hűtési szintek közötti középutat.
- A Cool optimalizálja az olyan számítási feladatok árát, amelyek nem túl sok tevékenységet folytatnak, és a legalacsonyabb, de a legmagasabb tranzakciós díjakat kínálják az adatokhoz.

Ha ritkán használt számítási feladatot tesz a tranzakcióoptimalált szintre, szinte semmit sem kell fizetnie a havi néhány alkalommal, amikor tranzakciókat eszközol a megosztáson, de az adattárolási költségek nagy részét fizeti. Ha ugyanezt a megosztást a ritka adatokat tároló szintre szeretné áthelyezni, akkor is szinte semmit sem kellene fizetnie a tranzakciós költségekért, egyszerűen azért, mert nagyon ritkán tranzakciókat kell tranzakciókat eszközlni ehhez a számítási feladathoz, de a ritka adatokat tároló réteg sokkal olcsóbb adattárolási árral rendelkezik. A megfelelő csomag kiválasztása lehetővé teszi a költségek jelentős csökkentését. A megfelelő csomag kiválasztása lehetővé teszi a költségek jelentős csökkentését.

Hasonlóképpen, ha a gyakran használt számítási feladatokat a gyakran használt adatok rétegében helyezze el, sokkal több tranzakciós költséget kell fizetnie, de az adattárolási költségek alacsonyabbak lesznek. Ez olyan helyzethez vezethet, amikor a tranzakciós árakból származó megnövekedett költségek meghaladják a csökkent adattárolási árból származó megtakarításokat, így több pénzt kell fizetnie a hűtésért, mint a tranzakcióoptimaláltakért. Bizonyos használati szintek esetében lehetséges, hogy míg a leggyakoribb a költséghatékony szint, a hűtési szint drágább lesz, mint a tranzakciók optimalizálása.

A számítási feladat és a tevékenységszint határozza meg a standard fájlmegosztás legköltségtakarékosabb szintjét. A gyakorlatban a legköltségtakarékosabb szint választásának legjobb módja a megosztás tényleges erőforrás-használatának (tárolt adatok, írási tranzakciók stb.) megtekintése.

### <a name="what-are-transactions"></a>Mik azok a tranzakciók?
A tranzakciók olyan műveletek vagy kérések, amelyek Azure Files a fájlmegosztás tartalmának feltöltésére, letöltésére vagy más módon való manipulálására vonatkozó kérések. A fájlmegosztáson minden egyes művelet egy vagy több tranzakcióra, valamint a használat alapján fizetett számlázási modellt használó standard megosztásra van lefordítva, amely tranzakciós költségekre van fordítva.

Öt alapvető tranzakciókategória van: írás, lista, olvasás, egyéb és törlés. A rendszer a REST API SMB-n keresztül végzett összes műveletet a következő 4 kategóriába sorolja:

| Művelettípus | Írási tranzakciók | Tranzakciók listása | Tranzakciók olvasása | Egyéb tranzakciók | Tranzakciók törlése |
|-|-|-|-|-|-|
| Felügyeleti műveletek | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Adatműveletek | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> Az NFS 4.1 csak a kiépített számlázási modellt használó prémium fájlmegosztások esetében érhető el, a tranzakciók nem befolyásolják a prémium fájlmegosztások számlázását.

## <a name="see-also"></a>Lásd még
- [Azure Files oldalon.](https://azure.microsoft.com/pricing/details/storage/files/)
- [A központi telepítés Azure Files tervezése](storage-files-planning.md) és a központi telepítés Azure File Sync [tervezése.](../file-sync/file-sync-planning.md)
- [Hozzon létre egy fájlmegosztást,](storage-how-to-create-file-share.md) [és telepítse Azure File Sync.](../file-sync/file-sync-deployment-guide.md)