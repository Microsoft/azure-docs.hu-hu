---
title: Webalkalmazás létrehozása a bemutató v1-ben
description: Ismerje meg, hogyan hozhat létre webalkalmazásokat egy App Service Environment v1-ben. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505409"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Webalkalmazás létrehozása App Service Environment v1-ben

> [!NOTE]
> Ez a cikk a App Service Environment v1-es verzióról szól.  A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
> 

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan hozhat létre webalkalmazásokat és App Service terveket egy [app Service Environment v1](app-service-app-service-environment-intro.md) -ben. 

> [!NOTE]
> Ha szeretné megismerni, hogyan hozhat létre egy webalkalmazást, de nem kell megtennie egy App Service Environmentban, tekintse meg a [.net-Webalkalmazás létrehozása](../quickstart-dotnetcore.md) vagy az egyéb nyelvekhez és keretrendszerekhez kapcsolódó oktatóanyagokat ismertető témakört.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy létrehozott egy App Service Environment. Ha még nem tette meg, tekintse meg [az App Service Environment létrehozását](app-service-web-how-to-create-an-app-service-environment.md)ismertető témakört. 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
1. Az [Azure Portalon](https://portal.azure.com/)kattintson az **erőforrás létrehozása > web és mobil > webalkalmazás** elemre. 
   
    ![Képernyőkép, amely bemutatja, hol hozhat létre webalkalmazást a Azure Portalban.][1]
2. Válassza ki előfizetését.  
   
    Ha több előfizetéssel is rendelkezik, akkor a környezet létrehozásakor használt előfizetést kell használnia, hogy létrehozzon egy alkalmazást a App Service Environmentban. 
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
   
    Az *erőforráscsoportok* lehetővé teszik a kapcsolódó Azure-erőforrások egységként való kezelését, és hasznosak lehetnek az alkalmazások *Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC-)* szabályainak létrehozásakor. További információt [Az Azure Resource Manager áttekintésében][ResourceGroups] találhat. 
