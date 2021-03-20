---
title: App Service URL-címhez való átirányítás hibakeresése
titleSuffix: Azure Application Gateway
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az átirányítási problémát az Azure Application Gateway Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182886"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>A Application Gateway App Service problémáinak elhárítása

Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a problémákat, amikor a Azure App Service az Azure-Application Gateway háttérbeli célként való használatakor merülhet fel.

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan lehet elhárítani a következő problémákat:

* Az App Service URL-címe elérhető a böngészőben, ha van átirányítás.
* Az App Service ARRAffinity cookie-tartománya az eredeti gazdagép helyett az App Service-állomásnévre, a example.azurewebsites.net-ra van állítva.

Ha egy háttérbeli alkalmazás átirányítási választ küld, előfordulhat, hogy át szeretné irányítani az ügyfelet egy másik URL-címre, mint amelyet a háttérbeli alkalmazás adott meg. Ezt akkor teheti meg, ha egy app Service-t egy Application Gateway mögött futtat, és megköveteli, hogy az ügyfél átirányítsa a relatív elérési útját. Ilyen például a contoso.azurewebsites.net/path1 és a contoso.azurewebsites.net/path2 közötti átirányítás. 

Amikor az App Service átirányítási választ küld, ugyanazt az állomásnevet használja a válasz Location fejlécében, mint az Application Gateway által fogadott kérelemben szereplőnek. Az ügyfél például közvetlenül a contoso.azurewebsites.net/path2 küldi a kérést ahelyett, hogy az Application Gateway-contoso.com/path2. Nem szeretné megkerülni az Application Gatewayt.

A probléma a következő fő okok miatt fordulhat elő:

- Az átirányítás konfigurálva van az App Service-ben. Az átirányítás olyan egyszerű lehet, mint egy záró perjel hozzáadása a kéréshez.
- Azure Active Directory hitelesítés, ami az átirányítás okát okozza.

Emellett, ha az Application Gateway mögötti app Servicest használ, az Application gatewayhez (example.com) társított tartománynév eltér az App Service-szolgáltatás tartománynevétől (például example.azurewebsites.net). Az App Service által beállított ARRAffinity-cookie tartományi értéke a example.azurewebsites.net tartománynevet adja meg, amely nem kívánatos. Az eredeti állomásnévnek (example.com) a cookie-ban a tartománynév értéknek kell lennie.

## <a name="sample-configuration"></a>Minta konfigurációja

- HTTP-figyelő: alapszintű vagy többhelyes
- Háttérbeli címkészlet: App Service
- HTTP-beállítások: **válassza az állomásnév lehetőséget a háttér címe** beállításnál
- Mintavétel: **válassza az állomásnév lehetőséget a http-beállítások** engedélyezve

## <a name="cause"></a>Ok

App Service egy több-bérlős szolgáltatás, ezért a kérelemben szereplő állomásfejléc használatával irányítja át a kérést a megfelelő végpontra. App Services, *. azurewebsites.net (Say, contoso.azurewebsites.net) alapértelmezett tartományneve eltér az Application Gateway tartománynevétől (azaz contoso.com). 

Az ügyfél eredeti kérelme az Application Gateway tartományneve, a contoso.com pedig az állomásnév. Úgy kell konfigurálnia az Application Gateway-t, hogy módosítsa az eredeti kérelemben szereplő állomásnevet az App Service állomásneve, amikor a kérést az App Service háttér felé irányítja. Az Application Gateway HTTP-beállítási konfigurációjában használja a kapcsoló- **kiválasztó gazdagépet a háttérbeli címről** . Használja a Switch **pick hostname elemet a háttérbeli http-beállítások közül** az állapot mintavételi konfigurációjában.



