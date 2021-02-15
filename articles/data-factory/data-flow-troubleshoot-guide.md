---
title: Az adatfolyamatok leképezésének hibakeresése
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory az adatfolyamokkal kapcsolatos problémákat.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523005"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory adatforgalmának hibáinak megoldása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az adatfolyamatok Azure Data Factoryban történő leképezésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-error-codes-and-messages"></a>Gyakori hibakódok és üzenetek 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **Okok**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik vagy elérhető-e.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian

- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztéshez implicit Descartes termék nem támogatott. Ha az oszlopok az illesztésben használatosak, akkor a kapcsolat mindkét oldalán lévő egyedi kulcs megadása kötelező.
- **Javaslat**: a nem egyenlőségen alapuló összekapcsolásokhoz egyéni kereszt-illesztést kell választania.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson

- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: nem támogatott kódolás, sérült bájtok vagy JSON-forrás használata több beágyazott sorban lévő egyetlen dokumentumként
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a "JSON-beállítások" elemet, és kapcsolja be az "egyetlen dokumentum" beállítást.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout

- **Üzenet**: szórásos csatlakozás időtúllépési hibája, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül, hibakeresési futtatások és 300 a feladat futtatásakor
- **Okok**: a szórás a hibakeresési futtatásokban és a 300 másodpercben, a feladatok futtatásakor alapértelmezett időkorláttal rendelkezik 60 mp. Úgy tűnik, hogy a szórásra kiválasztott stream túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget lehessen létrehozni
- **Javaslat**: a csatlakozáshoz, a létezéshez és a kereséshez tekintse meg az optimalizálás lapot az adatfolyam-transzformációk lapon. A szórás alapértelmezett beállítása az "Auto". Ha az "automatikus" beállítás be van állítva, vagy ha manuálisan állítja be a bal vagy a jobb oldali szórást a "rögzített" érték alatt, akkor beállíthat nagyobb Azure Integration Runtime konfigurációt, vagy kikapcsolhatja a szórást. Az Adatáramlások legjobb teljesítményének ajánlott megközelítése, hogy a Spark az "Auto" használatával közvetítse az "automatikus", és használja a memóriára optimalizált Azure IR. Ha hibakeresési folyamat futása során hajtja végre az adatáramlást hibakeresési teszt végrehajtása során, akkor gyakrabban futtathatja ezt az állapotot. Ennek az az oka, hogy az ADF leszabályozza a szórási időtúllépést 60 mp-re a gyorsabb hibakeresési élmény fenntartása érdekében. Ha azt szeretné, hogy a rendszer kiterjessze az aktivált Futtatás 300 másodperces időtúllépését, használja a hibakeresési > a tevékenység-futtatókörnyezet használata lehetőséget, hogy kihasználja az adatfolyam-feldolgozási folyamat végrehajtása tevékenységben definiált Azure IR.

- **Üzenet**: szórásos csatlakozás időtúllépési hibája esetén a probléma elkerülése érdekében a csatlakozás/létező/keresési átalakítás lehetőségnél kiválaszthatja a szórásos küldés lehetőséget. Ha a teljesítmény javítása érdekében szeretne csatlakozni a csatlakozáshoz, akkor győződjön meg arról, hogy a szórásos stream 60 másodpercen belül képes adatok létrehozására a hibakeresési és a 300-es mp-ben a feladatok futtatásakor.
- **OK**: a szórás a hibakeresési és a 300-es mp-es alapértelmezett időkorlátot (60 mp) tartalmaz a feladatok futtatásához. A szórásos csatlakozás esetében a szórásra kiválasztott adatfolyam túl nagynak tűnik, hogy a korláton belüli adatmennyiséget hozzon létre. Ha nem használ szórásos csatlakozást, a adatfolyam által végzett alapértelmezett szórás elérheti ugyanezt a korlátot
- **Javaslat**: kapcsolja ki a szórási beállítást, vagy Kerülje a nagyméretű adatfolyamok szórását, ahol a feldolgozás több mint 60 másodpercet vehet igénybe. Válasszon kisebb streamet a szóráshoz. A nagyméretű SQL/DW táblák és forrásfájlok általában rossz jelöltek. Szórásos csatlakozás hiányában használjon nagyobb fürtöt, ha a hiba történik.

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

### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **Okok**: nem kompatibilis adattípusok a deklarált típus és a tényleges paraméter értéke között
- **Javaslat**: Ellenőrizze, hogy az adatfolyamatba átadott paraméterérték megfelel-e a deklarált típusnak.

### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen
- **Okok**: érvénytelen vagy nem érhető el a kifejezésekben használt oszlopnév
- **Javaslat**: Tekintse át a kifejezésekben használt oszlopnevek (ka) t.

### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető
- **Okok**: a kifejezés formázása miatt hibákat elemez
- **Javaslat**: Ellenőrizze a kifejezés formázását.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian
- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztéshez implicit Descartes termék nem támogatott. Ha az illesztésben használt oszlopok létrehozzák az egyedi kulcsot
- **Javaslat**: a nem egyenlőségen alapuló összekapcsolások esetén a csatlakozást kell választania.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson
- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: nem támogatott kódolás, sérült bájtok vagy JSON-forrás használata több beágyazott sorban lévő egyetlen dokumentumként
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a "JSON-beállítások" elemet, és kapcsolja be az "egyetlen dokumentum" beállítást.



### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió
- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **Okok**: az adatértékek nem a várt formátumban vannak
- **Javaslat**: használja a megfelelő adattípust.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **Okok**: legfeljebb 100 000 nem véglegesített blokk lehet egy blobban.
- **Javaslat**: további részletekért vegye fel a kapcsolatot a Microsoft termék csapatával.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például *<Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30*) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (pl. *<Source Path> /<Partition gyökérkönyvtár 1>/A = 10/b = 20, <Source Path> /mappa 2/file1*), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útja több particionált címtárral vagy particionált könyvtárral rendelkezik más fájlokkal vagy nem particionált címtárral.
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.

### <a name="error-code-getcommand-outputasync-failed"></a>Hibakód: a GetCommand OutputAsync sikertelen
- **Üzenet**: az adatfolyam hibakeresése és az adatelőnézet: a GetCommand OutputAsync sikertelen volt...
- **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
- **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Hibakód: DF-végrehajtó-működése OutOfMemoryError
 
- **Üzenet**: a fürt kifogyott a memóriából a végrehajtás során, próbálkozzon újra egy integrációs modul használatával, amely nagyobb alapszámmal és/vagy memória-optimalizált számítási típussal rendelkezik.
- **Okok**: a fürt elfogyott a memóriából.
- **Javaslat**: a hibakeresési fürtök fejlesztési célokra szolgálnak. Használja ki az adatok mintavételezésének megfelelő számítási típust és méretet az adattartalom futtatásához. A legjobb teljesítmény érdekében tekintse meg a [adatfolyam Performance útmutatót](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) a adatfolyamok hangolásához.

### <a name="error-code-df-executor-illegalargument"></a>Hibakód: DF-végrehajtó-illegalArgument
- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa.
- **OK**: a fiók neve vagy a hozzáférési kulcs helytelen.
- **Javaslat**: adja meg a megfelelő fiók nevét vagy elérési kulcsát.

- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa
- **Okok**: helytelen a fiók neve vagy a hozzáférési kulcs
- **Javaslat**: Ellenőrizze, hogy helyes-e a társított szolgáltatásban megadott fiók neve vagy elérési kulcsa. 

### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **Okok**: nem kompatibilis adattípusok a deklarált típus és a tényleges paraméter értéke között
- **Javaslat**: adja meg a megfelelő adattípusokat.

### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen.
- **OK**: érvénytelen vagy nem elérhető oszlopnév van használatban a kifejezésekben.
- **Javaslat**: Tekintse át a kifejezésekben használt oszlopnevek (ka) t.


### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető.
- **OK**: a kifejezés formázása miatt hibákat elemez.
- **Javaslat**: Ellenőrizze a kifejezés formázását.


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

- **Üzenet**: az Excel-lapok neve és indexe nem létezhet egyszerre.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adja meg a táblázat nevét vagy indexét az Excel-adatok olvasásához.

- **Üzenet**: érvénytelen tartomány van megadva.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adjon meg érvényes tartományt a hivatkozás: [Excel-tulajdonságok](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties)alapján.

- **Üzenet**: érvénytelen Excel-fájl van megadva, amíg csak az. xlsx és az. xls támogatott
- **Okok**: meghatározatlan
- **Javaslat**: Győződjön meg róla, hogy az Excel fájlkiterjesztés vagy. xlsx vagy. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Hibakód: DF-Excel-InvalidData
- **Üzenet**: az Excel-munkalap nem létezik.
- **Okok**: meghatározatlan
- **Javaslat**: Ellenőrizze a paraméter értékét, és adjon meg érvényes nevet vagy indexet az Excel-adatok olvasásához.

- **Üzenet**: az Excel-fájlok más sémával való olvasása most nem támogatott.
- **Okok**: meghatározatlan
- **Javaslat**: a megfelelő Excel-fájl használata.

