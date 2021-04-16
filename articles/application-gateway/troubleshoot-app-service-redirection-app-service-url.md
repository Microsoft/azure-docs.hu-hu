---
title: Url-címre való átirányítás App Service hibaelhárítása
titleSuffix: Azure Application Gateway
description: Ez a cikk az átirányítási probléma elhárításáról nyújt információt, ha Azure Application Gateway a Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514886"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>A App Service kapcsolatos problémák Application Gateway

Megtudhatja, hogyan diagnosztizálhatja és oldhatja meg az olyan problémákat, amelyek akkor merülhetnek fel Azure App Service ha háttércélként használják a Azure Application Gateway.

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan háríthatja el a következő problémákat:

* Az App Service URL-címe átirányításkor nyílik a böngészőben.
* Az App Service ARRAffinity cookie-tartománya az eredeti gazdagép helyett az App Service-example.azurewebsites.net van beállítva.

Amikor egy háttéralkalmazás átirányítási választ küld, előfordulhat, hogy a háttéralkalmazás által megadott URL-címtől eltérő URL-címre szeretné átirányítani az ügyfelet. Ezt akkor érdemes megtenni, ha az App Service-t egy Alkalmazásátjáró mögött üzemeltetik, és az ügyfélnek átirányítást kell igényelni a relatív elérési úthoz. Ilyen például az átirányítás a contoso.azurewebsites.net/path1-contoso.azurewebsites.net/path2. 

Amikor az App Service átirányítási választ küld, ugyanazt az állomásnevet használja a válasz helyfejlécében, mint az Application Gatewaytől kapott kérelemben. Az ügyfél például közvetlenül a szolgáltatásnak contoso.azurewebsites.net/path2 kérést, nem pedig az Application Gateway contoso.com/path2. Nem szeretné megkerülni az Alkalmazásátjárót.

Ez a probléma a következő fő okok miatt fordulhat elő:

- Konfigurálta az átirányítást az App Service-hez. Az átirányítás egyszerű lehet, ha egy záró perjelet ad hozzá a kéréshez.
- Már van Azure Active Directory hitelesítése, ami az átirányítást okozza.

Emellett ha alkalmazásszolgáltatásokat használ egy Alkalmazásátjáró mögött, az Application Gatewayhez társított tartománynév (example.com) eltér az App Service tartománynevének nevétől (például example.azurewebsites.net). Az App Service által beállított ARRAffinity cookie tartományi értéke example.azurewebsites.net, ami nem kívánatos. Az eredeti gazdanévnek (example.com) a cookie-ban található tartománynév értéknek kell lennie.

## <a name="sample-configuration"></a>Mintakonfiguráció

- HTTP- figyelő: Alapszintű vagy többhelyű
- Háttércímkészlet: App Service
- HTTP-beállítások: **Válasszon gazdagépnevet az engedélyezett háttércímből**
- Mintavétel: **Válassza ki az engedélyezett HTTP-beállítások gazdagépnevét**

## <a name="cause"></a>Ok

App Service egy több-bérlős szolgáltatás, ezért a kérésben található állomásfejlécet használja a kérés megfelelő végpontra való útválasztására. A App Services *.azurewebsites.net (például contoso.azurewebsites.net) alapértelmezett tartományneve eltér az alkalmazásátjáró tartománynevéhez (például contoso.com). 

Az ügyféltől származó eredeti kérés állomásneve az Application Gateway contoso.com neve. Konfigurálnia kell az Application Gatewayt, hogy módosítsa az állomásnevet az App Service gazdagépnevére vonatkozó eredeti kérésben, amikor a kérést az App Service háttéralkalmazásához adja. Használja a **Gazdagépnév beállítása a háttércímből** kapcsolót az alkalmazásátjáró HTTP-beállítási konfigurációjában. Használja a **Gazdagépnév beállítása a háttér-HTTP-beállításokból** kapcsolót az állapot-mintavétel konfigurációjában.



