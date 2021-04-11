---
title: Azure Functions kimenő IP-cím szabályozása Azure virtuális hálózati NAT-átjáróval
description: Részletes oktatóanyag, amely bemutatja, hogyan konfigurálhatja a NAT-t egy Azure-beli virtuális hálózathoz csatlakoztatott függvényhez
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658157"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Oktatóanyag: Azure Functions kimenő IP-cím szabályozása Azure-beli virtuális hálózati NAT-átjáróval

A virtuális hálózati címfordítás (NAT) leegyszerűsíti a csak kimenő internetkapcsolatot a virtuális hálózatok számára. Ha egy alhálózaton van konfigurálva, minden kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja. A NAT Azure Functions vagy Web Apps számára hasznos lehet, ha olyan harmadik féltől származó szolgáltatást kell használnia, amely biztonsági mértékként IP-engedélyezési használ. További információ: [What is Virtual Network NAT?](../virtual-network/nat-overview.md).

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a virtuális hálózati NAT-ket a kimenő forgalom útválasztására egy HTTP által aktivált függvényből. Ez a funkció lehetővé teszi a saját kimenő IP-címének ellenőrzését. Az oktatóanyag során a következőket fogja megtekinteni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Prémium csomagbeli Function-alkalmazás létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * NAT-átjáró létrehozása
> * A Function app konfigurálása a kimenő forgalom útválasztásához a NAT-átjárón keresztül

## <a name="topology"></a>Topológia

A következő ábra a létrehozott megoldás architektúráját mutatja be:

![A NAT-átjáró integrációjának felhasználói felülete](./media/functions-how-to-use-nat-gateway/topology.png)

