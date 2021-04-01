---
title: Az Azure webalkalmazási tűzfal bemutatása
description: Ez a cikk áttekintést nyújt az Azure webalkalmazási tűzfal (WAF) szolgáltatásról
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475787"
---
# <a name="what-is-azure-web-application-firewall"></a>Mi az az Azure Web Application Firewall?

A Web Application Firewall (WAF) központosított védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen. A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. Az SQL-injektálás és a helyek közötti parancsfájlkezelés a leggyakoribb támadások közé tartozik.

![A WAF áttekintése](media/overview/wafoverview.png)

Az ilyen támadások megelőzése az alkalmazás kódjában kihívást jelent. Az alkalmazás topológiájának több rétegében szigorú karbantartási, javítási és figyelési funkciókra lehet szükség. A központosított webalkalmazási tűzfal sokkal egyszerűbbé teszi A biztonsági felügyeletet. A WAF lehetővé teszi, hogy az alkalmazás-rendszergazdák jobban meglegyenek a fenyegetésekkel és a behatolásokkal szembeni védelem.

Az egyes webalkalmazások biztonságossá tétele helyett a WAF-megoldás gyorsabban reagálhat a biztonsági fenyegetésekre, ha központilag javít egy ismert biztonsági rést.

## <a name="supported-service"></a>Támogatott szolgáltatás

A WAF az Azure Application Gateway, az Azure bejárati ajtóval és az Azure Content Delivery Network (CDN) szolgáltatással is üzembe helyezhető a Microsofttól. A WAF on Azure CDN jelenleg nyilvános előzetes verzióban érhető el.  A WAF az egyes szolgáltatásokhoz testreszabott funkciókat tartalmaz. Az egyes szolgáltatások WAF funkcióival kapcsolatos további információkért tekintse meg az egyes szolgáltatások áttekintését.

## <a name="next-steps"></a>Következő lépések

- A Application Gateway webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal az Azure Application Gateway](./ag/ag-overview.md).
- A webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal az Azure bejárati ajtó szolgáltatásban](./afds/afds-overview.md).
- A Azure CDN szolgáltatás webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal a Azure CDN szolgáltatásban](./cdn/cdn-overview.md)
