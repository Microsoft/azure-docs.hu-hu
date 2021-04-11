---
title: Microsoft biztonsági kód elemzése – bevezetési útmutató
description: Ismerje meg, hogyan telepítheti és telepítheti a Microsoft biztonsági kód elemzése bővítményt. Lásd az előfeltételek című témakört, és tekintse meg a további erőforrásokat.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c827dc81c1ef1ab03dd6c9178a609b512e59ef15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801214"
---
# <a name="onboarding-and-installing"></a>Előkészítés és telepítés

> [!Note]
> 2022. március 1-től érvényes a Microsoft biztonsági kód elemzése (MSCA) bővítmény kivonásra kerül. A meglévő MSCA-ügyfelek a 2022. március 1-től megőrzik a MSCA való hozzáférésüket. Az Azure DevOps alternatív lehetőségeiért tekintse meg a [OWASP forráskódját elemző eszközöket](https://owasp.org/www-community/Source_Code_Analysis_Tools) . Azon ügyfelek számára, akik a GitHubra kívánnak áttelepítést végezni, megnézheti a [GitHub fokozott biztonságát](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

A Microsoft biztonsági kód elemzésének megkezdéséhez szükséges előfeltételek:

- A következő szakaszban részletezett, jogosult Microsoft Egyesített támogatás-ajánlat.
- Egy Azure DevOps-szervezet.
- Engedélyt ad a bővítmények telepítésére az Azure DevOps-szervezet számára.
- A felhőben üzemeltetett Azure DevOps-folyamattal szinkronizálható forráskód.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód elemzési bővítményének bevezetése

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Érdekli a Microsoft biztonsági kód elemzése bővítmény megvásárlása?

Ha a következő támogatási ajánlatok valamelyikével rendelkezik, forduljon a technikai menedzserhez a meglévő órák megvásárlásához vagy lecserélésekor a bővítményhez való hozzáféréshez:

- Egységes támogatás – speciális szintű
- Egyesített támogatás teljesítményi szintje
- Premier szintű támogatás fejlesztők számára
- Premier szintű támogatás partnerek számára
- Nagyvállalati Premier szintű támogatás

Ha nem rendelkezik a fenti támogatási szerződések valamelyikével, megvásárolhatja a bővítményt az egyik partnertől.

**Következő lépések:**

Ha megfelel a fenti képesítéseknek, a Microsoft biztonsági kód elemzése bővítmény megvásárlásához vegye fel a kapcsolatot az alábbi listában található partnerrel. Ellenkező esetben forduljon a [Microsoft biztonsági kódok elemzésének támogatásához](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Partnerek**

- Zónák – kapcsolattartási adatok: cloudsupport@zones.com
- Wortell – kapcsolattartási adatok: info@wortell.nl
- Logicis – kapcsolattartási adatok: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Legyen Ön is partner

A Microsoft biztonsági kód elemzése csapat a partnereknek szóló szerződéssel rendelkező Premier szintű támogatás keres. A partnerek segítséget nyújtanak az Azure DevOps ügyfeleinek, hogy biztonságosabban fejlesszenek, ha a bővítményt a megvásárolni kívánt ügyfeleknek értékesítik, de nem rendelkeznek nagyvállalati támogatási szerződéssel a Microsofttal. Az érdekelt partnerek regisztrálhatnak [itt](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód elemzése bővítmény telepítése

1. A bővítmény Azure DevOps-szervezettel való megosztása után nyissa meg az Azure DevOps-szervezet lapját. Ilyen oldal például a következő URL-cím: `https://dev.azure.com/contoso` .
1. Válassza a bevásárló táska ikont a neve melletti jobb felső sarokban, majd válassza a **Bővítmények kezelése** lehetőséget.
1. Válassza a **megosztott** lehetőséget.
1. Válassza ki a Microsoft biztonsági kód elemzése bővítményt, majd válassza a **telepítés** lehetőséget.
1. A legördülő listában válassza ki az Azure DevOps-szervezetet a bővítmény telepítéséhez.
1. Válassza a **Telepítés** gombot. A telepítés befejezése után elkezdheti használni a bővítményt.

>[!NOTE]
> Még ha nincs hozzáférése a bővítmény telepítéséhez, folytassa a telepítési lépésekkel. A telepítési folyamat során az Azure DevOps-szervezet rendszergazdájától kérhet hozzáférést.

A bővítmény telepítése után a biztonságos fejlesztési felépítési feladatok láthatók és elérhetők az Azure-folyamatokhoz való hozzáadáshoz.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Konkrét felépítési feladatok hozzáadása az Azure DevOps-folyamathoz

1. Az Azure DevOps-szervezetből nyissa meg a csapat projektjét.
1. Válassza a **folyamatok**  >  **buildek** lehetőséget.
1. Válassza ki azt a folyamatot, amelyben hozzá szeretné adni a bővítmény felépítési feladatait:
   - Új folyamat: válassza az **új** lehetőséget, majd az új folyamat létrehozásához kövesse a részletes lépéseket.
   - Folyamat szerkesztése: válasszon ki egy meglévő folyamatot, majd válassza a **Szerkesztés** lehetőséget a folyamat szerkesztésének megkezdéséhez.
1. Válassza ki **+** , majd lépjen a **feladatok hozzáadása** ablaktáblára.
1. A listából vagy a keresőmező használatával keresse meg a felvenni kívánt felépítési feladatot. Válassza a **Hozzáadás** lehetőséget.
1. Határozza meg a feladathoz szükséges paramétereket.
1. Új Build várólistára helyezése.
   >[!NOTE]
   >A fájlok és mappák elérési útjai a forrás tárház gyökeréhez képest relatívak. Ha paraméterként adja meg a kimeneti fájlokat és mappákat, azokat a rendszer a Build ügynökön definiált közös helyre cseréli.

> [!TIP]
>
> - Ha a Build után szeretne elemzést futtatni, helyezze el a Microsoft biztonsági kód elemzése felépítési feladatokat a Build-összeállítási összetevők közzététele lépés után. Így a Build befejezhető, és a statikus elemzési eszközök futtatása előtt is közzéteheti az eredményeket.
> - A biztonságos fejlesztési felépítési feladatokhoz mindig válassza **a folytatás hiba** esetén lehetőséget. Még ha egy eszköz meghibásodik, a többi futtatható. Nincsenek összefüggőségek az eszközök között.
> - A Microsoft biztonsági kód elemzésekor felépítési feladatok csak akkor működnek, ha egy eszköz nem tud sikeresen futni. De akkor is sikeresek lesznek, ha az eszköz hibákat azonosít a kódban. Az elemzés utáni Build feladattal beállíthatja, hogy a Build működése meghiúsuljon, ha egy eszköz hibákat azonosít a kódban.
> - Egyes Azure DevOps-Build feladatok nem támogatottak, ha kiadási folyamaton keresztül futnak. Konkrétabban az Azure DevOps nem támogatja azokat a feladatokat, amelyek egy kiadási folyamaton belül teszik közzé az összetevőket.
> - Az Azure DevOps Team buildben paraméterként megadható előre definiált változók listáját az [Azure DevOps Build változói](/azure/devops/pipelines/build/variables?tabs=batch)című témakörben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

A Build-feladatok konfigurálásával kapcsolatos további információkért tekintse meg a [konfigurációs útmutatót](security-code-analysis-customize.md) vagy a [YAML konfigurációs útmutatóját](yaml-configuration.md).

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).