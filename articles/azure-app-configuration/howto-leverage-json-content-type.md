---
title: JSON tartalomtípus használata kulcsértékekhez
titleSuffix: Azure App Configuration
description: Megtudhatja, hogyan használhatja a kulcs-értékekhez a JSON-tartalom típusát
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 19de46bc87b72ada221c63e36e87d0545304d344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122153"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>A tartalom típusának kihasználása JSON-kulcsok tárolásához az alkalmazás konfigurációjában

Az adatok az alkalmazás konfigurációjában kulcs-értékként tárolódnak, ahol a rendszer alapértelmezés szerint karakterlánc-típusként kezeli az értékeket. Egyéni típust azonban megadhat az egyes kulcs-értékekhez társított Content-Type tulajdonság kihasználása mellett, így megőrizheti az adatai eredeti típusát, vagy az alkalmazás a Content-Type alapján eltérően viselkedik.


## <a name="overview"></a>Áttekintés

Az alkalmazás konfigurálásakor a JSON-adathordozó típusát használhatja a kulcs-értékek tartalomtípusa, hogy a következő előnyöket használja:
- **Egyszerűbb adatkezelés**: a kulcs-értékek, például a tömbök kezelése sokkal egyszerűbb lesz a Azure Portalban.
- **Bővített adatexportálás**: az adatexportálás során az egyszerű típusok, a tömbök és a JSON-objektumok is megmaradnak.
- **Natív támogatás az alkalmazás-konfigurációs szolgáltatónál**: a JSON-tartalommal rendelkező kulcs-értékek jól működnek, ha az alkalmazásban az alkalmazás konfigurációs szolgáltatói kódtárai használják azokat.

#### <a name="valid-json-content-type"></a>Érvényes JSON-tartalom – típus

Az [itt](https://www.iana.org/assignments/media-types/media-types.xhtml)definiált adathordozó-típusok az egyes kulcs-értékekhez társított tartalomtípusokhoz rendelhetők.
Az adathordozó típusa egy típusból és egy altípusból áll. Ha a típus `application` és az altípus (vagy utótag) `json` , a rendszer az adathordozó típusát érvényes JSON Content-Type értéknek tekinti.
Néhány példa a JSON-tartalmak érvényes típusaira:

- application/json
- alkalmazás/tevékenység + JSON
- Application/vnd. foobar + JSON; charset = UTF-8

#### <a name="valid-json-values"></a>Érvényes JSON-értékek

Ha a kulcs-érték JSON Content-Type értékű, az értéknek érvényes JSON formátumúnak kell lennie ahhoz, hogy az ügyfelek helyesen dolgozzák fel. Ellenkező esetben előfordulhat, hogy az ügyfelek meghibásodnak vagy visszaesnek, és a karakterlánc-formátumként kezelik.
Néhány példa az érvényes JSON-értékekre:

- "Gipsz Jakab"
- 723
- hamis
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"Targeting": {"default": igaz, "level": "információ"}}}

> [!NOTE]
> A cikk további részében minden olyan kulcs-érték az alkalmazás konfigurációjában, amely érvényes JSON-tartalomtípussal rendelkezik, és az érvényes JSON-érték **JSON kulcs-érték** lesz. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:
> [!div class="checklist"]
> * JSON-kulcs létrehozása – értékek az alkalmazás konfigurációjában.
> * JSON-kulcs importálása – egy JSON-fájl értékei.
> * JSON-kulcs exportálása – értékek egy JSON-fájlba.
> * JSON-kulcsok felhasználása az alkalmazásokban.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Az oktatóanyaghoz az Azure CLI 2.10.0 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>JSON-kulcs létrehozása – értékek az alkalmazás konfigurációjában

JSON-kulcs – az értékek a Azure Portal, az Azure CLI használatával vagy egy JSON-fájlból történő importálással hozhatók létre. Ebben a szakaszban útmutatást talál ugyanazon JSON-kulcsok létrehozásához mind a három módszer használatával.

### <a name="create-json-key-values-using-azure-portal"></a>JSON-kulcs létrehozása – értékek Azure Portal használatával

Tallózással keresse meg az alkalmazás konfigurációs tárolóját , és  >    >  a következő kulcs-érték párok hozzáadásához válassza a konfigurációs Explorer Create **Key-Value** elemet:

| Kulcs | Érték | Tartalom típusa |
|---|---|---|
| Beállítások: BackgroundColor | Zöld | application/json |
| Beállítások: FontSize | 24 | application/json |
| Beállítások: UseDefaultRouting | hamis | application/json |
| Beállítások: BlockedUsers | null | application/json |
| Beállítások: ReleaseDate | "2020-08-04T12:34:56.789 Z" | application/json |
| Beállítások: RolloutPercentage | [25, 50, 75100] | application/json |
| Beállítások: naplózás | {"Test": {"szint": "debug"}, "Prod": {"level": "figyelmeztetés"}} | application/json |

Hagyja üresen a **feliratot** , és kattintson az **alkalmaz** gombra.

### <a name="create-json-key-values-using-azure-cli"></a>JSON-kulcs létrehozása – értékek az Azure CLI használatával

