---
title: Fordított DNS az Azure-szolgáltatásokhoz – Azure DNS
description: Ezzel a képzési útvonallal megkezdheti a fordított DNS-keresések konfigurálását az Azure-ban üzemeltetett szolgáltatásokhoz.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: c72e17e601ebf87b0f344a4723159ae22abc81d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919867"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan konfigurálhatja a fordított DNS-kereséseket az Azure-ban üzemeltetett szolgáltatásokhoz.

Az Azure-szolgáltatások az Azure által hozzárendelt és a Microsoft tulajdonában lévő IP-címeket használják. Ezeket a fordított DNS-rekordokat (PTR-rekordokat) a megfelelő Microsoft tulajdonú fordított DNS-keresési zónákban kell létrehozni. Ez a cikk azt ismerteti, hogyan teheti ezt meg.

Ez a forgatókönyv nem tévesztendő össze azzal a képességgel, hogy [a fordított DNS-keresési zónákat a Azure DNS-beli hozzárendelt IP-címtartományok számára is üzemelteti](dns-reverse-dns-hosting.md). Ebben az esetben a névkeresési zóna által reprezentált IP-tartományokat hozzá kell rendelni a szervezethez, jellemzően az INTERNETSZOLGÁLTATÓnál.

A cikk elolvasása előtt Ismerkedjen meg a [fordított DNS és az Azure-támogatás áttekintésével](dns-reverse-dns-overview.md).

Azure DNS a számítási erőforrások (például a virtuális gépek, a virtuálisgép-méretezési csoportok vagy a Service Fabric fürtök) PublicIpAddress-erőforráson keresztül érhetők el. A fordított DNS-keresések a PublicIpAddress "ReverseFqdn" tulajdonságának használatával konfigurálhatók.


A fordított DNS jelenleg nem támogatott a Azure App Service és a Application Gateway.

## <a name="validation-of-reverse-dns-records"></a>Fordított DNS-rekordok ellenőrzése

Egy harmadik fél nem hozhat létre fordított DNS-rekordokat az Azure-szolgáltatáshoz való hozzárendeléshez a DNS-tartományokhoz. Ennek megelőzése érdekében az Azure csak olyan fordított DNS-rekord létrehozását engedélyezi, amelyben a fordított DNS-rekordban megadott tartománynév megegyezik a-vel, vagy feloldja a (z) egy PublicIpAddress vagy felhőalapú szolgáltatás DNS-nevét vagy IP-címét ugyanabban az Azure-előfizetésben.

Ezt az ellenőrzést csak akkor hajtja végre a rendszer, ha a fordított DNS-rekord be van állítva vagy módosul. A rendszer nem végez rendszeres újraellenőrzést.

Például: tegyük fel, hogy a PublicIpAddress erőforrás a DNS-név contosoapp1.northus.cloudapp.azure.com és IP-23.96.52.53 rendelkezik. A PublicIpAddress ReverseFqdn megadható a következőként:
* A PublicIpAddress DNS-neve, contosoapp1.northus.cloudapp.azure.com
* Egy másik PublicIpAddress DNS-neve ugyanabban az előfizetésben, például contosoapp2.westus.cloudapp.azure.com
* Egy hiúság DNS-neve, például app1.contoso.com, feltéve, hogy a név *először* CNAME-ként van konfigurálva a contosoapp1.NORTHUS.cloudapp.Azure.com, vagy egy másik PublicIpAddress ugyanahhoz az előfizetéshez.
* Egy hiúság DNS-neve, például app1.contoso.com, feltéve, hogy ezt a nevet *először* az IP-23.96.52.53, vagy egy másik PublicIpAddress IP-címére konfigurálja, ugyanabban az előfizetésben.

Ugyanezek a korlátozások érvényesek a Cloud Services fordított DNS-re.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Fordított DNS PublicIpAddress-erőforrásokhoz

Ez a szakasz részletesen ismerteti, hogyan konfigurálhatja a fordított DNS-t a Resource Manager-alapú üzemi modellben lévő PublicIpAddress-erőforrásokhoz a Azure PowerShell, a klasszikus Azure CLI vagy az Azure CLI használatával. A fordított DNS konfigurálása a PublicIpAddress-erőforrásokhoz jelenleg nem támogatott a Azure Portalon keresztül.

Az Azure jelenleg csak az IPv4 PublicIpAddress-erőforrások esetében támogatja a fordított DNS-t. IPv6 esetén nem támogatott.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Fordított DNS hozzáadása meglévő nyilvános IP

#### <a name="powershell"></a>PowerShell

Fordított DNS frissítése meglévő PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Ha olyan meglévő PublicIpAddress szeretné felvenni a fordított DNS-t, amely még nem rendelkezik DNS-névvel, akkor a DNS-nevet is meg kell adnia:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

