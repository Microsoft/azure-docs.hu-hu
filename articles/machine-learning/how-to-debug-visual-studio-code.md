---
title: Interaktív hibakeresés az Visual Studio Code-ban
titleSuffix: Azure Machine Learning
description: Interaktív hibakeresés Azure Machine Learning kód, folyamatok és üzembe helyezések során a Visual Studio Code használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813865"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktív hibakeresés az Visual Studio Code-ban



Megtudhatja, hogyan lehet interaktív módon hibakeresést végezni Azure Machine Learning kísérletekben, folyamatokban és üzembe helyezésekben a Visual Studio Code (VS Code) és a [debugpy használatával.](https://github.com/microsoft/debugpy/)

## <a name="run-and-debug-experiments-locally"></a>Kísérletek helyi futtatása és hibakeresése

A Azure Machine Learning a gépi tanulási kísérletek érvényesítéséhez, futtatásához és hibakereséshez, mielőtt beküldi őket a felhőbe.

### <a name="prerequisites"></a>Előfeltételek

* Azure Machine Learning VS Code-bővítmény (előzetes verzió). További információ: [Set up Azure Machine Learning VS Code extension](tutorial-setup-vscode-extension.md)..
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop Machez és Windowshoz
  * Docker Engine Linuxhoz.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Windows rendszeren győződjön meg arról, hogy a Docker linuxos [tárolók használatára van konfigurálva.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

> [!TIP]
> Windows esetén , bár nem kötelező, erősen ajánlott a Docker használata a [Linuxos Windows-alrendszer (WSL) 2-es verzióval.](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)

> [!IMPORTANT]
> A kísérlet helyi futtatása előtt ellenőrizze, hogy fut-e a Docker.

### <a name="debug-experiment-locally"></a>Kísérlet helyi hibakeresése

1. A VS Code-ban nyissa meg Azure Machine Learning bővítménynézetet.
1. Bontsa ki a munkaterületet tartalmazó előfizetési csomópontot. Ha még nem rendelkezik ilyen munkaterülettel, létrehozhat egy Azure Machine Learning [a](how-to-manage-resources-vscode.md#create-a-workspace) bővítmény használatával.
1. Bontsa ki a munkaterület csomópontját.
1. Kattintson a jobb gombbal **a Kísérletek csomópontra,** és válassza a **Kísérlet létrehozása lehetőséget.** Amikor megjelenik a kérdés, adja meg a kísérlet nevét.
1. Bontsa **ki a Kísérletek** csomópontot, kattintson a jobb gombbal a futtatni kívánt kísérletre, majd válassza a Kísérlet futtatása **lehetőséget.**
1. A kísérlet futtatására rendelkezésre álló lehetőségek listájából válassza a Helyi **lehetőséget.**
1. **Csak Windows rendszeren használja az első alkalommal.** Amikor a rendszer a fájlmegosztás engedélyezésekor kéri, válassza az **Igen lehetőséget.** Ha engedélyezi a fájlmegosztást, a Docker csatlakoztathatja a szkriptet tartalmazó könyvtárat a tárolóhoz. Emellett azt is lehetővé teszi, hogy a Docker a futtatás naplóit és kimenetét a rendszer egy ideiglenes könyvtárában tárolja.
1. A **kísérlet hibakereséshez** válassza az Igen lehetőséget. Egyéb esetben a **Nem** választógombot jelölje be. A nem kiválasztásával helyileg futtathatja a kísérletet a hibakereső csatlakoztatása nélkül.
1. A **futtatási konfiguráció létrehozásához válassza** az Új futtatási konfiguráció létrehozása lehetőséget. A futtatási konfiguráció határozza meg a futtatni kívánt szkriptet, a függőségeket és a használt adatkészleteket. Másik lehetőségként, ha már rendelkezik ilyenvel, válassza ki azt a legördülő menüből.
    1. Válassza ki a környezetet. Az össze összecsukott vagy saját Azure Machine Learning [bármelyiket](resource-curated-environments.md) választhatja.
    1. Adja meg a futtatni kívánt szkript nevét. Az elérési út a VS Code-ban megnyitott könyvtárhoz képest relatív.
    1. Válassza ki, hogy szeretne-e Azure Machine Learning adatkészletet használni. A bővítmény [Azure Machine Learning hozhat](how-to-manage-resources-vscode.md#create-dataset) létre új adatkészleteket.
    1. A hibakereső a kísérlet futtatásához szükséges a hibakereső csatlakoztatásához. A debugpy függőségként való hozzáadásához válassza a **Debugpy hozzáadása lehetőséget.** Ellenkező esetben válassza a **Kihagyás lehetőséget.** Ha nem adja hozzá függőségként a debugpyt, a kísérlet a hibakeresőhöz csatolása nélkül fut.
    1. A futtatási konfigurációs beállításokat tartalmazó konfigurációs fájl megnyílik a szerkesztőben. Ha elégedett a beállításokkal, válassza a **Kísérlet elküldése lehetőséget.** Másik lehetőségként nyissa meg a parancskatapaletta (**View > Command Palette**) (Parancskatapaletta megtekintése) elemet a menüsávon, és írja be a parancsot a `Azure ML: Submit experiment` szövegmezőbe.
1. Miután beküldi a kísérletet, létrejön egy Docker-rendszerkép, amely tartalmazza a szkriptet és a futtatási konfigurációban megadott konfigurációkat.

    Amikor megkezdődik a Docker-rendszerkép buildelési folyamata, a fájlstream tartalma a `60_control_log.txt` VS Code kimeneti konzoljára lesz továbbítva.

    > [!NOTE]
    > A Docker-rendszerkép első létrehozásakor több percig is eltarthat.

1. Miután a rendszerkép felépítette, megjelenik egy üzenet, amely elindítja a hibakeresőt. Állítsa be a töréspontokat a szkriptben, és válassza a **Hibakereső** futtatása lehetőséget, amikor készen áll a hibakeresésre. Ezzel csatolja a VS Code-hibakeresőt a kísérletet futtató tárolóhoz. Másik lehetőségként a Azure Machine Learning vigye az egérmutatót az aktuális futtatás csomópontja fölé, és válassza a lejátszás ikont a hibakereső indításhoz.

    > [!IMPORTANT]
    > Egy kísérlethez nem lehet több hibakeresési munkamenet. Két vagy több kísérlet hibakeresését azonban több VS Code-példány használatával is el lehet végezni.

Ezen a ponton képesnek kell lennie a kód lépésein végiglépkedni és hibakeresést végezni a VS Code használatával.

Ha bármikor le szeretné mondani a futtatását, kattintson a jobb gombbal a futtatás csomópontjára, és válassza a **Futtatás megszakítása lehetőséget.**

A távoli kísérletek futtatásához hasonlóan a futtatás csomópontját is kibonthatja a naplók és kimenetek vizsgálatához.

> [!TIP]
> Azok a Docker-rendszerképek, amelyek a környezetben meghatározott függőségeket használják, újra felhasználják a futtatásokat. Ha azonban új vagy eltérő környezetben futtat egy kísérletet, a rendszer létrehoz egy új rendszerképet. Mivel ezek a rendszerképek a helyi tárolóba vannak mentve, javasoljuk, hogy távolítsa el a régi vagy a nem használt Docker-rendszerképeket. Lemezképek a rendszerből való eltávolításához használja a [Docker CLI-t](https://docs.docker.com/engine/reference/commandline/rmi/) vagy a [VS Code Docker-bővítményt.](https://code.visualstudio.com/docs/containers/overview)

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Hibakeresés és hibaelhárítás a gépi tanulási folyamatokban

Bizonyos esetekben előfordulhat, hogy interaktív módon kell hibakeresést végeznie a gépi tanulási folyamatban használt Python-kódon. A VS Code és a debugpy használatával csatolhatja a kódot a betanító környezetben való futás közben.

### <a name="prerequisites"></a>Előfeltételek

* Azure __Azure Machine Learning__ használatára konfigurált Virtual Network __munkaterület.__
* Egy __Azure Machine Learning folyamat,__ amely Python-szkripteket használ a folyamat lépései részeként. Például egy PythonScriptStep.
* Egy Azure Machine Learning számítási fürt, amely a virtuális __hálózatban__ van, és amelyet a folyamat a __betanításra használ.__
* Egy __fejlesztési környezet,__ amely a __virtuális hálózaton belül van.__ A fejlesztési környezet a következők egyike lehet:

  * Egy Azure-beli virtuális gép a virtuális hálózaton
  * A notebookos virtuális gép számítási példánya a virtuális hálózaton
  * Olyan ügyfélszámítógép, amely magánhálózati kapcsolattal rendelkezik a virtuális hálózathoz VPN-en vagy ExpressRoute-on keresztül.

Az Azure-beli virtuális gépek Virtual Network és Azure Machine Learning lásd: Virtuális hálózatok elkülönítése [és adatvédelmi áttekintés.](how-to-network-security-overview.md)

> [!TIP]
> Bár használhatja a Azure Machine Learning, amelyek nem virtuális hálózat mögött vannak, ajánlott virtuális hálózatot használni.

### <a name="how-it-works"></a>Működés

Az ML-folyamat lépései Python-szkripteket futtatnak. Ezek a szkriptek a következő műveletek végrehajtásához módosulnak:

1. Naplózd annak a gazdagépnek az IP-címét, ahol futnak. Az IP-cím használatával csatlakoztathatja a hibakeresőt a szkripthez.

2. Indítsa el a hibakeresési összetevőt, és várjon, amíg a hibakereső csatlakozik.

3. A fejlesztési környezetben a betanítási folyamat által létrehozott naplók figyelése alapján megkeresheti azt az IP-címet, ahol a szkript fut.

4. Egy fájl használatával meg kell mondania a VS Code-nak az IP-címet, amelyhez a hibakeresőt `launch.json` csatlakoztatni kell.

5. Csatolja a hibakeresőt, és interaktívan végiglépked a szkripten.

### <a name="configure-python-scripts"></a>Python-szkriptek konfigurálása

A hibakeresés engedélyezéséhez a következő módosításokat kell tennie a gépi tanulási folyamat lépései által használt Python-szkript(öke)n:

1. Adja hozzá a következő importálási utasításokat:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Adja hozzá a következő argumentumokat. Ezekkel az argumentumokkal szükség szerint engedélyezheti a hibakeresőt, és beállíthatja a hibakereső csatolásának időtúllépését:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Adja hozzá a következő utasításokat. Ezek az utasítások betöltik az aktuális futtatás környezetét, így naplózhatja annak a csomópontnak az IP-címét, amelyen a kód fut:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adjon hozzá `if` egy utasítást, amely elindítja a debugpyt, és megvárja, amíg a hibakereső csatolva lesz. Ha nincs hibakereső csatolva az időtúllépés előtt, a szkript a szokásos módon folytatódik. Ügyeljen arra, hogy a `HOST` és `PORT` a értékeket a saját `listen` függvényére cserélje le.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

Az alábbi Python-példa egy egyszerű fájlt `train.py` mutat be, amely lehetővé teszi a hibakeresést:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Gépi tanulási folyamat konfigurálása

A debugpy futtatásához és a futtatás környezetének lekért létrehozásához szükséges Python-csomagok létrehozásához hozzon létre egy környezetet, és állítsa be a következőt: `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Módosítsa az SDK verzióját úgy, hogy megfeleljen az Ön által használt verziónak. Az alábbi kódrészlet bemutatja, hogyan hozhat létre környezetet:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

A [Python-szkriptek konfigurálása szakaszban](#configure-python-scripts) új argumentumokat adtunk hozzá az ML-folyamat lépései által használt szkriptekhez. A következő kódrészlet bemutatja, hogyan használhatók ezek az argumentumok az összetevő hibakeresésének engedélyezéséhez és időtúllépés beállításhoz. Azt is bemutatja, hogyan használhatja a korábban létrehozott környezetet a `runconfig=run_config` beállításával:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

A folyamat futtatásakor minden lépés létrehoz egy gyermekfutatot. Ha a hibakeresés engedélyezve van, a módosított szkript az alábbi szöveghez hasonló adatokat naplóz a `70_driver_log.txt` gyermekfutatban:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Mentse az `ip_address` értéket. Ezt a következő szakaszban használjuk.

> [!TIP]
> Az IP-címet a folyamatlépés gyermekfutatának futtatásnaplóiban is megtalálja. Az információk megtekintésével kapcsolatos további információkért lásd: [Azure ML-kísérletek futtatásának és metrika figyelése.](how-to-log-view-metrics.md)

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A debugpy a VS Code fejlesztői környezetben való telepítéséhez használja a következő parancsot:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    A hibakeresés VS Code-ban való használatával kapcsolatos további információkért lásd: [Távoli hibakeresés.](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

1. Ha úgy szeretné konfigurálni a VS Code-Azure Machine Learning, hogy az kommunikáljon a hibakeresőt futtató számítási erőforrással, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __Hibakeresés menüt,__ majd a __Konfigurációk megnyitása lehetőséget.__ Megnyílik a __launch.jsnevű__ fájl.

    1. Azlaunch.js __fájlban__ keresse meg a et tartalmazó sort, és szúrja be utána `"configurations": [` a következő szöveget. Módosítsa a bejegyzést az előző szakasz naplóiban visszaadott `"host": "<IP-ADDRESS>"` IP-címre. Módosítsa a bejegyzést egy helyi könyvtárra, amely tartalmazza a hibakeresés alatt található `"localRoot": "${workspaceFolder}/code/step"` szkript másolatát:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Ha a konfigurációk szakaszban már vannak más bejegyzések, adjon hozzá egy vesszőt (,) a beszúrt kód után.

        > [!TIP]
        > Az ajánlott eljárás, különösen a folyamatok esetében, ha a szkriptek erőforrásait külön könyvtárakban tartja, hogy a kód csak az egyes lépésekhez legyen releváns. Ebben a példában `localRoot` a példaérték a következőre hivatkozik: `/code/step1` .
        >
        > Ha több szkript hibakeresését is választja, különböző könyvtárakban hozzon létre külön konfigurációs szakaszt mindegyik szkripthez.

    1. Mentse a __launch.jsfájlt.__

### <a name="connect-the-debugger"></a>A hibakereső csatlakoztatása

1. Nyissa meg a VS Code-et, és nyissa meg a szkript egy helyi példányát.
2. Állítson be olyan töréspontokat, ahol a szkriptet le szeretné állítani a csatolt fájlban.
3. Amíg a gyermekfolyamat futtatja a szkriptet, és az megjelenik a naplókban, használja az `Timeout for debug connection` F5 billentyűt, vagy válassza a __Hibakeresés lehetőséget.__ Amikor a rendszer kéri, válassza Azure Machine Learning __Számítás: távoli hibakeresés konfigurációja__ lehetőséget. Az oldalsávon a hibakeresés ikont, a __Azure Machine Learning:__ távoli hibakeresés bejegyzést is választhatja a Hibakeresés legördülő menüből, majd a zöld nyíllal csatolhatja a hibakeresőt.

    Ezen a ponton a VS Code csatlakozik a számítási csomóponton található hibakeresési ponthoz, és leáll a korábban beállított töréspontnál. Most már végigléptetheti a kódot futtatáskor, megtekintheti a változókat stb.

    > [!NOTE]
    > Ha a naplóban megjelenik egy bejegyzés, amely szerint , akkor az időtúllépés lejárt, és a szkript a hibakereső nélkül `Debugger attached = False` folytatódik. Küldje el újra a folyamatot, és csatlakoztassa a hibakeresőt az üzenet után, illetve az `Timeout for debug connection` időkorlát lejárta előtt.

## <a name="debug-and-troubleshoot-deployments"></a>Üzemelő példányok hibakeresése és hibaelhárítása

Bizonyos esetekben szükség lehet a modell üzembe helyezésében található Python-kód interaktív hibakeresésére. Ha például a bejegyzés parancsfájlja sikertelen, és az okot nem lehet további naplózással meghatározni. A VS Code és a debugpy használatával csatolhatja a Docker-tárolóban futó kódot.

> [!IMPORTANT]
> Ez a hibakeresési módszer nem működik a és a használatával a modellek `Model.deploy()` `LocalWebservice.deploy_configuration` helyi üzembe helyezésekor. Ehelyett létre kell hoznia egy rendszerképet a [Model.package() metódussal.](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-)

A helyi webszolgáltatás üzembe helyezéséhez működő Docker-telepítés szükséges a helyi rendszeren. A Docker használatával kapcsolatos további információkért tekintse meg a [Docker dokumentációját.](https://docs.docker.com/) Vegye figyelembe, hogy számítási példányok esetén a Docker már telepítve van.

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A debugpy a helyi VS Code fejlesztői környezetben való telepítéséhez használja a következő parancsot:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    A hibakeresés VS Code-ban való használatával kapcsolatos további információkért lásd: [Távoli hibakeresés.](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

1. Ha úgy konfigurálja a VS Code-et, hogy kommunikáljon a Docker-rendszerképpel, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ban válassza a __Hibakeresés menüt__ a __Futtatási__ egységben, majd válassza a __Konfigurációk megnyitása lehetőséget.__ Megnyílik a __launch.jsnevű__ fájl.

    1. Azlaunch.js __fájlban__ keresse meg a __"configurations"__ elemet (a elemet tartalmazó sort), és szúrja be `"configurations": [` utána a következő szöveget. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        A beszúrás utánlaunch.js __fájlban__ található fájlnak az alábbihoz hasonlónak kell lennie:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Ha már vannak más bejegyzések a konfigurációk szakaszban, adjon hozzá egy vesszőt ( __,__ ) a beszúrt kód után.

        Ez a szakasz az __5678-as__ porton keresztül csatlakozik a Docker-tárolóhoz.

    1. Mentse a __launch.jsfájlt.__

### <a name="create-an-image-that-includes-debugpy"></a>A debugpyt tartalmazó rendszerkép létrehozása

1. Módosítsa az üzemelő példány Conda-környezetét úgy, hogy az tartalmazza a debugpy-t. Az alábbi példa bemutatja, hogyan adhatja hozzá a `pip_packages` paraméterrel:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. A hibakereséshez és a szolgáltatás indításakor a kapcsolatra való várakozáshoz adja hozzá a következőt a fájl `score.py` tetejéhez:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Hozzon létre egy rendszerképet a környezeti definíció alapján, és húzza le a rendszerképet a helyi regisztrációs adatbázisba. 

    > [!NOTE]
    > Ez a példa feltételezi, hogy a Azure Machine Learning, és `ws` ez az üzembe helyezett `model` modell. A fájl tartalmazza az 1. lépésben létrehozott `myenv.yml` Conda-függőségeket.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    A kép létrehozása és letöltése után (ez a folyamat több mint 10 percet is igénybe vehet, ezért várjon lassan), a kép elérési útja (az adattárat, a nevet és a címkét is tartalmazza, amely ebben az esetben a kivonata is) végül a következőhöz hasonló üzenetben jelenik meg:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. A lemezkép helyi használatának egyszerűbbé váltása érdekében a következő paranccsal adhat hozzá címkét ehhez a lemezképhez. A következő parancsban cserélje le a helyére az előző `myimagepath` lépés helyértékét.

    ```bash
    docker tag myimagepath debug:1
    ```

    A további lépésekhez a helyi rendszerképre hivatkozhat a teljes képútvonal értéke `debug:1` helyett.

### <a name="debug-the-service"></a>A szolgáltatás hibakeresése

> [!TIP]
> Ha időtúllépést ad meg a fájlban a hibakeresésipy-kapcsolathoz, az időkorlát lejárta előtt csatlakoztatnia kell a VS Code-et a hibakeresési `score.py` munkamenethez. Indítsa el a VS Code-et, nyissa meg a helyi példányát, állítson be egy töréspontot, és készítse elő a használatra az ebben `score.py` a szakaszban található lépések előtt.
>
> A hibakeresésről és a töréspontok beállításával kapcsolatos további információkért lásd: [Hibakeresés.](https://code.visualstudio.com/Docs/editor/debugging)

1. Docker-tároló rendszerkép használatával való indításhoz használja a következő parancsot:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Ezzel a helyi `score.py` tárolót a tárolóban találhatóhoz csatolja. Ezért a szerkesztőben végrehajtott módosítások automatikusan megjelennek a tárolóban

2. A jobb élmény érdekében egy új VS Code-felülettel is bemehet a tárolóba. Válassza ki `Docker` a kiterjedést a VS Code oldalsávjában, és keresse meg a létrehozott helyi tárolót, amely ebben a dokumentációban a következő: `debug:1` . Kattintson a jobb gombbal erre a tárolóra, és válassza a lehetőséget, majd automatikusan megnyílik egy új VS Code-felület, amely a létrehozott tárolón belüli `"Attach Visual Studio Code"` adatokat jeleníti meg.

    ![A tároló VS Code-felülete](./media/how-to-troubleshoot-deployment/container-interface.png)

3. Futtassa a következő parancsot a rendszerhéjban a tárolóban

    ```bash
    runsvdir /var/runit
    ```
    Ezután a következő kimenetet láthatja a tároló rendszerhéjában:

    ![A tároló futtatására szolgáló konzol kimenete](./media/how-to-troubleshoot-deployment/container-run.png)

4. A VS Code tárolón belüli hibakereséshez való csatolásához nyissa meg a VS Code-et, és használja az F5 billentyűt, vagy válassza a __Hibakeresés lehetőséget.__ Amikor a rendszer kéri, válassza ki a __Azure Machine Learning: Docker-hibakeresési konfiguráció lehetőséget.__ Az oldalsávon válassza a Run __extention__ (Futtatás kiterjesztése) ikont, a __Azure Machine Learning Deployment: Docker Debug__ (Üzembe helyezés: Docker hibakeresése) bejegyzést a Hibakeresés legördülő menüben, majd a zöld nyíllal csatolja a hibakeresőt.

    ![A hibakeresés ikon, a hibakeresési gomb és a konfigurációválasztó](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Miután a zöld nyílra kattintott, és csatlakoztatta a hibakeresőt, a tároló VS Code-felületén új információkat láthat:
    
    ![A tároló hibakeresőe információkat csatolt](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Emellett a VS Code fő felületén a következőt láthatja:

    ![A VS Code töréspontja a score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

A tárolóhoz csatolt helyi hely pedig már leállt a beállított `score.py` töréspontoknál. Ezen a ponton a VS Code csatlakozik a Docker-tárolóban található hibakeresési ponthoz, és leállítja a Docker-tárolót a korábban beállított törésponton. Most már végigléptetheti a kódot futtatáskor, megtekintheti a változókat stb.

A Python hibakeresésének VS Code használatával való használatával kapcsolatos további információkért lásd: [Python-kód hibakeresése.](https://code.visualstudio.com/docs/python/debugging)

### <a name="stop-the-container"></a>A tároló leállítása

A tárolót a következő paranccsal állíthatja le:

```bash
docker stop debug
```

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a TÁVOLI VS Code-et, egy számítási példányt távoli számításként használhat a VS Code-ból a kód interaktív hibakereséseként. 

További információ a hibaelhárításról:

* [Helyi modell üzembe helyezése](how-to-troubleshoot-deployment-local.md)
* [Távoli modell üzembe helyezése](how-to-troubleshoot-deployment.md)
* [Machine Learning-folyamatok](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

