---
title: Mappa szerkezetének leképezése Azure File Sync topológiára
description: Egy meglévő fájl-és mappa struktúrájának leképezése az Azure-fájlmegosztás számára a Azure File Sync való használatra. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 265d14d7cca05ff510e747c8d3a3b071e44a0a68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202399"
---
Ebben a lépésben azt értékeli, hogy hány Azure-fájlmegosztás szükséges. Egyetlen Windows Server-példány (vagy fürt) akár 30 Azure-fájlmegosztás szinkronizálására is képes.

Előfordulhat, hogy a köteteken további mappák vannak, amelyeket a felhasználók és alkalmazások SMB-megosztásként helyileg oszt meg. Ennek a forgatókönyvnek a legegyszerűbb módja, ha olyan helyszíni megosztást képzel el, amely a 1:1-et egy Azure-fájlmegosztás felé képezi le. Ha elég kicsi a száma, akkor egy Windows Server-példány esetében 30 alatta egy 1:1-es hozzárendelés javasolt.

Ha a 30-nál több megosztással rendelkezik, gyakran szükségtelen egy helyszíni megosztást (1:1) leképezni egy Azure-fájlmegosztás számára. Vegye figyelembe a következő beállításokat.

#### <a name="share-grouping"></a>Csoport megosztása

Ha például az emberi erőforrások (HR) részlege összesen 15 megosztást tartalmaz, érdemes lehet egyetlen Azure-fájlmegosztás összes HR-adatait tárolnia. Egy Azure-fájlmegosztás több helyszíni megosztásának tárolása nem akadályozza meg, hogy a szokásos 15 SMB-megosztást hozza létre a helyi Windows Server-példányon. Ez csak azt jelenti, hogy a 15 megosztás legfelső szintű mappáit almappákként rendezi a közös mappában. Ezután szinkronizálja ezt a közös mappát egy Azure-fájlmegosztás használatával. Így a helyszíni megosztások ezen csoportjára csak egyetlen Azure-fájlmegosztás szükséges a felhőben.

#### <a name="volume-sync"></a>Kötet szinkronizálása

Azure File Sync támogatja a kötetek gyökerének Azure-fájlmegosztás felé történő szinkronizálását. Ha a kötet gyökerét szinkronizálja, az összes almappa és fájl ugyanarra az Azure-fájlmegosztásra fog esni.

A kötet gyökerének szinkronizálása nem mindig a legjobb válasz. Több hely szinkronizálása is előnyökkel jár. Így például segít megőrizni az elemek számát a szinkronizálási hatókörben. Az Azure-fájlmegosztás és a Azure File Sync az 100 000 000 elemekkel (fájlokkal és mappákkal) történő tesztelését az ajánlott eljárás szerint a 20 000 000 vagy 30 000 000 alatti szám egyetlen megosztáson való megtartására próbálja meg. Az alacsonyabb számú elemet tartalmazó Azure File Sync beállítása nem csak a fájl-szinkronizálás esetén hasznos. Az elemek alacsonyabb száma is előnyökkel jár, például a következő esetekben:

* A felhő tartalmának kezdeti vizsgálata gyorsabban elvégezhető, ami viszont csökkenti a várakozást arra, hogy a névtér megjelenjen egy Azure File Sync-kompatibilis kiszolgálón.
* Az Azure fájlmegosztás pillanatképének Felhőbeli visszaállítása gyorsabb lesz.
* A helyszíni kiszolgálók vész-helyreállítási sebessége jelentősen felgyorsulhat.
* Az Azure-fájlmegosztás (szinkronizáláson kívül) által közvetlenül végrehajtott módosítások észlelhetők és gyorsabban szinkronizálhatók.

> [!TIP]
> Ha nem biztos benne, hogy hány fájlra és mappára van szüksége, tekintse meg a TreeSize eszközt a JAM Software GmbH-ból.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Egy üzembe helyezési Térkép strukturált megközelítése

Mielőtt egy későbbi lépésben üzembe helyezi a felhőalapú tárolót, fontos, hogy térképet hozzon létre a helyszíni mappák és az Azure-fájlmegosztás között. Ez a leképezés ezután tájékoztatja, hogy hány és melyik Azure File Sync *szinkronizálási csoport* erőforrásai lesznek kiépítve. A szinkronizálási csoport összekapcsolja az Azure-fájlmegosztást és a kiszolgálón található mappát, és létrehozza a szinkronizálási kapcsolatot.

A következő korlátozásokkal és ajánlott eljárásokkal határozhatja meg, hogy milyen számú Azure-fájlmegosztás szükséges. Ez segít a Térkép optimalizálásában.

