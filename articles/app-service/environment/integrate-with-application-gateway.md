---
title: Integrálás az Application Gateway szolgáltatással
description: Ismerje meg, hogyan integrálhat egy alkalmazást a ILB App Service Environment egy, a végpontok közötti átjárást tartalmazó Application Gateway.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962077"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Az ILB App Service Environment és az Azure Application Gateway integrációja #

Az [app Service Environment](./intro.md) a Azure app Service központi telepítése az ügyfél Azure-beli virtuális hálózatának alhálózatában. Nyilvános vagy privát végponton is üzembe helyezhető az alkalmazás eléréséhez. A App Service Environment egy privát végponttal (azaz belső terheléselosztó) való üzembe helyezése ILB App Service Environment.  

A webalkalmazási tűzfalak biztosítják a webalkalmazások védelmét azáltal, hogy megvizsgálják a bejövő webes forgalmat az SQL-injektálások, a helyek közötti parancsfájlok, a kártevő-feltöltések & az Application DDoS és más támadások ellen. Emellett megvizsgálja a háttér-webkiszolgálók válaszait az adatveszteség-megelőzési (DLP) szolgáltatáshoz. WAF-eszközt az Azure Marketplace-en szerezhet be, vagy használhatja az [azure Application Gatewayt][appgw]is.

Az Azure Application Gateway egy virtuális készülék, amely 7. rétegbeli terheléselosztást, TLS/SSL-kiszervezést és webalkalmazási tűzfal (WAF) védelmet biztosít. Egy nyilvános IP-cím figyelésére és az alkalmazás-végpontra irányuló forgalom irányítására is képes. Az alábbi információk azt ismertetik, hogyan integrálható egy WAF-konfigurált Application Gateway egy ILB App Service Environment alkalmazással.  

Az Application Gateway integrációja a ILB App Service Environment az alkalmazás szintjén van. Ha az Application Gateway-t a ILB App Service Environment konfigurálja, akkor a ILB App Service Environment adott alkalmazásaihoz. Ez a módszer lehetővé teszi a biztonságos több-bérlős alkalmazások üzemeltetését egyetlen ILB App Service Environment.  

![Application Gateway, amely az alkalmazásra mutat egy ILB App Service Environment][1]

A bemutató keretében a következő lépéseket fogja végrehajtani:

* Hozzon létre egy Azure-Application Gateway.
* Konfigurálja úgy a Application Gateway, hogy az ILB App Service Environment alkalmazásra mutasson.
* Konfigurálja az alkalmazást az Egyéni tartománynév tiszteletben tartására.
* Szerkessze a nyilvános DNS-állomásnevet, amely az Application gatewayre mutat.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Application Gateway integrálása a ILB App Service Environment, a következőkre lesz szüksége:

* Egy ILB App Service Environment.
* A ILB App Service Environment futó alkalmazás.
* A ILB App Service Environmentban az alkalmazáshoz használandó Internet-útválasztós tartománynév.
* A ILB App Service Environment által használt ILB-címe. Ez az információ a app Service Environment-portál **Beállítások**  >  **IP-címei** területen található:

    ![A ILB által használt IP-címek példáinak listája App Service Environment][9]
    
* Egy nyilvános DNS-név, amelyet később a Application Gatewayre mutatnak. 

A ILB-App Service Environment létrehozásával kapcsolatos további információkért lásd: [ILB-app Service Environment létrehozása és használata][ilbase].

Ez a cikk azt feltételezi, hogy egy Application Gateway ugyanabban az Azure-beli virtuális hálózaton kívánja használni, ahol a App Service Environment telepítve van. Mielőtt megkezdené a Application Gateway létrehozását, válasszon ki vagy hozzon létre egy alhálózatot, amelyet az átjáró üzemeltetéséhez fog használni. 

Olyan alhálózatot használjon, amely nem a GatewaySubnet nevű. Ha a GatewaySubnet-ben helyezi el a Application Gateway, akkor később nem hozhat létre virtuális hálózati átjárót. 

Nem helyezheti el az átjárót abban az alhálózatban, amelyet a ILB App Service Environment használ. A App Service Environment az egyetlen dolog, ami ebben az alhálózatban lehet.

## <a name="configuration-steps"></a>Konfigurációs lépések ##

1. A Azure Portal lépjen az **új**  >  **hálózati**  >  **Application Gateway** elemre.

