---
title: Az Azure Functions Core Tools használata
description: Megtudhatja, hogyan teheti meg a Azure Functions a parancssorból vagy a terminálból a helyi számítógépen, mielőtt futtatja őket a Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 3ddd84f2f73546b42a3925802b3357df16485488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521441"
---
# <a name="work-with-azure-functions-core-tools"></a>Az Azure Functions Core Tools használata

Az Azure Functions Core Tools segítségével a helyi számítógépén, a parancssorban vagy a terminálban fejleszthet és futtathat függvényeket. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a függvények a teljes functions Runtime használatával hibakeresést végezhetnek a helyi számítógépen. Az Azure-előfizetésben is üzembe helyezhet egy Function alkalmazást.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A functions a helyi számítógépen való fejlesztése és az Azure-ba való közzétételük az alapeszközök használatával az alábbi alapvető lépéseket követi:

> [!div class="checklist"]
> * [Telepítse az alapvető eszközöket és a függőségeket.](#v2)
> * [Function app-projekt létrehozása nyelvspecifikus sablonból.](#create-a-local-functions-project)
> * [Trigger-és kötési bővítmények regisztrálása.](#register-extensions)
> * [Tároló és egyéb kapcsolatok definiálása.](#local-settings-file)
> * [Hozzon létre egy függvényt egy triggerből és egy nyelvspecifikus sablonból.](#create-func)
> * [Futtassa helyileg a függvényt.](#start)
> * [Tegye közzé a projektet az Azure-ban.](#publish)

## <a name="core-tools-versions"></a>A Core Tools verziói

A Azure Functions Core Tools három verziója létezik. A használt verzió a helyi fejlesztési környezettől, a [választott nyelvtől](supported-languages.md)és a szükséges támogatási szinttől függ:

+ [**3. x/2. x verzió**](#v2): [a Azure functions futtatókörnyezet 3. x vagy 2. x verzióját](functions-versions.md)támogatja. Ezek a verziók támogatják a Windows, a [MacOS](?tabs=macos#v2)és a [Linux](?tabs=linux#v2) [rendszert](?tabs=windows#v2), és platform-specifikus csomagkezelő vagy NPM használatával telepíthetők.

+ **1. x verzió**: a Azure functions futtatókörnyezet 1. x verzióját támogatja. Az eszközök ezen verziója csak Windows rendszerű számítógépeken támogatott, és egy NPM- [csomagból](https://www.npmjs.com/package/azure-functions-core-tools)van telepítve.

Egy adott számítógépen csak az alapvető eszközök egyetlen verzióját lehet telepíteni. Ha másként nincs jelezve, a cikkben szereplő példák a 3. x verzióra vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

Azure Functions Core Tools jelenleg az [Azure CLI](/cli/azure/install-azure-cli) -vel vagy az Azure-fiókkal való hitelesítéshez [Azure PowerShell](/powershell/azure/install-az-ps) függ. Ez azt jelenti, hogy az alábbi eszközök egyikét kell telepítenie az Azure-ba való [közzétételhez](#publish) Azure functions Core Toolsról. 

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Azure functions Core Tools] tartalmaz egy olyan verziót, amely a helyi fejlesztési számítógépen futtatható Azure functions futtatókörnyezetet is felhasználja. Emellett parancsokat is biztosít a függvények létrehozásához, az Azure-hoz való kapcsolódáshoz és a functions-projektek üzembe helyezéséhez.

### <a name="version-3x-and-2x"></a><a name="v2"></a>3. x és 2. x verzió

Az eszközök 3. x/2. x verziója a .NET Core-ra épülő Azure Functions futtatókörnyezetet használja. Ez a verzió a .NET Core összes platformján támogatott, beleértve a Windows, a [MacOS](?tabs=macos#v2)és a [Linux](?tabs=linux#v2) [rendszert](?tabs=windows#v2)is. 

> [!IMPORTANT]
> A .NET Core SDK telepítési követelményeit kihagyhatja a [bővítmények]használatával.

# <a name="windows"></a>[Windows](#tab/windows)

A következő lépések a Windows Installer (MSI) használatával telepítik a v3. x alapszintű eszközöket. További információ az alapeszközök v2. x verziójának telepítéséhez szükséges egyéb Package-alapú telepítőről: [alapvető eszközök – fontos](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)információk.

1. Töltse le és futtassa a Core Tools telepítőjét a Windows-verziótól függően:

    - [v3. x – Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (ajánlott. A [Visual Studio Code-hibakereséshez](functions-develop-vs-code.md#debugging-functions-locally) 64 bites szükséges.)
    - [v3. x – Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Ha nem tervezi a [bővítmények](functions-bindings-register.md#extension-bundles)használatát, telepítse a [Windowshoz készült .net Core 3. x SDK](https://dotnet.microsoft.com/download)-t.

# <a name="macos"></a>[macOS](#tab/macos)

A következő lépések a Homebrew-t használják a fő eszközök macOS rendszeren való telepítéséhez.

1. Telepítse a [Homebrew](https://brew.sh/)-t, ha még nincs telepítve.

1. Telepítse a Core Tools csomagot:

    ##### <a name="v3x-recommended"></a>v3. x (ajánlott)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Ha nem tervezi a [bővítmények](functions-bindings-register.md#extension-bundles)használatát, telepítse a [.net Core 3. x SDK-t a MacOS rendszerhez](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Az alábbi [lépések segítségével telepítheti az alapvető](https://wiki.debian.org/Apt) eszközöket az Ubuntu/Debian Linux-disztribúción. Más Linux-disztribúciók esetében tekintse meg az [alapvető eszközök readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)című témakört.

1. Telepítse a Microsoft Package repository GPG kulcsot a csomag integritásának ellenőrzéséhez:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Az apt-források listájának beállítása az APT-frissítés végrehajtása előtt.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Az `/etc/apt/sources.list.d/dotnetdev.list` alábbi listában keresse meg a megfelelő Linux-verziókhoz tartozó karakterláncok egyikét:

    | Linux-disztribúció | Verzió |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20,04    | `focal`     |
    | Ubuntu 19,04    | `disco`     |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mint 18    | `xenial`  |

1. Az APT-forrás frissítésének elindítása:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Core Tools csomagot:

    ##### <a name="v3x-recommended"></a>v3. x (ajánlott)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2. x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Ha nem tervezi a [bővítmények](functions-bindings-register.md#extension-bundles)használatát, telepítse [a .net Core 3. x SDK-t a Linux rendszerhez](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Helyi functions-projekt létrehozása

A functions projekt könyvtára tartalmazza a és a [local.settings.js](#local-settings-file) [host.js](functions-host-json.md) fájlokat, valamint az egyes függvények kódját tartalmazó almappákat is. Ez a könyvtár egyenértékű egy Azure-beli Function alkalmazással. A functions mappa struktúrájával kapcsolatos további tudnivalókért tekintse meg a [Azure functions fejlesztői útmutató](functions-reference.md#folder-structure)című témakört.

A 3. x/2. x verzióhoz az inicializáláskor ki kell választania egy alapértelmezett nyelvet a projekthez. A 3. x/2. x verzióban minden funkció hozzáadva az alapértelmezett nyelvi sablonokat. Az 1. x verzióban az egyes függvények létrehozásakor minden alkalommal meg kell adnia a nyelvet.

A terminál ablakban vagy egy parancssorban futtassa a következő parancsot a projekt és a helyi git-tárház létrehozásához:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> A Java a Maven archetípus használatával hozza létre a helyi functions projektet, valamint az első HTTP által aktivált függvényt. A Java-projekt létrehozásához használja a következő parancsot: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . A Maven archetípusot használó példát a [parancssori](./create-first-function-cli-java.md)útmutatóban talál.  

Amikor megadja a projekt nevét, létrejön egy új, a névvel ellátott mappa, amely inicializálva van. Ellenkező esetben az aktuális mappa inicializálása megtörtént.  
A 3. x/2. x verzióban a parancs futtatásakor ki kell választania egy futtatókörnyezetet a projekthez. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

A fel/le nyílbillentyűk használatával válassza ki a nyelvet, majd nyomja le az ENTER billentyűt. Ha JavaScript-vagy írógéppel-függvények fejlesztését tervezi, válassza a **csomópont** lehetőséget, majd válassza ki a nyelvet. Az írógéppel [néhány további követelményt](functions-reference-node.md#typescript)is tartalmaz. 

A kimenet a következő példához hasonlít egy JavaScript-projekthez:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` a a következő beállításokat támogatja, amelyek a 3. x/2. x-only, kivéve, ha másként jelezzük:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--csx`** | C#-parancsfájlként hozza létre a .NET-függvényeket, amely az 1. x verzió viselkedése. Csak a esetében érvényes `--worker-runtime dotnet` . |
| **`--docker`** | Egy Docker hoz létre egy tárolóhoz a kiválasztott alaprendszerkép használatával `--worker-runtime` . Akkor használja ezt a beállítást, ha egyéni Linux-tárolóba szeretne közzétenni. |
| **`--docker-only`** |  Egy meglévő projekthez hozzáadja a Docker. Ha nincs megadva, vagy a local.settings.jsbe van állítva, a rendszer kéri a munkavégző-futtatókörnyezetet. Akkor használja ezt a beállítást, ha egy meglévő projektet szeretne közzétenni egy egyéni Linux-tárolón. |
| **`--force`** | A projekt inicializálása akkor is, ha vannak meglévő fájlok a projektben. Ez a beállítás felülírja a meglévő fájlokat ugyanazzal a névvel. A Project mappában található egyéb fájlok nem érintettek. |
| **`--language`** | Egy adott nyelvspecifikus projekt inicializálása. Jelenleg támogatott, ha a értékre van `--worker-runtime` állítva `node` . A lehetőségek a következők: `typescript` és `javascript` . Használhatja a vagy a `--worker-runtime javascript` -t is `--worker-runtime typescript` .  |
| **`--managed-dependencies`**  | Felügyelt függőségeket telepít. Jelenleg csak a PowerShell Worker futtatókörnyezet támogatja ezt a funkciót. |
| **`--source-control`** | Meghatározza, hogy a git-tárház létrejött-e. Alapértelmezés szerint a tárház nem jön létre. Amikor `true` létrejön egy adattár. |
| **`--worker-runtime`** | Beállítja a projekt nyelvi futtatókörnyezetét. A támogatott értékek a következők: `csharp` ,, `dotnet` `javascript` , `node` (JavaScript), `powershell` , `python` és `typescript` . A Javához használja a [mavent](functions-reference-java.md#create-java-functions). Ha nincs beállítva, a rendszer arra kéri, hogy válassza ki a futtatókörnyezetet az inicializálás során. |
|
> [!IMPORTANT]
> Alapértelmezés szerint a Core Tools 2. x vagy újabb verziója a .NET-futtatókörnyezethez [C#](functions-dotnet-class-library.md) -csproj (. Ezek a C#-projektek, amelyek a Visual Studióval vagy a Visual Studio Code-ban használhatók, a tesztelés során és az Azure-ba való közzétételkor vannak lefordítva. Ha ehelyett ugyanazt a C# parancsfájl-(. CSX) fájlt szeretné létrehozni és használni, amelyet az 1. x verzióban és a portálon hozott létre, akkor a `--csx` függvények létrehozásakor és telepítésekor meg kell adnia a paramétert.

## <a name="register-extensions"></a>Bővítmények regisztrálása

A HTTP-és időzítő-eseményindítók kivételével a függvények kötései a 2. x vagy újabb verziókban bővítmény-csomagokként vannak implementálva. A HTTP-kötések és az időzítő-eseményindítók nem igényelnek bővítményeket. 

A különböző kiterjesztési csomagok közötti inkompatibilitás csökkentése érdekében a functions lehetővé teszi, hogy a host.jsa Project fájlban hivatkozzon egy bővítményi csomagra. Ha úgy dönt, hogy nem használja a bővítmény-csomagokat, a .NET Core 2. x SDK-t helyileg kell telepítenie, és egy bővítményt kell fenntartania. a csproj a functions-projekttel.  

A 2. x verzióban és a Azure Functions futtatókörnyezeten túl explicit módon regisztrálnia kell a függvényekben használt kötési típusok kiterjesztéseit. Beállíthatja, hogy a kötési bővítmények egyenként legyenek telepítve, vagy hozzáadhat egy bővítményi csomagot a Project-fájl host.js. A bővítményi csomagok több kötési típus használata esetén is megszüntetik a csomagok kompatibilitási problémáinak esélyét. A kötési bővítmények regisztrálásának ajánlott módja. A bővítményi csomagok szintén megszüntetik a .NET Core 2. x SDK telepítésének követelményét. 

### <a name="use-extension-bundles"></a>Bővítmények használata

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

További információ: [Azure functions kötési bővítmények regisztrálása](functions-bindings-register.md#extension-bundles). Ahhoz, hogy a fájlhoz function.jskötéseket vegyen fel, hozzá kell adnia egy bővítményt a host.jshoz.

### <a name="explicitly-install-extensions"></a>Bővítmények explicit telepítése

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan, ha a projekt közzé van téve az Azure-ban. A `--publish-local-settings` [közzétételkor](#publish) használja a kapcsolót, és győződjön meg arról, hogy ezek a beállítások bekerülnek az Azure-beli Function alkalmazásba. Vegye figyelembe, hogy a **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function app Settings értékeit környezeti változókként is beolvashatja a kódban. További információkért tekintse meg az alábbi nyelvspecifikus témakörök környezeti változók című szakaszát:

* [C# előre lefordított](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Ha nincs beállítva érvényes tárolási kapcsolódási karakterlánc [`AzureWebJobsStorage`] , és az emulátor nincs használatban, a következő hibaüzenet jelenik meg:

> A (z) local.settings.jsAzureWebJobsStorage hiányzó értéke. Ez a HTTP-n kívüli összes eseményindító esetében kötelező. Futtathatja a "funkció Azure functionapp Fetch-app-Settings" parancsot, \<functionAppName\> vagy megadhat egy local.settings.js.

### <a name="get-your-storage-connection-strings"></a>A tárolási kapcsolatok karakterláncának beolvasása

Még ha a fejlesztési Microsoft Azure Storage Emulator is használja, érdemes lehet egy tényleges tárolási kapcsolatban is tesztelni. Feltételezve, hogy már [létrehozott egy Storage-fiókot](../storage/common/storage-account-create.md), a következő módokon szerezhet be érvényes tárolási kapcsolatok karakterláncot:

- A [Azure Portal]keresse meg és válassza ki a **Storage-fiókok** lehetőséget. 
  ![Válassza ki a Storage-fiókokat Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Válassza ki a Storage-fiókját, válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások** területen, majd másolja a **kapcsolati karakterlánc** egyik értékét.
  ![A Azure Portalból származó kapcsolatok karakterláncának másolása](./media/functions-run-local/copy-storage-connection-portal.png)

- Az Azure-fiókhoz való kapcsolódáshoz használjon [Azure Storage Explorer](https://storageexplorer.com/) . Az **Explorerben** bontsa ki az előfizetést, bontsa ki a **Storage-fiókok** elemet, válassza ki a Storage-fiókját, és másolja az elsődleges vagy másodlagos kapcsolatok karakterláncát.

  ![A Storage Explorerból származó kapcsolatok karakterláncának másolása](./media/functions-run-local/storage-explorer.png)

+ A projekt gyökerében található alapeszközök használatával töltse le az Azure-beli kapcsolódási karakterláncot az alábbi parancsok egyikével:

  + Egy meglévő Function alkalmazás összes beállításának letöltése:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Egy adott Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Ha még nem jelentkezett be az Azure-ba, a rendszer erre kéri. Ezek a parancsok felülírják a local.settings.jsfájl meglévő beállításait. 

## <a name="create-a-function"></a><a name="create-func"></a>Függvény létrehozása

Függvény létrehozásához futtassa az alábbi parancsot:

```
func new
```

A 3. x/2. x verzióban a futtatáskor a `func new` rendszer kéri, hogy válasszon egy sablont a Function app alapértelmezett nyelvén, majd a rendszer kéri, hogy válasszon egy nevet a függvénynek. Az 1. x verzióban a rendszer azt is kéri, hogy válassza ki a nyelvet.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

A függvény kódja a megadott nevű almappában jön létre, ahogy az a következő üzenetsor-trigger kimenetében látható:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Ezeket a beállításokat a paranccsal is megadhatja a következő argumentumok használatával:

| Argumentum     | Leírás                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (2. x vagy újabb verzió.) Ugyanazokat a C#-szkripteket (. CSX) hozza létre, amelyek az 1. x verzióban és a portálon használatosak. |
| **`--language`**, **`-l`**| A sablon programozási nyelve, például C#, F # vagy JavaScript. Ez a beállítás az 1. x verzióban szükséges. A 2. x vagy újabb verziókban ne használja ezt a kapcsolót, vagy válasszon olyan nyelvet, amely megfelel a munkavégző futtatókörnyezetnek. |
| **`--name`**, **`-n`** | A függvény neve. |
| **`--template`**, **`-t`** | A `func templates list` parancs használatával megtekintheti az elérhető sablonok teljes listáját az egyes támogatott nyelvekhez.   |


Ha például JavaScript HTTP-triggert szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Ha üzenetsor által aktivált függvényt szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Függvények helyi futtatása

Functions-projekt futtatásához futtassa a functions gazdagépet. A gazdagép lehetővé teszi az eseményindítók használatát a projektben lévő összes függvénynél. A Start parancs a projekt nyelvétől függően változhat.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Ezt a parancsot [virtuális környezetben kell futtatni](./create-first-function-cli-python.md).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> A functions Runtime 1. x verziójának a parancs futtatására van szüksége `host` , ahogy az alábbi példában is látható:
>
> ```
> func host start
> ```

`func start` a a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | A Futtatás előtt ne hozzon létre aktuális projektet. Csak a DotNet-projektekhez. Az alapértelmezett érték false (hamis). Az 1. x verzió esetében nem támogatott. |
| **`--cors-credentials`** | A több forrásból származó hitelesített kérelmek (azaz a cookie-k és a hitelesítési fejléc) engedélyezése az 1. x verzió esetében nem támogatott. |
| **`--cors`** | A CORS-eredetek vesszővel tagolt listája, szóközök nélkül. |
| **`--language-worker`** | A nyelv feldolgozójának konfigurálásához szükséges argumentumok. Engedélyezheti például a nyelvi feldolgozó hibakeresését a [hibakeresési port és egyéb szükséges argumentumok](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)megadásával. Az 1. x verzió esetében nem támogatott. |
| **`--cert`** | A titkos kulcsot tartalmazó. pfx-fájl elérési útja. Csak a alkalmazásban használatos `--useHttps` . Az 1. x verzió esetében nem támogatott. |
| **`--password`** | A jelszó vagy egy olyan fájl, amely egy. pfx fájl jelszavát tartalmazza. Csak a alkalmazásban használatos `--cert` . Az 1. x verzió esetében nem támogatott. |
| **`--port`**, **`-p`** | A figyelni kívánt helyi port. Alapértelmezett érték: 7071. |
| **`--pause-on-error`** | A folyamat bezárása előtt szüneteltesse a további adatokat. Csak akkor használható, ha egy integrált fejlesztői környezetből (IDE) indít alapszintű eszközöket.|
| **`--script-root`**, **`--prefix`** | A futtatni vagy telepíteni kívánt Function alkalmazás gyökeréhez tartozó elérési út megadására szolgál. Ez olyan lefordított projektek esetében használatos, amelyek a projektfájlok almappában hozhatók elő. Ha például egy C#-beli függvénytár-projektet hoz létre, akkor a host.jsbe, local.settings.jsbe, és function.jsa fájlokon egy, a (z) elérési úttal rendelkező *gyökér* almappában jön létre `MyProject/bin/Debug/netstandard2.0` . Ebben az esetben állítsa az előtagot a következőre: `--script-root MyProject/bin/Debug/netstandard2.0` . Ez a Function alkalmazás gyökere, ha az Azure-ban fut. |
| **`--timeout`**, **`-t`** | A függvények gazdagépének időtúllépése másodpercben. Alapértelmezett: 20 másodperc.|
| **`--useHttps`** | Kötés helyett a következőhöz: `https://localhost:{port}` `http://localhost:{port}` . Alapértelmezés szerint ez a beállítás megbízható tanúsítványt hoz létre a számítógépen.|

A functions-gazdagép indításakor a HTTP-triggert függvények URL-címét adja meg:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Helyileg futtatva az engedélyezés nem kényszeríti ki a HTTP-végpontokat. Ez azt jelenti, hogy az összes helyi HTTP-kérelem a következőképpen van kezelve: `authLevel = "anonymous"` . További információkért lásd a http- [kötést ismertető cikket](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Tesztelési adat átadása egy függvénynek

A függvények helyi teszteléséhez [indítsa el a functions gazdagépet](#start) , és hívja meg a végpontokat a helyi kiszolgálón a HTTP-kérelmek használatával. A hívott végpont a függvény típusától függ.

>[!NOTE]
> A jelen témakörben szereplő példák a cURL eszközzel küldenek HTTP-kéréseket a terminálról vagy egy parancssorból. Az Ön által választott eszköz használatával HTTP-kéréseket küldhet a helyi kiszolgálónak. A cURL eszköz alapértelmezés szerint a Linux-alapú rendszereken és a Windows 10-es Build 17063-es és újabb verzióiban érhető el. A régebbi Windows rendszeren először le kell töltenie és telepítenie kell a [curl eszközt](https://curl.haxx.se/).

A tesztelési funkciókkal kapcsolatos általánosabb információkért lásd: [stratégiák a kód teszteléséhez Azure functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>A HTTP és a webhook által aktivált függvények

A következő végpontot hívja meg helyileg a HTTP és a webhook által aktivált függvények futtatásához:

```
http://localhost:{port}/api/{function_name}
```

Győződjön meg arról, hogy ugyanazt a kiszolgálónevet és portot használja, amelyet a functions-gazdagép figyel. Ez a függvény gazdagépének indításakor generált kimenetben jelenik meg. Ezt az URL-címet bármely, az trigger által támogatott HTTP-módszerrel meghívhatja.

A következő cURL-parancs elindítja a gyors üzembe helyezési `MyHttpTrigger` függvényt egy Get kérelemből a lekérdezési karakterláncban átadott _Name_ paraméterrel.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

A következő példa ugyanazt a függvényt _hívja meg a_ kérelem törzsében a post kérelem átadásakor:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

A lekérdezési karakterláncban található adatok beolvasása egy böngészőből is elvégezhető. Minden más HTTP-módszer esetében a cURL, a Hegedűs, a Poster vagy egy hasonló HTTP-tesztelési eszközt kell használnia.

#### <a name="non-http-triggered-functions"></a>Nem HTTP által aktivált függvények

A HTTP-triggerek, a webhookok és a Event Grid-eseményindítók kivételével a függvények helyi teszteléséhez az adminisztrációs végpont meghívásával végezheti el a funkciókat. Ha ezt a végpontot egy HTTP POST-kérelemmel hívja meg a helyi kiszolgálón, a függvényt indítja el. 

Event Grid aktivált függvények helyi teszteléséhez tekintse meg [a local Testing with Viewer Web App](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)című témakört.

Szükség esetén a POST kérelem törzsében is elvégezheti a tesztelési célú adatellenőrzést. Ez a funkció hasonló a Azure Portal **teszt** lapjához.

A nem HTTP-függvények elindításához a következő rendszergazdai végpontot kell meghívni:

```
http://localhost:{port}/admin/functions/{function_name}
```

Ahhoz, hogy egy függvény rendszergazdai végpontján át lehessen adni a tesztelési feladatokat, meg kell adnia a POST kérelem üzenet törzsében lévő összes adatát. Az üzenet törzsének a következő JSON-formátummal kell rendelkeznie:

```JSON
{
    "input": "<trigger_input>"
}
```

Az `<trigger_input>` érték a függvény által várt formátumú értékeket tartalmaz. A következő cURL-példa egy `QueueTriggerJS` függvényre való bejegyzés. Ebben az esetben a bemenet egy karakterlánc, amely egyenértékű a várólistában várható üzenettel.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>A `func run` parancs használata (csak 1. x verzió)

>[!IMPORTANT]
> A `func run` parancs csak az eszközök 1. x verziójában támogatott. További információ: [Azure functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md).

Az 1. x verzióban a függvényt közvetlenül is meghívhatja `func run <FunctionName>` a függvénnyel, és a függvény bemeneti adatokat is megadhat. Ez a parancs hasonló egy függvény futtatásához a Azure Portal **teszt** lapján.

`func run` a a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Beágyazott tartalom. |
| **`--debug`**, **`-d`** | A függvény futtatása előtt csatoljon egy hibakeresőt a gazdagéphez.|
| **`--timeout`**, **`-t`** | Várakozási idő (másodpercben), amíg a helyi függvények gazdagépe nem áll készen.|
| **`--file`**, **`-f`** | A tartalomként használandó fájlnév.|
| **`--no-interactive`** | A nem kéri a bevitelt. Automatizálási forgatókönyvek esetén hasznos.|

Ha például egy HTTP-triggert használó függvényt szeretne meghívni, és tartalmat kell átadnia, futtassa a következő parancsot:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Közzététel az Azure-ban

A Azure Functions Core Tools kétféle telepítési típust támogat: a Function-projektfájlok közvetlen üzembe helyezése a Function alkalmazásban a [zip üzembe helyezésével](functions-deployment-technologies.md#zip-deploy) és az [Egyéni Docker-tároló üzembe helyezésével](functions-deployment-technologies.md#docker-container). Már létre kell hoznia [egy Function alkalmazást az Azure-előfizetésében](functions-cli-samples.md#create), amelyre telepíteni fogja a kódot. A fordítást igénylő projekteket úgy kell felépíteni, hogy a bináris fájlok üzembe helyezhetők legyenek.

>[!IMPORTANT]
>Az Azure [CLI](/cli/azure/install-azure-cli) -vel vagy a [Azure PowerShell](/powershell/azure/install-az-ps) helyileg kell telepíteni az Azure-ba az alapvető eszközökről.  

A Project mappa olyan nyelvspecifikus fájlokat és címtárakat tartalmazhat, amelyeket nem lehet közzétenni. A kizárt elemek a legfelső szintű projekt mappában található. funcignore fájlban szerepelnek.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Projektfájlok központi telepítése

Ha a helyi kódot egy Azure-beli Function alkalmazásban szeretné közzétenni, használja a következő `publish` parancsot:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> A Java a Maven használatával teszi közzé a helyi projektet az Azure-ban. Használja az alábbi parancsot az Azure-ban való közzétételhez: `mvn azure-functions:deploy` . Az Azure-erőforrások a kezdeti üzembe helyezés során jönnek létre.

Ez a parancs egy meglévő Function alkalmazásba tesz közzé az Azure-ban. Hibaüzenet jelenik meg, ha olyan közzétételi kísérletet próbál végrehajtani, `<FunctionAppName>` amely nem létezik az előfizetésben. Ha szeretné megtudni, hogyan hozhat létre egy Function alkalmazást a parancssorból vagy a terminálból az Azure CLI vagy a Azure PowerShell használatával, tekintse meg [a Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz](./scripts/functions-cli-create-serverless.md)című témakört. Alapértelmezés szerint ez a parancs [távoli buildet](functions-deployment-technologies.md#remote-build) használ, és üzembe helyezi az alkalmazást [a központi telepítési csomagból való futtatásra](run-functions-from-deployment-package.md). Az ajánlott telepítési mód letiltásához használja a `--nozip` kapcsolót.

>[!IMPORTANT]
> Ha a Azure Portalban hoz létre egy Function alkalmazást, a függvény alapértelmezés szerint a Function Runtime 3. x verzióját használja. Ha azt szeretné, hogy a Function app a futtatókörnyezet 1. x verzióját használja, kövesse az [1. x verzió futtatásának](functions-versions.md#creating-1x-apps)utasításait.
> Egy meglévő függvényt tartalmazó Function alkalmazás futásidejű verziója nem módosítható.

A következő közzétételi beállítások minden verzióra érvényesek:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  A beállítások közzététele az Azure-ban local.settings.js, ha a beállítás már létezik, a rendszer megkéri a felülírását. Ha a Microsoft Azure Storage Emulator használja, először módosítsa az alkalmazás beállításait egy [tényleges tárolási kapcsolatban](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | A használatakor a rendszer letiltja az Alkalmazásbeállítások felülírására vonatkozó kérést `--publish-local-settings -i` .|

A következő közzétételi beállítások csak a 2. x vagy újabb verziókban támogatottak:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Csak a beállítások közzététele és a tartalom kihagyása. Az alapértelmezett érték a prompt. |
|**`--list-ignored-files`** | A közzététel során figyelmen kívül hagyott fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--list-included-files`** | A közzétett fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--nozip`** | Kikapcsolja az alapértelmezett `Run-From-Package` üzemmódot. |
| **`--build-native-deps`** | Kihagyja a generálás. Wheels mappát a Python-függvények alkalmazásainak közzétételekor. |
| **`--build`**, **`-b`** | Build műveletet hajt végre Linux-Function alkalmazás telepítésekor. A következőket fogadja el: `remote` és `local` . |
| **`--additional-packages`** | A natív függőségek kiépítésekor telepítendő csomagok listája. Példa: `python3-dev libevent-dev`. |
| **`--force`** | Bizonyos helyzetekben figyelmen kívül hagyhatja a közzététel előtti ellenőrzést. |
| **`--csx`** | C# parancsfájl-(. CSX) projekt közzététele. |
| **`--no-build`** | A projekt nincs felépítve a közzététel során. A Python esetében `pip install` nincs elvégezve. |
| **`--dotnet-cli-params`** | A lefordított C#-(. csproj) függvények közzétételekor a Core Tools a "DotNet Build-output bin/publish" metódust hívja meg. Az erre átadott paraméterek a parancssorhoz lesznek hozzáfűzve. |

### <a name="deploy-custom-container"></a>Egyéni tároló üzembe helyezése

Azure Functions lehetővé teszi a Function projekt üzembe helyezését egy [Egyéni Docker-tárolóban](functions-deployment-technologies.md#docker-container). További információkért lásd: [függvény létrehozása Linuxon egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md). Az egyéni tárolók Docker kell rendelkezniük. Ha Docker szeretne létrehozni egy alkalmazást, használja a--Docker kapcsolót `func init` .

```
func deploy
```

A következő egyéni tároló üzembe helyezési lehetőségei érhetők el:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Annak a Docker-beállításjegyzéknek a neve, amelyre az aktuális felhasználó bejelentkezett. |
| **`--platform`** | Üzemeltetési platform a Function alkalmazáshoz. Az érvényes beállítások a következők `kubernetes` |
| **`--name`** | Function alkalmazás neve. |
| **`--max`**  | Igény szerint beállíthatja a alkalmazásban telepítendő Function app-példányok maximális számát. |
| **`--min`**  | Igény szerint beállíthatja az alkalmazásban telepítendő Function app-példányok minimális számát. |
| **`--config`** | Egy opcionális telepítési konfigurációs fájl beállítása. |

## <a name="monitoring-functions"></a>Figyelési függvények

A függvények végrehajtásának ajánlott figyelése az Azure Application Insights integrálásával történik. A végrehajtási naplókat a helyi számítógépre is továbbíthatja. További információért lásd: [Azure functions figyelése](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights integráció

Application Insights integrációt engedélyezni kell, amikor létrehozza a Function alkalmazást az Azure-ban. Ha a Function alkalmazás valamilyen okból nem kapcsolódik Application Insights-példányhoz, egyszerűen elvégezheti ezt az integrációt a Azure Portal. További információ: [Application Insights integráció engedélyezése](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Folyamatos átviteli naplók engedélyezése

Megtekintheti a függvények által a helyi számítógépen lévő parancssori munkamenetben létrehozott naplófájlok streamjét. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Az ilyen típusú folyamatos átviteli naplókhoz a Application Insights integrációjának engedélyezése szükséges a Function alkalmazáshoz.   


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan fejlesztheti, tesztelheti és teheti közzé Azure Functions a Azure Functions Core Tools [Microsoft Learning modul](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) használatával Azure functions Core Tools [nyílt forráskódú, és a githubon üzemeltethető](https://github.com/azure/azure-functions-cli).  
Egy hiba vagy szolgáltatás kérésének megkereséséhez [Nyisson meg egy GitHub-problémát](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[kiterjesztési csomagok]: functions-bindings-register.md#extension-bundles