* A telepített Azure File Sync ügynökkel rendelkező kiszolgálók akár 30 Azure-fájlmegosztás szinkronizálásával is szinkronizálhatók.
* Egy Azure-fájlmegosztás üzembe helyezése egy Storage-fiókon belül történik. Ennek köszönhetően a Storage-fiók egy méretezési célt szolgál a teljesítményszámlálók, például a IOPS és az átviteli sebesség számára.

  Egy standard szintű Azure-fájlmegosztás elméletileg telített lehet a Storage-fiók által kézbesíthető maximális teljesítményre. Ha több megosztást helyez át egyetlen Storage-fiókba, akkor a IOPS és az átviteli sebesség közös készletét hozza létre ezen megosztások számára. Ha azt tervezi, hogy csak Azure File Sync csatol a fájlmegosztás számára, a több Azure-fájlmegosztás ugyanabba a Storage-fiókba való csoportosítása nem okoz problémát. Tekintse át az Azure fájlmegosztás teljesítményének céljait, és tekintse át a releváns mérőszámokat. Ezek a korlátozások nem vonatkoznak a Premium Storage szolgáltatásra, ahol az egyes megosztások esetében a teljesítmény explicit módon van kiépítve és garantált.

  Ha azt tervezi, hogy az Azure-ban natív módon fogja használni az Azure-fájlmegosztást, akkor előfordulhat, hogy az Azure-fájlmegosztás nagyobb teljesítményre van szüksége. Ha ez a fajta használati lehetőség a jövőben is lehetséges, akkor a saját Storage-fiókjában egyetlen standard Azure-fájlmegosztás létrehozása a legmegfelelőbb.
* Azure-régiónként legfeljebb 250 Storage-fiók adható meg.

> [!TIP]
> Ezekkel az információkkal gyakran szükségessé válik, hogy a köteteken több legfelső szintű mappát egy közös, új gyökérkönyvtárba csoportosítson. Ezután szinkronizálja ezt az új gyökérkönyvtárat és a hozzá tartozó összes mappát egyetlen Azure-fájlmegosztás számára. Ez a módszer lehetővé teszi, hogy kiszolgálónként legfeljebb 30 Azure fájlmegosztás-szinkronizáláson belül maradjon.
>
> A közös gyökér alá tartozó csoportosítás nem befolyásolja az adataihoz való hozzáférést. Az ACL-ek maradnak. Csak úgy kell módosítania a megosztási útvonalakat (például az SMB-vagy NFS-megosztásokat), hogy a helyi kiszolgáló mappáiban módosult a közös gyökér. Semmi más nem változik.

> [!IMPORTANT]
> A Azure File Sync legfontosabb méretezési vektora a szinkronizálandó elemek (fájlok és mappák) száma. További részletekért tekintse át a [Azure file Sync méretezési célokat](../articles/storage/files/storage-files-scale-targets.md#azure-file-sync-scale-targets) .

Az ajánlott eljárás az, hogy a szinkronizálási hatókörben lévő elemek száma ne legyen alacsony. Ez fontos szempont a mappák Azure-fájlmegosztás számára történő leképezésében. A Azure File Sync egy megosztáson 100 000 000 elemekkel (fájlok és mappák) tesztelték. Azonban általában a legjobb, ha az 20 000 000-es vagy a 30 000 000-os elemek számát egyetlen megosztásban tartja. Ossza szét a névteret több megosztásra, ha elkezdi túllépni ezeket a számokat. Ha nagyjából a számok alatt marad, továbbra is csoportosíthatja több helyszíni megosztást ugyanabba az Azure-fájlmegosztásba. Ezzel a gyakorlattal megadhatja a növekedéshez szükséges helyet.

Ebben az esetben lehetséges, hogy a mappák egy halmaza képes logikailag szinkronizálni ugyanazt az Azure-fájlmegosztást (a korábban említett új, közös gyökérmappa-megközelítés használatával). Előfordulhat azonban, hogy továbbra is jobb lesz a mappák újracsoportosítása, például egy Azure-fájlmegosztás helyett kettőre szinkronizálva. Ezzel a módszerrel megtarthatja, hogy a fájlmegosztás hány fájlt és mappát használjon a kiszolgálón. A helyszíni megosztásokat és a szinkronizálást több helyszíni kiszolgálón is elvégezheti, így további 30 további Azure-fájlmegosztás esetén is szinkronizálhatja a szolgáltatást.

#### <a name="create-a-mapping-table"></a>Leképezési tábla létrehozása

:::row:::
    :::column:::
        [![Példa egy leképezési táblára. Töltse le a következő fájlt a rendszerkép tartalmának megkereséséhez és használatához.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Az előző fogalmak kombinációjának segítségével meghatározhatja, hogy hány Azure-fájlmegosztás szükséges, és hogy a meglévő adatai mely részeit fogják megállapítani az Azure-fájlmegosztás végén.
        
        Hozzon létre egy táblázatot, amely rögzíti a gondolatait, így szükség esetén hivatkozhat rá. A szervezett maradás azért fontos, mert könnyen elveszítheti a leképezési terv részleteit, ha egyszerre több Azure-erőforrást is üzembe helyez. A teljes leképezés létrehozásához a Microsoft Excel-fájlokat sablonként is letöltheti.

[//]: # (A HTML csak úgy jelenik meg, ha egy beágyazott kétoszlopos táblázatot ad hozzá a munkaképek elemzésével és szövegével vagy hiperhivatkozásával ugyanazon a sorban.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Névtér-leképezési sablon letöltése.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