A prémium szintű csomagban futó függvények ugyanazok az üzemeltetési képességek, mint a Azure App Service webalkalmazásai, beleértve a VNet-integrációs funkciót is. További információ a VNet-integrációról, beleértve a hibaelhárítást és a speciális konfigurációt: [az alkalmazás integrálása Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos megérteni az IP-címzést és alhálózatokat. Ebből a cikkből megtudhatja, hogyan kezelheti [a címzési és alhálók alapjait](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Számos további cikk és videó elérhető online állapotban.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Ha már elvégezte az [integrációs funkciókat egy Azure Virtual Network](./functions-create-vnet.md) -oktatóanyaggal, ugorjon a [http-trigger-függvény létrehozása](#create-function)lehetőségre.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Marketplace-en válassza a **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.

1. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki az alábbi táblázatban látható beállításokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása** elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Name | Adja meg a *myResourceGroup-vnet* értéket. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget.|

1. Válassza a **tovább lehetőséget: IP-címek** és **IPv4-címterület** esetében adja meg a *10.10.0.0/16* értéket.

1. Válassza az **alhálózat hozzáadása** lehetőséget, majd az alhálózati **címtartomány** esetében írja be a következőt: az alhálózati **név** és a *10.10.1.0/24* *oktatóanyaga* .

    ![IP-címek lap vnet létrehozásához](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Válassza a **Hozzáadás**, majd a **felülvizsgálat + létrehozás** elemet. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás** lehetőséget.

1. A **virtuális hálózat létrehozása** lapon válassza a **Létrehozás** lehetőséget.

Ezután hozzon létre egy Function alkalmazást a [prémium](functions-premium-plan.md)csomagban. Ez a csomag kiszolgáló nélküli skálázást biztosít a virtuális hálózatok integrálásának támogatása mellett.

## <a name="create-a-function-app-in-a-premium-plan"></a>Function-alkalmazás létrehozása prémium csomaggal

> [!NOTE]  
> Az oktatóanyagban a legjobb megoldás érdekében válassza a .NET for Runtime stack lehetőséget, és válassza a Windows operációs rendszer lehetőséget. Emellett hozzon létre egy Function-alkalmazást a virtuális hálózattal megegyező régióban.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>A Function alkalmazás összekötése a virtuális hálózattal

Mostantól a Function alkalmazást a virtuális hálózathoz is összekapcsolhatjuk.

1. A Function alkalmazásban a bal oldali menüben válassza a **hálózatkezelés** lehetőséget, majd a **VNet-integráció** területen válassza a **Konfigurálás** lehetőséget.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="A Function alkalmazásban válassza a hálózatkezelés lehetőséget.":::

1. A **VNET-integráció** lapon válassza a **VNET hozzáadása** elemet.

1. A **hálózati szolgáltatások állapota** területen használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    ![A Function app virtuális hálózat megadása](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup – vnet | Ez a virtuális hálózat a korábban létrehozott. |
    | **Alhálózat** | Új alhálózat létrehozása | Hozzon létre egy alhálózatot a virtuális hálózatban a Function alkalmazás használatára. A VNet-integrációt üres alhálózat használatára kell konfigurálni. |
    | **Alhálózat neve** | Function-Net | Az új alhálózat neve. |
    | **Virtuális hálózati címterület** | 10.10.0.0/16 | Csak egy címterület van definiálva. |
    | **Alhálózat-címterület** | 10.10.2.0/24   | Az alhálózat mérete korlátozza azon példányok számát, amelyeket a prémium szintű csomag funkciójának alkalmazásával fel lehet méretezni. Ez a példa egy `/24` alhálózatot használ, amely 254 elérhető gazdagép-címmel rendelkezik. Ez az alhálózat túl van kiépítve, de könnyen kiszámítható. |

1. Az alhálózat hozzáadásához kattintson **az OK gombra** . Az **VNet-integráció** és a **hálózati szolgáltatás állapota** lapok bezárásával térjen vissza a Function app-oldalára.

A Function alkalmazás most már elérheti a virtuális hálózatot. Ezután hozzáadhat egy HTTP-triggert a Function alkalmazáshoz.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP-trigger függvény létrehozása

1. A **függvények** ablak bal oldali menüjében válassza a **függvények** lehetőséget, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 
 
1. Az **új függvény** ablakban válassza a **http-trigger** lehetőséget, és fogadja el az **új függvény** alapértelmezett nevét, vagy adjon meg egy új nevet. 

1. A **Code + test** elemnél cserélje le a sablon által generált C# script (. CSX) kódot a következő kódra: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Ez a kód egy külső webhelyet hív meg, amely visszaadja a hívó IP-címét, ami ebben az esetben ez a függvény. Ezzel a módszerrel könnyedén meghatározható a Function alkalmazás által használt kimenő IP-cím.

Most már készen áll a függvény futtatására és a jelenlegi kimenő IP-címek vizsgálatára.

## <a name="verify-current-outbound-ips"></a>Aktuális kimenő IP-címek ellenőrzése

Most futtathatja a függvényt. Először is jelentkezzen be a portálon, és tekintse meg, hogy a Function alkalmazás milyen kimenő IP-címeket használ.  

1. A Function alkalmazásban válassza a **Tulajdonságok** lehetőséget, és tekintse át a **kimenő IP-címek** mezőt.

    ![A Function app kimenő IP-címeinek megtekintése](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Most térjen vissza a HTTP-trigger függvényhez, válassza a **Code + test** , majd a **test/Run** lehetőséget.

    ![Teszt függvény](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. A függvény végrehajtásához válassza a **Futtatás** lehetőséget, majd váltson a **kimenetre**. 

    ![Teszt funkció kimenete](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Győződjön meg arról, hogy a HTTP-válasz törzsében szereplő IP-cím a korábban megtekintett kimenő IP-címek egyike.

Most létrehozhat egy nyilvános IP-címet, és egy NAT-átjáró használatával módosíthatja ezt a kimenő IP-címet.

## <a name="create-public-ip"></a>Nyilvános IP-cím létrehozása

1. Az erőforráscsoport területen válassza a **Hozzáadás** lehetőséget, keresse meg az Azure piactéren a **nyilvános IP-címet**, és válassza a **Létrehozás** lehetőséget. Használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    ![Nyilvános IP-cím létrehozása](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Beállítás      | Ajánlott érték  |
    | ------------ | ---------------- |
    | **IP-verzió** | IPv4 |
    | **Termékváltozat** | Standard |
    | **Szint** | Regionális |
    | **Név** | Kimenő – IP |
    | **Előfizetés** | Ellenőrizze, hogy az előfizetése megjelenik-e | 
    | **Erőforráscsoport** | myResourceGroup (vagy az erőforráscsoporthoz hozzárendelt név) |
    | **Hely** | Az USA keleti régiója (vagy a más erőforrásokhoz rendelt hely) |
    | **Availability Zone** (Rendelkezésreállási zóna) | Nincs zóna |

1. Válassza a **Létrehozás** lehetőséget a telepítés elküldéséhez.

1. Az üzembe helyezés befejezése után navigáljon az újonnan létrehozott nyilvános IP-cím erőforráshoz, és tekintse meg az IP-címet az **áttekintésben**.

    ![Nyilvános IP-cím megtekintése](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>NAT-átjáró létrehozása

Most hozzuk létre a NAT-átjárót. Az [előző virtuális hálózatkezelési oktatóanyag](functions-create-vnet.md)használatakor `Function-Net` a javasolt alhálózat neve volt, és `MyResourceGroup-vnet` a javasolt virtuális hálózat neve volt ebben az oktatóanyagban.

1. Az erőforráscsoport területen válassza a **Hozzáadás** lehetőséget, keresse meg az Azure Marketplace-en a **NAT-átjárót**, és válassza a **Létrehozás** lehetőséget. A rendszerkép alatti táblázatban található beállításokkal töltheti fel az **alapok** lapot:

    ![NAT-átjáró létrehozása](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Beállítás      | Ajánlott érték  |
    | ------------ | ---------------- |  
    | **Előfizetés** | Az Ön előfizetése | 
    | **Erőforráscsoport** | myResourceGroup (vagy az erőforráscsoporthoz hozzárendelt név) |
    | **NAT-átjáró neve** | myNatGateway |
    | **Régió** | Az USA keleti régiója (vagy a más erőforrásokhoz rendelt hely) |
    | **Availability Zone** (Rendelkezésreállási zóna) | Nincsenek |

1. Válassza a Next (tovább) lehetőséget **: kimenő IP-cím**. A **nyilvános IP-címek** mezőben válassza ki a korábban létrehozott nyilvános IP-címet. Hagyja kiválasztva a **nyilvános IP-előtagot** .

1. Válassza a **Tovább: alhálózat** elemet. Válassza ki a *myResourceGroup-vnet* erőforrást a **virtuális hálózat** és a *függvény-net* alhálózat mezőben.

    ![Alhálózat kiválasztása](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. A telepítés elküldéséhez válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget.

Az üzembe helyezés befejezése után a NAT-átjáró készen áll arra, hogy átirányítsa a forgalmat a Function app-alhálózatról az internetre.

## <a name="update-function-configuration"></a>Frissítési függvény konfigurációja

Most hozzá kell adnia egy, az `WEBSITE_VNET_ROUTE_ALL` értékhez beállított Alkalmazásbeállítások értékét `1` .  Ez a beállítás kényszeríti a kimenő forgalmat a virtuális hálózaton és a hozzá tartozó NAT-átjárón keresztül. Ezen beállítás nélkül az internetes forgalom nem az integrált virtuális hálózaton keresztül történik, és ugyanazokat a kimenő IP-címeket fogja látni. 

1. Navigáljon a Function alkalmazáshoz a Azure Portal, majd válassza a **konfiguráció** lehetőséget a bal oldali menüben.

1. Az **Alkalmazásbeállítások** területen válassza az **+ új alkalmazás beállítása** elemet, és fejezze be a következő értékeket a mezők kitöltéséhez:

    |Mezőnév  |Érték |
    |---|---|
    |**Név**    |WEBSITE_VNET_ROUTE_ALL|
    |**Érték**   |1|

1. Az új Alkalmazásbeállítások párbeszédpanel bezárásához kattintson **az OK gombra** .

1. Válassza a **Mentés** lehetőséget, majd **folytassa** a beállítások mentését.

A Function app most úgy lett konfigurálva, hogy a kapcsolódó virtuális hálózaton keresztül irányítsa a forgalmat.

## <a name="verify-new-outbound-ips"></a>Új kimenő IP-címek ellenőrzése

Ismételje meg [a fenti lépéseket](#verify-current-outbound-ips) a függvény ismételt futtatásához. Ekkor meg kell jelennie a kimenetben látható NAT-ban konfigurált kimenő IP-címnek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzéséhez erőforrásokat hozott létre. Ezeket az erőforrásokat a [fiókja állapotától](https://azure.microsoft.com/account/) és a [szolgáltatás díjszabása](https://azure.microsoft.com/pricing/)alapján számítjuk fel. Ha el szeretné kerülni az extra költségek felmerülését, törölje az erőforrásokat, ha már tudja, hogy szüksége van rájuk. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)
