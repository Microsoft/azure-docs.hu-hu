---
title: SSH-hozzáférés Linux-tárolókhoz
description: Az SSH-munkamenetet megnyithatja egy Linux-tárolóra a Azure App Service. Az egyéni Linux-tárolók az egyéni rendszerkép néhány módosításával támogatottak.
keywords: azure app service, webalkalmazás, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3610c4a571d73631ed39d416c72d0d6004dd170d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871758"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Nyisson meg egy SSH-munkamenetet egy Linux-tárolóhoz a Azure App Service

[A Secure Shellt (SSH)](https://wikipedia.org/wiki/Secure_Shell) gyakran használják rendszergazdai parancsok távoli, parancssori terminálból történő végrehajtására. App Service on Linux SSH-támogatást biztosít az alkalmazástárolóhoz. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

A tárolóhoz közvetlenül a helyi fejlesztői gépről is csatlakozhat SSH és SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-rendszerképekkel

Lásd: [SSH konfigurálása egyéni tárolóban.](configure-custom-container.md#enable-ssh)

## <a name="open-ssh-session-from-remote-shell"></a>SSH-munkamenet megnyitása távoli rendszerhéjból

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

A TCP-alagút használatával létrehozhat egy hálózati kapcsolatot a fejlesztői gép és a Web App for Containers webSocket-kapcsolaton keresztül. Lehetővé teszi egy SSH-munkamenet megnyitását a tárolóval, amely App Service a választott ügyféltől.

Első lépésekként telepítenie kell az [Azure CLI-t.](/cli/azure/install-azure-cli) Az Azure CLI telepítése nélkül is láthatja, hogyan működik, ha [megnyitja a Azure Cloud Shell.](../cloud-shell/overview.md) 

Nyisson meg egy távoli kapcsolatot az alkalmazással [az az webapp remote-connection create paranccsal.](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) Az _\<subscription-id>_ _\<group-name>_ alkalmazáshoz adja meg a , a és a _ \_ \<app-name> értéket.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` A parancs végén csak az egyszerűség kedvéért van szükség, ha a parancsot Cloud Shell. A háttérben futtatja a folyamatot, így a következő parancsot ugyanabban a rendszerhéjban futtathatja.

> [!NOTE]
> Ha ez a parancs sikertelen, a  [következő](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) paranccsal győződjön meg arról, hogy a távoli hibakeresés le van tiltva:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

A parancskimenet megadja az SSH-munkamenet megnyitásához szükséges információkat.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenetet a tárolóval a választott ügyféllel a helyi port használatával. Az alábbi példa az alapértelmezett [ssh-parancsot](https://ss64.com/bash/ssh.html) használja:

```bash
ssh root@127.0.0.1 -p <port>
```

Amikor a rendszer kéri, írja be a `yes` parancsot a csatlakozás folytatásához. Ezután a rendszer kérni fogja a jelszót. Használja `Docker!` a korábban bemutatott et.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

A hitelesítés után megjelenik a munkamenet üdvözlőképernyője.

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

Most már csatlakozott az összekötőhöz.  

Próbálja meg a felső [parancsot](https://ss64.com/bash/top.html) futtatni. A folyamatlistában látnia kell az alkalmazás folyamatát. Az alábbi példakimenetben az látható, amely a következővel `PID 263` rendelkezik: .

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

Kérdéseit és aggodalmait az [Azure fórumán is közzétenheti.](/answers/topics/azure-webapps.html)

További információ a Web App for Containers:

* [A VS Code-on Node.js alkalmazások távoli hibakeresésének Azure App Service bevezetése](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Rövid útmutató: Egyéni tároló futtatása a App Service](quickstart-custom-container.md?pivots=container-linux)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](faq-app-service-linux.md)
