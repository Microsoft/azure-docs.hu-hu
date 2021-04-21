---
title: Fenyegetésintelligencia-adatok frissítése
description: A fenyegetésintelligencia-adatcsomag minden új Defender for IoT-verzióhoz elérhető, vagy szükség esetén a kiadások között.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750463"
---
# <a name="threat-intelligence-research-and-packages"></a>Fenyegetésintelligencia-kutatások és -csomagok #
## <a name="overview"></a>Áttekintés ##

A Microsoft biztonsági csapatai saját fejlesztésű ICS veszélyforrás-felderítési és biztonsági résekkel kapcsolatos kutatásokat végeznek. A csapatok közé tartozik az MSTIC (Microsoft Threat Intelligence Center), a DART (Microsoft Detection and Response Team), a DCU (Digital Crimes Unit) és az 52. szakasz (IoT/OT/ICS tartományi szakértők, akik az ICS-specifikus nulladik napokat, visszafejtési kártevőket, kampányokat és támadásokat követik nyomon)

A csapatok biztonsági észlelést, elemzést és válaszadást biztosítanak a Microsoft következő szolgáltatásaira:

- Felhőalapú infrastruktúra és szolgáltatások.
- Hagyományos termékek és eszközök.
- Belső vállalati erőforrások.

A biztonsági csapatok a következő előnyöket élvezhetik:

- Védelem az ismert és releváns fenyegetések ellen.
- Elemzések, amelyek segítenek az osztályozásban és a rangsorolásban.
- A fenyegetések teljes kontextusának ismerete azok befolyásolás előtt.
- Relevánsabb, pontosabb és használhatóbb adatok.

Ez az intelligencia környezeti információkat biztosít a Microsoft platformelemzésének gazdagítására, és támogatja a vállalat felügyelt szolgáltatásait az incidensek megoldására és a biztonsági incidensek kivizsgálására. A fenyegetésintelligencia-csomagok aláírásokat (például kártevő-aláírásokat), CVE-ket és egyéb biztonsági tartalmakat tartalmaznak.

## <a name="when-are-packages-delivered"></a>Mikor történik a csomagok kézbesítése? ##

A fenyegetésintelligencia-csomagokat havonta körülbelül egyszer, vagy szükség esetén gyakrabban biztosítjuk. Az új csomagokkal kapcsolatos bejelentések a következő ről érhetők el: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Fenyegetésintelligencia-csomagok frissítése az érzékelőkhöz ##

Három lehetőség érhető el a fenyegetésintelligencia-csomagok érzékelőkhöz való frissítéséhez:

- Csomagok automatikus leküldése az érzékelőkbe, amint a Defender for IoT kézbesíti őket.
- Szükség szerint manuálisan lekullhatja a fenyegetésintelligencia-csomagot az érzékelőkre.
- Töltsön le egy csomagot, majd töltse fel egy érzékelőre vagy több érzékelőre.

A Defender for IoT biztonsági olvasói engedélyekkel rendelkező felhasználók automatikusan és manuálisan is lekért csomagokat az érzékelőknek.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>A fenyegetésintelligencia-frissítések automatikus leküldése az érzékelőkre ###

