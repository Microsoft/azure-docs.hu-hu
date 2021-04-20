---
title: A jogszabályi megfelelési irányítópult használata a Azure Security Center
description: Megtudhatja, hogyan adhat hozzá és távolíthat el szabályozási szabványokat a megfelelőségi irányítópulton a Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: dab6b504fe026324251b7284fc3abdb52bb9911a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738953"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>A szabványok halmazának testreszabása a jogszabályi megfelelési irányítópulton

Azure Security Center az erőforrások konfigurációját az iparági szabványok, szabályozások és teljesítménytesztek követelményeivel. A **jogszabályi megfelelőségi irányítópult** a megfelelőségi követelményeknek való megfelelés alapján nyújt betekintést a megfelelőségi követelményeknek.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hogyan mutatja a jogszabályi megfelelőségi szabványokat a Security Center?

Az iparági szabványokat, szabályozási szabványokat és teljesítményteszteket a Security Center megfelelőségi irányítópultja képviseli. Minden szabvány egy kezdeményezés, amely a Azure Policy.

Ha az irányítópulton értékelésként leképezett megfelelőségi adatokat szeretne látni, adjon hozzá egy megfelelőségi szabványt a felügyeleti csoporthoz vagy előfizetéshez a Biztonsági **szabályzat lapon.** A biztonsági szabályzatokkal és Azure Policy kapcsolatos további információkért lásd: [Working with security policies (A biztonsági szabályzatok munkavégzése).](tutorial-security-policy.md)

Ha hozzárendelt egy standard vagy teljesítménytesztet a kiválasztott hatókörhöz, a szabvány megjelenik a jogszabályi megfelelőségi irányítópulton, és az összes kapcsolódó megfelelőségi adat értékelésként van leképezve. Az összesítő jelentéseket a hozzárendelt szabványok bármelyikéhez letöltheti.

A Microsoft maga követi nyomon a szabályozási szabványokat, és bizonyos csomagokban idővel automatikusan javítja lefedettségét. Amikor a Microsoft új tartalmat ad ki a kezdeményezéshez, az automatikusan megjelenik az irányítópulton, ahogy új szabályzatok vannak leképezve a vezérlőkre a szabványban.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Milyen szabályozási megfelelőségi szabványok érhetők el a Security Center?

Alapértelmezés szerint minden előfizetéshez hozzá van rendelve az **Azure biztonsági teljesítményteszt.** Ez a Microsoft által írt, azure-specifikus irányelvek a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](https://docs.microsoft.com/security/benchmark/azure/introduction).

A következő szabványokat is hozzáadhatja:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- Uk Official és uk NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Amint elérhetővé válnak, a szabványokat a szolgáltatás hozzáadja az irányítópulthoz.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Szabályozási szabvány hozzáadása az irányítópulthoz

Az alábbi lépések azt ismertetik, hogyan adhat hozzá egy csomagot, amely az egyik támogatott szabályozási szabványnak való megfelelést figyeli.

> [!NOTE]
> Ha szabványokat szeretne hozzáadni az irányítópulthoz, az előfizetésnek engedélyeznie kell Azure Defender használatát. Emellett csak a tulajdonos vagy a szabályzat közreműködője rendelkezik a megfelelőségi szabványok hozzáadásához szükséges engedélyekkel. 

1. A Security Center oldalsávján válassza a **Jogszabályi** megfelelőség lehetőséget a jogszabályi megfelelési irányítópult megnyitásához. Itt láthatja az aktuálisan kiválasztott előfizetéshez rendelt megfelelőségi szabványokat.   

1. A lap tetején válassza a **Megfelelőségi szabályzatok kezelése lehetőséget.** Megjelenik a Házirendkezelés lap.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a jogszabályi megfelelési környezetét kezelni szeretné. 

    > [!TIP]
    > Javasoljuk, hogy a megfelelőségi adatok összesítése és nyomon követése érdekében válassza ki azt a hatókört, amelyre a standard vonatkozik. 

1. A szervezet számára releváns szabványok hozzáadásához kattintson a **További szabványok hozzáadása elemre.** 

1. A Jogszabályi **megfelelőségi szabványok hozzáadása lapon** bármely elérhető szabványt megkeresheti, beleértve a következőket:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO és az Egyesült Királyság Nemzeti NemzetiHS-e**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Szabályozási szabványok hozzáadása Azure Security Center megfelelőségi irányítópultjához](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Válassza **a Hozzáadás** lehetőséget, és adja meg az adott kezdeményezéshez szükséges összes adatot, például a hatókört, a paramétereket és a szervizelést.

1. A Security Center oldalsávján válassza  ismét a Jogszabályi megfelelőség lehetőséget, hogy visszatérjön a jogszabályi megfelelési irányítópultra.

    Az új szabvány megjelenik az iparági szabályozási & listájában. 

    > [!NOTE]
    > Az újonnan hozzáadott szabvány megjelenése a megfelelőségi irányítópulton több órát is igénybe vehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Jogszabályi megfelelési irányítópult" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Szabvány eltávolítása az irányítópultról

Ha a megadott szabályozási szabványok bármelyike nem releváns a szervezet számára, akkor egyszerű eljárás eltávolítani őket a felhasználói felületről. Ez lehetővé teszi a jogszabályi megfelelési irányítópult további testreszabását, és csak az Ön számára érvényes szabványokra összpontosíthat.

A standardok eltávolítása:

1. A Security Center menüjében válassza a Biztonsági **szabályzat lehetőséget.**

1. Válassza ki azt az előfizetést, amelyből el szeretné távolítani a szabványt.

    > [!NOTE]
    > A standardokat eltávolíthatja az előfizetésből, de a felügyeleti csoportból nem. 

    Megnyílik a biztonsági szabályzat lapja. A kiválasztott előfizetéshez az alapértelmezett szabályzatot, az iparági és szabályozási szabványokat, valamint az Ön által létrehozott egyéni kezdeményezéseket jeleníti meg.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Szabályozási szabvány eltávolítása a jogszabályi megfelelési irányítópultról a Azure Security Center":::

1. Az eltávolítani kívánt szabványhoz válassza a **Letiltás lehetőséget.** Megjelenik egy megerősítési ablak.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Erősítse meg, hogy valóban el szeretné távolítani a kiválasztott szabályozási szabványt":::

1. Válassza az **Igen** lehetőséget. A rendszer eltávolítja a szabványt. 


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá megfelelőségi **szabványokat** a jogszabályi és az iparági szabványoknak való megfelelés figyelése érdekében.

Kapcsolódó anyagokért tekintse meg a következő oldalakat:

- [Azure-biztonsági teljesítményteszt](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [A Security Center jogszabályi megfelelési irányítópultja](security-center-compliance-dashboard.md) – Útmutató a jogszabályi megfelelőségi adatok nyomon követéséhez és exportálásához Security Center és külső eszközökkel
- [Biztonsági szabályzatok használata](tutorial-security-policy.md)