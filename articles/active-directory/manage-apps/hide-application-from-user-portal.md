---
title: Alkalmazás elrejtése a felhasználói élmény az Azure Active Directoryban |} A Microsoft Docs
description: Alkalmazás elrejtése a felhasználói élmény az Azure Active Directory hozzáférési paneljein vagy Office 365 kilövők módja.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd98aa974f2adcd363c04c10b7a10cef6ca8ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824534"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>A végfelhasználók számára az Azure Active Directoryban alkalmazások elrejtése

Útmutató az alkalmazások végfelhasználók MyApps panelről vagy az Office 365 indítója elrejtése. Amikor egy alkalmazás rejtve marad, a felhasználók továbbra is fennáll az alkalmazásnak. 

## <a name="prerequisites"></a>Előfeltételek

Alkalmazás elrejtése a MyApps panelen és az Office 365 indítója a alkalmazás rendszergazdai jogosultság szükséges.

Globális rendszergazdai jogosultság szükséges összes Office 365-alkalmazások elrejtése.


## <a name="hide-an-application-from-the-end-user"></a>A végfelhasználó számára az alkalmazás elrejtése
Az alábbi lépések segítségével alkalmazás elrejtése a MyApps panelen és az Office 365 alkalmazásindítójában.

1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza az **Azure Active Directory** elemet.
3.  Válassza ki **vállalati alkalmazások**. A **nagyvállalati alkalmazások – minden alkalmazás** panel nyílik meg.
4.  A **alkalmazástípus**válassza **vállalati alkalmazások**, ha még nincs kiválasztva.
5.  Keresse meg a elrejtéséhez, és kattintson az alkalmazás kívánt alkalmazást.  Megnyílik az alkalmazás áttekintése.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  Az a **látható a felhasználók számára?** kérdés, kattintson a **nem**.
8.  Kattintson a **Save** (Mentés) gombra.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Office 365-alkalmazások a MyApps panelről elrejtése

Az alábbi lépések segítségével a MyApps panelről az összes Office 365-alkalmazások elrejtése. Az alkalmazások továbbra is megjelennek az Office 365 portálra.

1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza az **Azure Active Directory** elemet.
3.  Válassza ki **felhasználói beállítások**.
4.  A **vállalati alkalmazások**, kattintson a **kezelése hogyan végfelhasználók indítsa el, és azok az alkalmazások megtekintéséhez.**
5.  A **csak a tekintse meg az Office 365 portálon az Office 365-alkalmazások**, kattintson a **Igen**.
6.  Kattintson a **Save** (Mentés) gombra.


## <a name="next-steps"></a>További lépések
* [Tekintse meg az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)

