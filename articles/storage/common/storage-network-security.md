---
title: Azure Storage-tűzfalak és virtuális hálózatok konfigurálása | Microsoft Docs
description: A Storage-fiók rétegzett hálózati biztonságának konfigurálása.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: d45b792c655820b771ba956721e9169750c39fbd
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475413"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-tűzfalak és virtuális hálózatok konfigurálása

Az Azure Storage rétegzett biztonsági modellel rendelkezik. Ez a modell lehetővé teszi az alkalmazások és a vállalati környezetek által igénybe vett Storage-fiókok elérési szintjének védelmét és szabályozását a felhasznált hálózatok típusa és részhalmaza alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá egy Storage-fiókhoz. A Storage-fiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokról vagy egy Azure-Virtual Network (VNet) lévő alhálózatok listájáról.

A Storage-fiókokhoz nyilvános végpont tartozik, amely az interneten keresztül érhető el. Létrehozhat [privát végpontokat is a Storage-fiókjához](storage-private-endpoints.md), amely egy magánhálózati IP-címet rendel a VNet a Storage-fiókhoz, és a VNet és a Storage-fiók közötti összes forgalmat privát kapcsolaton keresztül biztosítja. Az Azure Storage tűzfal hozzáférés-vezérlési hozzáférést biztosít a Storage-fiók nyilvános végpontjának. A tűzfalat használhatja a nyilvános végponton keresztüli összes hozzáférés blokkolására is, ha privát végpontokat használ. A tárolási tűzfal konfigurációja azt is lehetővé teszi, hogy a megbízható Azure platform szolgáltatásai biztonságosan férhessenek hozzá a Storage-fiókhoz.

Egy olyan alkalmazás, amely hozzáfér egy Storage-fiókhoz, ha a hálózati szabályok érvényben maradnak, a kérelemhez megfelelő engedély szükséges. Az engedélyezést Azure Active Directory (Azure AD) hitelesítő adatok támogatják a blobok és várólisták esetében, érvényes fiók-hozzáférési kulccsal vagy SAS-jogkivonattal.

