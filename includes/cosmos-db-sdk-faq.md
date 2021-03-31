---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90068735"
---
**Hogyan fogok értesülni az SDK kivezetéséről?**

A Microsoft értesítést küld 12 hónappal a kivezetett SDK támogatásának vége előtt, megkönnyítve a zökkenőmentes áttérést egy támogatott SDK-ra. Az értesítéseket különböző kommunikációs csatornákon továbbítjuk: az Azure Portalon, Azure-frissítés útján, valamint a hozzárendelt szolgáltatásadminisztrátorok közvetlen értesítésével.

**Létrehozhatok alkalmazásokat egy hamarosan kivezetendő Azure Cosmos DB SDK-val ebben a 12 hónapos időszakban?** 

Igen, a 12 hónapos értesítési időszakban is lehet alkalmazásokat létrehozni, üzembe helyezni és módosítani a hamarosan kivezetendő Azure Cosmos DB SDK-val. Javasoljuk, hogy a 12 hónap folyamán lehetőleg mielőbb térjen át az Azure Cosmos DB SDK újabb, támogatott verziójára. 

**A kivezetési dátum után mi lesz a már nem támogatott Azure Cosmos DB SDK-t használó alkalmazásokkal?** 

A kivezetési dátum után az Azure Cosmos DB nem biztosít több hibajavítást, új funkciót és támogatást a kivezetett SDK-verziókhoz. Ha nem frissíti az SDK-t, az Azure Cosmos DB szolgáltatás továbbra is kiszolgálja az SDK kivezetett verziói által küldött kéréseket. 

**Melyik SDK-verziók kapják meg a legújabb funkciókat és frissítéseket?**

Az új funkciók és frissítések csak a legújabb támogatott fő SDK-verzió legújabb alverziójában lesznek hozzáadva. Javasoljuk, hogy mindig használja a legújabb verziót, hogy hozzájusson az új funkciókhoz, teljesítménybeli fejlesztésekhez és hibajavításokhoz. Ha egy régebbi, még nem kivezetett SDK-verziót használ, akkor az Azure Cosmos DB felé küldött kérései továbbra is működni fognak, de nem lesz hozzáférése az új lehetőségekhez.  

**Mit tegyek, ha nem tudom frissíteni az alkalmazásomat egy megszűnési dátum előtt?**

Javasoljuk, hogy mindig a lehető leghamarabb frissítsen a legújabb SDK-verzióra. Miután egy SDK kivezetendő megjelölést kap, 12 hónap áll rendelkezésre az alkalmazás frissítésére. Ha nem tudja elvégezni a frissítést a kivezetési dátumig, az Azure Cosmos DB szolgáltatás továbbra is kiszolgálja az SDK kivezetett verziói által küldött kéréseket, így a futó alkalmazások továbbra is működni fognak. Azonban az Azure Cosmos DB nem biztosít több hibajavítást, új funkciót és támogatást a kivezetett SDK-verziókhoz. 

Ha rendelkezik támogatási csomaggal és műszaki támogatásra van szüksége, [lépjen kapcsolatba velünk](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) egy támogatási jegy beküldésével.
    


