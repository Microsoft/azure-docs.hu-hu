---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272184"
---
Egy olyan Azure Stack Edge-eszközön, amelyen a számítási szerepkör konfigurálva van, két különböző parancs használatával elháríthatja vagy figyelheti az eszközt.

- `iotedge`Parancsok használata. Ezek a parancsok alapszintű műveletekhez érhetők el az eszközön.
- `dkrdbe`Parancsok használata. Ezek a parancsok az eszközhöz tartozó műveletek széles köréhez érhetők el.

A fenti parancsok egyikének végrehajtásához [kapcsolódnia kell a PowerShell felületéhez](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>`iotedge`Parancsok használata

Az elérhető parancsok listájának megtekintéséhez [kapcsolódjon a PowerShell-felülethez](#connect-to-the-powershell-interface) , és használja a `iotedge` függvényt.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

A következő táblázat a rendelkezésre álló parancsok rövid leírását tartalmazza `iotedge` :

|command  |Leírás |
|---------|---------|
|`check`     | A gyakori konfigurációs és kapcsolódási problémák automatikus ellenőrzésének elvégzése       |
|`list`     | Modulok listázása         |
|`logs`     | Modul naplóinak beolvasása        |
|`restart`     | Modul leállítása és újraindítása         |


### <a name="use-dkrdbe-commands"></a>`dkrdbe`Parancsok használata

Az elérhető parancsok listájának megtekintéséhez [kapcsolódjon a PowerShell-felülethez](#connect-to-the-powershell-interface) , és használja a `dkrdbe` függvényt.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
A következő táblázat a rendelkezésre álló parancsok rövid leírását tartalmazza `dkrdbe` :

|command  |Leírás |
|---------|---------|
|`image`     | Lemezképek kezelése. A nem használt képek eltávolításához használja a következőt: `dkrdbe image prune -a -f`       |
|`images`     | Lemezképek listázása         |
|`inspect`     | Alacsony szintű információk visszaadása a Docker-objektumokon         |
|`login`     | Bejelentkezés Docker-beállításjegyzékbe         |
|`logout`     | Kijelentkezés Docker-beállításjegyzékből         |
|`logs`     | Tároló naplófájljainak beolvasása        |
|`port`     | Port hozzárendelésének vagy a tároló adott leképezésének listázása        |
|`ps`     | Tárolók listázása        |
|`pull`     | Rendszerkép vagy adattár lekérése egy beállításjegyzékből         |
|`start`     | Legalább egy leállított tároló indítása         |
|`stats`     | A tároló (k) erőforrás-használati statisztikáinak élő stream megjelenítése         |
|`stop`     | Egy vagy több futó tároló leállítása        |
|`system`     | A Docker kezelése         |
|`top`     | Tároló futó folyamatainak megjelenítése         |

Ha segítséget szeretne kérni bármely elérhető parancshoz, használja a következőt: `dkrdbe <command-name> --help` .

A parancs használatának megismeréséhez például `port` írja be a következőt:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

A függvényhez elérhető parancsok `dkrdbe` ugyanazokat a paramétereket használják, mint a normál Docker-parancsokhoz. A Docker parancshoz használt beállítások és paraméterek esetében lépjen a [Docker commandline használatára](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>A modul sikeres üzembe helyezésének megkeresése

A számítási modulok olyan tárolók, amelyeken a rendszer üzleti logikát alkalmaz. Annak vizsgálatához, hogy a számítási modul telepítése sikeresen megtörtént-e, futtassa a parancsot, és ellenőrizze, hogy fut-e `ps` a tároló (a számítási modulnak megfelelő).

Az összes tároló (beleértve a szüneteltetett is) listájának lekéréséhez futtassa a `ps -a` parancsot.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Ha hiba történt a tároló rendszerképének létrehozásakor vagy a rendszerkép húzása közben, futtassa a parancsot `logs edgeAgent` .  `EdgeAgent` a IoT Edge futtatókörnyezet tárolója, amely más tárolók kiépítési feladata.

Mivel a `logs edgeAgent` rendszer az összes naplót kiírja, jó módszer a legutóbbi hibák megtekintésére, ha a kapcsolót használja `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>A tároló naplófájljainak beolvasása

Egy adott tároló naplóinak lekéréséhez először sorolja fel a tárolót, majd szerezze be az érdeklik a tároló naplóit.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A futó tárolók listájának lekéréséhez futtassa a `ps` parancsot.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Jegyezze fel annak a tárolónak az AZONOSÍTÓját, amelyhez a naplók szükségesek.

4. Egy adott tároló naplóinak lekéréséhez futtassa a `logs` tároló-azonosítót biztosító parancsot.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Az eszköz használati statisztikáinak figyelése

A memória, a CPU-használat és az IO az eszközön való figyeléséhez használja az `stats` parancsot.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. Futtassa a `stats` parancsot úgy, hogy letiltsa az élő streamet, és csak az első eredményt kérje le.

   ```powershell
   dkrdbe stats --no-stream
   ```

   A következő példa a parancsmag használatát mutatja be:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

