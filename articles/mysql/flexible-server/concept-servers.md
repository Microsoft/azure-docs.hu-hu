---
title: Kiszolgálói fogalmak – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a témakör azokat a szempontokat és irányelveket ismerteti, amelyekkel Azure Database for MySQL rugalmas kiszolgálóval dolgozhat
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: b664dd406a1ab90b4ea5e85005a69935f345c609
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034659"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>A kiszolgálói fogalmak a Azure Database for MySQL rugalmas kiszolgálóban (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk az Azure Database for MySQL rugalmas kiszolgálók használatának szempontjait és irányelveit ismerteti.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Mi az Azure Database for MySQL rugalmas kiszolgáló?

Azure Database for MySQL rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely a MySQL közösségi verzióját futtatja. Általánosságban a szolgáltatás úgy lett kialakítva, hogy a felhasználói követelmények alapján rugalmasságot és konfigurációt biztosítson a testreszabáshoz. Ez ugyanaz a MySQL-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A rugalmas kiszolgáló felügyelt, és biztosítja a rendelkezésre álló teljesítményt, a kiszolgálók jobb kezelhetőségét és felügyeletét, valamint a hozzáférést és szolgáltatásokat a kiszolgáló szintjén.

Egy Azure Database for MySQL rugalmas kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisokhoz tartozó szülő erőforrás.
- Engedélyezi a MySQL-konfigurációt a kiszolgálói paramétereken keresztül (hivatkozás a kiszolgálói paraméterekkel kapcsolatos fogalmakra).
- Automatikus biztonsági mentést végez, és támogatja az időponthoz tartozó visszaállításokat.
- Névteret biztosít az adatbázisok számára.
- Egy erős élettartamú szemantikaú tároló, amely töröl egy kiszolgálót, és törli a bennük lévő adatbázisokat.
- Collocates-erőforrások egy régióban.
- Az ügyfél által biztosított kiszolgáló karbantartási ütemtervének támogatása
- Rugalmas kiszolgálók üzembe helyezésének lehetősége a magas rendelkezésre állás javítása érdekében a zónák redundáns beállítása
- Virtuális hálózati integrációt biztosít az adatbázis-kiszolgáló eléréséhez
- Lehetőséget biztosít a költségek megtakarítására a rugalmas kiszolgáló felfüggesztésével, ha nincs használatban
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- A a MySQL 5,7 és a MySQL 8,0 fő verzióját támogatja. További információ: [támogatott Azure Database for MySQL motor-verziók](./../concepts-supported-versions.md).

Egy Azure Database for MySQL rugalmas kiszolgálón belül létrehozhat egy vagy több adatbázist is. Dönthet úgy is, hogy kiszolgálónként létrehoz egy adatbázist az összes erőforrás használatára, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás a számítási réteg, a virtuális mag és a tárterület (GB) konfigurációja alapján strukturált kiszolgálónként történik. További információ: [számítás és tárolás](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló leállítása/elindítása

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi a kiszolgáló **leállítását** , ha nincs használatban, és a tevékenység folytatásakor **elindítja** a kiszolgálót. Ez lényegében az adatbázis-kiszolgálók költségeinek megtakarítására szolgál, és csak használat közben kell fizetnie az erőforrásért. Ez még fontosabbá válik a fejlesztési és tesztelési feladatokhoz, és ha csak a nap egy részében használja a kiszolgálót. Ha leállítja a kiszolgálót, az összes aktív kapcsolat el lesz dobva. Később, amikor újra online állapotba szeretné állítani a kiszolgálót, használhatja a [Azure Portal](how-to-stop-start-server-portal.md) vagy a CLI-t.

Ha a kiszolgáló **leállított** állapotban van, a kiszolgáló számítási felszámítása nem történik meg. A tárterület azonban továbbra is számlázásra kerül, mivel a kiszolgáló tárterülete továbbra is biztosítani fogja, hogy az adatfájlok elérhetők legyenek a kiszolgáló újraindításakor.

> [!IMPORTANT]
> Ha **leállítja** a kiszolgálót, akkor a következő 7 nap során marad ebben az állapotban. Ha ez idő alatt nem **indítja el** manuálisan, a rendszer 7 nap elteltével automatikusan elindítja a kiszolgálót. Ha nem használja a kiszolgálót, úgy is eldöntheti, hogy újra **leállítja** .

Az időkiszolgáló leállításakor nem hajthatók végre felügyeleti műveletek a kiszolgálón. A kiszolgáló konfigurációs beállításainak módosításához [el kell indítania a kiszolgálót](how-to-stop-start-server-portal.md). Tekintse meg a [Leállítás/indítás korlátozásait](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Hogyan felügyel egy kiszolgálót?

Azure Database for MySQL rugalmas kiszolgálót a [Azure Portal](./quickstart-create-server-portal.md) vagy az [Azure CLI](./quickstart-create-server-cli.md)használatával kezelheti.

## <a name="next-steps"></a>Következő lépések

-   További tudnivalók a [kiszolgálók létrehozásáról](./quickstart-create-server-portal.md)
-   Tudnivalók a [figyelésről és a riasztásokról](./how-to-alert-on-metric.md)