> [!IMPORTANT]
> Ha bekapcsolja a tűzfalszabályok bekapcsolását a Storage-fiókhoz, az alapértelmezés szerint letiltja a bejövő adatkéréseket, kivéve, ha a kérelmek Azure-Virtual Network (VNet) vagy engedélyezett nyilvános IP-címeken belüli szolgáltatásból származnak. A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább.
>
> Hozzáférést biztosíthat az olyan Azure-szolgáltatásokhoz, amelyek egy VNet belül működnek, és lehetővé teszik a szolgáltatás-példányt üzemeltető alhálózatról érkező forgalom elérését. Az alábbiakban ismertetett [kivételek](#exceptions) használatával korlátozott számú forgatókönyvet is engedélyezhet. Ha a Azure Portal keresztül szeretne hozzáférni a Storage-fiókból, a beállított megbízható határon (IP-vagy VNet) belüli gépen kell lennie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A Storage-fiók biztonságossá tételéhez először konfigurálnia kell egy olyan szabályt, amely megtagadja a hozzáférést az összes hálózatról (beleértve az internetes forgalmat) a nyilvános végponton, alapértelmezés szerint. Ezután olyan szabályokat kell konfigurálnia, amelyek hozzáférést biztosítanak az adott virtuális hálózatok érkező forgalomhoz. Olyan szabályokat is beállíthat, amelyek hozzáférést biztosítanak a forgalomhoz a nyilvános internetes IP-címtartományok kiválasztásával, valamint az adott internetes vagy helyszíni ügyfelek kapcsolatainak engedélyezésével. Ez a konfiguráció lehetővé teszi az alkalmazások biztonságos hálózati határának kiépítését.

Az adott virtuális hálózatokról és a nyilvános IP-címtartományok elérését engedélyező tűzfalszabályok ugyanazon a Storage-fiókon kombinálhatók. A tárolási tűzfalszabályok alkalmazhatók a meglévő Storage-fiókokra, illetve új Storage-fiókok létrehozásakor is.

A tárolási tűzfalszabályok a Storage-fiók nyilvános végpontján érvényesek. Nincs szükség tűzfal-hozzáférési szabályokra a Storage-fiókok privát végpontjai forgalmának engedélyezéséhez. A privát végpontok létrehozásának jóváhagyásának folyamata implicit hozzáférést biztosít a privát végpontot üzemeltető alhálózatról érkező forgalomhoz.

A hálózati szabályok az összes hálózati protokollon érvényesek az Azure Storage-ba, beleértve a REST és az SMB protokollt is. Ha olyan eszközökkel szeretné elérni az adatelérést, mint a Azure Portal, a Storage Explorer és a AZCopy, akkor explicit hálózati szabályokat kell konfigurálni.

A hálózati szabályok alkalmazása után minden kérelem esetében érvénybe lépnek. A megadott IP-címhez hozzáférést biztosító SAS-jogkivonatok korlátozzák a jogkivonat-tulajdonos hozzáférését, de nem biztosítanak új hozzáférést a konfigurált hálózati szabályokon túl.

A hálózati szabályok nem érintik a virtuális gép lemezes forgalmát (beleértve a csatlakoztatási és leválasztási műveleteket, valamint a lemez i/o). Az oldal Blobok REST-hozzáférését hálózati szabályok védik.

A klasszikus Storage-fiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

Nem felügyelt lemezeket használhat a Storage-fiókokban olyan hálózati szabályokkal, amelyek a virtuális gépek biztonsági mentésére és visszaállítására vonatkoznak a kivételek létrehozásával. Ezt a folyamatot a jelen cikk [kivételek](#exceptions) című szakaszában dokumentáljuk. A tűzfal kivételei nem alkalmazhatók a felügyelt lemezekre, mivel azokat már az Azure felügyeli.

## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező kapcsolatokat elfogadnak. A kijelölt hálózatok hozzáférésének korlátozásához elsőként módosítania kell az alapértelmezett műveletet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet az alkalmazás Azure Storage-hoz való kapcsolódására. Ha az alapértelmezett hálózati szabályt állítja **be, az letiltja** az összes hozzáférését az összes adathoz **, kivéve** , ha a hozzáférést biztosító meghatározott hálózati szabályok is érvényesek. Ügyeljen arra, hogy az alapértelmezett szabály hozzáférés-megtagadásra való módosítása előtt engedélyezze a hálózati szabályokat alkalmazó engedélyezett hálózatokhoz való hozzáférést.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

A Storage-fiókok alapértelmezett hálózati hozzáférési szabályait a Azure Portal, a PowerShell vagy a CLIv2 segítségével kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Ha alapértelmezés szerint szeretné megtagadni a hozzáférést, válassza a **kijelölt hálózatokból**való hozzáférés engedélyezése lehetőséget. Ha minden hálózatról engedélyezni szeretné a forgalmat, engedélyezze a hozzáférést az **Összes hálózatnak**.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. Megjeleníti a Storage-fiók alapértelmezett szabályának állapotát.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Alapértelmezés szerint a hálózati hozzáférés megtagadásához állítsa be az alapértelmezett szabályt.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Állítsa be az alapértelmezett szabályt úgy, hogy a hálózati hozzáférés alapértelmezés szerint engedélyezve legyen.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

1. Megjeleníti a Storage-fiók alapértelmezett szabályának állapotát.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Alapértelmezés szerint a hálózati hozzáférés megtagadásához állítsa be az alapértelmezett szabályt.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Állítsa be az alapértelmezett szabályt úgy, hogy a hálózati hozzáférés alapértelmezés szerint engedélyezve legyen.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Hozzáférés biztosítása egy virtuális hálózattól

A Storage-fiókokat úgy is konfigurálhatja, hogy csak bizonyos alhálózatokról engedélyezze a hozzáférést. Az engedélyezett alhálózatok ugyanahhoz az előfizetéshez vagy egy másik előfizetéshez tartozó VNet tartozhatnak, beleértve a más Azure Active Directory bérlőhöz tartozó előfizetéseket is.

Engedélyezzen egy [szolgáltatási végpontot](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage-hoz a VNet belül. A szolgáltatási végpont az Azure Storage szolgáltatás optimális elérési útján irányítja át a forgalmat a VNet. Az alhálózat és a virtuális hálózat identitásait is továbbítjuk az egyes kérésekhez. A rendszergazdák ezután konfigurálhatják a Storage-fiók hálózati szabályait, amelyek lehetővé teszik a kérelmek fogadását egy adott alhálózatról egy VNet. Azok az ügyfelek, akik ezen hálózati szabályokon keresztül kaptak hozzáférést, továbbra is meg kell felelniük a Storage-fiók engedélyezési követelményeinek az adat eléréséhez.

Az egyes Storage-fiókok akár 200 virtuális hálózati szabályt is támogatnak, amelyek az [IP-hálózati szabályokkal](#grant-access-from-an-internet-ip-range)kombinálhatók.

### <a name="available-virtual-network-regions"></a>Elérhető virtuális hálózati régiók

Általánosságban elmondható, hogy a szolgáltatási végpontok az ugyanazon az Azure-régióban lévő virtuális hálózatok és szolgáltatási példányok között működnek. A szolgáltatási végpontok Azure Storage-ban való használatakor ez a hatókör nő a [párosított régió](/azure/best-practices-availability-paired-regions)belefoglalásával. A szolgáltatási végpontok lehetővé teszik a folytonosságot a regionális feladatátvétel során, és hozzáférnek a csak olvasható, Geo-redundáns tárolási (RA-GRS) példányokhoz. Azok a hálózati szabályok, amelyek hozzáférést biztosítanak egy virtuális hálózatról a Storage-fiókhoz, hozzáférést biztosítanak az RA-GRS-példányokhoz is.

A regionális leállás során felmerülő vész-helyreállítás tervezése során előre létre kell hoznia a virtuális hálózatok a párosított régióban. Engedélyezze a szolgáltatás-végpontokat az Azure Storage-hoz, és az ezekhez az alternatív virtuális hálózatokhoz hozzáférést biztosító hálózati szabályokkal. Ezután alkalmazza ezeket a szabályokat a Geo-redundáns Storage-fiókokra.

> [!NOTE]
> A szolgáltatási végpontok nem vonatkoznak a virtuális hálózat és a kijelölt régió pár régióján kívüli forgalomra. Csak olyan hálózati szabályokat alkalmazhat, amelyek hozzáférést biztosítanak a virtuális hálózatokról a Storage-fiókok elsődleges régiójába vagy a kijelölt párosított régióba.

### <a name="required-permissions"></a>Szükséges engedélyek

Ha egy virtuális hálózati szabályt egy Storage-fiókra szeretne alkalmazni, a felhasználónak rendelkeznie kell a megfelelő engedélyekkel az alhálózatok hozzáadásához. A szükséges engedély *egy alhálózathoz való csatlakoztatási szolgáltatás* , amely a *Storage-fiók közreműködői* beépített szerepkör részét képezi. Az egyéni szerepkör-definíciók is hozzáadhatók.

A Storage-fiók és a hozzáférést biztosító virtuális hálózatok különböző előfizetésekben lehetnek, beleértve az olyan előfizetéseket, amelyek egy másik Azure AD-bérlő részét képezik.

> [!NOTE]
> A virtuális hálózatok olyan alhálózatokhoz való hozzáférését biztosító szabályok konfigurálása, amelyek egy másik Azure Active Directory bérlő részét képezik, jelenleg csak a PowerShell, a CLI és a REST API-k támogatják. Ezek a szabályok nem konfigurálhatók a Azure Portalon keresztül, de a portálon is megtekinthetők.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése

A Storage-fiókok virtuális hálózati szabályai a Azure Portal, a PowerShell vagy a CLIv2 használatával kezelhetők.

#### <a name="azure-portal"></a>Azure Portal

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. Ha új hálózati szabállyal szeretne hozzáférést biztosítani egy virtuális hálózathoz, a **virtuális hálózatok**területen kattintson a **meglévő virtuális hálózat hozzáadása**lehetőségre, válassza a **virtuális hálózatok** és **alhálózatok** lehetőséget, majd kattintson a **Hozzáadás**gombra. Új virtuális hálózat létrehozásához és az IT-hozzáférés biztosításához kattintson az **új virtuális hálózat hozzáadása**lehetőségre. Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd kattintson a **Létrehozás**gombra.

    > [!NOTE]
    > Ha az Azure Storage szolgáltatási végpontja korábban nem lett konfigurálva a kiválasztott virtuális hálózathoz és alhálózatokhoz, akkor a művelet részeként konfigurálhatja.
    >
    > Jelenleg csak az ugyanahhoz a Azure Active Directory bérlőhöz tartozó virtuális hálózatok jelennek meg a szabályok létrehozásakor. Egy másik bérlőhöz tartozó virtuális hálózatban lévő alhálózathoz való hozzáférés biztosításához használja a PowerShell, a CLI vagy a REST API-kat.

1. Virtuális hálózat vagy alhálózat szabályának eltávolításához kattintson a **...** elemre a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd kattintson az **Eltávolítás**gombra.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. Virtuális hálózati szabályok listázása.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. A szolgáltatás végpontjának engedélyezése az Azure Storage-hoz egy meglévő virtuális hálózaton és alhálózaton.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat hálózati szabályának hozzáadásához használjon egy teljesen minősített **VirtualNetworkResourceId** paramétert "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name" formátumban.

1. Hálózati szabály eltávolítása egy virtuális hálózat és alhálózat számára.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

1. Virtuális hálózati szabályok listázása.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. A szolgáltatás végpontjának engedélyezése az Azure Storage-hoz egy meglévő virtuális hálózaton és alhálózaton.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat szabályának hozzáadásához használjon egy teljesen minősített alhálózati azonosítót a "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> " formában.
    >
    > Az **előfizetés** paraméter használatával lekérheti az alhálózati azonosítót egy másik Azure ad-bérlőhöz tartozó VNet.

1. Hálózati szabály eltávolítása egy virtuális hálózat és alhálózat számára.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférés biztosítása internetes IP-címtartományról

A Storage-fiókok konfigurálásával engedélyezheti a hozzáférést a megadott nyilvános internetes IP-címtartományok eléréséhez. Ez a konfiguráció hozzáférést biztosít bizonyos internetalapú szolgáltatásokhoz és helyszíni hálózatokhoz, és blokkolja az általános internetes forgalmat.

Adja meg az engedélyezett IP-címtartományok [CIDR jelöléssel](https://tools.ietf.org/html/rfc4632) a *16.17.18.0/24* formátumban, illetve olyan egyedi IP-címeket, mint a *16.17.18.19*.

   > [!NOTE]
   > A "/31" vagy a "/32" előtaggal rendelkező kisméretű címtartományok nem támogatottak. Ezeket a tartományokat egyedi IP-cím szabályokkal kell konfigurálni.

Az IP-hálózati szabályok csak a **nyilvános internetes** IP-címek esetében engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózati hálózatok közé tartoznak a következők: _10. *_, _172,16. *_  -  _172,31. *_ és _192,168. *_.

   > [!NOTE]
   > Az IP-hálózati szabályok nem befolyásolják a Storage-fiókkal azonos Azure-régióból származó kérelmeket. A [virtuális hálózati szabályok](#grant-access-from-a-virtual-network) használatával engedélyezze az azonos régiókra vonatkozó kérelmeket.

  > [!NOTE]
  > A Storage-fiókkal azonos régióban üzembe helyezett szolgáltatások saját Azure IP-címeket használnak a kommunikációhoz. Így az adott Azure-szolgáltatásokhoz való hozzáférés nem korlátozható a nyilvános kimenő IP-címtartomány alapján.

A tárolási tűzfalszabályok konfigurálásához csak IPV4-címek támogatottak.

Az egyes Storage-fiókok legfeljebb 100 IP-hálózati szabályt támogatnak.

### <a name="configuring-access-from-on-premises-networks"></a>A helyszíni hálózatokhoz való hozzáférés konfigurálása

Ahhoz, hogy hozzáférést biztosítson a helyszíni hálózatokról a Storage-fiókhoz egy IP-hálózati szabállyal, meg kell határoznia a hálózat által használt internet felé irányuló IP-címeket. Segítségért forduljon a hálózati rendszergazdához.

Ha a [ExpressRoute](/azure/expressroute/expressroute-introduction) -t használja a telephelyéről, a nyilvános és a Microsoft-partnerek számára, meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. A Microsoft-társak esetében a használt NAT IP-címek vagy a szolgáltató által biztosított vagy biztosított ügyfelek. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

A Storage-fiókok IP-hálózati szabályait a Azure Portal, a PowerShell vagy a CLIv2 segítségével kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. Ha hozzáférést szeretne biztosítani egy internetes IP-tartományhoz, adja meg az IP-címet vagy címtartományt (CIDR formátumban) a **tűzfal**  >  **címtartomány alatt**.

1. Az IP-hálózati szabály eltávolításához kattintson a címtartomány melletti Kuka ikonra.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. IP-hálózati szabályok listázása.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adjon hozzá egy hálózati szabályt egy egyedi IP-címhez.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adja hozzá az IP-címtartomány hálózati szabályát.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Hálózati szabály eltávolítása egy adott IP-címhez.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

1. IP-hálózati szabályok listázása.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adjon hozzá egy hálózati szabályt egy egyedi IP-címhez.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adja hozzá az IP-címtartomány hálózati szabályát.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Hálózati szabály eltávolítása egy adott IP-címhez.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="exceptions"></a>Kivételek

A hálózati szabályok segítenek a biztonságos környezet létrehozásában az alkalmazások és az adatok közötti kapcsolatokhoz a legtöbb esetben. Néhány alkalmazás azonban olyan Azure-szolgáltatástól függ, amely nem különíthető el egyedien a virtuális hálózat vagy az IP-cím szabályai alapján. Az alkalmazások teljes funkcionalitásának lehetővé tételéhez azonban az ilyen szolgáltatásokat biztosítani kell a tároláshoz. Ilyen helyzetekben használhatja a ***megbízható Microsoft-szolgáltatások engedélyezése...*** beállítást annak engedélyezéséhez, hogy az ilyen szolgáltatások hozzáférjenek az adataihoz, a naplókhoz vagy az elemzésekhez.

### <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások

Bizonyos Microsoft-szolgáltatások olyan hálózatokból működnek, amelyek nem vehetők fel a hálózati szabályokba. Az ilyen megbízható Microsoft-szolgáltatások egy részhalmazát megadhatja a Storage-fiókhoz, miközben más alkalmazások hálózati szabályait is megtarthatja. Ezek a megbízható szolgáltatások a biztonságos hitelesítés használatával biztonságosan csatlakozhatnak a Storage-fiókhoz. Engedélyezte a Microsoft-szolgáltatásokhoz való megbízható hozzáférés két üzemmódját.

- Egyes szolgáltatások erőforrásai, **Amikor regisztrálva vannak az előfizetésben**, a kiválasztható műveletekhez, például a naplók vagy a biztonsági mentés írásához is hozzáférhetnek a Storage **-** fiókhoz.
- Egyes szolgáltatások erőforrásai explicit módon férhetnek hozzá a Storage-fiókhoz, ha **hozzárendel egy RBAC-szerepkört** a rendszerhez rendelt felügyelt identitáshoz.


Ha engedélyezi a **megbízható Microsoft-szolgáltatások engedélyezése...** beállítást, az alábbi, a Storage-fiókkal megegyező előfizetésben regisztrált szolgáltatások erőforrásai korlátozott számú művelethez kapnak hozzáférést a leírtak szerint:

| Szolgáltatás                  | Erőforrás-szolgáltató neve     | Engedélyezett műveletek                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. Recoveryservices szolgáltatónál | Biztonsági másolatok futtatása és a nem felügyelt lemezek visszaállítása a IAAS virtuális gépeken. (felügyelt lemezekhez nem szükséges). [További információ](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft. DataBox          | Lehetővé teszi az Azure-ba történő adatimportálást Data Box használatával. [További információ](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft. segédösszetevője       | Egyéni rendszerkép létrehozása és az összetevők telepítése. [További információ](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Engedélyezze Blob Storage az események közzétételét, és engedélyezze a Event Grid közzétételét a tárolási várólistákon. Tudnivalók a [blob Storage-eseményekről](/azure/event-grid/event-sources) és [a várólistákon való közzétételről](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft. EventHub         | Adatok archiválása Event Hubs rögzítéssel. [További információk](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft. StorageSync      | Lehetővé teszi a helyszíni fájlkiszolgáló átalakítását az Azure-fájlmegosztás gyorsítótárába. Lehetővé teszi a többhelyes szinkronizálást, a gyors katasztrófa-helyreállítást és a Felhőbeli biztonsági mentést. [További információ](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Az alapértelmezett fájlrendszer kezdeti tartalmának kiépítése egy új HDInsight-fürthöz. [További információ](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure importálási exportálás      | Microsoft. ImportExport     | Lehetővé teszi az adatok importálását az Azure-ba és az Azure-ból származó adatok exportálását az importálási/exportálási szolgáltatás segítségével. [További információ](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft. bepillantások         | Lehetővé teszi, hogy a figyelési adatai biztonságos Storage-fiókba legyenek írva, beleértve az erőforrás-naplókat, Azure Active Directory a bejelentkezést és a naplókat, valamint Microsoft Intune naplókat. [További információ](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-hálózatkezelés         | Microsoft.Network          | A hálózati forgalmi naplók tárolása és elemzése, beleértve a Network Watcher és Traffic Analytics szolgáltatásokat. [További információ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Engedélyezze a replikációt az Azure IaaS-alapú virtuális gépek vész-helyreállításához, ha tűzfalon alapuló gyorsítótár-, forrás-vagy tároló-fiókot használ.  [További információ](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A **megbízható Microsoft-szolgáltatások engedélyezése...** beállítás azt is lehetővé teszi, hogy az alábbi szolgáltatások egy adott példánya hozzáférhessen a Storage-fiókhoz, ha explicit módon HOZZÁRENDEL [egy RBAC-szerepkört](storage-auth-aad.md#assign-rbac-roles-for-access-rights) az adott erőforrás-példányhoz tartozó [rendszerhez rendelt felügyelt identitáshoz](../../active-directory/managed-identities-azure-resources/overview.md) . Ebben az esetben a példányhoz való hozzáférés hatóköre megfelel a felügyelt identitáshoz rendelt RBAC szerepkörnek.

| Szolgáltatás                        | Erőforrás-szolgáltató neve                 | Cél            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Lehetővé teszi az API Management szolgáltatás számára a tűzfal mögötti tárolási fiókok elérését a házirendek használatával. [További információ](/azure/api-management/api-management-authentication-policies#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. Search/searchServices        | Lehetővé teszi Cognitive Search szolgáltatások számára a Storage-fiókok elérését indexelésre, feldolgozásra és lekérdezésre. |
| Azure Container Registry Tasks | Microsoft. ContainerRegistry/nyilvántartók | Az ACR-feladatok tároló-lemezképek létrehozásakor férhetnek hozzá a Storage-fiókokhoz. |
| Azure Data Factory             | Microsoft. DataFactory/gyárak        | Lehetővé teszi a Storage-fiókok elérését az ADF futtatókörnyezeten keresztül. |
| Azure Data Share               | Microsoft. DataShare/fiókok           | Lehetővé teszi a Storage-fiókok elérését az adatmegosztáson keresztül. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Engedélyezi az IoT hub adatainak blob Storage-ba való írását. [További információ](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/munkafolyamatok              | Lehetővé teszi a Logic apps számára a Storage-fiókok elérését. [További információ](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning szolgáltatás | Microsoft.MachineLearningServices      | Engedélyezett Azure Machine Learning munkaterületek a kísérlet kimenetét, modelljeit és naplóit írják a blob Storage-ba, és beolvasják az adatokat. [További információ](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Lehetővé teszi az adatok importálását és exportálását egy adott SQL Database példányból a Base használatával. [További információ](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure SQL Database       | Microsoft.Sql                          | Lehetővé teszi a Storage-fiókok adatainak [importálását](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage) és a naplózási adatok [írását](https://docs.microsoft.com/azure/azure-sql/database/audit-write-storage-account-behind-vnet-firewall) a tűzfal mögötti tárolási fiókokba. |
| Azure Stream Analytics         | Microsoft. StreamAnalytics             | Lehetővé teszi a folyamatos átviteli feladatok adatainak blob Storage-ba való írását. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. [További információ](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. szinapszis/munkaterületek          | Lehetővé teszi az Azure Storage-beli adatokhoz való hozzáférést a szinapszis Analyticsből. |


### <a name="storage-analytics-data-access"></a>Storage Analytics-adathozzáférés

Bizonyos esetekben a hálózat határain kívülről kell hozzáférni az olvasási erőforrás-naplókhoz és a metrikához. A megbízható szolgáltatások a Storage-fiókhoz való hozzáférésének konfigurálásakor engedélyezheti az olvasási hozzáférést a naplófájlokhoz, a metrikák tábláihoz vagy mindkettőhöz. [További információ a Storage Analytics használatáról.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése

A hálózati szabályok kivételeit a Azure Portal, a PowerShell vagy az Azure CLI V2 használatával kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. A **kivételek**területen válassza ki a megadni kívánt kivételeket.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. A Storage-fiók hálózati szabályai alóli kivételek megjelenítése.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurálja a kivételeket a Storage-fiók hálózati szabályaira.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Távolítsa el a kivételeket a Storage-fiók hálózati szabályaihoz.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a kivételek eltávolítása ne legyen hatással.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

1. A Storage-fiók hálózati szabályai alóli kivételek megjelenítése.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurálja a kivételeket a Storage-fiók hálózati szabályaira.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Távolítsa el a kivételeket a Storage-fiók hálózati szabályaihoz.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a kivételek eltávolítása ne legyen hatással.

## <a name="next-steps"></a>További lépések

További információ az Azure Network Service-végpontokról a [szolgáltatási végpontokon](/azure/virtual-network/virtual-network-service-endpoints-overview).

Mélyebb betekintést nyújt az Azure Storage biztonságára az [Azure Storage biztonsági útmutatójában](../blobs/security-recommendations.md).