![Az Application Gateway módosítja az állomásnevet](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Amikor az App Service átirányítást tesz, a helyfejlécben a felülbírált gazdanevet contoso.azurewebsites.net használja az eredeti állomásnév helyett contoso.com, hacsak másként nincs konfigurálva. Ellenőrizze az alábbi példaként található kérés- és válaszfejléceket.
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
Az előző példában figyelje meg, hogy a válaszfejléc átirányítási állapotkódja 301. A helyfejlécben az eredeti gazdanév helyett az App Service állomásneve `www.contoso.com` található.

## <a name="solution-rewrite-the-location-header"></a>Megoldás: A helyfejléc átírása

A hely fejlécében állítsa be az állomásnevet az alkalmazásátjáró tartománynevére. Ehhez hozzon létre egy újraírási szabályt egy olyan feltétellel, amely kiértékeli, hogy [a](./rewrite-http-headers.md) válaszban található helyfejléc tartalmaz-e azurewebsites.net. Egy műveletet is végre kell hajtanának a helyfejléc átírásához, hogy az application gateway gazdagépnevét tartalmazza. További információkért lásd a helyfejléc [átírásának utasításait.](./rewrite-http-headers.md#modify-a-redirection-url)

> [!NOTE]
> A HTTP-fejléc átírásának támogatása csak az Standard_v2-WAF_v2 [termékváltozatához](./application-gateway-autoscaling-zone-redundant.md) Application Gateway. Javasoljuk, hogy a fejlécátíráshoz és [](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) a [2-es](./migrate-v1-v2.md) termékváltozattal elérhető egyéb speciális képességekhez a v2-re mirating to migrating to v2 (Átírás fejlécek átírása) és egyéb speciális képességek.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatív megoldás: Egyéni tartománynév használata

A App Service funkció Custom Domain megoldás, amely mindig átirányítja a forgalmat Application Gateway tartománynevére (a `www.contoso.com` példánkban). Ez a konfiguráció az ARR affinitási cookie-k problémájára is megoldást nyújt. Alapértelmezés szerint az ARRAffinity cookie-tartomány az App Service alapértelmezett állomásnevére (example.azurewebsites.net) van beállítva a Application Gateway tartományneve helyett. Ezért ilyen esetekben a böngésző elutasítja a cookie-t a kérés és a cookie tartománynevének eltérése miatt.

A megadott metódust az Átirányítás és az ARRAffinity cookie-tartományel nem egyező problémái esetén is használhatja. Ehhez a módszerhez szüksége lesz az egyéni tartomány DNS-zónához való hozzáférésére.

**1.** lépés: Állítson be egy Custom Domain a App Service, és ellenőrizze a tartomány tulajdonjogát a [CNAME & TXT DNS-rekordok hozzáadásával.](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)
A rekordok a következő módon néznek ki:
-  `www.contoso.com` A CNAME-BEN `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` IN TXT " `<verification id string>` "


**2.** lépés: Az előző lépésben található CNAME rekordra csak a tartomány-ellenőrzéshez volt szükség. Végső soron szükség van a forgalomra az útvonaltervezéshez Application Gateway. Így módosíthatja a `www.contoso.com` CNAME-et úgy, hogy Application Gateway teljes tartománynevére mutasson. Ha teljes tartománynevet szeretne beállítani a Application Gateway, lépjen a nyilvános IP-cím erőforráshoz, és rendeljen hozzá egy "DNS-név címkét". A frissített CNAME rekordnak most a következőnek kell lennie: 
-  `www.contoso.com` A CNAME-BEN `contoso.eastus.cloudapp.azure.com`


**3.** lépés: Tiltsa le a "Gazdagépnév beállítása a háttércímből" beállítást a társított HTTP-beállításhoz.

A PowerShellben ne használja a `-PickHostNameFromBackendAddress` kapcsolót a `Set-AzApplicationGatewayBackendHttpSettings` parancsban.


**4.** lépés: Ahhoz, hogy a mintavételek kifogástalan állapotúként és működési forgalomként határozzák meg a háttérkiszolgálót, állítson be egy egyéni állapot-mintavételt gazdagépmezővel a gazdagép egyéni vagy alapértelmezett App Service.

A PowerShellben ne használja a kapcsolót a parancsban, és a mintavétel -HostName kapcsolója App Service vagy alapértelmezett `-PickHostNameFromBackendHttpSettings` `Set-AzApplicationGatewayProbeConfig` tartományát használja.

Az előző lépések meglévő telepítéshez a PowerShell használatával való megvalósításához használja a következő PowerShell-példaszkprogramot. Figyelje meg, hogy nem használtuk a **-PickHostname** kapcsolókat a mintavétel és a HTTP-beállítások konfigurációjában.

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

Ha a fenti lépések nem oldják meg a problémát, nyisson egy [támogatási jegyet.](https://azure.microsoft.com/support/options/)
