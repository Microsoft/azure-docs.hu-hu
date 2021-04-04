---
title: Vendég operációsrendszer-család 1 – nyugdíjazási nyilatkozat | Microsoft Docs
description: Információt nyújt arról, hogy mikor történt az Azure vendég operációs rendszer 1. családjának kivonulása, és hogyan állapítható meg, hogy érintett-e
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743015"
---
# <a name="guest-os-family-1-retirement-notice"></a>1. vendég operációsrendszer-család használatból való kivonásáról szóló értesítés

Az 1. operációsrendszer-család kivonulása először 2013. június 1-jén jelent meg.

**Szeptember 2., 2014** A rendszer hivatalosan kivonta a Windows Server 2008 operációs rendszeren alapuló Azure vendég operációs rendszer (vendég operációs rendszer) 1. x családját. Az új szolgáltatások üzembe helyezésére vagy a meglévő szolgáltatások az 1. családdal való frissítésére tett kísérletek sikertelenek lesznek, és hibaüzenet jelenik meg, amely tájékoztatja, hogy a vendég operációs rendszer 1. családja kimaradt.

**November 3., 2014** A vendég operációs rendszer 1. családjának kiterjesztett támogatása befejeződött, és teljes mértékben megszűnik. A rendszer minden, az 1. családban található szolgáltatást érint. Ezeket a szolgáltatásokat bármikor leállíthatja. Nem garantálható, hogy a szolgáltatások továbbra is futnak, hacsak manuálisan nem frissíti őket.

Ha további kérdése van, látogasson el a [Microsoft Q&a Cloud Servicesra](/answers/topics/azure-cloud-services.html) , vagy [forduljon az Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Érintett?
A Cloud Services érintik, ha a következők valamelyike érvényes:

1. A "osFamily =" 1 "értéke explicit módon meg van adva a ServiceConfiguration. cscfg fájlban a felhőalapú szolgáltatáshoz.
2. Nem rendelkezik a felhőalapú szolgáltatáshoz tartozó ServiceConfiguration. cscfg fájlban explicit módon megadott osFamily-értékkel. A rendszer jelenleg az alapértelmezett "1" értéket használja ebben az esetben.
3. A Azure Portal a vendég operációs rendszer családjának értékét a "Windows Server 2008" értékkel listázza.

Annak megállapításához, hogy a Cloud Services melyik operációsrendszer-családot futtatja, a következő parancsfájlt futtathatja Azure PowerShellban, de először [be kell állítania a Azure PowerShell](/powershell/azure/) . További információ a parancsfájlról: [Azure vendég operációs rendszer 1. családjának vége: június 2014](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Ha a osFamily oszlop üres vagy "1", a Cloud Services az 1. operációsrendszer-család számára lesz hatással.

## <a name="recommendations-if-you-are-affected"></a>Javaslatok, ha érintett
Javasoljuk, hogy a Cloud Service-szerepköröket az egyik támogatott vendég operációsrendszer-családba telepítse át:

**Vendég operációsrendszer-család 4. x** – Windows Server 2012 R2 *(ajánlott)*

1. Győződjön meg arról, hogy az alkalmazás az SDK 2,1-es vagy újabb verzióját használja a .NET-keretrendszer 4,0, 4,5 vagy 4.5.1 használatával.
2. Állítsa a osFamily attribútumot "4" értékre a ServiceConfiguration. cscfg fájlban, és telepítse újra a Cloud Service-t.

**Vendég operációsrendszer-család 3. x** – Windows Server 2012

1. Győződjön meg arról, hogy az alkalmazás az SDK 1,8-es vagy újabb verzióját használja a .NET-keretrendszer 4,0 vagy 4,5 használatával.
2. Állítsa a osFamily attribútumot "3" értékre a ServiceConfiguration. cscfg fájlban, és telepítse újra a Cloud Service-t.

**Vendég operációsrendszer-család 2. x** – Windows Server 2008 R2

1. Győződjön meg arról, hogy az alkalmazás az SDK 1,3-es vagy újabb verzióját használja a .NET-keretrendszer 3,5 vagy a 4,0 használatával.
2. Állítsa a osFamily attribútumot "2" értékre a ServiceConfiguration. cscfg fájlban, és telepítse újra a Cloud Service-t.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Kiterjesztett támogatás a vendég operációs rendszer családja 1. november 3., 2014
A vendég operációsrendszer-családon futó Cloud Services már nem támogatott. A szolgáltatás megszakadásának elkerülése érdekében a lehető leghamarabb telepítse át az 1. családot.  

## <a name="next-steps"></a>Következő lépések
Tekintse át a [vendég operációs rendszer legújabb kiadásait](cloud-services-guestos-update-matrix.md).