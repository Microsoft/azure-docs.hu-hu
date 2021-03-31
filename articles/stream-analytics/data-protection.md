---
title: Adatvédelem a Azure Stream Analyticsban
description: Ez a cikk azt ismerteti, hogyan titkosíthatja a Azure Stream Analytics feladatok által használt személyes adatait.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019465"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Adatvédelem a Azure Stream Analyticsban 

A Azure Stream Analytics egy teljes körűen felügyelt platform-szolgáltatás, amellyel valós idejű elemzési folyamatokat hozhat létre. Az összes nehéz megoldás, mint például a fürt üzembe helyezése, a csomópontok méretezése a használathoz, és a belső ellenőrzőpontok kezelése a háttérben történik.

## <a name="private-data-assets-that-are-stored"></a>Tárolt privát adategységek

A Azure Stream Analytics a következő metaadatokat és adatokat őrzi meg a futtatásához: 

* Lekérdezés definíciója és kapcsolódó konfigurációja  

* Felhasználó által definiált függvények vagy összesítések  

* Az Stream Analytics futtatókörnyezet által igényelt ellenőrzőpontok

* A hivatkozási adatpillanatképek 

* A Stream Analytics-feladathoz használt erőforrások kapcsolati adatai

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>Adattárolás In-Region
A Azure Stream Analytics az ügyféladatokat és a fent leírt egyéb metaadatokat tárolja. A rendszer alapértelmezés szerint egyetlen régióban Azure Stream Analytics tárolja az ügyféladatokat, így a szolgáltatás automatikusan megfelel a régiókban tárolt adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.
Emellett dönthet úgy is, hogy a stream Analytics-feladathoz kapcsolódó összes adategységet (ügyféladatokat és egyéb metaadatokat) egyetlen régióban tárolja úgy, hogy az Ön által választott Storage-fiókban titkosítja őket.

## <a name="encrypt-your-data"></a>Adatok titkosítása

A Stream Analytics automatikusan alkalmazza a legjobb színvonalú titkosítási szabványokat az infrastruktúrájában, hogy titkosítsa és védje az adatait. Egyszerűen megbízhat Stream Analytics az összes adatainak biztonságos tárolásához, így nem kell aggódnia az infrastruktúra kezelésével kapcsolatban.

Ha ügyfél által felügyelt kulcsokat kíván használni az adatok titkosításához, használhatja a saját Storage-fiókját (általános célú v1 vagy v2) a Stream Analytics futtatókörnyezet által igényelt személyes adategységek tárolásához. A Storage-fiók igény szerint titkosítható. A privát adategységek egyikét sem tárolja véglegesen a Stream Analytics-infrastruktúra. 

Ezt a beállítást a Stream Analytics feladatok létrehozásának időpontjában kell konfigurálni, és nem módosítható a feladatok életciklusa során. A Stream Analytics által használt tárterület módosítása vagy törlése nem ajánlott. Ha törli a Storage-fiókját, akkor véglegesen törli az összes magánjellegű adategységet, ami miatt a feladat sikertelen lesz. 

A Storage-fiók kulcsainak frissítése vagy elforgatása nem lehetséges a Stream Analytics portál használatával. A kulcsokat a REST API-k használatával frissítheti.


### <a name="configure-storage-account-for-private-data"></a>A Storage-fiók konfigurálása magánjellegű adatként 

Titkosítsa a Storage-fiókját, hogy biztosítsa az összes adatait, és explicit módon válassza ki a személyes adatai helyét. 

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

A következő lépésekkel konfigurálhatja a Storage-fiókot a privát adategységekhez. Ez a konfiguráció a Stream Analyticsi feladatokból, és nem a Storage-fiókból történik.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra. 

1. Válassza az **elemzési**   >  **stream Analytics feladatot**   az eredmények listából. 

1. Töltse ki a Stream Analytics feladatot tartalmazó oldalt a szükséges adatokkal, például a név, a régió és a skála lehetőséggel. 

1. Jelölje be a jelölőnégyzetet, amely azt adja meg, hogy a *jelen feladatokban a saját Storage-fiókomban szükséges összes privát adategység biztonságos* legyen.

1. Válasszon egy Storage-fiókot az előfizetésből. Vegye figyelembe, hogy ez a beállítás a feladatok életciklusa során nem módosítható. Ezt a beállítást a feladatok létrehozása után is nem lehet hozzáadni.

1. A kapcsolódási karakterlánccal való hitelesítéshez válassza a **kapcsolódási karakterlánc** lehetőséget a hitelesítési mód legördülő listából. A rendszer automatikusan kitölti a Storage-fiók kulcsát az előfizetésből.

   ![Magánjellegű adattárolási fiók beállításai](./media/data-protection/storage-account-create.png)

1. A felügyelt identitás (előzetes verzió) használatával történő hitelesítéshez válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő listából. Ha a felügyelt identitást választja, hozzá kell adnia a Stream Analytics feladatot a Storage-fiók hozzáférés-vezérlési listájához a *Storage blob adatközreműködői* szerepkörével. Ha nem adja meg a feladathoz való hozzáférést, a feladatnak nem lesz lehetősége semmilyen művelet végrehajtására. További információ a hozzáférés engedélyezéséről: az [Azure RBAC használata egy felügyelt identitáshoz való hozzáférés másik erőforráshoz való hozzárendeléséhez](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Személyes adattárolási fiókok beállításai felügyelt identitásos hitelesítéssel":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>A Stream Analytics által tárolt privát adategységek

Az Stream Analytics által megőrzött személyes adatait a Storage-fiókban kell tárolni. A privát adategységek például a következők: 

* A létrehozott lekérdezések és a hozzájuk kapcsolódó konfigurációk  

* Felhasználó által definiált függvények 

* Az Stream Analytics futtatókörnyezet által igényelt ellenőrzőpontok

* A hivatkozási adatpillanatképek 

A Stream Analytics-feladatban használt erőforrások kapcsolati adatait is tárolja a rendszer. Titkosítsa a Storage-fiókját az összes adatai védelméhez. 

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Az adattárolás engedélyezése 
Ezt a szolgáltatást használhatja arra, hogy kikényszerítse az esetlegesen szükséges adattárolási követelményeket, ha ennek megfelelően biztosít egy Storage-fiókot.

## <a name="known-issues"></a>Ismert problémák
Ismert hiba történt, amikor az ügyfél által felügyelt kulcsot használó feladatok hibákba ütközik, amikor felügyelt identitást használ a bemenetek és kimenetek hitelesítéséhez. 

## <a name="next-steps"></a>Következő lépések

* [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)
* [Azure Stream Analytics-bemenetek ismertetése](stream-analytics-add-inputs.md)
* [Ellenőrzőpont-és visszajátszás-fogalmak a Azure Stream Analytics-feladatokban](stream-analytics-concepts-checkpoint-replay.md)
* [A Stream Analytics-keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)
