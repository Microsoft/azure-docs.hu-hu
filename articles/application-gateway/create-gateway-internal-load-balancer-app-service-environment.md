---
title: Azure-beli Application Gateway-ILB Microsoft Docs
description: Megtudhatja, hogyan végezhet hibakeresést egy Application Gateway használatával egy belső Load Balancer az Azure-ban App Service Environment
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84628534"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>A háttér-kiszolgálói tanúsítvány nem engedélyezett olyan Application Gateway számára, amely egy belső Load Balancert használ App Service Environment

Ez a cikk a következő problémát ismerteti: A tanúsítvány nem engedélyezett, ha egy belső Load Balancer (ILB) és egy App Service Environment (bevezető) használatával hoz létre egy alkalmazás-átjárót, ha az Azure-ban végpontok közötti TLS-t használ.

## <a name="symptoms"></a>Hibajelenségek

Amikor egy ILB használatával hoz létre egy Application Gateway-t, a háttérrendszer a háttér-kiszolgálóval is megsérült. Ez a probléma akkor fordul elő, ha az Application Gateway hitelesítési tanúsítványa nem egyezik meg a háttér-kiszolgálón konfigurált tanúsítvánnyal. Példaként tekintse meg a következő forgatókönyvet:

**Application Gateway konfiguráció:**

- **Figyelő:** Több hely
- **Port:** 443
- **Állomásnév:** test.appgwtestase.com
- **SSL-tanúsítvány:** CN = test. appgwtestase. com
- **Háttér-készlet:** IP-cím vagy teljes tartománynév
- **IP-cím:**: 10.1.5.11
- **Http-beállítások:** HTTPS
- **Port:**: 443
- **Egyéni mintavétel:** Állomásnév – test.appgwtestase.com
- **Hitelesítési tanúsítvány:** . cer – test.appgwtestase.com
- **Háttér állapota:** Sérült – a háttér-kiszolgáló tanúsítványa nem engedélyezett a Application Gateway.

**Bemutató konfiguráció:**

- **ILB IP-cím:** 10.1.5.11
- **Tartománynév:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL-kötés:** SNI SSL – CN = test. appgwtestase. com

Az Application Gateway eléréséhez a következő hibaüzenet jelenik meg, mert a háttér-kiszolgáló állapota nem kifogástalan:

**502 – a webkiszolgáló érvénytelen választ kapott az átjáróként vagy proxykiszolgálóként való működés közben.**

## <a name="solution"></a>Megoldás

Ha nem használ állomásnévt egy HTTPS-webhely eléréséhez, a háttér-kiszolgáló az alapértelmezett webhelyen visszaadja a konfigurált tanúsítványt, abban az esetben, ha a SNI le van tiltva. Egy ILB-benyújtó esetében az alapértelmezett tanúsítvány az ILB-tanúsítványból származik. Ha nincsenek konfigurált tanúsítványok a ILB, a tanúsítvány a benyújtó alkalmazás-tanúsítványból származik.

Ha teljes tartománynevet (FQDN) használ a ILB eléréséhez, a háttér-kiszolgáló a HTTP-beállításokban feltöltött megfelelő tanúsítványt fogja visszaadni. Ha ez nem igaz, vegye figyelembe a következő lehetőségeket:

- Használja a teljes tartománynevet az Application Gateway háttér-készletében, hogy a ILB IP-címére mutasson. Ez a beállítás csak akkor működik, ha rendelkezik saját DNS-zónával vagy egyéni DNS-kiszolgálóval. Ellenkező esetben létre kell hoznia egy "A" rekordot a nyilvános DNS-hez.

- Használja a feltöltött tanúsítványt a ILB vagy az alapértelmezett tanúsítványon (ILB-tanúsítványon) a HTTP-beállítások között. Az Application Gateway lekéri a tanúsítványt, amikor hozzáfér a ILB IP-címéhez a mintavételhez.

- Használjon helyettesítő tanúsítványt a ILB és a háttér-kiszolgálón, hogy az összes webhelyhez a tanúsítvány gyakori legyen. Ez a megoldás azonban csak altartományok esetén lehetséges, és nem, ha az egyes webhelyekhez eltérő állomásnevek szükségesek.

- Ha a ILB IP-címét használja, törölje az **app Service-hez való használat** beállítását az Application Gateway esetében.

A terhelés csökkentése érdekében feltöltheti a ILB-tanúsítványt a HTTP-beállításokba a mintavételi útvonal működésének érdekében. (Ez a lépés csak a listaelemek engedélyezésére szolgál. A TLS-kommunikációhoz nem használható.) A ILB-tanúsítvány lekéréséhez nyissa meg az IP-címét a ILB a HTTPS-en keresztül, majd exportálja a TLS/SSL-tanúsítványt egy Base-64 kódolású CER formátumba, és töltse fel a tanúsítványt a megfelelő HTTP-beállításokra.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
