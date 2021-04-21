---
title: Azure Storage-tűzfalak és virtuális hálózatok | Microsoft Docs
description: Többrétegű hálózati biztonságot konfigurál a tárfiókhoz Az Azure Storage-tűzfalak és az Azure Virtual Network.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5e8123c252d99b2999eeef42fecae189a05e382b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778120"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-tűzfalak és virtuális hálózatok konfigurálása

Az Azure Storage rétegzett biztonsági modellel rendelkezik. Ez a modell lehetővé teszi az alkalmazások és a vállalati környezetek által megkövetelt tárfiókok hozzáférési szintjének biztonságossá és szabályozhatósá a használt hálózatok vagy erőforrások típusa és részkészlete alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon vagy azure-erőforrásokon keresztül adatokat lekért alkalmazások férhetnek hozzá a tárfiókhoz. A tárfiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokból, azure Virtual Network-hálózatban (VNet) található alhálózatból vagy egyes Azure-szolgáltatások erőforráspéldányaiból származó kérésekre.

A tárfiókok nyilvános végponttal vannak, amely az interneten keresztül érhető el. Privát végpontokat [](storage-private-endpoints.md)is létrehozhat a tárfiókhoz, amelyek a virtuális hálózatról egy magánhálózati IP-címet rendelnek a tárfiókhoz, és egy privát kapcsolaton keresztül biztosítják a virtuális hálózat és a tárfiók közötti összes forgalmat. Az Azure Storage-tűzfal hozzáférés-vezérlést biztosít a tárfiók nyilvános végpontja számára. Privát végpontok használata esetén a tűzfal használatával minden hozzáférést letilthat a nyilvános végponton keresztül. A Storage-tűzfal konfigurációja azt is lehetővé teszi, hogy a kiválasztott megbízható Azure-platformszolgáltatások biztonságosan hozzáférjenek a tárfiókhoz.

Egy olyan alkalmazásnak, amely hálózati szabályok érvényben esetén is hozzáfér a tárfiókhoz, továbbra is megfelelő engedélyre van szüksége a kéréshez. Az engedélyezés támogatott Azure Active Directory (Azure AD) hitelesítő adatokkal blobok és üzenetsorok esetén, érvényes fiókelérési kulccsal vagy SAS-jogkivonattal.

> [!IMPORTANT]
> A tárfiók tűzfalszabályainak bekapcsolása alapértelmezés szerint blokkolja az adatokra vonatkozó bejövő kéréseket, kivéve, ha a kérések egy Azure Virtual Network-hálózaton (VNet) belül működő szolgáltatásból vagy engedélyezett nyilvános IP-címekről származnak. A blokkolt kérések közé tartoznak a más Azure-szolgáltatásoktól, a Azure Portal, a naplózási és metrikaszolgáltatásoktól stb. származó kérések.
>
> A virtuális hálózatról működő Azure-szolgáltatásokhoz úgy adhat hozzáférést, hogy engedélyezi a szolgáltatáspéldányt üzemeltető alhálózatról származó forgalmat. Az alább ismertetett kivételi mechanizmussal korlátozott számú forgatókönyvet is engedélyezhet. Ahhoz, hogy az adatokat a tárfiókból a Azure Portal-kapcsolaton keresztül el tudja férni, olyan gépen kell lennie, amely a beállított megbízható határvonalon (IP-cím vagy virtuális hálózat) belül található.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A tárfiók biztonságának biztosítása érdekében először konfigurálnia kell egy szabályt, amely alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton található összes hálózatról (beleértve az internetes forgalmat is). Ezután olyan szabályokat kell konfigurálnia, amelyek adott virtuális hálózatokról származó forgalomhoz biztosítják a hozzáférést. Szabályokat is konfigurálhat a kiválasztott nyilvános IP-címtartományból származó forgalomhoz való hozzáférés engedélyezéséhez, amelyek adott internetről vagy helyszíni ügyfelekről engedélyezik a kapcsolatokat. Ezzel a konfigurációval biztonságos hálózathatárt építhet ki alkalmazásai számára.

Kombinálhatja a tűzfalszabályokat, amelyek adott virtuális hálózatokról és nyilvános IP-címtartományból engedélyezik a hozzáférést ugyanazon a tárfiókon. A Storage-tűzfalszabályok alkalmazhatók a meglévő tárfiókok esetében, vagy új tárfiókok létrehozásakor.

