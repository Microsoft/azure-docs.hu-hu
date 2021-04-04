---
title: Adatok mozgatása kiterjesztett felhőalapú adatbázisok között
description: Elmagyarázza, hogyan kezelhetők a szegmensek, és hogyan helyezhetők át az adatai egy saját üzemeltetésű szolgáltatással a rugalmas adatbázis-API-k használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793466"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Adatok mozgatása kiterjesztett felhőalapú adatbázisok között
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ha Ön egy szolgáltatás-fejlesztőként szolgáló szoftver, és az alkalmazás hirtelen igénybe veszi a növekedést, alkalmazkodnia kell a növekedéshez. Így további adatbázisokat (szegmenseket) adhat hozzá. Hogyan terjeszthetők újra az adatok az új adatbázisokra az adatok integritásának megszakadása nélkül? A **felosztott egyesítés eszköz** használatával a korlátozott adatbázisokból származó adatok az új adatbázisokra helyezhetők át.  

A felosztási egyesítési eszköz Azure-webszolgáltatásként fut. A rendszergazda vagy a fejlesztő az eszköz használatával helyezi át a shardletek (a szegmensből származó adatok) a különböző adatbázisok (szegmensek) között. Az eszköz a szegmensek közötti leképezések felügyeletét használja a szolgáltatás metaadatainak adatbázisának karbantartásához, és biztosítja a konzisztens leképezéseket.

![Áttekintés][1]

## <a name="download"></a>Letöltés

