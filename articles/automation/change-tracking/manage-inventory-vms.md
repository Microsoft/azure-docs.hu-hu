---
title: Azure Automation leltározási gyűjtemény kezelése virtuális gépekről | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kezelhetők a leltár-gyűjtemények a virtuális gépekről.
services: automation
ms.subservice: change-inventory-management
keywords: leltár, automatizálás, változás, követés
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209638"
---
# <a name="manage-inventory-collection-from-vms"></a>Leltárkezelés virtuális gépekről

Az Azure-beli virtuális gépek leltárának nyomon követését a gép erőforrás oldaláról engedélyezheti. A következő leltározási információkat gyűjtheti és tekintheti meg a számítógépeken:

- Windows-frissítések, Windows-alkalmazások,-szolgáltatások,-fájlok és-beállításkulcsok
- Linuxos szoftvercsomagok, démonok és fájlok

A Azure Automation Change Tracking és a leltár egy böngészőalapú felhasználói felületet biztosít a leltári gyűjtemény beállításához és konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ez a cikk azt feltételezi, hogy rendelkezik egy virtuális géppel, amely lehetővé teszi a Change Tracking és a leltárt. Ha nem rendelkezik Azure-beli virtuális géppel, [létrehozhat egy virtuális gépet](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Leltár-gyűjtemény engedélyezése a virtuális gép erőforrásának oldaláról

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. A virtuális gépek listájában válasszon ki egy gépet.
3. Az **erőforrás** menüben, a **műveletek** területen válassza a **leltár** elemet.
4. Válasszon ki egy Log Analytics munkaterületet az adatnaplók tárolásához.
    Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot.
5. A számítógép engedélyezésének megkezdéséhez válassza az **Engedélyezés** lehetőséget.

   ![Előkészítési lehetőségek megtekintése](./media/manage-inventory-vms/inventory-onboarding-options.png)

    Az állapotsor értesíti arról, hogy a Change Tracking és a leltár funkció engedélyezve van. Ez a folyamat 15 percet is igénybe vehet. Ebben az időszakban lezárhatja az ablakot, vagy megnyithatja, és értesítést kaphat, ha a funkció engedélyezve van. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![Leltár megjelenítése](./media/manage-inventory-vms/inventory-onboarded.png)

Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. A rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. A leltár lapon kattintson a lap tetején található **beállítások szerkesztése** elemre.
2. Új gyűjteményi beállítás hozzáadásához lépjen a hozzáadni kívánt kategóriára a **Windows beállításjegyzék**, a **Windows-fájlok** vagy a **Linux-fájlok** lap kiválasztásával.
3. Válassza ki a megfelelő kategóriát, majd kattintson a **Hozzáadás** gombra az oldal tetején.

A következő szakaszokban információt talál a különböző kategóriákhoz konfigurálható egyes tulajdonságokról.

### <a name="windows-registry"></a>Windows-beállításjegyzék

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Group     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Windows-beállításkulcs   | A fájl elérési útja, például: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows-fájlok

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
|Elem neve     | A nyomon követett fájl rövid neve.        |
|Group     | Egy csoport neve a fájlok logikai csoportosításához.       |
|Elérési út megadása     | A fájl keresésének elérési útja, például **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux-fájlok

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
|Elem neve     | A nyomon követett fájl rövid neve.        |
|Group     | Egy csoport neve a fájlok logikai csoportosításához.        |
|Elérési út megadása     | A fájl keresésének elérési útja, például **/etc/*. conf**.       |
|Elérési út típusa     | A nyomon követett elemek típusa. Az értékek a fájl és a könyvtár.        |
|Rekurzió     | True (igaz), ha a rendszer rekurziót használ a nyomon követett elem keresésekor, máskülönben hamis értéket ad.        |
|Sudo használata     | Igaz, ha a rendszer az elem keresésekor a sudo értéket használja, ellenkező esetben hamis értéket ad.         |
|Hivatkozások     | Az érték azt jelzi, hogy a szimbolikus hivatkozások hogyan kezelhetők a címtárak bejárásakor. Lehetséges értékek: <br> Mellőzés – Mellőzi a szimbolikus hivatkozásokat, és nem szerepelteti a hivatkozott fájlokat/címtárakat<br>Követés – Követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat<br>Kezelés – Követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom kezelésének módosítását      |

## <a name="manage-machine-groups"></a>Számítógépcsoportok kezelése

A leltár lehetővé teszi, hogy Azure Monitor naplókban hozzon létre és tekintse meg a számítógépcsoportokat. A számítógépcsoportok olyan gépek gyűjteményei, amelyeket Azure Monitor naplók lekérdezése határoz meg.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A számítógépcsoportok megtekintéséhez válassza a **Machine groups (számítógép-csoportok** ) lapot a leltár lapon.

![Számítógépcsoportok megtekintése a leltár lapon](./media/manage-inventory-vms/inventory-machine-groups.png)

Ha kijelöl egy gépcsoport elemet a listából, megnyílik a számítógép-csoportok lap. Ez az oldal a számítógépcsoport részleteit jeleníti meg. Ezek az adatok tartalmazzák a csoport definiálásához használt Azure Monitor log-lekérdezést. A lap alján található a csoport részét képező gépek lapozható listája.

![Számítógép-csoport megtekintése lap](./media/manage-inventory-vms/machine-group-page.png)

A számítógép csoport klónozásához kattintson a **+ klón** elemre. A csoportnak új nevet és aliast kell adnia a csoportnak. A definíció most már módosítható. A lekérdezés módosítása után kattintson a **lekérdezés ellenőrzése** lehetőségre a kijelölni kívánt gépek előnézetének megtekintéséhez. Ha elégedett a csoporttal, kattintson a **Létrehozás** gombra a számítógépcsoport létrehozásához.

Ha új számítógépcsoportot szeretne létrehozni, kattintson **a + számítógépcsoport létrehozása** lehetőségre. Ezzel a gombbal megnyílik a **számítógép-csoport létrehozása** lap, ahol megadhatja az új csoportot. A csoport létrehozásához kattintson a **Létrehozás** parancsra.

![Új számítógépcsoport létrehozása](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>A virtuális gép leválasztása a felügyelet alól

A virtuális gép eltávolítása a Change Tracking és a leltár-kezelésből:

1. A Azure Portal bal oldali ablaktábláján válassza a **log Analytics** lehetőséget, majd válassza ki azt a munkaterületet, amelyet a virtuális gép Change Trackinghoz és leltárhoz való engedélyezésekor használt.
2. A **log Analytics** lapon nyissa meg az **erőforrás** menüt.
3. Válassza a **Virtual Machines** lehetőséget a **munkaterület-adatforrások** területen.
4. A listából válassza ki a leválasztani kívánt virtuális gépet. A gép a **OMS-kapcsolatok** oszlopban a **munkaterület** mellett zöld pipa jelöl.

   >[!NOTE]
   >Az Operations Management Suite (OMS) mostantól Azure Monitor naplók néven is ismert.

5. A következő lap tetején kattintson a **Leválasztás** elemre.
6. A megerősítést kérő ablakban kattintson az **Igen** gombra a gép a felügyelet alól való leválasztásához.

>[!NOTE]
>A gépek még a regisztráció törlése után is megjelennek, mert az elmúlt 24 órában az összes gépen leltározott jelentést küldünk. A gép leválasztása után 24 órát kell várnia, mielőtt azok már nem jelennek meg.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking és a leltár kezelésével](manage-change-tracking.md)foglalkozó témakört.
* A szoftverfrissítések nyomon követésével kapcsolatos további információkért lásd: [a szoftverek változásainak követése a környezetben a Change Tracking](overview.md)használatával.
* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](../troubleshoot/change-tracking.md)című témakört.
