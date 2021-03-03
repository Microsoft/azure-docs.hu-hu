---
title: Az eszköz modellek tárházának fogalmai | Microsoft Docs
description: Megoldás fejlesztőként vagy informatikai szakemberként megismerheti az eszköz modellek tárházának alapvető fogalmait.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1a58a2f69b9c6c6742c4b9daf32dd0e13341aac1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742143"
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

Ezen felületek programozott eléréséhez át kell alakítania egy DTMI egy relatív elérési útra, amelynek segítségével lekérdezheti a nyilvános végpontot.

A DTMI abszolút elérési útra való átalakításához használja a következő `DtmiToPath` függvényt `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Az eredményül kapott elérési úttal és a tárház alap URL-címével a következő felületet tudjuk beszerezni:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

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
