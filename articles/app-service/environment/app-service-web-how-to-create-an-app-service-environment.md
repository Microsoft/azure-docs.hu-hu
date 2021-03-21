---
title: Bemutató v1 létrehozása
description: Az App Service Environment v1-környezet létrehozási folyamatának leírása. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005205"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>App Service Environment v1 létrehozása 

> [!NOTE]
> Ez a cikk a App Service Environment v1-es verzióról szól. A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
> 

### <a name="overview"></a>Áttekintés
A App Service Environment (a bevezetési mód) olyan prémium szintű Azure App Service, amely a több-bérlős bélyegekben nem elérhető, továbbfejlesztett konfigurációs képességet biztosít. A bevezetési funkció lényegében központilag telepíti a Azure App Servicet az ügyfél virtuális hálózatán. A App Service környezetek által kínált képességek jobb megismeréséhez olvassa el a [Mi az a app Service Environment][WhatisASE] dokumentációt.

### <a name="before-you-create-your-ase"></a>A bekészítés előtt
Fontos, hogy tisztában legyenek a nem módosítható dolgokkal. A következő szempontok nem változtathatják meg a saját bevonását a létrehozása után:

* Hely
* Előfizetés
* Erőforráscsoport
* Használt VNet
* Használt alhálózat 
* Alhálózat mérete

A VNet kiválasztásakor és az alhálózat megadásakor győződjön meg arról, hogy elég nagy a jövőbeli növekedéshez. 

### <a name="creating-an-app-service-environment-v1"></a>App Service Environment v1 létrehozása
App Service Environment v1 létrehozásához keressen rá az Azure Marketplace-en a ***app Service Environment v1** _-re, vagy folytassa *a _ Create a Resource**-> **web és mobil**  ->  **app Service Environment**. ASEv1 létrehozása:

1. Adja meg a beadás nevét. A központhoz megadott nevet a központhoz tartozó elősegítő alkalmazásban létrehozott alkalmazások használják. Ha a appsvcenvdemo neve a következő lesz: *appsvcenvdemo.p.azurewebsites.net*. Ha így hozott létre egy *mytestapp* nevű alkalmazást, a *mytestapp.appsvcenvdemo.p.azurewebsites.net* címen lehet címezni. A szolgáltató neve nem használható üresen. Ha nagybetűket használ a névben, a tartománynév a név teljes kisbetűs változata lesz. Ha ILB használ, a rendszer nem használja a beléptetési nevet az altartományban, hanem explicit módon meg van határozva a központilag történő létrehozás során.
   
    ![Képernyőkép, amely bemutatja, hogyan hozható létre egy App Service Environment (beszerzési).][1]
2. Válassza ki előfizetését. A benyújtó csomaghoz használt előfizetés az adott kiegészítő csomagban létrehozott összes alkalmazásra is érvényes lesz. Egy másik előfizetésben található VNet nem helyezheti üzembe a Bea-t.
3. Válasszon ki vagy adjon meg egy új erőforráscsoportot. A kiegészítő szolgáltatáshoz használt erőforráscsoporthoz meg kell egyeznie a VNet. Ha már meglévő VNet választ, a rendszer frissíti az erőforráscsoport-kiválasztást a saját VNet.
   
    ![Képernyőkép, amely bemutatja, hogyan választhat ki vagy módosíthat egy új erőforráscsoportot.][2]
4. Adja meg a Virtual Network és a hely beállításait. Dönthet úgy, hogy új VNet hoz létre, vagy egy már meglévő VNet választ. Ha kiválaszt egy új VNet, akkor megadhatja a nevet és a helyet. Az új VNet a 192.168.250.0/23 címtartományt és egy **alapértelmezett** nevű alhálózatot fog tartalmazni, amely 192.168.250.0/24 néven van meghatározva. Egyszerűen kiválaszthat egy már létező klasszikus vagy Resource Manager-VNet is. A virtuális IP-cím típusának meghatározása meghatározza, hogy a központilag elérhető-e közvetlenül az internetről (külső), vagy belső Load Balancert (ILB) használ. Ha többet szeretne megtudni róluk, olvassa el a [belső Load Balancer egy app Service Environment használatával][ILBASE]című témakört. Ha a külső VIP-típust választja, kiválaszthatja, hogy a rendszer hány külső IP-címet hoz létre IP SSL célokra. Ha a belső lehetőséget választja, akkor meg kell adnia azt az altartományt, amelyet a szolgáltató használni fog. A ASE olyan virtuális hálózatokban is üzembe helyezhetők, *amelyek nyilvános címtartományt* *vagy* RFC1918 (például magánhálózati címeket) használnak. Ha nyilvános címtartományt használó virtuális hálózatot szeretne használni, az idő előtt létre kell hoznia a VNet. Egy már meglévő VNet kiválasztásakor létre kell hoznia egy új alhálózatot a beléptetési folyamat létrehozása során. **A portálon nem használhat előre létrehozott alhálózatot. Ha egy Resource Manager-sablonnal hozza létre a bevezetőt, létrehozhat egy meglévő alhálózattal rendelkező bevezetőt is.** Ha egy sablonból szeretne létrehozni egy adatforrást, használja az itt található információkat, [hozzon létre egy app Service Environment a sablonból][ILBAseTemplate] , és itt hozzon létre [egy ILB-app Service Environment a sablonból][ASEfromTemplate].

### <a name="details"></a>Részletek
A bekészítés két előtérből és 2 feldolgozóból áll. A kezelőfelületek HTTP/HTTPS-végpontként működnek, és elküldik a forgalmat az alkalmazásokat futtató szerepköröknek. A mennyiséget a bekapcsolás utáni létrehozás után módosíthatja, és beállíthatja az ezen erőforráskészlet-eszközökre vonatkozó automatikusan méretezhető szabályokat is. Az App Service Environment manuális skálázásával, kezelésével és figyelésével kapcsolatos további információkért látogasson el ide: [app Service Environment konfigurálása][ASEConfig] 

A szolgáltató által használt alhálózatban csak az egyik kiegészítő szolgáltatás létezhet. Az alhálózat nem használható a központhoz tartozó

### <a name="after-app-service-environment-v1-creation"></a>App Service Environment v1 létrehozása után
A kiegészítő környezet létrehozása után a következőket állíthatja be:

* Elülső végek mennyisége (minimum: 2)
* Feldolgozók mennyisége (minimum: 2)
* IP SSL számára elérhető IP-címek mennyisége
* Az előtér-és a feldolgozók által használt számítási erőforrások mérete (az előtér minimális mérete P2)

További részletek a App Service környezetek manuális skálázásával, kezelésével és figyelésével kapcsolatban: [app Service Environment konfigurálása][ASEConfig] 

Az automatikus skálázással kapcsolatos információkért tekintse meg a következő útmutatót: az automatikus skálázás [konfigurálása app Service Environment][ASEAutoscale]

Vannak olyan további függőségek, amelyek nem érhetők el a testreszabáshoz, például az adatbázishoz és a tároláshoz. Ezeket az Azure kezeli, és a rendszerbe kerül. A rendszer tárterülete legfeljebb 500 GB-ot támogat a teljes App Service Environment számára, és az adatbázist az Azure a rendszer skálázása által igénybe veszik.

## <a name="getting-started"></a>Első lépések
A App Service Environment v1 használatának megkezdéséhez lásd: [a app Service Environment v1 bemutatása][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