- **Üzenet**: az adattípus nem támogatott.
- **Okok**: meghatározatlan
- **Javaslat**: használja az Excel-fájl megfelelő adattípusait.

### <a name="error-code-4502"></a>Hibakód: 4502
- **Üzenet**: jelentős párhuzamos MappingDataflow-végrehajtások vannak, amelyek meghibásodást okoznak a Integration Runtime alatti szabályozás miatt.
- **Okok**: sok adatfolyam tevékenység fut egyszerre a Integration Runtime. További információ a [Azure Data Factory korlátozásokról](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Javaslat**: abban az esetben, ha párhuzamosan szeretné futtatni az adatfolyam-tevékenységeket, ossza meg ezeket több integrációs futtatókörnyezettel.


### <a name="error-code-invalidtemplate"></a>Hibakód: InvalidTemplate
- **Üzenet**: a folyamat kifejezése nem értékelhető ki.
- **OK**: a adatfolyam tevékenységben átadott folyamat kifejezése nem megfelelően lett feldolgozva a szintaktikai hiba miatt.
- **Javaslat**: Ellenőrizze a tevékenység figyelését a kifejezés ellenőrzéséhez.

### <a name="error-code-2011"></a>Hibakód: 2011
- **Üzenet**: a tevékenység Azure Integration Runtime fut, és nem tudta visszafejteni a saját üzemeltetésű Integration Runtime keresztül csatlakoztatott adattár vagy számítás hitelesítő adatait. Ellenőrizze az ehhez a tevékenységhez társított társított szolgáltatások konfigurációját, és győződjön meg arról, hogy az integrációs modul megfelelő típusát használja.
- **Okok**: az adatfolyam nem támogatja a társított szolgáltatásokat saját üzemeltetésű integrációs futtatókörnyezettel.
- **Javaslat**: állítsa be az adatfolyamatot úgy, hogy az integrációs modulon fusson a felügyelt Virtual Network használatával.

## <a name="miscellaneous-troubleshooting-tips"></a>Egyéb hibaelhárítási tippek
- **Probléma**: váratlan kivétel történt, és a végrehajtás nem sikerült
    - **Üzenet**: az adatfolyam tevékenységének végrehajtása során: váratlan kivétel történt, és a végrehajtás nem sikerült.
    - **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
    - **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

-  **Probléma**: az adatelőnézet hibakeresése nem rendelkezik kimeneti adatokat a csatlakozáshoz
    - **Üzenet**: nagy számú Null érték vagy hiányzó érték áll rendelkezésre, amelyek oka lehet a túl kevés sor mintavételének. Próbálja meg frissíteni a hibakeresési sor korlátját, és frissítse az adatforrást.
    - **Okok**: az illesztési feltétel nem felelt meg egyetlen sornak sem, vagy nagy MENNYISÉGű nullát eredményezett az adatelőnézet során.
    - **Javaslat**: lépjen a hibakeresési beállítások menüpontra, és növelje a sorok számát a Sorforrás korlátján belül. Ellenőrizze, hogy van-e olyan Azure IR, amely elég nagy adatáramlási fürttel rendelkezik a további adatkezelés érdekében.
    
- **Probléma**: érvényesítési hiba a (z) forrásnál TÖBBsoros CSV-fájlokkal 
    - **Üzenet**: az alábbi hibaüzenetek egyike jelenhet meg:
        - Az utolsó oszlop null értékű vagy hiányzik.
        - A séma érvényesítése a forrásnál meghiúsul.
        - A séma importálása nem sikerül helyesen megjeleníteni a UX-ben, és az utolsó oszlopban egy új sor karakter szerepel a névben.
    - **Okok**: a leképezési adatfolyamban jelenleg a TÖBBsoros CSV-forrás nem működik együtt a \r\n. Előfordulhat, hogy a szállításban az extra sorok a break forrás értékeit adja vissza. 
    - **Javaslat**: a (z)%-os fájlt a forrásban, a \r\n. helyett a \n sort kell előállítani. A másolási tevékenység használatával a CSV-fájlt \r\n és \n értékre is konvertálhatja.

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató
1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban keresse fel a társított szolgáltatást minden Ön által használt adatkészlet esetében, és tesztelje a kapcsolatokat.
2. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Kapcsolja be a hibakeresést, és kattintson az adatelőnézetre a forrás-átalakításokban, és győződjön meg arról, hogy képes hozzáférni az adataihoz.
3. Ha minden jól látható az adatok előnézetében, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
