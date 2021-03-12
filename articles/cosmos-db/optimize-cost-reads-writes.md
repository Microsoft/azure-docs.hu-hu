---
title: A kérések díjainak optimalizálása Azure Cosmos DBban
description: Ez a cikk azt ismerteti, hogyan optimalizálható a költségek Azure Cosmos DBre vonatkozó kérelmek kiállításakor.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633852"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>A kérések díjszabásának optimalizálása Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk azt ismerteti, hogy az olvasási és írási kérések hogyan fordíthatók le a kérelmek [egységei](request-units.md) között, és hogyan optimalizálható a kérelmek díja. Olvasási műveletek: pont olvasások és lekérdezések. Az írási műveletek közé tartozik az elemek beszúrása, cseréje, törlése és upsert.

Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, amelyek a tárolóban lévő elemeken működnek. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához szükséges erőforrások esetében.

## <a name="measuring-the-ru-charge-of-a-request"></a>Kérelem RU-díjainak mérése

Fontos, hogy megmérjék a kérések RU-díjait, hogy megértsék a tényleges költségeket, és értékeljék az optimalizáció hatékonyságát is. Ezt a díjat a Azure Portal használatával vagy a Azure Cosmos DB az egyik SDK-n keresztül visszaküldött válasz vizsgálatával kérheti le. A szolgáltatás elérésének részletes ismertetését lásd: [a kérési egység díjszabása Azure Cosmos db](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Adatolvasások: pont olvasási és lekérdezési pontjai

Az olvasási műveletek a Azure Cosmos DB általában a leggyorsabb/leghatékonyabbtól a lassabb/kevésbé hatékonyak szerint vannak rendezve, a következőképpen:  

* Pont olvasása (kulcs/érték keresése egyetlen elem-AZONOSÍTÓn és partíciós kulcson).
* Lekérdezés szűrő záradékkal egyetlen partíciós kulcson belül.
* Lekérdezés egyenlőség vagy Range Filter záradék nélkül bármely tulajdonság esetében.
* Lekérdezés szűrő nélkül.

### <a name="role-of-the-consistency-level"></a>A konzisztencia szintjének szerepe

Az **erős** vagy **határolt elavult** [konzisztencia-szintek](consistency-levels.md)használatakor az olvasási művelet (pont olvasása vagy lekérdezése) ru-díja megkétszereződik.

### <a name="point-reads"></a>Pont olvasások

Az egyetlen tényező, amely hatással van egy pont (a használt konzisztencia mellett) RU-díját is, a beolvasott elem mérete. Az alábbi táblázat az 1 KB-os és 100 KB méretű elemek esetében az RU-t mutatja.

| **Elemek mérete** | **Egy pont beolvasott díja** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

Mivel a pont olvasása (a kulcs/érték keresése az elem AZONOSÍTÓján) a leghatékonyabb típusú olvasás, az elem AZONOSÍTÓjának értelmes értékkel kell rendelkeznie, hogy az elemeket leolvassa (lekérdezés helyett), ha lehetséges.

### <a name="queries"></a>Lekérdezések

A lekérdezések kérelmi egységei számos tényezőtől függenek. Például a betöltött/visszaadott Azure Cosmos-elemek száma, az indexen végzett keresések száma, a lekérdezés fordítási ideje stb. részleteit. Azure Cosmos DB garantálja, hogy ugyanaz a lekérdezés, amikor ugyanazon az adattárban hajtja végre ugyanazt a kérést, még ismétlődő végrehajtás esetén is ugyanazokat a kérelmeket fogja használni. A lekérdezés-végrehajtási mérőszámokat használó lekérdezési profil jó ötlet a kérési egységek elköltésének módjára.  

Bizonyos esetekben előfordulhat, hogy egy 200-es és 429-as számú választ, valamint változó kérési egységeket fog látni a lekérdezések lapozható végrehajtása során, mert a lekérdezések az elérhető RUs alapján a lehető leggyorsabban futnak. Előfordulhat, hogy a lekérdezés végrehajtásának megszakítása több oldalra vagy oda, a kiszolgáló és az ügyfél között. Például az 10 000-es elemek több oldalként is visszaadhatók, az egyes díjak az adott laphoz végrehajtott számítás alapján. Ezen lapok összegzése esetén ugyanannyi RUst kell lekérnie, mint a teljes lekérdezéshez.

#### <a name="metrics-for-troubleshooting-queries"></a>A lekérdezések hibaelhárítási mérőszámai

A lekérdezések által felhasznált teljesítmény és átviteli sebesség (beleértve a felhasználó által definiált függvényeket is) többnyire a Function törzstől függ. A legkönnyebben megtudhatja, hogy mennyi idő telik el a lekérdezés végrehajtásának az UDF-ben való elköltése és a felhasznált RUs száma. ehhez engedélyezze a lekérdezési metrikákat. Ha a .NET SDK-t használja, az SDK a következő lekérdezési mérőszámokat adja vissza:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Ajánlott eljárások a lekérdezések optimalizálásához 

Vegye figyelembe a következő ajánlott eljárásokat a lekérdezések a Cost szolgáltatáshoz való optimalizálásakor:

* **Több entitás típusának elhelyezése**

   Próbáljon meg több entitást is elhelyezni egyetlen vagy kevesebb tárolón belül. Ez a módszer nem csupán árképzési szempontból, hanem lekérdezés-végrehajtáshoz és tranzakcióhoz is biztosít előnyöket. A lekérdezések hatóköre egyetlen tárolóra terjed ki. a tárolt eljárások/eseményindítók használatával több rekordon keresztüli atomi tranzakciók hatóköre egyetlen tárolón belüli partíciós kulcsra terjed ki. Az azonos tárolóban lévő entitások közös elhelyezésével csökkentheti a hálózati kerek utak számát a rekordok közötti kapcsolatok feloldásához. Így növeli a végpontok közötti teljesítményt, lehetővé teszi, hogy az atomi tranzakciók több rekordon is elérhetők legyenek egy nagyobb adatkészletnél, és ennek eredményeképpen csökkennek a költségek. Ha egy vagy több tárolóban több entitást is megnehezítik a forgatókönyv esetében, általában azért, mert egy meglévő alkalmazást telepít át, és nem kívánja végrehajtani a kód módosítását, érdemes megfontolnia az adatátvitelt az adatbázis szintjén.  

* **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

   A lekérdezés bonyolultsága befolyásolja, hogy hány kérési egység (RUs) van használatban egy művelethez. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok mérete. Az összes tényező befolyásolja a lekérdezési műveletek költségeit. 

Azure Cosmos DB kiszámítható teljesítményt biztosít az átviteli sebesség és a késés tekintetében a kiépített átviteli sebességi modell használatával. A kiépített átviteli sebesség a [kérelmek](request-units.md) száma másodpercenként, vagy ru/s. A kérési egység (RU) olyan számítási erőforrások (például CPU, memória, IO stb.) logikai absztrakciója, amelyek szükségesek a kérelem végrehajtásához. A kiépített átviteli sebesség (RUs) be van állítva, és a tárolóra vagy az adatbázisra van beállítva, hogy kiszámítható teljesítményt és késést biztosítson. A kiépített átviteli sebesség lehetővé teszi, hogy a Azure Cosmos DB kiszámítható és konzisztens teljesítményt, garantált alacsony késést és magas rendelkezésre állást biztosítson bármilyen méretben. A kérelmek egységei a normalizált pénznemet jelentik, amely leegyszerűsíti az alkalmazás által igényelt erőforrások számának okát.

A kérelem fejlécében visszaadott kérelem díja jelzi az adott lekérdezés költségeit. Ha például egy lekérdezés 1000 1 – KB elemet ad vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) cikk és a kérési egység kalkulátora.