[Microsoft. Azure. SqlDatabase. ElasticScale. Service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentáció

1. [Rugalmas adatbázis felosztása – egyesítési eszköz oktatóanyaga](elastic-scale-configure-deploy-split-and-merge.md)
2. [Felosztás – biztonsági konfiguráció egyesítése](elastic-scale-split-merge-security-configuration.md)
3. [Felosztás – biztonsági szempontok egyesítése](elastic-scale-split-merge-security-configuration.md)
4. [A szilánkleképezés kezelése](elastic-scale-shard-map-management.md)
5. [Meglévő adatbázisok migrálása horizontális felskálázáshoz](elastic-convert-to-use-elastic-tools.md)
6. [Rugalmas adatbázis-eszközök](elastic-scale-introduction.md)
7. [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Miért érdemes a Split-Merge eszközt használni

- **Rugalmasságot**

  Az alkalmazásoknak rugalmasan kell kiterjeszteniük a Azure SQL Database egyetlen adatbázisának korlátain kívül. Az eszköz használatával az adatok szükség szerint helyezhetők át az új adatbázisokra az integritás megőrzése mellett.

- **Felosztás növekvőre**

  A teljes kapacitás növeléséhez a robbanásszerű növekedés kezelése érdekében hozzon létre további kapacitást az adatelosztással és a Növekményesen több adatbázis között, amíg a kapacitási igények nem teljesülnek. Ez az **osztott** funkció elsődleges példája.

- **Egyesítés a zsugorodáshoz**

  A kapacitást a vállalat szezonális jellege miatt kell csökkenteni. Az eszköz lehetővé teszi, hogy kevesebb méretezési egységre Szűkítse az üzleti lassulást. A rugalmasan méretezhető Split-Merge szolgáltatás "Merge" funkciója magában foglalja ezt a követelményt.

- **Pontok kezelése a shardletek áthelyezésével**

  Ha az adatbázis több Bérlővel rendelkezik, a shardletek szegmensekre való kiosztása a kapacitás szűk keresztmetszetét eredményezheti bizonyos szegmensekben. Ehhez újra le kell foglalni a shardletek, vagy a foglalt shardletek új vagy kevésbé használt szegmensekre kell áthelyeznie.

## <a name="concepts--key-features"></a>Alapfogalmak & főbb funkciók

- **Ügyfél által üzemeltetett szolgáltatások**

  A felosztott egyesítés ügyfél által üzemeltetett szolgáltatásként történik. A szolgáltatást a Microsoft Azure-előfizetésében kell telepítenie és üzemeltetni. A NuGet-ből letöltött csomag egy olyan konfigurációs sablont tartalmaz, amely az adott üzemelő példányra vonatkozó információkkal fejeződött be. A részletekért tekintse meg a [Split-Merge oktatóanyagot](elastic-scale-configure-deploy-split-and-merge.md) . Mivel a szolgáltatás az Azure-előfizetésben fut, a szolgáltatás legtöbb biztonsági aspektusát szabályozhatja és konfigurálhatja. Az alapértelmezett sablon a TLS konfigurálására, a tanúsítványalapú ügyfél-hitelesítésre, a tárolt hitelesítő adatok titkosítására, a DoS-védelemre és az IP-korlátozásokra vonatkozó beállításokat tartalmazza. A biztonsági szempontokról további információt a következő dokumentum [felosztás – egyesítés biztonsági konfigurációjában](elastic-scale-split-merge-security-configuration.md)talál.

  Az alapértelmezett telepített szolgáltatás egy feldolgozóval és egy webes szerepkörrel fut. Mindegyik az A1-es VM-méretet használja az Azure Cloud Servicesban. Noha ezeket a beállításokat nem lehet módosítani a csomag telepítésekor, a futó felhőalapú szolgáltatásban (a Azure Portalon keresztül) sikeres üzembe helyezés után megváltoztathatja őket. Vegye figyelembe, hogy a feldolgozói szerepkört a technikai okokból nem lehet egynél több példányra konfigurálni.

- **Szegmens Térkép integrációja**

  A felosztási-egyesítési szolgáltatás az alkalmazás szegmenses térképével kommunikál. Ha a felosztási és egyesítési szolgáltatást a tartományok felosztása vagy egyesítése céljából, vagy a szegmensek közötti shardletek helyezi át, a szolgáltatás automatikusan naprakészen tartja a szegmenses leképezést. Ehhez a szolgáltatás csatlakozik az alkalmazás szegmens Map Manager-adatbázisához, és megtartja a tartományokat és a leképezéseket a felosztott/egyesítés/áthelyezési kérelmek folyamata során. Ez biztosítja, hogy a szegmens Térkép mindig naprakész nézetet jelenít meg, amikor a felosztott egyesítési műveletek folyamatban vannak. A felosztott, egyesíthető és shardletbe mozgatási műveletek a shardletek a forrás szegmensből a cél szegmensbe való áthelyezésével valósíthatók meg. A shardletbe-áthelyezési művelet során az aktuális köteg alá tartozó shardletek offline állapotú vannak megjelölve a szegmenses térképen, és nem érhetők el az adatkezelési útválasztási kapcsolatok számára a **OpenConnectionForKey** API használatával.

- **Konzisztens shardletbe-kapcsolatok**

  Ha az adatáthelyezés új köteg shardletek indul el, a rendszer a shardletbe tároló szegmensek által biztosított adatátviteli útvonalakat is letiltotta, és a szegmensek közötti Térkép API-k által a shardletek-hoz kapcsolódó további kapcsolatok le vannak tiltva, amíg az adatáthelyezés folyamatban van a következetlenségek elkerülése érdekében. Az ugyanazon a szegmensen lévő más shardletek létesített kapcsolatok is megszakadnak, de az újrapróbálkozáskor azonnal sikeresek lesznek. A köteg áthelyezése után a shardletek ismét online állapotba kerülnek, és a forrásadatok el lesznek távolítva a forrás szegmensből. A szolgáltatás minden köteghez végrehajtja ezeket a lépéseket, amíg az összes shardletek át nem helyezi. Ez több kapcsolódási műveletet is eredményez a teljes felosztási/egyesítési/áthelyezési művelet során.  

- **A shardletbe rendelkezésre állásának kezelése**

  Ha a fentiekben leírtak szerint korlátozni szeretné a shardletek aktuális kötegét, a fentiekben ismertetett módon korlátozza a nem rendelkezésre állási hatókört egyszerre egy köteg shardletek. Ez előnyben részesített egy olyan megközelítésnél, amelyben a teljes szegmens kapcsolat nélküli állapotban marad az összes shardletek egy megosztott vagy egyesítési művelet során. Egy olyan köteg mérete, amely egy időben áthelyezendő különböző shardletek száma, egy konfigurációs paraméter. Az alkalmazás rendelkezésre állási és teljesítménybeli igényeitől függően minden felosztási és egyesítési művelethez definiálható. Vegye figyelembe, hogy a szegmenses térképen zárolt tartomány mérete nagyobb, mint a megadott batch-méret. Ennek az az oka, hogy a szolgáltatás a tartomány méretét úgy keresi meg, hogy az adatokban szereplő, hozzávetőleges kulcs értékeinek tényleges száma nagyjából megfelel a köteg méretének. Fontos megjegyezni, hogy különösen a ritkán használt horizontális Felskálázási kulcsok esetében.

- **Metaadat-tároló**

  A felosztási-egyesítési szolgáltatás adatbázis használatával tartja karban az állapotát, és a naplók a kérelmek feldolgozásakor maradnak. A felhasználó létrehozza ezt az adatbázist az előfizetésében, és megadja a kapcsolódási karakterláncot a szolgáltatás központi telepítésének konfigurációs fájljában. A felhasználó szervezetének rendszergazdái is csatlakozhatnak ehhez az adatbázishoz a kérelmek előrehaladásának áttekintéséhez és a lehetséges hibákkal kapcsolatos részletes információk vizsgálatához.

- **Horizontális felskálázás – figyelemfelkeltés**

  A felosztott egyesítési szolgáltatás különbséget tesz a (1) tagolt táblázatok, (2) és a (3) normál táblák között. A felosztási/egyesítési/áthelyezési művelet szemantikai köre a használt táblázat típusától függ, és a következők szerint van meghatározva:

  - **Szilánkokra osztott táblák**

    A felosztási, egyesítési és áthelyezési műveletek shardletek helyezik át a forrásról a cél szegmensbe. A teljes kérelem sikeres befejezése után ezek a shardletek már nem jelennek meg a forráson. Vegye figyelembe, hogy a cél-tábláknak léteznie kell a cél szegmensen, és a művelet feldolgozása előtt nem tartalmazhatnak a céltartományban lévő adatmennyiséget.

  - **Hivatkozási táblák**

    A hivatkozási táblák esetében a felosztási, egyesítési és áthelyezési műveletek az adatok a forrásról a cél szegmensbe másolhatók. Ne feledje azonban, hogy egy adott tábla cél szegmensén nem történt változás, ha a cél egy sor már szerepel ebben a táblázatban. A táblázatnak üresnek kell lennie minden táblázatos másolási művelet feldolgozásához.

  - **Egyéb táblák**

    A többi tábla lehet egy felosztási és egyesítési művelet forrásán vagy célján is. A felosztási-egyesítési szolgáltatás figyelmen kívül hagyja ezeket a táblákat bármilyen adatáthelyezési vagy másolási művelet esetében. Ne feledje azonban, hogy megkötések esetén ezek a műveletek zavarhatják a műveleteket.

    A hivatkozással és a többrétegű táblákkal kapcsolatos információkat az API-k tartalmazzák a szegmenses `SchemaInfo` térképen. Az alábbi példa bemutatja, hogyan használhatók ezek az API-k egy adott szegmenses Térkép-kezelő objektumon:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    A "Region" és a "Nation" tábla a hivatkozási táblákként van definiálva, és a rendszer a felosztott/egyesítési/áthelyezési műveletekkel másolja őket. a "Customer" és a "Orders" elem a következőként van meghatározva: szilánkokra osztott táblák. `C_CUSTKEY` és `O_CUSTKEY` a kiosztott kulcsként szolgál.

- **Hivatkozási integritás**

  A felosztási-egyesítési szolgáltatás elemzi a táblák közötti függőségeket, és a idegenkulcs-elsődleges kulcs kapcsolatait használja a táblák és shardletek áthelyezésére szolgáló műveletek előkészítéséhez. Általánosságban a hivatkozási táblákat a rendszer először a függőségi sorrendben másolja, majd az egyes kötegeken belüli függőségeik sorrendjében másolja a shardletek. Erre azért van szükség, hogy a célként megadott szegmensen az FK-PK megkötések tiszteletben legyenek, mivel az új adatértékek megérkeznek.

- **Szegmens Térkép konzisztenciája és végleges befejezése**

  A meghibásodások jelenléte esetén a felosztási-egyesítési szolgáltatás a leállás után folytatja a műveleteket, és a folyamatban lévő kérelmek teljesítését célozza meg. Előfordulhat azonban, hogy helyreállíthatatlan helyzetek merülhetnek fel, például ha a cél szegmens elvész, vagy a javítás után sérült. Ilyen körülmények között előfordulhat, hogy egyes áthelyezni kívánt shardletek továbbra is a forrás szegmensen találhatók. A szolgáltatás biztosítja, hogy a shardletbe-hozzárendelések csak akkor frissüljenek, ha sikeresen átmásolta a szükséges adatfájlokat a célhelyre. A shardletek csak akkor törlődnek a forráshoz, ha az összes adattal a célhelyre másolták, és a megfelelő leképezések frissítése sikeresen megtörtént. A törlési művelet a háttérben történik, miközben a tartomány már online állapotú a cél szegmensen. A felosztási-egyesítési szolgáltatás mindig biztosítja a szegmenses térképen tárolt leképezések helyességét.

## <a name="the-split-merge-user-interface"></a>A felosztás és egyesítés felhasználói felülete

A felosztási-egyesítési szolgáltatási csomag tartalmaz egy feldolgozói szerepkört és egy webes szerepkört. A webes szerepkör a felosztott egyesítési kérelmek interaktív módon történő küldésére szolgál. A felhasználói felület fő összetevői a következők:

- **Művelettípus**

  A művelet típusa egy választógomb, amely a kérelem által a szolgáltatás által végrehajtott művelet típusát vezérli. Választhat a felosztás, az egyesítés és az áthelyezés forgatókönyvek között. Egy korábban elküldött műveletet is megszakíthat. Használhatja a felosztási, egyesítési és áthelyezési kérelmeket a tartományhoz tartozó szegmenses térképekhez. A szegmenses térképek listázása csak az áthelyezési műveleteket támogatja.

- **Szegmenses Térkép**

  A kérelem paramétereinek következő szakasza a szegmenses térképre és a szegmens-térképet üzemeltető adatbázisra vonatkozó információkat tartalmazza. Különösen a shardmap üzemeltető kiszolgáló és adatbázis nevét kell megadnia, hitelesítő adatokat kell létesítenie a szegmenses Térkép adatbázisához, végül pedig a szegmenshez tartozó Térkép nevét. Jelenleg a művelet csak a hitelesítő adatok egyetlen halmazát fogadja el. Ezeknek a hitelesítő adatoknak megfelelő engedélyekkel kell rendelkezniük a szegmenses Térkép módosításainak végrehajtásához, valamint a szegmensekben lévő felhasználói adatokhoz.

- **Forrás tartománya (felosztás és egyesítés)**

  A felosztott és egyesítési művelet az alacsony és a magas kulcs használatával dolgozza fel a tartományt. Ha nem kötött magas kulccsal rendelkező műveletet szeretne megadni, jelölje be a "magas kulcs max." jelölőnégyzetet, és hagyja üresen a magas kulcs mezőt. A megadott tartomány-kulcs értékeinek nem kell pontosan egyezniük a leképezés és a hozzá tartozó szegélyek között a szegmens térképen. Ha nem ad meg tartományon kívüli határokat az összes szolgáltatásnál, a rendszer automatikusan kikövetkezteti a legközelebbi tartományt. A GetMappings.ps1 PowerShell-parancsfájl segítségével lekérheti az aktuális leképezéseket egy adott szegmens-térképen.

- **Felosztott forrás viselkedése (Split)**

  A felosztási műveletek esetében adja meg a forrástartomány felosztására szolgáló pontot. Ezt úgy teheti meg, hogy megadja a felosztáshoz használni kívánt horizontális kulcsot. A választógomb használatával megadhatja, hogy a tartomány alsó részét (a felosztott kulcs kivételével) át kívánja-e helyezni, vagy hogy szeretné-e áthelyezni a felső részt (beleértve a felosztott kulcsot is).

- **Forrás shardletbe (áthelyezés)**

  Az áthelyezési műveletek nem különböznek a felosztási vagy egyesítési művelettől, mert a forrás leírásához nem szükséges tartomány. Az áthelyezés forrása egyszerűen azonosítható az áthelyezni kívánt horizontálisan használt kulcs értékével.

- **Cél szilánk (felosztás)**

  Miután megadta az adatokat a felosztott művelet forrásán, meg kell adnia, hogy hová szeretné másolni az adatokat, ha megadja a kiszolgáló és az adatbázis nevét.

- **Céltartomány (egyesítés)**

  Az egyesítési műveletek áthelyezik a shardletek egy meglévő szegmensbe. A meglévő szegmenst úgy azonosíthatja, hogy megadja a meglévő tartomány tartományának határait, amelyekkel egyesíteni kívánja a-t.

- **Köteg mérete**

  A köteg mérete határozza meg az adatáthelyezés során az offline állapotú shardletek számát. Ez egy egész szám, amelyben kisebb értékeket használhat, ha a shardletek hosszú ideje érzékeny. A nagyobb értékek növelik azt az időt, ameddig egy adott shardletbe offline állapotban van, de javíthatja a teljesítményt.

- **Művelet azonosítója (Mégse)**

  Ha már folyamatban van egy már nem szükséges művelet, megszakíthatja a műveletet úgy, hogy megadja a művelet AZONOSÍTÓját ebben a mezőben. A művelet AZONOSÍTÓját a kérelem állapota táblából kérheti le (lásd a 8,1 szakaszt) vagy a webböngészőben lévő kimenetet, ahová a kérelmet elküldte.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

A felosztási-egyesítési szolgáltatás aktuális implementációja az alábbi követelmények és korlátozások hatálya alá tartozik:

- A szegmenseknek léteznie kell a szegmensben, és regisztrálni kell őket a szegmensben lévő felosztási művelet elvégzése előtt.
- A szolgáltatás a művelet részeként nem hoz létre táblákat vagy más adatbázis-objektumokat automatikusan. Ez azt jelenti, hogy az összes felosztott tábla és hivatkozási tábla sémájának léteznie kell a cél szegmensen a felosztás/egyesítés/áthelyezés művelet előtt. A többrészes táblázatok csak abban a tartományban maradhatnak üresen, amelyben az új shardletek hozzá kell adni egy felosztott/egyesítési/áthelyezési művelethez. Ellenkező esetben a művelet sikertelen lesz a kezdeti konzisztencia-ellenőrzés a cél szegmensen. Azt is vegye figyelembe, hogy a hivatkozási adatok csak akkor másolódnak le, ha a hivatkozási tábla üres, és hogy nincsenek konzisztencia-garanciák a hivatkozási táblákon lévő más egyidejű írási műveletekkel kapcsolatban. Javasoljuk, hogy a felosztási/egyesítési műveletek futtatásakor más írási műveletek ne módosítsák a hivatkozási táblákat.
- A szolgáltatás egy egyedi index vagy kulcs által létrehozott sor identitásra támaszkodik, amely magában foglalja a horizontális Felskálázási kulcsot a nagy shardletek teljesítményének és megbízhatóságának javítása érdekében. Ez lehetővé teszi, hogy a szolgáltatás még finomabb részletességgel helyezze át az adatokat, mint a horizontális Felskálázási kulcs értéke. Ez segít csökkenteni a művelet során szükséges naplófájlok és zárolások maximális mennyiségét. Hozzon létre egy egyedi indexet vagy egy elsődleges kulcsot, beleértve az adott tábla horizontális Felskálázási kulcsát is, ha azt szeretné, hogy a táblázatot a felosztott/egyesítési/áthelyezési kérelmekkel kívánja használni. A teljesítménnyel kapcsolatos okokból a horizontális Felskálázási kulcsnak a kulcs vagy az index vezető oszlopának kell lennie.
- A kérelmek feldolgozásának során bizonyos shardletbe-adatmennyiségek a forráson és a cél szegmensen is szerepelhetnek. Ez a shardletbe-áthelyezés során fellépő hibák elleni védelemhez szükséges. A felosztási egyesítés és a szegmenses társítás összevonása biztosítja, hogy az Adatfüggő útválasztási API-k által a **OpenConnectionForKey** metódus használatával létesített kapcsolatok ne lássanak inkonzisztens közbenső állapotokat. Ha azonban a **OpenConnectionForKey** metódus használata nélkül csatlakozik a forráshoz vagy a cél szegmensekhez, inkonzisztens közbenső állapotok jelenhetnek meg, ha a felosztási/egyesítési/áthelyezési kérelmek folyamatban vannak. Ezek a kapcsolatok részleges vagy ismétlődő eredményeket is megjeleníthetnek az Időzítéstől vagy a kapcsolat alapjául szolgáló szegmenstől függően. Ez a korlátozás jelenleg a rugalmas skálázású többszegmenses lekérdezések által létrehozott kapcsolatokat foglalja magában.
- A felosztási-egyesítési szolgáltatás metaadat-adatbázisa nem osztható meg a különböző szerepkörök között. Például az átmeneti tárolásban futó felosztási-egyesítési szolgáltatás szerepkörének egy másik metaadat-adatbázisra kell mutatnia, mint a termelési szerepkörnek.

## <a name="billing"></a>Számlázás

A felosztott egyesítés szolgáltatás felhőalapú szolgáltatásként fut a Microsoft Azure-előfizetésben. Ezért a Cloud Services díjai a szolgáltatás egy példányára vonatkoznak. Ha gyakran végez felosztási/egyesítési/áthelyezési műveleteket, javasoljuk, hogy törölje a felosztott egyesítéses felhőalapú szolgáltatást. Ez a szolgáltatás a Cloud Service-példányok futtatására és üzembe helyezésére vonatkozó költségeket takarít meg. Bármikor újratelepítheti és elindíthatja az azonnal futtatható-konfigurációt, amikor felosztási vagy egyesítési műveleteket kell végrehajtania.

## <a name="monitoring"></a>Figyelés

### <a name="status-tables"></a>Állapot táblák

A felosztott egyesítés szolgáltatás biztosítja a **RequestStatus** táblát a metaadat-tároló adatbázisban a befejezett és a folyamatban lévő kérések figyeléséhez. A táblázat felsorolja az egyes felosztott egyesítési kérelmek sorát, amelyeket elküldtek a felosztott egyesítési szolgáltatás ezen példánya számára. Minden kérelemhez a következő információkat adja meg:

- **Időbélyeg**

  A kérelem elindításának időpontja és dátuma.

- **OperationId**

  Egy GUID, amely egyedileg azonosítja a kérést. Ezt a kérést a művelet megszakítására is használhatja, amíg még folyamatban van.

- **Állapot**

  A kérelem aktuális állapota. A folyamatban lévő kérések esetében az azt is felsorolja, hogy a kérelem milyen aktuális fázisban van.

- **CancelRequest**

  Egy jelző, amely jelzi, hogy a kérést megszakították-e.

- **Előrehaladás**

  A művelet befejezésének százalékos értéke. A 50 érték azt jelzi, hogy a művelet körülbelül 50%-ban befejeződött.

- **Részletek**

  Egy XML-érték, amely részletesebb jelentést nyújt a folyamatról. Az előrehaladási jelentés rendszeres időközönként frissül, mert a rendszer a forrásról a célhelyre másolja a sorok készletét. Meghibásodások vagy kivételek esetén ez az oszlop a hibával kapcsolatos részletesebb információkat is tartalmaz.

### <a name="azure-diagnostics"></a>Azure Diagnostics

A felosztási-egyesítési szolgáltatás az Azure SDK 2,5-alapú, monitorozási és diagnosztikai célú Azure Diagnosticst használja. A diagnosztikai konfigurációt az itt leírtak szerint vezérelheti: a [diagnosztika engedélyezése az Azure Cloud Services és Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). A letöltési csomag két diagnosztikai konfigurációt tartalmaz: egyet a webes szerepkörhöz, egyet pedig a feldolgozói szerepkörhöz. Ez magában foglalja a teljesítményszámlálók naplózását, az IIS-naplókat, a Windows-eseménynaplókat és a felosztott alkalmazás-eseménynaplókat.

## <a name="deploy-diagnostics"></a>Diagnosztika üzembe helyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Ha a NuGet-csomag által biztosított webes és feldolgozói szerepkörök diagnosztikai konfigurációjának használatával szeretné engedélyezni a figyelést és a diagnosztikát, futtassa a következő parancsokat a Azure PowerShell használatával:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

A diagnosztikai beállítások konfigurálásával és üzembe helyezésével kapcsolatos további információkért tekintse meg a [diagnosztika engedélyezése az Azure-ban Cloud Services és Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Diagnosztika beolvasása

A diagnosztika a Server Explorer fájának Azure-részében könnyen elérhető a Visual Studio Server Explorerben. Nyisson meg egy Visual Studio-példányt, és a menüsávban kattintson a nézet, majd a Server Explorer elemre. Az Azure-előfizetéshez való kapcsolódáshoz kattintson az Azure ikonra. Ezután navigáljon az Azure-> Storage-> `<your storage account>` -> Tables-> WADLogsTable. További információ: [Server Explorer](/previous-versions/x603htbk(v=vs.140)).

![WADLogsTable][2]

A fenti ábrán Kiemelt WADLogsTable a felosztott egyesítési szolgáltatás alkalmazási naplójának részletes eseményeit tartalmazza. Vegye figyelembe, hogy a letöltött csomag alapértelmezett konfigurációja az éles üzembe helyezésre irányul. Ezért a naplók és a számlálók a szolgáltatási példányokból való kihúzásának időköze nagy (5 perc). A teszteléshez és fejlesztéshez csökkentse az intervallumot úgy, hogy a webes vagy a feldolgozói szerepkör diagnosztikai beállításait módosítja az igényeinek megfelelően. Kattintson a jobb gombbal a szerepkörre a Visual Studio Server Explorerben (lásd fentebb), majd módosítsa az átvitel időtartamát a párbeszédpanelen a diagnosztika konfigurációs beállításaihoz:

![Konfiguráció][3]

## <a name="performance"></a>Teljesítmény

Általánosságban elmondható, hogy a jobb teljesítmény várhatóan magasabb, több teljesítményt nyújtó szolgáltatási szintet vár. Magasabb IO-, processzor-és memória-kiosztások a magasabb szolgáltatási rétegekhez előnyben részesülnek a felosztott egyesítési szolgáltatás által használt tömeges másolási és törlési műveletekben. Ebből kifolyólag növelje a szolgáltatási szintet az adott adatbázisokra vonatkozóan egy meghatározott, korlátozott ideig.

A szolgáltatás a szokásos műveleteinek részeként is végrehajtja az érvényesítési lekérdezéseket. Ezek az ellenőrzési lekérdezések az adatok váratlan jelenlétét keresik a céltartományban, és biztosítják, hogy a felosztott/egyesítési/áthelyezési műveletek konzisztens állapotból induljon el. Ezek a lekérdezések mindegyike a művelet hatóköre és a kérelem definíciójának részeként megadott köteg mérete által meghatározott horizontális skálázási tartományokon működik. Ezeket a lekérdezéseket a legjobban akkor hajtják végre, ha egy olyan index van jelen, amely a fő oszlophoz tartozó horizontális Felskálázási kulccsal rendelkezik.

Továbbá egy egyediségi tulajdonság a horizontális Felskálázási kulccsal, amely a vezető oszlopban lehetővé teszi, hogy a szolgáltatás egy optimalizált megközelítést használjon, amely az erőforrás-felhasználást korlátozza a naplózási terület és a memória tekintetében. Ez az egyediségi tulajdonság a nagyméretű adatméretek áthelyezéséhez szükséges (általában 1 GB-nál nagyobb).

## <a name="how-to-upgrade"></a>A frissítés lépései

1. Kövesse a [Split-Merge szolgáltatás üzembe helyezése](elastic-scale-configure-deploy-split-and-merge.md)című témakör lépéseit.
2. Módosítsa a felhőalapú szolgáltatás konfigurációs fájlját a felosztott egyesítési központi telepítésre, hogy az tükrözze az új konfigurációs paramétereket. Egy új kötelező paraméter a titkosításhoz használt tanúsítványra vonatkozó információ. Ennek egyszerű módja, ha összehasonlítja az új konfigurációs sablonfájl fájlját a letöltéssel a meglévő konfiguráció alapján. Ügyeljen rá, hogy a "DataEncryptionPrimaryCertificateThumbprint" és a "DataEncryptionPrimary" beállításait is hozzáadja a webes és a feldolgozói szerepkörhöz.
3. Mielőtt telepítené a frissítést az Azure-ban, győződjön meg arról, hogy az összes jelenleg futó felosztott egyesítési művelet befejeződött. Ezt egyszerűen megteheti úgy, hogy lekérdezi a RequestStatus és a PendingWorkflows táblákat a folyamatos kérések összevonása a metaadatokat tartalmazó adatbázisban.
4. Frissítse meglévő felhőalapú szolgáltatását az Azure-előfizetésben az új csomaggal és a frissített szolgáltatás konfigurációs fájljával, és ossza meg az Azure-előfizetését.

A frissítéshez nem szükséges új metaadat-adatbázist kiépíteni a felosztott egyesítéshez. Az új verzió automatikusan frissíti a meglévő metaadat-adatbázist az új verzióra.

## <a name="best-practices--troubleshooting"></a>Ajánlott eljárások és hibaelhárítás

- Definiáljon egy teszt bérlőt, és gyakorolja a legfontosabb felosztott/egyesítési/áthelyezési műveleteket a több szegmensen belüli tesztelési Bérlővel. Győződjön meg arról, hogy az összes metaadat helyesen van definiálva a szegmenses térképen, és hogy a műveletek nem sértik a korlátozásokat vagy a külső kulcsokat.
- Tartsa meg a legnagyobb bérlő maximális adatmérete feletti teszt bérlői adatméretet, hogy ne legyenek az adatmérettel kapcsolatos problémák. Ez segít felmérni a felső határt arra az időre, amíg egyetlen bérlőt helyez át a köré.
- Győződjön meg arról, hogy a séma lehetővé teszi a törlést. A felosztási-egyesítési szolgáltatásnak képesnek kell lennie az adatok eltávolítására a forrás szegmensből, ha az adatok a célhelyre való másolása sikeresen megtörtént. Az **Eseményindítók törlésével** például megakadályozhatja, hogy a szolgáltatás törölje a forrás adatait, és a műveletek meghiúsulnak.
- A horizontális Felskálázási kulcsnak az elsődleges kulcs vagy az egyedi index definíciójának vezető oszlopának kell lennie. Ez biztosítja a legjobb teljesítményt a felosztási vagy egyesítési ellenőrzési lekérdezéseknél, valamint a tényleges adatáthelyezési és törlési műveletekhez, amelyek mindig a kulcsfontosságú tartományokon működnek.
- A rézvezetékes végezhet a régióban és az adatközpontban tárolja az adatbázisokat.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png