A Storage-tűzfalszabályok a tárfiók nyilvános végpontjára vonatkoznak. A tárfiók privát végpontjainak forgalmának engedélyezése érdekében nincs szükség tűzfal-hozzáférési szabályokra. A privát végpont létrehozásának jóváhagyása implicit hozzáférést biztosít a privát végpontot tartalmazó alhálózat forgalmához.

A hálózati szabályok az Azure Storage összes hálózati protokollja, beleértve a REST-et és az SMB-t is. Az adatok olyan eszközökkel való eléréséhez, mint a Azure Portal, Storage Explorer és az AZCopy, explicit hálózati szabályokat kell konfigurálni.

A hálózati szabályok alkalmazása után a rendszer minden kérésre érvényesíti őket. Az egy adott IP-címhez hozzáférést adó SAS-jogkivonatok korlátozzák a jogkivonat tulajdonosának hozzáférését, de a konfigurált hálózati szabályokon túl nem ják meg az új hozzáférést.

A virtuális gépek lemezforgalmát (beleértve a csatlakoztatási és leválasztási műveleteket, valamint a lemez I/O-ját) nem befolyásolják a hálózati szabályok. A lapblobok REST-hozzáférését hálózati szabályok védik.

A klasszikus tárfiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

A tárfiókok nem használt lemezei olyan hálózati szabályokkal használhatók, amelyek a virtuális gépek biztonsági mentésére és visszaállítására vonatkoznak, kivétel létrehozásával. Ezt a folyamatot a cikk [Kivételek](#manage-exceptions) kezelése szakaszában dokumentáljuk. A tűzfal kivételei nem alkalmazhatók a felügyelt lemezekre, mivel már az Azure kezeli őket.

## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező kapcsolatokat elfogadnak. A kijelölt hálózatok hozzáférésének korlátozásához elsőként módosítania kell az alapértelmezett műveletet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet az alkalmazás Azure Storage-hoz való kapcsolódására. Az alapértelmezett hálózati  szabály megtagadásra való beállítása letilt minden adatelérést, kivéve, ha a hozzáférést **megadó** konkrét hálózati szabályok is érvényesek. Ügyeljen arra, hogy az alapértelmezett szabály hozzáférés-megtagadásra való módosítása előtt engedélyezze a hálózati szabályokat alkalmazó engedélyezett hálózatokhoz való hozzáférést.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

A tárfiókok alapértelmezett hálózati hozzáférési szabályait a következő szolgáltatásokkal kezelheti: Azure Portal, PowerShell vagy CLIv2.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a megvédeni kívánt tárfiókra.

2. A Hálózat menüben válassza a **lehetőséget.**

3. A Hozzáférés alapértelmezett megtagadása beállításnál válassza a Kijelölt hálózatok **hozzáférésének engedélyezése lehetőséget.** Ha minden hálózatról engedélyezni szeretné a forgalmat, engedélyezze a hozzáférést az **Összes hálózatnak**.

4. A módosítások alkalmazásához kattintson a **Mentés** gombra.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Telepítse a [Azure PowerShell,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

2. Megjeleníti a tárfiók alapértelmezett szabályának állapotát.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Állítsa be az alapértelmezett szabályt úgy, hogy alapértelmezés szerint megtagadja a hálózati hozzáférést.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Állítsa be az alapértelmezett szabályt úgy, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

2. Megjeleníti a tárfiók alapértelmezett szabályának állapotát.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Állítsa be az alapértelmezett szabályt úgy, hogy alapértelmezés szerint megtagadja a hálózati hozzáférést.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Állítsa be az alapértelmezett szabályt úgy, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Hozzáférés megadása virtuális hálózatról

A tárfiókokat konfigurálhatja úgy, hogy csak bizonyos alhálózatok számára engedélyezzenek hozzáférést. Az engedélyezett alhálózatok ugyanannak az előfizetésnek egy virtuális hálózatához vagy egy másik előfizetéshez tartoznak, beleértve a másik bérlőhöz tartozó Azure Active Directory is.

Engedélyezzen [egy szolgáltatásvégpontot az](../../virtual-network/virtual-network-service-endpoints-overview.md) Azure Storage számára a virtuális hálózatban. A szolgáltatásvégpont a virtuális hálózatról származó forgalmat az Azure Storage szolgáltatáshoz vezető optimális útvonalon keresztül vezeti. A rendszer minden kéréssel továbbítja az alhálózat és a virtuális hálózat identitását is. A rendszergazdák ezután konfigurálhatják a tárfiókra vonatkozó hálózati szabályokat, amelyek lehetővé teszik a kérések bekérését egy virtuális hálózat adott alhálózatairól. Az ezeken a hálózati szabályokon keresztül hozzáférést kapó ügyfeleknek továbbra is meg kell felelnie a tárfiók engedélyezési követelményeinek az adatok eléréséhez.

Minden tárfiók legfeljebb 200 virtuális hálózati szabályt támogat, amelyek az [IP-hálózati szabályokkal kombinálhatók.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Elérhető virtuális hálózati régiók

Általánosságban elmondható, hogy a szolgáltatásvégpontokat ugyanabban az Azure-régióban lévő virtuális hálózatok és szolgáltatáspéldányok között lehet eltenni. Ha szolgáltatásvégpontokat használ az Azure Storage szolgáltatással, ez a hatókör úgy növekszik, hogy a [párosított régiót is tartalmazza.](../../best-practices-availability-paired-regions.md) A szolgáltatásvégpontokkal a regionális feladatátvétel során folyamatos lehet a hozzáférés a csak olvasható georedundáns tárolási (RA-GRS) példányokhoz. A virtuális hálózatról egy tárfiókhoz való hozzáférést engedélyező hálózati szabályok bármely RA-GRS-példányhoz is hozzáférést engedélyeznek.

Ha regionális kimaradás esetén tervez vészhelyreállítást, a virtuális hálózatokat előre létre kell hoznia a párosított régióban. Engedélyezze a szolgáltatásvégpontokat az Azure Storage-hoz, és adjon hozzáférést ezekhez az alternatív virtuális hálózatokhoz hálózati szabályokkal. Ezután alkalmazza ezeket a szabályokat a georedundáns tárfiókra.

> [!NOTE]
> A szolgáltatásvégpont nem vonatkozik a virtuális hálózat és a kijelölt régiópár régióján kívüli forgalomra. Csak a tárfiók elsődleges régiójában vagy a kijelölt párosított régióban lévő tárfiókok számára biztosíthatja a virtuális hálózatokról való hozzáférést engedélyező hálózati szabályokat.

### <a name="required-permissions"></a>Szükséges engedélyek

Ahhoz, hogy egy felhasználó virtuális hálózati szabályt alkalmazhasson egy tárfiókra, rendelkeznie kell a hozzáadni kívánt alhálózatokra vonatkozó megfelelő engedélyekkel. A szabályokat a Tárfiók [](../../role-based-access-control/built-in-roles.md#storage-account-contributor) közreműködője vagy egy olyan felhasználó hajthatja végre, aki egy egyéni Azure-szerepkör használatával engedélyt kapott az `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` Azure-erőforrás-szolgáltatói művelethez. [](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork)

A tárfiók és a hozzáféréssel biztosított virtuális hálózatok különböző előfizetésben lehetnek, beleértve azokat az előfizetéseket is, amelyek egy másik Azure AD-bérlő részei.

> [!NOTE]
> Az olyan szabályok konfigurációja, amelyek hozzáférést engedélyeznek egy másik bérlőhöz Azure Active Directory virtuális hálózatok alhálózatai számára, jelenleg csak a PowerShell, a PARANCSSORi felület és a REST API-k támogatják. Ezek a szabályok nem konfigurálhatóak a Azure Portal, bár megtekinthetők a portálon.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése

A tárfiókok virtuális hálózati szabályait az Azure Portal, a PowerShell vagy a CLIv2 használatával kezelheti.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a megvédeni kívánt tárfiókra.

2. A Hálózat menüben válassza a **lehetőséget.**

3. Ellenőrizze, hogy a Kijelölt hálózatokról való hozzáférés engedélyezése lehetőséget **választotta-e.**

4. Ha új hálózati s szabálysal szeretne hozzáférést adni egy virtuális hálózathoz, a Virtuális hálózatok alatt válassza a **Meglévő** virtuális hálózat hozzáadása lehetőséget, válassza a **Virtuális** hálózatok és **alhálózatok** lehetőséget, majd a Hozzáadás **lehetőséget.** Új virtuális hálózat létrehozásához és hozzáférés megadásához válassza az **Új virtuális hálózat hozzáadása lehetőséget.** Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd válassza a **Létrehozás lehetőséget.**

    > [!NOTE]
    > Ha az Azure Storage szolgáltatásvégpontját korábban nem konfigurálta a kiválasztott virtuális hálózathoz és alhálózathoz, a művelet részeként konfigurálhatja azt.
    >
    > Jelenleg csak az azonos bérlőhöz Azure Active Directory virtuális hálózatok jelennek meg kiválasztásra a szabály létrehozása során. Egy másik bérlőhöz tartozó virtuális hálózat alhálózatához való hozzáférés megadásához használja a PowerShellt, a parancssori felületet vagy a REST API-kat.

5. Virtuális hálózat vagy alhálózati szabály eltávolításához válassza a **...** lehetőséget a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd válassza az **Eltávolítás lehetőséget.**

6. A **módosítások alkalmazáshoz** válassza a Mentés lehetőséget.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Telepítse a [Azure PowerShell,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

2. List virtuális hálózati szabályok.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Engedélyezze az Azure Storage szolgáltatásvégpontját egy meglévő virtuális hálózaton és alhálózaton.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Adjon hozzá egy hálózati szabályt egy virtuális hálózathoz és egy alhálózathoz.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Ha egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózatához szeretne hálózati szabályt hozzáadni, használja a "/subscriptions/subscription-ID/resourceGroups/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnets/subnet-name" alakban lévő teljes **VirtualNetworkResourceId** paramétert.

5. Távolítsa el a virtuális hálózat és az alhálózat hálózati szabályát.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Mindenképpen [tiltsa le az alapértelmezett szabályt,](#change-the-default-network-access-rule) különben a hálózati szabályoknak nincs hatása. 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

2. List virtuális hálózati szabályok.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Engedélyezze az Azure Storage szolgáltatásvégpontját egy meglévő virtuális hálózaton és alhálózaton.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Adjon hozzá egy hálózati szabályt egy virtuális hálózathoz és egy alhálózathoz.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Ha egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózatához szeretne szabályt hozzáadni, használja a "/subscriptions/ \<subscription-ID\> /resourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ " teljes alhálózat-azonosítót. \<subnet-name\>
    >
    > Az előfizetési **paraméterrel** lekérheti egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózati azonosítóját.

5. Távolítsa el a virtuális hálózat és az alhálózat hálózati szabályát.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Mindenképpen [tiltsa le az alapértelmezett szabályt,](#change-the-default-network-access-rule) különben a hálózati szabályoknak nincs hatása. 

---

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférés megadása internetes IP-címtartományból

IP-hálózati szabályokkal ip-hálózati szabályok létrehozásával engedélyezheti a hozzáférést adott nyilvános internetes IP-címtartományból. Minden tárfiók legfeljebb 200 szabályt támogat. Ezek a szabályok meghatározott internetalapú szolgáltatásokhoz és helyszíni hálózatokhoz biztosítják a hozzáférést, és letiltják az általános internetes forgalmat.

Az IP-címtartományra az alábbi korlátozások vonatkoznak.

- Az IP-hálózati szabályok csak nyilvános **internetes IP-címekhez** engedélyezettek. 

  A magánhálózatok számára fenntartott IP-címtartományok (az [RFC 1918-ban](https://tools.ietf.org/html/rfc1918#section-3)meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok a következő címekkel kezdődnek: _10.*_, _172.16.*_  -  _172.31.*_ és _192.168.*_.

- Az engedélyezett internetes címtartományokat [](https://tools.ietf.org/html/rfc4632) CIDR-formában, *a 16.17.18.0/24* formában kell meg adnia, vagy egyéni IP-címként, *például: 16.17.18.19.* 

- A "/31" vagy "/32" előtagméretet használó kis címtartományok nem támogatottak. Ezeket a tartományokat egyéni IP-címszabályokkal kell konfigurálni. 

- A tároló tűzfalszabályainak konfigurálásában csak az IPV4-címek támogatottak.

Az IP-hálózati szabályok nem használhatók a következő esetekben:

- A tárfiókkal azonos Azure-régióban található ügyfelek hozzáférésének korlátozása.
  
  Az IP-hálózati szabályok nincsenek hatással a tárfiókkal azonos Azure-régióból származó kérelmekre. A [Virtuális hálózati szabályok használatával](#grant-access-from-a-virtual-network) engedélyezheti az azonos régióra vonatkozó kéréseket. 

- A szolgáltatásvégponttal [](../../best-practices-availability-paired-regions.md) rendelkezik virtuális hálózatban található párosított régióban található ügyfelek hozzáférésének korlátozása.

- A tárfiókkal azonos régióban üzembe helyezett Azure-szolgáltatásokhoz való hozzáférés korlátozása.

  A tárfiókkal azonos régióban üzembe helyezett szolgáltatások privát Azure IP-címeket használnak a kommunikációhoz. Így nem korlátozhatja az adott Azure-szolgáltatásokhoz való hozzáférést a nyilvános kimenő IP-címtartományuk alapján.

### <a name="configuring-access-from-on-premises-networks"></a>Helyszíni hálózatokról való hozzáférés konfigurálása

Ha a helyszíni hálózatokról IP-hálózati s szabályt használva hozzáférést ad a tárfiókhoz, azonosítania kell a hálózata által használt internetes IP-címeket. Segítségért forduljon a hálózati rendszergazdához.

Ha az [ExpressRoute-et](../../expressroute/expressroute-introduction.md) a helyszínről használja, a nyilvános társviszony-létesítéshez vagy a Microsoft-társviszony-létesítéshez azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítéshez a használt NAT IP-címeket vagy az ügyfél, vagy a szolgáltató biztosítja. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

A tárfiókok IP-hálózati szabályait az Azure Portal, a PowerShell vagy a CLIv2 használatával kezelheti.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a megvédeni kívánt tárfiókra.

2. Válassza a Hálózat menüpontot a **Beállítások menüben.**

3. Ellenőrizze, hogy a Kiválasztott hálózatokról való hozzáférés engedélyezése lehetőséget **választotta-e.**

4. Internetes IP-címtartományhoz való hozzáférés megadásához adja meg az IP-címet vagy a címtartományt (CIDR formátumban) a **Tűzfal**  >  **címtartománya alatt.**

5. IP-hálózati szabály eltávolításához válassza a címtartomány melletti kuka ikont.

6. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Telepítse a [Azure PowerShell,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

2. IP-hálózati szabályok felsorolása.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Adjon hozzá egy hálózati szabályt egy adott IP-címhez.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Adjon hozzá egy ip-címtartományra vonatkozó hálózati szabályt.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Távolítsa el az egyes IP-címekre vonatkozó hálózati szabályokat.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Távolítsa el az IP-címtartomány hálózati szabályát.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Mindenképpen [tiltsa le az alapértelmezett szabályt,](#change-the-default-network-access-rule) különben a hálózati szabályoknak nincs hatása. 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

1. IP-hálózati szabályok felsorolása.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Adjon hozzá egy hálózati szabályt egy adott IP-címhez.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Adjon hozzá egy ip-címtartományra vonatkozó hálózati szabályt.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Távolítsa el az egyes IP-címekre vonatkozó hálózati szabályokat.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Távolítsa el az IP-címtartomány hálózati szabályát.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Mindenképpen [tiltsa le az alapértelmezett szabályt,](#change-the-default-network-access-rule) különben a hálózati szabályoknak nincs hatása. 

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Hozzáférés megadása Azure-erőforráspéldányból (előzetes verzió)

Bizonyos esetekben előfordulhat, hogy egy alkalmazás olyan Azure-erőforrásoktól függ, amelyek nem különíthetők el virtuális hálózaton vagy IP-címszabályon keresztül. Azonban továbbra is csak az alkalmazás Azure-erőforrásaira szeretné korlátozni a tárfiókok hozzáférését. Egy erőforráspéldány-szabály létrehozásával konfigurálhatja a tárfiókokat úgy, hogy bizonyos Azure-szolgáltatások adott erőforráspéldányainak hozzáférést engedélyezzenek. 

Az erőforráspéldány által a tárfiók adatain végrehajtható műveletek típusait az [erőforráspéldány](storage-auth-aad.md#assign-azure-roles-for-access-rights) Azure-beli szerepkör-hozzárendelései határozzák meg. Az erőforráspéldányok a tárfiókkal azonos bérlőből kell, hogy tartozni tudjanak, de a bérlő bármelyik előfizetéséhez tartozni tudnak.

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el, és minden nyilvános felhőrégióban elérhető.

> [!NOTE]
> Az erőforráspéldány-szabályok jelenleg csak a Azure Synapse. A cikk Megbízható hozzáférés a rendszer által hozzárendelt felügyelt identitás alapján szakaszában felsorolt egyéb [Azure-szolgáltatások](#trusted-access-system-assigned-managed-identity) támogatása a következő hetekben lesz elérhető.


### <a name="portal"></a>[Portál](#tab/azure-portal)

A fürtben hozzáadhat vagy eltávolíthat erőforrás-hálózati Azure Portal.

1. A Azure Portal [jelentkezzen](https://portal.azure.com/) be.

2. Keresse meg a tárfiókot, és jelenítse meg a fiók áttekintését.

3. Válassza **a Hálózat lehetőséget** a hálózat konfigurációs oldalának megjelenítéséhez.

4. Az Erőforrás **típusa legördülő** listában válassza ki az erőforráspéldány erőforrástípusát. 

5. A Példány **neve legördülő** listában válassza ki az erőforráspéldányt. Dönthet úgy is, hogy az összes erőforráspéldányt az aktív bérlőbe, előfizetésbe vagy erőforráscsoportba foglalja.

6. A módosítások alkalmazásához kattintson a **Mentés** gombra. Az erőforráspéldány a  hálózati beállítások oldalÁnak Erőforráspéldányok szakaszában jelenik meg. 

Az erőforráspéldány eltávolításához válassza az erőforráspéldány melletti törlés ikont ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsokkal erőforrás-hálózati szabályokat adhat hozzá vagy távolíthat el.

> [!IMPORTANT]
> Mindenképpen [tiltsa le az alapértelmezett szabályt,](#change-the-default-network-access-rule) különben a hálózati szabályok hatás nélküliek. 

#### <a name="install-the-preview-module"></a>Az előzetes verziójú modul telepítése

Telepítse a PowerShellGet-modul legújabb verzióját. Ezután zárja be, majd nyissa meg újra a PowerShell-konzolt.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Telepítse **az Az. Storage előzetes** modult.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

A PowerShell-modulok telepítésére vonatkozó további információkért lásd: Az Azure PowerShell [modul telepítése](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Hozzáférés biztosítása

Adjon hozzá egy hálózati szabályt, amely hozzáférést biztosít egy erőforráspéldányból.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

A hálózati szabálykészlet módosításával egyszerre több erőforráspéldányt is megadhat.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Hozzáférés eltávolítása

Eltávolíthat egy hálózati szabályt, amely hozzáférést biztosít egy erőforráspéldányból.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Távolítsa el az összes olyan hálózati szabályt, amely hozzáférést biztosít az erőforráspéldányok számára.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Az engedélyezett erőforráspéldányok listájának megtekintése

Tekintse meg azon erőforráspéldányok teljes listáját, amelyek hozzáférést kaptak a tárfiókhoz.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-parancsokkal erőforrás-hálózati szabályokat adhat hozzá vagy távolíthat el.

#### <a name="install-the-preview-extension"></a>Az előzetes verziójú bővítmény telepítése

1. Nyissa meg [a Azure Cloud Shell,](../../cloud-shell/overview.md)vagy ha [](/cli/azure/install-azure-cli) helyileg telepítette az Azure CLI-t, nyisson meg egy parancskonzol-alkalmazást, például a Windows PowerShell.

2. Ezután az alábbi paranccsal ellenőrizze, hogy az Azure CLI telepített verziója vagy újabb `2.13.0` verziójú-e.

   ```azurecli
   az --version
   ```

   Ha az Azure CLI verziója alacsonyabb, mint `2.13.0` , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

3. Az előzetes verziójú bővítmény telepítéséhez írja be a következő parancsot.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Hozzáférés biztosítása

Adjon hozzá egy hálózati szabályt, amely hozzáférést biztosít egy erőforráspéldányból.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Hozzáférés eltávolítása

Eltávolíthat egy hálózati szabályt, amely hozzáférést biztosít egy erőforráspéldányból.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Az engedélyezett erőforráspéldányok listájának megtekintése

Tekintse meg azon erőforráspéldányok teljes listáját, amelyek hozzáférést kaptak a tárfiókhoz.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Hozzáférés megadása megbízható Azure-szolgáltatásokhoz 

Egyes Azure-szolgáltatások olyan hálózatokból működnek, amelyek nem szerepelnek a hálózati szabályokban. Az ilyen megbízható Azure-szolgáltatások egy részéhez hozzáférést adhat a tárfiókhoz, miközben más alkalmazásokra vonatkozó hálózati szabályokat is fenntart. Ezek a megbízható szolgáltatások erős hitelesítést fognak használni a tárfiókhoz való biztonságos csatlakozáshoz.

A megbízható Azure-szolgáltatásokhoz egy hálózatiszabály-kivétel létrehozásával adhat hozzáférést. Részletes útmutatásért tekintse meg a cikk [Kivételek](#manage-exceptions) kezelése című szakaszát.

Amikor hozzáférést ad a megbízható Azure-szolgáltatásokhoz, a következő típusú hozzáférést adja meg:

- Megbízható hozzáférés az előfizetésben regisztrált erőforrások bizonyos műveleteihez.
- Megbízható hozzáférés az erőforrásokhoz a rendszer által hozzárendelt felügyelt identitás alapján.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Megbízható hozzáférés az előfizetésben regisztrált erőforrásokhoz

Egyes szolgáltatások **erőforrásai,** ha regisztrálva vannak az  előfizetésében, hozzáférhetnek az ugyanabban az előfizetésben regisztrált tárfiókhoz bizonyos műveletekhez, például naplók írásához vagy biztonsági mentéshez.  Az alábbi táblázat ismerteti az egyes adatokat és az engedélyezett műveleteket. 

| Szolgáltatás                  | Erőforrás-szolgáltató neve     | Engedélyezett műveletek                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS virtuális gépeken futtathatja a nem törölt lemezek biztonsági mentését és visszaállítását. (felügyelt lemezek esetén nem szükséges). [További információ](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Engedélyezi az adatok Azure-ba való importálását a Data Box. [További információ](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Egyéni rendszerkép létrehozása és összetevő telepítése. [További információ](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Engedélyezze Blob Storage események közzétételét, és Event Grid a storage-üzenetsorokbe való közzétételt. Ismerje meg a [Blob Storage eseményeit](../../event-grid/overview.md#event-sources) és [az üzenetsorokba való közzétételt.](../../event-grid/event-handlers.md) |
| Azure Event Hubs         | Microsoft.EventHub         | Archiválhatja az adatokat a Event Hubs Capture-val. [További információk](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft.StorageSync      | Lehetővé teszi a helyi fájlkiszolgáló átalakítását Azure-fájlmegosztások gyorsítótáravá. Lehetővé teszi a többhelyű szinkronizálást, a gyors vészhelyreállítást és a felhőoldali biztonsági mentést. [További információ](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Az új HDInsight-fürt alapértelmezett fájlrendszerének kezdeti tartalmának kiépítése. [További információ](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import Export      | Microsoft.ImportExport     | Engedélyezi az adatok Azure Storage-ba való importálását vagy az Adatok Azure Storage-ból való exportálását az Azure Storage Import/Export szolgáltatással. [További információ](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Lehetővé teszi a monitorozási adatok biztonságos tárfiókba írását, beleértve az erőforrásnaplókat, Azure Active Directory bejelentkezési és auditnaplókat, valamint Microsoft Intune naplókat. [További információ](../../azure-monitor/roles-permissions-security.md). |
| Azure-hálózatkezelés         | Microsoft.Network          | Tárolja és elemezze a hálózati forgalmi naplókat, beleértve a Network Watcher és Traffic Analytics is. [További információ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Engedélyezze az Azure IaaS virtuális gépek vészhelyreállításának replikációját tűzfallal kompatibilis gyorsítótár, forrás vagy cél tárfiókok használata esetén.  [További információ](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Megbízható hozzáférés a rendszer által hozzárendelt felügyelt identitás alapján

Az alábbi táblázat azokat a szolgáltatásokat sorolja fel, amelyek hozzáférhetnek a tárfiók adataihoz, ha a szolgáltatások erőforráspéldányai a megfelelő engedélyt kapják. Az engedélyek megadásához explicit módon hozzá kell rendelnie egy [Azure-szerepkört](../../active-directory/managed-identities-azure-resources/overview.md) [a](storage-auth-aad.md#assign-azure-roles-for-access-rights) rendszer által hozzárendelt felügyelt identitáshoz minden erőforráspéldányhoz. Ebben az esetben a példány hozzáférési hatóköre megfelel a felügyelt identitáshoz rendelt Azure-szerepkörnek. 

> [!TIP]
> Az egyes erőforrásokhoz való hozzáférés megadásának ajánlott módja az erőforráspéldány-szabályok használata. Adott erőforráspéldányokhoz való hozzáférés megadásához tekintse meg a cikk Hozzáférés megadása [az Azure-erőforráspéldányok (előzetes verzió)](#grant-access-specific-instances) szolgáltatásból című szakaszát.


| Szolgáltatás                        | Erőforrás-szolgáltató neve                 | Cél            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Szabályzatok használatával engedélyezi az API Management szolgáltatás számára a tűzfal mögötti tárfiókok hozzáférését. [További információ](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Lehetővé Cognitive Search szolgáltatások számára, hogy hozzáférjenek a tárfiókhoz indexelés, feldolgozás és lekérdezés céljából. |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Engedélyezi Cognitive Services tárfiókok hozzáférését. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | ACR-feladatok tárfiókokat a tároló rendszerképének kiépítésekor. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Engedélyezi a tárfiókok hozzáférését az ADF-futtatáson keresztül. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Lehetővé teszi a tárfiókok hozzáférését Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Hozzáférést biztosít a tárfiókok számára a DevTest Labs használatával. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Lehetővé teszi az IoT Hubról származó adatok blobtárolóba írását. [További információ](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Engedélyezi a logikai alkalmazások számára a tárfiókok hozzáférését. [További információ](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning szolgáltatás | Microsoft.MachineLearningServices      | Az Azure Machine Learning munkaterületek a kísérlet kimenetét, modelljeit és naplóit a Blob Storage-be írják, és beolvassák az adatokat. [További információ](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft.Media/mediaservices          | Tárfiókok hozzáférésének lehetővé Media Services. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Tárfiókok hozzáférésének lehetővé Azure Migrate. |
| Azure Purview                  | Microsoft.Purview/accounts             | A Tárfiókok elérésének lehetővé teszi a Purview-t. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Hozzáférést biztosít a tárfiókok számára Remote Rendering. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Tárfiókok hozzáférésének lehetővé Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Lehetővé [teszi a naplózási](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) adatok tűzfal mögötti tárfiókba való írását. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Engedélyezi az adatok importálását és exportálását adott SQL-adatbázisokból a COPY utasítás vagy a PolyBase használatával (dedikált készletben), vagy a kiszolgáló nélküli készletben lévő függvény és külső `openrowset` táblák használatával. [További információ](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | Lehetővé teszi a streamelési feladatból származó adatok blobtárolóba írását. [További információ](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Hozzáférést biztosít az Azure Storage-ban az adatokhoz Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Hozzáférés megadása a Storage Analyticshez

Bizonyos esetekben az erőforrásnaplók és metrikák olvasásához a hálózathatáron kívülről is hozzá kell férni. Amikor megbízható szolgáltatások tárfiókhoz való hozzáférését konfigurálja, engedélyezheti a naplófájlokhoz, a metrikatáblákhoz való olvasási hozzáférést, vagy akár mindkettőt egy hálózatiszabály-kivétel létrehozásával. Részletes útmutatásért lásd az alábbi **Kivételek kezelése** szakaszt. A Storage Analytics használatával kapcsolatos további információkért lásd: Az Azure Storage Analytics használata [naplók és metrikaadatok gyűjtésére.](./storage-analytics.md) 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Kivételek kezelése

A hálózati szabályok kivételei a Azure Portal, a PowerShell vagy az Azure CLI v2 használatával kezelhetők.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a megvédeni kívánt tárfiókra.

2. A Hálózat menüben válassza a **lehetőséget.**

3. Ellenőrizze, hogy a Kijelölt hálózatokról való hozzáférés engedélyezése lehetőséget **választotta-e.**

4. A **Kivételek alatt** válassza ki a megszabadni kívánt kivételeket.

5. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Telepítse a [Azure PowerShell,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

2. Megjeleníti a tárfiók hálózati szabályaira vonatkozó kivételeket.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Konfigurálja a kivételeket a tárfiók hálózati szabályaihoz.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Távolítsa el a tárfiók hálózati szabályai alóli kivételeket.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Győződjön meg [arról,](#change-the-default-network-access-rule) hogy az alapértelmezett szabályt megtagadja, vagy a kivételek eltávolítása nincs hatással.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

2. Megjeleníti a tárfiók hálózati szabályaira vonatkozó kivételeket.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Konfigurálja a kivételeket a tárfiók hálózati szabályaihoz.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Távolítsa el a tárfiók hálózati szabályai alóli kivételeket.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Győződjön meg arról, [hogy az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadja, vagy a kivételek eltávolítása nincs hatással. 

---

## <a name="next-steps"></a>Következő lépések

További információ az Azure hálózati szolgáltatásvégpontjairól: [Szolgáltatásvégpont.](../../virtual-network/virtual-network-service-endpoints-overview.md)

Az Azure Storage biztonsági útmutatója az [Azure Storage biztonságával kapcsolatos mélyebbre ás.](../blobs/security-recommendations.md)