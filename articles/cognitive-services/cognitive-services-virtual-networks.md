---
title: Virtuális hálózatok
titleSuffix: Azure Cognitive Services
description: A Cognitive Services-erőforrások rétegzett hálózati biztonságának konfigurálása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: eaffa535b51b786a53f1e6cc35233c55dd837233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99989080"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Az Azure Cognitive Services virtuális hálózatainak konfigurálása

Az Azure Cognitive Services többrétegű biztonsági modellt biztosít. Ez a modell lehetővé teszi a Cognitive Services-fiókok védelmét a hálózatok egy adott részhalmaza számára. A hálózati szabályok konfigurálásakor csak azok az alkalmazások férhetnek hozzá a fiókhoz, amelyek a megadott hálózatokon keresztül kérik le az adatokat. Az erőforrásokhoz való hozzáférést kérelemszűréssel korlátozhatja. Csak a megadott IP-címekről, IP-tartományokról vagy az [Azure virtuális hálózatokban](../virtual-network/virtual-networks-overview.md)lévő alhálózatok listájáról származó kérelmek engedélyezése.

Egy Cognitive Services erőforráshoz hozzáférő alkalmazás, ha a hálózati szabályok érvényben vannak, engedélyezésre van szükség. Az engedélyezés [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) hitelesítő adatokkal vagy egy érvényes API-kulccsal támogatott.

> [!IMPORTANT]
> A tűzfalszabályok bekapcsolásával a Cognitive Services fiók alapértelmezés szerint blokkolja a bejövő kérelmeket. A kérések engedélyezéséhez a következő feltételek egyikének teljesülnie kell:

> * A kérelemnek egy Azure Virtual Networkon (VNet) belül működő szolgáltatásból kell származnia, amely a cél Cognitive Services fiók engedélyezett alhálózatok listáján található. A VNet származó kérelmekben lévő végpontot a Cognitive Services fiókjának [Egyéni altartományának](cognitive-services-custom-subdomains.md) kell beállítania.
> * Vagy a kérelemnek az IP-címek engedélyezett listájáról kell származnia.
>
> A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A Cognitive Services-erőforrás biztonságossá tételéhez először konfigurálnia kell egy olyan szabályt, amely alapértelmezés szerint letiltja az összes hálózatról (beleértve az internetes forgalmat) érkező forgalom elérését. Ezután olyan szabályokat kell konfigurálnia, amelyek hozzáférést biztosítanak az adott virtuális hálózatok érkező forgalomhoz. Ez a konfiguráció lehetővé teszi az alkalmazások biztonságos hálózati határának kiépítését. Olyan szabályokat is beállíthat, amelyek hozzáférést biztosítanak a forgalomhoz a nyilvános internetes IP-címtartományok kiválasztásával, valamint az adott internetes vagy helyszíni ügyfelek kapcsolatainak engedélyezésével.

A hálózati szabályok érvénybe léptetése az Azure Cognitive Services összes hálózati protokollján történik, beleértve a REST és a WebSocket szolgáltatást is. Ha az Azure-tesztkörnyezet eszközeivel szeretné elérni az adatelérést, explicit hálózati szabályokat kell konfigurálnia. A hálózati szabályok alkalmazhatók meglévő Cognitive Services erőforrásokra, illetve új Cognitive Services erőforrások létrehozásakor is. A hálózati szabályok alkalmazása után minden kérelem esetében érvénybe lépnek.

## <a name="supported-regions-and-service-offerings"></a>Támogatott régiók és szolgáltatási ajánlatok

