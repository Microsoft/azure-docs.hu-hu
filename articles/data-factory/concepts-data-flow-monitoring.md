---
title: Figyelési leképezési adatfolyamok
description: A leképezési adatfolyamatok vizuális figyelése Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/22/2020
ms.openlocfilehash: 9ca5ea5cdebe297af5081ae6e219935c56ba942e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004871"
---
# <a name="monitor-data-flows"></a>Adatfolyamatok figyelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyamatok kiépítésének és hibakeresésének befejezése után az adatfolyamatot úgy kell ütemezni, hogy egy folyamat kontextusában hajtsa végre az ütemtervet. Az eseményindítók használatával ütemezhet Azure Data Factory folyamatot. Vagy használhatja az aktiválás most lehetőséget a Azure Data Factory pipeline Builder-ből úgy, hogy egyetlen futtatású végrehajtást hajtson végre az adatfolyamatok folyamaton belüli teszteléséhez.

A folyamat végrehajtásakor nyomon követheti a folyamatot és az összes olyan tevékenységet, amely a folyamat során szerepel, beleértve az adatfolyam tevékenységét is. Kattintson a figyelés ikonra a bal oldali Azure Data Factory felhasználói felület paneljén. Egy, az alábbihoz hasonló képernyő jelenik meg. A Kiemelt ikonok lehetővé teszik a folyamat tevékenységeinek részletezését, beleértve az adatfolyam tevékenységeit is.

![A képernyőképen a folyamatok számára kiválasztható ikonok láthatók. További információ:.](media/data-flow/mon001.png "Adatfolyam-figyelés")

Ezen a szinten láthatja a statisztikát is, beleértve a futtatási időpontokat és az állapotot. A futtatási azonosító a tevékenység szintjén eltér a folyamat szintjén lévő futtatási AZONOSÍTÓtól. A futtatási azonosító az előző szinten a folyamathoz szükséges. A szemüvegek kiválasztásával részletes információkhoz jutunk az adatfolyamok végrehajtásáról.

![Képernyőfelvétel: a szemüvegek ikonja az adatfolyam-végrehajtás részleteinek megtekintéséhez.](media/data-flow/monitoring-details.png "Adatfolyam-figyelés")

Ha a grafikus csomópont figyelési nézetében van, az Adatfolyam-diagram egyszerűsített, csak megtekintésre kész verzióját láthatja.

![A képernyőképen a gráf csak megtekintésre szolgáló verziója látható.](media/data-flow/mon003.png "Adatfolyam-figyelés")

Az alábbi videó áttekintést nyújt az adatfolyamatok teljesítményének figyeléséről az ADF-figyelés képernyőn:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Adatfolyam-végrehajtási tervek megtekintése

Ha az adatfolyamot a Sparkban hajtja végre, Azure Data Factory meghatározza a kódok optimális elérési útját az adatfolyam teljes egészében. Emellett a végrehajtási útvonalak különböző kibővíthető csomópontokon és adatpartíción is előfordulhatnak. Ezért a figyelési gráf a folyamat kialakítását jelképezi, figyelembe véve az átalakítások végrehajtási útvonalát. Az egyes csomópontok kijelölésekor a fürtön együtt végrehajtott kódokat jelölő "Csoportosítások" láthatók. Az időzítések és a megjelenő számadatok ezeket a csoportokat jelölik, a terv egyes lépéseivel szemben.

![Képernyőfelvétel: az adatfolyam oldalát jeleníti meg.](media/data-flow/mon004.png "Adatfolyam-figyelés")

* Ha kijelöli a Megnyitás helyet a figyelés ablakban, az alsó ablaktáblán látható statisztika megjeleníti az egyes fogadók időzítését és sorait, valamint az átalakítási vonal számára a fogadó adatokhoz vezető átalakításokat.

* Ha egyéni átalakításokat választ ki, akkor a jobb oldali panelen további visszajelzések jelennek meg, amelyek a partíciós statisztikát, az oszlopok számát, a torzítást (azaz a partíciók közötti elosztott adatok egyenletes eloszlását) és a csúcsosságát (azaz a tüskés adatok) jelenítik meg.

