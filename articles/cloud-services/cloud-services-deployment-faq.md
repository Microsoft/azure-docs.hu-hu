---
title: Üzembe helyezési problémák Microsoft Azure Cloud Services GYIK-ban | Microsoft Docs
description: Ez a cikk a Microsoft Azure Cloud Services üzembe helyezésével kapcsolatos gyakori kérdéseket sorolja fel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896477"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Üzembe helyezési problémák az Azure Cloud Services (klasszikus) szolgáltatásban: gyakori kérdések (GYIK)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.
Ez a cikk a [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)üzembe helyezésével kapcsolatos gyakori kérdéseket tartalmazza. A mérettel kapcsolatos információkért tekintse meg a [Cloud Services virtuális gép mérete lapot](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Miért működik a felhőalapú szolgáltatás üzembe helyezése az átmeneti tárolóhelyen, néha erőforrás-lefoglalási hiba esetén, ha már létezik üzemelő példány az üzemi tárolóhelyen?
Ha egy felhőalapú szolgáltatás egyik tárolóhelyen üzemel, a teljes felhőalapú szolgáltatás egy adott fürthöz van rögzítve. Ez azt jelenti, hogy ha egy üzemelő példány már létezik az üzemi tárolóhelyen, egy új átmeneti üzembe helyezés csak ugyanabban a fürtben lehet lefoglalni, mint az üzemi tárolóhely.

A foglalási hibák akkor fordulnak elő, ha a felhőalapú szolgáltatást tartalmazó fürt nem rendelkezik elegendő fizikai számítási erőforrással a telepítési kérelem teljesítéséhez.

A lefoglalási hibák enyhítésével kapcsolatos segítségért lásd [: Cloud Service foglalási hiba: megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Miért van a felhőalapú szolgáltatások üzembe helyezése vagy horizontális felskálázása a lefoglalási hiba miatt?
Felhőalapú szolgáltatás telepítésekor általában egy adott fürthöz rögzítve lesz. Ez azt jelenti, hogy a meglévő felhőalapú szolgáltatások felskálázásának fel kell osztania az új példányokat ugyanabban a fürtben. Ha a fürt közel van a kapacitáshoz, vagy a kívánt VM-méret/típus nem érhető el, a kérés meghiúsulhat.

A lefoglalási hibák enyhítésével kapcsolatos segítségért lásd [: Cloud Service foglalási hiba: megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Miért érdemes a felhőalapú szolgáltatást egy affinitási csoportba telepíteni, időnként lefoglalási hibát eredményezhet?
Egy üres felhőalapú szolgáltatás új üzembe helyezését a háló bármely, az adott régióban található fürtben kioszthatja, kivéve, ha a felhőalapú szolgáltatás egy affinitási csoportba van rögzítve. Ugyanazon a fürtön ugyanazon az affinitási csoporton üzemelő példányok fognak próbálkozni. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.

A lefoglalási hibák enyhítésével kapcsolatos segítségért lásd [: Cloud Service foglalási hiba: megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Miért módosítja a virtuális gép méretét, vagy ha egy meglévő felhőalapú szolgáltatáshoz hozzáad egy új virtuális gépet, időnként lefoglalási hibát eredményezhet?
Az adatközpontban lévő fürtök különböző konfigurációkkal rendelkezhetnek (például sorozat, Av2 sorozat, D sorozat, Dv2 sorozat, G sorozat, H sorozat stb.). A fürtök azonban nem feltétlenül rendelkeznek a virtuális gépek összes típusával. Ha például egy olyan felhőalapú szolgáltatáshoz próbál hozzáadni egy D sorozatú virtuális gépet, amely már telepítve van egy csak sorozattal rendelkező fürtben, akkor foglalási hibát tapasztal. Ez akkor is előfordulhat, ha a virtuális gép SKU-méreteit próbálja meg módosítani (például egy sorozatról D sorozatra váltás).

A lefoglalási hibák enyhítésével kapcsolatos segítségért lásd [: Cloud Service foglalási hiba: megoldások](cloud-services-allocation-failures.md#solutions).

A régiójában elérhető méretek ellenőrzéséhez tekintse meg a [Microsoft Azure: a régiók által elérhető termékek](https://azure.microsoft.com/regions/services)című témakört.

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Miért sikertelen a Cloud Service üzembe helyezése az előfizetés vagy a szolgáltatás korlátai/kvótái/korlátai miatt?
A felhőalapú szolgáltatás telepítése meghiúsulhat, ha a lefoglalni kívánt erőforrások meghaladják a szolgáltatáshoz a régió/adatközpont szintjén engedélyezett alapértelmezett vagy maximális kvótát. További információ: [Cloud Services korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Az előfizetéshez tartozó aktuális használati/kvótát a portálon is nyomon követheti: Azure Portal => Subscriptions => \<appropriate subscription>   => "használat + kvóta".

Az erőforrás-használat/fogyasztással kapcsolatos információk a Azure-számlázási API-k használatával is beolvashatók. Lásd: az [Azure felhasználási API áttekintése](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hogyan változtathatom meg egy üzembe helyezett Cloud Service-beli virtuális gép méretét az újbóli üzembe helyezése nélkül?
Az üzembe helyezett felhőalapú szolgáltatások virtuálisgép-mérete nem módosítható újbóli üzembe helyezés nélkül. A virtuális gép mérete a CSDEF van beépítve, amely csak újbóli telepítéssel frissíthető.

További információ: [a Cloud Service frissítése](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Miért nem tudom telepíteni Cloud Services a Service Management API-kon vagy a PowerShellen keresztül Azure Resource Manager Storage-fiók használatakor? 

Mivel a Cloud Service egy klasszikus erőforrás, amely nem kompatibilis a Azure Resource Manager modellel, nem társíthatja azt a Azure Resource Manager Storage-fiókokhoz. Íme néhány lehetőség: 

- Üzembe helyezés REST APIon keresztül.

    Ha a Service Management REST APIon keresztül végzi a telepítést, a korlátozás megkerülhető úgy, hogy megad egy SAS URL-címet a blob Storage-hoz, amely a klasszikus és a Azure Resource Manager Storage-fiókkal is működik. További információ az "PackageUrl" tulajdonságról [itt](/previous-versions/azure/reference/ee460813(v=azure.100))olvasható.

- Üzembe helyezés [Azure Portalon](https://portal.azure.com)keresztül.

    Ez a művelet a [Azure Portal](https://portal.azure.com) fog működni, mivel a hívás olyan proxyn/alátéten halad át, amely lehetővé teszi a Azure Resource Manager és a klasszikus erőforrások közötti kommunikációt. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Miért Azure Portal megkövetelni a Storage-fiókot az üzembe helyezéshez?

A klasszikus portálon a csomag közvetlenül a felügyeleti API-rétegbe lett feltöltve, majd az API-réteg átmenetileg a csomagot egy belső Storage-fiókba helyezi.  Ez a folyamat teljesítménybeli és méretezhetőségi problémákat okoz, mivel az API-réteg nem fájlfeltöltés-szolgáltatásként lett kialakítva.  A Azure Portal (Resource Manager-alapú üzemi modell) esetében megkerüljük az API-rétegre való első feltöltés átmeneti lépését, ami gyorsabb és megbízhatóbb üzembe helyezést eredményezett.

Ami a költségeket illeti, nagyon kicsi, és az összes üzemelő példányon újra felhasználhatja ugyanazt a Storage-fiókot. A [tárolási Cost kalkulátor](https://azure.microsoft.com/pricing/calculator/#storage1) segítségével meghatározhatja a SZERVIZCSOMAG (CSPKG) feltöltésének költségeit, letöltheti a CSPKG, majd törölheti a CSPKG.
