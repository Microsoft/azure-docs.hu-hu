---
title: Szerepkörök és engedélyek az Azure Data Factoryhoz
description: Az adat-előállítók létrehozásához és a gyermekek erőforrásainak használatához szükséges szerepköröket és engedélyeket ismerteti.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364477"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Szerepkörök és engedélyek az Azure Data Factoryhoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Ez a cikk a Azure Data Factory erőforrások létrehozásához és kezeléséhez szükséges szerepköröket, valamint a szerepkörök által biztosított engedélyeket ismerteti.

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Data Factory példányok létrehozásához az Azure-ba való bejelentkezéshez használt felhasználói fióknak a *közreműködő* szerepkör, a *tulajdonos* szerepkör vagy az Azure-előfizetés *rendszergazdája* tagjának kell lennie. Az előfizetésben található engedélyek megtekintéséhez a Microsoft Azure Portalon kattintson a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. 

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:
- A Azure Portal alárendelt erőforrásainak létrehozásához és kezeléséhez a **Data Factory közreműködő** szerepkörhöz kell tartoznia az **erőforráscsoport** szintjén vagy felett.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../cost-management-billing/manage/add-change-subscription-administrator.md) című cikket.

## <a name="set-up-permissions"></a>Engedélyek beállítása

A Data Factory létrehozása után előfordulhat, hogy más felhasználók is használni szeretnék az adatelőállítót. Ahhoz, hogy ez a hozzáférés más felhasználók számára is elérhető legyen, hozzá kell adnia azokat a beépített **Data Factory közreműködő** szerepkörhöz a Data Factory tartalmazó **erőforráscsoporthoz** .

### <a name="scope-of-the-data-factory-contributor-role"></a>A Data Factory közreműködő szerepkör hatóköre

A **Data Factory közreműködő** szerepkör tagsága lehetővé teszi a felhasználók számára a következő műveleteket:
- Adat-és alárendelt erőforrások létrehozása, szerkesztése és törlése, beleértve az adatkészleteket, a társított szolgáltatásokat, a folyamatokat, az eseményindítókat és az integrációs modulokat.
- Resource Manager-sablonok üzembe helyezése. A Resource Manager-alapú üzemelő példány a Azure Portal Data Factory által használt központi telepítési módszer.
- Az alkalmazással kapcsolatos adatelemzési riasztások kezelése egy adatgyárban.
- Támogatási jegyek létrehozása.

