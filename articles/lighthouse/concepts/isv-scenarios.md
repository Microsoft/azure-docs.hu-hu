---
title: Azure Lighthouse ISV-forgatókönyvekben
description: Az Azure Lighthouse képességei az ISV-kal nagyobb rugalmasságot és az ügyfelek ajánlatait is használhatják.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696354"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse ISV-forgatókönyvekben

Az [Azure Lighthouse](../overview.md) általános forgatókönyve az ügyfelek Azure Active Directory (Azure ad) bérlők erőforrásait kezelő szolgáltató. Az Azure Lighthouse képességeit a független szoftvergyártók (ISV-ket) is használhatják, SaaS-alapú ajánlatokat használva ügyfeleiknek. Az Azure Lighthouse különösen hasznos lehet olyan független szoftvergyártók számára, akik olyan felügyelt szolgáltatásokat vagy támogatást kínálnak, amelyek hozzáférést igényelnek az előfizetési hatókörhöz.

## <a name="managed-service-offers-in-azure-marketplace"></a>Felügyelt szolgáltatási ajánlatok az Azure Marketplace-en

A SZOFTVERGYÁRTÓk már közzétett megoldásokat is használhatnak az Azure Marketplace-en. Ha felügyelt szolgáltatásokat kínál ügyfeleinek, akkor ezt egy felügyelt szolgáltatási ajánlat közzétételével teheti meg. Ezek egyszerűbbé teszik a bevezetési folyamatot, és a szolgáltatások méretezhetőségét a lehető legtöbb ügyfél számára teszi elérhetővé. Az Azure Lighthouse számos [felügyeleti feladatot és forgatókönyvet](cross-tenant-management-experience.md#enhanced-services-and-scenarios) támogat, amelyek segítségével értéket biztosíthat ügyfeleinek.

További információ: [felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace](../how-to/publish-managed-services-offers.md)-en.

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Az Azure Lighthouse használata Azure-beli felügyelt alkalmazásokkal

Az [Azure által felügyelt alkalmazások](../../azure-resource-manager/managed-applications/overview.md) egy másik módszer, amellyel az ISV szolgáltatásokat biztosíthat ügyfeleinek. Az Azure Lighthouse és az Azure által felügyelt alkalmazásai is használhatók a továbbfejlesztett forgatókönyvek engedélyezéséhez.

További információ: az [Azure Lighthouse és az Azure által felügyelt alkalmazások](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>SaaS-alapú több-bérlős ajánlatok

Egy további forgatókönyv, ahol az ISV a saját bérlőn lévő előfizetésben lévő erőforrásokat üzemelteti, majd az Azure Lighthouse használatával teszi lehetővé az ügyfelek számára a hozzáférést ezekhez az erőforrásokhoz. Az ügyfél ezután bejelentkezhet a saját bérlőbe, és igény szerint hozzáférhet ezekhez az erőforrásokhoz. Az ISV-k saját maguk is fenntartják az IP-címet a saját bérlőn, és használhatják a saját támogatási csomagjaikat a bérlőn üzemeltetett megoldáshoz kapcsolódó jegyek előfizetésére, az ügyfél csomagjának használata helyett. Mivel az erőforrások az ISV bérlőben találhatók, az összes műveletet közvetlenül az ISV-k is elvégezhetik, például virtuális gépekbe való bejelentkezéssel, alkalmazások telepítésével és karbantartási feladatok végrehajtásával.

Ebben az esetben az ügyfél bérlője számára alapvetően a "bérlők kezelése" jogosultságot biztosítanak, noha az ügyfél nem kezeli az ISV erőforrásait. Mivel közvetlenül férnek hozzá az ISV bérlőhöz, fontos, hogy csak a minimálisan szükséges engedélyeket adja meg, így az ügyfelek nem tudják véletlenül módosítani a megoldást vagy más ISV-erőforrásokat.

Ennek az architektúrának az engedélyezéséhez az ISV-nek be kell szereznie az ügyfél Azure AD-bérlőben található felhasználói csoportjának objektumazonosító-AZONOSÍTÓját, valamint a bérlői AZONOSÍTÓját. Az ISV Ezután létrehoz egy ARM-sablont, amely megadja ezt a felhasználói csoportot a megfelelő engedélyekkel, és [telepíti azt az ISV előfizetésében](../how-to/onboard-customer.md) , amely tartalmazza az ügyfél által elérni kívánt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Megismerheti az [Azure által delegált erőforrás-kezelés](azure-delegated-resource-management.md) részleteit.