---
title: Tároló-példányok naplóinak beolvasása & események
description: Megtudhatja, hogyan kérhet le Azure Container Instances tároló naplóit és eseményeit a tárolókkal kapcsolatos problémák elhárítása érdekében
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746963"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Tárolónaplók és -események lekérése az Azure Container Instancesben

Ha a tároló nem működik a Azure Container Instancesban, először tekintse meg a naplókat az [az Container logs][az-container-logs]paranccsal, és továbbítsa a standard out és a standard szintű hibát az [az Container Attach][az-container-attach]paranccsal. Megtekintheti a Azure Portal tároló példányainak naplóit és eseményeit, vagy a naplók napló-és esemény-adatfájljait is elküldheti [Azure monitor naplóiba](container-instances-log-analytics.md).

## <a name="view-logs"></a>Naplók megtekintése

Ha az alkalmazás kódjában lévő naplókat szeretné megtekinteni egy tárolóban, használhatja az az [Container logs][az-container-logs] parancsot.

Az alábbiakban a példa feladathoz tartozó tároló kimenete látható, amely a [parancssort egy tároló példányban állítja be](container-instances-start-command.md#azure-cli-example), miután érvénytelen URL-címet adott meg a parancssori felülbírálás használatával:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

Az az [Container Attach][az-container-attach] parancs diagnosztikai adatokat biztosít a tároló indításakor. A tároló megkezdése után az STDOUT és a STDERR a helyi konzolra kerül.

Például itt látható a feladat-alapú tárolóban a [parancssor beállítása egy tároló-példányban](container-instances-start-command.md#azure-cli-example), miután a nagy szövegfájl érvényes URL-címét adta meg a feldolgozáshoz:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Diagnosztikai események beolvasása

Ha a tároló telepítése nem sikerül, tekintse át a Azure Container Instances erőforrás-szolgáltató által biztosított diagnosztikai adatokat. A tároló eseményeinek megtekintéséhez futtassa az az [Container show][az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet tartalmazza a tároló alapvető tulajdonságait, valamint az üzembe helyezési eseményeket (Itt láthatók a csonkítva):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogy miként lehet [elhárítani a Azure Container instances gyakori tároló-és üzembe helyezési problémáit](container-instances-troubleshooting.md) .

Megtudhatja, hogyan küldhet napló-és eseményvezérelt információkat a tárolók csoportjai számára a [naplók Azure monitor](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
