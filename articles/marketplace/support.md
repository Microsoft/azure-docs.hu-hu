---
title: Támogatás kérése a kereskedelmi piactér programhoz a partner Centerben
description: Ismerje meg a partner Centerben elérhető kereskedelmi piactér program támogatási lehetőségeit, beleértve a támogatási kérések beszerzését.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879241"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>A kereskedelmi piactér program támogatása a partner Centerben

A Microsoft számos termék és szolgáltatás támogatását biztosítja. A megfelelő támogatási csapat megtalálása fontos a megfelelő és kellő időben történő válaszadás biztosításához. Vegye figyelembe a következő forgatókönyveket, amelyek segítenek a lekérdezés átirányításában a megfelelő csapatnak:

- Ha Ön kiadó, és kérdése van egy ügyféltől, kérje meg az ügyfelet, hogy kérjen támogatást a [Azure Portal](https://portal.azure.com/)támogatási hivatkozásait használva.
- Ha Ön közzétevő, és biztonsági problémát észlelt az Azure-on futó alkalmazással kapcsolatban, tekintse meg [a biztonsági eseményekre vonatkozó támogatási jegy naplózása](../security/fundamentals/event-support-ticket.md)című témakört. A kiadóknak a lehető leghamarabb jelenteniük kell a feltételezett biztonsági eseményeket, beleértve az Azure Marketplace-szoftverek és-szolgáltatások biztonsági incidenseit és biztonsági réseit.
- Ha Ön kiadó, és az alkalmazásával vagy szolgáltatásával kapcsolatos kérdése van, tekintse át a következő támogatási lehetőségeket.

## <a name="get-help-or-open-a-support-ticket"></a>Segítség kérése vagy támogatási jegy megnyitása

1. Jelentkezzen be a munkahelyi fiókjával. Ha még nem tette meg, akkor [létre kell hoznia egy partner Center-fiókot](partner-center-portal/create-account.md).

1. A lap jobb felső részén található menüben válassza a **támogatás** ikont. A **Súgó és támogatás** panel a lap jobb oldalán jelenik meg.

1. Ha segítségre van a kereskedelmi piactéren, válassza a **kereskedelmi piactér** lehetőséget.

   ![Támogatás legördülő menü](./media/support/commercial-marketplace-support-pane.png)

1. A **probléma összegzése** mezőben adja meg a probléma rövid leírását.

1. A **probléma típusa** mezőben tegye a következők egyikét:

    - **1. lehetőség**: olyan kulcsszavakat adjon meg, mint például a piactér, az Azure-alkalmazás, az SaaS-ajánlat, a fiókok kezelése, az érdeklődők felügyelete, a üzembe helyezési probléma, a kifizetés vagy a közös értékesítés ajánlata. Ezután válassza ki a megjelenő ajánlott listából a probléma típusát.

    - **2. lehetőség**: válassza a **témakörök tallózása** lehetőséget a **Kategória** listából, majd válassza a **kereskedelmi piactér** lehetőséget. Ezután válassza ki a megfelelő **témakört** és **altémakört**.

1. Miután megtalálta a kívánt témakört, válassza a **megoldások áttekintése** elemet.

    ![Következő lépés](./media/support/next-step.png)

A következő lehetőségek jelennek meg:

- Egy másik témakör kiválasztásához kattintson **a másik probléma kiválasztása** lehetőségre.
- A probléma megoldásához tekintse át az ajánlott lépéseket és dokumentumokat, ha vannak ilyenek.

    ![Javasolt megoldások](./media/support/recommended-solutions.png)

Ha nem találja a választ a saját súgójában, válassza a **probléma részleteinek megadása** lehetőséget. Fejezze be az összes kötelező mezőt a megoldási folyamat felgyorsításához, majd válassza a **Küldés** lehetőséget.

>[!Note]
>Ha még nem jelentkezett be a partner központba, előfordulhat, hogy a jegy létrehozása előtt be kell jelentkeznie.

## <a name="track-your-existing-support-requests"></a>Meglévő támogatási kérelmek nyomon követése

A nyitott és lezárt jegyek áttekintéséhez a bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **támogatás** lehetőséget.

## <a name="record-issue-details-with-a-har-file"></a>Probléma részleteinek rögzítése egy HAR-fájllal

Ha az ügynököket a probléma megoldásához szeretné támogatni, érdemes lehet HTTP Archive Format (HAR) fájlt csatolni a támogatási jegyhez. A HAR-fájlok egy webböngészőben lévő hálózati kérelmek naplói.

> [!WARNING]
> A HAR-fájlok rögzíthetik a partner Center-fiókkal kapcsolatos bizalmas adatokat.

### <a name="microsoft-edge-and-google-chrome"></a>A Microsoft Edge és a Google Chrome

HAR-fájl létrehozása a **Microsoft Edge** vagy a **Google Chrome** használatával:

1. Lépjen arra a weblapra, ahol a problémát tapasztalja.
2. Az ablak jobb felső sarkában válassza a három pontot ábrázoló ikont, majd a **további eszközök**  >  **fejlesztői eszközöket**. Az F12 billentyű lenyomása parancsikonként is megadható.
3. A fejlesztői eszközök panelen válassza a **hálózat** lapot.
4. A meglévő naplók eltávolításához válassza a **hálózati napló rögzítésének leállítása** és **Törlés** lehetőséget. A rekord ikonja szürkévé válik.

    ![Meglévő naplók eltávolítása a Microsoft Edge-ben vagy a Google Chrome-ban](media/support/chromium-stop-clear-session.png)

5. A rögzítés elindításához válassza a **hálózati napló rögzítése** lehetőséget. A rögzítés megkezdése után a rekord ikon pirosra vált.

    ![Felvétel indítása a Microsoft Edge-ben vagy a Google Chrome-ban](media/support/chromium-start-session.png)

6. Reprodukálja a hibakereséshez használni kívánt problémát.
7. A probléma reprodukálása után válassza a **hálózati napló rögzítésének leállítása** lehetőséget.
8. Válassza a **har exportálása** elemet, amely egy lefelé mutató nyíl ikonnal van megjelölve, és mentse a fájlt.

    ![HAR-fájl exportálása a Microsoft Edge-ben vagy a Google Chrome-ban](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

HAR-fájl létrehozása a **Mozilla Firefox** használatával:

1. Lépjen arra a weblapra, ahol a problémát tapasztalja.
1. Az ablak jobb felső sarkában válassza a három pontot ábrázoló ikont, majd a **Web Developer**  >  **pecek eszközöket**. Az F12 billentyű lenyomása parancsikonként is megadható.
1. Válassza a **hálózat** fület, majd a **Törlés** gombra kattintva távolítsa el a meglévő naplókat.

    ![Meglévő naplók eltávolítása a Mozilla Firefox-ban](media/support/firefox-clear-session.png)

1. Reprodukálja a hibakereséshez használni kívánt problémát.
1. A probléma reprodukálása után válassza a **har Exportálás/Importálás** az  >  **összes mentése a har-ként** lehetőséget.

    ![HAR-fájl exportálása a Mozilla Firefox böngészőben](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

HAR-fájl létrehozása a **Safari** használatával:

1. A fejlesztői eszközök engedélyezése a Safariban: válassza a **Safari**-  >  **Beállítások** lehetőséget. Lépjen a **speciális** lapra, majd válassza a **menü fejlesztés megjelenítése menüpontját**.
1. Lépjen arra a weblapra, ahol a problémát tapasztalja.
1. Válassza a **fejlesztés**, majd a **webes ellenőr megjelenítése** lehetőséget.
1. Válassza a **hálózat** fület, majd a meglévő naplók eltávolításához válassza a **hálózati elemek törlése** elemet.

    ![Meglévő naplók eltávolítása a Safari alkalmazásban](media/support/safari-clear-session.png)

1. Reprodukálja a hibakereséshez használni kívánt problémát.
1. A probléma reprodukálása után válassza az **Exportálás** lehetőséget, és mentse a fájlt.

    ![HAR-fájl exportálása a Safari alkalmazásba](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](partner-center-portal/update-existing-offer.md)