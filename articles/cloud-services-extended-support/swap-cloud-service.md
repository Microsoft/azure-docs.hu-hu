---
title: Üzemelő példányok felcserél helyezése vagy váltása Azure Cloud Services (kiterjesztett támogatás)
description: Megtudhatja, hogyan válthat az üzemelő példányok között a Azure Cloud Services (kiterjesztett támogatás).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748821"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Üzemelő példányok felcserél helyezése vagy váltása Azure Cloud Services (kiterjesztett támogatás)

A két független felhőszolgáltatás üzemelő példánya között felcserélhet a Azure Cloud Services (kiterjesztett támogatás). A Azure Cloud Services (klasszikus) Azure Resource Manager modell a Azure Cloud Services (kiterjesztett támogatás) nem használ üzembe helyezési pontokat. A Azure Cloud Services (kiterjesztett támogatás) esetén a felhőszolgáltatás új kiadásának üzembe helyezésekor a felhőszolgáltatást "felcserélhetőre" helyezheti egy meglévő felhőszolgáltatással a Azure Cloud Services (kiterjesztett támogatás).

Az üzemelő példányok felcserélte után az új kiadást az új felhőszolgáltatás üzemelő példányával is tesztelheti. A felcserélés valójában egy új felhőszolgáltatást hirdet meg, amely éles környezetben való kiadásra van előléptetve.

> [!NOTE]
> Nem lehet felcserélni egy Azure Cloud Services (klasszikus) és egy Azure Cloud Services (kiterjesztett támogatás) üzemelő példány között.

A felhőszolgáltatást fel kell cserélni egy másik felhőszolgáltatásra, amikor két felhőszolgáltatást helyez üzembe.

Az üzembe helyezéseket felcserélheti egy Azure Resource Manager sablonnal (ARM-sablon), a Azure Portal vagy a REST API.

## <a name="arm-template"></a>ARM-sablon

Ha ARM-sablonalapú üzembe helyezési módszert használ, hogy a felhőszolgáltatások felcserélhetők, állítsa az objektum tulajdonságát a párosított `SwappableCloudService` `networkProfile` `cloudServices` felhőszolgáltatás azonosítójára:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure Portal

Üzemelő példány felcserél helyezése a Azure Portal:

1. A portál menüjében válassza a Cloud Services **(kiterjesztett támogatás)** vagy az **Irányítópult lehetőséget.**
1. Válassza ki a frissíteni kívánt felhőszolgáltatást.
1. A **felhőszolgáltatás** Áttekintésében válassza a **Felcserélés lehetőséget:**

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="A felhőszolgáltatás felcserélés lapját bemutató képernyőkép.":::

1. A felcserélés megerősítése panelen ellenőrizze az üzembe helyezési adatokat, majd kattintson az **OK** gombra az üzemelő példányok felcserél helyezéséhez:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Képernyőkép az üzembehelyezés felcserélési információinak megerősítéséről.":::

Az üzemelő példányok gyorsan felcserélnek, mert az egyetlen dolog, ami megváltozik, az az üzembe helyezett felhőszolgáltatás virtuális IP-címe.

A számítási költségek csökkentése érdekében törölheti az egyik felhőszolgáltatást (amely előkészítési környezetként van ki jelölve az alkalmazás üzembe helyezéséhez), miután ellenőrizte, hogy a felcserélhető felhőszolgáltatás a várt módon működik-e.

## <a name="rest-api"></a>REST API

Az alábbi REST API és JSON-konfigurációval váltson át egy új felhőszolgáltatás üzembe helyezésére a Azure Cloud Services (kiterjesztett támogatás) használatával:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Gyakori kérdések az üzemelő példányok felcserélésről

Tekintse át ezeket a válaszokat az üzembehelyezés felcserélhető példányai Azure Cloud Services (kiterjesztett támogatás) kérdésére.

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Mik az új felhőszolgáltatások üzemelő példányára való felcserélés előfeltételei?

A sikeres üzembe helyezési felcseréléshez két fő előfeltételt kell teljesítenie a Azure Cloud Services (kiterjesztett támogatás):

* Ha statikus vagy fenntartott IP-címet szeretne használni az egyik felcserélhető felhőszolgáltatáshoz, a másik felhőszolgáltatásnak is fenntartott IP-címet kell használnia. Ellenkező esetben a csere sikertelen lesz.
* Ahhoz, hogy a felcserélés sikeres legyen, a szerepkörök összes példányának futnia kell. A példányok állapotának ellenőrzéséhez a Azure Portal az  újonnan üzembe helyezett felhőszolgáltatás áttekintését ismertető oldalon, vagy használja a parancsot a `Get-AzRole` Windows PowerShell.

A vendég operációs rendszer frissítései és a szolgáltatás-javító műveletek az üzembe helyezés felcserélének meghiúsulását okozhatják. További információ: [Felhőszolgáltatások üzemelő példányának hibaelhárítása.](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>A VIP-felcserélést egy másik mutató művelettel párhuzamosan is el lehet tetsszen?

Nem. A VIP-felcserélés csak hálózattal kapcsolatos módosítás, amely csak akkor ér véget, ha bármilyen más számítási műveletet elindít egy felhőszolgáltatásban. Ha egy felhőszolgáltatás frissítési, törlési vagy automatikus skálázási művelete van folyamatban, miközben egy VIP-felcserélés van folyamatban, vagy vip-felcserélést vált ki egy másik számítási művelet közben, előfordulhat, hogy a felhőszolgáltatás nem állítható vissza hibaállapotba.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>A felcserélés állásidőt jelent az alkalmazásom számára, és hogyan kezeljem?

A felhőszolgáltatások felcserélhetősége általában gyors, mivel ez csak egy konfigurációs változás az Azure Load Balancerben. Bizonyos esetekben a felcserélés 10 vagy több másodpercig is igénybe vehet, ami átmeneti csatlakozási hibákat eredményezhet. A felcserélés felhasználókra gyakorolt hatásának korlátozása érdekében fontolja meg az ügyfél-újrapróbálkozási logika alkalmazását.

## <a name="next-steps"></a>Következő lépések 

* Tekintse [át az üzembe helyezés előfeltételeit](deploy-prerequisite.md) a Azure Cloud Services (kiterjesztett támogatás).
* Tekintse [át a gyakori kérdéseket a](faq.md) Azure Cloud Services (kiterjesztett támogatás).
* Üzembe helyezhet egy Azure Cloud Services (kiterjesztett támogatás) felhőszolgáltatást az alábbi lehetőségek egyikének használatával:
  * [Azure Portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM-sablon](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
