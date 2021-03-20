---
title: Altartomány delegálása – Azure PowerShell – Azure DNS
description: Ezzel a képzési útvonallal megkezdheti a Azure DNS altartomány delegálását Azure PowerShell használatával.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84712547"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Azure DNS altartomány delegálása Azure PowerShell használatával

A Azure PowerShell a DNS-altartományok delegálására is használható. Ha például a contoso.com-tartomány tulajdonosa, akkor a *Engineering* nevű altartományt delegálhatja egy másikra, külön zónára, amelyet a contoso.com-zónától függetlenül kezelhet.

Ha szeretné, delegálhat egy altartományt az [Azure Portal](delegate-subdomain.md)használatával.

> [!NOTE]
> A Contoso.com a cikk során példaként szolgál. A contoso.com helyére írja be a saját tartománynevét.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Azure DNS altartomány delegálásához először delegálnia kell a nyilvános tartományt Azure DNSre. A kiszolgálók delegáláshoz való konfigurálásával kapcsolatos útmutatásért lásd: [tartomány delegálása Azure DNS](./dns-delegate-domain-azure-dns.md) . Miután a tartomány delegálásra került a Azure DNS zónába, delegálhatja az altartományt.

## <a name="create-a-zone-for-your-subdomain"></a>Zóna létrehozása az altartományhoz

Először hozza létre a zónát a **mérnöki** altartományhoz.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Jegyezze fel a névkiszolgálók nevét

Ezután jegyezze fel a mérnöki altartomány négy névkiszolgálói kiszolgálóját.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Teszt rekord létrehozása

Hozzon létre **egy rekordot a** mérnöki zónában a teszteléshez.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy névkiszolgálói (NS) rekordot a **mérnöki** zónához a contoso.com zónában.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>A delegálás tesztelése

A delegálás teszteléséhez használja az nslookupt.

1. Indítson el egy PowerShell-ablakot.
2. A parancssorba írja be a következőt: `nslookup www.engineering.contoso.com.`
3. Meg kell kapnia egy nem mérvadó választ, amely a **10.10.10.10**.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhat fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz](dns-reverse-dns-for-azure-services.md).