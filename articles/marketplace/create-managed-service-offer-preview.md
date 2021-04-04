---
title: Előzetes verzió célközönségének hozzáadása a felügyelt szolgáltatási ajánlathoz
description: Megtudhatja, hogyan adhat hozzá előnézeti célközönséget a felügyelt szolgáltatási ajánlathoz a Microsoft partner Centerben.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918278"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Előzetes verzió célközönségének hozzáadása a felügyelt szolgáltatási ajánlathoz

Ez a cikk azt ismerteti, hogyan konfigurálható az előzetes verziójú célközönség egy felügyelt szolgáltatási ajánlathoz a kereskedelmi piactéren a partner Center használatával. Az előzetes verzió célközönsége még az azonnali működés előtt áttekintheti az ajánlatot.

## <a name="define-a-preview-audience"></a>Előnézet célközönségének meghatározása

A **célközönség előnézete** lapon meghatározhatja a felügyelt szolgáltatás ajánlatát, mielőtt közzéteszi azt a szélesebb körű piactéren. Az előnézeti célközönséget az Azure-előfizetési azonosítók használatával határozhatja meg, valamint az egyes verziók opcionális leírását is. Ezen mezők egyikét sem láthatja az ügyfelek. Az Azure-előfizetés AZONOSÍTÓját a Azure Portal **előfizetések** oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10), vagy egy CSV-fájl feltöltésével (legfeljebb 100) annak meghatározásához, hogy ki tekintheti meg az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlat már élő, akkor is megadhatja az előzetes verzió célközönségét az ajánlat frissítéseinek teszteléséhez.

> [!NOTE]
> Az *előzetes* verzió célközönsége különbözik a *privát* közönségtől. Az előzetes verzió célközönsége az online áruházakban való közzététel előtt jogosult az ajánlathoz való hozzáférésre. Megtekinthetik és ellenőrizhetik az összes csomagot, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlata teljes mértékben közzé lett téve a piactéren. A tervet csak privát célközönség számára lehet elérhetővé tenni. A saját célközönség (a csomag rendelkezésre állási lapján definiált) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

## <a name="add-email-addresses-manually"></a>E-mail címek manuális hozzáadása

1. A **célközönség előnézete** lapon adjon hozzá egy Azure-előfizetési azonosítót és egy opcionális leírást a megadott mezőkben.
2. Egy másik e-mail-cím hozzáadásához válassza az **Add ID (max 10)** hivatkozást.
3. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="add-email-addresses-using-a-csv-file"></a>E-mail címek hozzáadása CSV-fájl használatával

1. A célközönség előzetes verziójának **célközönsége** lapon válassza a **célközönség exportálása (CSV)** hivatkozást.
2. Nyissa meg a CSV-fájlt. Az **azonosító** oszlopban adja meg az előnézeti közönséghez hozzáadni kívánt Azure-előfizetési azonosítókat.
3. A **Leírás** oszlopban lehetősége van az egyes bejegyzések leírásának hozzáadására.
4. A **Type (típus** ) oszlopban adja hozzá a **SubscriptionId** minden olyan SORHOZ, amely rendelkezik azonosítóval.
5. Mentse a fájlt CSV-fájlként.
6. A **célközönség előnézete** lapon válassza ki a **célközönség importálása (CSV)** hivatkozást.
7. A **megerősítés** párbeszédpanelen válassza az **Igen**, majd a CSV-fájl feltöltése elemet.
8. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Csomagok létrehozása](create-managed-service-offer-plans.md)
