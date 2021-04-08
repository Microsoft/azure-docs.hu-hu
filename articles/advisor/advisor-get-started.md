---
title: Ismerkedés az Azure Advisor szolgáltatással
description: A Azure Advisor első lépései.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85117862"
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Ismerje meg, hogyan érheti el az Advisorot a Azure Portalon keresztül, javaslatokat kaphat, és javaslatokat valósíthat meg.

> [!NOTE]
> Azure Advisor automatikusan fut a háttérben az újonnan létrehozott erőforrások megtalálásához. Akár 24 órát is igénybe vehet, hogy javaslatokat nyújtson ezekre az erőforrásokra vonatkozóan.

## <a name="get-recommendations"></a>Javaslatok kérése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblán kattintson az **Advisor** elemre.  Ha nem látja az Advisort a bal oldali ablaktáblán, kattintson a **minden szolgáltatás** elemre.  Kattintson a szolgáltatás menü **felügyelet és kezelés** területén található **Advisor** elemre. Megjelenik az Advisor irányítópultja.

   ![Hozzáférés Azure Advisor a Azure Portal használatával](./media/advisor-get-started/advisor-portal-menu.png) 

1. Az Advisor irányítópultja a javaslatok összefoglalását jeleníti meg az összes kiválasztott előfizetésre vonatkozóan.  Az előfizetési szűrő legördülő menüje segítségével kiválaszthatja azokat az előfizetéseket, amelyekhez javaslatokat kíván megjeleníteni.

1. Egy adott kategóriához tartozó javaslatok beszerzéséhez kattintson az egyik lapra: **megbízhatóság**, **Biztonság**, **teljesítmény** vagy **Cost**. 

   ![Azure Advisor irányítópult](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Javaslat részleteinek beszerzése és megoldás implementálása

Kiválaszthat egy javaslatot az Advisorban, ahol további részleteket tekinthet meg – például a javasolt műveleteket és az érintett erőforrásokat –, valamint a megoldás megvalósítását a javaslathoz.  

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Válasszon egy javaslatot a kategórián belüli javaslatok listájának megjelenítéséhez, vagy válassza az **összes** fület az összes javaslat megtekintéséhez.

1. Kattintson arra a javaslatra, amelyet részletesen szeretne áttekinteni.

1. Tekintse át a javaslatra vonatkozó információkat és azokat az erőforrásokat, amelyekre a javaslat vonatkozik.

1. A javaslat megvalósításához kattintson a **javasolt műveletre** .

## <a name="filter-recommendations"></a>Javaslatok szűrése

A javaslatok szűrésével a legfontosabb dolgokra szűkítheti a részletezést.  Az előfizetés, az erőforrástípus vagy a javaslat állapota alapján szűrhet.  

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Használja az Advisor irányítópult legördülő menüit az előfizetés, az erőforrástípus vagy a javaslat állapota alapján történő szűréshez.

    ![Advisor Search – szűrési feltételek](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Javaslatok elhalasztása vagy elvetése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Navigáljon az elhalasztani vagy elvetni kívánt javaslathoz.

1. Kattintson a javaslatra.

1. Kattintson az **elhalasztás** gombra. 

1. Adja meg az elhalasztási időszakot, vagy válassza a **soha** lehetőséget a javaslat bezárásához.

## <a name="exclude-subscriptions-or-resource-groups"></a>Előfizetések vagy erőforráscsoportok kizárása

Lehetnek olyan erőforráscsoportok vagy előfizetések, amelyekhez nem kíván tanácsadói javaslatokat kapni – például "teszt" erőforrásokra.  Az Advisort úgy is beállíthatja, hogy csak bizonyos előfizetésekhez és erőforráscsoportokhöz készítsen javaslatokat.

> [!NOTE]
> Előfizetés vagy erőforráscsoport az Advisorból való felvételéhez vagy kizárásához előfizetés-tulajdonosnak kell lennie.  Ha nem rendelkezik az előfizetéshez vagy az erőforráscsoporthoz szükséges engedélyekkel, a felhasználói felületen le kell tiltania vagy kizárhatja a beállítást.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Kattintson a **Konfigurálás** elemre a művelet sávon.

1. Törölje az összes olyan előfizetést vagy erőforráscsoportot, amelyről nem kíván tanácsadói javaslatokat kapni.

    ![Advisor-erőforrások konfigurálása – példa](./media/advisor-get-started/advisor-configure-resources.png)

1. Kattintson az **alkalmaz** gombra.

## <a name="configure-low-usage-vm-recommendation"></a>Alacsony használati virtuálisgép-javaslat konfigurálása

Ezzel az eljárással konfigurálhatja az alacsony kihasználtságú virtuális gépekre vonatkozó ajánlás átlagos CPU-kihasználtsági szabályát.

Az Advisor 7 napig figyeli a virtuális gépek használatát, majd azonosítja az alacsony kihasználtságú virtuális gépeket. A virtuális gépek alacsony kihasználtságnak számítanak, ha a processzor kihasználtsága 5% vagy kevesebb, és a hálózati kihasználtsága kevesebb, mint 2%, vagy ha a jelenlegi számítási feladatot kisebb virtuálisgép-mérettel lehet elfogadni.

Ha szeretné, hogy az alacsony kihasználtságú virtuális gépek azonosítása agresszívebb legyen, az átlagos CPU-kihasználtsági szabályt előfizetési alapon állíthatja be.  A CPU-kihasználtsági szabály 5%-ra, 10%-ra, 15%-ra vagy 20%-ra állítható be.

> [!NOTE]
> Az alacsony kihasználtságú virtuális gépek azonosítására szolgáló átlagos CPU-kihasználtsági szabály módosításához előfizetés- *tulajdonosnak* kell lennie.  Ha nem rendelkezik az előfizetéshez vagy az erőforráscsoporthoz szükséges engedélyekkel, a felhasználói felületen le kell tiltania vagy kizárhatja a beállítást. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Kattintson a **Konfigurálás** elemre a művelet sávon.

1. Kattintson a **szabályok** fülre.

1. Válassza ki azokat az előfizetéseket, amelyekre módosítani szeretné az átlagos CPU-kihasználtsági szabályt, majd kattintson a **Szerkesztés** gombra.

1. Válassza ki a kívánt átlagos CPU-kihasználtsági értéket, majd kattintson az **alkalmaz** gombra.

1. Az új átlagos CPU-kihasználtsági szabály használatához kattintson a **javaslatok frissítése** lehetőségre a meglévő javaslatok frissítéséhez. 

   ![Tanácsadói javaslatok konfigurálása – példa](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Javaslatok letöltése

Az Advisor lehetővé teszi a javaslatok összefoglalásának letöltését.  Az ajánlásokat PDF-fájlként vagy CSV-fájlként töltheti le.  A javaslatok letöltésével könnyedén megoszthatja munkatársaival, vagy elvégezheti a saját elemzését az ajánlási adatok felett.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

1. Kattintson a **Letöltés CSV** **formátumban vagy Letöltés PDF** -ként lehetőségre a műveleti sávon.

A letöltési lehetőség tiszteletben tartja az Advisor irányítópultra alkalmazott szűrőket.  Ha a letöltés lehetőséget választja egy adott javaslat kategóriájának vagy javaslatának megtekintésekor, a letöltött összefoglalás csak az adott kategória vagy javaslat információit tartalmazza. 

## <a name="next-steps"></a>Következő lépések

Az Advisor szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következőt:

- [Az Azure Advisor bemutatása](advisor-overview.md)
- [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
- [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
- [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
- [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
- [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
