---
title: Igény szerinti hozzáférés igénylése
description: Azt ismerteti, hogyan Azure Managed Applications a felhasználók a felügyelt alkalmazásokhoz való igény szerinti hozzáférésre vonatkozó kérések jóváhagyását.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651019"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Igény szerinti hozzáférés konfigurálása és jóváhagyása Azure Managed Applications

A felügyelt alkalmazások felhasználóinak nem biztos, hogy a kiadó állandó hozzáférést biztosítanak a felügyelt erőforráscsoporthoz. A felügyelt erőforrásokhoz való hozzáférés biztosításának nagyobb mértékű szabályozása érdekében Azure Managed Applications biztosít egy igény szerinti (JIT) hozzáférés szolgáltatást, amely jelenleg előzetes verzióban érhető el. Ezzel a beállítással jóváhagyhatja, hogy a közzétevő mikor és mennyi ideig férhet hozzá az erőforráscsoporthoz. A közzétevő az idő múlásával elvégezheti a szükséges frissítéseket, de ha az idő véget ért, a közzétevő hozzáférése lejár.

A hozzáférés biztosításának munkafolyamata a következő:

1. A közzétevő egy felügyelt alkalmazást ad hozzá a piactérhez, és megadja, hogy az JIT-hozzáférés elérhető.

1. Az üzembe helyezés során engedélyezheti a felügyelt alkalmazás példányának JIT-hozzáférését.

1. Az üzembe helyezés után módosíthatja a JIT-hozzáférés beállításait.

1. A közzétevő kérelmet küld a hozzáféréshez.

1. Jóváhagyja a kérelmet.

Ez a cikk a felhasználók által a JIT-hozzáférés engedélyezéséhez és a kérelmek jóváhagyásához szükséges műveletekre összpontosít. A felügyelt alkalmazások JIT-hozzáféréssel való közzétételének megismeréséhez tekintse meg [az igény szerinti hozzáférés kérése Azure Managed Applicationsban](request-just-in-time-access.md)című témakört.

> [!NOTE]
> Az igény szerinti hozzáférés használatához [Azure Active Directory P2-licenccel](../../active-directory/privileged-identity-management/subscription-requirements.md)kell rendelkeznie.

## <a name="enable-during-deployment"></a>Engedélyezés az üzembe helyezés során

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Egy felügyelt alkalmazáshoz tartozó piactér-bejegyzés keresése, amelyen engedélyezve van az JIT. Válassza a **Létrehozás** lehetőséget.

1. Az új felügyelt alkalmazáshoz tartozó értékek megadásakor az **JIT konfigurációs** lépés lehetővé teszi a felügyelt alkalmazáshoz tartozó JIT-hozzáférés engedélyezését vagy letiltását. Válassza az **Igen** lehetőséget a **JIT-hozzáférés engedélyezéséhez**. Ez a beállítás alapértelmezés szerint a piactéren engedélyezett JIT-vel definiált felügyelt alkalmazások esetében van kiválasztva.

   ![Hozzáférés konfigurálása](./media/approve-just-in-time-access/configure-jit-access.png)

   Az üzembe helyezés során csak a JIT-hozzáférés engedélyezésére van lehetőség. Ha a **nem** lehetőséget választja, a közzétevő állandó hozzáférést kap a felügyelt erőforráscsoporthoz. Az JIT-hozzáférés később nem engedélyezhető.

1. Az alapértelmezett jóváhagyási beállítások módosításához válassza a **JIT-konfiguráció testreszabása** lehetőséget.

   ![Hozzáférés testreszabása](./media/approve-just-in-time-access/customize-jit-access.png)

   Alapértelmezés szerint a JIT-t használó felügyelt alkalmazás a következő beállításokkal rendelkezik:

   * Jóváhagyási mód – automatikus
   * Maximális hozzáférés időtartama – 8 óra
   * Jóváhagyók – nincs

   Ha a jóváhagyási mód **automatikus** értékre van állítva, a jóváhagyók értesítést kapnak az egyes kérésekről, de a kérést a rendszer automatikusan jóváhagyja. Ha a **manuális** értékre van állítva, a jóváhagyók értesítést kapnak minden kérelemről, és az egyiknek jóvá kell hagynia.

   Az aktiválás maximális időtartama határozza meg azt a maximális időtartamot, ameddig a közzétevő a felügyelt erőforráscsoport számára hozzáférést igényelhet.

   A jóváhagyók listája az a Azure Active Directory felhasználók, akik elfogadják a JIT hozzáférési kérelmeket. A jóváhagyó hozzáadásához válassza a **jóváhagyó hozzáadása** lehetőséget, és keresse meg a felhasználót.

   A beállítás frissítése után válassza a **Mentés** lehetőséget.

## <a name="update-after-deployment"></a>Frissítés az üzembe helyezés után

Módosíthatja a kérések jóváhagyásának értékeit. Ha azonban nem engedélyezte a JIT-hozzáférést az üzembe helyezés során, később nem engedélyezheti.

Telepített felügyelt alkalmazás beállításainak módosítása:

1. A portálon válassza az alkalmazás kezelése lehetőséget.

1. Válassza a **JIT-konfiguráció** lehetőséget, és szükség szerint módosítsa a beállításokat.

   ![Hozzáférési beállítások módosítása](./media/approve-just-in-time-access/change-settings.png)

1. Ha elkészült, kattintson a **Mentés** elemre.

## <a name="approve-requests"></a>Kérések jóváhagyása

Amikor a közzétevő hozzáférést kér, értesítést kap a kérelemről. A JIT hozzáférési kérelmeket közvetlenül a felügyelt alkalmazáson vagy az összes felügyelt alkalmazáson keresztül is jóváhagyhatja a Azure AD Privileged Identity Management szolgáltatáson keresztül. Az igény szerinti hozzáférés használatához [Azure Active Directory P2-licenccel](../../active-directory/privileged-identity-management/subscription-requirements.md)kell rendelkeznie.

Kérelmek jóváhagyása a felügyelt alkalmazáson keresztül:

1. Válassza a felügyelt alkalmazáshoz tartozó **JIT-hozzáférés** lehetőséget, majd válassza a **kérések jóváhagyása** lehetőséget.

   ![Kérések jóváhagyása](./media/approve-just-in-time-access/approve-requests.png)
 
1. Válassza ki a jóváhagyandó kérést.

   ![Kérelem kiválasztása](./media/approve-just-in-time-access/select-request.png)

1. Az űrlapon adja meg a jóváhagyás okát, és válassza a **jóváhagyás** lehetőséget.

Kérelmek jóváhagyása Azure AD Privileged Identity Managementon keresztül:

1. Válassza a **minden szolgáltatás** lehetőséget, és kezdje meg a **Azure ad Privileged Identity Management** keresését. Válassza ki az elérhető lehetőségek közül.

   ![Szolgáltatás keresése](./media/approve-just-in-time-access/search.png)

1. Válassza a **kérelmek jóváhagyása** lehetőséget.

   ![Válassza ki a kérelmek jóváhagyása lehetőséget](./media/approve-just-in-time-access/select-approve-requests.png)

1. Válassza ki az **Azure által felügyelt alkalmazások** elemet, majd válassza ki a jóváhagyásra vonatkozó kérést.

   ![Kérelmek kiválasztása](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Következő lépések

A felügyelt alkalmazások JIT-hozzáféréssel való közzétételének megismeréséhez tekintse meg [az igény szerinti hozzáférés kérése Azure Managed Applicationsban](request-just-in-time-access.md)című témakört.