A virtuális hálózatok (virtuális hálózatok) támogatottak azokon a [régiókban, ahol elérhetők a Cognitive Services](https://azure.microsoft.com/global-infrastructure/services/). Cognitive Services támogatja a hálózati szabályok konfigurálásához szükséges szolgáltatási címkéket. Az alább felsorolt szolgáltatások a **CognitiveServicesManagement** szolgáltatás címkéjén találhatók.

> [!div class="checklist"]
> * Anomáliadetektor
> * Computer Vision
> * Content Moderator
> * Custom Vision
> * Arcfelismerés
> * Form Recognizer
> * Modern olvasó
> * Language Understanding (LUIS)
> * Personalizer
> * Beszédfelismerési szolgáltatások
> * Text Analytics
> * QnA Maker
> * Translator Text


> [!NOTE]
> Ha LUIS vagy Speech Services szolgáltatást használ, a **CognitiveServicesManagement** címke csak az SDK vagy a REST API használatával teszi lehetővé a szolgáltatás használatát. A LUIS Portal és/vagy a Speech Studio virtuális hálózatról való eléréséhez és használatához a következő címkéket kell használnia:  
> * **AzureActiveDirectory**
> * **AzureFrontDoor. frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása

Alapértelmezés szerint a Cognitive Services-erőforrások minden hálózaton fogadnak kapcsolatokat az ügyfelektől. A kijelölt hálózatok hozzáférésének korlátozásához elsőként módosítania kell az alapértelmezett műveletet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet az alkalmazások Azure Cognitive Serviceshoz való kapcsolódására. Ha az alapértelmezett hálózati szabályt állítja **be, az letiltja** az összes hozzáférését az összes adathoz **, kivéve** , ha a hozzáférést biztosító meghatározott hálózati szabályok is érvényesek. Ügyeljen arra, hogy az alapértelmezett szabály hozzáférés-megtagadásra való módosítása előtt engedélyezze a hálózati szabályokat alkalmazó engedélyezett hálózatokhoz való hozzáférést. Ha engedélyezi a helyszíni hálózat IP-címeinek listázását, ügyeljen arra, hogy az összes lehetséges kimenő nyilvános IP-címet hozzáadja a helyszíni hálózatról.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

Cognitive Services erőforrások alapértelmezett hálózati hozzáférési szabályait a Azure Portal, a PowerShell vagy az Azure CLI segítségével kezelheti.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Lépjen a védeni kívánt Cognitive Services erőforráshoz.

1. Válassza ki a **virtuális hálózat** nevű **erőforrás-kezelési** menüt.

   ![Virtuális hálózat lehetőség](media/vnet/virtual-network-blade.png)

1. Ha alapértelmezés szerint szeretné megtagadni a hozzáférést, válassza a **kijelölt hálózatokból** való hozzáférés engedélyezése lehetőséget. Ha a **kiválasztott hálózatok** esetében egyedül a konfigurált **virtuális hálózatok** vagy **címtartományok** nincsenek társítva, akkor az összes hozzáférés ténylegesen meg lesz tagadva. Ha minden hozzáférés meg van tagadva, a Cognitive Services erőforrást felhasználó kérelmek nem engedélyezettek. A Azure Portal, Azure PowerShell vagy az Azure CLI továbbra is használható a Cognitive Services erőforrás konfigurálásához.
1. Ha minden hálózatról engedélyezni szeretné a forgalmat, engedélyezze a hozzáférést az **Összes hálózatnak**.

   ![Virtuális hálózatok – megtagadás](media/vnet/virtual-network-deny.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps) , és [Jelentkezzen](/powershell/azure/authenticate-azureps)be, vagy válassza a **kipróbálás** lehetőséget.

1. Megjeleníti a Cognitive Services erőforrás alapértelmezett szabályának állapotát.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Alapértelmezés szerint a hálózati hozzáférés megtagadásához állítsa be az alapértelmezett szabályt.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Állítsa be az alapértelmezett szabályt úgy, hogy a hálózati hozzáférés alapértelmezés szerint engedélyezve legyen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen](/cli/azure/authenticate-azure-cli)be, vagy válassza a **kipróbálás** lehetőséget.

1. Megjeleníti a Cognitive Services erőforrás alapértelmezett szabályának állapotát.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Alapértelmezés szerint a hálózati hozzáférés megtagadásához állítsa be az alapértelmezett szabályt.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Állítsa be az alapértelmezett szabályt úgy, hogy a hálózati hozzáférés alapértelmezés szerint engedélyezve legyen.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Hozzáférés biztosítása egy virtuális hálózattól

