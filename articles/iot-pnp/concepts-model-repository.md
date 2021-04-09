---
title: Az eszköz modellek tárházának fogalmai | Microsoft Docs
description: Megoldás fejlesztőként vagy informatikai szakemberként megismerheti az eszköz modellek tárházának alapvető fogalmait.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582646"
---
# <a name="device-models-repository"></a>Eszköz modellek tárháza

Az eszköz modellek tárháza (DMR) lehetővé teszi az eszközök építői számára a IoT Plug and Play eszköz modelljeinek felügyeletét és megosztását. Az eszköz modelljei a [digitális Twins modellezési nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)használatával meghatározott JSON ld-dokumentumok.

A DMR meghatározza azt a mintát, amely az DTDL-interfészeket az eszköz kettős modell-azonosítója (DTMI) alapján tárolja a mappák struktúrájában. A DMR található felületet úgy keresheti meg, hogy a DTMI egy relatív elérési útra konvertálja. Például a `dtmi:com:example:Thermostat;1` DTMI lefordítja a következőre: `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Nyilvános eszköz modelljeinek tárháza

A Microsoft a következő tulajdonságokkal rendelkező nyilvános DMR üzemeltet:

- Kurátori modellek. A Microsoft felülvizsgálja és jóváhagyja az összes elérhető felületet egy GitHub pull-kérés (PR) érvényesítési munkafolyamattal.
- Módosíthatatlansági.  A közzétételt követően az illesztőfelület nem frissíthető.
- Hyper-Scale. A Microsoft biztosítja a szükséges infrastruktúrát ahhoz, hogy biztonságos, méretezhető végpontot hozzon létre, amely lehetővé teszi az eszközök modelljeinek közzétételét és felhasználását.

## <a name="custom-device-models-repository"></a>Egyéni eszköz modellek tárháza

Ugyanazt a DMR mintát használva egyéni DMR hozhat létre bármely tárolóeszközben, például helyi fájlrendszerben vagy egyéni HTTP-webkiszolgálókban. Az egyéni DMR ugyanúgy kérhet le eszközöket, mint a nyilvános DMR, ha módosítja a DMR eléréséhez használt alap URL-címet.

> [!NOTE]
> A Microsoft eszközöket biztosít a nyilvános DMR lévő eszközök modelljeinek ellenőrzéséhez. Ezeket az eszközöket egyéni adattárakban is felhasználhatja.

## <a name="public-models"></a>Nyilvános modellek

A modellek tárházában tárolt nyilvános eszközök modelljei mindenki számára elérhetők, hogy az alkalmazásaikat használják és integrálják. A nyilvános eszközök modelljei lehetővé teszik, hogy az eszköz-építők és a megoldások fejlesztői számára nyílt öko-rendszer használatával megosszák és újra felhasználhassa a IoT Plug and Play eszköz modelljeit.

Tekintse át a modell [közzététele](#publish-a-model) című szakaszt, amely útmutatást nyújt ahhoz, hogyan tehet közzé egy modellt a modell-adattárban annak érdekében, hogy azok nyilvánosak legyenek.

A felhasználók megkereshetik, megkereshetik és megtekinthetik a hivatalos [GitHub-tárházból](https://github.com/Azure/iot-plugandplay-models)származó nyilvános felületeket.

A mappákban található összes felület `dtmi` a nyilvános végpontról is elérhető. [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Modellek feloldása

Ezen felületek programozott eléréséhez használhatja az `ModelsRepositoryClient` [Azure. IoT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository)NuGet-csomagban elérhető lehetőséget. Ez az ügyfél alapértelmezés szerint úgy van konfigurálva, hogy lekérdezze a [devicemodels.Azure.com](https://devicemodels.azure.com/) címen elérhető nyilvános DMR, és bármely egyéni tárházra konfigurálható.

Az ügyfél a `DTMI` bemenetet fogadja, és egy olyan szótárt ad vissza, amely az összes szükséges csatolóval rendelkezik:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

A várt kimenetnek a `DTMI` függőségi láncban található három csatolót kell megjelenítenie:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

A konfigurálható `ModelsRepositoryClient` úgy, hogy egy egyéni modell-tárházat Kérdezzen le – http (s) protokollon keresztül érhető el –, és a függőségi feloldást a rendelkezésre állók bármelyikével megadhatja `ModelDependencyResolution` :

- Letiltva. Csak a megadott felületet adja vissza, függőség nélkül.
- Engedélyezve. A függőségi lánc összes felületét adja vissza.
- TryFromExpanded. A `.expanded.json` fájl használata az előre kiszámított függőségek lekéréséhez 

> [!Tip] 
> Előfordulhat, hogy az egyéni Tárházak nem teszik `.expanded.json` elérhetővé a fájlt, ha az ügyfél nem áll rendelkezésre az egyes függőségek helyi feldolgozásához.

A következő mintakód azt mutatja be, hogyan lehet inicializálni a `ModelsRepositoryClient` -t egy egyéni adattár ALAPurl-címével, ebben az esetben a `raw` GitHub API URL-címeivel anélkül, hogy az űrlapot kellene használnia `expanded` – mivel az nem érhető el a `raw` végponton. A `AzureEventSourceListener` inicializálva van az ügyfél által végrehajtott HTTP-kérelem vizsgálatára:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

További minták érhetők el az Azure SDK GitHub-tárház forráskódjában: [Azure. IOT. ModelsRepository/Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Modell közzététele

> [!Important]
> Olyan GitHub-fiókkal kell rendelkeznie, amely lehetővé teszi modellek beküldését a nyilvános DMR.

1. A nyilvános GitHub-tárház elágazása: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Az elágazó tárház klónozása. Opcionálisan létrehozhat egy új ágat, hogy a módosítások elszigetelve maradjanak a `main` fiókirodában.
1. Adja hozzá az új felületeket a `dtmi` mappához a mappa/fájlnév konvenció használatával. További információ: [modell importálása a `dtmi/` mappába](#import-a-model-to-the-dtmi-folder).
1. Ellenőrizze a modelleket helyileg az `dmr-client` eszköz használatával. További információ: [modellek ellenőrzése](#validate-models).
1. Véglegesítse a módosításokat helyileg, és küldje el az elágazásba.
1. Az elágazásból hozzon létre egy lekéréses kérelmet, amely az ágat célozza meg `main` . Lásd: [probléma létrehozása vagy lekéréses kérelmekre](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) vonatkozó dokumentumok.
1. Tekintse át a [pull-kérelmekre vonatkozó követelményeket](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

A lekéréses kérelem elindít egy olyan GitHub-műveletet, amely érvényesíti az elküldött adaptereket, és gondoskodik arról, hogy a lekéréses kérelem megfeleljen az összes követelménynek.

A Microsoft három munkanapon belül minden ellenőrzés után válaszol egy lekéréses kérelemre.

### <a name="dmr-client-tools"></a>`dmr-client` eszközök

A modellek a PR-ellenőrzések során történő ellenőrzéséhez használt eszközök a DTDL felületek helyi hozzáadására és ellenőrzésére is használhatók.

> [!NOTE]
> Ehhez az eszközhöz a [.net SDK](https://dotnet.microsoft.com/download) 3,1-es vagy újabb verziójára van szükség.

### <a name="install-dmr-client"></a>Telepítése `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Modell importálása a `dtmi/` mappába

