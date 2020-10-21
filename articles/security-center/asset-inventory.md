---
title: Azure Security Center leltározása
description: Ismerkedjen meg Azure Security Center Asset Management-felülettel, amely teljes körű láthatóságot biztosít az összes Security Center figyelt erőforráson.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/22/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: d15d73b0f2b87b8e6f66c7bd4e7fb34f6b06e1a0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341923"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel

Azure Security Center eszközök leltározási lapja egyetlen oldalt biztosít a Security Centerhoz csatlakoztatott erőforrások biztonsági állapotának megtekintéséhez. 

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

Ha bármilyen erőforráshoz függőben lévő javaslatok vannak, akkor azok megjelennek a leltárban.

Ezt a nézetet és a hozzá tartozó szűrőket használhatja a következő kérdések megválaszolásához:

- Milyen előfizetések vannak engedélyezve az Azure Defender szolgáltatásban?
- A "Production" címkével rendelkező gépek közül melyiknek hiányzik a Log Analytics ügynöke?
- Az adott címkével rendelkező gépek közül hányon vannak Kiemelt javaslatok?
- Egy adott erőforráscsoport hány erőforrása rendelkezik biztonsági megállapításokkal a sebezhetőségi felmérési szolgáltatástól?

Az eszköz erőforrás-kezelési lehetőségei jelentősek, és folyamatosan növekednek. 

> [!TIP]
> Az eszközök leltározása lapon megjelenő biztonsági javaslatok ugyanazok, mint a **javaslatok** lapon, de itt láthatók az érintett erőforrásnak megfelelően. További információ a javaslatok megoldásáról: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|Minden felhasználó|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Mik az eszközök leltárának főbb jellemzői?

A leltár oldal a következő eszközöket biztosítja:

- **Összegzések** – a szűrők definiálása előtt a leltár nézet felső részén látható értékek egy kiemelt sáv jelenik meg:

    - **Összes erőforrás**: Security Centerhoz csatlakoztatott erőforrások teljes száma.
    - **Sérült erőforrások**: aktív biztonsági javaslatokkal rendelkező erőforrások. [További információ a biztonsági javaslatokról](security-center-recommendations.md).
    - Nem **figyelt erőforrások**: az ügynök figyelésével kapcsolatos problémákkal rendelkező erőforrások – log Analytics ügynök telepítve van, de az ügynök nem küld adatokat, vagy egyéb állapottal kapcsolatos problémákba ütközik.

- **Szűrők** – a lap tetején található több szűrő lehetővé teszi, hogy gyorsan pontosítsa az erőforrások listáját a válaszolni kívánt kérdésnek megfelelően. Ha például a *"Production" címkével rendelkező gépekkel* kapcsolatos kérdést szeretné megválaszolni, akkor a log Analytics ügynök hiányzik? az **ügynök figyelési** szűrőjét a **címkék** szűrővel kombinálhatja a következő klipben látható módon:

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel" kifejezést a nyelvhez. [További információ ebben a KQL-oktatóanyagban](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Eszközkezelés beállításai** – a leltár lehetővé teszi összetett felderítési lekérdezések végrehajtását. Ha megtalálta a lekérdezéseknek megfelelő erőforrásokat, a leltár a következő műveletekhez nyújt parancsikonokat:

    - Címkék kiosztása a szűrt erőforrásokhoz – jelölje be a címkével ellátni kívánt erőforrások melletti jelölőnégyzeteket.
    - Új kiszolgálók előkészítése a Security Centerhoz – használja a **nem Azure-kiszolgálók hozzáadása** eszköztár gombot.
    - Munkaterhelések automatizálása a Azure Logic Apps használatával – a logikai alkalmazás **elindítása** gomb segítségével futtathat egy logikai alkalmazást egy vagy több erőforráson. A logikai alkalmazásokat előre elő kell készíteni, és el kell fogadni a megfelelő trigger típusát (HTTP-kérés). [További információ a Logic apps](../logic-apps/logic-apps-overview.md)szolgáltatásról.


## <a name="how-does-asset-inventory-work"></a>Hogyan működik a tárgyieszköz-leltár?

Az Asset Inventory az [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)szolgáltatást használja, amely lehetővé teszi, hogy a Security Center biztonsági állapotát több előfizetésen keresztül lekérdezze.

Az ARG úgy lett kialakítva, hogy hatékony erőforrás-feltárást biztosítson a nagy léptékű lekérdezési lehetőségekkel.

A [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)használatával az eszközök leltározása gyors elemzéseket KÉSZÍTHET az ASC-adatforrások más erőforrás-tulajdonságokkal való hivatkozásával.


