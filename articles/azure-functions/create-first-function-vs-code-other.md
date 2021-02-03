---
title: Függvény létrehozása a Go-ban vagy a Rustban a Visual Studio Code-Azure Functions használatával
description: Megtudhatja, hogyan hozhat létre go-funkciót Azure Functions egyéni kezelőként, majd a Visual Studio Code-ban a Azure Functions bővítménnyel közzéteheti a helyi projektet a kiszolgáló nélküli üzemeltetésben Azure Functions.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 4f2e0b30c4bf5e6c4629fc63f3125e5ddda70ad2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493658"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Gyors útmutató: go-vagy Rust-függvény létrehozása az Azure-ban a Visual Studio Code használatával

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy [Egyéni kezelői](functions-custom-handlers.md) függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

Az egyéni kezelők bármilyen nyelven vagy futtatókörnyezetben létrehozhatnak függvényeket egy HTTP-kiszolgálói folyamat futtatásával. Ez a cikk a [Go](create-first-function-vs-code-other.md?tabs=go) és a [Rust](create-first-function-vs-code-other.md?tabs=rust)együttes használatát is támogatja.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

# <a name="go"></a>[Ugrás](#tab/go)

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

+ A [Azure functions Core Tools](./functions-run-local.md#v2) 3. x verzió. A `func --version` parancs használatával győződjön meg arról, hogy megfelelően van-e telepítve.

+ [Ugrás](https://golang.org/doc/install), a legújabb verzió ajánlott. A `go version` parancs használatával vizsgálja meg a verziót.

# <a name="rust"></a>[Rust](#tab/rust)

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

+ A [Azure functions Core Tools](./functions-run-local.md#v2) 3. x verzió. A `func --version` parancs használatával győződjön meg arról, hogy megfelelően van-e telepítve.

+ Rozsda toolchain a [rustup](https://www.rust-lang.org/tools/install)használatával. A `rustc --version` parancs használatával vizsgálja meg a verziót.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions egyéni kezelői projektet. A cikk későbbi részében közzéteszi a függvény kódját az Azure-ban.

1. A műveleti sávban válassza ki az Azure ikont, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `Custom` elemet.

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP trigger függvénnyel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>A függvény létrehozása és felépítése

A *HttpExample* mappában található fájl *function.js* deklarál egy http trigger függvényt. A függvényt egy kezelő hozzáadásával és egy végrehajtható fájlba való fordításával végezheti el.

# <a name="go"></a>[Ugrás](#tab/go)

1. Új fájl létrehozásához nyomja le a <kbd>Ctrl + n</kbd> billentyűkombinációt (<kbd>cmd + n</kbd> MacOS rendszeren). Mentse *kezelőként. Nyissa* meg a Function App Root (ugyanabban a mappában, mint *host.json*).

1. A *kezelő. go* fájlban adja hozzá a következő kódot, és mentse a fájlt. Ez az Ön go Custom Handler.

    ```go
    package main

    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )

    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }

    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Nyomja le a <kbd>CTRL + SHIFT +</kbd> billentyűkombinációt, vagy válassza az *új terminál* lehetőséget a *terminál* menüjéből egy új, integrált terminálnak a vs Code-ban való megnyitásához.

1. Fordítsa le az egyéni kezelőt a következő parancs használatával. `handler` `handler.exe` A (Windows rendszeren) nevű végrehajtható fájl kimenete a Function App Root mappában található.

    ```bash
    go build handler.go
    ```

    ![VS Code – Build Go – egyéni kezelő](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Nyomja le a <kbd>CTRL + SHIFT +</kbd> billentyűkombinációt, vagy válassza az *új terminál* lehetőséget a *terminál* menüjéből egy új, integrált terminálnak a vs Code-ban való megnyitásához.

1. A Function alkalmazás gyökérkönyvtárában (a *host.jst* tartalmazó mappában) inicializálja a nevű Rust-projektet `handler` .

    ```bash
    cargo init --name handler
    ```

1. A *toml*-ben adja hozzá a rövid útmutató elvégzéséhez szükséges alábbi függőségeket. A példa a [Warp](https://docs.rs/warp/) Web Server keretrendszert használja.

    ```toml
    [dependencies]
    warp = "0.2"
    tokio = { version = "0.2", features = ["full"] }
    ```

1. A *src/Main. rs* fájlban adja hozzá a következő kódot, és mentse a fájlt. Ez a rozsda egyéni kezelője.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Lefordíthatja az egyéni kezelőhöz tartozó bináris fájlt. `handler` `handler.exe` A (Windows rendszeren) nevű végrehajtható fájl kimenete a Function App Root mappában található.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code – a rozsda egyéni kezelőjének létrehozása](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>A Function alkalmazás konfigurálása

A függvény gazdagépét úgy kell konfigurálni, hogy az elindulásakor az egyéni kezelő bináris fájlját futtassa.

1. *host.jsmegnyitása a* következőn:.

1. A `customHandler.description` (z) szakaszban állítsa be a `defaultExecutablePath` értéket `handler` (Windows rendszeren állítsa be `handler.exe` ).

1. A `customHandler` szakaszban adjon hozzá egy nevű tulajdonságot, `enableForwardingHttpRequest` és állítsa be a értékét a következőre: `true` . A csak HTTP-triggerből álló függvények esetében ez a beállítás leegyszerűsíti a programozást azáltal, hogy az egyéni kezelői [kérelem adattartalma](functions-custom-handlers.md#request-payload)helyett egy tipikus http-kérelemmel dolgozik.

1. Erősítse meg, hogy a `customHandler` szakasz a következő példához hasonlít. Mentse a fájlt.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

A Function alkalmazás úgy van konfigurálva, hogy elindítsa az egyéni kezelő végrehajtható fájlját.

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Ezt a projektet a helyi fejlesztési számítógépen is futtathatja az Azure-ban való közzététel előtt.

1. Az integrált terminálon indítsa el a Function alkalmazást Azure Functions Core Tools használatával.

    ```bash
    func start
    ```

1. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre egy GET kérelem végrehajtásához, amely tartalmazza a `?name=Functions` lekérdezési karakterláncot.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki egy böngészőben:

    ![Böngésző – a localhost példa kimenete](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![A feladat gazdagépének indítása – VS Code-terminál kimenete](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához.

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Az Azure-hoz készült egyéni kezelő fordítása

Ebben a szakaszban egy Linux rendszerű Function-alkalmazásban teszi közzé a projektjét az Azure-ban. A legtöbb esetben újra kell fordítania a bináris fájlt, és úgy kell beállítania a konfigurációt, hogy az megfeleljen a célként megadott platformnak, mielőtt közzéteszi azt az Azure-ban.

# <a name="go"></a>[Ugrás](#tab/go)

1. Az integrált terminálon fordítsuk le a kezelőt a Linux/x64 rendszerhez. A rendszer létrehoz egy nevű bináris fájlt `handler` a Function alkalmazás gyökerében.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build hello.go
    ```

    Módosítsa a `defaultExecutablePath` t *host.jsa* verzióról a verzióra `handler.exe` `handler` . Ez arra utasítja a Function alkalmazást, hogy futtassa a Linux bináris fájlt.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Hozzon létre egy fájlt a *. Cargo/config* fájlban. Adja hozzá a következő tartalmakat, és mentse a fájlt.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. Az integrált terminálon fordítsuk le a kezelőt a Linux/x64 rendszerhez. Létrejön egy nevű bináris fájl `handler` . Másolja a függvény alkalmazás gyökerébe.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Ha Windows rendszert használ, módosítsa a alkalmazást `defaultExecutablePath` *host.jsa* rendszerről a verzióra `handler.exe` `handler` . Ez arra utasítja a Function alkalmazást, hogy futtassa a Linux bináris fájlt.

1. Adja hozzá a következő sort a *. funcignore* fájlhoz:

    ```
    target
    ```

    Ez megakadályozza a *célmappa* tartalmának közzétételét.

---

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 


1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Mappa kiválasztása**: válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ez nem jelenik meg, ha már nyitva van egy érvényes Function alkalmazás.

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `+ Create new Function App (advanced)` elemet. 
    
        > [!IMPORTANT]
        > A `advanced` beállítással kiválaszthatja azt az operációs rendszert, amelyen a Function alkalmazás fut az Azure-ban, ami ebben az esetben a Linux.

        ![VS Code – válassza a speciális új függvény létrehozása alkalmazást](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen.

    + **Válassza ki a futtatókörnyezetet**: válassza a lehetőséget `Custom Handler` .

    + **Válasszon ki egy operációs rendszert**: válassza a lehetőséget `Linux` .

    + **Válasszon egy üzemeltetési csomagot**: válassza a lehetőséget `Consumption` .

    + **Válasszon erőforráscsoportot**: válassza a lehetőséget `+ Create new resource group` . Adja meg az erőforráscsoport nevét. Ennek a névnek egyedinek kell lennie az Azure-előfizetésen belül. A parancssorban javasolt nevet használhatja.

    + **Válassza ki a Storage-fiókot**: válassza a lehetőséget `+ Create new storage account` . Ennek a névnek globálisan egyedinek kell lennie az Azure-on belül. A parancssorban javasolt nevet használhatja.

    + **Válasszon ki egy Application Insights erőforrást**: válassza a lehetőséget `+ Create Application Insights resource` . Ennek a névnek globálisan egyedinek kell lennie az Azure-on belül. A parancssorban javasolt nevet használhatja.

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . A bővítmény megjeleníti az egyes erőforrások állapotát az értesítési terület Azure-ban való létrehozásakor.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Értesítés az Azure-erőforrások létrehozásáról":::

1. Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 

4. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók az egyéni kezelők Azure Functions](functions-custom-handlers.md)
