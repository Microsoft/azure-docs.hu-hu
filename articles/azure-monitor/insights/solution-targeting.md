---
title: Figyelési megoldások célzása Azure Monitorban | Microsoft Docs
description: A megfigyelési megoldások célzása lehetővé teszi, hogy a figyelési megoldásokat egy adott készletre korlátozza.  Ez a cikk bemutatja, hogyan hozhat létre hatókör-konfigurációt, és hogyan alkalmazhatja azt egy megoldásra.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728569"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Figyelési megoldások célzása Azure Monitorban (előzetes verzió)
Amikor felvesz egy figyelési megoldást az előfizetéséhez, a rendszer alapértelmezés szerint automatikusan telepíti a Log Analytics munkaterülethez csatlakoztatott összes Windows-és Linux-ügynököt.  Érdemes lehet felügyelni a költségeket, és korlátozni a megoldáshoz összegyűjtött adatok mennyiségét, ha korlátozza azt egy adott ügynökre.  Ez a cikk azt ismerteti, hogyan használható a megoldások **célzása** , amely egy olyan funkció, amely lehetővé teszi egy hatókör alkalmazását a megoldásokra.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Megoldás célzása
Három lépésben kell megcéloznia a megoldást a következő szakaszokban leírtak szerint. 


### <a name="1-create-a-computer-group"></a>1. számítógépcsoport létrehozása
A hatókörbe felvenni kívánt számítógépeket a Azure Monitorban lévő [számítógépcsoport](../logs/computer-groups.md) létrehozásával adhatja meg.  A számítógépcsoport a naplózási lekérdezésen alapulhat, vagy más forrásokból, például Active Directory vagy WSUS-csoportokból is importálható. Az [alább leírtaknak](#solutions-and-agents-that-cant-be-targeted)megfelelően a hatókörben csak azok a számítógépek lesznek felszámítva, amelyek közvetlenül kapcsolódnak Azure monitorhoz.

Miután létrehozta a számítógépcsoportot a munkaterületen, akkor azt egy hatókör-konfigurációban fogja felvenni, amely egy vagy több megoldásra alkalmazható.
 
 
### <a name="2-create-a-scope-configuration"></a>2. hatókör-konfiguráció létrehozása
 A **hatókör-konfiguráció** egy vagy több számítógépcsoportot tartalmaz, és egy vagy több megoldásra alkalmazható. 
 
 Hozzon létre egy hatókör-konfigurációt a következő folyamat használatával.  

 1. A Azure Portal navigáljon **log Analytics munkaterületek** elemre, és válassza ki a munkaterületet.
 2. A munkaterület- **adatforrások** területének tulajdonságok területén válassza a **hatókör-konfigurációk** elemet.
 3. Új hatókör-konfiguráció létrehozásához kattintson a **Hozzáadás** gombra.
 4. Adja meg a hatókör-konfiguráció **nevét** .
 5. Kattintson a **számítógépcsoportok kiválasztása** elemre.
 6. Válassza ki a létrehozott számítógépcsoportot, és opcionálisan bármely más csoportot, amelyet hozzá szeretne adni a konfigurációhoz.  Kattintson a **Kiválasztás** elemre.  
 6. A hatókör-konfiguráció létrehozásához kattintson **az OK** gombra. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. alkalmazza a hatókör-konfigurációt egy megoldásra.
Ha már rendelkezik hatókör-konfigurációval, azt egy vagy több megoldásra is alkalmazhatja.  Vegye figyelembe, hogy habár egyetlen hatókör-konfigurációt is használhat több megoldással, minden megoldás csak egyetlen hatókör-konfigurációt használhat.

Hatókör-konfiguráció alkalmazása a következő folyamat használatával.  

 1. A Azure Portal navigáljon **log Analytics munkaterületek** elemre, és válassza ki a munkaterületet.
 2. A munkaterület tulajdonságainál válassza a **megoldások** elemet.
 3. Kattintson a hatókörre használni kívánt megoldásra.
 4. A megoldás tulajdonságai a **munkaterület-adatforrások** területen válassza a **megoldás célzása** lehetőséget.  Ha a beállítás nem érhető el, [Ez a megoldás nem célozható](#solutions-and-agents-that-cant-be-targeted)meg.
 5. Kattintson a **hatókör-konfiguráció hozzáadása** lehetőségre.  Ha a megoldáshoz már van konfiguráció alkalmazva, akkor ez a beállítás nem lesz elérhető.  Egy másik hozzáadása előtt el kell távolítania a meglévő konfigurációt.
 6. Kattintson a létrehozott hatókör-konfigurációra.
 7. Tekintse meg a konfiguráció **állapotát** , és győződjön meg arról, hogy az a **sikeres** megjelenítést mutatja.  Ha az állapot hibát jelez, kattintson a konfigurációtól jobbra lévő ellipszisre, és válassza a **hatókör-konfiguráció szerkesztése** lehetőséget a módosítások elvégzéséhez.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Nem megcélozható megoldások és ügynökök
Az alábbi feltételek a megoldás célcsoportját nem használó ügynökökre és megoldásokra vonatkoznak.

- A megoldások célzása csak az ügynökökre telepítendő megoldásokra vonatkozik.
- A megoldások célzása csak a Microsoft által biztosított megoldásokra vonatkozik.  Nem vonatkozik a [saját maga vagy partnerei által létrehozott](./solutions.md)megoldásokra.
- Csak olyan ügynökök szűrhetők, amelyek közvetlenül a Azure Monitorhoz kapcsolódnak.  A rendszer automatikusan telepíti a megoldásokat a csatlakoztatott Operations Manager felügyeleti csoport részét képező ügynökökre, függetlenül attól, hogy azok szerepelnek-e a hatókör-konfigurációban.

### <a name="exceptions"></a>Kivételek
A megoldások célzása nem használható a következő megoldásokkal annak ellenére, hogy azok megfelelnek a megadott feltételeknek.

- Agent Health Értékelés

## <a name="next-steps"></a>Következő lépések
- További információ a figyelési megoldásokról, beleértve a környezetében telepítendő megoldásokat az [Azure log Analytics-figyelési megoldások hozzáadása a munkaterülethez](solutions.md)lehetőségre.
- További információ a számítógépcsoportok számítógép [-csoportokon való létrehozásáról Azure monitor log lekérdezésekben](../logs/computer-groups.md).
