---
title: Szolgáltatások támogatása és kivonulás
titleSuffix: Azure Defender for IoT
description: A IoT Defender továbbra is támogatja a C, C# és Edge nyelveket, 2022. március 1-től.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809874"
---
# <a name="feature-support-and-retirement"></a>Szolgáltatások támogatása és kivonulás

Ez a cikk az Azure Defender IoT funkcióit és támogatását ismerteti a Defender for IoT különböző képességeihez.

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>Defender a IoT C, C# és Edge biztonsági moduljának elavulása esetén

Az új Micro Agent az aktuális C, C# és Edge biztonsági modult fogja cserélni.  

Az új Micro Agent az ismereteken alapul, és a kilépő biztonsági modul fejlesztését, az ügyfeleket és a partnerek visszajelzéseit négy fontos fejlesztéssel gyűjtötte: 

- **Mélységi biztonsági érték**: az új ügynök a gazdagép szintjén fog futni, ami nagyobb láthatóságot biztosít az eszköz mögöttes műveletei számára, és lehetővé teszi a jobb biztonsági lefedettséget.

- **Jobb teljesítmény és kisebb helyigény**: kis RAM-mal, a ROM memória-lábnyomával, valamint az alacsony CPU-fogyasztással érhető el.  

- **Plug and Play**: az új Micro Agent már nem rendelkezik kernel-szintű függőségekkel, és az összes szoftver-függősége a csomag részeként van megadva. A Micro Agent támogatja a gyakori CPU-architektúrát.

- **Könnyen üzembe helyezhető**: a Micro Agent különböző terjesztési modelleket támogat, forráskódon keresztül és bináris csomagként. 

### <a name="timeline"></a>Idővonal 

A IoT Defender továbbra is támogatja a C, C# és Edge nyelveket, 2022. március 1-től. 

## <a name="micro-agent-preview-support"></a>Micro Agent előzetes verziójának támogatása

Az előzetes verzió ideje alatt a Micro Agent értesítés nélkül is felmerülhet.

## <a name="next-steps"></a>Következő lépések

Tekintse meg [a Defender for IoT szenzor és a felügyeleti konzol API-](references-work-with-defender-for-iot-apis.md)jait.