Fordított DNS hozzáadása meglévő PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Ha olyan meglévő PublicIpAddress szeretné felvenni a fordított DNS-t, amely még nem rendelkezik DNS-névvel, akkor a DNS-nevet is meg kell adnia:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Fordított DNS hozzáadása meglévő PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Ha olyan meglévő PublicIpAddress szeretné felvenni a fordított DNS-t, amely még nem rendelkezik DNS-névvel, akkor a DNS-nevet is meg kell adnia:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Nyilvános IP-cím létrehozása fordított DNS-sel

Már megadott fordított DNS-tulajdonsággal rendelkező új PublicIpAddress létrehozása:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Fordított DNS megtekintése meglévő PublicIpAddress

Meglévő PublicIpAddress konfigurált értékének megtekintése:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Fordított DNS eltávolítása meglévő Nyilvános IP-címek

Fordított DNS-tulajdonság eltávolítása meglévő PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Fordított DNS konfigurálása Cloud Serviceshoz

Ez a szakasz részletesen ismerteti, hogyan konfigurálhatja a fordított DNS-t a Cloud Serviceshoz a klasszikus üzemi modellben Azure PowerShell használatával. A fordított DNS konfigurálása Cloud Services esetén nem támogatott a Azure Portal, az Azure klasszikus CLI vagy az Azure CLI használatával.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Fordított DNS hozzáadása meglévő Cloud Serviceshoz

Fordított DNS-rekord hozzáadása meglévő felhőalapú szolgáltatáshoz:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Felhőalapú szolgáltatás létrehozása fordított DNS-sel

Új felhőalapú szolgáltatás létrehozása a fordított DNS-tulajdonsággal már meg van adva:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Fordított DNS megtekintése meglévő Cloud Services

Egy meglévő felhőalapú szolgáltatás fordított DNS-tulajdonságának megtekintése:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Fordított DNS eltávolítása meglévő Cloud Services

Fordított DNS-tulajdonság eltávolítása meglévő felhőalapú szolgáltatásból:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>GYIK

### <a name="how-much-do-reverse-dns-records-cost"></a>Mennyibe kerül a fordított DNS-rekordok díja?

Ingyenesek!  A fordított DNS-rekordok és-lekérdezések esetében nincs további díj.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>A fordított DNS-rekordok feloldása az internetről történik?

Igen. Miután beállította az Azure-szolgáltatás fordított DNS tulajdonságát, az Azure felügyeli az összes olyan DNS-delegálást és DNS-zónát, amely szükséges ahhoz, hogy a fordított DNS-rekord feloldja az összes Internet-felhasználót.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Az Azure-szolgáltatásokhoz létrehozott alapértelmezett fordított DNS-rekordok?

Nem. A fordított DNS egy opt-in funkció. Nem jön létre alapértelmezett fordított DNS-rekord, ha úgy dönt, hogy nem konfigurálja őket.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Mi a teljes tartománynév (FQDN) formátuma?

A teljes tartománynevek továbbítási sorrendben vannak megadva, és egy ponttal kell leállítani (például "app1.contoso.com").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Mi történik, ha a megadott fordított DNS-re vonatkozó ellenőrzési ellenőrzés meghiúsul?

Ha a fordított DNS-ellenőrzés sikertelen, a fordított DNS-rekord konfigurálásának művelete meghiúsul. Szükség szerint javítsa ki a fordított DNS-értéket, és próbálkozzon újra.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Beállíthat fordított DNS-t a Azure App Servicehoz?

Nem. A fordított DNS nem támogatott a Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Több fordított DNS-rekord is konfigurálható az Azure-szolgáltatáshoz?

Nem. Az Azure egyetlen fordított DNS-rekordot támogat minden Azure Cloud Service-vagy PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Beállíthat fordított DNS-t az IPv6-PublicIpAddress erőforrásaihoz?

Nem. Az Azure jelenleg csak az IPv4 PublicIpAddress-erőforrások és a Cloud Services esetében támogatja a fordított DNS-t.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Küldhetek e-maileket külső tartományokra az Azure számítási szolgáltatásaiból?

Az e-mailek közvetlenül az Azure-telepítésből való elküldésének technikai képessége az előfizetés típusától függ. Az előfizetés típusától függetlenül a Microsoft azt javasolja, hogy a megbízható levelek továbbítása szolgáltatás használatával küldjön kimenő leveleket. További részletekért lásd: [bővített Azure-biztonság az e-mailek küldéséhez – November 2017 frissítés](../virtual-network/troubleshoot-outbound-smtp-connectivity.md).

## <a name="next-steps"></a>Következő lépések

A fordított DNS-sel kapcsolatos további információkért lásd: [fordított DNS-keresés a wikipedia-ben](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [futtathatja a névkeresési zónát az internetszolgáltató által hozzárendelt IP-címtartomány Azure DNS](dns-reverse-dns-for-azure-services.md).
