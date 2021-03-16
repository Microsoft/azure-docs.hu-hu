---
title: Az Azure Defender IoT újdonságai
description: Ebből a cikkből megtudhatja, hogy a Defender legújabb kiadásának újdonságai a IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2021
ms.author: shhazam
ms.openlocfilehash: ef72be60b6294ad4e1fca2ce9c0e3c66b64ac687
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493957"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Az Azure Defender IoT újdonságai

Ez a cikk felsorolja a Defender új szolgáltatásait és funkcióinak fejlesztéseit a IoT.

A feljegyzett funkciók előzetes verzióban érhetők el. Az [Azure előzetes verziójának kiegészítő feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak az olyan Azure-funkciókra vonatkozó további jogi feltételek, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelennek meg az általánosan elérhetővé tételben.
## <a name="march-2021"></a>Március 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Érzékelő – bővített egyéni riasztási szabályok (nyilvános előzetes verzió)

Mostantól létrehozhat egyéni riasztási szabályokat a nap alapján, a napok csoportját és az időpontot hálózati tevékenységet észlelve.  A nap-és időszabályi feltételek használata hasznos lehet, például azokban az esetekben, amikor a riasztás súlyossága a riasztási esemény időpontjában van származtatva. Létrehozhat például egy olyan egyéni szabályt, amely magas súlyosságú riasztást indít el, ha egy hétvégén vagy esténként a hálózati tevékenységet észlel.

Ez a funkció az érzékelőn érhető el az 10,2-es verzió kiadásával.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Helyszíni felügyeleti konzol – riasztások exportálása (nyilvános előzetes verzió)

A riasztási adatok mostantól egy. csv-fájlba exportálhatók a helyszíni felügyeleti konzolról. Exportálhatja az összes észlelt riasztás adatait, vagy exportálhatja az adatokat a szűrt nézet alapján.

Ez a szolgáltatás a helyszíni felügyeleti konzolon érhető el, az 10,2-es verzió kiadásával.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Második hálózati adapter hozzáadása a helyszíni felügyeleti konzolhoz (nyilvános előzetes verzió)

Most már javíthatja a központi telepítés biztonságát úgy, hogy hozzáad egy második hálózati adaptert a helyszíni felügyeleti konzolhoz. Ez a funkció lehetővé teszi, hogy a helyszíni felügyelet egy biztonságos hálózaton lévő csatlakoztatott érzékelőkkel rendelkezzen, miközben lehetővé teszi a felhasználók számára, hogy egy második különálló hálózati adapteren keresztül hozzáférjenek a helyszíni felügyeleti konzolhoz.

Ez a szolgáltatás a helyszíni felügyeleti konzolon érhető el, az 10,2-es verzió kiadásával.
### <a name="device-builder---new-micro-agent-public-preview"></a>Device Builder – új Micro Agent (nyilvános előzetes verzió)

Új Eszközkezelő modul érhető el. A modul, amelyet a Micro-Agent is nevez, lehetővé teszi a következőket:

- **Integráció az azure IoT hub és az Azure Defender for IoT** használatával – az Azure IoT hub és az Azure Defender for IoT által biztosított figyelési lehetőséggel integrálva közvetlenül a IoT-eszközökön is erősebb végponti biztonságot hozhat létre.
- A **standard szintű IoT operációs rendszerek támogatásával rugalmas üzembe helyezési lehetőségek** használhatók bináris csomagként vagy módosítható forráskódként, a szabványos IoT operációs rendszerek, például a Linux és az Azure RTOS támogatásával.
- **Minimális erőforrás-követelmények az operációsrendszer-kernel függőségei nélkül** – kis helyigény, alacsony CPU-fogyasztás, és nincs operációsrendszer-kernel függőségei.
- **Biztonsági állapot kezelése** – proaktív módon figyeli a IoT-eszközök biztonsági állapotát.
- **Folyamatos, valós idejű IoT/s veszélyforrások észlelése** – olyan fenyegetések észlelése, mint például a botnetek, a találgatásos támadások, a kriptográfiai bányászok és a gyanús hálózati tevékenységek

Az elavult Defender-IoT-Micro-Agent dokumentáció át lesz helyezve az *ügynök-alapú megoldásba az eszköz-építők számára>klasszikus* mappába.

Ez a szolgáltatáskészlet a jelenleg elérhető nyilvános előzetes verziójú felhőben érhető el.

## <a name="january-2021"></a>2021. január

- [Biztonság](#security)
- [Előkészítés](#onboarding)
- [Használhatóság](#usability)
- [Egyéb frissítések](#other-updates)
### <a name="security"></a>Biztonság

Ehhez a kiadáshoz a tanúsítvány-és jelszó-helyreállítási fejlesztések történtek.

#### <a name="certificates"></a>Tanúsítványok
  
Ez a verzió a következőket teszi lehetővé:

- Az SSL-tanúsítványok feltöltése közvetlenül az érzékelőkre és a helyszíni felügyeleti konzolokra.
- Ellenőrzés végrehajtása a helyszíni felügyeleti konzol és a csatlakoztatott érzékelők között, valamint egy felügyeleti konzol és egy magas rendelkezésre állású felügyeleti konzol között. Az érvényesítés a lejárati dátumok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ hitelessége és a visszavont tanúsítványok listája alapján történik.  Ha az érvényesítés sikertelen, a munkamenet nem folytatódik.

Frissítések esetén:

- A frissítés során nem módosul az SSL-tanúsítvány vagy az érvényesítési funkció.
- A frissítés után az érzékelő és a helyszíni felügyeleti konzol rendszergazda felhasználók lecserélhetik az SSL-tanúsítványokat, vagy aktiválják az SSL-tanúsítvány érvényesítését a rendszerbeállítások, az SSL-tanúsítvány ablakában.  

Új telepítések esetén:

- Az első bejelentkezés során a felhasználóknak SSL-tanúsítványt (ajánlott) vagy helyileg generált önaláírt tanúsítványt kell használniuk (nem ajánlott)
- A tanúsítvány érvényesítése alapértelmezés szerint be van kapcsolva a friss telepítések esetében.

#### <a name="password-recovery"></a>Jelszó-helyreállítás
  
Az érzékelő és a helyszíni felügyeleti konzol a rendszergazda felhasználók mostantól helyreállíthatók az Azure Defender for IoT portálon elérhető jelszavak. Korábban a jelszó-helyreállításhoz a támogatási csapat beavatkozás szükséges.

### <a name="onboarding"></a>Előkészítés

#### <a name="on-premises-management-console---committed-devices"></a>Helyszíni felügyeleti konzol – véglegesített eszközök

A helyszíni felügyeleti konzolra való első bejelentkezés után a felhasználóknak most már szükségük van egy aktiválási fájl feltöltésére. A fájl tartalmazza a szervezeti hálózaton figyelni kívánt eszközök összesített számát. Ezt a számot a véglegesített eszközök számának nevezzük.
A véglegesített eszközök a bevezetési folyamat során vannak meghatározva az Azure Defender for IoT portálon, ahol létrejön az aktiválási fájl.
Az aktiválási fájl feltöltéséhez először a felhasználók és a felhasználók frissítésére van szükség.
A kezdeti aktiválás után a hálózaton észlelt eszközök száma túllépheti a véglegesített eszközök számát. Ez az esemény például akkor fordulhat elő, ha több érzékelőt csatlakozik a felügyeleti konzolhoz. Ha az észlelt eszközök száma és a véglegesített eszközök száma között eltérés tapasztalható, a felügyeleti konzolon megjelenik egy figyelmeztetés. Ha ez az esemény történik, fel kell töltenie egy új aktiválási fájlt.

#### <a name="pricing-page-options"></a>Díjszabási oldal beállításai

A díjszabási oldalon új előfizetéseket hozhat létre az Azure Defender IoT, és meghatározhatja a hálózatban lévő véglegesített eszközöket.  
Emellett a díjszabási oldal segítségével kezelheti az érzékelőhöz társított meglévő előfizetéseket, és frissítheti az eszközre vonatkozó kötelezettségvállalást.

#### <a name="view-and-manage-onboarded-sensors"></a>Beépített érzékelők megtekintése és kezelése

Az új hely és érzékelők portál oldalon A következőket teheti:

- Leíró információk hozzáadása az érzékelőhöz. Például az érzékelőhöz társított zóna, vagy a szabad szöveges címkék.
- Az érzékelő adatainak megtekintése és szűrése. Például megtekintheti a felhőhöz csatlakoztatott vagy helyileg felügyelt érzékelők adatait, vagy megtekintheti az érzékelőkre vonatkozó információkat egy adott zónában.  

### <a name="usability"></a>Használhatóság

#### <a name="azure-sentinel-new-connector-page"></a>Az Azure Sentinel új összekötő lapja

Az Azure Defender for IoT adatösszekötő lapja az Azure Sentinelben újratervezve. Az adatösszekötő mostantól a IoT hubok helyett előfizetéseken alapul. lehetővé teszi, hogy az ügyfelek jobban kezeljék az Azure Sentinelhez való konfigurációs kapcsolataikat.

#### <a name="azure-portal-permission-updates"></a>Azure Portal engedélyek frissítései  

A biztonsági olvasó és a biztonsági rendszergazdai támogatás hozzá lett adva.

### <a name="other-updates"></a>Egyéb frissítések

#### <a name="access-group---zone-permissions"></a>Hozzáférési csoport – zóna engedélyei
  
A helyszíni felügyeleti konzol hozzáférési csoportjának szabályai nem tartalmazzák az adott zónához való hozzáférés engedélyezésének lehetőségét. Nem változik a helyek, régiók és üzleti egységek használatát használó szabályok meghatározása.   A frissítés után a megadott zónákhoz hozzáférést biztosító szabályokat tartalmazó hozzáférési csoportok módosulnak, hogy engedélyezze a hozzáférést a fölérendelt helyéhez, beleértve az összes zónáját.

#### <a name="terminology-changes"></a>Terminológiai változások

Az eszköz kifejezése átnevezve lett az eszközre az érzékelő és a helyszíni felügyeleti konzolon, a jelentésekben és az egyéb megoldási felületeken.
Az érzékelő és a helyszíni felügyeleti konzol riasztásai esetében az eseményt kezelő kifejezés szervizelési lépések elnevezésű.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés a Defender IoT-vel való használatába](getting-started.md)
