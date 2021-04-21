---
title: 'Rövid útmutató: Első lépések'
description: Ebből a rövid útmutatóból megtudhatja, hogyan ismerkedhat meg a Defender for IoT üzembe helyezésének alapvető munkafolyamatával.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750445"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Rövid útmutató: A Defender for IoT első lépések

Ez a cikk áttekintést nyújt a szolgáltatás beállításának Azure Defender for IoT. A folyamathoz a következő szükséges:

- Regisztrálja előfizetését és érzékelőit a Azure Defender for IoT portálon.
- Telepítse az érzékelőt és a helyszíni felügyeleti konzol szoftverét.
- Végezze el az érzékelő és a felügyeleti konzol kezdeti aktiválását.

## <a name="permission-requirements"></a>Engedélykövetelmények

Egyes beállítási lépésekhez adott felhasználói engedélyekre van szükség.

Az érzékelő és a felügyeleti konzol aktiválásához, SSL-/TLS-tanúsítványok feltöltéséhez és új jelszavak létrehozásához rendszergazdai jogosultságok szükségesek.

Az alábbi táblázat a portál eszközeinek felhasználói Azure Defender for IoT engedélyeket ismerteti:

| Engedély | Biztonsági olvasó | Biztonsági rendszergazda | Előfizetés közreműködője | Előfizetés tulajdonosa |
|--|--|--|--|--|
| Részletek megtekintése és a szoftverek, aktiválási fájlok és fenyegetésintelligencia-csomagok elérése  | ✓ | ✓ | ✓ | ✓ |
| Érzékelő be- és berekeszi  |  |  ✓ | ✓ | ✓ |
| Díjszabás frissítése  |  |  ✓ | ✓ | ✓ |
| Jelszó helyreállítása  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>A megoldás infrastruktúrájának azonosítása

**A hálózat beállítási igényeinek tisztázása**

A hálózati architektúra, a figyelt sávszélesség és egyéb hálózati adatok kutatása. További információ: [About Azure Defender for IoT network setup](how-to-set-up-your-network.md)(A hálózati Azure Defender for IoT beállítása).

**A hálózati terhelés kezeléséhez szükséges érzékelők és felügyeleti konzolkészülékek tisztázása**

Azure Defender for IoT fizikai és virtuális üzemelő példányok is támogatottak. A fizikai üzembe helyezéshez különböző tanúsított berendezéseket vásárolhat. További információ: [A szükséges berendezések azonosítása.](how-to-identify-required-appliances.md)

Javasoljuk, hogy számítsa ki a figyelt eszközök hozzávetőleges számát. Később, amikor regisztrálja Azure-előfizetését a portálon, meg kell adnia ezt a számot. A számok 1000 másodperces időközönként hozzáadhatóak. A figyelt eszközök számát lekötött *eszközöknek nevezzük.*

## <a name="register-with-azure-defender-for-iot"></a>Regisztráció a Azure Defender for IoT

A regisztráció a következőket tartalmazza:

- Azure-előfizetések beállítása a Defender for IoT-hez.
- A lekötött eszközök meghatározása.
- Aktiválási fájl letöltése a helyszíni felügyeleti konzolhoz.

Regisztráció:

1. Ugrás a Azure Defender for IoT portálra.

1. Válassza **az Előfizetések előjegyzése lehetőséget.**

1. A Díjszabás **lapon válasszon** ki egy előfizetést, vagy hozzon létre egy újat, és adja hozzá a lekötött eszközök számát.

1. Válassza a **Helyszíni felügyeleti konzol letöltése** lapot, és mentse a letöltött aktiválási fájlt. Ez a fájl tartalmazza a meghatározott összesített lekötött eszközöket. A fájl a kezdeti bejelentkezést követően fel lesz töltve a felügyeleti konzolra.

