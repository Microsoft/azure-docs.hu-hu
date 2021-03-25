---
title: A kapcsolatok problémáinak elhárítása – Azure Database for MySQL – rugalmas kiszolgáló
description: Megtudhatja, hogyan oldhatja meg Azure Database for MySQL rugalmas kiszolgáló kapcsolódási problémáit.
keywords: MySQL-kapcsolat, kapcsolati karakterlánc, csatlakozási problémák, állandó hiba, kapcsolódási hiba
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: bdd52ba77fd9a65ce27985ff3c86a93fc887ddf9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109983"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL-rugalmas kiszolgáló kapcsolódási problémáinak elhárítása

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A kapcsolódási problémákat számos különböző dolog okozhatja, többek között:

* Tűzfalbeállítások
* A kapcsolatok időtúllépése
* Helytelen bejelentkezési adatok
* Néhány Azure Database for MySQL rugalmas kiszolgálói erőforrás esetében elérte a maximális korlátot

Ebből a cikkből megtudhatja, hogyan lehet elhárítani a hibák elhárításához szükséges gyakori hibákat és lépéseket.

## <a name="troubleshoot-common-errors"></a>Gyakori hibák elhárítása

Ha az alkalmazás tartósan nem tud kapcsolódni Azure Database for MySQL rugalmas kiszolgálóhoz, általában a következők egyikével kapcsolatos problémát jelez:

* Titkosított kapcsolat TLS/SSL használatával: a rugalmas kiszolgáló csak Transport Layer Security (TLS 1,2) titkosítású titkosított kapcsolatokat támogatja, és **a tls 1,0 és a tls 1,1 használatával minden bejövő kapcsolat megtagadva**. A TLS-verzió nem tiltható le vagy nem módosítható. További információ a [titkosított kapcsolatokról a Transport Layer Security (TLS 1,2) használatával Azure Database for MySQL-rugalmas kiszolgálókon](./how-to-connect-tls-ssl.md).
- Rugalmas kiszolgáló a *privát hozzáférésben (VNet-integráció)*: Győződjön meg arról, hogy a virtuális hálózaton belülről csatlakozik a rugalmas kiszolgálóról. Lásd: [Virtual Network in Azure Database for MySQL rugalmas kiszolgáló]<!--(./concepts-networking-virtual-network.md)-->
- Rugalmas kiszolgáló *nyilvános hozzáféréssel (engedélyezett IP-címekkel)*, győződjön meg arról, hogy a tűzfal úgy van konfigurálva, hogy engedélyezze a kapcsolódást az ügyfélről. [A rugalmas kiszolgálói Tűzfalszabályok létrehozásával és kezelésével kapcsolatban a Azure Portal használatával](./how-to-manage-firewall-portal.md)talál további információt.
* Ügyféloldali tűzfal konfigurációja: az ügyfélen lévő tűzfalnak engedélyeznie kell az adatbázis-kiszolgálóhoz való kapcsolódást. A kiszolgáló azon IP-címeit és portjait, amelyeket nem lehet engedélyezni, valamint az alkalmazások neveit, például a MySQL-t egyes tűzfalakon.
* Felhasználói hiba: lehet, hogy hibás típusú kapcsolatok, például a kiszolgáló neve szerepel a kapcsolatok karakterláncában.

### <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldása

* A titkosított kapcsolatokkal kapcsolatos további tudnivalókért tekintse meg a [titkosított kapcsolatot a Transport Layer Security (TLS 1,2) használatával Azure Database for MySQL rugalmas kiszolgálókon](./how-to-connect-tls-ssl.md) – >.
* Ha **nyilvános hozzáférést (engedélyezett IP-címeket)** használ, a [Tűzfalszabályok](./how-to-manage-firewall-portal.md) beállításával engedélyezheti az ügyfél IP-címét. Csak ideiglenes tesztelési célokra állítson be egy tűzfalszabály használatát a 0.0.0.0 értékkel a kezdő IP-címként, és használja a 255.255.255.255 a záró IP-címként. Ekkor megnyílik a kiszolgáló minden IP-címre. Ha ezzel feloldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályot a megfelelő korlátozott IP-címhez vagy címtartomány létrehozásához.
* Az ügyfél és az Internet közötti összes tűzfalon ellenőrizze, hogy a 3306-es port nyitva van-e a kimenő kapcsolatok számára.
* Ellenőrizze a kapcsolatok karakterláncát és az egyéb kapcsolatbeállításokat. Tekintse meg az előre definiált kapcsolódási karakterláncokat a kiszolgálójának a Azure Portal közös nyelvekhez elérhető **kapcsolódási karakterláncok** lapján.

## <a name="next-steps"></a>Következő lépések
- A [MySQL Workbench használatával kapcsolódhat és lekérdezheti Azure Database for MySQL rugalmas kiszolgálókon tárolt adatlekérdezéseket](./connect-workbench.md).
- A [php használatával csatlakozhat, és lekérdezheti Azure Database for MySQL rugalmas kiszolgálókon tárolt adatlekérdezéseket](./connect-php.md).
- A [Python használatával csatlakozhat, és lekérdezheti Azure Database for MySQL rugalmas kiszolgálókon tárolt adatlekérdezéseket](./connect-python.md).
