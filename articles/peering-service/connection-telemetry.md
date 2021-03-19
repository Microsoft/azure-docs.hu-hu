---
title: Azure-partneri szolgáltatási kapcsolatok telemetria
description: További tudnivalók a Microsoft Azure társ-szolgáltatási kapcsolatok telemetria
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872007"
---
# <a name="peering-service-connection-telemetry"></a>Társ-szolgáltatási kapcsolatok telemetria

A kapcsolati telemetria az ügyfél és a Microsoft hálózata közötti kapcsolathoz nyújt bepillantást. Az ügyfelek az Azure-partneri szolgáltatási kapcsolatok telemetria szerezhetik be, ha regisztrálják a kapcsolatot a Azure Portalban. Ez a szolgáltatás előtag-biztonságot és betekintést biztosít a hálózat teljesítményére.


A kapcsolatok telemetria a következő hatókörökből áll:

### <a name="latency-measurement"></a>Késés mérése

 A késést az ügyfélről a Microsoft Edge PoP-ra mérjük a partneri szolgáltatáson belüli regisztrált előtagoknál.

### <a name="route-prefix-monitoring-and-protection"></a>Útvonal-előtag figyelése és védelme

Az útválasztási útvonalakat a rendszer az eseménynaplókban rögzített gyanús tevékenységek esetében figyeli. Az eseménynaplók például a következő tényezőkhöz jönnek létre:

- Előtag-eltérítések
- Előtag-visszavonás
- Útvonal-szivárgások

## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatok megismeréséhez tekintse meg a társítási [szolgáltatás kapcsolódása](connection.md)című témakört.
- A társ-szolgáltatási kapcsolatok bevezetéséhez tekintse meg a következő témakört: bevezetési [szolgáltatási modell](onboarding-model.md).
- A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