További információ az előfizetések ki- és kikapcsolásról: [Előfizetések ki- és kikapcsolása.](how-to-manage-subscriptions.md#offboard-a-subscription)

## <a name="install-and-set-up-the-on-premises-management-console"></a>A helyszíni felügyeleti konzol telepítése és beállítása

A helyszíni felügyeleti konzol készülékének beszerezése után:

- Töltse le az ISO-csomagot a Azure Defender for IoT portálról.
- Telepítse a szoftvert.
- Aktiválja és végezze el a felügyeleti konzol kezdeti beállítását.

Telepítés és beállítás:

1. A **Defender** for IoT portálon válassza az Első lépések lehetőséget.
1. Válassza a **Helyszíni felügyeleti konzol lapot.**
1. Válasszon egy verziót, majd válassza a **Letöltés lehetőséget.**
1. Telepítse a helyszíni felügyeleti konzol szoftverét. További információ: [Defender for IoT installation (Defender for IoT telepítése).](how-to-install-software.md)
1. Aktiválja és állítsa be a felügyeleti konzolt. További információ: A helyszíni felügyeleti konzol [aktiválása és beállítása.](how-to-activate-and-set-up-your-on-premises-management-console.md)

## <a name="onboard-a-sensor"></a>Érzékelő be- és berekeszi ##

Regisztrálja az érzékelőt a Azure Defender for IoT és töltsön le egy érzékelőaktiválási fájlt:

1. Definiálja az érzékelő nevét, és társítsa egy előfizetéshez.
1. Válasszon egy érzékelőkapcsolati módot:

   - **Felhőhöz csatlakoztatott érzékelők:** Az érzékelők által észlelt információk megjelennek az érzékelő konzolján. Emellett a riasztási információk egy IoT Hubon keresztül lesznek kézbesítve, és megoszthatóak más Azure-szolgáltatásokkal, például a Azure Sentinel.  Választhatja azt is, hogy automatikusan lekulálja a fenyegetésintelligencia-csomagokat a Azure Defender for IoT portálról az érzékelőkre. További információ: [Veszélyforrás-felderítési kutatások és csomagok.](how-to-work-with-threat-intelligence-packages.md)

   - **Helyileg felügyelt érzékelők:** Az érzékelők által észlelt információk megjelennek az érzékelő konzolján. Ha egy levegővel eltolt hálózatban dolgozik, és szeretné egységes nézetben megtekinteni a több helyileg felügyelt érzékelő által észlelt összes információt, működjön együtt a helyszíni felügyeleti konzollal.

1. Töltsön le egy érzékelőaktiválási fájlt.

Az eszközbe való be- és berekedés részleteiért lásd: Érzékelők beállítása és kezelése [a Defender for IoT portálon.](how-to-manage-sensors-on-the-cloud.md)

## <a name="install-and-set-up-the-sensor"></a>Az érzékelő telepítése és beállítása

Töltse le az ISO-csomagot a Azure Defender for IoT portálról, telepítse a szoftvert, és állítsa be az érzékelőt.

1. A Defender for IoT **portálon** válassza az Első lépések lehetőséget.

1. Válassza **az Érzékelő beállítása lehetőséget.**

1. Válasszon egy verziót, majd válassza a **Letöltés lehetőséget.**

1. Telepítse az érzékelőszoftvert. További információ: [Defender for IoT installation (Defender for IoT telepítése).](how-to-install-software.md)

1. Aktiválja és állítsa be az érzékelőt. További információ: [Bejelentkezés és érzékelő aktiválása.](how-to-activate-and-set-up-your-sensor.md)

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Érzékelők csatlakoztatása helyszíni felügyeleti konzolhoz

Csatlakoztassa az érzékelőket a felügyeleti konzolhoz, hogy:

- Az érzékelők riasztási és eszközleltár-információkat küldenek a helyszíni felügyeleti konzolnak.

- A helyszíni felügyeleti konzol képes az érzékelők biztonsági mentésére, az érzékelők által észlelt riasztások kezelésére, az érzékelők leválasztott kapcsolatainak kivizsgálására és egyéb tevékenységek elvégzésére a csatlakoztatott érzékelőken.

Javasoljuk, hogy egy zónába csoportosan csoportosítsa az azonos hálózatokat figyelő érzékelőket. Ezzel egyesülnek a több érzékelő által gyűjtött információk.

További információ: [Érzékelők csatlakoztatása a helyszíni felügyeleti konzolhoz.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>A Azure Sentinel adatokkal való feltöltése (nem kötelező)

Riasztási információk küldése a Azure Sentinel a riasztások Azure Sentinel. Lásd: [Adatok csatlakoztatása a Defender for IoT-ről Azure Sentinel.](how-to-configure-with-sentinel.md)

## <a name="next-steps"></a>Következő lépések ##

[Üdvözli a Azure Defender for IoT](overview.md)

[Azure Defender for IoT architektúra](architecture.md)
