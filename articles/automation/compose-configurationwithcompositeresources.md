---
title: DSC-konfigurációk összeállítása
description: Ez a cikk azt ismerteti, hogyan hozhat létre konfigurációkat összetett erőforrásokkal Azure Automation állapot-konfigurációban.
keywords: PowerShell DSC, a kívánt állapot konfigurációja, PowerShell DSC Azure, összetett erőforrások
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98894728"
---
# <a name="compose-dsc-configurations"></a>DSC-konfigurációk összeállítása

Ha több mint egy kívánt állapot-konfigurációval (DSC) kell kezelnie az erőforrást, a legjobb elérési út az [összetett erőforrások](/powershell/scripting/dsc/resources/authoringresourcecomposite)használata. Az összetett erőforrás egy beágyazott és paraméteres konfiguráció, amely DSC-erőforrásként van használatban egy másik konfiguráción belül. Az összetett erőforrások használata lehetővé teszi, hogy összetett konfigurációkat hozzon létre, miközben lehetővé teszi az alapul szolgáló összetett erőforrások egyedi felügyeletét és létrehozását.

Azure Automation lehetővé teszi az [összetett erőforrások importálását és összeállítását](automation-dsc-compile.md). Miután importálta az összetett erőforrásokat az Automation-fiókjába, használhatja Azure Automation állapot konfigurációját a Azure Portal **állapot konfigurálása (DSC)** funkciójával.

## <a name="compose-a-configuration"></a>Konfiguráció összeállítása

A Azure Portal összetett erőforrásaiból származó konfiguráció hozzárendeléséhez létre kell hoznia a konfigurációt. A kompozíció az állapot konfigurálása (DSC) lapon a **konfigurációk** vagy a **lefordított konfigurációk** lapon található összeállítási **konfigurációt** használja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **állapot konfigurálása (DSC)** lehetőséget a **konfiguráció kezelése** területen.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **konfigurációk** vagy a **lefordított konfigurációk** fülre, majd kattintson a **konfiguráció összeállítása** lehetőségre a lap tetején található menüben.
1. Az **alapvető** beállítások lépésben adja meg az új konfiguráció nevét (kötelező), majd kattintson bárhová az új konfigurációba felvenni kívánt összetett erőforrások sorában, majd kattintson a **tovább** gombra, vagy kattintson a **forráskód** lépésre. A következő lépésekhez kiválasztottuk `PSExecutionPolicy` és `RenameAndDomainJoin` összetett erőforrásokat.
   ![Az összeállítási konfiguráció oldalának alapjai lépésének képernyőképe](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **forráskód** lépés azt mutatja be, hogy a kiválasztott összetett erőforrások hogyan állíthatók össze. Láthatja az összes paraméter egyesítését, valamint azt, hogy a rendszer hogyan adja át őket az összetett erőforrásnak. Ha elkészült az új forráskód megtekintésével, kattintson a **tovább** gombra, vagy kattintson a **Parameters (paraméterek** ) lépésre.
   ![Képernyőkép az összeállítási konfigurációs oldal forráskód lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. A **Parameters (paraméterek** ) lépésben minden összetett erőforrás paramétere elérhetővé vált, így az értékek megadhatók. Ha egy paraméternek van leírása, akkor a paraméter mező mellett jelenik meg. Ha egy paraméter `PSCredential` típusú, a legördülő lista az aktuális Automation-fiókban található **hitelesítőadat** -objektumok listáját tartalmazza. A **+ a hitelesítő adatok hozzáadása** lehetőség is elérhető. Ha minden szükséges paramétert megadtak, kattintson a **Mentés és fordítás** elemre.
   ![Képernyőkép az összeállítási konfigurációs oldal paraméterek lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>A konfiguráció elküldése a fordításhoz

Az új konfiguráció mentése után a rendszer elküldi a fordítást. A fordítási feladatok állapotát úgy tekintheti meg, ahogy az importált konfigurációval is rendelkezik. További információ: [fordítási feladatok megtekintése](automation-dsc-getting-started.md#view-a-compilation-job).

A fordítás sikeresen befejeződött, az új konfiguráció megjelenik a **lefordított konfigurációk** lapon. Ezt követően a konfigurációt hozzárendelheti egy felügyelt csomóponthoz, a [csomópont ismételt hozzárendelésének lépéseivel egy másik csomópont-konfigurációhoz](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Következő lépések

- A csomópontok engedélyezésével kapcsolatos további információkért lásd: [Azure Automation állapot konfigurációjának engedélyezése](automation-dsc-onboarding.md).
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
