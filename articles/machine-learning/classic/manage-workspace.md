---
title: 'ML Studio (klasszikus): munkaterületek kezelése – Azure'
description: Azure Machine Learning Studio (klasszikus) munkaterületek hozzáférésének kezelése, valamint Machine Learning API-webszolgáltatások üzembe helyezése és kezelése
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 6f136d0818daca1585de3896305a080ddbabfbe2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426911"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület kezelése

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


> [!NOTE]
> A webszolgáltatások Machine Learning webszolgáltatások portálján való kezelésével kapcsolatos információkért lásd: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md).
> 
> 

A Azure Portal Machine Learning Studio (klasszikus) munkaterületeket is kezelheti.



## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Studio (klasszikus) munkaterületének kezelése a Azure Portalban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) egy Azure-előfizetés rendszergazdai fiók használatával.
2. A lap tetején található keresőmezőbe írja be a "Machine learning Studio (klasszikus) munkaterületek" kifejezést, majd válassza a **Machine learning Studio (klasszikus) munkaterületek**lehetőséget.
3. Kattintson a kezelni kívánt munkaterületre.

A szabványos erőforrás-kezelési információk és a rendelkezésre álló lehetőségek mellett a következőket teheti:

- **Tulajdonságok** megtekintése – ezen a lapon látható a munkaterület és az erőforrás adatai, és módosíthatja azt az előfizetést és erőforráscsoportot, amelyhez ez a munkaterület csatlakozik.
- A **tárolási kulcsok újraszinkronizálása** – a munkaterület kulcsok megtartása a Storage-fiókban. Ha a Storage-fiók megváltoztatja a kulcsokat, kattintson a **kulcsok újraszinkronizálása** lehetőségre a kulcsok munkaterülettel való szinkronizálásához.

A Studio (klasszikus) munkaterülethez társított webszolgáltatások kezeléséhez használja a Machine Learning Web Services portált. A teljes körű tájékoztatásért lásd: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md) .

> [!NOTE]
> Új webszolgáltatások üzembe helyezéséhez vagy kezeléséhez hozzá kell rendelni egy közreműködői vagy rendszergazdai szerepkört azon az előfizetésen, amelyhez a webszolgáltatás telepítve van. Ha egy másik felhasználót egy Machine learning Studio (klasszikus) munkaterületre hívja meg, akkor a webszolgáltatások üzembe helyezése vagy kezelése előtt hozzá kell rendelnie őket egy közreműködői vagy rendszergazdai szerepkörhöz az előfizetésben. 
> 
>A hozzáférési engedélyek beállításával kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések
* További információ a [Machine Learning Azure Resource Manager-sablonokkal való üzembe helyezéséről](deploy-with-resource-manager-template.md). 
