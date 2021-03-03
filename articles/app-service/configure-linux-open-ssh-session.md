---
title: SSH-hozzáférés Linux-tárolók számára
description: Megnyithat egy SSH-munkamenetet egy Linux-tárolóban Azure App Service. Az egyéni Linux-tárolók az egyéni rendszerkép bizonyos módosításaival támogatottak.
keywords: Azure app Service, Web App, Linux, OSS
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8e9dd76b60d05b9fa5e3a4aaf7ccc6663f4a969b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709036"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Nyisson meg egy SSH-munkamenetet egy Linux-tárolóban Azure App Service

A [Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) általában a felügyeleti parancsok parancssori terminálról történő távoli végrehajtásához használatos. A Linuxon futó App Service SSH-támogatást biztosít az alkalmazás-tárolóban. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

A tárolóhoz közvetlenül a helyi fejlesztési gépről is csatlakozhat SSH és SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-rendszerképekkel

Lásd: [az SSH konfigurálása egyéni tárolóban](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>SSH-munkamenet megnyitása a távoli rendszerhéjból

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

A TCP-bújtatás használatával hálózati kapcsolatot hozhat létre a fejlesztői gép és Web App for Containers között egy hitelesített WebSocket-kapcsolaton keresztül. Lehetővé teszi, hogy egy SSH-munkamenetet az Ön által választott ügyféltől App Service futtatott tárolóval nyisson meg.

Első lépésként telepítenie kell az [Azure CLI](/cli/azure/install-azure-cli)-t. Ha szeretné megtekinteni, hogyan működik az Azure CLI telepítése nélkül, nyissa meg [Azure Cloud Shell](../cloud-shell/overview.md). 

Nyisson meg egy távoli kapcsolódást az alkalmazáshoz az az [WebApp Remote-Network-kapcsolatkezelő Create](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) paranccsal. Adja _\<subscription-id>_ meg _\<group-name>_ és \_ \<app-name> _ az alkalmazáshoz.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` a parancs végén a Cloud Shell használata esetén csak kényelmi célokat szolgál. A háttérben futtatja a folyamatot, így a következő parancsot ugyanazon a rendszerhéjon futtathatja.

> [!NOTE]
> Ha a parancs végrehajtása sikertelen, győződjön meg arról, hogy a [távoli hibakeresés](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) *le van tiltva* a következő paranccsal:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

A parancs kimenete megadja az SSH-munkamenet megnyitásához szükséges információkat.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenetet az Ön által választott ügyféllel a helyi port használatával. Az alábbi példa az alapértelmezett [SSH](https://ss64.com/bash/ssh.html) -parancsot használja:

```bash
ssh root@127.0.0.1 -p <port>
```

Ha a rendszer kéri, írja be a következőt: `yes` a csatlakozás folytatásához. Ezt követően a rendszer kéri a jelszót. Használja `Docker!` , amely korábban volt látható.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

A hitelesítés után látnia kell a munkamenet-üdvözlőképernyő képernyőt.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Ezzel csatlakozott az összekötőhöz.  

Próbálja meg futtatni a [felső](https://ss64.com/bash/top.html) parancsot. Az alkalmazás folyamatát meg kell tudnia tekinteni a folyamat listában. Az alábbi példában szereplő kimenetben ez a következő: `PID 263` .

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>Következő lépések

Kérdéseket és problémákat tehet közzé az [Azure fórumán](/answers/topics/azure-webapps.html).

További információ a Web App for Containersről:

* [Node.js-alkalmazások távoli hibakeresése Azure App Service a VS Code-ból](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Gyors útmutató: egyéni tároló futtatása App Serviceon](quickstart-custom-container.md?pivots=container-linux)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](faq-app-service-linux.md)
