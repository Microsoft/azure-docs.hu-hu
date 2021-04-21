---
title: Azure App Service hozzáférési korlátozások
description: Megtudhatja, hogyan biztosíthatja az alkalmazás biztonságossá Azure App Service hozzáférési korlátozások beállításával.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 541af6d0051d06de5721b22616fbf1e2867b71d6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833363"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Hozzáférési Azure App Service beállítása

A hozzáférési korlátozások beállításával prioritás szerint rendezett engedélyező/megtagadási listát határozhat meg, amely szabályozza az alkalmazás hálózati hozzáférését. A lista IP-címeket vagy Azure-beli Virtual Network tartalmazhat. Ha egy vagy több bejegyzés van, a lista végén egy *implicit* tiltás is megjelenik.

A hozzáférés-korlátozási képesség az összes Azure App Service számítási feladatokkal működik. A számítási feladatok közé tartozhatnak a webalkalmazások, az API-alkalmazások, a Linux-alkalmazások, a Linux-tárolóalkalmazások és a Functions.

Amikor kérést ad az alkalmazásnak, a RENDSZER kiértékeli a FROM címet a hozzáférés-korlátozási listában található szabályok alapján. Ha a FROM cím a Microsoft.Web szolgáltatásvégpontjaival konfigurált alhálózaton található, a rendszer összehasonlítja a forrás alhálózatot a hozzáférési korlátozások listájában található virtuális hálózati szabályokkal. Ha a cím hozzáférése nem engedélyezett a listában található szabályok alapján, a szolgáltatás [EGY HTTP 403-as](https://en.wikipedia.org/wiki/HTTP_403) állapotkóddal válaszol.

A hozzáférés-korlátozási képesség az elő App Service szerepkörben valósul meg, amelyek a kódot tartalmazó feldolgozó gazdagépek upstreamében vannak. Ezért a hozzáférés-korlátozások gyakorlatilag hálózati hozzáférés-vezérlési listák (ACL-ek).

Az Azure-beli virtuális hálózatokról a webalkalmazáshoz való hozzáférés korlátozása a [szolgáltatásvégpontokkal engedélyezhető.][serviceendpoints] A szolgáltatásvégpontokkal korlátozhatja a több-bérlős szolgáltatásokhoz való hozzáférést a kiválasztott alhálózatok számára. Nem működik a forgalom korlátozása az egy adott környezetben üzemeltetett App Service Environment. Ha egy alkalmazáshoz App Service Environment, IP-címszabályok alkalmazásával szabályozhatja az alkalmazáshoz való hozzáférést.

> [!NOTE]
> A szolgáltatásvégpontokat a hálózat és az Azure-szolgáltatás számára is engedélyezni kell, amihez engedélyezve vannak. A szolgáltatásvégpontokat támogató Azure-szolgáltatások listáját lásd: Virtual Network [szolgáltatásvégpontokat.](../virtual-network/virtual-network-service-endpoints-overview.md)
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="A hozzáférési korlátozások folyamatának ábrája.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Hozzáférés-korlátozási szabályok kezelése a portálon

Ha hozzáférés-korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, tegye a következőket:

1. Jelentkezzen be az Azure Portalra.

1. A bal oldali panelen válassza a **Hálózat lehetőséget.**

1. A Hálózat **panelen a** Hozzáférési korlátozások **alatt** válassza a **Hozzáférési korlátozások konfigurálása lehetőséget.**

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Képernyőkép a App Service beállítások panelről a Azure Portal.":::

1. A Hozzáférési **korlátozások lapon** tekintse át az alkalmazáshoz definiált hozzáférés-korlátozási szabályok listáját.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Képernyőkép a hozzáférés-korlátozások lapról a Azure Portal, a kiválasztott alkalmazáshoz definiált hozzáférési korlátozási szabályok listájával.":::

   A lista megjeleníti az alkalmazásra érvényes összes jelenlegi korlátozást. Ha az alkalmazásra virtuális hálózati korlátozás vonatkozik, a táblázatban látható, hogy a szolgáltatásvégpont engedélyezve van-e a Microsoft.Web számára. Ha az alkalmazásra nincsenek korlátozások meghatározva, az alkalmazás bárhonnan elérhető lesz.

### <a name="add-an-access-restriction-rule"></a>Hozzáférés-korlátozási szabály hozzáadása

Ha hozzáférés-korlátozási szabályt szeretne hozzáadni az alkalmazáshoz, a Hozzáférési **korlátozások panelen** válassza a **Szabály hozzáadása lehetőséget.** A szabály hozzáadása után az azonnal hatályba lép. 

A szabályok prioritási sorrendben vannak kikényszeredve, a Prioritás oszlop legalacsonyabb **számának megfelelően.** Az összes *implicit elutasítása* csak egyetlen szabály hozzáadása után lesz érvényben.

A Hozzáférési **korlátozás hozzáadása panelen** a szabály létrehozásakor tegye a következőket:

1. A **Művelet alatt** válassza az Allow **(Engedélyez) vagy** a Deny **(Megtagadás) lehetőséget.**  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="A &quot;Hozzáférés-korlátozás hozzáadása&quot; panel képernyőképe.":::

1. Ha szükséges, adja meg a szabály nevét és leírását.
1. A Prioritás **mezőben** adjon meg egy prioritásértéket.
1. A Típus **legördülő** listában válassza ki a szabály típusát.

A különböző szabálytípusokat a következő szakaszok ismertetik.

> [!NOTE]
> - A korlát 512 hozzáférés-korlátozási szabály. Ha 512-esnél több hozzáférés-korlátozási szabályra van szüksége, javasoljuk, hogy telepítsen egy különálló biztonsági terméket, például az Azure Front Door-t, az Azure App Gateway-t vagy egy alternatív WAF-et.
>
#### <a name="set-an-ip-address-based-rule"></a>IP-címalapú szabály beállítása

Kövesse az előző szakaszban leírt eljárást, de a következő kiegészítéssel:
* A 4. lépésnél a **Típus legördülő** listában válassza az **IPv4 vagy** az **IPv6 lehetőséget.** 

Adja meg **az IP-címblokkot** a classless Inter-Domain Routing (CIDR) (Útválasztás (CIDR) beállításban az IPv4- és IPv6-címekhez egyaránt. Cím megadásához az *1.2.3.4/32* címhez hasonlót használhat, ahol az első négy oktett az Ön IP-címét, a */32* pedig a maszkot jelenti. Az IPv4 CIDR-notation minden címhez a következő: 0.0.0.0/0. A CIDR-notationról további információt a [Classless Inter-Domain Routing (Osztály nélküli útválasztás) Inter-Domain.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

#### <a name="set-a-service-endpoint-based-rule"></a>Szolgáltatásvégpont-alapú szabály beállítása

* A 4. lépésnél a **Típus legördülő** listában válassza a **Virtual Network** lehetőséget.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="A &quot;Korlátozás hozzáadása&quot; panel képernyőképe a kiválasztott Virtual Network típussal.":::

Adja meg **az Előfizetés,** **Virtual Network,** és **Alhálózat** legördülő listát, amelyek megegyezik a hozzáférés korlátozásához szükséges beállításokkal.

A szolgáltatásvégpontokkal korlátozhatja a hozzáférést a kiválasztott Azure-beli virtuális hálózati alhálózatokra. Ha a szolgáltatásvégpont még nincs engedélyezve a Microsoft.Web szolgáltatással a kiválasztott alhálózaton, akkor azok automatikusan engedélyezve lesznek, hacsak be nem választja a Hiányzó **Microsoft.Web** szolgáltatásvégpont figyelmen kívül hagyása jelölőnégyzetet. Az a forgatókönyv, ahol érdemes lehet engedélyezni a szolgáltatásvégpontokat az alkalmazásban, de az alhálózatot nem, elsősorban attól függ, hogy rendelkezik-e az alhálózaton való engedélyezéshez szükséges engedélyekkel. 

Ha valaki másra van szüksége a szolgáltatásvégpont engedélyezéséhez az alhálózaton, jelölje be a Hiányzó **Microsoft.Web** szolgáltatásvégpont figyelmen kívül hagyása jelölőnégyzetet. Az alkalmazás úgy lesz konfigurálva a szolgáltatásvégponthoz, hogy később engedélyezni fogja őket az alhálózaton. 

A szolgáltatásvégpontokkal nem korlátozhatja a hozzáférését a App Service Environment. Ha az alkalmazás egy App Service Environment, IP-hozzáférési szabályok alkalmazásával szabályozhatja a hozzáférését. 

A szolgáltatásvégpontokkal alkalmazásátjárókat vagy más webalkalmazási tűzfaleszközöket (WAF) is konfigurálhat az alkalmazáshoz. Többrétegű alkalmazásokat is konfigurálhat biztonságos háttéralkalmazásokkal. További információ: [Hálózati](networking-features.md) szolgáltatások és App Service és [Application Gateway integráció szolgáltatásvégpontokkal.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - A szolgáltatásvégpontokat jelenleg nem támogatják az OLYAN webalkalmazások, amelyek IP-SSL (SSL) virtuális IP-címet (VIP) használnak.
>
#### <a name="set-a-service-tag-based-rule"></a>Szolgáltatáscímke-alapú szabály beállítása

* A 4. lépés Típus **legördülő** listájában válassza a **Szolgáltatáscímke lehetőséget.**

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="A &quot;Korlátozás hozzáadása&quot; panel képernyőképe a kiválasztott Szolgáltatáscímke típussal.":::

Minden szolgáltatáscímke az Azure-szolgáltatásokBÓL származó IP-tartományok listáját jelöli. A szolgáltatások listája és az adott tartományokra mutató hivatkozások a szolgáltatáscímke [dokumentációjában találhatók.][servicetags]

A hozzáférés-korlátozási szabályok minden elérhető szolgáltatáscímkét támogatnak. Az egyszerűség kedvéért csak a leggyakoribb címkék listája érhető el a Azure Portal. A Azure Resource Manager vagy szkriptek használatával speciális szabályokat konfigurál, például regionális hatókörű szabályokat. A következő címkék érhetők el a Azure Portal:

* ActionGroup (Műveletcsoport)
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Szabály szerkesztése

1. Egy meglévő hozzáférés-korlátozási szabály  szerkesztésének megkezdéséhez a Hozzáférési korlátozások lapon válassza ki a szerkeszteni kívánt szabályt.

1. A Hozzáférési **korlátozás szerkesztése panelen** módosítsa a módosításokat, majd válassza a **Szabály frissítése lehetőséget.** A módosítások azonnal hatnak, beleértve a prioritásrendezés változásait is.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Képernyőkép a hozzáférés-korlátozás szerkesztésére vonatkozó panelről a Azure Portal, amely egy meglévő hozzáférés-korlátozási szabály mezőit mutatja.":::

   > [!NOTE]
   > Szabály szerkesztésekor nem válthat a szabálytípusok között. 

### <a name="delete-a-rule"></a>Szabály törlése

Szabály törléséhez a  Hozzáférési korlátozások lapon válassza a törölni kívánt szabály melletti három pont **(...**), majd az Eltávolítás **lehetőséget.**

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="A &quot;Hozzáférési korlátozások&quot; lap képernyőképe, amely a törölni szükséges hozzáférési korlátozási szabály melletti &quot;Eltávolítás&quot; három ponttal jelenik meg.":::

## <a name="access-restriction-advanced-scenarios"></a>Hozzáférés-korlátozás – speciális forgatókönyvek
A következő szakaszok néhány speciális forgatókönyvet ismertetnek, amelyek hozzáférési korlátozásokat alkalmaznak.

### <a name="filter-by-http-header"></a>Szűrés HTTP-fejléc alapján

Bármely szabály részeként hozzáadhat további HTTP-fejlécszűrőket. A következő HTTP-fejlécnevek támogatottak:
* X-Forwarded-for
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Minden fejlécnévhez legfeljebb 8, vesszővel elválasztott értéket adhat hozzá. A HTTP-fejléc szűrői a szabály után lesznek kiértékelve, és mindkét feltételnek igaznak kell lennie ahhoz, hogy a szabály alkalmazva legyen.

### <a name="multi-source-rules"></a>Több forrásra vonatkozó szabályok

A több forrásból származó szabályok segítségével egyetlen szabályban legfeljebb 8 IP-címtartományt vagy 8 szolgáltatáscímkét kombinálhatja. Ezt akkor használhatja, ha 512-esnél több IP-címtartománya van, vagy olyan logikai szabályokat szeretne létrehozni, amelyekben több IP-címtartomány van egyetlen HTTP-fejlécszűrővel kombinálva.

A többforrásos szabályok ugyanúgy vannak definiálva, mint az egyforrásos szabályok, de az egyes tartományok vesszővel vannak elválasztva.

PowerShell-példa:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Egyetlen IP-cím blokkolása

Az első hozzáférés-korlátozási szabály hozzáadásakor a szolgáltatás hozzáad egy explicit *Minden* elutasítása szabályt 2147483647 prioritással. A gyakorlatban az explicit *Minden* elutasítása szabály lesz az utolsó végrehajtandó szabály, és letiltja a hozzáférést minden olyan IP-címhez, amelyet egy szabály nem *engedélyez explicit* módon.

Olyan forgatókönyvek esetén, ahol explicit módon blokkolni szeretne egyetlen IP-címet vagy IP-címblokkot, de minden máshoz hozzáférést szeretne adni, adjon hozzá egy explicit *Minden engedélyezése szabályt.*

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Képernyőkép a lap &quot;Hozzáférési korlátozások&quot; Azure Portal egyetlen blokkolt IP-címmel.":::

### <a name="restrict-access-to-an-scm-site"></a>SCM-webhelyhez való hozzáférés korlátozása 

Amellett, hogy szabályozhatja az alkalmazáshoz való hozzáférést, korlátozhatja az alkalmazás által használt SCM-webhelyhez való hozzáférést is. Az SCM webhely a webes üzembe helyezési végpont és a Kudu-konzol is. Az SCM-webhelyhez az alkalmazásból külön is hozzárendelhet hozzáférési korlátozásokat, vagy használhatja ugyanazt a korlátozáskészletet az alkalmazáshoz és az SCM-webhelyhez is. Ha bejelölti **a Ugyanazokkal a \<app name>** korlátozásokkal, mint a jelölőnégyzetet, minden ki lesz hagyva. Ha törli a jelölőnégyzet jelölését, a rendszer újraalkalmazhatja az SCM-hely beállításait. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Képernyőkép a lap &quot;Hozzáférési korlátozások&quot; lapjára Azure Portal, amely azt mutatja, hogy az SCM-webhelyre vagy az alkalmazásra nem vonatkoznak hozzáférési korlátozások.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Hozzáférés korlátozása egy adott Azure Front Door példányra
Az Azure Front Door forgalma az AzureFrontDoor.Backend szolgáltatáscímkében meghatározott jól ismert IP-címtartomány-készletből származik. Szolgáltatáscímke-korlátozási szabály használatával úgy korlátozhatja a forgalmat, hogy az csak a Azure Front Door. Annak biztosítása érdekében, hogy a forgalom csak az Ön adott példányából származik, tovább kell szűrni a bejövő kéréseket a küldött egyedi HTTP Azure Front Door alapján.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="A képernyőfelvételen látható &quot;Hozzáférési korlátozások&quot; lap Azure Portal, amely a korlátozás hozzáadásának Azure Front Door mutatja be.":::

PowerShell-példa:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Hozzáférés-korlátozási szabályok programozott kezelése

A hozzáférési korlátozásokat a következő lépések valamelyikével használhatja programozott módon: 

* Használja [az Azure CLI-t.](/cli/azure/webapp/config/access-restriction) Például:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Használja a [Azure PowerShell.](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule) Például:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > A szolgáltatáscímkék, HTTP-fejlécek vagy többforrású szabályok használatához legalább 5.7.0-s verzió szükséges. A telepített modul verzióját a következővel ellenőrizheti: **Get-InstalledModule -Name Az**

Az értékeket manuálisan is beállíthatja a következők valamelyikével:

* Használjon [Azure REST API](/rest/api/azure/) PUT műveletet az alkalmazáskonfiguráción a Azure Resource Manager. Ennek az információnak a helye a Azure Resource Manager:

  management.azure.com/subscriptions//resourceGroups/erőforráscsoportok /providers/Microsoft.Web/sites/**webalkalmazás** neve /config/web?api-version=2020-06-01

* Használjon Resource Manager sablont. A szükséges JSON hozzáadásához például használhatja resources.azure.com ipSecurityRestrictions blokkot.

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
  A szolgáltatáscímke és a HTTP-fejléc korlátozásának használatával egy speciális példa JSON-szintaxisa a következő:
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
## <a name="set-up-azure-functions-access-restrictions"></a>Hozzáférési Azure Functions beállítása

A hozzáférési korlátozások olyan függvényalkalmazások esetében is elérhetők, amelyek ugyanazokkal a funkciókkal App Service a csomagokat. Ha engedélyezi a hozzáférési korlátozásokat, letiltja a Azure Portal a nem engedélyezett IP-k számára.

## <a name="next-steps"></a>Következő lépések
[Hozzáférési korlátozások a Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway szolgáltatásvégpontokkal való integráció](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