Cognitive Services erőforrásokat úgy konfigurálhatja, hogy csak adott alhálózatokról engedélyezze a hozzáférést. Az engedélyezett alhálózatok ugyanahhoz az előfizetéshez tartozó VNet, vagy egy másik előfizetéshez tartoznak, beleértve a másik Azure Active Directory bérlőhöz tartozó előfizetéseket is.

Engedélyezzen egy [szolgáltatási végpontot](../virtual-network/virtual-network-service-endpoints-overview.md) az Azure Cognitive Services számára a VNet belül. A szolgáltatás végpontja az Azure Cognitive Services szolgáltatásának optimális elérési útján irányítja át a forgalmat a VNet. Az alhálózat és a virtuális hálózat identitásait is továbbítjuk az egyes kérésekhez. A rendszergazdák ezután konfigurálhatják a Cognitive Services erőforráshoz tartozó hálózati szabályokat, amelyek lehetővé teszik a kérelmek fogadását egy adott alhálózatról egy VNet. Azok az ügyfelek, akik ezen hálózati szabályokon keresztül kaptak hozzáférést, továbbra is meg kell felelniük az Cognitive Services erőforrás engedélyezési követelményeinek az adat eléréséhez.

Minden Cognitive Services erőforrás legfeljebb 100 virtuális hálózati szabályt támogat, amelyek az [IP-hálózati szabályokkal](#grant-access-from-an-internet-ip-range)kombinálhatók.

### <a name="required-permissions"></a>Szükséges engedélyek

Ha egy virtuális hálózati szabályt Cognitive Services erőforrásra kíván alkalmazni, a felhasználónak rendelkeznie kell a megfelelő engedélyekkel az alhálózatok hozzáadásához. A szükséges engedély az alapértelmezett *közreműködő* szerepkör vagy a *Cognitive Services közreműködő* szerepkör. A szükséges engedélyek hozzáadhatók egyéni szerepkör-definícióhoz is.

Cognitive Services erőforrás és a hozzáférést kapott virtuális hálózatok különböző előfizetésekben lehetnek, beleértve az olyan előfizetéseket, amelyek egy másik Azure AD-bérlő részét képezik.

> [!NOTE]
> A virtuális hálózatok olyan alhálózatokhoz való hozzáférését biztosító szabályok konfigurálása, amelyek egy másik Azure Active Directory bérlő részét képezik, jelenleg csak a PowerShell, a CLI és a REST API-k támogatják. Ezek a szabályok nem konfigurálhatók a Azure Portalon keresztül, de a portálon is megtekinthetők.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése

Cognitive Services erőforrások virtuális hálózati szabályait a Azure Portal, a PowerShell vagy az Azure CLI segítségével kezelheti.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Lépjen a védeni kívánt Cognitive Services erőforráshoz.

1. Válassza ki a **virtuális hálózat** nevű **erőforrás-kezelési** menüt.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból** való hozzáférés engedélyezését választotta.

1. Egy meglévő hálózati szabállyal rendelkező virtuális hálózathoz való hozzáférés biztosításához a **virtuális hálózatok** területen válassza a **meglévő virtuális hálózat hozzáadása** elemet.

   ![Meglévő vNet hozzáadása](media/vnet/virtual-network-add-existing.png)

1. Válassza ki a **virtuális hálózatok** és **alhálózatok** beállításait, majd válassza az **Engedélyezés** lehetőséget.

   ![Meglévő vNet-részletek hozzáadása](media/vnet/virtual-network-add-existing-details.png)

1. Új virtuális hálózat létrehozásához és az IT-hozzáférés biztosításához válassza az **új virtuális hálózat hozzáadása** elemet.

   ![Új vNet hozzáadása](media/vnet/virtual-network-add-new.png)

1. Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd válassza a **Létrehozás** lehetőséget.

   ![VNet létrehozása](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Ha az Azure Cognitive Services szolgáltatásbeli végpont korábban nem lett konfigurálva a kiválasztott virtuális hálózathoz és alhálózatokhoz, akkor a művelet részeként konfigurálhatja.
    >
    > Jelenleg csak az ugyanahhoz a Azure Active Directory bérlőhöz tartozó virtuális hálózatok jelennek meg a szabályok létrehozásakor. Egy másik bérlőhöz tartozó virtuális hálózatban lévő alhálózathoz való hozzáférés biztosításához használja a PowerShell, a CLI vagy a REST API-kat.

1. Virtuális hálózat vagy alhálózat szabályának eltávolításához válassza a **...** lehetőséget a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd válassza az **Eltávolítás** lehetőséget.

   ![VNet eltávolítása](media/vnet/virtual-network-remove.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps) , és [Jelentkezzen](/powershell/azure/authenticate-azureps)be, vagy válassza a **kipróbálás** lehetőséget.

1. Virtuális hálózati szabályok listázása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Az Azure Cognitive Services szolgáltatás végpontjának engedélyezése meglévő virtuális hálózaton és alhálózaton.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat hálózati szabályának hozzáadásához használjon egy teljesen minősített **VirtualNetworkResourceId** paramétert "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name" formátumban.

1. Hálózati szabály eltávolítása egy virtuális hálózat és alhálózat számára.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen](/cli/azure/authenticate-azure-cli)be, vagy válassza a **kipróbálás** lehetőséget.

1. Virtuális hálózati szabályok listázása.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Az Azure Cognitive Services szolgáltatás végpontjának engedélyezése meglévő virtuális hálózaton és alhálózaton.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat szabályának hozzáadásához használjon egy teljesen minősített alhálózati azonosítót a "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" formában.
    > 
    > Az **előfizetés** paraméter használatával lekérheti az alhálózati azonosítót egy másik Azure ad-bérlőhöz tartozó VNet.

1. Hálózati szabály eltávolítása egy virtuális hálózat és alhálózat számára.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás** értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférés biztosítása internetes IP-címtartományról

Cognitive Services erőforrásokat úgy konfigurálhatja, hogy engedélyezze a hozzáférést a megadott nyilvános internetes IP-címtartományok számára. Ez a konfiguráció hozzáférést biztosít bizonyos szolgáltatásokhoz és helyszíni hálózatokhoz, és hatékonyan blokkolja az általános internetes forgalmat.

Adja meg az engedélyezett internetes címtartományt az űrlapon [CIDR-jelöléssel](https://tools.ietf.org/html/rfc4632) `16.17.18.0/24` vagy egyéni IP-címekként, például: `16.17.18.19` .

   > [!Tip]
   > A "/31" vagy a "/32" előtaggal rendelkező kisméretű címtartományok nem támogatottak. Ezeket a tartományokat egyedi IP-cím szabályokkal kell konfigurálni.

Az IP-hálózati szabályok csak a **nyilvános internetes** IP-címek esetében engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok olyan címeket tartalmaznak, amelyek a, a és a rendszertől kezdődnek `10.*` `172.16.*`  -  `172.31.*` `192.168.*` .

Jelenleg csak IPV4-címek támogatottak. Minden Cognitive Services erőforrás akár 100 IP hálózati szabályt is támogat, amelyek a [virtuális hálózati szabályokkal](#grant-access-from-a-virtual-network)kombinálhatók.

### <a name="configuring-access-from-on-premises-networks"></a>A helyszíni hálózatokhoz való hozzáférés konfigurálása

Ha a helyszíni hálózatokról egy IP-hálózati szabállyal kíván hozzáférést biztosítani a Cognitive Services erőforráshoz, meg kell határoznia a hálózat által használt internet felé irányuló IP-címeket. Segítségért forduljon a hálózati rendszergazdához.

Ha [ExpressRoute](../expressroute/expressroute-introduction.md) használ a nyilvános vagy a Microsoft-partnerek számára, meg kell határoznia a NAT IP-címeit. Nyilvános társítás esetén a ExpressRoute-áramkör alapértelmezés szerint két NAT IP-címet használ. Mindegyiket az Azure-szolgáltatás forgalmára alkalmazza a rendszer, amikor a forgalom belép a Microsoft Azure hálózati gerincbe. A Microsoft-társak esetében a használt NAT IP-címek vagy a szolgáltató által biztosított vagy biztosított ügyfelek. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

A Azure Portal, a PowerShell vagy az Azure CLI segítségével kezelheti Cognitive Services erőforrásainak IP-hálózati szabályait.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Lépjen a védeni kívánt Cognitive Services erőforráshoz.

1. Válassza ki a **virtuális hálózat** nevű **erőforrás-kezelési** menüt.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból** való hozzáférés engedélyezését választotta.

1. Ha hozzáférést szeretne biztosítani egy internetes IP-tartományhoz, adja meg az IP-címet vagy címtartományt ( [CIDR formátumban](https://tools.ietf.org/html/rfc4632)) a **tűzfal**  >  **címtartomány alatt**. Csak érvényes nyilvános IP-címet (nem fenntartott) fogad el a rendszer.

   ![IP-címtartomány hozzáadása](media/vnet/virtual-network-add-ip-range.png)

1. IP-hálózati szabály eltávolításához válassza a <span class="docon docon-delete x-hidden-focus"></span> címtartomány melletti Kuka ikont.

   ![IP-címtartomány törlése](media/vnet/virtual-network-delete-ip-range.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps) , és [Jelentkezzen](/powershell/azure/authenticate-azureps)be, vagy válassza a **kipróbálás** lehetőséget.

1. IP-hálózati szabályok listázása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Adjon hozzá egy hálózati szabályt egy egyedi IP-címhez.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Adja hozzá az IP-címtartomány hálózati szabályát.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Hálózati szabály eltávolítása egy adott IP-címhez.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen](/cli/azure/authenticate-azure-cli)be, vagy válassza a **kipróbálás** lehetőséget.

1. IP-hálózati szabályok listázása.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Adjon hozzá egy hálózati szabályt egy egyedi IP-címhez.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Adja hozzá az IP-címtartomány hálózati szabályát.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Hálózati szabály eltávolítása egy adott IP-címhez.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás** értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="use-private-endpoints"></a>Privát végpontok használata

A Cognitive Services erőforrásaihoz [saját végpontokat](../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik, hogy a virtuális hálózat (VNet) ügyfelei biztonságosan hozzáférjenek az [adatkapcsolatokhoz](../private-link/private-link-overview.md). A privát végpont IP-címet használ a Cognitive Services erőforráshoz tartozó VNet. A VNet lévő ügyfelek és az erőforrás közötti hálózati forgalom áthalad a VNet és a Microsoft gerinc hálózatán lévő privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget.

A Cognitive Services erőforrásokhoz tartozó magánhálózati végpontok a következőket teszik lehetővé:

* A Cognitive Services-erőforrás biztonságossá tételéhez konfigurálja a tűzfalat úgy, hogy az Cognitive Services szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
* A VNet biztonságának fokozása azáltal, hogy letiltja a VNet kiszűrése adatait.
* Biztonságosan csatlakozhat Cognitive Services erőforrásokhoz olyan helyszíni hálózatokból, amelyek VPN-vagy [Expressroute](../expressroute/expressroute-locations.md) [-](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

### <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont a [VNet](../virtual-network/virtual-networks-overview.md)található Azure-erőforrások speciális hálózati adaptere. A Cognitive Services erőforráshoz tartozó privát végpontok létrehozása biztonságos kapcsolatot biztosít a VNet és az erőforrás-ügyfelek között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a Cognitive Services szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a szolgáltatáshoz a magánhálózati végponton keresztül, ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni fognak. A kivétel a Speech Services, amelyhez külön végpont szükséges. Tekintse meg a [saját végpontok című szakaszt a Speech Servicesben](#private-endpoints-with-the-speech-services). A magánhálózati végpontok a Cognitive Services erőforrás által támogatott összes protokollal használhatók, beleértve a REST-et is.

A magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózaton lévő ügyfelek privát végponton keresztül csatlakozhatnak egy Cognitive Services erőforráshoz, míg más szolgáltatás-végpontok használatával is hozzáférhetnek.

Ha a VNet egy Cognitive Services-erőforráshoz hoz létre privát végpontot, a rendszer jóváhagyásra vonatkozó kérést küld a Cognitive Services erőforrás-tulajdonosnak. Ha a privát végpont létrehozását kérő felhasználó az erőforrás tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

Cognitive Services erőforrás-tulajdonosok kezelhetik a belefoglalt kérelmeket és a privát végpontokat a [Azure Portal](https://portal.azure.com)Cognitive Services erőforrásának "*privát végpontok*" lapján.

### <a name="private-endpoints"></a>Privát végpontok

A magánhálózati végpont létrehozásakor meg kell adnia azt a Cognitive Services erőforrást, amelyhez csatlakozik. A privát végpontok létrehozásával kapcsolatos további információkért lásd:

* [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../private-link/create-private-endpoint-portal.md)
* [Privát végpont létrehozása az Azure CLI-vel](../private-link/create-private-endpoint-cli.md)
* [Privát végpont létrehozása Azure PowerShell használatával](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

A privát végpontot használó VNet lévő ügyfeleknek ugyanazt a kapcsolati karakterláncot kell használniuk a Cognitive Services erőforráshoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A kivétel a Speech Services, amelyhez külön végpont szükséges. Tekintse meg a [saját végpontok című szakaszt a Speech Servicesben](#private-endpoints-with-the-speech-services). A DNS-feloldási szolgáltatás arra támaszkodik, hogy automatikusan átirányítja a kapcsolatokat a VNet a Cognitive Services erőforráshoz egy privát kapcsolaton keresztül. 

A VNet csatolt [saját DNS-zónát](../dns/private-dns-overview.md) hozunk létre a privát végpontokhoz szükséges frissítésekkel, alapértelmezés szerint. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban. Az alábbi [DNS-változások](#dns-changes-for-private-endpoints) című szakasz a privát végpontokhoz szükséges frissítéseket ismerteti.

### <a name="private-endpoints-with-the-speech-services"></a>Privát végpontok a Speech Services szolgáltatással

Lásd: a [Speech Services használata az Azure Private link által biztosított privát végpontokkal](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Privát végpont létrehozásakor a rendszer a Cognitive Services erőforráshoz tartozó DNS CNAME erőforrásrekordot a "*privatelink*" előtaggal rendelkező altartományban lévő aliasra frissíti. Alapértelmezés szerint a "*privatelink*" altartománynak megfelelő [privát DNS-zónát](../dns/private-dns-overview.md)is létrehozunk, a DNS a saját végpontokhoz tartozó erőforrásrekordokat.

Ha a végponti URL-címet a VNet kívülről a privát végpontra oldja fel, a rendszer a Cognitive Services erőforrás nyilvános végpontját oldja fel. A privát végpontot futtató VNet feloldva a végpont URL-címe feloldódik a magánhálózati végpont IP-címére.

Ez a megközelítés lehetővé teszi a Cognitive Services erőforrás elérését ugyanazzal a kapcsolati karakterlánccal, mint a privát végpontokat és a VNet kívüli ügyfeleket működtető VNet lévő ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek képesnek kell lenniük feloldani a Cognitive Services erőforrás-végpont teljes tartománynevét (FQDN) a magánhálózati végpont IP-címére. Konfigurálja úgy a DNS-kiszolgálót, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához.

> [!TIP]
> Ha egyéni vagy helyszíni DNS-kiszolgálót használ, a DNS-kiszolgálót úgy kell konfigurálnia, hogy az "privatelink" altartományban lévő Cognitive Services erőforrás nevét feloldja a magánhálózati végpont IP-címére. Ezt úgy teheti meg, hogy delegálja a "privatelink" altartományt a VNet magánhálózati DNS-zónájába, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Azure virtuális hálózatokon található erőforrások névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [A magánhálózati végpontok DNS-konfigurációja](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a különböző [Azure-Cognitive Servicesokkal](./what-are-cognitive-services.md)
* További információ az [Azure Virtual Network Service-végpontokról](../virtual-network/virtual-network-service-endpoints-overview.md)
