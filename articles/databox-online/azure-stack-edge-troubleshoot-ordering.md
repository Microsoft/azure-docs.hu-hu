---
title: A Azure Portal használata Azure Stack Edge Pro-rendezési problémák elhárításához | Microsoft Docs
description: Leírja, hogyan lehet elhárítani a Azure Stack Edge Pro rendezésével kapcsolatos problémákat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e074043cb685f60027d3c09ae7ad8dc17dded0df
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443403"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>A Azure Stack Edge Pro-rendezéssel kapcsolatos problémák elhárítása

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure Stack Edge Pro rendezésével kapcsolatos problémákat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Rendezési problémák elhárítása

## <a name="unsupported-subscription-or-region"></a>Nem támogatott előfizetés vagy régió

**Hiba leírása:** Ha a következő hibaüzenetet kapja Azure Portal:

*A kiválasztott előfizetés vagy régió nem támogatott. Válasszon másik előfizetést vagy régiót.*

![Nem támogatott előfizetés vagy régió](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Javasolt megoldás:**  Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint például a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/overview/sales-number/), a [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/). Az utólagos elszámolású előfizetések nem támogatottak. További információ: [Azure stack Edge-erőforrás előfeltételei](azure-stack-edge-deploy-prep.md#prerequisites).

Lehetséges, hogy a Microsoft engedélyezheti az előfizetési típus frissítését eseti alapon. Vegye fel a kapcsolatot a [Microsoft ügyfélszolgálatával](https://azure.microsoft.com/support/options/) , hogy tisztában legyen az igényeivel, és megfelelően módosítsa ezeket a korlátokat.

## <a name="selected-subscription-type-not-supported"></a>A kiválasztott előfizetés típusa nem támogatott

**Hiba:** EA, CSP vagy szponzorált előfizetéssel rendelkezik, és a következő hibaüzenetet kapja:

*A kiválasztott előfizetés típusa nem támogatott. Győződjön meg arról, hogy támogatott előfizetést használ. [További információ](azure-stack-edge-deploy-prep.md#prerequisites). Ha támogatott előfizetés-típust használ, győződjön meg arról, hogy a `Microsoft.DataBoxEdge` szolgáltató regisztrálva van. A regisztrálásával kapcsolatos információkért lásd: az [erőforrás-szolgáltató regisztrálása](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Javasolt megoldás:** Az Azure Stack Edge erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket:

1. A Azure Portalban lépjen a **Kezdőlap**  >  **előfizetések** elemre.

2. Válassza ki azt az előfizetést, amelyet az eszköz megrendeléséhez használni fog.

3. Válassza az **erőforrás-szolgáltatók** lehetőséget, majd keressen rá a **Microsoft. DataBoxEdge** kifejezésre.

    ![Erőforrás-szolgáltató regisztrálása](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Ha nem rendelkezik tulajdonosi vagy közreműködői hozzáféréssel az erőforrás-szolgáltató regisztrálásához, a következő hibaüzenet jelenik meg: *az előfizetés- &lt; előfizetés neve &gt; nem rendelkezik engedéllyel az erőforrás-szolgáltató (k) regisztrálásához: Microsoft. DataBoxEdge.*

További információ: erőforrás- [szolgáltatók regisztrálása](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>A Microsoft. DataBoxEdge nincs regisztrálva az előfizetéshez

**Hiba:** Azure Portal a Azure Stack Edge Pro vagy a Data Box Gateway esetében használni kívánt előfizetést választhatja, és a következő hibaüzenetet kaphatja:

*Erőforrás-szolgáltató (k): a Microsoft. DataBoxEdge nincs regisztrálva az előfizetés &lt; -előfizetés neveként &gt; , és nincs engedélye az erőforrás-szolgáltató regisztrálására az előfizetés- &lt; előfizetés neveként &gt;*.

**Javasolt megoldás:** Emelje fel az előfizetés-hozzáférését, vagy keressen tulajdonosi vagy közreműködői hozzáféréssel rendelkező személyt az erőforrás-szolgáltató regisztrálásához.

## <a name="resource-disallowed-by-policy"></a>Az erőforrást a szabályzat nem engedélyezte

**Hiba:** A Azure Portal-ben megkísérli regisztrálni az erőforrás-szolgáltatót, és a következő hibaüzenetet kapja:

*&lt; &gt; A házirend nem engedélyezte az erőforrás-erőforrás nevét. (Kód: RequestDisallowedByPolicy). Kezdeményezés: általában nemkívánatos erőforrástípusok megtagadása. Házirend: nem engedélyezett erőforrástípusok.*

**Javasolt megoldás:** Ez a hiba egy meglévő Azure-szabályzat miatt fordul elő, amely blokkolja az erőforrások létrehozását. Az Azure-szabályzatokat a szervezet rendszergazdája állítja be, hogy biztosítsa az Azure-erőforrások használatának vagy létrehozásának megfelelőségét. Ha bármely ilyen házirend blokkolja Azure Stack peremhálózati erőforrás-létrehozást, forduljon a rendszergazdához az Azure-szabályzat szerkesztéséhez.

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure stack Edge Pro-problémák elhárításáról](azure-stack-edge-troubleshoot.md).