---
title: Végponti kvóta emelése – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) lehetővé teszi a végponti kérelmek kvótájának növelését egy adott kulcs kvótája után. Ezt úgy teheti meg, hogy több kulcsot hoz létre a LUIS számára, és hozzáadja őket a LUIS-alkalmazáshoz az **erőforrások és kulcsok** szakasz **Közzététel** lapján.
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609651"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>A végpontok kvótájának kezelése Microsoft Azure Traffic Manager használatával a kulcsok között
Language Understanding (LUIS) lehetővé teszi a végponti kérelmek kvótájának növelését egy adott kulcs kvótája után. Ezt úgy teheti meg, hogy több kulcsot hoz létre a LUIS számára, és hozzáadja őket a LUIS-alkalmazáshoz az **erőforrások és kulcsok** szakasz **Közzététel** lapján.

Az ügyfél-alkalmazásnak a kulcsokon keresztül kell kezelnie a forgalmat. LUIS nem ezt teszi.

Ez a cikk azt ismerteti, hogyan kezelhető a kulcsok közötti forgalom az Azure [Traffic Manager][traffic-manager-marketing]használatával. Már rendelkeznie kell egy betanított és közzétett LUIS-alkalmazással. Ha még nem rendelkezik ilyennel, kövesse az előre elkészített tartomány [rövid](luis-get-started-create-app.md)útmutatóját.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Kapcsolódás a PowerShellhez a Azure Portal
Az [Azure][azure-portal] Portalon nyissa meg a PowerShell ablakot. A PowerShell-ablak ikonja a felső navigációs sávban lévő **>_** . A PowerShell a portálról való használatával a PowerShell legújabb verzióját kapja meg, és Ön hitelesítve van. A portálon található PowerShell használatához [Azure Storage](https://azure.microsoft.com/services/storage/) -fiók szükséges.

![Képernyőkép a Azure Portal PowerShell-ablak megnyitásával](./media/traffic-manager/azure-portal-powershell.png)

Az alábbi fejezetek [Traffic Manager PowerShell-parancsmagokat](/powershell/module/az.trafficmanager/#traffic_manager)használnak.

## <a name="create-azure-resource-group-with-powershell"></a>Azure-erőforráscsoport létrehozása a PowerShell-lel
Az Azure-erőforrások létrehozása előtt hozzon létre egy erőforráscsoportot, amely tartalmazza az összes erőforrást. Nevezze el az erőforráscsoportot `luis-traffic-manager` , és használja a régiót `West US` . Az erőforráscsoport a csoportra vonatkozó metaadatokat tárolja. Nem lassítja le az erőforrásokat, ha egy másik régióban vannak.

Erőforráscsoport létrehozása a **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** parancsmaggal:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS-kulcsok létrehozása az összes végponti kvóta növeléséhez
1. A Azure Portal hozzon létre két **Language Understanding** kulcsot, amelyek közül az egyik a `West US` és egy `East US` . Használja az előző szakaszban létrehozott meglévő erőforráscsoportot `luis-traffic-manager` .

    ![Képernyőkép a Azure Portalról két LUIS kulccsal a Luis-Traffic-Manager erőforráscsoporthoz](./media/traffic-manager/luis-keys.png)

2. A [Luis][LUIS] webhely **kezelés** szakaszában, az **Azure-erőforrások** lapon rendeljen kulcsokat az alkalmazáshoz, és tegye közzé újból az alkalmazást a jobb felső menü **Közzététel** gombjára kattintva.

    A **végpont** oszlopban lévő példa URL-cím lekérdezési paraméterként egy Get kérést használ a Endpoint kulccsal. Másolja a két új kulcs végpontjának URL-címét. Ezek a cikk későbbi részében a Traffic Manager konfiguráció részeként használatosak.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>LUIS-végponti kérelmek kezelése a kulcsok között Traffic Manager
Traffic Manager új DNS-hozzáférési pontot hoz létre a végpontokhoz. Nem átjáróként vagy proxyként működik, de szigorúan a DNS szintjén. Ez a példa nem módosítja a DNS-rekordokat. Egy DNS-függvénytárat használ a Traffic Managersal való kommunikációhoz, hogy az adott kéréshez megfelelő végpontot kapjon. A LUIS-ra szánt _minden_ kérelemhez Traffic Manager kérelem szükséges a használni kívánt Luis-végpont meghatározásához.

### <a name="polling-uses-luis-endpoint"></a>A lekérdezés LUIS-végpontot használ
Traffic Manager rendszeresen lekérdezi a végpontokat, hogy a végpont továbbra is elérhető legyen. A lekérdezett Traffic Manager URL-címnek elérhetőnek kell lennie egy GET kérelemmel, és vissza kell térnie egy 200-as számú Ez a **közzétételi** oldalon a végpont URL-címe. Mivel az egyes végponti kulcsok eltérő útvonal-és lekérdezési karakterlánc-paraméterekkel rendelkeznek, a végponti kulcsoknak eltérő lekérdezési útvonalra van szükségük. Minden alkalommal, amikor Traffic Manager a lekérdezéseket, a kvóta megfizetését kéri. A LUIS végpont lekérdezési karakterláncának **q** paramétere a teljes érték, amelyet a Luis-nek küldtek. Ezt a paramétert a teljes művelet elküldése helyett a rendszer felveszi a LUIS Endpoint log-be Traffic Manager lekérdezéseket hibakeresési módszerként a Traffic Manager konfigurálásakor.

Mivel minden LUIS-végpontnak saját elérési útnak kell lennie, saját Traffic Manager profilra van szüksége. A profilok közötti felügyelethez hozzon létre egy [ _beágyazott_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) architektúrát. Az egyik fölérendelt profil a gyermek profilra mutat, és a rajtuk keresztül felügyeli a forgalmat.

A Traffic Manager konfigurálása után ne felejtse el módosítani az elérési utat a naplózás = false Query string paraméter használatára, hogy a napló ne legyen kitöltve lekérdezéssel.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Traffic Manager konfigurálása beágyazott profilokkal
A következő részekben két alárendelt profilt hoz létre, egyet a keleti LUIS-kulcshoz, egyet pedig a Nyugat-LUIS-kulcshoz. Ekkor létrejön egy fölérendelt profil, és a rendszer hozzáadja a két alárendelt profilt a szülő profilhoz.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Az USA keleti Traffic Manager-profiljának létrehozása a PowerShell-lel
Az USA keleti Traffic Manager profiljának létrehozásához több lépés is van: profil létrehozása, végpont hozzáadása és végpont beállítása. Egy Traffic Manager-profil több végponttal is rendelkezhet, de mindegyik végpont ugyanazzal az érvényesítési útvonallal rendelkezik. Mivel a Kelet-és Nyugat-előfizetések LUIS-végponti URL-címei eltérnek a régió és a végpont kulcsa miatt, minden LUIS-végpontnak egyetlen végpontnak kell lennie a profilban.

1. Profil létrehozása a **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** parancsmaggal

    A profil létrehozásához használja a következő parancsmagot. Ügyeljen rá, hogy módosítsa a `appIdLuis` és a `subscriptionKeyLuis` . A subscriptionKey az USA keleti régiója, LUIS kulcs. Ha az elérési út nem megfelelő, beleértve a LUIS-alkalmazás AZONOSÍTÓját és a végpont kulcsát, akkor a Traffic Manager lekérdezés állapota, `degraded` mert a forgalom kezelése nem tudja sikeresen kérni a Luis-végpontot. Győződjön meg arról, hogy az érték az, hogy `q` `traffic-manager-east` Ez az érték látható a Luis-végpont naplófájljaiban.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|Luis-profil-eastus|Traffic Manager neve Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Létrehozva az előző szakaszban|
    |-TrafficRoutingMethod|Teljesítmény|További információ: [Traffic Manager útválasztási módszerek][routing-methods]. Teljesítmény használata esetén a Traffic Manager URL-címére irányuló kérelemnek a felhasználó régiójából kell származnia. Ha egy Csevegőrobot vagy más alkalmazáson halad át, akkor a Csevegőrobot feladata, hogy utánozza a régiót a Traffic Manager hívásakor. |
    |-RelativeDnsName|Luis-DNS-eastus|Ez a szolgáltatás altartománya: luis-dns-eastus.trafficmanager.net|
    |– TTL|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Cserélje `<appIdLuis>` le `<subscriptionKeyLuis>` a és a értéket a saját értékeire.|

    Egy sikeres kérelemnek nincs válasza.

2. Az USA keleti végpontjának hozzáadása az **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** parancsmaggal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |– Végpontneve|Luis – Kelet – végpont|A profil alatt megjelenő végpont neve|
    |-TrafficManagerProfile|$eastprofile|Az 1. lépésben létrehozott profil objektum használata|
    |-Típus|ExternalEndpoints|További információ: [Traffic Manager végpont][traffic-manager-endpoints] |
    |– Cél|eastus.api.cognitive.microsoft.com|Ez a LUIS-végpont tartománya.|
    |-EndpointLocation|eastus|A végpont régiója|
    |-EndpointStatus|Engedélyezve|Végpont engedélyezése a létrehozáskor|

    A sikeres válasz a következőképpen néz ki:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Az USA keleti végpontjának beállítása a **[set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** parancsmaggal

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    A sikeres válasz a 2. lépéssel megegyező választ fog kapni.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Az USA nyugati Traffic Manager-profiljának létrehozása a PowerShell-lel
Az USA nyugati Traffic Manager profiljának létrehozásához kövesse az alábbi lépéseket: profil létrehozása, végpont hozzáadása és végpont beállítása.

1. Profil létrehozása a **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** parancsmaggal

    A profil létrehozásához használja a következő parancsmagot. Ügyeljen rá, hogy módosítsa a `appIdLuis` és a `subscriptionKeyLuis` . A subscriptionKey az USA keleti régiója, LUIS kulcs. Ha az elérési út nem megfelelő, beleértve a LUIS-alkalmazás AZONOSÍTÓját és a végpont kulcsát, akkor a Traffic Manager lekérdezés állapota, `degraded` mert a forgalom kezelése nem tudja sikeresen kérni a Luis-végpontot. Győződjön meg arról, hogy az érték az, hogy `q` `traffic-manager-west` Ez az érték látható a Luis-végpont naplófájljaiban.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|Luis-profil-westus|Traffic Manager neve Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Létrehozva az előző szakaszban|
    |-TrafficRoutingMethod|Teljesítmény|További információ: [Traffic Manager útválasztási módszerek][routing-methods]. Teljesítmény használata esetén a Traffic Manager URL-címére irányuló kérelemnek a felhasználó régiójából kell származnia. Ha egy Csevegőrobot vagy más alkalmazáson halad át, akkor a Csevegőrobot feladata, hogy utánozza a régiót a Traffic Manager hívásakor. |
    |-RelativeDnsName|Luis-DNS-westus|Ez a szolgáltatás altartománya: luis-dns-westus.trafficmanager.net|
    |– TTL|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Cserélje `<appId>` le `<subscriptionKey>` a és a értéket a saját értékeire. Ne feledje, hogy a végpont kulcsa eltér a keleti végponti kulcstól|

    Egy sikeres kérelemnek nincs válasza.

2. Az USA nyugati végpontjának hozzáadása az **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** parancsmaggal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |– Végpontneve|Luis-Nyugat-végpont|A profil alatt megjelenő végpont neve|
    |-TrafficManagerProfile|$westprofile|Az 1. lépésben létrehozott profil objektum használata|
    |-Típus|ExternalEndpoints|További információ: [Traffic Manager végpont][traffic-manager-endpoints] |
    |– Cél|westus.api.cognitive.microsoft.com|Ez a LUIS-végpont tartománya.|
    |-EndpointLocation|westus|A végpont régiója|
    |-EndpointStatus|Engedélyezve|Végpont engedélyezése a létrehozáskor|

    A sikeres válasz a következőképpen néz ki:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Az USA nyugati végpontjának beállítása a **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** parancsmaggal

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    A sikeres válasz a 2. lépéssel megegyező válasz.

### <a name="create-parent-traffic-manager-profile"></a>Szülő Traffic Manager-profil létrehozása
Hozza létre a szülő Traffic Manager profilt, és csatolja a szülőhöz két gyermek Traffic Manager profilt.

1. Szülő profil létrehozása a **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** parancsmaggal

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |-Name|Luis-profil-Parent|Traffic Manager neve Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Létrehozva az előző szakaszban|
    |-TrafficRoutingMethod|Teljesítmény|További információ: [Traffic Manager útválasztási módszerek][routing-methods]. Teljesítmény használata esetén a Traffic Manager URL-címére irányuló kérelemnek a felhasználó régiójából kell származnia. Ha egy Csevegőrobot vagy más alkalmazáson halad át, akkor a Csevegőrobot feladata, hogy utánozza a régiót a Traffic Manager hívásakor. |
    |-RelativeDnsName|Luis-DNS-Parent|Ez a szolgáltatás altartománya: luis-dns-parent.trafficmanager.net|
    |– TTL|30|Lekérdezési időköz, 30 másodperc|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A LUIS portja és protokollja HTTPS/443|
    |-MonitorPath|`/`|Ez az elérési út nem számít, mert a gyermek végpont elérési útjait használják.|

    Egy sikeres kérelemnek nincs válasza.

2. Az USA keleti gyermek-profiljának hozzáadása a **[AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** és a **NestedEndpoints** típussal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |– Végpontneve|gyermek-végpont – USEast|Kelet-profil|
    |-TrafficManagerProfile|$parentprofile|A végpont hozzárendeléséhez használandó profil|
    |-Típus|NestedEndpoints|További információ: [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– Targetresourceid azonosítója|$eastprofile. ID|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Végpont állapota a szülőhöz való hozzáadás után|
    |-EndpointLocation|eastus|Az erőforrás [Azure-régiójának neve](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|A gyermek végpontok minimális száma|

    A sikeres válasz a következőhöz hasonlóan néz ki, és az új `child-endpoint-useast` végpontot tartalmazza:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Az Amerikai Egyesült államokbeli gyermek profil hozzáadása a szülőhöz az **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** parancsmaggal és a **NestedEndpoints** típussal

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Ez a táblázat a parancsmag egyes változóit ismerteti:

    |Konfigurációs paraméter|Változó neve vagy értéke|Cél|
    |--|--|--|
    |– Végpontneve|gyermek-végpont – USWest|Nyugat-profil|
    |-TrafficManagerProfile|$parentprofile|A végpont hozzárendeléséhez használandó profil|
    |-Típus|NestedEndpoints|További információ: [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– Targetresourceid azonosítója|$westprofile. ID|A gyermek profil azonosítója|
    |-EndpointStatus|Engedélyezve|Végpont állapota a szülőhöz való hozzáadás után|
    |-EndpointLocation|westus|Az erőforrás [Azure-régiójának neve](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|A gyermek végpontok minimális száma|

    A sikeres válasz ugyanúgy néz ki, mint az előző `child-endpoint-useast` végpont és az új `child-endpoint-uswest` végpont is:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Végpontok beállítása a **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** parancsmaggal

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    A sikeres válasz ugyanaz a válasz, mint a 3. lépés.

### <a name="powershell-variables"></a>PowerShell-változók
Az előző szakaszban három PowerShell-változó lett létrehozva: `$eastprofile` , `$westprofile` , `$parentprofile` . Ezek a változók a Traffic Manager konfigurációjának vége felé használatosak. Ha úgy döntött, hogy nem hozza létre a változókat, vagy elfelejtette a PowerShell-ablakot, a **[Get-AzTrafficManagerProfile PowerShell-](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** parancsmag segítségével újra beolvashatja a profilt, és hozzárendelheti egy változóhoz.

Cserélje le az elemeket szögletes zárójelek közé, `<>` és a megfelelő értékeket adja meg a három szükséges profilhoz.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Traffic Manager működésének ellenőrzése
Annak ellenőrzéséhez, hogy a Traffic Manager-profilok működnek-e, a profiloknak `Online` ennek az állapotnak az állapotát a végpont lekérdezési útvonalán kell alapulnia.

### <a name="view-new-profiles-in-the-azure-portal"></a>Új profilok megtekintése a Azure Portal
Az erőforráscsoport erőforrásainak megtekintésével ellenőrizheti, hogy mindhárom profil létrejött-e `luis-traffic-manager` .

![A Luis-Traffic-Manager Azure-erőforráscsoport képernyőképe](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Ellenőrizze, hogy a profil állapota online állapotban van-e
A Traffic Manager lekérdezi az egyes végpontok elérési útját, hogy az online állapotú legyen. Ha online állapotú, a gyermek profilok állapota `Online` . Ez az egyes profilok **áttekintésében** jelenik meg.

![Képernyőfelvétel az Azure Traffic Manager profilról – áttekintés az online figyelő állapotáról](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager-lekérdezés működésének ellenőrzése
A Traffic Manager-lekérdezések ellenőrzésének egy másik módja a LUIS Endpoint logs. A [Luis][LUIS] webhely-alkalmazások listája lapon exportálja az alkalmazáshoz tartozó végponti naplót. Mivel Traffic Manager gyakran a két végpont esetében, a naplók bejegyzései is megtalálhatók, még akkor is, ha azok csak néhány percen belül voltak. Ne felejtse el megkeresni azokat a bejegyzéseket, amelyekben a lekérdezés kezdődik `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>A Traffic Manager Works DNS-válaszának ellenőrzése
Annak ellenőrzéséhez, hogy a DNS-válasz LUIS-végpontot ad vissza, a DNS-ügyfél függvénytárának használatával kérje meg a forgalmat a szülő profil DNS szolgáltatásával. A szülő profil DNS-neve: `luis-dns-parent.trafficmanager.net` .

A következő Node.js kód egy kérést küld a szülő profilhoz, és egy LUIS-végpontot ad vissza:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A LUIS-végpont sikeres válasza:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>A Traffic Manager szülő profil használata
A végpontok közötti forgalom kezeléséhez be kell szúrnia egy hívást a Traffic Manager DNS-be a LUIS-végpont megtalálásához. Ezt a hívást minden LUIS-végponti kérelemhez meg kell hívni, és a LUIS ügyfélalkalmazás felhasználójának földrajzi helyét szimulálni kell. Adja hozzá a DNS-választ a LUIS ügyfélalkalmazás és a LUIS-re irányuló kérelem között a végpont előrejelzéséhez.

## <a name="resolving-a-degraded-state"></a>Csökkentett teljesítményű állapot feloldása

Engedélyezze a [diagnosztikai naplókat](../../traffic-manager/traffic-manager-diagnostic-logs.md) a Traffic Manager számára, hogy megtudja, miért romlik a végpont állapota.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Távolítsa el a két LUIS Endpoint Key, a három Traffic Manager profilt és az öt erőforrást tartalmazó erőforráscsoportot. Ez a Azure Portalból történik. Törli az öt erőforrást az erőforrások listából. Ezután törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Tekintse át a Botframework témakörben v4 [middleware](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) -beállításait, hogy megtudja, hogyan vehetők fel ez a Traffic Management kód egy botframework témakörben-robotba.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md