---
title: Nevesített helyek konfigurálása Azure Active Directory használatával | Microsoft Docs
description: Ismerje meg, hogyan történik a nevesített helyek konfigurálása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12991813f68a42f9846c1c9c9c31c01d371d1d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107639"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Gyors útmutató: Nevesített helyek konfigurálása az Azure Active Directoryban

A nevesített helyek segítségével megcímkézhetők a szervezeten belül a megbízható IP-címtartományok. Az Azure AD a nevesített helyeket a következő célokra használja:
- Észlelheti a vakriasztású [kockázati eseményeket](concept-risk-events.md). A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata.   
- Konfigurálása [helyalapú feltételes hozzáférés](../conditional-access/location-condition.md).

Ebben a rövid útmutatóban megismerheti, hogyan lehet nevesített helyeket konfigurálni a saját környezetében.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Egy Azure AD-bérlő. Regisztráljon az [ingyenes próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Egy felhasználó, aki a bérlő globális rendszergazdája lesz.
* Egy IP-címtartomány, amely jól ismert és hiteles a szervezeten belül. Az IP-címtartománynak a **CIDR** formátumban kell lennie.

## <a name="configure-named-locations"></a>Nevesített helyek konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali panelen válassza ki a **Azure Active Directory**, majd **feltételes hozzáférési** a a **biztonsági** szakaszban.

    ![Feltételes hozzáférés lap](./media/quickstart-configure-named-locations/entrypoint.png)

3. A **Feltételes hozzáférési** lapon jelölje be **Nevesített helyeket** és válassza az **Új hely** lehetőséget.

    ![Névvel ellátott helyek](./media/quickstart-configure-named-locations/namedlocation.png)

6. Töltse ki az űrlapot az új oldalon. 

   * A **Név** mezőbe írja be a megnevezett hely nevét.
   * Az **IP-címtartományok** mezőbe írja be az IP-címtartományt CIDR formátumban.  
   * Kattintson a **Create** (Létrehozás) gombra.
    
     ![Az új panel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Az Azure ad-beli feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md).
- [Az Azure AD feltételes hozzáférés a helyszínre vonatkozó feltételek](../conditional-access/location-condition.md)
- [Kockázatos bejelentkezések jelentés](concept-risky-sign-ins.md).  
