---
title: Parancsok végrehajtása futó tárolópéldányban
description: Megtudhatja, hogyan hajt végre egy parancsot egy jelenleg a Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 42832910efff67f111c669793798d9ff0e413536
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790778"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Parancs végrehajtása futó Azure-tárolópéldányban

Az Azure Container Instances támogatja a parancsok végrehajtását a futó tárolókban. A parancsok a már elindított tárolókban való végrehajtása különösen hasznos lehet az alkalmazásfejlesztés és a hibaelhárítás során. Ezt a lehetőséget a leggyakrabban akkor veszik igénybe, amikor elindítanak egy interaktív felületet, amellyel elháríthatók a futó tároló hibái.

## <a name="run-a-command-with-azure-cli"></a>Parancs futtatása az Azure CLI használatával

Futtason egy parancsot egy futó tárolóban [az az container exec paranccsal][az-container-exec] az [Azure CLI-ban:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Például egy Bash-rendszerhéj nginx-tárolóban való elindításához:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Az alábbi példakimenetben a Bash-rendszerhéj egy futó Linux-tárolóban indul el, és egy terminált biztosít a `ls` végrehajtáshoz:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Ebben a példában a parancssor egy futó Nanoserver-tárolóban indul el:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Többtárolós csoportok

Ha a [tárolócsoport](container-instances-container-groups.md) több tárolóval rendelkezik, például egy alkalmazástárolóval és egy naplózási oldalkocsival, adja meg annak a tárolónak a nevét, amelyben a parancsot futtatni kell a `--container-name` paranccsal.

A *mynginx* tárolócsoportban például két tároló található: *nginx-app* és *logger*. Rendszerhéj indítása az *nginx-app tárolón:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Korlátozások

Azure Container Instances jelenleg egyetlen folyamat indítását támogatja [az az container exec][az-container-exec]paranccsal, és nem adhat át parancsar argumentumokat. Nem láncolhat például olyan parancsokat, mint a , vagy nem `sh -c "echo FOO && echo BAR"` hajthatja végre a `echo FOO` parancsot.

## <a name="next-steps"></a>Következő lépések

További információ a hibaelhárítási eszközökről és az üzembe helyezéssel kapcsolatos gyakori [problémákról: Tárolóval](container-instances-troubleshooting.md)és üzembe helyezéssel kapcsolatos problémák elhárítása a Azure Container Instances.

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[azure-cli]: /cli/azure