---
title: A jogszabályi megfelelési irányítópult használata a Azure Security Center
description: Megtudhatja, hogyan adhat hozzá és távolíthat el szabályozási szabványokat a megfelelőségi irányítópulton a Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 4ba65517b698896c57ca2254394efcadd6efbb1d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835037"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>A szabványok halmazának testreszabása a jogszabályi megfelelési irányítópulton

Azure Security Center az erőforrások konfigurációját az iparági szabványok, szabályozások és teljesítménytesztek követelményeivel. A **jogszabályi megfelelőségi irányítópult** a megfelelőségi követelményeknek való megfelelés alapján nyújt betekintést a megfelelőségi követelményeknek.

> [!TIP]
> A jogszabályi Security Center irányítópultjáról a gyakori kérdések között [olvashat bővebben.](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hogyan mutatja a jogszabályi megfelelőségi szabványokat a Security Center?

Az iparági szabványokat, szabályozási szabványokat és teljesítményteszteket a Security Center megfelelőségi irányítópultja képviseli. Minden szabvány egy kezdeményezés, amely a Azure Policy.

Ha az irányítópulton értékelésként leképezett megfelelőségi adatokat szeretne látni, adjon hozzá egy megfelelőségi szabványt a felügyeleti csoporthoz vagy előfizetéshez a Biztonsági **szabályzat lapon.** A biztonsági szabályzatokkal és Azure Policy kapcsolatos további információkért lásd: [Working with security policies (A biztonsági szabályzatok munkavégzése).](tutorial-security-policy.md)

Ha hozzárendelt egy standard vagy teljesítménytesztet a kiválasztott hatókörhöz, a szabvány megjelenik a jogszabályi megfelelőségi irányítópulton, és az összes kapcsolódó megfelelőségi adat értékelésként van leképezve. Az összesítő jelentéseket a hozzárendelt szabványok bármelyikéhez letöltheti.

A Microsoft maga követi nyomon a szabályozási szabványokat, és bizonyos csomagokban idővel automatikusan javítja lefedettségét. Amikor a Microsoft új tartalmat ad ki a kezdeményezéshez, az automatikusan megjelenik az irányítópulton, ahogy új szabályzatok vannak leképezve a vezérlőkre a szabványban.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Milyen szabályozási megfelelőségi szabványok érhetők el a Security Center?

Alapértelmezés szerint minden előfizetéshez hozzá van rendelve az **Azure biztonsági teljesítményteszt.** Ez a Microsoft által írt, azure-specifikus irányelvek a biztonsági és megfelelőségi ajánlott eljárásokhoz a közös megfelelőségi keretrendszerek alapján. [További tudnivalók az Azure-biztonsági teljesítménytesztről](https://docs.microsoft.com/security/benchmark/azure/introduction).

Emellett a következő szabványokat is hozzáadhatja:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official és uk NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC 3. szint
- Új-Zélandi ISM korlátozott

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

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO és egyesült királysági NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC 3. szint**
    - **Új-Zéland isM korlátozott**
    
    ![Szabályozási szabványok hozzáadása Azure Security Center megfelelőségi irányítópultjához](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Válassza **a Hozzáadás** lehetőséget, és adja meg az adott kezdeményezéshez szükséges összes részletet, például a hatókört, a paramétereket és a szervizelést.

1. A Security Center oldalsávján válassza  ismét a Jogszabályi megfelelőség lehetőséget, hogy visszatérjön a jogszabályi megfelelés irányítópultjára.

    Az új szabvány megjelenik az iparági szabványok & listájában. 

    > [!NOTE]
    > Az újonnan hozzáadott szabvány megjelenése a megfelelőségi irányítópulton néhány órát is igénybe vehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Jogszabályi megfelelés irányítópultja" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

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