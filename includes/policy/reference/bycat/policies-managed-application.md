---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 308c94332ac71f873b32178fe6da418c9079010f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866835"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt alkalmazás alkalmazásdefiníciójának az ügyfél által megadott tárfiókot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |A saját tárfiókját használhatja az alkalmazásdefiníciós adatok szabályozására, ha ez szabályozási vagy megfelelőségi követelmény. Dönthet úgy, hogy a felügyelt alkalmazás definícióját a létrehozás során megadott tárfiókban tárolja, így annak helye és hozzáférése teljes mértékben kezelhető az előírásoknak való megfelelés érdekében. |naplózás, tiltás, letiltás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Társítások üzembe helyezése felügyelt alkalmazáshoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Üzembe helyez egy társítási erőforrást, amely a kiválasztott erőforrástípusokat társítja a megadott felügyelt alkalmazáshoz.  Ez a szabályzattelepítés nem támogatja a beágyazott erőforrástípusokat. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
