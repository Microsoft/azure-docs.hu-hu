---
title: Első lépések
description: Ismerkedés a Defender alapszintű munkafolyamatainak IoT-telepítéssel való megismerésével.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/18/2021
ms.author: shhazam
ms.openlocfilehash: c224dc3f919471e769717f308aff9ae58630d421
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712759"
---
# <a name="get-started-with-defender-for-iot"></a>Ismerkedés a Defender IoT

Ez a cikk áttekintést nyújt az Azure Defender IoT való beállításához szükséges lépésekről. A folyamathoz a következőket kell tennie:

- Regisztrálja előfizetését és érzékelőkét az Azure Defender for IoT portálon.
- Telepítse az érzékelőt és a helyszíni felügyeleti konzol szoftverét.
- Hajtsa végre az érzékelő és a felügyeleti konzol kezdeti aktiválását.

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

## <a name="1-identify-the-solution-infrastructure"></a>1. a megoldási infrastruktúra azonosítása

**A hálózat telepítési igényeinek tisztázása**

A hálózati architektúra, a figyelt sávszélesség és az egyéb hálózati adatok kutatása. További információ: [Az Azure Defender IoT-hálózat beállítása](how-to-set-up-your-network.md).

**Annak tisztázása, hogy mely érzékelőkre és felügyeleti konzolra vonatkozó berendezések szükségesek a hálózati terhelés kezeléséhez**

A IoT készült Azure Defender mind a fizikai, mind a virtuális üzemelő példányokat támogatja. A fizikai telepítések esetében különböző tanúsítvánnyal rendelkező berendezéseket vásárolhat. További információkért lásd a [szükséges berendezések azonosítását](how-to-identify-required-appliances.md)ismertető témakört.

Javasoljuk, hogy számítsa ki a figyelni kívánt eszközök hozzávetőleges számát. Később, amikor regisztrálja Azure-előfizetését a portálon, a rendszer kérni fogja, hogy adja meg ezt a számot. A számok 1 000 másodperces időközökben adhatók hozzá. A figyelt eszközök számát *véglegesített eszközöknek* nevezzük.

## <a name="2-register-with-azure-defender-for-iot"></a>2. Regisztráljon az Azure Defender for IoT

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

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. a helyszíni felügyeleti konzol telepítése és beállítása

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

## <a name="4-onboard-a-sensor"></a>4. érzékelő előkészítése

Érzékelők beléptetése az Azure Defender IoT való regisztrálásával és az érzékelő aktiválási fájljának letöltésével:

1. Határozza meg az érzékelő nevét, és társítsa azt egy előfizetéshez.
1. Válasszon érzékelő-felügyeleti módot:

   - **Felhőalapú csatlakoztatott érzékelők**: az érzékelők észlelésére szolgáló információk az érzékelő konzolon jelennek meg. Emellett a riasztási adatokat egy IoT hub továbbítja, és más Azure-szolgáltatásokkal, például az Azure Sentinelrel is megosztható.

   - **Helyileg felügyelt érzékelők**: az érzékelők észlelésére szolgáló információk az érzékelő konzolon jelennek meg. Ha gapped hálózaton dolgozik, és szeretné, hogy a több helyileg felügyelt érzékelők által észlelt összes információ egységes legyen, működjön együtt a helyszíni felügyeleti konzollal. 

1. Töltse le az érzékelő aktiválási fájlját.

További információkért lásd: érzékelők beléptetése [és kezelése a Defender for IoT portálon](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. az érzékelő telepítése és beállítása

Töltse le az ISO-csomagot az Azure Defender for IoT portálról, telepítse a szoftvert, és állítsa be az érzékelőt.

1. Válassza a **első lépések** lehetőséget a Defender for IoT portálon.
1. Válassza az **érzékelő beállítása** lehetőséget.
1. Válasszon egy verziót, és válassza a **Letöltés** lehetőséget.
1. Telepítse az érzékelő szoftverét. További információ: [Defender for IoT telepítés](how-to-install-software.md).
1. Aktiválja és állítsa be az érzékelőt. További információ: [Bejelentkezés és az érzékelő aktiválása](how-to-activate-and-set-up-your-sensor.md).

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. érzékelők összekapcsolása helyszíni felügyeleti konzolhoz

Kapcsolja össze az érzékelőket a felügyeleti konzolon, és győződjön meg arról, hogy:

- Az érzékelők a riasztási és az eszköz leltározási adatait a helyszíni felügyeleti konzolra küldik.

- A helyszíni felügyeleti konzol képes a biztonsági mentések elvégzésére, a riasztások kezelésére, az érzékelők észlelésére, az érzékelő leválasztásának kivizsgálására és a csatlakoztatott érzékelőkkel kapcsolatos egyéb tevékenységek végrehajtására.

Azt javasoljuk, hogy több érzékelőt is egy zónában ugyanazokat a hálózatokat figyelje. Ezzel egyesítheti a több érzékelő által gyűjtött információkat.

További információ: [érzékelők összekapcsolása a helyszíni felügyeleti konzolon](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. az Azure Sentinel feltöltése riasztási információkkal (opcionális)

Riasztási információk küldése az Azure Sentinelnek az Azure Sentinel konfigurálásával. Lásd: [adatok összekötése a defenderből a IoT az Azure Sentinel](how-to-configure-with-sentinel.md)szolgáltatásba.

## <a name="see-also"></a>Lásd még

- [Üdvözli a IoT készült Azure Defender!](overview.md)

- [Azure Defender a IoT architektúrához](architecture.md)
