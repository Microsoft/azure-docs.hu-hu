---
title: Windows-rendszerképek használata az Azure-ban
description: A Visual Studio előfizetési előnyeinek használata a Windows 7, Windows 8 vagy Windows 10 Azure-ban való üzembe helyezéséhez fejlesztési és tesztelési helyzetekben
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 0f8a17ca69da15d650ba88642d7a81e477a6c537
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627146"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-ügyfél használata az Azure-ban fejlesztési/tesztelési helyzetekben
A fejlesztői és tesztelési forgatókönyvekhez használhatja a Windows 7, Windows 8 vagy Windows 10 Enterprise (x64) rendszert az Azure-ban, amennyiben rendelkezik a megfelelő Visual Studio (korábbi MSDN) előfizetéssel. 

Ha éles környezetben szeretné futtatni a Windows 10 rendszert, tekintse [meg a Windows 10 Azure-beli üzembe helyezése a több-bérlős üzemeltetési jogosultságokkal](windows-desktop-multitenant-hosting-deployment.md)című témakört.


## <a name="subscription-eligibility"></a>Előfizetés támogathatósága
Az aktív Visual Studio-előfizetők (akik Visual Studio-előfizetési licenccel szereztek be) Windows-rendszerképeket használhatnak fejlesztési és tesztelési célokra. A Windows-ügyfélszoftverek saját hardveren vagy Azure-beli virtuális gépeken is használhatók.

Bizonyos Windows-ügyfélprogramok elérhetők az Azure piactéren. A Visual Studio-előfizetők bármilyen típusú ajánlaton belül [előkészíthetnek és létrehozhatnak](prepare-for-upload-vhd-image.md) 64-bites Windows 7, Windows 8 vagy Windows 10 rendszerképeket, majd [feltöltheti őket az Azure](upload-generalized-managed.md)-ba.

## <a name="eligible-offers-and-client-images"></a>Jogosult ajánlatok és ügyféloldali rendszerképek
Az alábbi táblázat a Windows-rendszerképek Azure piactéren való üzembe helyezésére jogosult ajánlat-azonosítókat ismerteti. A Windows-ügyfél lemezképei csak a következő ajánlatokban láthatók. 

| Offer Name | Ajánlat száma | Elérhető ügyféloldali rendszerképek | 
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Premium MSDN-nel (juttatás)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise- (BizSpark-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Azure-előfizetés keresése
Ha nem ismeri az ajánlat AZONOSÍTÓját, az alábbi két módszer egyikével szerezheti be a Azure Portal:  
- Az *előfizetések* ablakban: ![ az ajánlat azonosítójának részletei a Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- Vagy kattintson a **számlázás** lehetőségre, majd az előfizetés-azonosítóra. Az ajánlat azonosítója megjelenik a *Számlázási* ablakban.
Az ajánlat AZONOSÍTÓját az Azure-fiók portál [előfizetés lapján](https://account.windowsazure.com/Subscriptions) is megtekintheti: az ![ ajánlat azonosítójának részletei az Azure-fiók portálján](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Következő lépések
Most már üzembe helyezheti a virtuális gépeket a [PowerShell](quick-create-powershell.md), a [Resource Manager-sablonok](ps-template.md)vagy a [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)használatával.