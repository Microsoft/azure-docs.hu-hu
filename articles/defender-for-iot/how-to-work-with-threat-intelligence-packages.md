---
title: Fenyegetési intelligencia-adatok frissítése
description: A veszélyforrások felderítésére szolgáló adatcsomag minden új Defender számára elérhető a IoT-verzióhoz, vagy szükség esetén a kiadások között.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784050"
---
# <a name="threat-intelligence-research-and-packages"></a>Veszélyforrások elleni kutatás és csomagok

A Microsoft biztonsági csoportjai a saját MEGOSZTÁSú fenyegetések felderítésével és a sebezhetőségi kutatással foglalkoznak. Ezek a csapatok többek között a MSTIC (Microsoft Threat Intelligence Center), a DART (Microsoft észlelési és gyorsreagáló csapat), a DCU (digitális bűncselekmények egység) és a 52-es szakasz (IoT/OT/ICS tartományi szakértők, amelyek az IC-specifikus, nulla napos, fordított mérnöki kártevők, kampányok és ellenfelek követésére használhatók).

A csapatok biztonsági észlelést, elemzést és választ adnak a Microsoftnak:

- Felhőalapú infrastruktúra és szolgáltatások.
- Hagyományos termékek és eszközök.
- Belső vállalati erőforrások.

A biztonsági csapatok a következőket vehetik igénybe:

- Az ismert és a kapcsolódó fenyegetések elleni védelem.
- Az osztályozást és rangsorolást segítő megállapítások.
- A fenyegetések teljes kontextusának megértése az érintettek előtt.
- További releváns, pontos és gyakorlatban használható adatokat.

Ez az intelligencia környezetfüggő információkat biztosít a Microsoft Platform Analytics bővítéséhez, és támogatja a vállalat felügyelt szolgáltatásait az incidensek és a szabálysértések kivizsgálására. A veszélyforrások intelligencia csomagjai aláírásokat (beleértve a kártevő-aláírásokat is), a CVEs és más biztonsági tartalmakat tartalmaznak.

A csomagokat letöltheti az Azure Defender IoT portáljának **frissítések** lapjáról.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Töltse le a frissítéseket az Azure Defender for IoT portálon.":::

## <a name="update-threat-intelligence-data"></a>Fenyegetési intelligencia-adatok frissítése

A veszélyforrások felderítésére szolgáló csomagokat minden új Defender IoT-frissítéssel, vagy szükség esetén a kiadások között biztosítjuk.

A Defender for IoT portálról letöltött csomagok manuálisan is feltölthetők az egyes érzékelőkre. Ha a helyszíni felügyeleti konzol az érzékelők felügyeletét végzi, letöltheti a veszélyforrások felderítésére szolgáló csomagokat a felügyeleti konzolra, és egyszerre több érzékelőkre is leküldheti azokat.

Csomag frissítése egyetlen érzékelőn:

1. Nyissa meg az Azure Defender for IoT **Updates** oldalt.

2. Töltse le és mentse a **veszélyforrások felderítésére** szolgáló csomagot.

3. Jelentkezzen be az érzékelő konzolra.

4. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

5. Válassza ki a **veszélyforrások intelligencia-adatok** elemet, majd válassza a **frissítés** lehetőséget.

6. Töltse fel az új csomagot.

Csomag frissítése egyszerre több érzékelőkre:

1. Nyissa meg az Azure Defender for IoT **Updates** oldalt.

2. Töltse le és mentse a **veszélyforrások felderítésére** szolgáló csomagot.

3. Jelentkezzen be a felügyeleti konzolra.

4. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

5. Az **érzékelő motorjának konfigurációja** szakaszban válassza ki azokat az érzékelőket, amelyeknek meg kell kapniuk a frissített csomagokat.  

6. A **veszélyforrások felderítése adatok kiválasztása** szakaszban válassza a pluszjelet ( **+** ).

7. Töltse fel a csomagot.

## <a name="see-also"></a>Lásd még

[Frissítési verziók](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
