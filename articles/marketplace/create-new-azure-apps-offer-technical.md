---
title: Technikai részletek hozzáadása az Azure-alkalmazás ajánlatához
description: Megtudhatja, hogyan adhatja hozzá az Azure-alkalmazás ajánlatának technikai részleteit a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370238"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Technikai részletek hozzáadása az Azure-alkalmazás ajánlatához

Ez a cikk a Microsoft kereskedelmi piactérről a megoldáshoz való kapcsolódást segítő technikai részletek megadását ismerteti. Ez a kapcsolat lehetővé teszi, hogy az Ön ajánlatát kiépítse az ügyfélnek, ha úgy dönt, hogy beszerezze és felügyelje.

Csak akkor hajtsa végre ezt a szakaszt, ha az ajánlat olyan felügyelt alkalmazást tartalmaz, amely a [piactéren mért számlázási API](partner-center-portal/marketplace-metering-service-apis.md) -k használatával kibocsátja a mérési eseményeket, és rendelkezik egy olyan szolgáltatással, amely egy Azure ad biztonsági jogkivonattal lesz hitelesítve. További információ: a [piactér-mérési szolgáltatás hitelesítési stratégiái](partner-center-portal/marketplace-metering-service-authentication.md) a különböző hitelesítési beállításokon.

## <a name="technical-configuration-offer-level"></a>Technikai konfiguráció (ajánlat – szint)

A **technikai konfiguráció** lap csak akkor érvényes, ha olyan felügyelt alkalmazást hoz létre, amely mérési eseményeket bocsát ki a [piactér által mért számlázási API](partner-center-portal/marketplace-metering-service-apis.md)-k használatával. Ha igen, hajtsa végre az alábbi lépéseket. Ellenkező esetben folytassa a [következő lépésekkel](#next-steps). 

További információ ezekről a mezőkről: [Azure-alkalmazás ajánlatának megtervezése a kereskedelmi piactéren](plan-azure-application-offer.md#technical-configuration).

1. A **technikai konfiguráció** lapon adja meg a két szolgáltatás közötti kapcsolat ellenőrzéséhez használt **Azure Active Directory bérlői azonosítót** és **Azure Active Directory alkalmazás azonosítóját** egy hitelesített kommunikáció mögött.

1. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: terv áttekintése.

## <a name="next-steps"></a>Következő lépések

- [Csomagok létrehozása az Azure-alkalmazás ajánlatához](create-new-azure-apps-offer-plans.md)
