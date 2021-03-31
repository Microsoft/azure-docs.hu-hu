---
title: A GitHub-tárház szinkronizálása az alkalmazás konfigurációjával
description: A GitHub-műveletek használatával automatikusan frissítheti az alkalmazás konfigurációs példányát, amikor frissíti a GitHub-tárházat.
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1c290032f7a33079b560d3c4cc1fcb9526e70331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762157"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>A GitHub-tárház szinkronizálása az alkalmazás konfigurációjával

Azok a csapatok, amelyek továbbra is a meglévő verziókövetés gyakorlatát szeretnék használni, a GitHub-műveletekkel automatikusan szinkronizálhatják a GitHub-tárházat az alkalmazás konfigurációs tárolójával. Ez lehetővé teszi, hogy a szokásos módon módosításokat hajtson végre a konfigurációs fájlokban, miközben az alkalmazások konfigurációjának előnyeit, például a következő előnyöket biztosítja: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Központosított konfiguráció a kódban kívül <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Konfiguráció frissítése a teljes alkalmazás újbóli üzembe helyezése nélkül <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integráció olyan szolgáltatásokkal, mint a Azure App Service és a functions. 

A GitHub-műveletek [munkafolyamat](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) a GitHub-tárházban automatikus folyamatot határoz meg. Az *Azure app Configuration Sync* művelet elindít egy alkalmazás-konfigurációs példány frissítéseit, amikor módosítja a forrás adattárát. Egy YAML (. YML) fájlt használ a `/.github/workflows/` tárház elérési útjában, hogy meghatározza a lépéseket és a paramétereket. A konfigurációs frissítéseket aktiválhatja az alkalmazás konfigurációs fájljainak leküldésekor, áttekintésekor vagy elágazásakor ugyanúgy, mint az alkalmazás kódjával.

