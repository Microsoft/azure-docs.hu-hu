---
title: Biztonsági riasztások a Azure DevTest Labs környezetekben
description: Ebből a cikkből megtudhatja, hogyan tekintheti meg a környezetek biztonsági riasztásait a DevTest Labs szolgáltatásban, és megteheti a megfelelő lépéseket.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9eea06066cfca5f67d920456f16e2eb7893dce39
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483975"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Biztonsági riasztások a Azure DevTest Labs környezetekben
Labor-felhasználóként most megtekintheti Azure Security Center riasztásokat a tesztkörnyezet környezetében. A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek. [További információ a Azure Security Center biztonsági értesítéseiről](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Előfeltételek
A biztonsági riasztásokat jelenleg csak a tesztkörnyezetben üzembe helyezett, szolgáltatásként szolgáló környezetekben lehet megtekinteni. A szolgáltatás teszteléséhez vagy használatához [telepítsen egy környezetet a laborba](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Környezet biztonsági értesítéseinek megtekintése

1. A labor kezdőlapján válassza a bal oldali menüben a **biztonsági riasztások** elemet. Ekkor meg kell jelennie a biztonsági riasztások számának (magas, közepes és alacsony). További információ a [riasztások besorolásáról](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Biztonsági riasztások – áttekintés](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kattintson a jobb gombbal az utolsó oszlopban található három pontra (...), majd válassza a **biztonsági riasztások megtekintése**lehetőséget. 

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. A riasztásokkal és az Advisor-javaslatokkal kapcsolatban további részleteket talál. További információ a [Azure Security Center biztonsági riasztások kezeléséről és megválaszolásáról](../security-center/security-center-managing-and-responding-alerts.md).

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>További lépések
A környezetekkel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Több virtuális gépre kiterjedő környezetek és Pásti-erőforrások létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata](devtest-lab-configure-use-public-environments.md)
