---
title: Advisor-adatok az Azure Resource Graph-ban
description: Lekérdezések lekérdezése az Advisor-adatbázishoz az Azure Resource Graph-ban
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87057776"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Advisor-adatbázis lekérdezése az erőforrás-diagram Explorerben (Azure Resource Graph)

Az Advisor-erőforrások mostantól az [Azure Resource Graphba](https://azure.microsoft.com/features/resource-graph/)vannak bevezetve. Ez számos, az Advisor ajánlásaihoz tartozó, az ügyfelek számára készült forgatókönyvhöz biztosít alapot. Néhány forgatókönyv, amely még nem volt lehetséges a méretezés előtt, és most már elérhető az erőforrás-gráf használatával:
* Lehetővé teszi, hogy összetett lekérdezéseket végezzen az összes előfizetéséhez Azure Portal
* Kategóriák szerint összefoglalt ajánlások (például megbízhatóság, teljesítmény) és a hatások típusai (magas, közepes, alacsony)
* Egy adott javaslat típusára vonatkozó összes javaslat
* Érintett erőforrások száma javaslati kategória szerint

![Advisor az Azure Resource Graph Explorerben](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Advisor-erőforrástípusok az Azure Graph-ban

Elérhető Advisor-erőforrástípusok az [erőforrás-gráfban](../governance/resource-graph/index.yml): 3 erőforrástípus áll rendelkezésre az Advisor-erőforrások alatt történő lekérdezéshez. Az alábbi lista felsorolja az erőforrás-gráfban lekérdezéshez elérhető erőforrásokat.
* Microsoft. Advisor/konfigurációk
* Microsoft. Advisor/javaslatok
* Microsoft. Advisor/tiltások

Ezek az erőforrástípusok a AdvisorResources nevű új tábla alatt vannak felsorolva, amely a Azure Portal Resource Graph Explorerben is lekérdezhető.


## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Azure Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
