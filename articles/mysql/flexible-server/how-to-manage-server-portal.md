---
title: Kiszolgáló – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló kezelése
description: Megtudhatja, hogyan kezelheti Azure Database for MySQL rugalmas kiszolgálót a Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935071"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) kezelése Azure Portal használatával


> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan kezelheti Azure Database for MySQL rugalmas kiszolgálókat (előzetes verzió). A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a REST-kiszolgáló rendszergazdai jelszava és a kiszolgáló törlése.

## <a name="sign-in"></a>Bejelentkezés
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a rugalmas kiszolgálói erőforrást a Azure Portal.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A kiszolgáló létrehozása után a különböző [díjszabási szintek](https://azure.microsoft.com/pricing/details/mysql/) között méretezheti a változó igényeket. A számítási és memóriateljesítmény emellett a virtuális magok számának növelésével vagy csökkentésével módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Válassza a **számítás és tárolás** elemet, amely a **Beállítások** szakaszban található.

2. Megváltoztathatja a **számítási szintet**, a **virtuális mag**, a **tárterületet** a kiszolgáló vertikális felskálázásához magasabb számítási szinten vagy az ugyanazon a szinten belüli skálázással, ha növeli a tárterületet vagy a virtuális mag a kívánt értékre.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="a Storage rugalmas kiszolgáló méretezése":::

   > [!Important]
   > - A tárterület nem méretezhető le.
   > - A skálázás virtuális mag a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez kattintson **az OK gombra** .

## <a name="reset-admin-password"></a>Rendszergazdai jelszó visszaállítása

A rendszergazdai szerepkör jelszava a Azure Portal használatával módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **jelszó alaphelyzetbe állítása** lehetőséget.

2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegmező a jelszó bonyolultságára vonatkozó követelményeket fogja kérni.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="jelszó alaphelyzetbe állítása rugalmas kiszolgálóhoz":::

3. Az új jelszó mentéséhez kattintson a **Mentés** gombra.

## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha már nincs szüksége rá, törölheti a kiszolgálót.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **Törlés** lehetőséget.

2. Írja be a kiszolgáló nevét a beviteli mezőbe annak megerősítéséhez, hogy törölni kívánja a kiszolgálót.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="a rugalmas kiszolgáló törlése":::

   > [!NOTE]
   > A kiszolgáló törlése visszafordíthatatlan.

3. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések
- [Útmutató a kiszolgálók indításához és leállításához](how-to-stop-start-server-portal.md)
- [További információ a kiszolgálók visszaállításáról](how-to-restore-server-portal.md)
- [Kapcsolati problémák hibaelhárítása](how-to-troubleshoot-common-connection-issues.md)

