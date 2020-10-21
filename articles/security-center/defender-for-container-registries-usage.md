---
title: Az Azure Defender használata a Container-nyilvántartásokhoz
description: Tudnivalók az Azure Defender for Container-beállításjegyzékek használatáról a beállításjegyzékben található rendszerképek vizsgálatához
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 57e8b6f47c4166c4f8b9f5de0f3e03a7d757e100
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342076"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>A biztonsági rések vizsgálatához használja az Azure Defender for Container nyilvántartót

Ez az oldal azt ismerteti, hogyan használható a beépített sebezhetőségi ellenőrzőeszköz a Azure Resource Manager-alapú Azure Container Registry tárolt tároló-lemezképek vizsgálatához.

Ha engedélyezve van az **Azure Defender a tároló** -beállításjegyzékek számára, a rendszer azonnal megvizsgálja a beállításjegyzékbe beküldött képeket. Emellett a rendszer az utolsó 30 napban lekért képeket is ellenőrzi. 

Ha a képolvasó Security Center biztonsági réseket, Security Center a megállapításokat és a kapcsolódó információkat a javaslatok alapján mutatja be. Emellett a megállapítások olyan kapcsolódó információkat is tartalmaznak, mint például a Szervizelési lépések, a kapcsolódó CVEs, CVSS pontszámok és egyebek. Megtekintheti egy vagy több előfizetéshez tartozó azonosított biztonsági réseket vagy egy adott beállításjegyzéket.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Az **Azure Defender for Container** -beállításjegyzékek számlázása [a díjszabási oldalon](security-center-pricing.md) látható.|
|Támogatott nyilvántartások és lemezképek:|![Igen, ](./media/icons/yes-icon.png) a nyilvános internetről elérhető és a rendszerhéj elérését biztosító, a Linux által üzemeltetett ACR-nyilvántartások.<br>![Nem található a ](./media/icons/no-icon.png) Windows által üzemeltetett ACR-jegyzék.<br>![Nincsenek ](./media/icons/no-icon.png) "privát" beállításjegyzékek – Security Center megköveteli, hogy a kibocsátásiegység-forgalmi jegyzékek elérhetők legyenek a nyilvános internetről. Security Center jelenleg nem tud csatlakozni a szolgáltatáshoz, vagy beolvasni azokat a beállításjegyzékeket, amelyekhez hozzáférés van korlátozva tűzfallal, szolgáltatási végponttal vagy privát végpontokkal, például az Azure Private-hivatkozással.<br>![Nincsenek olyan ](./media/icons/no-icon.png) Super minimalista képek, mint például a [Docker](https://hub.docker.com/_/scratch/) -lemezképek, vagy a "eltérítetlen" lemezképek, amelyek csak egy alkalmazást és annak futásidejű függőségeit tartalmazzák csomagkezelő, rendszerhéj vagy operációs rendszer nélkül.|
|Szükséges szerepkörök és engedélyek:|**Biztonsági olvasó** és [Azure Container Registry olvasó szerepkör](../container-registry/container-registry-roles.md)|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Biztonsági rések azonosítása az Azure Container nyilvántartóban található lemezképekben 

1. A Azure Resource Manager-alapú Azure Container Registry tárolt rendszerképek sebezhetőségi vizsgálatának engedélyezése:

    1. Engedélyezze az **Azure Defender számára** az előfizetéséhez tartozó tároló-beállításjegyzéket.

        A Security Center most már készen áll a beállításjegyzékben található rendszerképek vizsgálatára.

        >[!NOTE]
        > Ez a funkció rendszerkép alapján van felszámítva.

1. A rendszer minden leküldéses vagy importálási képvizsgálatot aktivál, és ha a rendszerkép az elmúlt 30 napban lett kihúzva. 

    Ha a vizsgálat befejeződik (általában körülbelül 2 percet, de akár 15 percet is igénybe vehet), az eredmények Security Center javaslatként érhetők el.

1. [Tekintse meg és javítsa ki a megállapításokat az alább leírtak szerint](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Biztonsági rések azonosítása más tárolókban lévő lemezképekben 

1. Az ACR-eszközök használatával képeket hozhat a beállításjegyzékbe a Docker hub-ból vagy a Microsoft Container Registryból.  Ha az importálás befejeződik, az importált rendszerképeket az Azure Defender ellenőrzi. 

    További információ a [tároló-lemezképek tároló-beállításjegyzékbe való importálásáról](../container-registry/container-registry-import-images.md)

    Ha a vizsgálat befejeződik (általában körülbelül 2 percet, de akár 15 percet is igénybe vehet), az eredmények Security Center javaslatként érhetők el.

1. [Tekintse meg és javítsa ki a megállapításokat az alább leírtak szerint](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Eredmények megtekintése és szervizelése

1. A megállapítások megtekintéséhez nyissa meg a **javaslatok** lapot. Ha problémák merültek fel, akkor a **Azure Container Registry lemezképekben található javaslati biztonsági réseket fel kell javítani**

    ![Javaslatok a problémák megoldásához ](media/monitor-container-security/acr-finding.png)

1. Válassza ki a javaslatot. 

    Megnyílik a javaslat részletei lap, amely további információkat tartalmaz. Ez az információ tartalmazza a sebezhető rendszerképekkel ("érintett erőforrásokkal") és a Szervizelési lépésekkel rendelkező kibocsátásiegység-forgalmi jegyzékek listáját. 

1. Válasszon ki egy adott beállításjegyzéket, és tekintse meg a védett adattárakkal rendelkező adattárakat.

    ![Beállításjegyzék kiválasztása](media/monitor-container-security/acr-finding-select-registry.png)

    Megnyílik a beállításjegyzék részletei lap az érintett adattárak listájával.

1. Válasszon ki egy adott tárházat a sebezhető lemezképekkel rendelkező adattárak megtekintéséhez.

    ![Adattár kiválasztása](media/monitor-container-security/acr-finding-select-repository.png)

    Megnyílik az adattár részletei lap. Felsorolja a sebezhető lemezképeket, valamint az eredmények súlyosságának értékelését.

1. Válasszon ki egy adott képet a biztonsági rések megtekintéséhez.

    ![Képek kiválasztása](media/monitor-container-security/acr-finding-select-image.png)

    Megnyílik a kiválasztott rendszerkép megállapításainak listája.

    ![Megállapítások listája](media/monitor-container-security/acr-findings.png)

1. Ha többet szeretne megtudni a keresésről, válassza a keresés lehetőséget. 

    Megnyílik a megállapítások részletei ablaktábla.

    [![Megállapítások részletei ablaktábla](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Ez a panel a probléma részletes ismertetését és a külső erőforrásokra mutató hivatkozásokat tartalmaz a fenyegetések enyhítése érdekében.

1. Kövesse az ablaktábla szervizelés szakaszának lépéseit.

1. Ha elvégezte a biztonsági probléma megoldásához szükséges lépéseket, cserélje le a rendszerképet a beállításjegyzékbe:

    1. A frissített rendszerkép leküldése. Ez elindítja a vizsgálatot. 
    
    1. Tekintse meg a "biztonsági rések a Azure Container Registry lemezképekben" című részt a javaslatok oldalon. 
    
        Ha a javaslat továbbra is megjelenik, és a kezelt lemezkép továbbra is megjelenik a sebezhető lemezképek listájában, ellenőrizze újra a szervizelés lépéseit.

    1. Ha biztos abban, hogy a frissített rendszerkép le lett küldve, a vizsgálat megtörtént, és már nem jelenik meg a javaslatban, törölje a "régi" sebezhető rendszerképet a beállításjegyzékből.


## <a name="disable-specific-findings-preview"></a>Meghatározott eredmények letiltása (előzetes verzió)

Ha a szervezetnek figyelmen kívül kell hagynia egy megállapítást, és nem javítja azt, akkor letilthatja. A letiltott eredmények nem befolyásolják a biztonságos pontszámot, vagy nem eredményeznek nemkívánatos zajt.

Ha a keresés megfelel a letiltási szabályokban definiált feltételeknek, az nem jelenik meg az eredmények listájában. A tipikus forgatókönyvek a következők:

- Az eredmények súlyosságának letiltása közepesnél
- Nem javítható megállapítások letiltása
- A megállapítások letiltása a CVSS pontszám 6,5 alatt
- Meghatározott szöveggel rendelkező megállapítások letiltása a biztonsági ellenőrzés vagy a kategória alapján (például "RedHat", "CentOS biztonsági frissítés a sudo számára")

> [!IMPORTANT]
> Szabály létrehozásához engedélyekkel kell rendelkeznie a szabályzat szerkesztéséhez Azure Policy.
>
> További információ az [Azure RBAC engedélyeiről Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

A következő feltételek bármelyike használható: 

- AZONOSÍTÓ keresése 
- Kategória
- Biztonsági vizsgálat 
- CVSS v3 pontszámok
- Súlyosság 
- Javítható állapot 

Szabály létrehozása:

1. A **Azure Container Registry lemezképekben található biztonsági rések**részletes ajánlásai oldalon válassza a **szabály letiltása**lehetőséget.
1. Válassza ki a megfelelő hatókört.
1. Adja meg a feltételeket.
1. Válassza a **szabály alkalmazása**lehetőséget.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Letiltási szabály létrehozása a VA-eredményekhez a beállításjegyzékben":::

1. Szabály megtekintése, felülbírálása vagy törlése: 
    1. Válassza a **szabály letiltása**lehetőséget.
    1. A hatókör listából az aktív szabályokkal rendelkező előfizetések **szabályként**jelennek meg.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Letiltási szabály létrehozása a VA-eredményekhez a beállításjegyzékben":::
    1. A szabály megtekintéséhez vagy törléséhez válassza a három pontot ("...").


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure Defenderről](azure-defender.md)