* Amikor kijelöli a fogadót a csomópont nézetben, megtekintheti az oszlop Lineage elemét. Három különböző módszer áll rendelkezésre az oszlopok összegyűjtéséhez a fogadóban lévő összes adatfolyamban. Ezek a következők:

  * Számított: az oszlopot a feltételes feldolgozáshoz, vagy egy kifejezésen belül használja az adatfolyamatban, de a fogadóban nem landol
  * Származtatott: az oszlop egy olyan új oszlop, amelyet a folyamatában hozott létre, azaz nem volt jelen a forrásban.
  * Leképezve: az oszlop a forrástól származik, és a leképezést egy fogadó mezőhöz rendeli hozzá
  * Adatfolyam állapota: a végrehajtás aktuális állapota
  * Fürt indítási ideje: a JIT Spark számítási környezet beolvasásához szükséges idő az adatfolyam-végrehajtáshoz
  * Átalakítások száma: hány átalakítási lépést hajt végre a folyamaton belül
  
![Képernyőfelvétel: a frissítési lehetőség.](media/data-flow/monitornew.png "Adatfolyam-figyelés – új")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Teljes fogadófeldolgozási idő kontra átalakításfeldolgozási idő

Az egyes átalakítási szakaszok teljes időt biztosítanak ahhoz, hogy az adott fázis befejeződjön az egyes partíciók végrehajtási idejével együtt. Ha a fogadóra kattint, megjelenik a "fogadó feldolgozási idő". Ez az idő magában foglalja az átalakítási idő összegét, *valamint* azt az I/O-időt, amelyet az adatainak a célhelyre való írásához vett igénybe. A fogadó feldolgozási ideje és az átalakítás összege közötti különbség az az I/O-idő, amellyel az adatmennyiséget el kell írni.

Az egyes partíció-transzformációs lépések részletes időzítését is megtekintheti, ha a JSON-kimenetet az ADF-folyamat figyelési nézetében nyitja meg az adatfolyam-tevékenységből. A JSON az egyes partíciók esetében ezredmásodperces időzítést tartalmaz, míg az UX-figyelési nézet a partíciók összesített időzítése, amely együtt bővült:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Feldolgozás utáni idő

Amikor kijelöl egy fogadó transzformációs ikont a térképen, a jobb oldalon található becsúsztatható panel egy további adatpontot jelenít meg, amelynek alján a "post Processing Time" (feldolgozás időpontja) felirat jelenik meg. Az adatok betöltését, átalakítását és írását *követően* ez az idő a Spark-fürtön végzett feladatok végrehajtásával töltött időt. Ez az idő magában foglalhatja a kapcsolatok lezárását, az illesztőprogramok leállítását, a fájlok törlését, a coalescing-fájlokat stb. Ha olyan műveleteket hajt végre a folyamatokban, mint például a "fájlok áthelyezése" és a "kimenet egyetlen fájlba", akkor valószínű, hogy növekedni fog a post Processing Time értéke.
  
## <a name="error-rows"></a>Hibák sorai

Az adatfolyam-fogadóban az adatfolyam-fogadóban a hibák sorba való kezelésének engedélyezése a figyelési kimenetben jelenik meg. Ha a "jelentés sikeressége hiba esetén" értékre állítja a fogadót, a figyelési kimenetben a sikeres és sikertelen sorok száma jelenik meg, amikor a fogadó figyelés csomópontra kattint.

![A képernyőfelvételen a hibák sorai láthatók.](media/data-flow/error-row-2.png "A hiba sor figyelése sikeres")

Ha a "hiba jelentése hibával" lehetőséget választja, a kimenet csak a tevékenység figyelése kimeneti szövegben jelenik meg. Ennek az az oka, hogy az adatfolyam tevékenysége sikertelen lesz a végrehajtáshoz, és a részletes figyelési nézet nem lesz elérhető.

![A képernyőfelvétel a tevékenységben lévő hibák sorát jeleníti meg.](media/data-flow/error-rows-4.png "Hiba sor figyelési hibája")

## <a name="monitor-icons"></a>Ikonok figyelése

Ez az ikon azt jelenti, hogy az átalakítási adatgyűjtés már gyorsítótárazva van a fürtön, így az időzítések és a végrehajtás elérési útja figyelembe vette a következőt:

![A képernyőképen a lemez ikon látható.](media/data-flow/mon005.png "Adatfolyam-figyelés")

Az átalakításban a zöld kör ikonjai is megjelennek. Ezek a mosdók számát jelölik, amelyekben az adatforgalom beáramlik.
