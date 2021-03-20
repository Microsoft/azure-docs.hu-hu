---
title: Több-bérlős alkalmazások forgalmának kezelése a portál használatával
titleSuffix: Azure Application Gateway
description: Ez a cikk útmutatást nyújt ahhoz, hogyan konfigurálhatja az Azure app Service Web Apps-t a háttér-készlet tagjaiként egy meglévő vagy egy új Application Gateway-átjárón.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97854910"
---
# <a name="configure-app-service-with-application-gateway"></a>App Service konfigurálása Application Gateway

Mivel az App Service egy több-bérlős szolgáltatás és nem dedikált üzemelő példány, az állomásfejlécet használja a bejövő kérésben a megfelelő App Service-végpontnak küldött kérés feloldásához. Az alkalmazás DNS-neve általában az App Service-hez kapcsolódó Application Gateway-hez társított DNS-név, amely eltér a háttér-alkalmazás szolgáltatás tartománynevétől. Ezért az Application Gateway által fogadott eredeti kérelemben szereplő állomásfejléc nem egyezik meg a háttér-szolgáltatás állomásneve nevével. Emiatt, ha az Application Gateway-ből a háttérbe irányuló kérelemben szereplő állomásfejléc nem változik a háttérrendszer állomásneve, a több-bérlős háttérrendszer nem tudja feloldani a kérést a megfelelő végpontra.

A Application Gateway egy nevű kapcsolót biztosít `Pick host name from backend target` , amely felülbírálja a kérelemben található állomásfejléc állomásnevét, ha a kérést a Application Gateway a háttérbe irányítja. Ez a funkció lehetővé teszi a több-bérlős back-végpontok támogatását, például az Azure app Service és az API Management használatát. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

- Háttérbeli készlet szerkesztése és App Service hozzáadása
- HTTP-beállítások szerkesztése a "pick hostname" kapcsoló engedélyezésével

## <a name="prerequisites"></a>Előfeltételek

- Application Gateway: hozzon létre egy Application Gateway-t a háttér-készlet célja nélkül. További információ [: gyors webes forgalom az Azure Application Gateway-Azure Portal](quick-create-portal.md)

- App Service: Ha nem rendelkezik meglévő app Service-szolgáltatással, tekintse meg az [app Service dokumentációját](../app-service/index.yml).

## <a name="add-app-service-as-backend-pool"></a>App Service hozzáadása háttér-készletként

1. A Azure Portal válassza ki az Application Gateway-t.

2. A **háttér-készletek** területen válassza ki a háttér-készletet.

4. A **cél típusa** területen válassza a **app Services** lehetőséget.

5. A **cél** területen válassza ki a app Service.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App Service-háttérrendszer":::
   
   > [!NOTE]
   > A legördülő lista csak azokat az alkalmazás-szolgáltatásokat tölti fel, amelyek ugyanabban az előfizetésben vannak, mint a Application Gateway. Ha olyan app Service-t szeretne használni, amely egy másik előfizetésben található, mint a Application Gateway, **app Services** akkor a **célok** legördülő menüben válassza az **IP-cím vagy állomásnév** lehetőséget, és adja meg a hostname (példa. azurewebsites.net) az App Service-ben.
1. Kattintson a **Mentés** gombra.

## <a name="edit-http-settings-for-app-service"></a>App Service HTTP-beállításainak szerkesztése

1. A **http-beállítások** területen válassza ki a meglévő http-beállítást.

2. A **felülbírálás új állomásnévvel** területen válassza az **Igen** lehetőséget.
3. Az **állomásnév felülbírálása** területen válassza **az állomásnév kiválasztása a háttérbeli célként** lehetőséget.
4. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Állomásnév kiválasztása a háttérbeli http-beállításokból":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>További konfiguráció az App Service relatív elérési útjára történő átirányítás esetén

Amikor az App Service átirányítási választ küld az ügyfélnek, hogy átirányítsa a relatív elérési útjára (például egy átirányításról `contoso.azurewebsites.net/path1` `contoso.azurewebsites.net/path2` ), ugyanazt a gazdagépet használja a válaszának Location fejlécében, mint az Application gatewaytől kapott kérésben. Így az ügyfél a kérést közvetlenül az `contoso.azurewebsites.net/path2` Application Gateway () szolgáltatáson keresztül teszi elérhetővé `contoso.com/path2` . Az Application Gateway megkerülése nem kívánatos.

Ha használja a használati esetet, vannak olyan helyzetek, amikor az App Service-nek át kell küldenie egy átirányítási választ az ügyfélnek, a [további lépéseket végrehajtva írja felül a hely fejlécét](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Hozzáférés korlátozása

A példákban telepített webalkalmazások nyilvános IP-címeket használnak, amelyek közvetlenül az internetről érhetők el. Ez segít a hibaelhárításban az új funkciók megismerése és új dolgok kipróbálása során. Ha azonban éles környezetben szeretne üzembe helyezni egy szolgáltatást, további korlátozásokat is fel szeretne venni.

A webalkalmazásokhoz való hozzáférés korlátozásának egyik módja a [Azure app Service statikus IP-korlátozások](../app-service/app-service-ip-restrictions.md)használata. Például korlátozhatja a webalkalmazást úgy, hogy az csak az Application gatewaytől kapjon forgalmat. Az App Service IP-korlátozási funkciója segítségével az Application Gateway VIP-t csak az elérési címmel listázhatja.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az App Service-ről és az Application Gateway további több-bérlős támogatásáról, tekintse meg a [több-bérlős szolgáltatás támogatása az Application gatewayrel](./application-gateway-web-app-overview.md)című témakört.
