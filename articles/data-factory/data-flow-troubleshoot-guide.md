---
title: Az adatfolyamatok leképezésének hibakeresése
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory az adatfolyamokkal kapcsolatos problémákat.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: cc87694686bd5143b03d690286bd3171cf8b0e18
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222149"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory adatforgalmának hibáinak megoldása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az adatfolyamatok Azure Data Factoryban történő leképezésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **Okok**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik vagy elérhető-e.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian

- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztéshez implicit Descartes termék nem támogatott. Ha az illesztésben használt oszlopok létrehozzák az egyedi kulcsot, a kapcsolat mindkét oldalán legalább egy oszlopot kötelező megadni.
- **Javaslat**: a nem egyenlőségen alapuló összekapcsolásokhoz egyéni kereszt-illesztést kell választania.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson

- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: nem támogatott kódolás, sérült bájtok vagy JSON-forrás használata több beágyazott sorban lévő egyetlen dokumentumként
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a "JSON-beállítások" elemet, és kapcsolja be az "egyetlen dokumentum" beállítást.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout

- **Üzenet**: szórásos csatlakozás időtúllépési hibája, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül, hibakeresési futtatások és 300 a feladat futtatásakor
- **Okok**: a szórás a hibakeresési futtatásokban és a 300 másodpercben, a feladatok futtatásakor alapértelmezett időkorláttal rendelkezik 60 mp. Úgy tűnik, hogy a szórásra kiválasztott stream túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget lehessen létrehozni
- **Javaslat**: a csatlakozáshoz, a létezéshez és a kereséshez tekintse meg az optimalizálás lapot az adatfolyam-transzformációk lapon. A szórás alapértelmezett beállítása az "Auto". Ha az "automatikus" beállítás be van állítva, vagy ha manuálisan állítja be a bal vagy a jobb oldali szórást a "rögzített" érték alatt, akkor beállíthat nagyobb Azure Integration Runtime konfigurációt, vagy kikapcsolhatja a szórást. Az Adatáramlások legjobb teljesítményének ajánlott megközelítése, hogy a Spark az "Auto" használatával közvetítse az "automatikus", és használja a memóriára optimalizált Azure IR.

Ha hibakeresési folyamat futása során hajtja végre az adatáramlást hibakeresési teszt végrehajtása során, akkor gyakrabban futtathatja ezt az állapotot. Ennek az az oka, hogy az ADF leszabályozza a szórási időtúllépést 60 mp-re a gyorsabb hibakeresési élmény fenntartása érdekében. Ha azt szeretné, hogy a rendszer kiterjessze az aktivált Futtatás 300 másodperces időtúllépését, használja a hibakeresési > a tevékenység-futtatókörnyezet használata lehetőséget, hogy kihasználja az adatfolyam-feldolgozási folyamat végrehajtása tevékenységben definiált Azure IR.

### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió

- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **Okok**: az adatértékek nem a várt formátumban vannak
- **Javaslat**: használja a megfelelő adattípust

### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-végrehajtó-InvalidColumn

- **Üzenet**: az oszlop nevét meg kell adni a lekérdezésben, egy aliast kell beállítania, ha SQL-függvényt használ
- **Okok**: nincs megadva oszlopnév
- **Javaslat**: alias beállítása, ha olyan SQL-függvényt használ, mint a min ()/Max (), stb.

 ### <a name="error-code-df-executor-drivererror"></a>Hibakód: DF-végrehajtó-DriverError