![Az Application Gateway megváltoztatja az állomásnév nevét](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Ha az App Service átirányítást végez, a felülbírált állomásnév contoso.azurewebsites.net az eredeti állomásnév helyett a contoso.com, kivéve, ha másként van konfigurálva. Olvassa el a következő példában szereplő kérelem és válasz fejléceket.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Az előző példában figyelje meg, hogy a válasz fejlécének 301-as állapotkód van az átirányításhoz. A Location fejlécben az App Service állomásneve az eredeti állomásnév helyett `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Megoldás: a hely fejlécének újraírása

A Location (hely) fejlécben állítsa be az állomásnév nevét az Application Gateway tartománynevére. Ehhez hozzon létre egy [Újraírási szabályt](./rewrite-http-headers.md) egy feltétellel, amely kiértékeli, hogy a válaszban található azurewebsites.net tartalmaz-e. Emellett olyan műveletet is végre kell hajtania, amely újraírja a Location fejlécet, hogy az Application Gateway állomásneve legyen. További információ: a [Location fejléc újraírásának](./rewrite-http-headers.md#modify-a-redirection-url)útmutatója.

> [!NOTE]
> A HTTP-fejléc újraírásának támogatása csak a Application Gateway [Standard_v2 és WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) esetében érhető el. Ha v1 SKU-t használ, javasoljuk, hogy [telepítse a v1-ről v2-re](./migrate-v1-v2.md). A v2 SKU-hoz elérhető újraírást és egyéb [speciális képességeket](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) kívánja használni.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatív megoldás: Egyéni tartománynév használata

Ha v1 SKU-t használ, nem tudja újraírni a hely fejlécét. Ez a funkció csak a v2 SKU esetében érhető el. Az átirányítási probléma megoldásához adja meg ugyanazt az állomásnevet, amelyet az Application Gateway az App Service-nek is fogad, a gazdagép felülbírálásának végrehajtása helyett.

Az App Service mostantól átirányítja az átirányítást (ha van ilyen) ugyanazon az eredeti állomásfejléc-fejlécen, amely az Application gatewayre mutat, és nem a saját.

Egy egyéni tartománynak kell lennie, és a következő eljárást kell követnie:

- Regisztrálja a tartományt az App Service egyéni tartomány listájában. Az App Service teljes tartománynevére mutató CNAME-t kell megadnia az egyéni tartományban. További információ: [meglévő egyéni DNS-név leképezése Azure app Service](../app-service/app-service-web-tutorial-custom-domain.md).

    ![App Service – egyéni tartomány listája](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Az App Service készen áll az állomásnév elfogadására `www.contoso.com` . Módosítsa a DNS-beli CNAME-bejegyzést úgy, hogy az az Application Gateway teljes tartománynevére mutasson, például: `appgw.eastus.cloudapp.azure.com` .

- DNS-lekérdezés esetén győződjön meg arról, hogy a tartomány `www.contoso.com` az Application Gateway teljes tartománynevére van feloldva.

- Az egyéni mintavétel beállításával letilthatja a **pick hostname elemet a háttérbeli http-beállítások közül**. A Azure Portal törölje a jelölést a mintavételi beállítások között. A PowerShellben ne használja a **-PickHostNameFromBackendHttpSettings** kapcsolót a **set-AzApplicationGatewayProbeConfig** parancsban. A mintavétel állomásnév mezőjében adja meg az App Service teljes tartománynevét (example.azurewebsites.net). Az Application Gateway által küldött mintavételi kérelmek ezt a teljes tartománynevet a gazdagép fejlécében hajtják végre.

  > [!NOTE]
  > A következő lépéshez győződjön meg arról, hogy az egyéni mintavétel nincs társítva a háttérbeli HTTP-beállításokhoz. A HTTP-beállítások ezen a ponton továbbra is a **háttérbeli címek közül** választhatnak.

- Állítsa be az Application Gateway HTTP-beállításait, hogy letiltsa a **pick hostname elemet a háttér-címről**. A Azure Portal törölje a jelölőnégyzet jelölését. A PowerShellben ne használja a **-PickHostNameFromBackendAddress** kapcsolót a **set-AzApplicationGatewayBackendHttpSettings** parancsban.

- Rendelje vissza az egyéni mintavételt a háttérbeli HTTP-beállításokhoz, és ellenőrizze, hogy a háttér állapota Kifogástalan-e.

- Az Application Gatewaynek ekkor el kell küldenie ugyanazt az állomásnevet az `www.contoso.com` app Service-be. Az átirányítás ugyanazon az állomásnéven történik. Olvassa el a következő példában szereplő kérelem és válasz fejléceket.

Az előző lépések végrehajtásához a PowerShell használatával egy meglévő beállításhoz használja az alábbi minta PowerShell-parancsfájlt. Figyelje meg, hogy nem használtuk a **-PickHostname** kapcsolókat a mintavétel és a http-beállítások konfigurációjában.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldották meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).