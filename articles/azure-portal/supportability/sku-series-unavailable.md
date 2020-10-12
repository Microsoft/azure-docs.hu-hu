---
title: A régió vagy SKU sorozata nem érhető el – Azure
description: Egyes SKU-sorozatok nem érhetők el a kiválasztott előfizetéshez ehhez a régióhoz, ami előfizetés-kezelési támogatási kérést igényelhet.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 87533b2e0c1397d0b8b2e9a89450a80a75c5e348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763822"
---
# <a name="region-or-sku-unavailable"></a>Nem elérhető régió vagy termékváltozat

Ez a cikk azt ismerteti, hogyan oldható fel egy olyan Azure-előfizetés problémája, amely nem fér hozzá egy adott régióhoz vagy virtuális géphez tartozó SKU-hoz.

## <a name="symptoms"></a>Hibajelenségek

Virtuális gép telepítésekor a következő hibaüzenetek egyike jelenik meg:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Fenntartott virtuálisgép-példányok vásárlása esetén a következő hibaüzenetek egyike jelenik meg:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Ha támogatási kérést hoz létre a számítási alapkvóta növeléséhez, egy régió vagy SKU-család nem érhető el a kiválasztáshoz.

## <a name="solution"></a>Megoldás

Először azt javasoljuk, hogy vegye fontolóra egy olyan alternatív régiót vagy SKU-t, amely megfelel az Ön üzleti igényeinek.

Ha nem talál megfelelő régiót vagy SKU-t, hozzon létre egy **előfizetés-kezelési** [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a következő lépésekkel:

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget. Ezután válassza az **új támogatási kérés**lehetőséget.

1. Az **alapok**területen a **probléma típusa**beállításnál válassza az **előfizetés-kezelés**lehetőséget.

1. Válasszon ki egy **előfizetést** , és adjon meg egy rövid leírást az **összegzésben**.

   ![Az új támogatási kérelem alapjai lap](./media/SKU-series-unavailable/support-request-basics.png)

1. A **probléma típusa**beállításnál válassza a **probléma típusának kiválasztása**lehetőséget.

1. A **probléma típusának kiválasztásához**válasszon egy beállítást, például nem **tud hozzáférni az előfizetéshez vagy az erőforráshoz**,  >  **a probléma nem szerepel a fenti listában**. Kattintson a **Mentés** gombra.

   ![Probléma megadása a kérelemhez](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Válassza a Next (tovább) lehetőséget **: megoldások** a lehetséges megoldások megismerésére. Ha szükséges, kattintson a **Next (tovább** ) gombra a folytatáshoz.

1. Adja meg a megadható további információkat, valamint a kapcsolattartási adatait.

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. Az adatok ellenőrzése után válassza a **Létrehozás** lehetőséget a kérelem létrehozásához.

## <a name="send-us-your-suggestions"></a>Küldje el nekünk javaslatait

Mindig nyitottak vagyunk visszajelzésre és javaslatokra! Küldje el nekünk [javaslatait](https://feedback.azure.com/forums/266794-support-feedback). Emellett a [Twitteren](https://twitter.com/azuresupport) vagy a [Microsoft Q&egy kérdés oldalon](https://docs.microsoft.com/answers/products/azure)is felveheti velünk a kapcsolatot.

## <a name="learn-more"></a>Tudjon meg többet

[Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq)