A fenyegetésintelligencia-csomagok automatikusan frissíthetők a felhőhöz csatlakoztatott érzékelőkre, amint a Defender for IoT kiadták őket.  Ellenőrizze az automatikus csomagfrissítést a felhőhöz csatlakoztatott érzékelő bekapcsolt Automatikus fenyegetésintelligencia-frissítések **beállítással** való beállításával. További információkért lásd: [Érzékelő be- és berekesze.](getting-started.md#onboard-a-sensor)

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Fenyegetésintelligencia-frissítések manuális leküldése az érzékelőkre ###

A *felhőhöz csatlakoztatott* érzékelők automatikusan frissíthetők fenyegetésintelligencia-csomagokkal. Ha azonban ennél is inkább óvatosabb megközelítést szeretne alkalmazni, a Azure Defender for IoT Portálról csak akkor kell csomagokat leküldenie az érzékelőknek, ha úgy érzi, hogy szükség van rá.
Így anélkül szabályozhatja a csomagok telepítését, hogy le kellene töltenie, majd fel kellene töltenie az érzékelőkre.

**Csomagok manuális leküldése:**

1. Ugrás a Azure Defender for IoT és érzékelők **lapra.**
1. Válassza az érzékelő három pontját (...), majd a **Push Threat Intelligence update (Fenyegetésintelligencia-frissítés leküldése) lehetőséget.** A **Fenyegetésintelligencia frissítési állapota** mezőben megjelenik a frissítési folyamat állapota.

#### <a name="change-the-threat-intelligence-update-mode"></a>A fenyegetésintelligencia frissítési módjának módosítása ####

Az érzékelő fenyegetési felderítésének frissítési módja a kezdeti be- és leváltás után is megváltoztatható.

**A frissítési mód módosítása:**

1. Válassza az érzékelő három pontját (...), majd a **Szerkesztés lehetőséget.**
1. Engedélyezze vagy tiltsa le az **Automatikus fenyegetésintelligencia-frissítések** kapcsolót.

### <a name="download-packages-and-upload-to-sensors"></a>Csomagok letöltése és feltöltése érzékelőkbe ###

A csomagok a Defender for IoT portálról tölthetők le, és manuálisan tölthetők fel az egyes érzékelőkre. Ha a helyszíni felügyeleti konzol kezeli az érzékelőket, letöltheti a fenyegetés-felderítési csomagokat a felügyeleti konzolra, és egyszerre több érzékelőre is leküldheti őket.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Töltse le a frissítéseket a Azure Defender for IoT portálon.":::

Ez a lehetőség a felhőhöz csatlakoztatott *és* a *helyileg* felügyelt érzékelőkhöz is elérhető.

**Feltöltés egyetlen érzékelőre:**

1. Ugrás a Azure Defender for IoT **lapra.**

2. Töltse le és mentse a **fenyegetésintelligencia-csomagot.**

3. Jelentkezzen be az érzékelő konzoljára.

4. Az oldalsó menüben válassza a **Rendszerbeállítások lehetőséget.**

5. Válassza **a Fenyegetésintelligencia-adatok,** majd a **Frissítés lehetőséget.**

6. Töltse fel az új csomagot.

**Egyszerre több érzékelőre történő feltöltéshez:**

1. Ugrás a Azure Defender for IoT **lapra.**

2. Töltse le és mentse a **fenyegetésintelligencia-csomagot.**

3. Jelentkezzen be a felügyeleti konzolba.

4. Az oldalsó menüben válassza a **Rendszerbeállítások lehetőséget.**

5. A **Sensor Engine Configuration (Érzékelőmotor konfigurációja)** szakaszban válassza ki azokat az érzékelőket, amelyek a frissített csomagokat kapják meg.  

6. A **Fenyegetésintelligencia-adatok kiválasztása szakaszban** válassza a pluszjelet ( **+** ).

7. Töltse fel a csomagot.

## <a name="review-package-update-status-on-the-sensor"></a>Csomagfrissítés állapotának áttekintése az érzékelőn ##

A csomagfrissítés állapota és verzióinformációi az érzékelő Rendszerbeállítások **,** **Fenyegetésintelligencia szakaszában jelennek** meg.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Felhőhöz csatlakoztatott érzékelők csomaginformációinak áttekintése ##

Tekintse át az alábbi információkat a felhőhöz csatlakoztatott érzékelők fenyegetésiintelligencia-csomagjairól:

- Telepített csomagverzió
- Fenyegetésintelligencia frissítési módja
- Fenyegetési intelligencia frissítési állapota

A fenyegetésintelligencia-információk áttekintése:

1. Ugrás a Azure Defender for IoT és érzékelők **lapra.**
1. Tekintse át **az egyes érzékelőkre telepített** fenyegetésintelligencia-verziót. A verzióelnevezés azon a napon alapul, amikor a csomagot a Defender for IoT felépítette.
1. Tekintse át **a Fenyegetésintelligencia módot.** *Az Automatikus* beállítás azt jelzi, hogy az újonnan elérhető csomagok automatikusan telepítve lesznek az érzékelőkre, amint a Defender for IoT ki teszi azokat. *A Manuális* beállítás azt jelzi, hogy az újonnan elérhető csomagokat szükség szerint közvetlenül az érzékelőkre is lekultálhatja.
1. Tekintse át **a Fenyegetésintelligencia frissítési állapotát.** A következő állapotok jelennek meg:

- Sikertelen
- Folyamatban
- Elérhető frissítés
- oké

Ha a felhőhöz csatlakoztatott fenyegetésintelligencia-frissítések sikertelenek, tekintse át a kapcsolati adatokat a Helyek és érzékelők oldal Érzékelő **állapota** és Utolsó csatlakoztatott **UTC** **oszlopában.** 

## <a name="see-also"></a>Lásd még

[Érzékelő be- és berekesze](getting-started.md#onboard-a-sensor)

[Érzékelők kezelése a felügyeleti konzolról](how-to-manage-sensors-from-the-on-premises-management-console.md)