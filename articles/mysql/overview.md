---
title: Áttekintés – Azure Database for MySQL
description: Ismerkedjen meg az Azure Database for MySQL szolgáltatással, amely a Microsoft Cloud-on alapuló, a MySQL Community Edition rendszerre épülő, kapcsolódó adatbázis-szolgáltatás.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736219"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az az Azure Database for MySQL?

A Azure Database for MySQL a Microsoft Cloud-on alapuló, a [MySQL Community Edition](https://www.mysql.com/products/community/) (a GPLv2 licenc alatt elérhető) adatbázismotor, a 5,6, a 5,7 és a 8,0 verziók alapján készült, a Microsoft felhőben található, Az Azure Database for MySQL a következőket nyújtja:

- Beépített magas rendelkezésre állás.
- Adatvédelem automatikus biztonsági mentéssel és időponthoz való visszaállítással akár 35 napig.
- Automatizált karbantartás az alapul szolgáló hardver, operációs rendszer és adatbázismotor számára a szolgáltatás biztonságos és naprakészen tartásához.
- Kiszámítható teljesítmény, használatalapú fizetéses befoglaló díjszabással.
- Rugalmas skálázás másodpercek alatt.
- A kiszolgáló leállítására/indítására képes optimalizálási vezérlők. 
- Nagyvállalati szintű biztonság és piacvezető megfelelőség a bizalmas adatok védelme érdekében a nyugalmi és a mozgásban.
- Figyelés és automatizálás a nagyméretű központi telepítések felügyeletének és figyelésének egyszerűsítése érdekében.
- Piacvezető támogatási élmény.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL fogalmi diagram":::

## <a name="deployment-models"></a>Üzembe helyezési modellek

A MySQL Community Edition Azure Database for MySQL a két üzembe helyezési módban érhető el:
- Önálló kiszolgáló 
- Rugalmas kiszolgáló (előzetes verzió)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL – egyetlen kiszolgáló

Azure Database for MySQL egyetlen kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely minimális követelményeket támaszt az adatbázis testreszabásához. Az egykiszolgálós platform úgy lett kialakítva, hogy az adatbázis-kezelési funkciók, például a javítások, a biztonsági mentések, a magas rendelkezésre állás, a biztonság minimális felhasználói konfigurációval és vezérléssel kezelhető legyen. Az architektúra beépített, magas rendelkezésre állású, 99,99%-os rendelkezésre állást biztosít egyetlen rendelkezésre állási zónában. A MySQL 5,6, 5,7 és 8,0 közösségi verzióját támogatja. A szolgáltatás ma már széles körben elérhető az [Azure számos régiójában](https://azure.microsoft.com/global-infrastructure/services/).

Az egykiszolgálós üzembehelyezési lehetőség három tarifacsomagot kínál: Alapszintű, Általános célú és Memóriaoptimalizált. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-pricing-tiers.md) leírása tartalmazza.

Az egyetlen kiszolgáló a legjobb megoldás a Felhőbeli natív alkalmazásokhoz, amelyek az automatikus javítás kezelésére szolgálnak anélkül, hogy a javítási ütemterv és az egyéni MySQL konfigurációs beállítások részletes szabályozására lenne szükség. 

Az egykiszolgálós telepítési mód részletes áttekintéséhez tekintse meg az [egykiszolgálós áttekintés című témakört](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – rugalmas kiszolgáló (előzetes verzió)

Azure Database for MySQL rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások felett. Általánosságban a szolgáltatás a felhasználói követelmények alapján nagyobb rugalmasságot és testreszabást biztosít. A rugalmas kiszolgáló architektúrája lehetővé teszi a felhasználók számára, hogy az egyetlen rendelkezésre állási zónában és több rendelkezésre állási zónában is magas rendelkezésre állást engedélyezzenek A rugalmas kiszolgálók jobb költség-optimalizálási vezérlőket biztosítanak a kiszolgáló és a feltört számítási szint leállítására/indítására, ideális olyan munkaterhelésekhez, amelyek folyamatosan teljes számítási kapacitást igényelnek. A szolgáltatás a MySQL 5,7 és a 8,0 közösségi verzióját támogatja. A szolgáltatás jelenleg nyilvános előzetes verzióban érhető el, ma már számos [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/)elérhető.

A rugalmas kiszolgálók a legmegfelelőbbek a következőhöz: 
- Hatékonyabb vezérlést és testreszabást igénylő alkalmazások fejlesztése.
- Zóna redundáns magas rendelkezésre állása
- Felügyelt karbantartási időszakok

A rugalmas kiszolgáló üzembe helyezési módjának részletes áttekintését lásd: [rugalmas kiszolgáló – áttekintés](flexible-server/overview.md).

## <a name="contacts"></a>Kapcsolattartók
Ha bármilyen kérdése vagy javaslata van a Azure Database for MySQL használatáról, küldjön e-mailt a Azure Database for MySQL csapatnak ([ @Ask a MySQL-hez készült Azure-adatbázis](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ez az e-mail-cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Következő lépések

További információ a Azure Database for MySQL két üzembe helyezési módjáról, és az igényeinek megfelelő lehetőségek közül választhat.

- [Önálló kiszolgáló](single-server/index.yml)
- [Rugalmas kiszolgáló](flexible-server/index.yml)
- [Válassza ki a megfelelő MySQL üzembe helyezési lehetőséget a munkaterhelés számára](select-right-deployment-type.md)
