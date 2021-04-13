---
title: A felügyelt biztonsági szolgáltató (MSSPs) szellemi tulajdonának védelme az Azure Sentinelben | Microsoft Docs
description: Ismerje meg, hogy a felügyelt biztonsági szolgáltatók (MSSPs-EK) hogyan biztosíthatják az Azure Sentinelben létrehozott szellemi tulajdont.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315534"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>A MSSP szellemi tulajdon védelme az Azure Sentinelben

Ez a cikk azokat a módszereket ismerteti, amelyeket a felügyelt biztonsági szolgáltatók (MSSPs-EK) használhatnak az Azure Sentinelben fejlesztett szellemi tulajdon védelméhez, mint például az Azure Sentinel Analytics-szabályok, a vadászati lekérdezések, a forgatókönyvek és a munkafüzetek.

A választott módszer attól függ, hogy az ügyfelek hogyan vásárolhatják meg az Azure-t; függetlenül attól, hogy [felhőalapú megoldás-szolgáltatóként működik-e (CSP)](#cloud-solutions-providers-csp), vagy az ügyfél [nagyvállalati szerződés (EA)/Pay-as-you-go (TB)](#enterprise-agreements-ea--pay-as-you-go-payg) fiókja van. Az alábbi szakaszok ezeket a módszereket külön részletezik.

## <a name="cloud-solutions-providers-csp"></a>Cloud Solutions-szolgáltatók (CSP)

Ha az Azure-t felhőalapú megoldás-szolgáltatóként (CSP) szeretné értékesíteni, az ügyfél Azure-előfizetését kezelheti. A [rendszergazda nevében (AOBO)](/partner-center/azure-plan-manage)a MSSP-bérlő felügyeleti ügynökök csoportjában lévő felhasználók tulajdonosi hozzáféréssel rendelkeznek az ügyfél Azure-előfizetéséhez, és az ügyfél alapértelmezés szerint nem fér hozzá.

Ha a MSSP-bérlőn kívül más felhasználók is hozzáférhetnek az ügyfél-környezethez, azt javasoljuk, hogy az [Azure Lighthouse](multiple-tenants-service-providers.md)-t használja. Az Azure Lighthouse lehetővé teszi, hogy a felhasználók vagy csoportok számára hozzáférést biztosítson egy adott hatókörhöz (például egy erőforráscsoporthoz vagy előfizetéshez) egy beépített szerepkör használatával.

Ha az Azure-környezethez hozzáférést kell biztosítania az ügyfelek számára, javasoljuk, hogy a teljes előfizetés helyett az *erőforráscsoport* szintjén biztosítson hozzáférést számukra, így a környezet részeit igény szerint megjelenítheti vagy elrejtheti.

Például:

- Megadhatja, hogy az ügyfél hozzáférjen több olyan erőforráscsoporthoz, ahol az alkalmazásai találhatók, de továbbra is megtarthatja az Azure Sentinel-munkaterületet egy külön erőforráscsoporthoz, ahol az ügyfélnek nincs hozzáférése.

- Ezzel a módszerrel engedélyezheti, hogy az ügyfelek megtekinthessék a kiválasztott munkafüzeteket és forgatókönyveket, amelyek különálló erőforrások, amelyek a saját erőforráscsoporthoz is lehetnek.

Még ha az erőforráscsoport szintjén is biztosít hozzáférést, az ügyfelek továbbra is hozzáférhetnek az elérhető erőforrásokhoz, például a virtuális gépek naplófájljaihoz, még az Azure Sentinel-hozzáférés nélkül is. További információ: az [Azure Sentinel-adatokhoz való hozzáférés kezelése erőforrás alapján](resource-context-rbac.md).

> [!TIP]
> Ha meg kell adnia ügyfeleinek a teljes előfizetéshez való hozzáférést, érdemes megtekinteni a [nagyvállalati szerződésekben (EA)/](#enterprise-agreements-ea--pay-as-you-go-payg)utólagos elszámolású (TB) című témakör útmutatását.
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Példa az Azure Sentinel CSP architektúrára

Az alábbi ábrán azt mutatjuk be, hogy az [előző szakaszban](#cloud-solutions-providers-csp) leírt engedélyek hogyan működhetnek a CSP-ügyfelek hozzáférésének biztosításakor:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Az Azure Sentinel szellemi tulajdonának védelme a CSP-ügyfelekkel.":::

Ezen a képen:

- A CSP-előfizetéshez **tulajdonosi** hozzáféréssel rendelkező felhasználók a MSSP Azure ad-bérlő felhasználói a felügyeleti ügynökök csoportjában.
- A MSSP származó egyéb csoportok az Azure Lighthouse használatával férhetnek hozzá az ügyfél-környezethez.
- Az Azure-erőforrásokhoz való felhasználói hozzáférést az Azure RBAC az erőforráscsoport szintjén kezelheti.

    Ez lehetővé teszi, hogy a MSSPs szükség szerint elrejtse az Azure Sentinel-összetevőket, például az elemzési szabályokat és a vadászati lekérdezéseket.

További információt az [Azure Lighthouse dokumentációjában](/azure/lighthouse/concepts/cloud-solution-provider)talál.

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Nagyvállalati szerződések (EA)/utólagos elszámolású (TB)

Ha az ügyfél közvetlenül a Microsofttól vásárol, az ügyfélnek már teljes hozzáférése van az Azure-környezethez, és nem rejtheti el az ügyfél Azure-előfizetésében található bármit.

Ehelyett az Azure Sentinelben fejlesztett szellemi tulajdon védelme az alábbiak szerint történik, a védelemhez szükséges erőforrás típusától függően:

### <a name="analytics-rules-and-hunting-queries"></a>Elemzési szabályok és vadászati lekérdezések

Az elemzési szabályok és a vadászati lekérdezések egyaránt az Azure Sentinelben találhatók, ezért nem választhatók el az Azure Sentinel munkaterületről.

Még akkor is megtekintheti a lekérdezést, ha egy felhasználó csak Azure Sentinel Reader-engedélyekkel rendelkezik. Ebben az esetben javasoljuk, hogy az ügyfél bérlője helyett a saját MSSP-bérlője saját elemzési szabályait és a vadászati lekérdezéseket is használja.

Ehhez szüksége lesz egy, a saját bérlőn lévő munkaterületre, amelyen engedélyezve van az Azure Sentinel, és az [Azure Lighthouse](multiple-tenants-service-providers.md)használatával is látnia kell az ügyfél munkaterületét.

Analitikus szabály vagy vadászati lekérdezés létrehozásához az MSSP-bérlőben, amely az ügyfél bérlőn lévő adatokra hivatkozik, az utasítást a következőképpen kell használnia `workspace` :

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Amikor utasítást ad hozzá `workspace` az elemzési szabályokhoz, vegye figyelembe a következőket:

- **Nincsenek riasztások az ügyfél munkaterületen**. Az ily módon létrehozott szabályok nem hoznak létre riasztásokat vagy incidenseket az ügyfél munkaterületen. Mind a riasztások, mind az incidensek csak a MSSP-munkaterületen jelennek meg.

- **Hozzon létre külön riasztásokat az egyes ügyfelek számára**. Ha ezt a módszert használja, javasoljuk, hogy külön riasztási szabályokat használjon minden ügyfél és észlelés esetében, mivel a munkaterület-utasítás minden esetben eltérő lesz.

    Az ügyfél nevét hozzáadhatja a riasztási szabály nevéhez, így egyszerűen azonosíthatja azt az ügyfelet, amelyben a riasztás aktiválva van. A különböző riasztások nagy mennyiségű szabályt eredményezhetnek, amelyeket érdemes lehet a parancsfájlok használatával vagy az [Azure Sentinel kódként](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)kezelni.

    Például:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Hozzon létre külön szabályokat a MSSP-munkaterületen az egyes ügyfelek számára.":::

- **Hozzon létre külön MSSP-munkaterületet az egyes ügyfelek számára**. Az ügyfelek és az észlelés külön szabályainak létrehozásakor a munkaterület elemzési szabályainak maximális száma is elérhető (512). Ha sok ügyfelünk van, és várhatóan eléri ezt a korlátot, érdemes lehet külön MSSP-munkaterületet létrehozni az egyes ügyfelek számára.

    Például:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Hozzon létre egy munkaterületet és szabályokat az MSSP-bérlőben az egyes ügyfelek számára.":::

> [!IMPORTANT]
> A metódus sikeres használatának kulcsa az Automation használata a különböző szabályok nagy készletének kezeléséhez a munkaterületeken.
>
> További információ: [Cross-Workspace Analytics-szabályok](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Munkafüzetek

Ha olyan Azure Sentinel-munkafüzetet fejlesztett ki, amelyről nem kívánja másolni az ügyfelet, akkor a MSSP-bérlőben tárolja a munkafüzetet. Győződjön meg arról, hogy az Azure Világítótoronyon keresztül fér hozzá az ügyfél-munkaterületekhez, majd módosítsa a munkafüzetet az ügyfelek munkaterületek használatára.

Például:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Több munkaterületet tartalmazó munkafüzetek":::

További információ: több [munkaterületet tartalmazó munkafüzetek](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Ha azt szeretné, hogy az ügyfél megtekinthesse a munkafüzet vizualizációit, miközben továbbra is megtartja a kód titkos kódját, javasoljuk, hogy a munkafüzetet Power BIba exportálja.

A munkafüzet exportálása Power BIba:

- **Megkönnyíti a munkafüzet vizualizációinak megosztását**. Az ügyfél a Power BI irányítópultra mutató hivatkozást is küldhet, ahol megtekintheti a jelentett adatmegjelenítést anélkül, hogy Azure-hozzáférési engedélyeket kellene megadnia.
- Az **Ütemezés engedélyezése**. Konfigurálja Power BI úgy, hogy rendszeres időközönként küldjön e-maileket, amelyek az irányítópult pillanatképét tartalmazzák.

További információ: [Azure monitor naplózási adatok importálása a Power BIba](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Forgatókönyvek

A forgatókönyvek a következőképpen védhetők, attól függően, hogy a forgatókönyv milyen analitikai szabályokkal lett létrehozva:

- **Az MSSP munkaterületen létrehozott elemzési szabályok**.  Győződjön meg arról, hogy létrehozta a forgatókönyveit a MSSP-bérlőben, és hogy megkapja az incidensek és a riasztások adatait a MSSP munkaterületen. A forgatókönyveket csatolhatja, amikor új szabályt hoz létre a munkaterületen.

    Például:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="A MSSP munkaterületen létrehozott szabályok.":::

- **Az ügyfél munkaterületen létrehozott elemzési szabályok**. Az Azure Lighthouse használatával a MSSP-munkaterületen üzemeltetett forgatókönyvek alapján csatolhat elemzési szabályokat az ügyfél munkaterületéről. Ebben az esetben a forgatókönyv a riasztást és az incidens adatait, valamint a többi ügyfél-információt az ügyfél munkaterületéről kapja meg.

    Például:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Az ügyfél munkaterületen létrehozott szabályok.":::

Mindkét esetben, ha a forgatókönyvnek hozzá kell férnie az ügyfél Azure-környezetéhez, használjon olyan felhasználói vagy egyszerű szolgáltatásnevet, amely a Világítótoronyon keresztül fér hozzá.

Ha azonban a forgatókönyvnek el kell érnie az ügyfél bérlője nem Azure-beli erőforrásait, például az Azure AD, az Office 365 vagy Microsoft 365 Defender szolgáltatást, létre kell hoznia egy egyszerű szolgáltatásnevet a megfelelő engedélyekkel az ügyfél bérlője számára, majd hozzá kell adnia ezt az azonosítót a forgatókönyvhöz.

> [!NOTE]
> Az automatizálási szabályok és a forgatókönyvek együttes használata esetén be kell állítania az Automation-szabály engedélyeit arra az erőforrás-csoportra, amelyben a forgatókönyvek élőben vannak.
> További információ: az [automatizálási szabályok futtatásához szükséges engedélyek](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Következő lépések

További információkért lásd:

- [Azure Sentinel – technikai forgatókönyv a MSSPs-hez](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Több bérlő kezelése az Azure Sentinelben MSSP](multiple-tenants-service-providers.md)
- [Az Azure Sentinel kiterjesztése munkaterületek és bérlők között](extend-sentinel-across-workspaces-tenants.md)
- [Oktatóanyag: Az adatok megjelenítése és figyelése](tutorial-monitor-your-data.md)
- [Oktatóanyag: automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](tutorial-respond-threats-playbook.md)