## <a name="how-to-use-asset-inventory"></a>Az eszközök leltározásának használata

1. A Security Center oldalsávján válassza a **leltár**lehetőséget.

1. Egy adott erőforrás megjelenítéséhez használja a **szűrés név alapján** mezőt, vagy használja az alább leírt szűrőket.

1. A szűrőkben válassza ki a megfelelő beállításokat a végrehajtani kívánt lekérdezés létrehozásához.

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel" lightbox="./media/asset-inventory/inventory-filters.png":::

    Alapértelmezés szerint az erőforrásokat az aktív biztonsági javaslatok száma alapján rendezi a rendszer.

    > [!IMPORTANT]
    > Az egyes szűrők beállításai az aktuálisan kijelölt előfizetésekben lévő erőforrásokra **és** a többi szűrőben megadott beállításokra vonatkoznak.
    >
    > Ha például csak egy előfizetést jelölt ki, és az előfizetés nem rendelkezik olyan erőforrásokkal, amelyek nem rendelkeznek magas biztonsági javaslatokkal a szervizeléshez (0 sérült erőforrás), a **javaslatok** szűrője nem lesz elérhető. 

1. A **biztonsági eredmények** szűrővel való használatához adja meg az érintett erőforrásokhoz szűrni kívánt biztonsági rés azonosítójának, biztonsági vizsgálatának vagy CVE-nevének a szabad szövegét:

    !["Biztonsági eredmények" szűrő](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > A **biztonsági megállapítások** és a **címkék** szűrők csak egyetlen értéket fogadnak el. Ha egynél többre szeretne szűrni, használja a **szűrők hozzáadása**lehetőséget.

1. Az **Azure Defender** -szűrő használatához válasszon ki egy vagy több beállítást (off, on vagy részleges):

    - Nem az Azure **Defender-csomag** által védett erőforrások. Kattintson a jobb gombbal bármelyikre, és frissítse őket:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - Azure **Defender-csomag által védett** erőforrások
    - **Részleges** – ez vonatkozik azokra az **előfizetésekre** , amelyek esetében nem mindegyik Azure Defender-csomag le van tiltva. A következő előfizetésben például öt Azure Defender-csomag van letiltva. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel":::

1. A lekérdezés eredményeinek további vizsgálatához válassza ki az Önt érdeklő erőforrásokat.

1. Ha az aktuálisan kiválasztott szűrőbeállításokat lekérdezésként szeretné megtekinteni az erőforrás-diagram Explorerben, válassza a nézet lehetőséget a **Resource Graph Explorerben**.

    ![Leltár lekérdezése az ARG-ben](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Korábban definiált logikai alkalmazás futtatása a következővel 

1. Ha meghatározta a szűrőket, és megnyitotta a lapot, Security Center nem frissíti automatikusan az eredményeket. Az erőforrások módosításai nem befolyásolják a megjelenített eredményeket, hacsak nem tölti be manuálisan a lapot, vagy a **frissítés**lehetőséget választja.


## <a name="faq---inventory"></a>Gyakori kérdések – leltár

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Miért nem jelenik meg az összes előfizetésem, gép, Storage-fiók stb.?

A leltár nézet a Felhőbeli biztonsági állapot-felügyeleti (CSPM) perspektívából listázza a Security Center csatlakoztatott erőforrásait. A szűrők nem adják vissza a környezet minden erőforrását; csak a kiemelkedő (vagy "aktív") javaslatok közül. 

Az alábbi képernyőfelvétel például egy, a 38-es előfizetéshez hozzáféréssel rendelkező felhasználót mutat be, de csak 10 jelenleg rendelkezik javaslatokkal. Tehát amikor az **Erőforrás típusa = előfizetések**alapján szűr, csak az aktív ajánlásokkal rendelkező 10 előfizetés jelenik meg a leltárban:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Miért mutatnak be néhány erőforrás üres értéket az Azure Defender vagy az ügynök figyelési oszlopaiban?

Nem minden Security Center figyelt erőforrás rendelkezik ügynökkel. Ilyenek például az Azure Storage-fiókok vagy a Pásti-erőforrások, például lemezek, Logic Apps, Data Lake elemzések és az Event hub.

Ha a díjszabás vagy az ügynök figyelése nem vonatkozik egy erőforrásra, a leltárban semmi sem jelenik meg.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="A nem figyelt éles erőforrásokra való szűrés&quot;:::

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a &quot;Feel":::

## <a name="next-steps"></a>Következő lépések

Ez a cikk a Azure Security Center eszköz leltározási lapját ismerteti.

A kapcsolódó eszközökről a következő lapokon talál további információt:

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)