4. Válassza ki vagy hozzon létre egy App Service-csomagot.
   
    A *app Service csomagok* a webalkalmazások felügyelt készletei.  Általában a díjszabás kiválasztásakor a díjat az egyes alkalmazások helyett a App Servicei csomagra kell alkalmazni. Egy olyan előfizetésben, amelyet a központhoz rendelt számítási példányok kell fizetnie, és nem az ASP-vel megjelenő.  A webalkalmazások példányai számának vertikális felskálázásához felskálázást végez a App Service terv példányain, és az adott csomagban található összes webalkalmazásra hatással van.  Bizonyos funkciók, például a tárolóhelyek vagy a VNET-integráció a csomagon belül mennyiségi korlátozásokkal is rendelkeznek.  További információ: [Azure app Service csomagok áttekintése](../overview-hosting-plans.md)
   
    A szolgáltató App Service-csomagjait a csomag neve alatt megjelenő helyen tekintheti meg.  
   
    ![A következő képernyőképen megtekintheti a központjának App Service terveit.][5]
   
    Ha olyan App Service csomagot szeretne használni, amely már létezik a App Service Environmentban, válassza ki ezt a csomagot. Ha új App Service csomagot szeretne létrehozni, tekintse meg az oktatóanyag következő szakaszát, és [hozzon létre egy app Service tervet egy app Service Environmentban](#createplan).
5. Adja meg a webalkalmazás nevét, majd kattintson a **Létrehozás** gombra. 
   
    Ha a kiegészítő szolgáltatás külső virtuális IP-címet használ, akkor a rendszer a (z) [*sitename*]-t használja az alkalmazás URL-címére. [*a app Service Environment neve*]. p.azurewebsites.net helyett a (z) [*sitename*]. azurewebsites.net
   
    Ha a bemutató belső virtuális IP-címet használ, akkor az alkalmazás URL-címe a (z) [*sitename*]. [a beléptetési *létrehozás során megadott altartomány*]   
    Miután kiválasztotta az ASP-t a központilag történő létrehozás során, látni fogja **az alábbi** altartomány-frissítést.

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> App Service terv létrehozása
Ha egy App Service Environment App Service csomagot hoz létre, a feldolgozói döntések eltérőek, mivel nincsenek megosztott feldolgozók egy központba.  Azokat a munkavégzőket kell használni, amelyeket a rendszergazda a szolgáltatóhoz rendelt.  Ez azt jelenti, hogy új csomag létrehozásához több dolgozót kell kiosztania a bedolgozói készletben, mint az összes, az adott munkaterületen már meglévő csomag példányainak száma.  Ha nem rendelkezik elegendő feldolgozóval a csomag létrehozásához, akkor hozzá kell dolgoznia a beadásával a beszerzéshez.

Egy másik különbség a App Service Environment által üzemeltetett App Service-csomagokkal kapcsolatban a díjszabás hiánya.  Ha van App Service Environment, akkor a rendszer által használt számítási erőforrásokért kell fizetnie, és nem számítunk fel díjat az adott környezetben lévő csomagokhoz.  Általában Amikor létrehoz egy App Service tervet, ki kell választania egy díjszabási tervet, amely meghatározza a számlázást.  Az App Service Environment lényegében egy privát hely, ahol tartalmat hozhat létre.  A környezetért kell fizetnie, és nem kell a tartalmat üzemeltetni.

Az alábbi utasítások bemutatják, hogyan hozhat létre egy App Service tervet a webalkalmazások létrehozásakor az oktatóanyag előző szakaszában leírtak szerint.

1. Kattintson az **új létrehozása** lehetőségre a terv kiválasztása felhasználói felületen, és adjon meg egy nevet a csomagnak ugyanúgy, ahogy azt a Központon kívüli szokásos módon tenné.
2. Válassza ki azt a beadási szolgáltatót, amelyet a hely választójában szeretne használni.
   
    Mivel az App Service Environment lényegében egy privát telepítési hely, az a hely területen jelenik meg. 
   
    ![Képernyőkép, amely megjeleníti a kívánt előállítók kiválasztására szolgáló hely választóját.][2]
   
    Miután kiválasztotta a beléptetést a hely választójában, a App Service tervezze meg a felhasználói felület frissítéseit.  A hely ekkor megjeleníti a beszállítói rendszer és a régió nevét, és a díjszabási csomagot a feldolgozó készlet választója váltja fel.  
   
    ![Képernyőkép a beszállítói rendszer részleteiről, miután kiválasztotta a központot a hely választójában.][3]

### <a name="selecting-a-worker-pool"></a>Munkavégző készlet kiválasztása
Általában a App Service Environment Azure App Service és azon kívül 3 számítási méret áll rendelkezésre, amely egy dedikált díjcsomag kiválasztásával érhető el.  A beadáshoz hasonló módon legfeljebb 3 készletet határozhat meg, és meghatározhatja a munkavégző készlethez használt számítási méretet.  Ez azt jelenti, hogy a beadási csomag bérlői számára a számítási mérettel rendelkező díjszabási csomag kiválasztása helyett a App Service a *munkavégző készletnek* kell kiválasztania.  

A Worker Pool kiválasztási felhasználói felülete az adott feldolgozó készlethez használt számítási méretet jeleníti meg a név alatt.  A rendelkezésre álló mennyiség arra utal, hogy hány számítási példány használható a készletben.  Előfordulhat, hogy a teljes készletnek több példánya is van ennél a számnál, de ez az érték azt jelenti, hogy a nem használatos.  Ha módosítania kell a App Service Environment további számítási erőforrások hozzáadására, tekintse [meg a app Service Environment konfigurálását](app-service-web-configure-an-app-service-environment.md)ismertető témakört.

![Képernyőfelvétel: a munkavégző készlet ablaktáblája, ahol kiválaszthatja a feldolgozó készleteket a kiegészítő szolgáltatáshoz.][4]

Ebben a példában csak két munkavégző készlet érhető el. Ennek oka, hogy a szolgáltatói rendszergazda csak a két munkavégző készletbe foglalt gazdagépeket.  A harmadik akkor jelenik meg, ha virtuális gépek vannak lefoglalva.  

## <a name="after-web-app-creation"></a>A webalkalmazás létrehozása után
A webalkalmazások futtatására és a App Service csomagok egy olyan központba való felügyeletére van szükség, amelyet figyelembe kell venni.  

Amint azt korábban említettük, a szolgáltató tulajdonosa felelős a rendszer méretétől, és ennek eredményeképpen a felelősek annak biztosításáért is, hogy elegendő kapacitás áll rendelkezésre a kívánt App Service csomagok üzemeltetéséhez. Ha nincsenek elérhető feldolgozók, nem hozhatja létre a App Service tervet.  Ez a webalkalmazás skálázására is igaz.  Ha több példányra van szüksége, akkor további feldolgozók hozzáadásához a App Service Environment rendszergazdájának kell megszereznie.

A webalkalmazás létrehozása és a App Service megtervezése után érdemes felskálázást készíteni.  A beadásban mindig legalább két példánynak kell lennie a App Service terve, hogy hibatűrést biztosítson az alkalmazásai számára.  A App Service-csomag egy beosztásban való skálázása megegyezik a App Service terv felhasználói felületén található normál értékkel.  További információ a méretezésről [: webalkalmazás](app-service-web-scale-a-web-app-in-an-app-service-environment.md) skálázása app Service Environment

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/