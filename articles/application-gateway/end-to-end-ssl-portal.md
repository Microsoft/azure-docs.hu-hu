---
title: Végpontok közötti TLS-titkosítás konfigurálása a portál használatával
titleSuffix: Azure Application Gateway
description: Ebből a témakörből megtudhatja, hogyan hozhat létre teljes körű TLS-titkosítást használó Application Gateway-t a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397501"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Teljes körű TLS konfigurálása az Application Gateway használatával a portálon

Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Portal a végpontok közötti Transport Layer Security (TLS) titkosítás, korábbi nevén SSL (SSL) titkosítás az Azure Application Gateway v1 SKU használatával.

> [!NOTE]
> Application Gateway v2 SKU-nak megbízható legfelső szintű tanúsítványokra van szüksége a végpontok közötti konfiguráció engedélyezéséhez.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

A végpontok közötti TLS Application Gateway-sel való konfigurálásához tanúsítvány szükséges az átjáróhoz. A háttér-kiszolgálókhoz is szükség van tanúsítványokra. Az átjáró tanúsítványa szimmetrikus kulcs származtatása a TLS protokoll specifikációjának megfelelően történik. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. 

A végpontok közötti TLS-titkosításhoz a megfelelő háttér-kiszolgálókat engedélyezni kell az Application gatewayben. A hozzáférés engedélyezéséhez töltse fel a háttér-kiszolgálók, más néven hitelesítési tanúsítványok (v1) vagy megbízható főtanúsítványok (v2) nyilvános tanúsítványát az Application gatewaybe. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak ismert háttérbeli példányokkal kommunikáljon. Ez a konfiguráció tovább biztosítja a végpontok közötti kommunikációt.

További információ: [a TLS-lezárás áttekintése és a végpontok közötti TLS és a Application Gateway](./ssl-overview.md).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Új Application Gateway létrehozása végpontok közötti TLS-vel

Ahhoz, hogy új Application Gateway-t hozzon létre végpontok közötti TLS-titkosítással, először engedélyeznie kell a TLS-megszakítást egy új Application Gateway létrehozásakor. Ez a művelet engedélyezi a TLS-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután fel kell helyeznie a biztonságos címzetteket a HTTP-beállításokban található háttér-kiszolgálók tanúsítványainak listájára. Ez a konfiguráció lehetővé teszi a TLS-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz. Ez a végpontok közötti TLS-titkosítást hajtja végre.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>TLS-megszakítás engedélyezése új Application Gateway létrehozásakor

További információ: [a TLS-megszakítás engedélyezése új Application Gateway létrehozásakor](./create-ssl-portal.md).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Hitelesítő/főtanúsítványok hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. Az Azure automatikusan létrehozta a **appGatewayBackendHttpSettings** alapértelmezett http-beállítását az Application Gateway létrehozásakor. 

3. Válassza a **appGatewayBackendHttpSettings** lehetőséget.

4. A **protokoll** területen válassza a **https** lehetőséget. Megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája.

5. Válassza az **Új létrehozása** lehetőséget.

6. A **név** mezőben adjon meg egy megfelelő nevet.

