---
title: 'Gyors útmutató: első lépések'
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el a Defender alapszintű munkafolyamatának megismerését a IoT telepítéséhez.
ms.topic: quickstart
ms.date: 2/18/2021
ms.openlocfilehash: c6136d734570714b691a4ba3c2a72305c4c85590
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784492"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Gyors útmutató: az IoT Defender használatának első lépései

Ez a cikk áttekintést nyújt az Azure Defender IoT való beállításához szükséges lépésekről. A folyamathoz a következőket kell tennie:

- Regisztrálja előfizetését és érzékelőkét az Azure Defender for IoT portálon.
- Telepítse az érzékelőt és a helyszíni felügyeleti konzol szoftverét.
- Hajtsa végre az érzékelő és a felügyeleti konzol kezdeti aktiválását.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek

## <a name="permission-requirements"></a>Engedélyekre vonatkozó követelmények

Néhány telepítési lépéshez konkrét felhasználói engedélyek szükségesek.

A rendszergazda felhasználói engedélyek szükségesek az érzékelő és a felügyeleti konzol aktiválásához, az SSL/TLS-tanúsítványok feltöltéséhez és új jelszavak létrehozásához.

Az alábbi táblázat az Azure Defender IoT-portál eszközeire vonatkozó felhasználói hozzáférési engedélyeket ismerteti:

| Engedély | Biztonsági olvasó | Biztonsági rendszergazda | Előfizetés közreműködői | Előfizetés tulajdonosa |
|--|--|--|--|--|
| A IoT-képernyők és-az összes Defender megtekintése | ✓ | ✓ | ✓ | ✓ |
| Érzékelő előkészítése  |  |  ✓ | ✓ | ✓ |
| Frissítés díjszabása  |  |  ✓ | ✓ | ✓ |
| Jelszó helyreállítása  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>A megoldás-infrastruktúra azonosítása

**A hálózat telepítési igényeinek tisztázása**

A hálózati architektúra, a figyelt sávszélesség és az egyéb hálózati adatok kutatása. További információ: [Az Azure Defender IoT-hálózat beállítása](how-to-set-up-your-network.md).

**Annak tisztázása, hogy mely érzékelőkre és felügyeleti konzolra vonatkozó berendezések szükségesek a hálózati terhelés kezeléséhez**

A IoT készült Azure Defender mind a fizikai, mind a virtuális üzemelő példányokat támogatja. A fizikai telepítések esetében különböző tanúsítvánnyal rendelkező berendezéseket vásárolhat. További információkért lásd a [szükséges berendezések azonosítását](how-to-identify-required-appliances.md)ismertető témakört.

Javasoljuk, hogy számítsa ki a figyelni kívánt eszközök hozzávetőleges számát. Később, amikor regisztrálja Azure-előfizetését a portálon, a rendszer kérni fogja, hogy adja meg ezt a számot. A számok 1 000 másodperces időközökben adhatók hozzá. A figyelt eszközök számát *véglegesített eszközöknek* nevezzük.

## <a name="register-with-azure-defender-for-iot"></a>Regisztráció az Azure Defender for IoT

A regisztráció a következőket tartalmazza:

- Azure-előfizetések bevezetése a Defender for IoT.
- Véglegesített eszközök definiálása.
- Aktiválási fájl letöltése a helyszíni felügyeleti konzolhoz.

Regisztráció:

1. Nyissa meg az Azure Defender for IoT portált.
1. Válassza ki a **fedélzeti előfizetés** elemet.
1. A **díjszabás** lapon válasszon ki egy előfizetést, vagy hozzon létre egy újat, és adja hozzá a véglegesített eszközök számát.
1. Válassza a helyszíni **felügyeleti konzol letöltése** lapot, és mentse a letöltött aktiválási fájlt. Ez a fájl tartalmazza az Ön által megadott összesített véglegesített eszközöket. A rendszer a kezdeti bejelentkezés után feltölti a fájlt a felügyeleti konzolra.

