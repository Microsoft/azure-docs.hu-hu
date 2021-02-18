---
title: 'Azure-beli bejárati ajtó: Endpoint Manager'
description: Ez a cikk áttekintést nyújt az Azure-beli első ajtós végpont-kezelőről.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100097"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Mi az Azure bejárati ajtó standard/Premium (előzetes verzió) Endpoint Manager?

> [!NOTE]
> * Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő [dokumentumokat](../front-door-overview.md).

A Endpoint Manager áttekintést nyújt az Azure-előtérben konfigurált végpontokról. A végpont egy tartomány és a hozzájuk társított konfigurációk logikai csoportosítása. A Endpoint Manager segítségével kezelheti a szifilisz-(létrehozási, olvasási, frissítési és törlési) végpontok gyűjteményét. A végpontok következő elemeit a Endpoint Manager használatával kezelheti:

* Tartományok
* Origó csoportok
* Útvonalak
* Biztonság

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Képernyőkép az Endpoint Managerről konfigurációk nélkül." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

A Endpoint Manager-lista azt sorolja fel, hogy az egyes elemek hány példánya legyen létrehozva egy végponton belül. Az egyes elemek társítási állapota is megjelenik. Létrehozhat például több tartományt és erőforráscsoportot, és hozzárendelheti a társítást különböző útvonalakkal.

> [!IMPORTANT]
> * Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [**Microsoft Azure előzetes verziójának kiegészítő használati feltételei**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Csatolt nézet

A végpont-kezelőn belüli csatolt nézettel könnyedén azonosíthatja az Azure-beli ajtó elemei közötti társítást, például:

* Mely tartományok vannak társítva az aktuális végponthoz?
* Melyik forrástartomány van társítva a tartományhoz?
* Melyik WAF-házirend van társítva a tartományhoz?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="A Endpoint Manager és a konfigurációk közötti képernyőkép." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
