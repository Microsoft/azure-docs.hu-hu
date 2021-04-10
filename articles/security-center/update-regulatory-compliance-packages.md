---
title: A szabályozási megfelelőségi irányítópult használata Azure Security Center
description: Megtudhatja, hogyan adhat hozzá és távolíthat el szabályozási szabványokat a megfelelőségi irányítópultról Security Center
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
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381732"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>A szabályzatok megfelelőségi irányítópultján lévő szabványok testreszabása

Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. A megfelelőségi **irányítópult** az adott megfelelőségi követelmények teljesítése alapján nyújt betekintést a megfelelőségi helyzetbe.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hogyan jelennek meg a Security Centerban szereplő szabályozási megfelelőségi szabványok?

Az iparági szabványok, a szabályozási szabványok és a referenciaértékek a Security Center szabályozási megfelelőségi irányítópultján jelennek meg. Mindegyik standard egy Azure Policyban definiált kezdeményezés.

Az irányítópulton kiértékelt megfelelőségi adatok megtekintéséhez a **biztonsági házirend** lapon adja meg a felügyeleti csoport vagy előfizetés megfelelőségi szabványát. Ha többet szeretne megtudni a Azure Policy és a kezdeményezésekről, tekintse meg a [biztonsági szabályzatok használata](tutorial-security-policy.md)című témakört.

Ha standard vagy teljesítménytesztet rendelt hozzá a kiválasztott hatókörhöz, a szabvány a szabályzatoknak megfelelő megfelelőségi irányítópulton jelenik meg, és az összes hozzá tartozó megfelelőségi adat az értékelésként van leképezve. A hozzárendelt szabványok bármelyikére vonatkozó összegző jelentéseket is letöltheti.

A Microsoft saját maga követi nyomon a szabályozási szabványokat, és az egyes csomagokban automatikusan javítja annak lefedettségét. Amikor a Microsoft új tartalmat szabadít fel a kezdeményezéshez, automatikusan megjelenik az irányítópulton, mivel a szabványos vezérlőkhöz hozzárendelt új szabályzatok jelennek meg.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Milyen szabályozási megfelelőségi szabályok érhetők el Security Centerban?

Alapértelmezés szerint minden előfizetéshez hozzá van rendelve az **Azure biztonsági teljesítményteszte** . Ez a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi ajánlott eljárások számára, a közös megfelelőségi keretrendszerek alapján. [További tudnivalók az Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

Emellett olyan szabványokat is hozzáadhat, mint például a:

- NIST SP 800-53 R4
- SWIFT CSP CSCF – v2020
- Egyesült Királyság hivatalos és egyesült királysági NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

A szabványokat a rendszer hozzáadja az irányítópulthoz, amint azok elérhetővé válnak.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Szabályozási szabvány hozzáadása az irányítópulthoz

A következő lépések azt ismertetik, hogyan adhat hozzá egy csomagot a megfelelőség monitorozásához a támogatott szabályozási szabványok egyikével.

> [!NOTE]
> Ha szabványokat szeretne hozzáadni az irányítópulthoz, az előfizetésnek engedélyezve kell lennie az Azure Defender számára. Emellett csak azok a felhasználók férhetnek hozzá a megfelelőségi szabványokhoz, akik tulajdonosi vagy házirend közreműködők. 

1. A Security Center oldalsávján válassza a **jogszabályi megfelelőség** lehetőséget a megfelelőségi irányítópult megnyitásához. Itt láthatja az aktuálisan kijelölt előfizetésekhez jelenleg hozzárendelt megfelelőségi szabványokat.   

1. A lap tetején válassza a **megfelelőségi szabályzatok kezelése** lehetőséget. Megjelenik a házirend-kezelés lap.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez felügyelni kívánja a szabályozási megfelelőségi állapotot. 

    > [!TIP]
    > Azt javasoljuk, hogy válassza ki a legmagasabb hatókört, amelyre a standard érvényes, hogy a megfelelőségi adatokat összesítsék és nyomon kövessék az összes beágyazott erőforrás esetében. 

1. A szervezethez kapcsolódó szabványok hozzáadásához kattintson a **további szabványok hozzáadása** lehetőségre. 

1. A **szabályozási megfelelőségi szabványok hozzáadása** lapon bármely elérhető szabványra rákereshet, többek között a következőkre:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO és egyesült királysági NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Szabályozási szabványok hozzáadása a Azure Security Center szabályozási megfelelőségi irányítópulthoz](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Válassza a **Hozzáadás** lehetőséget, és adja meg az adott kezdeményezéshez szükséges összes adatot, például a hatókört, a paramétereket és a szervizelést.

1. A Security Center oldalsávján válassza a **szabályozás megfelelősége** ismét lehetőséget a megfelelőségi irányítópultra való visszatéréshez.

    Az új szabvány megjelenik az iparági & szabályozási szabványainak listájában. 

    > [!NOTE]
    > A megfelelőségi irányítópulton néhány órát is igénybe vehet, hogy egy újonnan hozzáadott szabvány megjelenjen.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Szabályozási megfelelőségi irányítópult" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Standard eltávolítása az irányítópultról

Ha a megadott szabályozási szabványok bármelyike nem felel meg a szervezete számára, egy egyszerű folyamat, amellyel eltávolíthatja őket a felhasználói felületről. Ez lehetővé teszi a szabályozási megfelelőségi irányítópult további testreszabását, és csak az Önre vonatkozó szabványokra koncentrálhat.

Standard eltávolítása:

1. A Security Center menüjében válassza a **biztonsági szabályzat** elemet.

1. Válassza ki a megfelelő előfizetést, amelyből el kívánja távolítani a standardot.

    > [!NOTE]
    > A standardokat eltávolíthat egy előfizetésből, de nem felügyeleti csoportból. 

    Megnyílik a biztonsági házirend lap. A kiválasztott előfizetéshez az alapértelmezett házirend, az iparági és a szabályozási szabványok, valamint a létrehozott egyéni kezdeményezések láthatók.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Szabályozási szabvány eltávolítása a szabályozási megfelelőségi irányítópultról Azure Security Center":::

1. Az eltávolítani kívánt szabványhoz válassza a **Letiltás** lehetőséget. Megjelenik egy megerősítő ablak.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Erősítse meg, hogy valóban el kívánja távolítani a kiválasztott szabályozási szabványt":::

1. Válassza az **Igen** lehetőséget. A standard el lesz távolítva. 


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan **adhat hozzá megfelelőségi szabványokat** a szabályozási és iparági szabványoknak való megfelelés monitorozásához.

Kapcsolódó anyagok esetében tekintse meg a következő lapokat:

- [Azure-biztonsági teljesítményteszt](../security/benchmarks/introduction.md)
- A [Security Center szabályozási megfelelőségi irányítópultja](security-center-compliance-dashboard.md) – megtudhatja, hogyan követheti nyomon és exportálhatja a szabályozási megfelelőségi adatait Security Center és külső eszközökkel
- [Biztonsági szabályzatok használata](tutorial-security-policy.md)