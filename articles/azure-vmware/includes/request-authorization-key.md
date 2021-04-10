---
title: ExpressRoute-kérelem engedélyezési kulcsának kérése
description: A ExpressRoute tartozó engedélyezési kulcs igénylésének lépései.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026973"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. A Azure Portal a **kapcsolat** szakaszban, a **ExpressRoute** lapon válassza a **+ engedélyezési kulcs kérése** lehetőséget. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="A ExpressRoute-engedélyezési kulcs igénylését bemutató képernyőkép." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Adjon meg egy nevet, és válassza a **Létrehozás** lehetőséget. 
      
   A kulcs létrehozása körülbelül 30 másodpercig is eltarthat. A létrehozást követően az új kulcs megjelenik a privát felhőhöz tartozó engedélyezési kulcsok listájában.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="A ExpressRoute Global Reach engedélyezési kulcsát ábrázoló képernyőkép.":::
  
1. Másolja az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját. Ezeket a társítások befejezéséhez fogja használni.  

   > [!NOTE]
   > Az engedélyezési kulcs némi idő elteltével eltűnik, ezért másolja azt, amint megjelenik.