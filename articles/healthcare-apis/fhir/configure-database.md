---
title: Adatbázis-beállítások konfigurálása a FHIR készült Azure API-ban
description: Ez a cikk bemutatja, hogyan konfigurálhatja az adatbázis-beállításokat a FHIR készült Azure API-ban
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3ce51141f20d2e032cb255f53efe3cb63882f1b2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018710"
---
# <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása 

Az Azure API for FHIR az adatbázis használatával tárolja az adattárolási adatbázist. Az alapul szolgáló adatbázis teljesítménye attól függ, hogy a szolgáltatás üzembe helyezése vagy a szolgáltatás üzembe helyezése után hány kérési egység (RU) van kiválasztva.

A FHIR készült Azure API a Cosmos DB (lásd a [kérelmek egységeit Azure Cosmos db](../../cosmos-db/request-units.md)) az alapul szolgáló adatbázis teljesítményének beállításakor. 

Az átviteli sebességet úgy kell kiépíteni, hogy mindig elegendő rendszererőforrás álljon rendelkezésre az adatbázishoz. Az alkalmazástól függően hány RUs szükséges a végrehajtott műveletekhez. A műveletek az egyszerű olvasás és írások között összetettebb lekérdezésekre is terjedhetnek. 

> [!NOTE]
> Mivel a különböző műveletek különböző számú RU-t használnak, a válasz fejlécében szereplő összes API-hívásban felhasznált RUs tényleges számát fogjuk visszaadni. Így megtekintheti az alkalmazás által felhasznált RUs számát.

## <a name="update-throughput"></a>Frissítési sebesség

Ha módosítani szeretné ezt a beállítást a Azure Portalban, lépjen a FHIR Azure API-ra, és nyissa meg az adatbázis panelt. Ezután módosítsa a kiosztott átviteli sebességet a kívánt értékre a teljesítmény igényeitől függően. Az értéket legfeljebb 10 000 RU/s értékre módosíthatja. Ha nagyobb értékre van szüksége, forduljon az Azure ügyfélszolgálatához.

Ha az adatbázis átviteli sebessége nagyobb, mint 10 000 RU/s, vagy ha az adatbázisban tárolt adatok száma meghaladja az 50 GB-ot, az ügyfélalkalmazás képesnek kell lennie a folytatási tokenek kezelésére. A rendszer létrehoz egy új partíciót az adatbázisban minden 10 000 RU/s érték növeléséhez, vagy ha a tárolt adatok mennyisége meghaladja az 50 GB-ot. Több partíció létrehoz egy többoldalas választ, amelyben a tördelés a folytatási jogkivonatok használatával valósul meg.

> [!NOTE] 
> A magasabb érték azt jelenti, hogy magasabb szintű Azure API-t biztosít a FHIR átviteli sebességhez és a szolgáltatás magasabb díjszabásához.

![Konfiguráció Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan frissítheti a FHIR készült Azure API-t. Az ügyfél által felügyelt kulcsok adatbázis-beállításként való konfigurálásának megismeréséhez:

>[!div class="nextstepaction"]
>[Ügyfél által felügyelt kulcsok konfigurálása](customer-managed-key.md)

Vagy üzembe helyezhet egy teljes körűen felügyelt Azure API-t a FHIR-hez:
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)