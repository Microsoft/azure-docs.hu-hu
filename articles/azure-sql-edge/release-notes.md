---
title: Az Azure SQL Edge kibocsátási megjegyzései
description: A kibocsátási megjegyzések részletesen ismertetik az Azure SQL Edge-lemezképek újdonságait vagy változásait.
keywords: kibocsátási megjegyzések – SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 6218715878ec40fecee79f1c93bf2ca1820af007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277011"
---
# <a name="azure-sql-edge-release-notes"></a>Az Azure SQL Edge kibocsátási megjegyzései 

Ez a cikk az Azure SQL Edge minden új buildének újdonságait és változásait ismerteti.

## <a name="azure-sql-edge-103"></a>Azure SQL Edge 1.0.3

SQL Engine-Build 15.0.2000.1554

### <a name="fixes"></a>Javítások

- ONNX-futtatókörnyezet frissítése 1.5.3-re
- Frissítés a Microsoft. SqlServer. DACFx verziójára 150.5084.2
- Egyéb hibajavítások  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL Engine-Build 15.0.2000.1554

### <a name="fixes"></a>Javítások

- T-SQL streaming  
   - Az adatfolyam-objektumok tulajdonjogának és engedélyeinek javítása
   - Naplózási javítás a naplók elforgatásával és a napló előjavításával
   - Azure Stream Analytics: a naplózás továbbfejlesztése, a hibakódok és hibaüzenetek javítása az adaptereken 

- ONNX
    - Hibajavítások a párhuzamos lekérdezési forgatókönyvek és a modell-karbantartási hibák esetén
    - A ONNX futtatókörnyezet frissítése a 1.5.1-re

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL Engine-Build 15.0.2000.1553

### <a name="whats-new"></a>Újdonságok

- A számított oszlopokban definiált Date_Bucket kifejezések engedélyezése.

### <a name="fixes"></a>Javítások

- Adatmegőrzési szabályzat javítása egy olyan tábla eldobásához, amelynek adatmegőrzési szabályzata végtelen időkorlát miatt engedélyezve van
- A DacFx üzembe helyezésének támogatása a folyamatos átviteli funkciók és a megőrzési szabályzat funkciói számára 
- A DacFx üzembe helyezésének javítása egy beágyazott mappából a SAS URL-címeken való üzembe helyezésének engedélyezéséhez 
- A hosszú oszlopnevek támogatásának ELŐREJELZÉSe a hibaüzenetekben

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL Engine-Build 15.0.2000.1552

### <a name="whats-new"></a>Újdonságok
- Az Ubuntu 18,04-alapú tároló-lemezképek 
- A és `IGNORE NULL` a `RESPECT NULL` `LAST_VALUE()` `FIRST_VALUE()` függvények támogatása 
- Megbízhatósági változások az ELŐREJELZÉShez a ONNX
- Az adatmegőrzési szabályzat alapján történő tisztítás támogatása:
   - Az adatmegőrzési karbantartási feladat gyűrűs pufferének támogatása a hibaelhárításhoz
- Új funkció támogatása: 
   - Gyors helyreállítás
   - Lekérdezések automatikus finomhangolása
   - Párhuzamos végrehajtási forgatókönyvek
- Az alacsony energiaellátású üzemmódok energiatakarékos fejlesztése
- A streaming új funkcióinak támogatása: 
   - [Pillanatkép-Windows](/stream-analytics-query/snapshot-window-azure-stream-analytics): az új ablak típusa lehetővé teszi, hogy az egy időben megjelenő események alapján csoportosítsa azokat.
   - Az [TopOne](/stream-analytics-query/topone-azure-stream-analytics) és a [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) analitikai függvényekként is engedélyezhető. Az Ön által választott oszlop alapján megrendelt rekordokat adhat vissza. Nem kell egy ablak részét képezniük. 
   - A [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)fejlesztése. 

### <a name="fixes"></a>Javítások
- További hibaüzenetek és részletek a T-SQL streaming-műveletek hibaelhárításához 
- Az akkumulátor élettartamának fenntartása üresjárati módban 
- A T-SQL streaming Engine javításai: 
   - Leállított folyamatos átviteli feladatok karbantartása 
   - Javítások a honosításhoz 
   - Továbbfejlesztett Unicode-kezelési 
   - Továbbfejlesztett hibakeresés az SQL Edge T-SQL streaming szolgáltatáshoz, amely lehetővé teszi a felhasználók számára, hogy lekérdezzenek a feladatok meghibásodásával kapcsolatos hibákat get_streaming_job
- Karbantartás adatmegőrzési házirend alapján: 
    - Az adatmegőrzési szabályzat létrehozási és tisztítási forgatókönyveit érintő javítások
- Javítások a háttérben futó időzítő feladataihoz az alacsony energiaellátású üzemmódhoz szükséges energiamegtakarítás javítása érdekében

### <a name="known-issues"></a>Ismert problémák 
- A Date_Bucket T-SQL függvény nem használható számított oszlopban.


