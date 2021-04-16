---
title: Tárolópéldány-naplók le & eseményekhez
description: Megtudhatja, hogyan olvashatja be a tárolónaplókat és -eseményeket Azure Container Instances tárolóproblémák elhárításához
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: b2b7ffb2cb4a7b1171afa42c2ef5a64b2bd928f8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379281"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Tárolónaplók és -események lekérése az Azure Container Instancesben

Ha a tároló hibásan viselkedik a Azure Container Instances, először az [az container logs][az-container-logs]naplóit kell megtekintenie, majd streamelje a standard out és standard hibát az az container attach [sal.][az-container-attach] Megtekintheti a tárolópéldányok naplóit és eseményeit a Azure Portal, vagy elküldheti a tárolócsoportok naplóit és eseményadatokat a [Azure Monitor naplókba.](container-instances-log-analytics.md)

## <a name="view-logs"></a>Naplók megtekintése

A tárolóban található alkalmazáskód naplóinak megtekintéséhez használhatja az [az container logs][az-container-logs] parancsot.

Az alábbiakban a Set the [command line in a](container-instances-start-command.md#azure-cli-example)container instance (Parancssor beállítása tárolópéldányban) példa feladatalapú tároló naplókimenete található, miután érvénytelen URL-címet adott meg parancssori felülbírálással:

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

Az [az container attach][az-container-attach] parancs diagnosztikai információkat biztosít a tároló indításakor. A tároló az elindulás után az STDOUT és az STDERR folyamot továbbítja a helyi konzolra.

Itt például a Set the [command line in a](container-instances-start-command.md#azure-cli-example)container instance (Parancssor beállítása tárolópéldányban) dokumentum feladatalapú tároló kimenete, miután megadott egy feldolgozandó nagy méretű szövegfájl érvényes URL-címét:

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

## <a name="get-diagnostic-events"></a>Diagnosztikai események lekérte

Ha a tároló üzembe helyezése sikertelen, tekintse át az erőforrás-szolgáltató által Azure Container Instances diagnosztikai adatokat. A tároló eseményeinek megtekintéséhez futtassa [az az container show][az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet tartalmazza a tároló alapvető tulajdonságait, valamint az üzembe helyezési eseményeket (itt csonkolt módon látható):

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
Megtudhatja, [hogyan háríthatja el a tárolók](container-instances-troubleshooting.md) és üzembe helyezés gyakori problémáit a Azure Container Instances.

Megtudhatja, hogyan küldhet napló- és eseményadatokat tárolócsoportoknak [a Azure Monitor naplókba.](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