További információ az előfizetés regisztrációjának megszüntetésére szolgáló: [regisztrációjának megszüntetésére szolgáló](how-to-manage-sensors-on-the-cloud.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol telepítése és beállítása

A helyszíni felügyeleti konzol berendezésének megvásárlása után:

- Töltse le az ISO-csomagot az Azure Defender for IoT portálról.
- Telepítse a szoftvert.
- A kezdeti felügyeleti konzol telepítésének aktiválása és elvégzése.

A telepítése és beállítása:

1. Válassza a **első lépések** lehetőséget a Defender for IoT portálon.
1. Válassza a helyszíni **felügyeleti konzol** fület.
1. Válasszon egy verziót, és válassza a **Letöltés** lehetőséget.
1. Telepítse a helyszíni felügyeleti konzol szoftverét. További információ: [Defender for IoT telepítés](how-to-install-software.md).
1. Aktiválja és állítsa be a felügyeleti konzolt. További információkért lásd: [a helyszíni felügyeleti konzol aktiválása és beállítása](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Érzékelő előkészítése

Érzékelők beléptetése az Azure Defender IoT való regisztrálásával és az érzékelő aktiválási fájljának letöltésével:

1. Határozza meg az érzékelő nevét, és társítsa azt egy előfizetéshez.
1. Válasszon érzékelő-felügyeleti módot:

   - **Felhőalapú csatlakoztatott érzékelők**: az érzékelők észlelésére szolgáló információk az érzékelő konzolon jelennek meg. Emellett a riasztási adatokat egy IoT hub továbbítja, és más Azure-szolgáltatásokkal, például az Azure Sentinelrel is megosztható.

   - **Helyileg felügyelt érzékelők**: az érzékelők észlelésére szolgáló információk az érzékelő konzolon jelennek meg. Ha gapped hálózaton dolgozik, és szeretné, hogy a több helyileg felügyelt érzékelők által észlelt összes információ egységes legyen, működjön együtt a helyszíni felügyeleti konzollal. 

1. Töltse le az érzékelő aktiválási fájlját.

További információkért lásd: érzékelők beléptetése [és kezelése a Defender for IoT portálon](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Az érzékelő telepítése és beállítása

Töltse le az ISO-csomagot az Azure Defender for IoT portálról, telepítse a szoftvert, és állítsa be az érzékelőt.

1. Válassza a **első lépések** lehetőséget a Defender for IoT portálon.
1. Válassza az **érzékelő beállítása** lehetőséget.
1. Válasszon egy verziót, és válassza a **Letöltés** lehetőséget.
1. Telepítse az érzékelő szoftverét. További információ: [Defender for IoT telepítés](how-to-install-software.md).
1. Aktiválja és állítsa be az érzékelőt. További információ: [Bejelentkezés és az érzékelő aktiválása](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Érzékelők összekapcsolása helyszíni felügyeleti konzollal

Kapcsolja össze az érzékelőket a felügyeleti konzolon, és győződjön meg arról, hogy:

- Az érzékelők a riasztási és az eszköz leltározási adatait a helyszíni felügyeleti konzolra küldik.

- A helyszíni felügyeleti konzol képes a biztonsági mentések elvégzésére, a riasztások kezelésére, az érzékelők észlelésére, az érzékelő leválasztásának kivizsgálására és a csatlakoztatott érzékelőkkel kapcsolatos egyéb tevékenységek végrehajtására.

Azt javasoljuk, hogy több érzékelőt is egy zónában ugyanazokat a hálózatokat figyelje. Ezzel egyesítheti a több érzékelő által gyűjtött információkat.

További információ: [érzékelők összekapcsolása a helyszíni felügyeleti konzolon](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Azure Sentinel kitöltése riasztási információkkal (opcionális)

Riasztási információk küldése az Azure Sentinelnek az Azure Sentinel konfigurálásával. Lásd: [adatok összekötése a defenderből a IoT az Azure Sentinel](how-to-configure-with-sentinel.md)szolgáltatásba.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Üdvözli a IoT](overview.md) 
>  készült Azure Defender! [Azure Defender a IoT architektúrához](architecture.md)