7. Válassza ki a tanúsítványfájl elemet a **CER-tanúsítvány feltöltése** mezőben.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgálói tanúsítvány nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![Tanúsítvány hozzárendelése](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén. cer formátumban töltse fel a háttér-kiszolgálói tanúsítvány főtanúsítványát. Ha a háttér-tanúsítványt egy jól ismert hitelesítésszolgáltató (CA) állítja ki, akkor jelölje be a **jól ismert hitelesítésszolgáltatói tanúsítvány használata** jelölőnégyzetet, és nem kell tanúsítványt feltöltenie.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Főtanúsítvány](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Kattintson a **Mentés** gombra.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Végpontok közötti TLS engedélyezése meglévő Application gatewayhez

Egy meglévő Application Gateway végpontok közötti TLS-titkosítással való konfigurálásához először engedélyeznie kell a TLS-megszakítást a figyelőben. Ez a művelet engedélyezi a TLS-titkosítást az ügyfél és az Application Gateway közötti kommunikációhoz. Ezután helyezze ezeket a tanúsítványokat a háttér-kiszolgálókhoz a biztonságos címzettek listájának HTTP-beállításai között. Ez a konfiguráció lehetővé teszi a TLS-titkosítást az Application Gateway és a háttér-kiszolgálók közötti kommunikációhoz. Ez a végpontok közötti TLS-titkosítást hajtja végre.

Egy figyelőt kell használnia a HTTPS protokollal, valamint egy tanúsítványt a TLS-megszakítás engedélyezéséhez. Használhat olyan meglévő figyelőt, amely megfelel a feltételeknek, vagy létrehozhat egy új figyelőt. Ha a korábbi lehetőséget választja, figyelmen kívül hagyhatja a következő "TLS-megszakítás engedélyezése egy meglévő Application Gateway-ben" szakaszt, és közvetlenül a "hitelesítő és megbízható főtanúsítványok hozzáadása a háttér-kiszolgálókhoz" szakaszban.

Ha az utóbbi lehetőséget választja, alkalmazza az alábbi eljárás lépéseit.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>TLS-megszakítás engedélyezése meglévő Application Gateway-ben

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.

2. Válassza a **figyelők** lehetőséget a bal oldali menüben.

3. A követelményektől függően válasszon az **alapszintű** vagy a **többhelyes** figyelő közül.

4. A **protokoll** területen válassza a **https** lehetőséget. Megjelenik a **tanúsítvány** ablaktáblája.

5. Töltse fel az ügyfél és az Application Gateway közötti TLS-megszakításhoz használni kívánt PFX-tanúsítványt.

   > [!NOTE]
   > Tesztelési célból önaláírt tanúsítványt is használhat. Ez azonban nem javasolt az éles számítási feladatokhoz, mivel azok nehezebben kezelhetők és nem teljesen biztonságosak. További információért lásd: [önaláírt tanúsítvány létrehozása](./create-ssl-portal.md#create-a-self-signed-certificate).

6. Adja hozzá a **figyelő** egyéb szükséges beállításait a követelményektől függően.

7. A mentéshez kattintson az **OK** gombra.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Hitelesítési/megbízható legfelső szintű tanúsítványok hozzáadása a háttér-kiszolgálókhoz

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.

2. A bal oldali menüben válassza a **http-beállítások** lehetőséget. A tanúsítványokat egy meglévő háttérbeli HTTP-beállításban is elhelyezheti a biztonságos címzettek listáján, vagy létrehozhat egy új HTTP-beállítást. (A következő lépésben az alapértelmezett HTTP-beállításhoz ( **appGatewayBackendHttpSettings**) tartozó tanúsítvány hozzá lesz adva a biztonságos címzettek listájához.)

3. Válassza a **appGatewayBackendHttpSettings** lehetőséget.

4. A **protokoll** területen válassza a **https** lehetőséget. Megjelenik a **háttér-hitelesítési tanúsítványok vagy a megbízható főtanúsítványok** ablaktáblája. 

5. Válassza az **Új létrehozása** lehetőséget.

6. A **név** mezőben adjon meg egy megfelelő nevet.

7. Válassza ki a tanúsítványfájl elemet a **CER-tanúsítvány feltöltése** mezőben.

   A standard és a WAF (v1) alkalmazás-átjárók esetében a háttér-kiszolgálói tanúsítvány nyilvános kulcsát. cer formátumban kell feltöltenie.

   ![Tanúsítvány hozzárendelése](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 és WAF_v2 Application Gateway esetén. cer formátumban töltse fel a háttér-kiszolgálói tanúsítvány főtanúsítványát. Ha a háttér-tanúsítványt egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, akkor jelölje be a **jól ismert hitelesítésszolgáltatói tanúsítvány használata** jelölőnégyzetet, és nem kell tanúsítványt feltöltenie.

   ![Megbízható főtanúsítvány hozzáadása](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)