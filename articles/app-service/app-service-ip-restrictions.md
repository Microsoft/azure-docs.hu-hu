---
title: Hozzáférési korlátozások Azure App Service
description: Megtudhatja, hogyan védheti meg alkalmazásait Azure App Service a hozzáférési korlátozások beállításával.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 420dade645d1a4ee32bb888aecb76b033d5756e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731299"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Hozzáférési korlátozások beállítása Azure App Service

A hozzáférési korlátozások beállításával meghatározhatja az alkalmazáshoz való hálózati hozzáférést szabályozó, prioritással elválasztott engedélyezési/megtagadási listát. A lista tartalmazhat IP-címeket vagy Azure Virtual Network alhálózatokat. Ha egy vagy több bejegyzés van, akkor a lista végén van egy implicit *Megtagadás* .

A hozzáférés-korlátozási funkció minden Azure App Service üzemeltetett munkaterheléssel működik. A számítási feladatok közé tartozhatnak a webalkalmazások, az API-alkalmazások, a Linux-alkalmazások, a linuxos tároló-alkalmazások és a függvények.

Amikor kérelmet küld az alkalmazásnak, a feladó címe kiértékelésre kerül a hozzáférési korlátozási listán szereplő szabályok alapján. Ha a feladó címe olyan alhálózatban található, amely szolgáltatási végpontokkal van konfigurálva a Microsoft. Web számára, a rendszer összehasonlítja a forrás alhálózatot a hozzáférési korlátozási listán szereplő virtuális hálózati szabályokkal. Ha a címnek a listában szereplő szabályok alapján való hozzáférése nem engedélyezett, a szolgáltatás [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot választ.

A hozzáférés-korlátozási képesség a App Service előtér-szerepkörökben valósul meg, amelyek a programkód futtatását végző feldolgozó gazdagépekről származnak. Ezért a hozzáférési korlátozások hatékony hálózati hozzáférés-vezérlési listák (ACL-ek).

A webalkalmazáshoz való hozzáférés korlátozása egy Azure-beli virtuális hálózatról [szolgáltatás-végpontok][serviceendpoints]által engedélyezett. A szolgáltatás-végpontok segítségével korlátozhatja a hozzáférést egy több-bérlős szolgáltatáshoz a kiválasztott alhálózatokból. Nem működik a App Service Environmentban üzemeltetett alkalmazások forgalmának korlátozására. Ha App Service Environment van, az IP-cím szabályok alkalmazásával szabályozhatja az alkalmazáshoz való hozzáférést.

> [!NOTE]
> A szolgáltatási végpontokat engedélyezni kell mind a hálózatkezelési, mind az Azure-szolgáltatás számára, amelyeken engedélyezve vannak. A szolgáltatási végpontokat támogató Azure-szolgáltatások listáját itt tekintheti meg: [Virtual Network szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="A hozzáférési korlátozások folyamatábrája.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Hozzáférés-korlátozási szabályok kezelése a portálon

Ha hozzáférési korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, tegye a következőket:

1. Jelentkezzen be az Azure Portalra.

1. A bal oldali ablaktáblán válassza a **hálózatkezelés** lehetőséget.

1. A **hálózat** ablaktábla **hozzáférési korlátozások** területén válassza a **hozzáférési korlátozások konfigurálása** lehetőséget.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Képernyőkép a Azure Portal App Service hálózatkezelési beállítások panelről.":::

1. A **hozzáférési korlátozások** lapon tekintse át az alkalmazáshoz definiált hozzáférési korlátozási szabályok listáját.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Képernyőkép a Azure Portal hozzáférési korlátozások oldaláról, amely a kiválasztott alkalmazáshoz megadott hozzáférési korlátozási szabályok listáját jeleníti meg.":::

   A lista megjeleníti az alkalmazásra alkalmazott összes jelenlegi korlátozást. Ha az alkalmazáshoz virtuális hálózati korlátozás tartozik, a táblázat azt mutatja, hogy engedélyezve vannak-e a szolgáltatási végpontok a Microsoft. Web számára. Ha az alkalmazásban nincsenek korlátozások meghatározva, az alkalmazás bárhonnan elérhető.

### <a name="add-an-access-restriction-rule"></a>Hozzáférési korlátozási szabály hozzáadása

Ha hozzáférési korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, a **hozzáférési korlátozások** panelen válassza a **szabály hozzáadása** lehetőséget. A szabály hozzáadása után azonnal érvénybe lép. 

A szabályok érvényesítése prioritási sorrendben történik, a **prioritás** oszlopban szereplő legalacsonyabb számtól kezdve. Egy implicit *Megtagadás minden* esetben érvényben van, miután még egyetlen szabályt adott hozzá.

A **hozzáférés-korlátozás hozzáadása** panelen hozzon létre egy szabályt, tegye a következőket:

1. A **művelet** területen válassza az **Engedélyezés** vagy a **Megtagadás** lehetőséget.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Képernyőkép a &quot;hozzáférés-korlátozás hozzáadása&quot; panelről.":::

1. Szükség esetén megadhatja a szabály nevét és leírását is.
1. A **Priority (prioritás** ) mezőben adjon meg egy prioritási értéket.
1. A **típus** legördülő listában válassza ki a szabály típusát.

A különböző típusú szabályok leírása az alábbi szakaszokban található.

> [!NOTE]
> - Az 512 hozzáférési korlátozási szabályok száma korlátozott. Ha több mint 512 hozzáférés-korlátozási szabályra van szüksége, javasoljuk, hogy egy önálló biztonsági termék, például az Azure bejárati ajtó, az Azure app Gateway vagy egy alternatív WAF telepítését vegye fontolóra.
>
#### <a name="set-an-ip-address-based-rule"></a>IP-cím alapú szabály beállítása

Kövesse az előző szakaszban ismertetett eljárást, de a következő kiegészítéssel:
* A 4. lépésnél a **típus** legördülő listában válassza az **IPv4** vagy **IPv6** lehetőséget. 

Az IPv4-és IPv6-címek esetében az **IP-cím blokkot** az osztály nélküli Inter-Domain ÚTVÁLASZTÁS (CIDR) jelölése határozza meg. Cím megadásához használhatja a *1.2.3.4/32*-et, ahol az első négy oktett az Ön IP-címét és */32* a maszkot jelöli. Az összes cím IPv4-CIDR jelölése 0.0.0.0/0. További információ a CIDR jelöléséről: [osztály nélküli Inter-Domain útválasztás](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Szolgáltatás végpont-alapú szabályának beállítása

* A 4. lépésnél a **típus** legördülő listában válassza a **Virtual Network** lehetőséget.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Képernyőkép a &quot;korlátozás hozzáadása&quot; panelről, ahol a Virtual Network típus van kiválasztva.":::

Adja meg az **előfizetést**, a **Virtual Network** és az **alhálózatot** legördülő listát, és válassza ki, hogy mit szeretne korlátozni a hozzáférését.

A szolgáltatási végpontok használatával korlátozhatja a hozzáférést a kiválasztott Azure-beli virtuális hálózati alhálózatokhoz. Ha a szolgáltatási végpontok még nincsenek engedélyezve a Microsoft. Web-ben a kiválasztott alhálózathoz, akkor automatikusan engedélyezve lesznek, kivéve, ha bejelöli a **hiányzó Microsoft. Web Service-végpontok figyelmen kívül hagyása** jelölőnégyzetet. A forgatókönyv, ahol előfordulhat, hogy engedélyezni szeretné a szolgáltatási végpontokat az alkalmazáson, de az alhálózat nem függ elsősorban attól, hogy rendelkezik-e az alhálózaton való engedélyezéséhez szükséges engedélyekkel. 

Ha valaki másnak kell engedélyeznie a szolgáltatási végpontokat az alhálózaton, jelölje be a **hiányzó Microsoft. webszolgáltatás-végpontok figyelmen kívül hagyása** jelölőnégyzetet. Az alkalmazás a szolgáltatás-végpontok számára lesz konfigurálva, hogy az alhálózaton később engedélyezzék őket. 

A szolgáltatási végpontok nem használhatók a App Service Environmenton futó alkalmazásokhoz való hozzáférés korlátozására. Ha az alkalmazás egy App Service Environmentban van, akkor az IP-hozzáférési szabályok alkalmazásával szabályozhatja a hozzáférését. 

A szolgáltatási végpontok segítségével az alkalmazást Application Gateway vagy más webalkalmazási tűzfal (WAF) eszközzel konfigurálhatja. A többrétegű alkalmazásokat is konfigurálhatja biztonságos háttérbeli befejezéssel. További információ: [hálózati szolgáltatások és app Service](networking-features.md) és [Application Gateway integráció a szolgáltatás-végpontokkal](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - A szolgáltatási végpontok jelenleg nem támogatottak az IP-SSL (SSL) virtuális IP-címet (VIP) használó webalkalmazások esetén.
>
#### <a name="set-a-service-tag-based-rule"></a>Szolgáltatás-címkén alapuló szabály beállítása

* A 4. lépésnél a **típus** legördülő listában válassza a **szolgáltatás címkéje** elemet.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Képernyőkép a &quot;korlátozás hozzáadása&quot; panelről a kiválasztott szolgáltatás címkéjének típusával.":::

Az egyes szolgáltatási címkék az Azure-szolgáltatásokból származó IP-címtartományok listáját jelölik. A szolgáltatások listája és az adott tartományokra mutató hivatkozások a [szolgáltatás címkéjének dokumentációjában][servicetags]találhatók.

Az összes elérhető szolgáltatás címkéje támogatott a hozzáférési korlátozási szabályokban. Az egyszerűség kedvéért csak a leggyakoribb címkék listája érhető el a Azure Portalon keresztül. A speciális szabályok, például a regionális hatókörű szabályok konfigurálásához Azure Resource Manager sablonokat vagy parancsfájlokat használhat. Ezek a címkék Azure Portalon keresztül érhetők el:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor. backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Szabály szerkesztése

1. Egy meglévő hozzáférés-korlátozási szabály szerkesztésének megkezdéséhez a **hozzáférési korlátozások** lapon válassza ki a szerkeszteni kívánt szabályt.

1. A **hozzáférési korlátozás szerkesztése** panelen végezze el a módosításokat, majd válassza a **szabály frissítése** lehetőséget. A módosítások azonnal érvénybe lépnek, beleértve a prioritási sorrend változásait is.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Képernyőkép a &quot;hozzáférési korlátozás szerkesztése&quot; panelről a Azure Portal, amely egy meglévő hozzáférés-korlátozási szabály mezőit jeleníti meg.":::

   > [!NOTE]
   > Amikor szerkeszt egy szabályt, a szabályok típusai között nem válthat. 

### <a name="delete-a-rule"></a>Szabály törlése

Egy szabály törléséhez a **hozzáférési korlátozások** lapon válassza a törölni kívánt szabály melletti három pontot (**...**), majd válassza az **Eltávolítás** lehetőséget.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Képernyőkép a &quot;hozzáférési korlátozások&quot; oldalról, amely a törölni kívánt hozzáférés-korlátozási szabály melletti &quot;Eltávolítás&quot; három pontot mutatja.":::

## <a name="access-restriction-advanced-scenarios"></a>Hozzáférés-korlátozás speciális forgatókönyvek
A következő szakaszok a hozzáférési korlátozásokat használó speciális forgatókönyveket ismertetik.

### <a name="filter-by-http-header"></a>Szűrés HTTP-fejléc alapján

Bármely szabály részeként további HTTP-fejléc-szűrőket adhat hozzá. A következő HTTP-fejlécek nevei támogatottak:
* X – továbbított – a következőhöz:
* X-továbbított-gazdagép
* X – Azure – FDID
* X-FD-HealthProbe

Minden fejléc neveként legfeljebb 8 értéket adhat hozzá vesszővel elválasztva. A rendszer kiértékeli a HTTP-fejléc szűrőit, miután maga a szabály, és mindkét feltételnek igaznak kell lennie ahhoz, hogy a szabály vonatkozzon.

### <a name="multi-source-rules"></a>Több forrásra vonatkozó szabályok

A többforrásos szabályok lehetővé teszik legfeljebb 8 IP-tartomány vagy 8 szolgáltatási címke egyesítését egyetlen szabályban. Ezt akkor érdemes használni, ha több mint 512 IP-tartománnyal rendelkezik, vagy olyan logikai szabályokat szeretne létrehozni, amelyekben több IP-tartomány van összekapcsolva egyetlen HTTP-fejléc-szűrővel.

A többforrásos szabályok definiálása ugyanúgy történik, mint az egyforrású szabályok meghatározása, de az egyes tartományok vesszővel elválasztva.

PowerShell-példa:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Egyetlen IP-cím blokkolása

Az első hozzáférés-korlátozási szabály hozzáadásakor a szolgáltatás hozzáad egy explicit *megtagadási* szabályt, amelynek prioritása 2147483647. A gyakorlatban az összes szabály explicit *megtagadása* a végső szabály, amely letiltja az *engedélyezési* szabályok által explicit módon nem engedélyezett IP-címekhez való hozzáférést.

Olyan forgatókönyv esetén, ahol explicit módon le szeretné tiltani egyetlen IP-címet vagy IP-címtartományt, de minden más számára engedélyezi a hozzáférést, adjon hozzá egy explicit *engedélyezett minden* szabályt.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Képernyőkép a Azure Portal &quot;hozzáférési korlátozások&quot; oldaláról, amely egyetlen letiltott IP-címet mutat.":::

### <a name="restrict-access-to-an-scm-site"></a>SCM-helyhez való hozzáférés korlátozása 

Az alkalmazáshoz való hozzáférés szabályozása mellett korlátozhatja az alkalmazás által használt SCM-helyhez való hozzáférést is. Az SCM-hely a webes üzembe helyezési végpont és a kudu-konzol is. Az SCM-helyhez külön hozzáférési korlátozásokat rendelhet az alkalmazáshoz, vagy az alkalmazásra és az SCM-helyre vonatkozó korlátozásokat is használhat. Ha **ugyanazokat a korlátozásokat \<app name>** jelöli be, mint a jelölőnégyzet, minden kimarad. Ha törli a jelölőnégyzet jelölését, az SCM-hely beállításai újra lesznek alkalmazva. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Képernyőkép a Azure Portal hozzáférési korlátozások oldaláról, amely azt mutatja, hogy az SCM-helyhez vagy az alkalmazáshoz nincs hozzáférés-korlátozás beállítva.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Egy adott Azure-beli előtérben lévő példány hozzáférésének korlátozása
Az Azure bejárati ajtóról az alkalmazásba érkező forgalom a AzureFrontDoor. backend szolgáltatás címkéjében meghatározott IP-címtartományok ismert készletével származik. A szolgáltatási címke korlátozási szabályának használatával korlátozhatja a forgalmat, hogy csak az Azure bejárati ajtóból származzon. Annak biztosítása érdekében, hogy a forgalom csak az adott példányból származzon, további szűrést kell végeznie a beérkező kéréseket az Azure-beli bejárati ajtó által küldött egyedi HTTP-fejléc alapján.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Képernyőkép a Azure Portal &quot;hozzáférési korlátozások&quot; oldaláról, amely bemutatja, hogyan adható hozzá az Azure-beli elülső ajtó korlátozása.":::

PowerShell-példa:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Hozzáférés-korlátozási szabályok programozott kezelése

A következő módszerek egyikével adhat hozzáférési korlátozásokat programozott módon: 

* Használja [Az Azure CLI](/cli/azure/webapp/config/access-restriction)-t. Például:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule)használata. Például:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > A szolgáltatási címkék, a HTTP-fejlécek vagy a többforrásos szabályok használata legalább 5.7.0-verziót igényel. A telepített modul verziójának ellenőrzéséhez a: **Get-InstalledModule-Name** az

Az értékeket manuálisan is megadhatja a következők valamelyikével:

* Használjon [Azure REST API](/rest/api/azure/) Put műveletet a Azure Resource Manager alkalmazás-konfigurációjában. Az információk helye Azure Resource Manager a következő:

  management.azure.com/subscriptions/**előfizetés**-azonosító **/resourceGroups//Providers/Microsoft.Web/Sites/****webalkalmazás neve**/config/web? API-Version = 2020-06-01

* Használjon Resource Manager-sablont. Például használhatja a resources.azure.com, és szerkesztheti a ipSecurityRestrictions-blokkot a szükséges JSON hozzáadásához.

  A korábbi példa JSON-szintaxisa a következő:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  A Service tag és a HTTP-fejléc korlátozását használó speciális példa JSON-szintaxisa a következő:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Hozzáférési korlátozások beállítása Azure Functions

A hozzáférési korlátozások a App Service csomagokkal megegyező funkciókkal rendelkező Function apps-alkalmazásokhoz is elérhetők. Ha engedélyezi a hozzáférési korlátozásokat, a Azure Portal Kódszerkesztő is letiltható a nem engedélyezett IP-címekhez.

## <a name="next-steps"></a>Következő lépések
[Azure Functions hozzáférési korlátozásai](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integráció a szolgáltatási végpontokkal](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md