- **Üzenet**: a INT96 olyan örökölt időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.
- **Okok**: illesztőprogram-hiba
- **Javaslat**: a INT96 elavult időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **Okok**: legfeljebb 100 000 nem véglegesített blokk lehet egy blobban.
- **Javaslat**: további részletekért forduljon a Microsoft termék csapatához a probléma kapcsán

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például <Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (például <Source Path> /<Partition Root Directory 1>/a = 10/b = 20, <Source Path> /mappa 2/file1), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útja több particionált címtárral vagy particionált könyvtárral rendelkezik más fájlokkal vagy nem particionált címtárral.
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Hibakód: DF-végrehajtó-működése OutOfMemoryError
- **Üzenet**: a fürt kifogyott a memóriából a végrehajtás során, próbálkozzon újra egy integrációs modul használatával, amely nagyobb alapszámmal és/vagy memória-optimalizált számítási típussal rendelkezik.
- **Okok**: a fürt elfogyott a memóriából
- **Javaslat**: a hibakeresési fürtök fejlesztési célokra szolgálnak. Használja ki az adatok mintavételezését, a megfelelő számítási típust és méretet az adattartalom futtatásához. A legjobb teljesítmény elérése érdekében tekintse meg a [leképezési folyamat teljesítményének útmutatóját](concepts-data-flow-performance.md) a hangoláshoz.

 ### <a name="error-code-df-executor-illegalargument"></a>Hibakód: DF-végrehajtó-illegalArgument
- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa
- **Okok**: helytelen a fiók neve vagy a hozzáférési kulcs
- **Javaslat**: Ellenőrizze, hogy helyes-e a társított szolgáltatásban megadott fiók neve vagy elérési kulcsa. 

 ### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **Okok**: nem kompatibilis adattípusok a deklarált típus és a tényleges paraméter értéke között
- **Javaslat**: Győződjön meg róla, hogy az átadott paraméterérték megfelel a deklarált típusnak.

 ### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen
- **Okok**: érvénytelen vagy nem érhető el a kifejezésekben használt oszlopnév
- **Javaslat**: a kifejezésekben használt oszlop neve (i)

 ### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető
- **Okok**: a kifejezés formázása miatt hibákat elemez
- **Javaslat**: a kifejezés formázásának keresése

### <a name="error-code-getcommand-outputasync-failed"></a>Hibakód: a GetCommand OutputAsync sikertelen

- **Üzenet**: az adatfolyam hibakeresése és az adatelőnézet: a GetCommand OutputAsync sikertelen volt...
- **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
- **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Hibakód: váratlan kivétel történt, és a végrehajtás nem sikerült

- **Üzenet**: az adatfolyam tevékenységének végrehajtása során: váratlan kivétel történt, és a végrehajtás nem sikerült.
- **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
- **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Hibakód: az adatelőnézet hibakeresése kimeneti adatokat nem a csatlakozáskor

- **Üzenet**: nagy számú Null érték vagy hiányzó érték áll rendelkezésre, amelyek oka lehet a túl kevés sor mintavételének. Próbálja meg frissíteni a hibakeresési sor korlátját, és frissítse az adatforrást.
- **Okok**: az illesztési feltétel nem felelt meg egyetlen sornak sem, vagy nagy MENNYISÉGű nullát eredményezett az adatelőnézet során.
- **Javaslat**: lépjen a hibakeresési beállítások menüpontra, és növelje a sorok számát a Sorforrás korlátján belül. Ellenőrizze, hogy van-e olyan Azure IR, amely elég nagy adatáramlási fürttel rendelkezik a további adatkezelés érdekében.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Hibakód: érvényességi hiba a forrásnál többsoros CSV-fájlokkal 

- **Üzenet**: az alábbi hibaüzenetek egyike jelenhet meg:
   - Az utolsó oszlop null értékű vagy hiányzik.
   - A séma érvényesítése a forrásnál meghiúsul.
   - A séma importálása nem sikerül helyesen megjeleníteni a UX-ben, és az utolsó oszlopban egy új sor karakter szerepel a névben.
- **Okok**: a leképezési adatfolyamban jelenleg a TÖBBsoros CSV-forrás nem működik együtt a \r\n. Előfordulhat, hogy a szállításban az extra sorok a break forrás értékeit adja vissza. 
- **Javaslat**: a (z)%-os fájlt a forrásban, a \r\n. helyett a \n sort kell előállítani. A másolási tevékenység használatával a CSV-fájlt \r\n és \n értékre is konvertálhatja.

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **Okok**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik vagy elérhető-e.


 ### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian
- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztéshez implicit Descartes termék nem támogatott. Ha az illesztésben használt oszlopok létrehozzák az egyedi kulcsot
- **Javaslat**: a nem egyenlőségen alapuló összekapcsolások esetén a csatlakozást kell választania.


 ### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson
- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: nem támogatott kódolás, sérült bájtok vagy JSON-forrás használata több beágyazott sorban lévő egyetlen dokumentumként
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a "JSON-beállítások" elemet, és kapcsolja be az "egyetlen dokumentum" beállítást.


 ### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout
- **Üzenet**: szórásos csatlakozás időtúllépési hibája esetén a probléma elkerülése érdekében a csatlakozás/létező/keresési átalakítás lehetőségnél kiválaszthatja a szórásos küldés lehetőséget. Ha a teljesítmény javítása érdekében szeretne csatlakozni a csatlakozáshoz, akkor győződjön meg arról, hogy a szórásos stream 60 másodpercen belül képes adatok létrehozására a hibakeresési és a 300-es mp-ben a feladatok futtatásakor.
- **OK**: a szórás a hibakeresési és a 300-es mp-es alapértelmezett időkorlátot (60 mp) tartalmaz a feladatok futtatásához. A szórásos csatlakozás esetében a szórásra kiválasztott adatfolyam túl nagynak tűnik, hogy a korláton belüli adatmennyiséget hozzon létre. Ha nem használ szórásos csatlakozást, a adatfolyam által végzett alapértelmezett szórás elérheti ugyanezt a korlátot
- **Javaslat**: kapcsolja ki a szórási beállítást, vagy Kerülje a nagyméretű adatfolyamok szórását, ahol a feldolgozás több mint 60 másodpercet vehet igénybe. Válasszon kisebb streamet a szóráshoz. A nagyméretű SQL/DW táblák és forrásfájlok általában rossz jelöltek. Szórásos csatlakozás hiányában használjon nagyobb fürtöt, ha a hiba történik.


 ### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió
- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **Okok**: az adatértékek nem a várt formátumban vannak
- **Javaslat**: használja a megfelelő adattípust


 ### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-végrehajtó-InvalidColumn
- **Üzenet**: az oszlop nevét meg kell adni a lekérdezésben, egy aliast kell beállítania, ha SQL-függvényt használ
- **Okok**: nincs megadva oszlopnév.


 ### <a name="error-code-df-executor-drivererror"></a>Hibakód: DF-végrehajtó-DriverError
- **Üzenet**: a INT96 olyan örökölt időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.
- **Okok**: illesztőprogram-hiba.
- **Javaslat**: a INT96 olyan örökölt időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.


 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **Okok**: legfeljebb 100 000 nem véglegesített blokk lehet egy blobban.
- **Javaslat**: további részletekért vegye fel a kapcsolatot a Microsoft termék csapatával.

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például <Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (pl. <Source Path> /<Partition gyökérkönyvtár 1>/a = 10/b = 20, <Source Path> /mappa 2/file1), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útja több particionált címtárral vagy particionált könyvtárral rendelkezik más fájlokkal vagy nem particionált címtárral.
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.


 ### <a name="error-code-df-executor-outofmemoryerror"></a>Hibakód: DF-végrehajtó-működése OutOfMemoryError
- **Üzenet**: a fürt kifogyott a memóriából a végrehajtás során, próbálkozzon újra egy integrációs modul használatával, amely nagyobb alapszámmal és/vagy memória-optimalizált számítási típussal rendelkezik.
- **Okok**: a fürt elfogyott a memóriából.
- **Javaslat**: a hibakeresési fürtök fejlesztési célokra szolgálnak. Használja ki az adatok mintavételezésének megfelelő számítási típust és méretet az adattartalom futtatásához. A legjobb teljesítmény érdekében tekintse meg a [adatfolyam Performance útmutatót](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) a adatfolyamok hangolásához.


 ### <a name="error-code-df-executor-illegalargument"></a>Hibakód: DF-végrehajtó-illegalArgument
- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa.
- **OK**: a fiók neve vagy a hozzáférési kulcs helytelen.
- **Javaslat**: adja meg a megfelelő fiók nevét vagy elérési kulcsát.


 ### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **Okok**: nem kompatibilis adattípusok a deklarált típus és a tényleges paraméter értéke között
- **Javaslat**: adja meg a megfelelő adattípusokat.


 ### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen.
- **OK**: érvénytelen vagy nem elérhető oszlopnév van használatban a kifejezésekben.
- **Javaslat**: a kifejezésekben használt oszlop neve (i).


 ### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető.
- **OK**: a kifejezés formázása miatt hibákat elemez.
- **Javaslat**: a kifejezés formázásának megkeresése.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Hibakód: DF-végrehajtó-OutOfDiskSpaceError
- **Üzenet**: belső kiszolgálóhiba
- **Okok**: a fürtön elfogyott a szabad lemezterület.
- **Javaslat**: próbálkozzon újra a folyamattal. Ha a probléma továbbra is fennáll, forduljon az ügyfél-támogatási szolgálathoz.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Hibakód: DF-végrehajtó-StoreIsNotDefined
- **Üzenet**: az áruház konfigurációja nincs definiálva. Ezt a hibát a folyamat Érvénytelen paraméter-hozzárendelése okozta.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékének hozzárendelését a folyamatban. A paraméter kifejezése érvénytelen karaktereket tartalmazhat.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Hibakód: DF-Excel-InvalidConfiguration
- **Üzenet**: az Excel-lapok neve vagy indexe kötelező.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adja meg a táblázat nevét vagy indexét az Excel-adatok olvasásához.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Hibakód: DF-Excel-InvalidConfiguration
- **Üzenet**: az Excel-lapok neve és indexe nem létezhet egyszerre.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adja meg a táblázat nevét vagy indexét az Excel-adatok olvasásához.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Hibakód: DF-Excel-InvalidConfiguration
- **Üzenet**: érvénytelen tartomány van megadva.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adjon meg érvényes tartományt a hivatkozás: [Excel-tulajdonságok](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties)alapján.


 ### <a name="error-code-df-excel-invaliddata"></a>Hibakód: DF-Excel-InvalidData
- **Üzenet**: az Excel-munkalap nem létezik.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adjon meg érvényes nevet vagy indexet az Excel-adatok olvasásához.

 ### <a name="error-code-df-excel-invaliddata"></a>Hibakód: DF-Excel-InvalidData
- **Üzenet**: az Excel-fájlok más sémával való olvasása most nem támogatott.
- **Okok**: meghatározatlan
- **Javaslat**: a megfelelő Excel-fájl használata.


 ### <a name="error-code-df-excel-invaliddata"></a>Hibakód: DF-Excel-InvalidData
- **Üzenet**: az adattípus nem támogatott.
- **Okok**: meghatározatlan
- **Javaslat**: használja az Excel-fájl megfelelő adattípusait.

 ### <a name="error-code-df-excel-invalidconfiguration"></a>Hibakód: DF-Excel-InvalidConfiguration
- **Üzenet**: érvénytelen Excel-fájl van megadva, amíg csak az. xlsx és az. xls támogatott
- **Okok**: meghatározatlan
- **Javaslat**: Győződjön meg róla, hogy az Excel fájlkiterjesztés vagy. xlsx vagy. xls.

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató
1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban keresse fel a társított szolgáltatást minden Ön által használt adatkészlet esetében, és tesztelje a kapcsolatokat.
2. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Kapcsolja be a hibakeresést, és kattintson az adatelőnézetre a forrás-átalakításokban, és győződjön meg arról, hogy képes hozzáférni az adataihoz.
3. Ha minden jól látható az adatok előnézetében, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:
*  [Data Factory blog](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Az ADF-leképezés adatforgalmának teljesítmény-útmutatója](concepts-data-flow-performance.md)