## <a name="writing-data"></a>Adatírás

Egy elem írásának RU-díja a következőktől függ:

- Az elemek mérete.
- Az [indexelési házirend](index-policy.md) által lefedett tulajdonságok száma, és indexelésre van szükség.

1 KB-os tétel beszúrása a költségek indexelése nélkül ~ 5,5 RUs. Ha az adott tételt az adott tétel behelyezéséhez szükséges díjat kétszer cseréli le.

### <a name="optimizing-writes"></a>Írások optimalizálása

Az írási műveletek RU-díjainak optimalizálásának legjobb módja az elemek megfelelő méretben helyezheti és az indexelt tulajdonságok száma.

- A nagyon nagy méretű elemek tárolása Azure Cosmos DB magas RU-díjakat eredményez, és Anti-pattern lehet. Különösen Ne tároljon olyan bináris tartalmat vagy nagy méretű adathalmazokat, amelyeket nem kell lekérdezni. Az ajánlott eljárás az, ha ilyen típusú adattípusokat helyez el az [Azure Blob Storageban](../storage/blobs/storage-blobs-introduction.md) , és egy hivatkozást (vagy hivatkozást) tárol a blobhoz a Azure Cosmos DBba írt elemben.
- Az indexelési szabályzat optimalizálásával csak azokat a tulajdonságokat lehet indexelni, amelyeket a lekérdezési szűrő on használhat az írási műveletek által felhasznált RUs óriási különbsége. Új tároló létrehozásakor az alapértelmezett indexelési házirend indexeli az elemekben található minden egyes tulajdonságot. Habár ez egy jó alapértékű fejlesztési tevékenység, erősen ajánlott az indexelési szabályzat ismételt kiértékelése és [testre szabása](how-to-manage-indexing-policy.md) az éles környezetben, illetve ha a számítási feladat jelentős forgalmat kezd.

Ha tömeges betöltést végez, akkor azt javasoljuk, hogy használja a [Azure Cosmos db tömeges végrehajtó könyvtárat](bulk-executor-overview.md) , mivel az az ilyen műveletek ru-felhasználásának optimalizálására lett kialakítva. Igény szerint a [Azure Data Factory](../data-factory/introduction.md) is használhatja, amely ugyanarra a könyvtárra épül.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
* További információ a [Azure Cosmos db fenntartott kapacitásról](cosmos-db-reserved-capacity.md)
