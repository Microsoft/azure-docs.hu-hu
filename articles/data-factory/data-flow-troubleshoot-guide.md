---
title: Az adatfolyamatok leképezésének hibakeresése
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory adatáramlási problémáit.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 3c73d1652d4fdaa34fa65e7186298bafc0a905a9
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453240"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory adatforgalmának hibáinak megoldása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az adatfolyamatok Azure Data Factoryban történő leképezésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-error-codes-and-messages"></a>Gyakori hibakódok és üzenetek 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **OK**: az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban.
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik-e, és hogy elérhető-e.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian

- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztések implicit Descartes-as termékei nem támogatottak. Ha az illesztésben oszlopokat használ, hozzon létre egy egyedi kulcsot legalább egy oszloppal a kapcsolat mindkét oldaláról.
- **Javaslat**: a nem egyenlőségen alapuló illesztésekhez használjon egyéni kereszt-illesztést.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson

- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: a nem támogatott kódolás, a sérült bájtok vagy a JSON-forrás egyetlen dokumentumként való használata sok beágyazott sorban.
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a **JSON-beállítások** elemet, és kapcsolja be az **egy dokumentumot**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout

- **Üzenet**: szórásos csatlakozás időtúllépési hibája, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül, hibakeresési futtatások és 300 a feladat futtatásakor
- **OK**: a szórás a hibakeresési és a 300 másodperces 60 másodperces időkorlátot futtatja a feladatok futtatásakor. A szórásra kiválasztott adatfolyam túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget hozzon létre.
- **Javaslat**: a csatlakozáshoz, a létezéshez és a kereséshez tekintse meg az **optimalizálás** lapot az adatfolyam-transzformációk lapon. A szórás alapértelmezett beállítása **automatikus**. Ha az **automatikus** beállítás be van állítva, vagy ha manuálisan állítja be a bal vagy a jobb oldali szórást a **rögzített** értékre, akkor beállíthat egy nagyobb Azure Integration Runtime (IR) konfigurációt vagy kikapcsolhatja a szórást. A legjobb teljesítmény az adatforgalomban azt javasoljuk, hogy engedélyezze a Spark számára az **automatikus** küldést, és használjon memóriát optimalizált Azure IR. 
 
  Ha hibakeresési folyamat futása során futtatja az adatfolyamatot hibakeresési teszt végrehajtás közben, akkor gyakrabban is futtathatja ezt az állapotot. Ennek az az oka, hogy a Azure Data Factory a szórási időtúllépést 60 másodpercre szabályozza a gyorsabb hibakeresési élmény fenntartása érdekében. Az időtúllépést kiterjesztheti egy aktivált Futtatás 300 másodperces időtúllépésére. Ehhez használhatja a **hibakeresési**  >  **tevékenység futtatókörnyezete** lehetőséget a végrehajtás adatáramlási folyamat tevékenységében definiált Azure IR használatára.

- **Üzenet**: szórásos csatlakozás időtúllépési hibája esetén a probléma elkerülése érdekében a csatlakozás/létező/keresési átalakítás lehetőségnél kiválaszthatja a szórásos küldés lehetőséget. Ha a teljesítmény javítása érdekében szeretne csatlakozni a csatlakozáshoz, akkor győződjön meg arról, hogy a szórásos stream 60 másodpercen belül képes adatok létrehozására a hibakeresési és a 300-es mp-ben a feladatok futtatásakor.
- **OK**: a szórás a hibakeresési futtatásokban és a 300 másodpercben, a feladatok futtatásakor 60 másodperces időkorlátot tartalmaz. A szórásos csatlakozás esetében a szórásra kiválasztott stream túl nagy ahhoz, hogy az adott korláton belül adatmennyiséget hozzon létre. Ha nem használ szórásos csatlakozást, a adatfolyam alapértelmezett szórása ugyanezt a korlátot érheti el.
- **Javaslat**: kapcsolja ki a szórási beállítást, vagy Kerülje a nagyméretű adatfolyamok szórását, amelyek esetében a feldolgozás több mint 60 másodpercet vehet igénybe. Válasszon ki egy kisebb streamet a szóráshoz. A nagyméretű Azure SQL Data Warehouse táblák és forrásfájlok általában nem jó választás. Szórásos csatlakozás hiányában használjon nagyobb fürtöt, ha ez a hiba történik.

### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió

- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **OK**: az adatértékek nem a várt formátumban vannak.
- **Javaslat**: használja a megfelelő adattípust.

### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-végrehajtó-InvalidColumn
- **Üzenet**: az oszlop nevét meg kell adni a lekérdezésben, egy aliast kell beállítania, ha SQL-függvényt használ
- **OK**: nincs megadva oszlopnév.
- **Javaslat**: állítsa be az aliast, ha olyan SQL-függvényt használ, mint a min () vagy a Max ().

### <a name="error-code-df-executor-drivererror"></a>Hibakód: DF-végrehajtó-DriverError
- **Üzenet**: a INT96 olyan örökölt időbélyeg-típus, amelyet az ADF adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusokra.
- **OK**: illesztőprogram-hiba.
- **Javaslat**: a INT96 olyan örökölt időbélyeg-típus, amelyet Azure Data Factory adatfolyam nem támogat. Érdemes lehet frissíteni az oszlop típusát a legújabb típusra.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **OK**: a blobban a nem véglegesített blokkok maximális száma 100 000.
- **Javaslat**: a problémával kapcsolatos további információkért forduljon a Microsoft termék csapatához.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például <Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (például <Source Path> /<Partition Root Directory 1>/a = 10/b = 20, <Source Path> /mappa 2/file1), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útjának több particionált könyvtára vagy egy másik fájlt vagy nem particionált könyvtárat tartalmazó particionált könyvtár is van.
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.

### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **OK**: a deklarált típus adattípusa nem kompatibilis a tényleges paraméter értékével.
- **Javaslat**: Győződjön meg arról, hogy az adatfolyamnak átadott paraméterérték megfelel a deklarált típusnak.

### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen
- **OK**: érvénytelen vagy nem elérhető oszlopnév a kifejezésben.
- **Javaslat**: oszlopnevek keresése a kifejezésekben.

### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető
- **OK**: a rendszer helytelen formázás miatt generált egy kifejezést a hibák elemzéséhez.
- **Javaslat**: vizsgálja meg a kifejezés formázását.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian
- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztések implicit Descartes-as termékei nem támogatottak. Ha az illesztésben oszlopok vannak használatban, hozzon létre egy egyedi kulcsot.
- **Javaslat**: a nem egyenlőségen alapuló illesztésekhez használjon kereszt-összekapcsolást.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson
- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: a nem támogatott kódolás, a sérült bájtok vagy a JSON-forrás egyetlen dokumentumként való használata sok beágyazott sorban.
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a **JSON-beállítások** elemet, és kapcsolja be az **egy dokumentumot**.



### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió
- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **OK**: az adatértékek nem a várt formátumban vannak.
- **Javaslat**: használja a megfelelő adattípust.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hibakód: DF-végrehajtó-BlockCountExceedsLimitError
- **Üzenet**: a nem véglegesített blokkok száma nem haladhatja meg a 100 000 blokk maximális korlátját. Keresse meg a blob konfigurációját.
- **OK**: a blobban a nem véglegesített blokkok maximális száma 100 000.
- **Javaslat**: a problémával kapcsolatos további információkért forduljon a Microsoft termék csapatához.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hibakód: DF-végrehajtó-PartitionDirectoryError
- **Üzenet**: a megadott forrás elérési út több particionált címtárral rendelkezik (például *<Source Path> /<partíció gyökérkönyvtára 1>/a = 10/b = 20, <Source Path> /<partíció gyökérkönyvtára 2>/c = 10/d = 30*) vagy particionált könyvtár más fájlokkal vagy nem particionált címtárral (pl. *<Source Path> /<Partition gyökérkönyvtár 1>/A = 10/b = 20, <Source Path> /mappa 2/file1*), távolítsa el a partíció gyökérkönyvtárát a forrás elérési útról, és olvassa el külön forrás-átalakítással.
- **OK**: a forrás elérési útjának több particionált könyvtára vagy egy másik fájlt vagy nem particionált könyvtárat tartalmazó particionált könyvtár is van. 
- **Javaslat**: távolítsa el a particionált gyökérkönyvtárat a forrás elérési útról, és olvassa el külön forrás-átalakítással.

### <a name="error-code-getcommand-outputasync-failed"></a>Hibakód: a GetCommand OutputAsync sikertelen
- **Üzenet**: az adatfolyam hibakeresése és az adatelőnézet: a GetCommand OutputAsync sikertelen volt...
- **OK**: Ez a hiba egy háttér-szolgáltatási hiba. 
- **Javaslat**: Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet. Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Hibakód: DF-végrehajtó-működése OutOfMemoryError
 