2. Az **alapok** területén:

   a. A **név** mezőben adja meg a Application Gateway nevét.

   b. A **rétegek** területen válassza a **WAF** lehetőséget.

   c. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelyet a app Service Environment virtuális hálózat használ.

   d. **Erőforráscsoport** esetében hozza létre vagy válassza ki az erőforráscsoportot.

   e. A **hely** mezőben válassza ki a app Service Environment virtuális hálózat helyét.

   ![Új Application Gateway létrehozási alapjai][2]

3. A **Beállítások** területén:

   a. A **Virtual Network (virtuális hálózat**) területen válassza ki a app Service Environment virtuális hálózatot.

   b. Az **alhálózat** területen válassza ki azt az alhálózatot, ahol a Application Gateway telepíteni kell. Ne használja a GatewaySubnet-t, mert megakadályozza a VPN-átjárók létrehozását.

   c. Az **IP-cím típusa** beállításnál válassza a **nyilvános** lehetőséget.

   d. **Nyilvános IP-cím** esetén válasszon egy nyilvános IP-címet. Ha még nem rendelkezik ilyennel, hozzon létre egyet most.

   e. A **protokoll** beállításnál válassza a **http** vagy a **https** lehetőséget. Ha HTTPS-re konfigurálja, meg kell adnia egy PFX-tanúsítványt.

   f. A **webalkalmazási tűzfal** esetében engedélyezheti a tűzfalat, és beállíthatja az **észleléshez** vagy a **megelőzéshez** is, ahogy azt látja.

   ![Új Application Gateway létrehozási beállításai][3]
    
4. Az **Összefoglalás** szakaszban tekintse át a beállításokat, majd kattintson az **OK gombra**. A telepítés befejezéséhez a Application Gateway akár 30 percet is igénybe vehet.  

5. Miután a Application Gateway befejezte a telepítést, lépjen a Application Gateway portálra. Válassza a **háttér-készlet** lehetőséget. Adja hozzá a ILB App Service Environment ILB-címeit.

   ![Háttérbeli készlet konfigurálása][4]

6. Miután befejezte a háttér-készlet konfigurálásának folyamatát, válassza az **állapot**-mintavétel lehetőséget. Hozzon létre egy állapot-mintavételt az alkalmazáshoz használni kívánt tartománynévhez. 

   ![Állapotminták konfigurálása][5]
    
7. Miután befejezte az állapot-mintavételek konfigurálását, válassza a **http-beállítások** lehetőséget. Szerkessze a meglévő beállításokat, válassza az **Egyéni mintavétel használata** lehetőséget, és válassza ki a konfigurált mintavételt.

   ![HTTP-beállítások konfigurálása][6]
    
8. Nyissa meg a Application Gateway **Áttekintés** szakaszát, és másolja a Application Gateway által használt nyilvános IP-címet. Állítsa be az IP-címet az alkalmazás tartománynevéhez tartozó rekordként, vagy használja az adott cím DNS-nevét egy CNAME-rekordban. Könnyebben kiválaszthatja a nyilvános IP-címet, és átmásolhatja a nyilvános IP-cím KEZELŐFELÜLETéről ahelyett, hogy átmásolja a hivatkozást a Application Gateway **Áttekintés** szakaszának hivatkozására. 

   ![Application Gateway portál][7]

9. Állítsa be az alkalmazás egyéni tartománynevét a ILB App Service Environment. Nyissa meg az alkalmazást a portálon, és a **Beállítások** területen válassza az **Egyéni tartományok** elemet.

   ![Egyéni tartománynév beállítása az alkalmazásban][8]

A webalkalmazásokhoz tartozó egyéni tartománynevek beállítására vonatkozó információk a [webalkalmazás egyéni tartománynevének beállítása][custom-domain]című cikkben találhatók. Egy ILB App Service Environment alkalmazás esetében azonban nincs érvényesítés a tartománynévben. Mivel Ön az alkalmazás-végpontokat kezelő DNS-t birtokolja, bármit is igénybe vehet. Az ebben az esetben hozzáadott egyéni tartománynévnek nem kell a DNS-ben lennie, de még be kell állítania az alkalmazással. 

Miután a telepítés befejeződött, és a DNS-módosítások propagálásához szükséges rövid idő, az alkalmazáshoz az Ön által létrehozott egyéni tartománynév használatával férhet hozzá. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md