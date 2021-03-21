---
title: A AppSource Dynamics 365 pénzügyi és üzemeltetési ajánlatának funkcionális ellenőrzése az Azure Marketplace-en.
description: A Dynamics 365 pénzügyi és üzemeltetési ajánlatának működési ellenőrzése az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613629"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 pénzügyi és üzemeltetési funkciók ellenőrzése

Ha első közzétételt szeretne végrehajtani a [partner Centerben](https://partner.microsoft.com/dashboard/home), a Dynamics 365 pénzügyi és üzemeltetési feladatokhoz két működési érvényesítés szükséges:

- Töltsön fel egy bemutató videót a Dynamics 365-környezetről, amely alapszintű funkciókat mutat be.
- A megoldás [Lifecycle Services](https://lcs.dynamics.com/) -(LCS-) környezetét bemutató képernyőképek.

> [!NOTE]
> A későbbi újraminősítési közzétételekhez nem szükséges bemutató. További információért lásd a [AppSource szabályzat dokumentumát](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Az érvényesítés módja

A funkcionális ellenőrzésnek két lehetősége van:

- Tartsa meg a 30 perces konferenciahívást a csendes-óceáni téli idő (PST) munkaidőn belül, és jegyezze fel az [LCS](https://lcs.dynamics.com/) környezetet és megoldást, vagy
- A partner Centerben tekintse meg a [kereskedelmi piactér](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >  **áttekintését** , és töltse fel a bemutató videó URL-címét és az LCS képernyőképeket az ajánlat kiegészítő tartalmak lapján.

A Microsoft minősítési csapata áttekinti a videót és a fájlokat, majd jóváhagyja a következő lépésekkel kapcsolatos megoldást vagy e-maileket.

### <a name="option-1-30-minute-conference-call"></a>Lehetőség 1:30 perces konferenciahívásra

A végső felülvizsgálati hívások bejelentéséhez vegye fel a kapcsolatot [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) az ajánlat nevével, és néhány lehetséges időpontot a csendes-óceáni idő 8 és 5 óra között.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>2. lehetőség: bemutató videó és LCS képernyőképek feltöltése

1. Rögzítse a videót, és töltse fel a címeket az Ön által választott üzemeltetési webhelyre. Kövesse az alábbi irányelveket:

    - A Microsoft minősítési csapata megtekintheti.
    - Kevesebb, mint 20 perc.
    - A Dynamics 365 környezetben akár három alapvető funkciót is kiemel a megoldásához.

    > [!NOTE]
    > A meglévő marketing-videók használata elfogadható, ha megfelel az irányelveknek.

2. Hajtsa végre a következő képernyőképeket az [LCS](https://lcs.dynamics.com/) -környezetből, amely megfelel a közzétenni kívánt ajánlatnak vagy megoldásnak. Elég Egyértelműnek kell lenniük ahhoz, hogy a minősítési csapat beolvassa a szöveget. Mentse a képernyőképeket JPG-fájlként. Engedélyt biztosíthat az [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) LCS-környezet számára, hogy a képernyőképek megadása helyett a telepítőt is ellenőrizzük.

    1. Nyissa meg a következőt: **LCS**  >  **Business Process Modeling**  >  **Project Library**. Képernyőkép készítése a folyamat lépéseiről. Adja meg a **diagramokat** és az **áttekintett** oszlopokat az itt látható módon:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Megjeleníti a Project Library ablakát.":::

      2. Lépjen az **LCS**  >  **megoldás-felügyeleti**  >  **tesztelési megoldás csomagjába**. Készítsen képernyőképeket, amelyek tartalmazzák az alábbi példákban látható csomag áttekintését és tartalmát:

    | Mező | Kép |
    | --- | --- |
    | Csomag – áttekintés | [![A "csomag áttekintése" ablakot bemutató képernyőkép.](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Megoldás-jóváhagyók</li></ul> | [![Csomag áttekintése képernyő](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Csomag tartalma<ul><li>Modellezés</li><li>Szoftveresen telepíthető csomag</li></ul> | [![Csomag tartalmának képernyője](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>CSOPORTMENTESSÉGi konfiguráció</li><li>Adatbázis biztonsági mentése</li></ul><br>Az összetevők nem szükségesek a **GER konfigurációs** szakaszban. | [![Csomag tartalma két képernyő](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI jelentési modell</li><li>BPM-összetevő</li></ul><br>Az összetevők nem szükségesek a **Power bi** szakaszban. | [![Csomag tartalmának három képernyője](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Adatcsomag feldolgozása</li><li>Megoldási licencszerződés és adatvédelmi szabályzat</li></ul><br>A **csoportmentességi konfiguráció** és a **Power bi jelentési modell** részei nem kötelezőek a pénzügyi és üzemeltetési ajánlatokhoz. | [![Csomag tartalmának négy képernyője](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Az LCS-portál egyes szakaszaival kapcsolatos további tudnivalókért tekintse meg az [LCS felhasználói útmutatót](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Feltöltés a partneri központba.

    1. Hozzon létre egy szöveges dokumentumot, amely tartalmazza a bemutató Videójának címeit és képernyőképeit, vagy mentse a képernyőképeket különálló JPG-fájlként.
    2. Adja hozzá a szöveget és az összes JPG-fájlt egy. zip-fájlhoz a partner Center [kereskedelmi piactéren](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a Pénzügy és az operatív ajánlat **kiegészítő tartalmak** lapján.

    [![A Project Library ablak megjelenítése](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Az ajánlatok létrehozásával kapcsolatos további tudnivalókért lásd: [Dynamics 365 for Operations ajánlat létrehozása](./partner-center-portal/create-new-operations-offer.md).