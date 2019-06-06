---
title: Váltás a B2C-bérlőre az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan lehet váltani, az Active Directory B2C-bérlője kontextusában.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d1580931a94b58e772f9f11cb7b9948216e9063a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509894"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Átváltás az Azure AD B2C-bérlőre

Az Azure AD B2C konfigurálásához az Azure AD B2C-bérlői kontextusában kell lennie.

## <a name="log-into-azure-ad-b2c-tenant"></a>Bejelentkezés az Azure AD B2C-bérlőre

Az Azure AD B2C-bérlőre való navigáláshoz az Azure AD B2C-bérlő globális rendszergazdájaként kell bejelentkeznie az Azure Portalra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bérlők között az e-mail-címére vagy a jobb felső sarokban található képre kattintva válthat.
1. A megjelenő `Directory` listában válassza ki a kezelni kívánt Azure AD B2C-bérlőt.

Az Azure Portal frissül.  Sikeresen bejelentkezett az Azure Portalra az Azure AD B2C-bérlője kontextusában.

## <a name="navigate-to-the-b2c-features-pane"></a>Lépjen a B2C funkciók ablaktáblára

1. Kattintson a képernyő bal oldalán található **Tallózás** navigációs elemre.
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az `Azure AD B2C` elemet a bal oldali navigációs ablaktáblán.  (A bal oldali kezdőpulthoz való rögzítéshez kattintson az Azure AD B2C-től balra található csillagra.)
1. A B2C-funkciók ablaktábla eléréséhez kattintson az **Azure AD B2C** elemre.
   
    ![Képernyőkép, keresse meg a B2C funkciók ablaktáblára](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> A B2C-bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C-funkciók ablaktáblához. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
