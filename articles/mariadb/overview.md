---
title: Áttekintés – Azure Database for MariaDB
description: Ismerkedjen meg az Azure Database for MariaDB szolgáltatással, amely a Microsoft Cloud-on alapuló, a MySQL Community Edition rendszerre épülő, kapcsolódó adatbázis-szolgáltatás.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 8115625099543d378728a6313a8cc4c95fec0cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662017"
---
# <a name="what-is-azure-database-for-mariadb"></a>Mi az az Azure Database for MariaDB?

Az Azure Database for MariaDB egy fejlesztőknek készült relációsadatbázis-szolgáltatás a Microsoft Cloudban. A Azure Database for MariaDB a [MariaDB Community Edition rendszeren](https://mariadb.org/download/) alapul (amely az GPLv2-licenc alatt érhető el) adatbázismotor, 10,2-es és 10,3-es verzió.

Az Azure Database for MariaDB a következőket biztosítja:

- Beépített magas rendelkezésre állás, további költségek nélkül.
- Kiszámítható teljesítmény, használatalapú fizetéses befoglaló díjszabással.
- Igény szerinti skálázás másodpercek alatt.
- A bizalmas adatok biztonságos védelme inaktív állapotban és mozgásban is.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Nagyvállalati szintű biztonság és megfelelőség.

Ezen képességek használata szinte semmilyen felügyeletet nem igényel, és nem jár további költségekkel. Az Azure Database for MariaDB segítségével gyorsabban fejlesztheti ki és hozhatja forgalomba az alkalmazásait. Nem kell értékes időt és erőforrásokat szánnia a virtuális gépek és az infrastruktúra kezelésére. Az alkalmazások fejlesztését a használni kívánt nyílt forráskódú eszközökkel és platformon folytathatja. A folyamatot az üzlet által megkövetelt sebességen és hatékonysággal hajthatja végre, és új készségeket sem kell elsajátítania.

Ha szeretne megismerkedni az Azure Database for MariaDB alapfogalmaival és jellemzőivel, beleértve a teljesítményt, a méretezhetőséget és a kezelhetőséget, tekintse át a következő rövid útmutatókat:

- [Azure-adatbázis létrehozása MariaDB-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure Database for MariaDB-kiszolgáló létrehozása az Azure CLI használatával](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

A Azure Database for MariaDB szolgáltatás számos szolgáltatási szintet kínál: alapszintű, általános célú és memória optimalizálva. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőségnek köszönhetően az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor valóban szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-pricing-tiers.md) leírása tartalmazza.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Hogyan lehet megállapítani, hogy mikor van szükség vertikális fel- vagy leskálázásra? Használja az Azure Database for MariaDB virtuális magokon alapuló, teljesítményértékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközeit. Ezekkel az eszközökkel gyorsan kiértékelheti, milyen hatásai vannak a virtuális magok aktuális vagy becsült teljesítményigényeken alapuló vertikális fel- vagy leskálázásának. A részleteket a [riasztások](howto-alert-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését

Az Azure piacvezető 99,99%-os rendelkezésre állási SLA-t a Microsoft által felügyelt adatközpontok globális hálózata látja el. Ez a hálózat segíti elő, hogy alkalmazása a hét minden napján, napi 24 órában fusson. Az Azure Database for MariaDB által biztosított beépített biztonság, hibatűrés és adatvédelem előnyeit is kihasználhatja. Az Azure Database for MariaDB-vel az időponthoz kötött visszaállítás segítségével a kiszolgálót visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba.

## <a name="secure-your-data"></a>Az adatok védelme

Az Azure-beli adatbázis-szolgáltatásokra jellemző adatvédelmet az Azure Database for MariaDB is biztosítja. Az Azure Database for MariaDB funkciói korlátozzák a hozzáférést, védik az inaktív és a mozgásban lévő adatokat, és segítenek a tevékenységek monitorozásában. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. A Azure Database for MariaDB biztonsági funkcióival kapcsolatos további információkért tekintse meg a [Biztonság áttekintése](concepts-security.md)című témakört.

## <a name="contacts"></a>Kapcsolattartók

Az Azure Database for MariaDB használatával kapcsolatos kérdéseit és javaslatait elküldheti egy e-mailben az [Azure Database for MariaDB csapatának](mailto:AskAzureDBforMariaDB@service.microsoft.com) (ez nem egyenlő a műszaki támogatással).

A következő elérhetőségeket is használhatja:
- Ha az Azure ügyfélszolgálatával szeretne kapcsolatba lépni, [nyújtson be támogatási kérelmet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az Azure Portalon.
- Ha a fiókjával van probléma, akkor is [nyújtson be támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új funkciókra szeretne javaslatot tenni, hozzon létre egy bejegyzést az [Azure visszajelzési fórumain](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Következő lépések

Most, hogy elolvasta az Azure Database for MariaDB bevezetését, készen áll a következőkre:
- Tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/mariadb/) oldalt a Cost-összehasonlításokhoz és a számológépekhez. 
- Első lépésként [hozza létre első kiszolgálóját](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
