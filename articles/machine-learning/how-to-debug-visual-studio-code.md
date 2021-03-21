---
title: Interaktív hibakeresés a Visual Studio Code-ban
titleSuffix: Azure Machine Learning
description: Interaktív hibakeresés Azure Machine Learning kód, folyamatok és központi telepítések számára a Visual Studio Code használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 783b5afdaef369582614cde3525f7968fdb5e567
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508639"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktív hibakeresés a Visual Studio Code-ban



Megtudhatja, hogyan lehet interaktív módon hibakeresést végezni Azure Machine Learning kísérleteket, folyamatokat és üzembe helyezéseket a Visual Studio Code (VS Code) és a [debugpy](https://github.com/microsoft/debugpy/)használatával.

## <a name="run-and-debug-experiments-locally"></a>Kísérletek helyi futtatása és hibakeresése

A Azure Machine Learning bővítmény használatával ellenőrizheti, futtathatja és hibakereséssel végezheti el a gépi tanulási kísérleteket, mielőtt elküldené őket a felhőbe.

### <a name="prerequisites"></a>Előfeltételek

* Azure Machine Learning VS Code bővítmény (előzetes verzió). További információkért lásd: [Azure Machine learning vs Code bővítmény beállítása](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop Mac és Windows rendszerekhez
  * A Linux rendszerhez készült Docker-motor.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Windows rendszeren a [Docker konfigurálása Linux-tárolók használatára](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> A Windows esetében, bár nem szükséges, erősen ajánlott a [Docker használata a Linux Windows alrendszerével (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> A kísérlet helyi futtatása előtt győződjön meg arról, hogy a Docker fut.

### <a name="debug-experiment-locally"></a>Kísérletezés helyi hibakeresése

1. A VS Code-ban nyissa meg a Azure Machine Learning bővítmény nézetet.
1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot. Ha még nem rendelkezik ilyennel, [létrehozhat egy Azure Machine learning munkaterületet](how-to-manage-resources-vscode.md#create-a-workspace) a bővítmény használatával.
1. Bontsa ki a munkaterület csomópontot.
1. Kattintson a jobb gombbal a **kísérletek** csomópontra, majd válassza a **kísérlet létrehozása** lehetőséget. Ha megjelenik a kérdés, adja meg a kísérlet nevét.
1. Bontsa ki a **kísérletek** csomópontot, kattintson a jobb gombbal a futtatni kívánt kísérletre, és válassza a **kísérlet futtatása** parancsot.
1. A kísérlet futtatásához válassza a **helyileg** lehetőséget a lehetőségek listájából.
1. **Első alkalommal csak Windows rendszeren használható**. Amikor a rendszer kéri, hogy engedélyezze a fájlmegosztást, válassza az **Igen** lehetőséget. A fájlmegosztás engedélyezése lehetővé teszi a Docker számára a parancsfájlt tartalmazó könyvtár csatlakoztatását a tárolóhoz. Emellett lehetővé teszi a Docker számára a naplók és kimenetek tárolását a rendszer egy ideiglenes könyvtárába.
1. A kísérlet hibakereséséhez válassza az **Igen** lehetőséget. Egyéb esetben a **Nem** választógombot jelölje be. A nem gombra kattintva helyileg futtathatja a kísérletet a hibakeresőhöz való csatolás nélkül.
1. A futtatási konfiguráció létrehozásához válassza az **új futtatási konfiguráció létrehozása** lehetőséget. A futtatási konfiguráció határozza meg a futtatni kívánt parancsfájlt, a függőségeket és a használt adatkészleteket. Ha már rendelkezik ilyennel, válassza ki a legördülő menüből.
    1. Válassza ki a környezetet. Bármelyik [Azure Machine learning](resource-curated-environments.md) közül választhat, vagy létrehozhatja a sajátját.
    1. Adja meg a futtatni kívánt parancsfájl nevét. Az elérési út a VS Code-ban megnyitott könyvtárhoz képest relatív.
    1. Válassza ki, hogy Azure Machine Learning adatkészletet kíván-e használni, vagy sem. A bővítmény használatával létrehozhat [Azure Machine learning adatkészleteket](how-to-manage-resources-vscode.md#create-dataset) .
    1. Debugpy szükséges ahhoz, hogy a hibakeresőt a kísérletet futtató tárolóhoz csatolja. Ha függőségként szeretné hozzáadni a debugpy, válassza a **Debugpy hozzáadása** elemet. Ellenkező esetben válassza a **kihagyás** lehetőséget. Ha nem ad hozzá debugpy, a rendszer a hibakeresőhöz való csatolás nélkül futtatja a kísérletet.
    1. A szerkesztőben megnyílik a futtatási konfigurációs beállításokat tartalmazó konfigurációs fájl. Ha elégedett a beállításokkal, válassza a **kísérlet küldése** lehetőséget. Azt is megteheti, hogy a menüsávban megnyitja a parancssort (**> a parancs-paletta megtekintése**), és a `Azure ML: Submit experiment` parancsot a szövegmezőbe írja be.
1. Miután elküldte a kísérletet, a parancsfájlt tartalmazó Docker-rendszerkép és a futtatási konfigurációban megadott konfigurációk jönnek létre.

    A Docker-rendszerkép létrehozási folyamatának megkezdése után a fájl tartalma a `60_control_log.txt` kimeneti konzolra kerül a vs Code-ban.

    > [!NOTE]
    > A Docker-rendszerkép első létrehozása több percet is igénybe vehet.

1. A rendszerkép felépítése után megjelenik egy üzenet, amely elindítja a hibakeresőt. Állítsa be a töréspontokat a parancsfájlban, és válassza a hibakereső **elindítása** lehetőséget, amikor készen áll a hibakeresés elindítására. Ehhez csatolja a VS Code debuggert a kísérletet futtató tárolóhoz. Másik lehetőségként az Azure Machine Learning-bővítményben vigye a kurzort a jelenlegi Futtatás csomópontra, és válassza a lejátszás ikont a hibakereső elindításához.

    > [!IMPORTANT]
    > Egyetlen kísérlethez nem tartozhat több hibakeresési munkamenet. A két vagy több kísérletet egyszerre több VS Code-példány használatával is lehet debug.

Ezen a ponton át kell lépnie a kódot, és hibakeresést végezhet a VS Code használatával.

Ha bármikor le szeretné mondani a futtatást, kattintson a jobb gombbal a Futtatás csomópontra, és válassza a **Futtatás megszakítása** parancsot.

A távoli kísérletekhez hasonlóan a Futtatás csomópont is kiterjeszthető a naplók és kimenetek vizsgálatához.

> [!TIP]
> A rendszer a környezetében definiált azonos függőségeket használó Docker-rendszerképeket újra felhasználja a futtatások között. Ha azonban új vagy eltérő környezet használatával futtat kísérletet, új rendszerkép jön létre. Mivel ezeket a lemezképeket a helyi tárolóba menti, javasoljuk, hogy távolítsa el a régi vagy fel nem használt Docker-lemezképeket. Ha el szeretné távolítani a rendszerképeket a rendszerből, használja a [Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) -t vagy a [vs Code Docker-bővítményt](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Hibakeresés és hibaelhárítás a gépi tanulási folyamatokban

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a ML-folyamaton használt Python-kóddal. A VS Code és a debugpy használatával a betanítási környezetben is csatlakoztathatja a kódot.

### <a name="prerequisites"></a>Előfeltételek

* Egy __Azure Machine learning munkaterület__ , amely __Azure-Virtual Network__ használatára van konfigurálva.
* Egy __Azure Machine learning folyamat__ , amely a folyamat lépéseinek részeként Python-parancsfájlokat használ. Például egy PythonScriptStep.
* Egy Azure Machine Learning számítási fürt, amely __a virtuális hálózaton__ található, és amelyet __a folyamat a képzéshez használ__.
* A __virtuális hálózatban__ található __fejlesztési környezet__ . A fejlesztési környezet a következők egyike lehet:

  * Egy Azure-beli virtuális gép a virtuális hálózaton
  * Jegyzetfüzet virtuális gép számítási példánya a virtuális hálózaton
  * Olyan ügyfélszámítógép, amely magánhálózati kapcsolattal rendelkezik a virtuális hálózathoz VPN-vagy ExpressRoute-n keresztül.

Az Azure Virtual Network és a Azure Machine Learning használatával kapcsolatos további információkért lásd: [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md).

> [!TIP]
> Habár a virtuális hálózatokat nem futtató Azure Machine Learning erőforrásokkal is dolgozhat, javasolt a virtuális hálózat használata.

### <a name="how-it-works"></a>Működés

A ML-folyamat lépései Python-szkripteket futtatnak. Ezek a parancsfájlok a következő műveletek végrehajtásához vannak módosítva:

1. Naplózza annak a gazdagépnek az IP-címét, amelyen a futnak. Az IP-cím segítségével kapcsolja össze a hibakeresőt a parancsfájlhoz.

2. Indítsa el a debugpy hibakeresési összetevőjét, és várjon, amíg a hibakereső csatlakozni próbál.

3. A fejlesztési környezetből megfigyelheti a betanítási folyamat által létrehozott naplókat, hogy megkeresse azt az IP-címet, amelyen a parancsfájl fut.

4. A VS Code az IP-címet, hogy a hibakeresőt egy fájl használatával kapcsolja össze `launch.json` .

5. Csatlakoztatja a hibakeresőt, és interaktív módon átugorja a parancsfájlt.

### <a name="configure-python-scripts"></a>Python-parancsfájlok konfigurálása

A hibakeresés engedélyezéséhez végezze el a következő módosításokat az ML-folyamat lépései által használt Python-szkript (ek) ben:

1. Adja hozzá a következő importálási utasításokat:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Adja hozzá a következő argumentumokat. Ezekkel az argumentumokkal engedélyezheti a hibakeresőt igény szerint, és beállíthatja a hibakereső csatolásának időtúllépését:

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

1. Adja hozzá a következő utasításokat. Ezek az utasítások betöltik az aktuális futtatási környezetet, így naplózni lehet annak a csomópontnak az IP-címét, amelyen a kód fut:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adjon hozzá egy `if` utasítást, amely elindítja a debugpy, és megvárja, amíg a hibakereső csatolva van. Ha az időkorlát előtt nincs hibakereső társítva, a parancsfájl a szokásos módon folytatódik. Ügyeljen arra, hogy a `HOST` és a `PORT` érték helyett a függvény legyen a `listen` saját.

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

A következő Python-példa egy egyszerű `train.py` fájlt mutat be, amely lehetővé teszi a hibakeresést:

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

### <a name="configure-ml-pipeline"></a>ML folyamat konfigurálása

A debugpy indításához és a futtatási környezet beszerzéséhez szükséges Python-csomagok biztosításához hozzon létre egy környezetet, és állítsa be a következőt: `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Módosítsa az SDK verzióját úgy, hogy az megfeleljen a használtnak. A következő kódrészlet bemutatja, hogyan hozhat létre környezetet:

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

A [Python-parancsfájlok konfigurálása](#configure-python-scripts) szakaszban új argumentumok lettek hozzáadva az ml-folyamat lépései által használt parancsfájlokhoz. A következő kódrészlet azt mutatja be, hogyan használhatók ezek az argumentumok az összetevő hibakeresésének engedélyezéséhez, illetve időtúllépési érték megadásához. Azt is bemutatja, hogyan használhatja a korábban létrehozott környezetet a következő beállítással `runconfig=run_config` :

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

A folyamat futásakor minden lépés létrehoz egy alárendelt futtatást. Ha a hibakeresés engedélyezve van, a módosított parancsfájl a `70_driver_log.txt` gyermek által futtatott következő szöveghez hasonló adatokat naplóz:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Mentse az `ip_address` értéket. A következő szakaszban használatos.

> [!TIP]
> Az IP-címet az ehhez a folyamathoz tartozó alárendelt futtatási naplókból is megtalálhatja. Az információk megtekintésével kapcsolatos további információkért lásd: az [Azure ml-kísérletek futtatásának és metrikáinak monitorozása](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A VS Code fejlesztői környezet debugpy telepítéséhez használja a következő parancsot:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    További információ a debugpy és a VS Code használatával kapcsolatban: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Ha úgy szeretné konfigurálni a VS Code-t, hogy kommunikáljon a hibakeresőt futtató Azure Machine Learning számítási feladatokkal, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __hibakeresés__ menüt, majd válassza a __konfigurációk megnyitása__ lehetőséget. Megnyílik egy __launch.js__ nevű fájl.

    1. A fájl __launch.js__ keresse meg a benne található sort `"configurations": [` , majd szúrja be a következő szöveget. Módosítsa a `"host": "<IP-ADDRESS>"` bejegyzést a naplókban az előző szakaszban visszaadott IP-címhez. Módosítsa a `"localRoot": "${workspaceFolder}/code/step"` bejegyzést egy helyi könyvtárba, amely a hibakereső parancsfájl másolatát tartalmazza:

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
        > Ha már vannak más bejegyzések a konfigurációk szakaszban, adjon hozzá egy vesszőt (,) a beszúrt kód után.

        > [!TIP]
        > Az ajánlott eljárás, különösen a folyamatok esetében, hogy a parancsfájlok erőforrásai külön könyvtárakban maradjanak, így a kód csak az egyes lépésekre vonatkozik. Ebben a példában a `localRoot` példa értéke a következőre hivatkozik: `/code/step1` .
        >
        > Ha több parancsfájlt is hibakeresést végez, különböző címtárakban hozzon létre külön konfigurációs szakaszt minden parancsfájlhoz.

    1. Mentse a __launch.js__ fájlt.

### <a name="connect-the-debugger"></a>A hibakereső összekötése

1. Nyissa meg a VS Code-ot, és nyissa meg a parancsfájl helyi példányát.
2. Állítsa be azokat a töréspontokat, amelyeken a parancsfájlt le szeretné állítani a csatolása után.
3. Amíg az alárendelt folyamat futtatja a szkriptet, és `Timeout for debug connection` megjelenik a naplókban, használja az F5 billentyűt, vagy válassza a __hibakeresés__ lehetőséget. Ha a rendszer kéri, válassza a __Azure Machine learning számítás: Távoli hibakeresési__ konfiguráció elemet. Azt is megteheti, hogy kijelöli a hibakeresés ikont az oldalsó sávon, a __Azure Machine learning: Távoli hibakeresési__ bejegyzést a hibakeresés legördülő menüből, majd a zöld nyíl használatával csatlakoztatja a hibakeresőt.

    Ezen a ponton a VS Code csatlakozik a debugpy a számítási csomóponton, és a korábban beállított törésponton leáll. Most már megkezdheti a kód futtatását, megtekintheti a változókat stb.

    > [!NOTE]
    > Ha a napló egy bejegyzést jelez `Debugger attached = False` , akkor az időtúllépés lejárt, és a szkript a hibakereső nélkül folytatódott. Küldje el újra a folyamatot, és kapcsolja össze a hibakeresőt az `Timeout for debug connection` üzenet után, és az időtúllépés lejárta előtt.

## <a name="debug-and-troubleshoot-deployments"></a>Központi telepítések hibakeresése és hibaelhárítása

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a modell üzembe helyezésében található Python-kóddal. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A VS Code és a debugpy használatával a Docker-tárolón belül futó kód is csatolható.

> [!IMPORTANT]
> Ez a hibakeresési módszer nem működik `Model.deploy()` helyi modell használatakor és `LocalWebservice.deploy_configuration` üzembe helyezése esetén. Ehelyett létre kell hoznia egy rendszerképet a [Model. package ()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-) metódus használatával.

A helyi webszolgáltatás üzembe helyezéséhez a helyi rendszeren működő Docker-telepítés szükséges. A Docker használatával kapcsolatos további információkért lásd a [Docker dokumentációját](https://docs.docker.com/). Vegye figyelembe, hogy a számítási példányok használatakor a Docker már telepítve van.

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. Ha a debugpy-et a helyi VS Code-fejlesztési környezetbe szeretné telepíteni, használja a következő parancsot:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    További információ a debugpy és a VS Code használatával kapcsolatban: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Ha a VS Code-t úgy szeretné konfigurálni, hogy kommunikáljon a Docker-lemezképpel, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __hibakeresés__ menüt a __futtatási__ hatókörben, majd válassza a __konfigurációk megnyitása__ lehetőséget. Megnyílik egy __launch.js__ nevű fájl.

    1. A __launch.js__ fájlon keresse meg a __"konfigurációk"__ (a benne található) elemét `"configurations": [` , majd szúrja be a következő szöveget. 

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
        A Beszúrás után a fájl __launch.js__ a következőhöz hasonlónak kell lennie:
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

        Ez a szakasz a Docker-tárolóhoz csatlakozik a __5678__-es porton keresztül.

    1. Mentse a __launch.js__ fájlt.

### <a name="create-an-image-that-includes-debugpy"></a>Debugpy tartalmazó rendszerkép létrehozása

1. Módosítsa az üzemelő példány Conda-környezetét, hogy az tartalmazza a debugpy-t. Az alábbi példa a paraméterrel való hozzáadását mutatja be `pip_packages` :

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

1. A debugpy elindításához és a szolgáltatás indításakor várjon, ha a következőt adja hozzá a fájl elejéhez `score.py` :

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Hozzon létre egy rendszerképet a környezeti definíció alapján, és kérje le a rendszerképet a helyi beállításjegyzékbe. 

    > [!NOTE]
    > Ez a példa azt feltételezi, hogy az `ws` Azure Machine learning munkaterületre mutat, és ez `model` az üzembe helyezett modell. A `myenv.yml` fájl tartalmazza az 1. lépésben létrehozott Conda-függőségeket.

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

    A rendszerkép létrehozása és letöltése után (ez a folyamat több mint 10 percet is igénybe vehet, ezért várjon türelmesen), a rendszerkép elérési útja (tartalmazza a tárházat, a nevet és a címkét, amely ebben az esetben a kivonata is) a következőhöz hasonló üzenetben jelenik meg:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Annak érdekében, hogy könnyebben működjön együtt a rendszerképpel, a következő paranccsal adhat hozzá címkét ehhez a képhez. A `myimagepath` következő parancsban cserélje le az előző lépésben megadott Location értéket.

    ```bash
    docker tag myimagepath debug:1
    ```

    A többi lépésnél a `debug:1` teljes rendszerkép elérési útja helyett a helyi rendszerképre is hivatkozhat.

### <a name="debug-the-service"></a>A szolgáltatás hibakeresése

> [!TIP]
> Ha a fájlban a debugpy-kapcsolat időtúllépését állítja be `score.py` , akkor az időkorlát lejárta előtt csatlakoztatnia kell a vs Code-ot a hibakeresési munkamenethez. Indítsa el a VS Code-ot, nyissa meg a helyi példányát `score.py` , állítson be egy töréspontot, és készen áll arra, hogy az ebben a szakaszban ismertetett lépések használata előtt folytassa.
>
> A töréspontok hibakeresésével és beállításával kapcsolatos további információkért lásd: [hibakeresés](https://code.visualstudio.com/Docs/editor/debugging).

1. Ha egy Docker-tárolót a rendszerkép használatával szeretne elindítani, használja a következő parancsot:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Ez a `score.py` tárolóban lévő egyikhez csatolja a helyileg. Ezért a szerkesztőben végrehajtott módosítások automatikusan megjelennek a tárolóban.

2. A jobb élmény érdekében új VS Code-felülettel léphet be a tárolóba. Válassza ki a `Docker` vs Code oldali sáv kiterjedését, és keresse meg a létrehozott helyi tárolót, ebben a dokumentációban `debug:1` . Kattintson a jobb gombbal erre a tárolóra, és válassza ki az `"Attach Visual Studio Code"` új vs Code felületét, és ez az interfész a létrehozott tároló belsejében jelenik meg.

    ![A Container VS Code felülete](./media/how-to-troubleshoot-deployment/container-interface.png)

3. A tárolón belül futtassa a következő parancsot a rendszerhéjban.

    ```bash
    runsvdir /var/runit
    ```
    Ezután a tárolóban a következő kimenet jelenik meg a rendszerhéjon belül:

    ![A tároló futtatási konzoljának kimenete](./media/how-to-troubleshoot-deployment/container-run.png)

4. Ha a VS Code-t a tárolón belül debugpy szeretné csatolni, nyissa meg a VS Code-ot, és használja az F5 billentyűt, __vagy válassza a__ Ha a rendszer kéri, válassza a __Azure Machine learning központi telepítés: Docker hibakeresési__ konfigurációt. Azt is megteheti, hogy kijelöli a __futtatási__ mérték ikont az oldalsó sávon, a __Azure Machine learning üzembe helyezés: Docker debug__ bejegyzést a hibakeresés legördülő menüből, majd a zöld nyíl használatával csatolja a hibakeresőt.

    ![A hibakeresés ikon, a hibakeresés elindítása gomb és a konfigurációs választó](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Miután rákattintott a zöld nyílra, és csatolja a hibakeresőt, a Container VS Code felületén láthat néhány új információt:
    
    ![A tároló Debugger csatolt információi](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Emellett a fő VS Code felületén a következőt láthatja:

    ![A VS Code töréspont a score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

És most a `score.py` tárolóhoz csatolt helyi hely már le van állítva a beállított töréspontokon. Ezen a ponton a VS Code a Docker-tárolón belül csatlakozik a debugpy-hez, és leállítja a Docker-tárolót a korábban beállított törésponton. Most már megkezdheti a kód futtatását, megtekintheti a változókat stb.

A VS Code a Python hibakereséséhez való használatával kapcsolatos további információkért lásd [a Python-kód hibakeresését](https://code.visualstudio.com/docs/python/debugging)ismertető témakört.

### <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához használja a következő parancsot:

```bash
docker stop debug
```

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a VS Code Remote szolgáltatást, számítási példányt használhat távoli számításként a VS Code-ból a kód interaktív hibakereséséhez. 

További információ a hibaelhárításról:

* [Helyi modell üzembe helyezése](how-to-troubleshoot-deployment-local.md)
* [Távoli modell üzembe helyezése](how-to-troubleshoot-deployment.md)
* [Machine Learning-folyamatok](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