Ha a modell már a JSON-fájlokban van tárolva, a parancs használatával a `dmr-client import` `dtmi/` megfelelő fájlnevekkel adhatja hozzá a mappához:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> A `--local-repo` argumentum használatával megadhatja a helyi tárház gyökérmappa-mappáját.

### <a name="validate-models"></a>Modellek ellenőrzése

A modelleket a `dmr-client validate` paranccsal ellenőrizheti:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> Az érvényesítés a legújabb DTDL-elemző verziót használja annak biztosítására, hogy az összes csatoló kompatibilis legyen a DTDL nyelvi specifikációval.

A külső függőségek érvényesítéséhez a helyi tárházban léteznie kell. A modellek érvényesítéséhez használja a `--repo` kapcsolót a `local` `remote` függőségek feloldására szolgáló vagy mappa megadásához:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Szigorú érvényesítés

A DMR további [követelményeket](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)is tartalmaz, a `stict` jelölővel ellenőrizheti a modelljét:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Győződjön meg arról, hogy a konzol kimenete bármilyen hibaüzenetet tartalmaz.

### <a name="export-models"></a>Modellek exportálása

A modelleket egy adott tárházból (helyi vagy távoli) egyetlen fájlba lehet exportálni egy JSON-tömb használatával:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Következő lépések

A javasolt következő lépés a [IoT Plug and Play architektúra](concepts-architecture.md)áttekintése.
