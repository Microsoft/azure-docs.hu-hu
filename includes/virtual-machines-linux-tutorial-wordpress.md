---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5df1f7ff44a1603dd03d1d803ae9960dc124781e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161724"
---
## <a name="install-wordpress"></a>A WordPress telepítése

Ha ki szeretné próbálni a vermet, telepítsen egy mintaalkalmazást. Például a következő lépésekkel telepíthető a nyílt forráskódú [WordPress](https://wordpress.org/) platform, amellyel webhelyeket és blogokat hozhat létre. Emellett más számítási feladatokat (például [Drupal](http://www.drupal.org) és [Moodle](https://moodle.org/)) is kipróbálhat. 

Ez a WordPress-telepítés csak a működés ellenőrzésére szolgál. A WordPress legfrissebb verziójának az ajánlott biztonsági beállításokkal való telepítéséhez tekintse meg [a WordPress dokumentációját](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>A WordPress-csomag telepítése

Futtassa a következő parancsot:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>A WordPress konfigurálása

A WordPress konfigurálása a MySQL és a PHP használatához.

Egy munkakönyvtárban hozzon létre egy `wordpress.sql` szövegfájlt a MySQL-adatbázis konfigurálásához a WordPresshez: 

```bash
sudo sensible-editor wordpress.sql
```

Adja hozzá az alábbi parancsokat, és cserélje le a *yourPassword* kifejezést a választott adatbázisjelszóra (a többi értéket hagyja változatlanul). Ha előzőleg beállított egy MySQL biztonsági szabályzatot a jelszó erősségének ellenőrzésére, győződjön meg róla, hogy a jelszó megfelel a jelszóerősségi követelményeknek. Mentse a fájlt.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Futtassa az alábbi parancsot az adatbázis létrehozásához:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Mivel a `wordpress.sql` fájl tartalmazza az adatbázis hitelesítő adatait, a használat után érdemes törölnie:

```bash
sudo rm wordpress.sql
```

A PHP konfigurálásához futtassa az alábbi parancsot, amellyel megnyitja a tetszés szerinti szövegszerkesztőt, és létrehozza az `/etc/wordpress/config-localhost.php` fájlt:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Adja hozzá az alábbi sorokat a fájlhoz, és cserélje le a *yourPassword* kifejezést a saját WordPress-adatbázisa jelszavára (a többi értéket hagyja változatlanul). Ezután mentse a fájlt.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Helyezze át a WordPress-telepítést a webkiszolgáló dokumentum gyökérmappájába:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Most végezze el a WordPress beállítását, és tegye közzé a platformon. Nyissa meg a böngészőt, és lépjen a `http://yourPublicIPAddress/wordpress` webhelyre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![A WordPress telepítési oldala](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)