A GitHub [dokumentációja](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions) részletes áttekintést nyújt a GitHub-munkafolyamatokról és-műveletekről. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-műveletek engedélyezése a tárházban
A GitHub-művelet elindításához nyissa meg a tárházat, és válassza a **műveletek** lapot. Válassza az **Új munkafolyamat** lehetőséget, majd **állítson be egy munkafolyamatot**. Végül keresse meg a piactéren az "Azure app Configuration Sync" kifejezést.
> [!div class="mx-imgBorder"]
> ![Válassza a művelet fület](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Az alkalmazás konfigurációjának szinkronizálása művelet kiválasztása](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Konfigurációs fájlok szinkronizálása leküldéses üzenet után
Ez a művelet szinkronizálja az Azure-alkalmazás konfigurációs fájljait, amikor egy módosítást küld a rendszer `appsettings.json` . Amikor egy fejlesztő leküldi a módosítást `appsettings.json` , az alkalmazás konfigurációs szinkronizálási művelete frissíti az alkalmazás konfigurációs példányát az új értékekkel.

A munkafolyamat első szakasza azt *határozza meg,* hogy a művelet a fő ágat tartalmazó *leküldéses* műveletet indít `appsettings.json` el.  A második szakasz azokat a feladatokat sorolja fel, amelyek a művelet elindítása után futnak. A művelet megkeresi a kapcsolódó fájlokat, és frissíti az alkalmazás konfigurációs példányát az adattárban titkosként tárolt kapcsolati sztring használatával.  A GitHubon található titkok használatával kapcsolatos további információkért lásd a [githubról szóló cikket](https://docs.github.com/en/actions/reference/encrypted-secrets) a titkosított titkok létrehozásával és használatával kapcsolatban.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Szigorú szinkronizálás használata
Alapértelmezés szerint a GitHub-művelet nem engedélyezi a szigorú módot, ami azt jelenti, hogy a szinkronizálás csak a konfigurációs fájl kulcs-értékeit adja hozzá az alkalmazás konfigurációs példányához (a kulcs-érték párok nem lesznek törölve). A szigorú üzemmód engedélyezése azt jelenti, hogy a konfigurációs fájlban nem szereplő kulcs-érték párok törlődnek az alkalmazás konfigurációs példányáról, hogy az megfeleljen a konfigurációs fájlnak. Ha több forrásból végez szinkronizálást, vagy a Azure Key Vault alkalmazást használja az alkalmazás konfigurálásával, a konfigurációs beállítások más fájlokból való törlésének elkerüléséhez különböző előtagokat vagy címkéket kell használnia (lásd az alábbi mintákat). 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Több fájl szinkronizálása egy művelettel 

Ha a konfiguráció több fájlban található, az alábbi minta használatával elindíthat egy szinkronizálást a fájl módosításakor. Ez a minta a glob könyvtárat használja https://www.npmjs.com/package/glob . Vegye figyelembe, hogy ha a konfigurációs fájl neve vesszőt tartalmaz, egy fordított perjelet is használhat. 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Szinkronizálás előtag vagy címke alapján
Az előtagok vagy címkék megadása a szinkronizálási műveletben csak az adott készletet fogja szinkronizálni. Ez azért fontos, mert a több fájllal történő szigorú szinkronizálást kell használni. Attól függően, hogy a konfiguráció hogyan van beállítva, egy előtag vagy egy címke társítható minden fájlhoz, majd minden előtag vagy címke külön szinkronizálható, hogy semmi ne legyen felülírva. A különböző környezetekhez általában előtagokat használnak. 

Szinkronizálás előtag alapján: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Szinkronizálás címke szerint: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Dinamikus felirat használata szinkronizáláskor
A következő művelet minden egyes szinkronizáláshoz beszúr egy dinamikus címkét, így biztosítva, hogy minden egyes szinkronizálás egyedi módon azonosítható legyen, és lehetővé tegye a kód módosításának hozzárendelését a konfiguráció módosításaira.

A munkafolyamat első szakasza azt *határozza meg,* hogy a művelet a fő ágat tartalmazó *leküldéses* műveletet indít `appsettings.json` el.  A második szakasz egy olyan feladatot futtat, amely egyedi címkét hoz létre a konfigurációs frissítéshez a véglegesítő kivonat alapján. Ekkor a rendszer frissíti az alkalmazás konfigurációs példányát az új értékekkel és a frissítés egyedi címkéjével.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Azure Key Vault használata a GitHub-művelettel
A AppConfiguration Azure Key Vaultt használó fejlesztőknek két különálló fájlt kell használniuk, jellemzően a appsettings.jst és a secretreferences.js. A secretreferences.jsekkor a Key Vault titkos kulcsának URL-címét fogja tartalmazni.

{"keresési kifejezésként": "{ \" URI \" : \" https://myKeyVault.vault.azure.net/secrets/mySecret "} "}

A GitHub-művelet ezután úgy konfigurálható, hogy szigorú szinkronizálást végezzen a appsettings.json, majd a secretreferences.json nem szigorú szinkronizálást hajt végre. A következő minta elindítja a szinkronizálást a fájl frissítésekor:

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>A GitHub-művelet maximális mélységének használata
A beágyazott JSON-attribútumok alapértelmezett viselkedése a teljes objektum összeolvasztása.  Az alábbi JSON a kulcs-érték párokat határozza meg:

| Kulcs | Érték |
| --- | --- |
| Objektum: belső: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Ha a beágyazott objektum a konfigurációs példánynak leküldhető értékre van kiválasztva, a *mélység* értékével állíthatja le az összeolvasztást a megfelelő mélységben. 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

A 2. mélysége miatt a fenti példa a következő kulcs-érték párokat adja vissza:

| Kulcs | Érték |
| --- | --- |
| Objektum: belső | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>A műveleti bemenetek ismertetése
A bemeneti paraméterek a művelet által a Futtatás során használt adatokat határozzák meg.  Az alábbi táblázat az alkalmazás konfigurációs szinkronizálása által elfogadott bemeneti paramétereket és az egyes értékek várt értékeit tartalmazza.  A GitHub-műveletekkel kapcsolatos művelet-bemenetekkel kapcsolatos további információkért lásd a GitHub [dokumentációját](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> A bemeneti azonosítók a kis-és nagybetűk megkülönböztetése.


| Bemeneti név | Kötelező? | Érték |
|----|----|----|
| configurationFile | Yes | A tárházban található konfigurációs fájl relatív elérési útja.  A glob-minták támogatottak, és több fájlt is tartalmazhatnak. |
| formátumban | Yes | A konfigurációs fájl fájlformátuma.  Az érvényes formátumok a következők: JSON, YAML, Properties. |
| connectionString | Yes | Az alkalmazás konfigurációs példányához tartozó kapcsolatok karakterlánca. A rendszer a GitHub-tárházban titkosként tárolja a kapcsolatok karakterláncát, és csak a titkos nevet kell használni a munkafolyamatban. |
| elválasztó | Yes | A konfigurációs fájl kulcs-érték párokra való összeolvasztásakor használt elválasztó.  Az érvényes értékek a következők:. , ; : - _ __ / |
| előtag | No | A kulcsok elejéhez hozzáadandó előtag. |
| címke | No | Kulcs-érték párok beállításakor használt címke Ha nincs megadva, a rendszer null címkét használ. |
| szigorú | No | Logikai érték, amely meghatározza, hogy engedélyezve van-e a szigorú üzemmód. Az alapértelmezett érték a hamis. |
| mélység | No | A konfigurációs fájl összeolvasztásának maximális mélysége.  A mélységnek pozitív számnak kell lennie.  Az alapértelmezett érték nem rendelkezik maximális mélységgel. |
| tags | No | Megadja a kulcs-érték párokon beállított címkét.  A várt formátum a következő alakzat JSON-objektumának sztringesített formája: {[propertyName: string]: string;} Minden tulajdonságnév-érték címkévé válik. |

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan automatizálható az alkalmazás konfigurációjának szinkronizálása a GitHub művelettel, és hogyan használható az alkalmazás-konfigurációs példány frissítéseinek automatizálására. A következő [cikkből](./concept-app-configuration-event.md)megtudhatja, hogy az Azure-alkalmazások konfigurációjának hogyan működik a kulcs-érték párok változásaira.
