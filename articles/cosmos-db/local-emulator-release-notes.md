---
title: A Azure Cosmos DB Emulator letöltése és kibocsátási megjegyzései
description: Szerezze be a Azure Cosmos DB Emulator kibocsátási megjegyzéseit a különböző verziókhoz, és töltse le az információkat.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: ff20479062e31bb61756b3de7bbf04540b2d5598
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690396"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB Emulator – kibocsátási megjegyzések és letöltési információk
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk a Azure Cosmos DB Emulator kibocsátási megjegyzéseit mutatja be az egyes kiadásokban végrehajtott szolgáltatások frissítéseinek listájával. Emellett felsorolja az emulátor legújabb verzióját is a letöltéshez és a használatához.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltése**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Első lépések**|[Helyi fejlesztés Azure Cosmos DB emulátorral](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="21111-22-february-2021"></a>2.11.11 (22 február 2021)

 - Ez a kiadás frissíti a helyi Adatkezelő tartalmat a legújabb Azure Portal verzióra.


### <a name="21110-5-january-2021"></a>2.11.10 (5 január 2021)

 - Ez a kiadás frissíti a helyi Adatkezelő tartalmat a legújabb Azure Portal verzióra, és egy új nyilvános lehetőséget ("/ExportPemCert") ad hozzá, amely lehetővé teszi, hogy az emulátor felhasználója közvetlenül exportálja a nyilvános emulátor tanúsítványát. PEM-fájl.

### <a name="2119-3-december-2020"></a>2.11.9 (2020. december 3.)

 - Ez a kiadás a Azure Cosmos DB legújabb funkcióit és fejlesztéseit tükröző általános tartalom frissítésén kívül a Azure Cosmos DB Emulator funkcióival kapcsolatos problémákat is tárgyalja:
 * Javítsa ki a problémát, ha a nagyméretű dokumentum-adattartalom kérése sikertelen a közvetlen és a Java-ügyfélalkalmazások használata esetén.
 * Javítsa ki a MongoDB Endpoint 3,6-es verziójának kapcsolódási problémáját, ha a .NET-alapú alkalmazások célozzák meg.

### <a name="2118-6-november-2020"></a>2.11.8 (2020. november 6.)

 - Ez a kiadás tartalmazza a Cosmos Emulator Adatkezelő frissítését, és kijavítja azt a problémát, amelyben a TLS 1,3-ügyfelek megpróbálják megnyitni a Adatkezelő.

### <a name="2116-6-october-2020"></a>2.11.6 (2020. október 6.)

 - Ez a kiadás a párhuzamosságtal kapcsolatos problémát tárgyalja, ha egyszerre több tároló is létrehozható. Ilyen esetekben az emulátor adatai sérült állapotban maradnak, és az emulátor végpontjának API-kérelmei a "szolgáltatás nem érhető el" hibával meghiúsulnak, újraindítást igényelnek, és az emulátor helyi adatai alaphelyzetbe állnak.

### <a name="2115-23-august-2020"></a>2.11.5 (2020. augusztus 23.)

Ez a kiadás két új, Cosmos Emulator indítási lehetőséget hoz létre: 

* "/EnablePreview" – lehetővé teszi az emulátor előzetes funkcióinak használatát. Az előzetes verzióban még fejlesztés alatt álló funkciók is elérhetők a CI és a Sample Writing használatával.
* "/EnableAadAuthentication" – lehetővé teszi, hogy az emulátor fogadja az egyéni Azure Active Directory jogkivonatokat az Azure Cosmos elsődleges kulcsai alternatívájaként. Ez a funkció továbbra is fejlesztés alatt áll; az adott szerepkör-hozzárendelések és az egyéb engedélyekkel kapcsolatos beállítások jelenleg nem támogatottak.

### <a name="2112-07-july-2020"></a>2.11.2 (2020. július 7.)

- Ez a kiadás megváltoztatja a Cosmos-emulátor hibaelhárításakor szükséges ETL-nyomkövetési adatokat. A WPR (Windows teljesítmény-futtatókörnyezet eszközei) mostantól az ETL-alapú Nyomkövetések rögzítésének alapértelmezett eszközei, miközben a régi LOGMAN-alapú rögzítés elavult. Ez a módosítás azért szükséges, mert a legújabb Windows-biztonsági frissítések váratlan hatással voltak arra, hogy a LOGMAN hogyan működik, amikor a Cosmos emulatoron keresztül hajtja végre.

### <a name="2111-10-june-2020"></a>2.11.1 (2020. június 10.)

- Ez a kiadás javít néhány, az Emulator Adatkezelőával kapcsolatos hibát. Bizonyos esetekben, amikor egy webböngészőn keresztül Adatkezelő emulátort, nem tud kapcsolódni a Cosmos Emulator-végponthoz, és az összes kapcsolódó művelet, például egy adatbázis vagy egy tároló létrehozása hibát okoz. A második probléma kijavítva van, hogy egy JSON-fájlból Adatkezelő feltöltési művelettel hozzon létre egy elemet.

### <a name="2110"></a>2.11.0

- Ez a kiadás a kiosztott átviteli sebesség támogatását mutatja be. Ezek az új funkciók lehetővé teszik az egyéni maximális kiosztott átviteli sebesség beállítását a kérelmek egységében (RU/s), az autoskálázást a meglévő adatbázisokon és tárolókban, valamint a programozási támogatást Azure Cosmos DB SDK-k segítségével.
- Probléma megoldása a nagy számú dokumentum (1 GB-nál több) lekérdezése során az emulátor sikertelen lesz a belső hiba 500-as állapotkód esetén.

### <a name="292"></a>2.9.2

- Ez a kiadás kijavít egy hibát, miközben engedélyezi az MongoDb Endpoint 3,2-es verziójának támogatását. Emellett támogatást nyújt az ETL-Nyomkövetések létrehozásához a hibaelhárítási célból a LOGMAN helyett a WPR használatával.

### <a name="291"></a>2.9.1

- Ez a kiadás javít néhány problémát a lekérdezési API támogatásában, és visszaállítja a kompatibilitást a régebbi OSs-vel, például a Windows Server 2012-mel.

### <a name="290"></a>2.9.0

- Ezzel a kiadással beállíthatja, hogy a konzisztencia konzisztens előtagra legyen beállítva, és növelje a felhasználók és az engedélyek maximális korlátját.

### <a name="272"></a>2.7.2

- Ez a kiadás hozzáadja a MongoDB 3,6-es verziójú kiszolgálói támogatást a Cosmos Emulatorhoz. Egy olyan MongoDB-végpont indításához, amely a szolgáltatás 3,6-es verzióját célozza meg, indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableMongoDBEndpoint = 3.6" kapcsolóval.

### <a name="270"></a>2.7.0

- Ez a kiadás javít egy regressziót, amely megakadályozta, hogy a felhasználók a .NET Core vagy x86 .NET-alapú ügyfelek használatakor lekérdezéseket futtassanak az emulátorból az SQL API-fiókból.

### <a name="246"></a>2.4.6

- Ez a kiadás paritást biztosít az Azure Cosmos szolgáltatás szolgáltatásaival ( [Azure Cosmos DB](local-emulator.md)2019. július). Emellett javít számos, az emulátorral kapcsolatos hibát, amikor a parancssorból és a belső IP-címek felülbírálásait használja a közvetlen módú kapcsolattal rendelkező SDK-ügyfelek számára.

### <a name="243"></a>2.4.3

- Alapértelmezés szerint le van tiltva a MongoDB szolgáltatás elindítása. Csak az SQL-végpont engedélyezett alapértelmezettként. A felhasználónak manuálisan kell elindítania a végpontot az emulátor "/EnableMongoDbEndpoint" parancssori kapcsolójának használatával. Most, mint az összes többi szolgáltatási végpont, például Gremlin, Cassandra és Table.
- Kijavított egy hibát az emulátorban a "/AllowNetworkAccess" kifejezéssel kezdődően, ahol a Gremlin, Cassandra és Table végpontok nem tudták megfelelően kezelni a külső ügyfelektől érkező kéréseket.
- Közvetlen kapcsolódási portok hozzáadása a tűzfalszabályok beállításaihoz.

### <a name="240"></a>2.4.0

- Kijavítva egy probléma az emulátorban, amely nem indul el, ha a hálózati figyelési alkalmazások (például a Pulse-ügyfél) jelen vannak a gazdagépen.