A következő parancsok a JSON-kulcsokat fogják létrehozni az alkalmazás konfigurációs tárolójában. A helyére írja `<appconfig_name>` be az alkalmazás konfigurációs tárolójának nevét.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Ha az Azure CLI-t használja, vagy a Azure Cloud Shell JSON kulcs-értékek létrehozásához, a megadott értéknek Escape-JSON-karakterláncnak kell lennie.

### <a name="import-json-key-values-from-a-file"></a>JSON-kulcs importálása-értékek egy fájlból

Hozzon létre egy nevű JSON `Import.json` -fájlt a következő tartalommal, és importálja kulcs-értékként az alkalmazás-konfigurációba:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> Az `--depth` argumentumot a rendszer a hierarchikus adatok egy fájlból a kulcs-érték párokba való összeolvasztására használja. Ebben az oktatóanyagban részletesen ismertetjük, hogy az alkalmazás konfigurációjában hogyan tárolhatók a JSON-objektumok. Ha a mélység nincs megadva, a JSON-objektumok alapértelmezés szerint a legmélyebb szintre lesznek lelapulva.

A létrehozott JSON-kulcsnak a következőhöz hasonlóan kell kinéznie az alkalmazás konfigurációjában:

![JSON-kulcsokat tartalmazó konfigurációs tár – értékek](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>JSON-kulcs exportálása – értékek fájlba

A JSON-kulcsok használatának egyik legfőbb előnye, hogy az értékek eredeti adattípusának megőrzése az exportálás során. Ha az alkalmazás konfigurációjában nem szerepel JSON-tartalomtípus, az érték karakterláncként lesz kezelve. 

Tekintse át ezeket a kulcsokat JSON Content-Type típusú adatok nélkül:

| Kulcs | Érték | Tartalom típusa |
|---|---|---|
| Beállítások: FontSize | 24 | |
| Beállítások: UseDefaultRouting | hamis | |

Ha ezeket a kulcs-értékeket egy JSON-fájlba exportálja, az értékek karakterláncként lesznek exportálva:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Ha azonban egy fájlba exportálja a JSON kulcs-értékeket, az összes érték megőrzi az eredeti adattípust. Ennek ellenőrzéséhez exportálja a kulcs-értékeit az alkalmazás konfigurációjától egy JSON-fájlba. Látni fogja, hogy az exportált fájl tartalma megegyezik a `Import.json` korábban importált fájllal.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Ha az alkalmazás konfigurációs tárolója tartalmaz néhány, JSON-tartalomtípus nélküli kulcs-értéket, akkor a rendszer a karakterlánc formátumú fájlba is exportálja őket. Ha csak a JSON-kulcs-értékeket kívánja exportálni, rendeljen hozzá egy egyedi címkét vagy előtagot a JSON-kulcshoz, és használjon címkét vagy előtag-szűrést az exportálás során.


## <a name="consuming-json-key-values-in-applications"></a>JSON-kulcs felhasználása – értékek az alkalmazásokban

Az alkalmazásban a JSON-kulcsok felhasználásának legegyszerűbb módja az alkalmazások konfigurációs szolgáltatói könyvtárain keresztül. A szolgáltatói kódtárak nem szükségesek az alkalmazásban található JSON-kulcsok speciális kezelésére. A rendszer elemzi és átalakítja ezeket az alkalmazásokat az alkalmazás natív konfigurációjának megfelelően.

Ha például a következő kulcs-érték szerepel az alkalmazás konfigurációjában:

| Kulcs | Érték | Tartalom típusa |
|---|---|---|
| Beállítások | {"FontSize": 24, "UseDefaultRouting": false} | application/json |

A .NET-alkalmazás konfigurációja a következő kulcs-értékekkel fog rendelkezni:

| Kulcs | Érték |
|---|---|
| Beállítások: FontSize | 24 |
| Beállítások: UseDefaultRouting | hamis |

Az új kulcsokat közvetlenül is elérheti, vagy dönthet úgy, hogy a [konfigurációs értékeket .net-objektumok példányaihoz köti](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern).


> [!Important]
> Natív támogatás a JSON-kulcsokhoz – az értékek a .NET-konfiguráció szolgáltatójának 4.0.0 (vagy újabb verzió) érhetők el. További részletekért tekintse meg a [*következő lépések*](#next-steps) szakaszt.

Ha az SDK-t vagy REST API használja az alkalmazás konfigurációjában található kulcs-érték olvasására a Content-Type alapján, az alkalmazás felelős a JSON-kulcs értékének elemzéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan dolgozhat a JSON-kulcsokkal az alkalmazás konfigurációs tárolójában, hozzon létre egy alkalmazást a következő kulcs-értékek fogyasztásához:

* [ASP.NET Core rövid útmutató](./quickstart-aspnet-core-app.md)
    * Előfeltétel: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 vagy újabb.

* [.NET Core gyors útmutató](./quickstart-dotnet-core-app.md)
    * Előfeltétel: [Microsoft.Extensions.Configszülő. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) Package v 4.0.0 vagy újabb.
