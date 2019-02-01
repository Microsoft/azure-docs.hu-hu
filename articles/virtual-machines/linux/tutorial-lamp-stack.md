---
title: Oktatóanyag – LAMP üzembe helyezése Linux rendszerű virtuális gépen az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan telepíthet LAMP stacket az Azure-ban üzemeltetett linuxos virtuális gépre
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 2e0befa302d9fe7b93ce8d9993ffc195dfc7a00b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511243"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: LAMP-webkiszolgáló telepítése Azure-beli Linux rendszerű virtuális gépen

Ez a cikk ismerteti, hogyan helyezhet üzembe Apache-webkiszolgálót, MySQL-t és PHP-t (a LAMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. Ha az NGINX-webkiszolgálót szeretné használni, tekintse meg a [LEMP-veremmel](tutorial-lemp-stack.md) kapcsolatos oktatóanyagot. Ha szeretné működés közben megtekinteni a LAMP-vermet, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása (a LAMP-veremben az „L” betű)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható. További információt a LAMP-veremről – beleértve az éles környezetre vonatkozó javaslatokat – az [Ubuntu dokumentációjában](https://help.ubuntu.com/community/ApacheMySQLPHP) talál.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Az Apache, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az Apache, a MySQL és a PHP telepítéséhez. A parancs végén lévő beszúrási jel (^) a `lamp-server^` csomag nevének része. 


```bash
sudo apt update && sudo apt install lamp-server^
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket.  

## <a name="verify-installation-and-configuration"></a>A telepítés és a konfigurálás ellenőrzése


### <a name="verify-apache"></a>Az Apache ellenőrzése

Ellenőrizze az Apache verzióját a következő paranccsal:
```bash
apache2 -v
```

Most, hogy az Apache telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az Apache2 Ubuntu alapértelmezett oldalának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az Apache alapértelmezett oldala][3]


### <a name="verify-and-secure-mysql"></a>Győződjön meg arról, és biztonságos MySQL

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

A gyökér szintű jelszó beállításával együtt, MySQL telepítésének biztosításához futtassa a `mysql_secure_installation` parancsfájlt. 

```bash
sudo mysql_secure_installation
```

Igény szerint beállíthatja a érvényesítése jelszó beépülő modult (ajánlott). Ezután állítson be jelszót a MySQL-gyökér szintű felhasználó számára, és konfigurálja a környezet többi biztonsági beállításait. Azt javasoljuk, hogy "Y" (Igen) válaszoljon minden kérdésre.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez.

```bash
sudo mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

### <a name="verify-php"></a>Ellenőrizze a PHP

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Ha további tesztelést szeretne végezni, hozzon létre egy rövid PHP-információs oldalt. amely a böngészőben tekinthető meg. A következő paranccsal hozhatja létre a PHP-információs oldalt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Most ellenőrizheti a létrehozott PHP-információs oldalt. Nyissa meg a böngészőt, és lépjen a `http://yourPublicIPAddress/info.php` webhelyre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![PHP-információs oldal][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy LAMP-kiszolgálót helyezett üzembe az Azure-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

Folytassa a következő oktatóanyaggal, amelyből megismerheti, hogyan tehetők biztonságossá a webkiszolgálók SSL-tanúsítványok használatával.

> [!div class="nextstepaction"]
> [Webkiszolgáló biztonságossá tétele SSL használatával](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png