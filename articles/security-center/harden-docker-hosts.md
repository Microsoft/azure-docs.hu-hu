---
title: Azure Security Center használata a Docker-gazdagépek megkeményedik és a tárolók megóvása érdekében
description: Útmutató – a Docker-gazdagépek megóvása és annak ellenőrzése, hogy megfelelnek-e a CIS Docker-teljesítménytesztnek
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 25d8d3dee8810311ae25f54c0e51e34bdf918c09
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935011"
---
# <a name="harden-your-docker-hosts"></a>A Docker-gazdagépek megerősítése

Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a [Center for Internet Security (CIS) Docker-teljesítményteszttel](https://www.cisecurity.org/benchmark/docker/).

Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. Ha helytelen konfigurációkat talál, Security Center biztonsági javaslatokat hoz létre. A javaslatok **lapon** megtekintheti az ajánlásokat és javíthatja a problémákat. Emellett a **containers (tárolók** ) lapon megjelenő javaslatokat is láthatja, amelyek a Docker-vel üzembe helyezett összes virtuális gépet megjelenítik. 

A biztonsági rések észlelése esetén egyetlen javaslaton belül vannak csoportosítva.

>[!NOTE]
> Ezek a CIS-alapú teljesítményteszt-ellenőrzések nem fognak futni az AK által felügyelt példányokon vagy Databricks által felügyelt virtuális gépeken.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Szükséges szerepkörök és engedélyek:|Az **olvasó** azon a munkaterületen, amelyhez a gazdagép csatlakozik|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Biztonsági rések azonosítása és szervizelése a Docker-konfigurációban

1. A Security Center menüjében nyissa meg a **javaslatok** lapot.

1. A **tároló biztonsági konfigurációinak ajánlott biztonsági réseit** kell szűrnie, és ki kell választania a javaslatot.

    Az ajánlás oldalon az érintett erőforrások (Docker-gazdagépek) láthatók. 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Javaslat a biztonsági rések szervizelésére a tároló biztonsági beállításaiban ":::

1. Ha meg szeretné tekinteni és szervizelni szeretné a CIS-vezérlőket, hogy egy adott gazdagép sikertelen volt, válassza ki a vizsgálni kívánt gazdagépet. 

    > [!TIP]
    > Ha elindította az eszköz leltározása lapon, és elérte ezt a javaslatot, akkor a javaslat oldalon kijelölése a **művelet végrehajtása** gombra.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Log Analytics elindításához tegye a művelet gombot":::

    A Log Analytics egy egyéni művelettel nyílik meg, amely készen áll a futtatásra. Az alapértelmezett egyéni lekérdezés tartalmazza az összes észlelt szabály listáját, valamint útmutatást nyújt a problémák megoldásához.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics lap az összes sikertelen CIS-vezérlőt megjelenítő lekérdezéssel":::

1. Szükség esetén a lekérdezés paramétereinek csípése.

1. Ha biztos abban, hogy a parancs megfelelő, és készen áll a gazdagépre, válassza a **Futtatás**lehetőséget.


## <a name="next-steps"></a>Következő lépések

A Docker-megerősítés csupán egyetlen aspektusa Security Center tároló biztonsági funkcióinak. 

További tudnivalók [a tárolók biztonságáról Security Centerban](container-security.md).