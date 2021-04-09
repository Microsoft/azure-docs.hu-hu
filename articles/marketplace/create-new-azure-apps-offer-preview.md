---
title: Előzetes verzió célközönségének hozzáadása az Azure-alkalmazás ajánlatához
description: Megtudhatja, hogyan adhat hozzá előzetes verziójú célközönséget az Azure-alkalmazás ajánlatához a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370251"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Előzetes verzió célközönségének hozzáadása az Azure-alkalmazás ajánlatához

Ez a cikk azt ismerteti, hogyan konfigurálható az előzetes verzió célközönsége egy Azure-alkalmazás ajánlatához a kereskedelmi piactéren. Meg kell határoznia az előnézeti célközönséget, aki áttekintheti az ajánlati listát, mielőtt működésbe lép.

## <a name="define-a-preview-audience"></a>Előnézet célközönségének meghatározása

A **célközönség előnézete** lapon meghatározhatja, hogy az Azure-alkalmazásra vonatkozó ajánlata milyen mértékben tekinthető meg, mielőtt közzéteszi a piactéren a szélesebb körben. Az előnézeti célközönséget az Azure-előfizetési azonosítók használatával határozhatja meg, valamint az egyes verziók opcionális leírását is. Ezen mezők egyikét sem láthatja az ügyfelek. Az Azure-előfizetési azonosítót a Azure Portal **előfizetések** oldalán találja.

Vegyen fel legalább egy legfeljebb 10 Azure-előfizetés azonosítóját egyenként (legfeljebb 10), vagy töltsön fel egy CSV-fájlt (akár 100-ig) annak meghatározására, hogy ki láthatja el az ajánlatot az élő közzététel előtt. Ha az ajánlata már élő, akkor is megadhatja az előnézeti célközönséget az ajánlat változásainak vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége az online áruházakban való közzététel előtt jogosult az ajánlathoz való hozzáférésre. Megtekinthetik és ellenőrizhetik az összes csomagot, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlata teljes mértékben közzé lett téve a piactéren. A tervet csak privát célközönség számára lehet elérhetővé tenni. A saját célközönség (a csomag **rendelkezésre állási** lapján definiált) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

### <a name="add-email-addresses-manually"></a>E-mail címek manuális hozzáadása

1. A **célközönség előnézete** lapon adjon hozzá egy Azure-előfizetési azonosítót és egy opcionális leírást a megadott mezőkben.
1. Egy másik e-mail-cím hozzáadásához válassza az **Add ID (max 10)** hivatkozást.
1. A következő lapra történő továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: technikai konfiguráció.
1. Ugrás a [következő lépésekre](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>E-mail-címek hozzáadása a CSV-fájl használatával

1. A **célközönség előnézete** lapon válassza ki a **célközönség exportálása (CSV)** hivatkozást.
1. Nyissa meg a t. CSV-fájl egy alkalmazásban, például a Microsoft Excelben.
1. A-ben. CSV-fájl **azonosítója** oszlopban adja meg az előnézeti közönséghez hozzáadni kívánt Azure-előfizetési azonosítókat.
1. A **Leírás** oszlopban opcionálisan megadhatja az egyes e-mail-címek leírását.
1. A **Type (típus** ) oszlopban adja hozzá a **SubscriptionID** minden olyan sorhoz, amelyhez e-mail-cím tartozik.
1. Mentse a fájlt. CSV-fájl.
1. A **célközönség előnézete** lapon válassza ki a **célközönség importálása (CSV)** hivatkozást.
1. A **megerősítés** párbeszédpanelen válassza az **Igen** lehetőséget.
1. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget.
1. A következő lapra történő továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: technikai konfiguráció.

## <a name="next-steps"></a>Következő lépések

- [Technikai részletek hozzáadása az Azure-alkalmazás ajánlatához](create-new-azure-apps-offer-technical.md)
