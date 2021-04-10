---
title: Oktatóanyag – LAMP üzembe helyezése Linux rendszerű virtuális gépen az Azure-ban
description: Ebből az oktatóanyagból elsajátíthatja, hogyan telepíthet LAMP stacket az Azure-ban üzemeltetett linuxos virtuális gépre
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.collection: linux
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 3813931f47c110abcfb595065c1415ca9ed84c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564713"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: LAMP-webkiszolgáló telepítése Linux rendszerű virtuális gépre az Azure-ban

Ez a cikk ismerteti, hogyan helyezhet üzembe Apache-webkiszolgálót, MySQL-t és PHP-t (a LAMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. Ha szeretné működés közben megtekinteni a LAMP-vermet, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása (a LAMP-veremben az „L” betű)
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható. További információt a LAMP-veremről – beleértve az éles környezetre vonatkozó javaslatokat – az [Ubuntu dokumentációjában](https://help.ubuntu.com/community/ApacheMySQLPHP) talál.

Ez az oktatóanyag a CLI-t használja a [Azure Cloud Shellon](../../cloud-shell/overview.md)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Az Apache, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az Apache, a MySQL és a PHP telepítéséhez. A parancs végén lévő beszúrási jel (^) a `lamp-server^` csomag nevének része. 


```bash
sudo apt update && sudo apt install lamp-server^
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket.  

## <a name="verify-installation-and-configuration"></a>A telepítés és a konfigurálás ellenőrzése


### <a name="verify-apache"></a>Apache ellenőrzése

Ellenőrizze az Apache verzióját a következő paranccsal:
```bash
apache2 -v
```

Most, hogy az Apache telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az Apache2 Ubuntu alapértelmezett oldalának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az Apache alapértelmezett oldala][3]


### <a name="verify-and-secure-mysql"></a>A MySQL ellenőrzése és védelme

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

A MySQL telepítésének biztonságossá tételéhez, beleértve a gyökér jelszavának beállítását is, futtassa a `mysql_secure_installation` parancsfájlt. 

```bash
sudo mysql_secure_installation
```

Igény szerint beállíthatja a jelszó ellenőrzése beépülő modult (ajánlott). Ezután állítson be egy jelszót a MySQL root felhasználóhoz, és konfigurálja a környezete fennmaradó biztonsági beállításait. Javasoljuk, hogy az "Y" (igen) kérdésre válaszoljon az összes kérdésre.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez.

```bash
sudo mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

### <a name="verify-php"></a>A PHP ellenőrzése

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Ha további tesztelést szeretne végezni, hozzon létre egy rövid PHP-információs oldalt. amely a böngészőben tekinthető meg. A következő paranccsal hozhatja létre a PHP-információs oldalt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Most ellenőrizheti a létrehozott PHP-információs oldalt. Nyisson meg egy böngészőt, és ugorjon a `http://yourPublicIPAddress/info.php` címre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![PHP-információs oldal][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy LAMP-kiszolgálót helyezett üzembe az Azure-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan védheti meg a TLS/SSL-tanúsítványokkal rendelkező webkiszolgálókat.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló TLS-vel](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