További információ erről a szerepkörről: [Data Factory közreműködő szerepkör](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Resource Manager-sablon üzembe helyezése

Az **Data Factory közreműködő** szerepkör, az erőforráscsoport szintjén vagy felett, lehetővé teszi a felhasználóknak Resource Manager-sablonok telepítését. Ennek eredményeképpen a szerepkör tagjai a Resource Manager-sablonok használatával üzembe helyezhetik az adat-előállítók és a hozzájuk tartozó alárendelt erőforrásokat, beleértve az adatkészleteket, a társított szolgáltatásokat, a folyamatokat, az eseményindítókat és az integrációs modulokat. A szerepkör tagsága nem teszi lehetővé, hogy a felhasználó más erőforrásokat hozzon létre.

Az Azure Repos és a GitHub engedélyei függetlenek Data Factory engedélyeitől. Ennek eredményeképpen egy olyan felhasználó, aki a tárház engedélyekkel rendelkezik, és csak az olvasó szerepkör tagja, szerkesztheti Data Factory gyermek erőforrásait és véglegesítheti a tárház módosításait, de nem teheti közzé ezeket a módosításokat.


> [!IMPORTANT]
> A **Data Factory közreműködő** szerepkörrel rendelkező Resource Manager-sablonok nem emelik fel az engedélyeiket. Ha például olyan sablont telepít, amely egy Azure-beli virtuális gépet hoz létre, és nem rendelkezik virtuális gépek létrehozásához szükséges engedéllyel, akkor a központi telepítés engedélyezési hibával meghiúsul.

   A közzétételi kontextusban a **Microsoft. DataFactory/gyárak/írási** engedélyek az alábbi módokon érvényesek.
- Ez az engedély csak akkor szükséges az élő módban, ha az ügyfél módosítja a globális paramétereket.
- Ezt az engedélyt mindig a git módban kell megadni, mivel az ügyfél által közzétett minden alkalommal, a legutóbbi véglegesítő AZONOSÍTÓval rendelkező gyári objektumot frissíteni kell.

### <a name="custom-scenarios-and-custom-roles"></a>Egyéni forgatókönyvek és egyéni szerepkörök

Előfordulhat, hogy különböző hozzáférési szinteket kell megadnia a különböző adat-előállító felhasználók számára. Például:
- Előfordulhat, hogy olyan csoportra van szüksége, amelyben a felhasználók csak egy adott adatelőállítóhoz rendelkeznek engedéllyel.
- Vagy szükség lehet egy olyan csoportra, amelyben a felhasználók csak egy adatgyárat (vagy gyárakat) tudnak figyelni, de nem módosíthatják azt.

Ezeket az egyéni forgatókönyveket egyéni szerepkörök létrehozásával és a szerepkörökhöz tartozó felhasználók hozzárendelésével érheti el. További információ az egyéni szerepkörökről: [Egyéni szerepkörök az Azure-ban](..//role-based-access-control/custom-roles.md).

Íme néhány példa, amely bemutatja, hogy mi valósítható meg az egyéni szerepkörökkel:

- Lehetővé teszi, hogy a felhasználó a Azure Portal egy erőforráscsoport adatelőállítóját hozza létre, szerkessze vagy törölje.

  Rendelje hozzá a beépített **Data Factory közreműködő** szerepkört a felhasználó erőforráscsoport szintjén. Ha engedélyezni szeretné az előfizetéshez tartozó bármely adat-előállító hozzáférését, rendelje hozzá a szerepkört az előfizetés szintjén.

- A felhasználó megtekintheti (olvashatja) és figyelheti az adatgyárat, de nem szerkesztheti vagy módosíthatja.

  Rendelje hozzá a beépített **olvasó** szerepkört a felhasználóhoz tartozó adat-előállító erőforráshoz.

- Lehetővé teszi, hogy a felhasználó egyetlen adatfeldolgozót szerkesszen a Azure Portalban.

  Ehhez a forgatókönyvhöz két szerepkör-hozzárendelés szükséges.

  1. Társítsa a beépített **közreműködő** szerepkört a adat-előállító szintjén.
  2. Hozzon létre egy egyéni szerepkört az engedéllyel rendelkező  **Microsoft. Resources/Deployments/**. Rendelje hozzá ezt az egyéni szerepkört a felhasználóhoz erőforráscsoport szintjén.

- Lehetővé teszi, hogy a felhasználó tesztelje a kapcsolatokat egy társított szolgáltatásban, vagy előnézeti adatokat egy adatkészletben

    Hozzon létre egy egyéni szerepkört, amely rendelkezik engedélyekkel a következő műveletekhez: **Microsoft. DataFactory/Factorys/getFeatureValue/Read** és **Microsoft. DataFactory/gyárak/getDataPlaneAccess/Action**. Rendelje hozzá ezt az egyéni szerepkört a felhasználóhoz tartozó adat-előállító erőforráshoz.

- Lehetővé teheti, hogy a felhasználó frissítsen egy adatgyárat a PowerShellből vagy az SDK-ból, de ne a Azure Portal.

  Rendelje hozzá a beépített **közreműködő** szerepkört a felhasználóhoz tartozó adat-előállító erőforráshoz. Ez a szerepkör lehetővé teszi, hogy a felhasználó láthassa a Azure Portal erőforrásait, de a felhasználó nem férhet hozzá a  **Közzététel** és az **összes közzététel** gombhoz.


## <a name="next-steps"></a>Következő lépések

- További információ az Azure-beli szerepkörökről – a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

- További információ a **Data Factory közreműködő** szerepkör- [Data Factory közreműködő szerepkörről](../role-based-access-control/built-in-roles.md#data-factory-contributor).