- **Üzenet**: a fürt kifogyott a memóriából a végrehajtás során, próbálkozzon újra egy integrációs modul használatával, amely nagyobb alapszámmal és/vagy memória-optimalizált számítási típussal rendelkezik.
- **OK**: a fürt elfogyott a memóriából.
- **Javaslat**: a hibakeresési fürtök fejlesztést jelentenek. Használja az adatok mintavételezését és a megfelelő számítási típust és méretet az adattartalom futtatásához. A teljesítménnyel kapcsolatos tippekért lásd: [az adatfolyamok teljesítményére vonatkozó útmutató leképezése](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Hibakód: DF-végrehajtó-illegalArgument

- **Üzenet**: Ellenőrizze, hogy helyes-e a társított szolgáltatás elérési kulcsa
- **OK**: a fiók neve vagy a hozzáférési kulcs helytelen.
- **Javaslat**: Ellenőrizze, hogy helyes-e a társított szolgáltatásban megadott fiók neve vagy elérési kulcsa. 

### <a name="error-code-df-executor-invalidtype"></a>Hibakód: DF-végrehajtó-InvalidType
- **Üzenet**: Ellenőrizze, hogy a paraméter típusa egyezik-e az átadott érték típusával. Az úszó paraméterek átadása a folyamatokból jelenleg nem támogatott.
- **OK**: a deklarált típus adattípusa nem kompatibilis a tényleges paraméter értékével. 
- **Javaslat**: adja meg a megfelelő adattípusokat.

### <a name="error-code-df-executor-columnunavailable"></a>Hibakód: DF-végrehajtó-ColumnUnavailable
- **Üzenet**: a kifejezésben használt oszlopnév nem érhető el vagy érvénytelen.
- **OK**: érvénytelen vagy nem elérhető oszlopnév van használatban egy kifejezésben.
- **Javaslat**: vizsgálja meg a kifejezésekben használt oszlopnevek nevét.


### <a name="error-code-df-executor-parseerror"></a>Hibakód: DF-végrehajtó-értelmezési hiba
- **Üzenet**: a kifejezés nem elemezhető.
- **OK**: a rendszer helytelen formázás miatt generált egy kifejezést a hibák elemzéséhez. 
- **Javaslat**: vizsgálja meg a kifejezés formázását.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Hibakód: DF-végrehajtó-OutOfDiskSpaceError
- **Üzenet**: belső kiszolgálóhiba
- **OK**: a fürtön elfogyott a szabad lemezterület.
- **Javaslat**: próbálkozzon újra a folyamattal. Ha ez nem oldja meg a problémát, forduljon az ügyfélszolgálathoz.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Hibakód: DF-végrehajtó-StoreIsNotDefined
- **Üzenet**: az áruház konfigurációja nincs definiálva. Ezt a hibát a folyamat Érvénytelen paraméter-hozzárendelése okozta.
- **OK**: nem meghatározott.
- **Javaslat**: a paraméter érték-hozzárendelésének ellenőrzését a folyamatban. Egy paraméter kifejezése érvénytelen karaktereket tartalmazhat.

### <a name="error-code-df-excel-invalidconfiguration"></a>Hibakód: DF-Excel-InvalidConfiguration
- **Üzenet**: az Excel-lapok neve vagy indexe kötelező.
- **OK**: nem meghatározott.
- **Javaslat**: keresse meg a paraméter értékét. Adja meg a munkalap nevét vagy indexét az Excel-információk olvasásához.

- **Üzenet**: az Excel-lapok neve és indexe nem létezhet egyszerre.
- **OK**: nem meghatározott.
- **Javaslat**: keresse meg a paraméter értékét. Adja meg a munkalap nevét vagy indexét az Excel-információk olvasásához.

- **Üzenet**: érvénytelen tartomány van megadva.
- **OK**: nem meghatározott.
- **Javaslat**: keresse meg a paraméter értékét. Érvényes tartományt válasszon a hivatkozás alapján. További információ: Excel- [Tulajdonságok](./format-excel.md#dataset-properties).

- **Üzenet**: érvénytelen Excel-fájl van megadva, amíg csak az. xlsx és az. xls támogatott
- **OK**: nem meghatározott.
- **Javaslat**: Győződjön meg arról, hogy az Excel-fájlkiterjesztés vagy. xlsx vagy. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Hibakód: DF-Excel-InvalidData
- **Üzenet**: az Excel-munkalap nem létezik.
- **OK**: nem meghatározott.
- **Javaslat**: keresse meg a paraméter értékét. Adjon meg egy érvényes munkalap-nevet vagy-indexet az Excel-információk olvasásához.

- **Üzenet**: az Excel-fájlok más sémával való olvasása most nem támogatott.
- **OK**: nem meghatározott.
- **Javaslat**: támogatott Excel-fájl használata.

- **Üzenet**: az adattípus nem támogatott.
- **OK**: nem meghatározott.
- **Javaslat**: használja az Excel-fájlok támogatott adattípusait.

### <a name="error-code-4502"></a>Hibakód: 4502
- **Üzenet**: jelentős egyidejű MappingDataflow-végrehajtás történt, amely a Integration Runtime alatti szabályozás miatt hibákat okoz.
- **OK**: nagy mennyiségű adatfolyam-tevékenység fut egyidejűleg az integrációs modulban. További információ: [Azure Data Factory korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Javaslat**: Ha több adatfolyam-tevékenységet szeretne párhuzamosan futtatni, Ossza szét őket több integrációs modul között.


### <a name="error-code-invalidtemplate"></a>Hibakód: InvalidTemplate
- **Üzenet**: a folyamat kifejezése nem értékelhető ki.
- **OK**: az adatfolyam-tevékenységben átadott egyik folyamat kifejezése nem megfelelően van feldolgozva egy szintaktikai hiba miatt.
- **Javaslat**: Ellenőrizze a tevékenység figyelését a kifejezés ellenőrzéséhez.

### <a name="error-code-2011"></a>Hibakód: 2011
- **Üzenet**: a tevékenység Azure Integration Runtime fut, és nem tudta visszafejteni a saját üzemeltetésű Integration Runtime keresztül csatlakoztatott adattár vagy számítás hitelesítő adatait. Ellenőrizze az ehhez a tevékenységhez társított társított szolgáltatások konfigurációját, és győződjön meg arról, hogy az integrációs modul megfelelő típusát használja.
- **OK**: az adatfolyam nem támogatja a társított szolgáltatásokat a saját üzemeltetésű integrációs modulokon.
- **Javaslat**: állítsa be az adatfolyamot felügyelt Virtual Network integrációs futtatókörnyezeten való futtatásra.

## <a name="miscellaneous-troubleshooting-tips"></a>Egyéb hibaelhárítási tippek
- **Probléma**: váratlan kivétel történt, és a végrehajtás nem sikerült.
    - **Üzenet**: az adatfolyam tevékenységének végrehajtása során: váratlan kivétel történt, és a végrehajtás nem sikerült.
    - **OK**: Ez a hiba egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
    - **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

-  **Probléma**: a hibakeresési adatelőnézet során nem érhető el kimeneti érték a csatlakoztatáshoz.
    - **Üzenet**: nagy számú Null érték vagy hiányzó érték áll rendelkezésre, amelyek oka lehet a túl kevés sor mintavételének. Próbálja meg frissíteni a hibakeresési sor korlátját, és frissítse az adatforrást.
    - **OK**: az illesztési feltétel nem felelt meg egyetlen sornak sem, vagy nagy mennyiségű null értéket eredményezett az adatelőnézet során.
    - **Javaslat**: a **hibakeresési beállítások** területen növelje a sorok számát a Sorforrás korlátján belül. Ügyeljen arra, hogy olyan Azure IR válasszon ki, amely egy olyan adatáramlási fürttel rendelkezik, amely elég nagy a további adatkezeléshez.
    
- **Probléma**: érvényesítési hiba a (z) forrásnál TÖBBsoros CSV-fájlokkal. 
    - **Üzenet**: a következő hibaüzenetek egyike jelenhet meg:
        - Az utolsó oszlop null értékű vagy hiányzik.
        - A séma érvényesítése a forrásnál meghiúsul.
        - A séma importálása nem sikerül helyesen megjeleníteni a UX-ben, és az utolsó oszlopban egy új sor karakter szerepel a névben.
    - **OK**: a leképezési adatfolyamban a többsoros CSV-forrásfájlok jelenleg nem működnek, ha \r\n használ. Előfordulhat, hogy a kocsivissza extra vonalak hibát okozhatnak. 
    - **Javaslat**: a fájl a forráshoz való előállítása a \n értékkel a \r\n. helyett. Vagy a másolási tevékenység használatával alakítsa át a CSV-fájlt, hogy a \n-as sort elválasztóként használja.

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató
1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban nyissa meg a társított szolgáltatást minden Ön által használt adatkészlethez, majd tesztelje a kapcsolatokat.
2. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Hibakeresési módban válassza az **adatelőnézet** lehetőséget a forrás átalakításokban, hogy hozzáférjen az adataihoz.
3. Ha úgy tűnik, hogy minden megfelelően megjelenik az adatelőnézetben, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért tekintse meg a következő forrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
