---
title: A Azure Cosmos DB Emulator parancssori és PowerShell-referenciája
description: Megtudhatja Azure Cosmos DB emulátor parancssori paramétereit, hogyan vezérelheti az emulátort a PowerShell használatával, és hogyan módosíthatja az emulátoron belül létrehozható tárolók számát.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979504"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>A Azure Cosmos DB Emulator parancssori és PowerShell-referenciája
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB Emulator egy helyi környezetet biztosít, amely a Azure Cosmos DB szolgáltatást helyi fejlesztési célokra emulálja. [Az emulátor telepítése](local-emulator.md)után a parancssori és a PowerShell-parancsokkal vezérelheti az emulátort. Ez a cikk a parancssori és a PowerShell-parancsok használatát ismerteti az emulátor elindításához és leállításához, a beállítások konfigurálásához és egyéb műveletek végrehajtásához. A parancsokat a telepítési helyről kell futtatnia.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Az emulátor kezelése parancssori szintaxissal

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

A beállítások listájának megtekintéséhez írja be a `Microsoft.Azure.Cosmos.Emulator.exe /?` parancsot a parancssorba.

|**Beállítás** | **Leírás** | **Parancs**| **Argumentumok**|
|---|---|---|---|
|[Nincsenek argumentumok] | Az alapértelmezett beállításokkal indítja el az Azure Cosmos DB Emulatort. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Súgó] |Megjeleníti a támogatott parancssori argumentumok listáját.|Microsoft.Azure.Cosmos.Emulator.exe/? | |
| GetStatus |Lekéri az Azure Cosmos DB Emulator állapotát. Az állapotot a kilépési kód jelzi: 1 = Indítás, 2 = Fut, 3 = Leállítva. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet. | Microsoft.Azure.Cosmos.Emulator.exe/GetStatus| |
| Leállítás| Bezárja az Azure Cosmos DB Emulatort.| Microsoft.Azure.Cosmos.Emulator.exe/shutdown parancsot | |
|DataPath | Meghatározza az adatfájlok tárolására szolgáló elérési utat. Az alapértelmezett érték a%LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Elérhető elérési út |
|Port | Az emulátorhoz használni kívánt portszámot határozza meg. Az alapértelmezett érték a 8081. |Microsoft.Azure.Cosmos.Emulator.exe/port =\<port\> | \<port\>: Egyetlen portszám |
| ComputePort | A számítási együttműködési átjáró szolgáltatáshoz használandó portszám megadása. Az átjáró HTTP-végpontjának mintavételi portja a következőképpen számítja ki: ComputePort + 79. Ezért a ComputePort és a ComputePort + 79 nyitva kell lennie és elérhetőnek kell lennie. Az alapértelmezett érték a 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Egyetlen portszám |
| EnableMongoDbEndpoint = 3.2 | A MongoDB API 3,2-es verziójának engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | A MongoDB API 3,6-es verziójának engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Az alapértelmezett érték a 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Egyetlen portszám|
| EnableCassandraEndpoint | Engedélyezi a Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe/EnableCassandraEndpoint | |
| CassandraPort | Megadja a Cassandra-végponthoz használandó portszámot. Az alapértelmezett érték a 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Egyetlen portszám |
| EnableGremlinEndpoint | Gremlin API engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe/EnableGremlinEndpoint | |
| GremlinPort | A Gremlin-végponthoz használandó portszám. Az alapértelmezett érték a 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Egyetlen portszám |
|EnableTableEndpoint | Az Azure Table API engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe/EnableTableEndpoint | |
|TablePort | Az Azure Table végponthoz használandó portszám Az alapértelmezett érték a 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Egyetlen portszám|
| KeyFile | Az engedélyezési kulcs olvasása a megadott fájlból. A/GenKeyFile kapcsoló használata a keyfile létrehozásához | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: A fájl elérési útja |
| ResetDataPath | A megadott elérési úton lévő összes fájl rekurzív eltávolítása. Ha nem ad meg elérési utat, az alapértelmezett érték a%LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Fájl elérési útja  |
| StartTraces  |  Hibakeresési nyomkövetési naplók gyűjtésének megkezdése a LOGMAN használatával. | Microsoft.Azure.Cosmos.Emulator.exe/StartTraces | |
| StopTraces     | A hibakeresési nyomkövetési naplók gyűjtésének leállítása a LOGMAN használatával. | Microsoft.Azure.Cosmos.Emulator.exe/StopTraces  | |
| StartWprTraces  |  A hibakeresési nyomkövetési naplók gyűjtésének megkezdése a Windows Teljesítményfigyelő eszközzel. | Microsoft.Azure.Cosmos.Emulator.exe/StartWprTraces | |
| StopWprTraces     | A hibakeresési nyomkövetési naplók gyűjtésének leállítása a Windows Teljesítményfigyelő eszközzel. | Microsoft.Azure.Cosmos.Emulator.exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Alapértelmezés szerint az emulátor újra létrehozza önaláírt TLS/SSL-tanúsítványát, ha a tanúsítvány SAN nem tartalmazza az emulátor tartományának nevét, a helyi IPv4-címeket, a "localhost" és a "127.0.0.1" értéket. Ha ezt a lehetőséget választja, az emulátor indításkor sikertelen lesz. Ezután a/GenCert lehetőség használatával hozzon létre és telepítsen egy új önaláírt TLS/SSL-tanúsítványt. | Microsoft.Azure.Cosmos.Emulator.exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Új önaláírt TLS/SSL-tanúsítvány létrehozása és telepítése. szükség esetén a további DNS-nevek vesszővel tagolt listáját is megadhatja az emulátor hálózaton keresztüli eléréséhez. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: A további DNS-nevek vesszővel tagolt listája nem kötelező  |
| DirectPorts |A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPorts:\<directports\> | \<directports\>: 4 Port vesszővel tagolt listája |
| Kulcs |Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: A kulcsnak egy 64 bájtos vektor Base-64 kódolásának kell lennie|
| EnableRateLimiting | Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van. |Microsoft.Azure.Cosmos.Emulator.exe/EnableRateLimiting | |
| DisableRateLimiting |Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva. |Microsoft.Azure.Cosmos.Emulator.exe/DisableRateLimiting | |
| NoUI | Az emulátor felhasználói felületének megjelenítése nélkül. | Microsoft.Azure.Cosmos.Emulator.exe/NoUI | |
| NoExplorer | Az adatkezelő nem jelenik meg az indításkor. |Microsoft.Azure.Cosmos.Emulator.exe/NoExplorer | | 
| PartitionCount | A particionált tárolók maximális számát határozza meg. További információt [a tárolók számának módosítása](#set-partitioncount) című témakörben talál. | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Engedélyezett egyetlen partíciós tárolók maximális száma. Az alapértelmezett érték 25. Maximálisan 250 engedélyezett.|
| DefaultPartitionCount| Meghatározza egy particionált tároló partícióinak alapértelmezett számát. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> Az alapértelmezett érték 25.|
| AllowNetworkAccess | Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. \<key_string\> \<file_name\> A hálózati hozzáférés engedélyezéséhez a/Key = vagy a/keyfile = kapcsolót is át kell adnia. | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> vagy Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/keyfile =\<file_name\>| |
| NoFirewall | Ne módosítsa a tűzfalszabályok használatát, ha a/AllowNetworkAccess beállítás van használatban. |Microsoft.Azure.Cosmos.Emulator.exe/NoFirewall | |
| GenKeyFile | Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Konzisztencia | Állítsa be a fiók alapértelmezett konzisztenciaszintjét. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: Az értéknek a következő konzisztencia- [szintek](consistency-levels.md)egyikének kell lennie: munkamenet, erős, végleges vagy BoundedStaleness. Az alapértelmezett érték a munkamenet (session). |
| ? | A súgóüzenet megjelenítése.| | |

## <a name="manage-the-emulator-with-powershell"></a>Az emulátor kezelése a PowerShell-lel

Az emulátor egy PowerShell-modullal elindíthatja, leállíthatja, eltávolíthatja és lekérheti a szolgáltatás állapotát. Futtassa a következő parancsmagot a PowerShell-modul használatához:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

vagy helyezze a `PSModules` könyvtárat a könyvtárba `PSModulePath` , és importálja a következő parancsban látható módon:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Itt láthatja az emulátor PowerShellből való vezérlésére szolgáló parancsok összegzését:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Megjegyzések**

Ezen ServiceControllerStatus értékek egyikét adja vissza: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running vagy ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Megjegyzések**

Elindítja az emulátort. Alapértelmezés szerint a parancs addig vár, amíg az emulátor készen nem áll a kérések fogadására. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor indításakor visszatérjen.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Megjegyzések**

Leállítja az emulátort. Alapértelmezés szerint ez a parancs addig vár, amíg az emulátor teljesen le nem állt. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor leállításakor visszatérjen.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Megjegyzések**

Eltávolítja az emulátort, és választhatóan eltávolítja az $env:LOCALAPPDATA\CosmosDbEmulator mappa teljes tartalmát.
A parancsmag biztosítja, hogy az emulátor le legyen állítva az eltávolítása előtt.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Az alapértelmezett tárolók számának módosítása

Alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre (csak Azure Cosmos DB SDK-k használata esetén támogatott), vagy 5 korlátlan tárolót a Azure Cosmos DB Emulator használatával. A **PartitionCount** érték módosításával akár 250 rögzített méretű tárolót vagy 50 korlátlan tárolót hozhat létre, vagy a kettő bármely kombinációját, amely nem haladja meg a 250 rögzített méretű tárolókat (ahol egy korlátlan tároló = 5 rögzített méretű tároló). Az emulátor azonban nem ajánlott úgy beállítani, hogy több mint 200 rögzített méretű tárolóval fusson. A lemez i/o-műveleteihez hozzáadott terhelés miatt a végponti API-k használatakor előre nem látható időtúllépéseket eredményezhet.

Ha az aktuális partíciók számának túllépése után megpróbál létrehozni egy tárolót, az emulátor egy ServiceUnavailable-kivételt hoz létre a következő üzenettel.

> Jelenleg nagy az igényünk ebben a régióban, ezért nem teljesítheti a kérést. Folyamatosan dolgozunk, hogy a kapacitás online állapotba kerüljön, és javasoljuk, hogy próbálkozzon újra.
> Tevékenységazonosító: 12345678-1234-1234-1234-123456789abc

A Azure Cosmos DB emulátorban elérhető tárolók számának módosításához futtassa a következő lépéseket:

1. Az összes helyi Azure Cosmos DB Emulator adat törléséhez kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson az **Adatok visszaállítása…** lehetőségre.

1. A mappában lévő összes Emulator-érték törlése `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Az összes nyitott példány bezárásához kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson a **Kilépés** lehetőségre. Az összes példány bezárása egy percig is eltarthat.

1. Telepítse az [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) legújabb verzióját.

1. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Példa: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Következő lépések

* [A Azure Cosmos DB Emulator-tanúsítványok exportálása Java-, Python-és Node.js-alkalmazásokkal való használatra](local-emulator-export-ssl-certificates.md)
* [Hibakeresési problémák az emulátorral](troubleshoot-local-emulator.md)
