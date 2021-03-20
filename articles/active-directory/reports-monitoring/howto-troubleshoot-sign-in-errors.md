---
title: A bejelentkezési hibákkal kapcsolatos jelentések hibaelhárítása | Microsoft Docs
description: A bejelentkezéssel kapcsolatos hibák elhárítása Azure Active Directory jelentések használatával a Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85608126"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Útmutató: Bejelentkezési hibák elhárítása az Azure Active Directory jelentéseivel

A Azure Active Directory (Azure AD) [bejelentkezési jelentései](concept-sign-ins.md) lehetővé teszik a szervezet alkalmazásaihoz való hozzáférés kezelésével kapcsolatos kérdésekre adott válaszok megkeresését, beleértve a következőket:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?


A bejelentkezési jelentés továbbá segítséget nyújt a cégen belüli felhasználók bejelentkezési hibáinak elhárításához. Ebből az útmutatóból megtudhatja, hogyan különíthető el a bejelentkezési hiba a bejelentkezési jelentésben, és hogyan használható a hiba kiváltó okának megismerésére.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy prémium szintű (P1/P2) licenccel rendelkező Azure AD-bérlő. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört.
* Egy felhasználó, aki a **globális rendszergazda**, a **biztonsági rendszergazda**, a **biztonsági olvasó** vagy a **jelentéskészítő olvasó** szerepkörben található a bérlőhöz. Emellett a felhasználók hozzáférhetnek a saját bejelentkezésekhez is. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Bejelentkezési hibák elhárítása a bejelentkezési jelentés használatával

1. Navigáljon a [Azure Portal](https://portal.azure.com) , és válassza ki a címtárat.
2. Válassza a **Azure Active Directory** lehetőséget, majd a **figyelés** szakaszban válassza a **bejelentkezések** lehetőséget. 
3. A megadott szűrők segítségével Szűkítse le a hibát a Felhasználónév vagy az objektumazonosító, az alkalmazás neve vagy a dátum alapján. Emellett válassza az **állapot** legördülő lista **meghibásodás** elemét, hogy csak a sikertelen bejelentkezéseket jelenítse meg. 

    ![Szűrés eredményei](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Azonosítsa a vizsgálni kívánt sikertelen bejelentkezést. Válassza ki azt a további részletek ablak megnyitásához, ahol további információ található a sikertelen bejelentkezésről. Jegyezze fel a **bejelentkezéshez tartozó hibakódot** és a **hiba okát**. 

    ![Rekord kiválasztása](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Ezeket az információkat a részletek ablak **Hibaelhárítás és támogatás** lapján tekintheti meg.

    ![Hibaelhárítás és támogatás](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. A hiba oka a hibát mutatja. A fenti forgatókönyvben például a hiba oka **érvénytelen Felhasználónév vagy jelszó, vagy érvénytelen a helyszíni Felhasználónév vagy jelszó**. A javítás a helyes felhasználónévvel és jelszóval való újbóli bejelentkezés.

7. További információkat, például a Szervizelési ötleteket is megkeresheti, ha az ebben a példában szereplő **50126** -as hibakódot keresi, a [bejelentkezések hibakód-referenciájában](reference-sign-ins-error-codes.md). 

8. Ha az összes kötél meghibásodik, vagy ha a probléma továbbra is fennáll, az ajánlott művelet végrehajtása előtt [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a **Hibaelhárítás és támogatás** lapon leírt lépések követésével. 

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezések hibakód-referenciája](reference-sign-ins-error-codes.md)
* [A bejelentkezések jelentésének áttekintése](concept-sign-ins.md)
