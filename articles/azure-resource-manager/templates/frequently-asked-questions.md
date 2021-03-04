---
title: ARM-sablon – gyakori kérdések
description: Gyakori kérdések (GYIK) Azure Resource Manager-sablonokról (ARM-sablonok).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 24baa0dc7444f632040ddaf9fcc2266f9e4ccae5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036140"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Az ARM-sablonokkal kapcsolatos gyakori kérdések

Ez a cikk a Azure Resource Manager-sablonokkal (ARM-sablonokkal) kapcsolatos gyakori kérdésekre ad választ.

## <a name="getting-started"></a>Első lépések

* **Mik azok az ARM-sablonok, és miért érdemes használni?**

  Az ARM-sablonok JSON-fájlok, ahol meghatározhatja, hogy mit szeretne az Azure-ba telepíteni. A sablonok segítséget nyújtanak az Azure-hoz készült infrastruktúra-kód megoldás megvalósításában. A szervezet ismételten és megbízhatóan helyezheti üzembe a szükséges infrastruktúrát különböző környezetekben.

  Ha többet szeretne megtudni arról, hogy az ARM-sablonok hogyan segítik az Azure-infrastruktúra kezelését, tekintse meg a [Mi az ARM-sablonok?](overview.md)

* **Hogyan a sablonokkal való ismerkedést?**

  Az ARM-sablonok egyszerűbb létrehozásához a megfelelő eszközökre van szükség. Javasoljuk, hogy telepítse a [Visual Studio Code](https://code.visualstudio.com/) -ot és a [Azure Resource Manager Tools bővítményt](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Az eszközök gyors bevezetését a rövid útmutató [: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)használatával című témakörben tekintheti meg.

  Ha készen áll az ARM-sablonok létrehozásával kapcsolatos információk megismerésére, indítsa el a [kezdő oktatóanyag-sorozatot az ARM-sablonokban](template-tutorial-create-first-template.md). Ezek az oktatóanyagok részletesen ismertetik az ARM-sablonok összeállításának folyamatát. Megismerheti a sablon különböző részeit és azok közös működését. Ez a tartalom [Microsoft Learn modulként](/learn/modules/authoring-arm-templates/)is elérhető.

* **ARM-sablonokat vagy Terraform kell használni az Azure-ban való üzembe helyezéshez?**

  Használja a legmegfelelőbb lehetőséget. Mindkét szolgáltatás segítséget nyújt az üzembe helyezések automatizálásában az Azure-ban.

  Hiszünk abban, hogy az ARM-sablonokat más infrastruktúra-kódú szolgáltatásokhoz is használják. További információ ezekről az előnyökről: [Miért érdemes az ARM-sablonokat választani?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **Kihagytam a bemutatót a Microsoft Build 2020-es verziójában. Elérhető a bemutató a megtekintéshez?**

  Igen, kérjük, [tekintse meg bármikor](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Hol kaphatok további információt a Build-ben bejelentett új szolgáltatásokról?**

  A jelenleg használt funkciókkal kapcsolatos általános információkért csatlakozzon az [Azure Advisors üzembe helyezési Yammer csoportjához](https://aka.ms/ARMMeet).

  Az új sablon nyelvének megismeréséhez [regisztráljon az értesítésekre](https://aka.ms/armLangUpdates).

  A sablon specifikációinak megismeréséhez tekintse meg a [Azure Resource Manager sablon specifikációi (előzetes verzió)](template-specs.md)című témakört.

## <a name="creating-and-testing-templates"></a>Sablonok létrehozása és tesztelése

* **Hol tudhatok meg többet az ARM-sablonok ajánlott eljárásairól?**

  A sablonok megvalósításával kapcsolatos javaslatokért lásd: [ARM-sablon – ajánlott eljárások](template-best-practices.md). A sablon létrehozása után futtassa az [ARM test toolkitt](https://github.com/azure/arm-ttk). Ellenőrzi, hogy a sablon megfelel-e az ajánlott eljárásoknak.

* **Beállítottam a környezetet a portálon. Van valamilyen mód a sablon meglévő erőforráscsoporthoz való beolvasására?**

  Igen, [exportálhatja a sablont](export-template-portal.md) egy erőforráscsoport használatával. Az exportált sablon jó kiindulási pont a sablonok megismeréséhez, de érdemes módosítania, mielőtt éles környezetben használja.

  A sablon exportálásakor kiválaszthatja, hogy mely erőforrásokat szeretné felvenni a sablonba.

* **Létrehozhatok egy erőforráscsoportot egy ARM-sablonban, és erőforrásokat telepíthetek rá?**

  Igen, létrehozhat egy erőforráscsoportot egy sablonban, ha az Azure-előfizetése szintjén telepíti a sablont. Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez lásd: [erőforráscsoport és erőforrások](deploy-to-subscription.md#resource-groups).

* **Létrehozhatok egy előfizetést egy ARM-sablonban?**

  Igen, további információ: programozott módon [hozza létre az Azure-előfizetéseket a legújabb API](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)-kkal.

* **Hogyan lehet tesztelni a sablont a telepítés előtt?**

  Azt javasoljuk, hogy az [ARM test Toolkit](https://github.com/azure/arm-ttk) és a [mi-if művelet](template-deploy-what-if.md) futtatása előtt futtassa a saját sablonjait. A tesztelési eszközkészlet ellenőrzi, hogy a sablon az ajánlott eljárásokat használja-e. Ha olyan módosításokat azonosít, amelyek javíthatják a sablon megvalósítását, figyelmeztetéseket ad meg.

  A mi-if művelet megjeleníti a sablon által a környezetben végrehajtott módosításokat. A telepítés előtt megtekintheti a nem kívánt módosításokat. Mi a teendő, ha az ellenőrzés során észlelt hibákat is visszaadja. Ha például a sablon szintaktikai hibát tartalmaz, ezt a hibát adja vissza. Továbbá visszaadja azokat a hibákat, amelyek a telepített erőforrások végső állapotáról meghatározhatók. Ha például a sablon olyan nevű Storage-fiókot telepít, amely már használatban van, a következőt adja vissza:.

* **Hol találhatók az egyes erőforrástípusok számára elérhető tulajdonságokkal kapcsolatos információk?**

  A VS Code biztosítja az IntelliSense használatát az erőforrás-tulajdonságok kezeléséhez. Megtekintheti a tulajdonságok és leírások [sablon-referenciáját](/azure/templates/) is.

* **Létre kell hozni egy erőforrástípus több példányát. Hogyan létrehozni egy iterációt a saját sablonban?**

  A másolási elem használatával több példányt is megadnia. Használhatja a másolást az [erőforrásokon](copy-resources.md), a [tulajdonságokon](copy-properties.md), a [változókon](copy-variables.md)és a [kimeneteken](copy-outputs.md).

## <a name="template-language"></a>Sablon nyelve

* **Hallottam, hogy az új sablon nyelvén dolgozik. Hol találhatok további információt?**

  További információ az új nyelvről: [Mi az a bicep (előzetes verzió)?](bicep-overview.md).

* **Van olyan terv, amely támogatja a sablonok létrehozását a YAML-ben?**

  Jelenleg nincs terv a YAML támogatására. Hisszük, hogy az új sablon nyelve olyan megoldást kínál, amely könnyebben használható, mint a YAML vagy a JSON.

* **Továbbra is írhatók a JSON-sablonok az új sablon nyelvének megjelenése után?**

  Igen, továbbra is használhatja a JSON-sablonokat.

* **Olyan eszközt is kínál, amely a JSON-sablonokat az új sablon nyelvére konvertálja?**

  Igen. Lásd: [JSON lefordítása a bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="template-specs"></a>Sablonspecifikációk

* **Hogyan szerezhetem be az első lépéseket a sablon specifikációinak előzetes kiadásával?**

  Telepítse a PowerShell vagy az Azure CLI legújabb verzióját. Azure PowerShell esetén használja a [5.0.0 vagy újabb verziót](/powershell/azure/install-az-ps). Azure CLI esetén használja az [2.14.2 vagy újabb verziót](/cli/azure/install-azure-cli).

* **Hogyan kapcsolódnak a sablon specifikációi és az Azure-tervrajzok?**

  Az Azure-tervezetek a sablonhoz tartozó specifikációkat használják a megvalósításában az erőforrás erőforrással való lecserélése után `blueprint definition` `template spec` . Áttelepítési útvonalat biztosítunk a terv definíciójának a sablonra való átalakításához, de a terv-definíciós API-k továbbra is támogatottak lesznek. Az erőforrás nem módosult `blueprint assignment` . A tervrajzok felhasználói élményt nyújtanak a szabályozott környezetek létrehozásához az Azure-ban.

* **A sablonhoz tartozó specifikációk felülírják a csatolt sablonokat?**

  Nem, de a sablon specifikációi úgy vannak kialakítva, hogy jól működjenek a csatolt sablonokkal. A fölérendelt sablon üzembe helyezése előtt nem kell áthelyeznie a csatolt sablont egy nyilvánosan elérhető végpontra. Ehelyett a sablon és a hozzá tartozó összetevők összecsomagolása a sablon specifikációjának létrehozásakor végezhető el.

* **Megoszthatók a sablonhoz tartozó szemüvegek az előfizetések között?**

  Igen, használhatók az előfizetések között, feltéve, hogy a felhasználó rendelkezik olvasási hozzáféréssel a sablonhoz. A sablon specifikációi nem használhatók a bérlők között.

## <a name="scripts-in-templates"></a>Sablonokban található parancsfájlok

* **Használhatok-e parancsfájlokat a sablonban olyan feladatok elvégzéséhez, amelyek nem lehetségesek a sablonban?**

  Igen, használja az [üzembe helyezési parancsfájlokat](deployment-script-template.md). A sablonokban Azure PowerShell vagy Azure CLI-szkripteket is hozzáadhat. A funkció előzetes verzióban érhető el.

* **Használhatom továbbra is az egyéni szkriptek bővítményeit és a kívánt állapot-konfigurációt (DSC)?**

  Ezek a beállítások továbbra is elérhetők, és nem változtak. Az üzembe helyezési parancsfájlok olyan műveletek végrehajtására lettek kialakítva, amelyek nem kapcsolódnak a virtuális gép vendégéhez. Ha parancsfájlt kell futtatnia a gazdagép operációs rendszerén egy virtuális gépen, akkor az egyéni szkriptek bővítménye és/vagy a DSC jobb választás lenne. Az üzembe helyezési parancsfájlok azonban előnyei vannak, például az időtúllépési időtartam beállítása.

* **Támogatottak-e az üzembe helyezési parancsfájlok Azure Government?**

  Igen, az üzembe helyezési szkripteket US Gov Arizona és US Gov Virginia használhatja.

## <a name="preview-changes-before-deployment"></a>Előzetes módosítások az üzembe helyezés előtt

* **Megnézhetem a sablon telepítése előtt megjelenő módosításokat?**

  Igen, használja a [mi-if funkciót](template-deploy-what-if.md). Kiértékeli a környezet aktuális állapotát, és összehasonlítja azt az állapottal, amely az üzembe helyezés után is létezni fog. Megvizsgálhatja az összegzett módosításokat, így meggyőződhet arról, hogy a sablon nem rendelkezik váratlan eredményekkel.

* **Használhatom a mi – if kombinációt a növekményes és a teljes üzemmód esetében is?**

  Igen, mindkét [telepítési mód](deployment-modes.md) támogatott. A növekményes mód használatának példáját lásd: a [mi-if művelet futtatása](template-deploy-what-if.md#run-what-if-operation). A teljes üzemmód használatára vonatkozó példáért lásd: [Törlés megerősítése](template-deploy-what-if.md#confirm-deletion).

* **Mi a teendő, ha társított sablonokkal dolgozik?**

  Igen, mi –, ha kiértékeli a fölérendelt sablon és a csatolt sablonjainak állapotát.

* **Használhatom az Azure-folyamatokat?**

  Igen, használhatja a mi – ha annak ellenőrzését, hogy a folyamat folytatódjon-e.

* **Mi a teendő, ha a nem a saját sablonban található tulajdonságok módosításait látom. A rendszer ezt a "zajt" várta?**

  Dolgozunk a zaj csökkentésén. A GitHub-tárházban található problémák a következő lépések elküldésével segíthetnek a fejlesztésben: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Sablon megjelenítő

* **Van mód arra, hogy megjelenítse az ARM-sablont és az erőforrásait?**

  Van egy [Közösség által biztosított vs Code-bővítményünk](https://aka.ms/ARMVisualizer) , amely nagyszerű feladatot nyújt az ARM-sablon megjelenítéséhez. Megjeleníti az üzembe helyezett erőforrásokat és a köztük lévő kapcsolatokat.

* **Használhatom a VS code-on kívül látható sablont?**

  A sablon megjelenítését a portálon tekintheti meg. További információkért tekintse meg ezt a [rövid munkamenetet a buildből](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Központi telepítés korlátai

* **Hány erőforráscsoportot telepíthetek egyetlen telepítési műveletben?**

  A múltban ez a korlát öt erőforráscsoport volt. A közelmúltban a 800-es erőforráscsoport lett megnövelve. További információ: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

* **Hiba történt az üzembe helyezési előzményekben a 800-es üzemelő példányok esetében. Mit tegyek?**

  Módosítjuk, hogy az erőforráscsoport telepítési előzményei hogyan maradnak karban. A hiba elkerüléséhez a múltban manuálisan kellett törölni a központi telepítéseket ebből az előzményekből. A 2020. júniustól kezdődően a rendszer automatikusan törli az előzményekből származó üzembe helyezéseket a korlát közelében. További információ: [automatikus törlések az üzembe helyezési előzményekből](deployment-history-deletions.md).

  Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

## <a name="templates-and-devops"></a>Sablonok és DevOps

* **Integrálható az ARM-sablonok az Azure-folyamatokba?**

  Igen. A sablon és a folyamatok használatáról az [oktatóanyag: ARM-sablonok folyamatos integrálása az Azure-folyamatokkal](deployment-tutorial-pipeline.md) és az ARM- [sablonok integrálása az Azure-folyamatokkal](add-template-to-azure-pipelines.md)című cikkből tájékozódhat.

* **Használhatok GitHub-műveleteket egy sablon üzembe helyezéséhez?**

  Igen, lásd: [ARM-sablonok telepítése GitHub-műveletek használatával](deploy-github-actions.md).

## <a name="next-steps"></a>Következő lépések

Az ARM-sablonok bemutatása: [Mi az ARM-sablonok?](overview.md).