## <a name="ctp-23"></a>CTP 2,3
SQL Engine-Build 15.0.2000.1549
### <a name="whats-new"></a>Újdonságok
- Egyéni eredetek támogatása a Date_Bucket () függvényben 
- BACPAC-fájlok támogatása az SQL-telepítés részeként
- Az adatmegőrzési szabályzat alapján történő tisztítás támogatása:      
   - DDL-támogatás az adatmegőrzési szabályzat engedélyezéséhez 
   - Tárolt eljárások és a háttér-karbantartási feladat karbantartása
   - Kibővített események a karbantartási feladatok figyeléséhez

### <a name="fixes"></a>Javítások
- További hibaüzenetek és részletek a T-SQL streaming-műveletek hibaelhárításához 
- Az akkumulátor élettartamának fenntartása üresjárati módban 
- T-SQL streaming Engine: 
   - Rögzített vízjel kijavítása a beragadt kiugró ablakban 
   - Javítsa ki a keretrendszer kivétel-kezelését, hogy a rendszer felhasználói beavatkozást okozó hibaként gyűjtsön


## <a name="ctp-22"></a>CTP 2,2
SQL Engine-Build 15.0.2000.1546
### <a name="whats-new"></a>Újdonságok
- Nem legfelső szintű tárolók támogatása 
- A használati és diagnosztikai adatok gyűjtésének támogatása 
- T-SQL streaming-frissítések:
   - Unicode-karakterek támogatása a stream-objektumok neveinél

### <a name="fixes"></a>Javítások
- T-SQL streaming-frissítések:
   - Folyamatok tisztításának fejlesztése
   - A naplózás és a diagnosztika fejlesztése
- Az adatfeldolgozás teljesítményének növelése

## <a name="ctp-21"></a>CTP 2,1 
SQL Engine-Build 15.0.2000.1545
### <a name="fixes"></a>Javítások
- A PREDIKTÍV ONNX modellek használata a CPUID-problémák kezelésére a ARM-ben 
- A hiba elérési útjának jobb kezelése a T-SQL streaming indításakor
- A vízjelek késleltetésének korrigált értéke a feladatok metrikájában, ha nincs adat.
- Javítsa ki a kimeneti adapter hibáját, ha az adapterhez változó séma tartozik a kötegek között.  

## <a name="ctp-20"></a>CTP 2,0 
SQL Engine-Build 15.0.2000.1401
### <a name="whats-new"></a>Újdonságok
-   A termék neve frissítve az *Azure SQL Edge* -be
-  Date_Bucket függvény:
    - A dátum-, idő-és DateTime-típusok támogatása
- ELŐREJELZÉS a ONNX:
    - A FUTÁSIDEJŰ paraméter ONNX-követelménye  
- T-SQL streaming-támogatás (korlátozott előzetes verzió) 
 
### <a name="known-issues"></a>Ismert problémák

- Probléma: lehetséges hibák a DACPAC alkalmazás indításakor az időzítési probléma miatt.
- Áthidaló megoldás: indítsa újra SQL Server. Ellenkező esetben a tároló újra megpróbálja alkalmazni a DACPAC.

### <a name="request-support"></a>Támogatás kérése
A támogatási [oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)támogatást kérhet. Válassza ki a következő mezőket: 
- **Problématípus**: *Technikai* 
- **Szolgáltatás**: *IoT Edge*
- **Probléma típusa**: *a probléma egy IoT Edge modulhoz kapcsolódik*
- **Probléma altípusa**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="A minta támogatási jegyét bemutató képernyőkép.":::

## <a name="ctp-15"></a>CTP 1,5
SQL Engine-Build 15.0.2000.1331
### <a name="whats-new"></a>Újdonságok
- Date_Bucket függvény:
    - A DateTimeOffset-típus támogatása
- ELŐREJELZÉS ONNX-modellekkel:
  - NVARCHAR-támogatás
 
## <a name="ctp-14"></a>CTP 1,4
SQL Engine-Build 15.0.2000.1247
### <a name="whats-new"></a>Újdonságok
-   ELŐREJELZÉS ONNX-modellekkel:
    - VARCHAR-támogatás
    - Áttelepítés a ONNX Runtime 1,0-es verziójára 

- A következő funkciók engedélyezettek:
  - CDC-támogatás
  - Előzmények táblázat tömörítéssel
  - Nagyobb léptékű tényező a napló olvasás előtt
  - Batch Mode ES szűrő pushdown
  - Olvasási előre optimalizálás
 
## <a name="ctp-13"></a>CTP 1,3
SQL Engine-Build 15.0.2000.1147
### <a name="whats-new"></a>Újdonságok
- Azure IoT-portál üzembe helyezése: 
    - Az AMD64-és ARM-lemezképek üzembe helyezésének támogatása
    - A folyamatos átviteli feladatok létrehozásának támogatása
    - DACPAC üzembe helyezése
- ELŐREJELZÉS ONNX-modellekkel:
    - Numerikus típusú támogatás
- A következő funkciók engedélyezettek:
    - Pushdown összesítése az oszlopok tárolására
    - Merry-go-round vizsgálatok
- A lábnyom és a memória-felhasználás csökkentési munkája
