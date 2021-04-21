---
title: A Azure Defender for IoT
description: Ez a cikk a Defender for IoT legújabb kiadásának újdonságát teszi elérhetővé.
ms.topic: overview
ms.date: 04/19/2021
ms.openlocfilehash: da5358ccf0f69ca2ba8f5722b75889b6b7c92c07
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752605"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>A Azure Defender for IoT újdonsága

Ez a cikk a Defender for IoT új funkcióit és funkcióinak fejlesztéseit sorolja fel.

A feljegyzett funkciók előzetes verzióban érhetőek el. Az [Azure előzetes verzió kiegészítő](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) feltételei további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkoznak.

## <a name="april-2021"></a>2021. április

### <a name="work-with-automatic-threat-intelligence-updates-public-preview"></a>Automatikus fenyegetésintelligencia-frissítések (nyilvános előzetes verzió)

A Microsoft Defender for IoT által kiadott új fenyegetésintelligencia-csomagok mostantól automatikusan lekértek a felhőhöz csatlakoztatott érzékelőkbe. Ez a fenyegetésintelligencia-csomagok letöltését, majd az érzékelőkbe való feltöltését is tartalmazza.

Az automatikus frissítésekkel való munka csökkenti a működési erőfeszítéseket, és nagyobb biztonságot nyújt. Az automatikus frissítés engedélyezéséhez a felhőhöz csatlakoztatott érzékelőt a Defender for IoT portálon kell engedélyezni, bekapcsolva az **Automatikus** fenyegetésintelligencia-frissítések kapcsolót.

Ha ennél is óvatosabb megközelítést szeretne alkalmazni a fenyegetés-felderítési adatok frissítéséhez, manuálisan is leküldhet csomagokat a Azure Defender for IoT Portalról a felhőhöz csatlakoztatott érzékelőkbe, ha úgy érzi, hogy szükség van rá.
Ez lehetővé teszi annak szabályozását, hogy egy csomag mikor van telepítve anélkül, hogy le kellene töltenie, majd fel kellene töltenie azt az érzékelőkre. Az érzékelők frissítéseinek manuális leküldése a Defender for IoT Sites and Sensors (Defender ioT-helyekhez és **-érzékelőkhöz) lapról.**

A fenyegetésintelligencia-csomagokkal kapcsolatos alábbi információkat is áttekintheti:

- Telepített csomagverzió
- Fenyegetésintelligencia frissítési módja 
- Fenyegetésintelligencia frissítési állapota

### <a name="view-cloud-connected-sensor-information-public-preview"></a>Felhőhöz csatlakoztatott érzékelő információinak megtekintése (nyilvános előzetes verzió)

Tekintse meg a felhőhöz csatlakoztatott érzékelőkkel kapcsolatos fontos üzemeltetési információkat a **Helyek és érzékelők oldalon.**

- Az érzékelő telepített verziója
- Az érzékelő és a felhő kapcsolati állapota.
- Az utolsó alkalom, amikor a rendszer észlelte, hogy az érzékelő a felhőhöz csatlakozott.

### <a name="alert-api-enhancements"></a>A Alert API fejlesztései

Új mezők érhetők el a riasztási API-kat használó felhasználók számára.

**Helyszíni felügyeleti konzol**

- Forrás- és célcím
- Megoldási lépések
- A felhasználó által definiált érzékelő neve
- Az érzékelőhöz társított zóna neve 
- Az érzékelőhöz társított webhely neve

**Érzékelő**

- Forrás- és célcím
- Megoldási lépések

Az új mezőkkel való munka esetén az API 2-es verziójára van szükség.

### <a name="features-delivered-as-generally-available-ga"></a>Általánosan elérhetőként (GA) elérhető funkciók

A következő funkciók korábban elérhetők voltak a nyilvános előzetes verzióhoz, és mostantól általánosan elérhető (GA) funkciók:

- Érzékelő – továbbfejlesztett egyéni riasztási szabályok
- Helyszíni felügyeleti konzol – riasztások exportálása
- Második hálózati adapter hozzáadása a helyszíni felügyeleti konzolhoz
- Eszközszerkesztő – új mikroügynök

## <a name="march-2021"></a>2021. március

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Érzékelő – továbbfejlesztett egyéni riasztási szabályok (nyilvános előzetes verzió)

Most már létrehozhat egyéni riasztási szabályokat a nap, a napcsoport és az időszak hálózati tevékenységének észlelése alapján.  A napi és idő szabályokra vonatkozó feltételek hasznosak, például olyan esetekben, amikor a riasztás súlyosságát a riasztási esemény beeseményének ideje alapján adhatja meg. Létrehozhat például egy egyéni szabályt, amely nagy súlyosságú riasztást aktivál, ha a rendszer egy hétvégen vagy este hálózati tevékenységet észlel.

Ez a funkció a 10.2-es verziójú érzékelőn érhető el.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Helyszíni felügyeleti konzol – riasztások exportálása (nyilvános előzetes verzió)

A riasztási adatok mostantól exportálhatók egy .csv-fájlba a helyszíni felügyeleti konzolról. A szűrt nézet alapján exportálhatja az összes észlelt riasztás adatait, vagy exportálhatja az adatokat.

Ez a funkció a helyszíni felügyeleti konzolon, a 10.2-es verzióval érhető el.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Második hálózati adapter hozzáadása a helyszíni felügyeleti konzolhoz (nyilvános előzetes verzió)

Most már javíthatja az üzemelő példány biztonságát, ha hozzáad egy második hálózati adaptert a helyszíni felügyeleti konzolhoz. Ezzel a funkcióval a helyszíni felügyelet egyetlen biztonságos hálózaton csatlakoztathatja az érzékelőket, a felhasználók pedig egy második külön hálózati adapteren keresztül férhetnek hozzá a helyszíni felügyeleti konzolhoz.

Ez a funkció a helyszíni felügyeleti konzolon, a 10.2-es verzióval érhető el.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Második hálózati adapter hozzáadása a helyszíni felügyeleti konzolhoz (nyilvános előzetes verzió)

Most már javíthatja az üzemelő példány biztonságát, ha hozzáad egy második hálózati adaptert a helyszíni felügyeleti konzolhoz. Ezzel a funkcióval a helyszíni felügyelet egyetlen biztonságos hálózaton csatlakoztathatja az érzékelőket, a felhasználók pedig egy második külön hálózati adapteren keresztül férhetnek hozzá a helyszíni felügyeleti konzolhoz.

Ez a funkció a helyszíni felügyeleti konzolon, a 10.2-es verzióval érhető el.
### <a name="device-builder---new-micro-agent-public-preview"></a>Eszközszerkesztő – új mikroügynök (nyilvános előzetes verzió)

Elérhető egy új eszközszerkesztő modul. A modul, más néven a mikroügynök a következőt teszi lehetővé:

- **Integráció a Azure IoT Hub** és Azure Defender for IoT – erősebb végpontbiztonságot építhet be közvetlenül az IoT-eszközeibe, ha integrálja azt az Azure IoT Hub és a Azure Defender for IoT.
- Rugalmas üzembehelyezési lehetőségek standard **IoT** operációs rendszerek támogatásával – bináris csomagként vagy módosítható forráskódként telepíthetők a standard IoT operációs rendszerek, például a Linux és a Azure RTOS.
- **Minimális erőforrás-követelmények operációsrendszer-kernelfüggőségek** nélkül – kis erőforrásigény, alacsony CPU-használat és operációsrendszer-kernelfüggőségek nélkül.
- **Biztonsági állapotkezelés** – proaktívan monitorja az IoT-eszközök biztonsági állását.
- **Folyamatos, valós idejű IoT/OT** fenyegetésészlelés – olyan fenyegetések észlelése, mint a botnetek, a találgatásos kísérletek, a kriptográfiai minimumok és a gyanús hálózati tevékenységek

Az elavult Defender-IoT-micro-agent dokumentáció átkerül az eszközkészítők ügynökalapú megoldásába, *és>klasszikus mappába.*

Ez a funkciókészlet az aktuális nyilvános előzetes felhőbeli kiadással érhető el.

## <a name="january-2021"></a>2021. január

- [Biztonság](#security)
- [Előkészítés](#onboarding)
- [Használhatóság](#usability)
- [Egyéb frissítések](#other-updates)
### <a name="security"></a>Biztonság

Ebben a kiadásban továbbfejlesztettünk tanúsítványokat és jelszavakat.

#### <a name="certificates"></a>Tanúsítványok
  
Ez a verzió a következőt teszi lehetővé:

- Töltse fel az SSL-tanúsítványokat közvetlenül az érzékelőkre és a helyszíni felügyeleti konzolokra.
- Végezzen ellenőrzést a helyszíni felügyeleti konzol és a csatlakoztatott érzékelők, valamint a felügyeleti konzol és a magas rendelkezésre állású felügyeleti konzol között. Az érvényesítés alapja a lejárati dátum, a hitelesítésszolgáltatói főtanúsítvány hitelessége és a visszavont tanúsítványok listája.  Ha az érvényesítés sikertelen, a munkamenet nem folytatódik.

Frissítések esetén:

- A frissítés során az SSL-tanúsítvány és az érvényesítési funkció nem változik.
- A frissítés után az érzékelő és a helyszíni felügyeleti konzol rendszergazdai felhasználói lecserélhetik az SSL-tanúsítványokat, vagy aktiválhatja az SSL-tanúsítványérvényesítést a Rendszerbeállítások, SSL-tanúsítvány ablakban.  

Friss telepítések esetén:

- Az első bejelentkezés során a felhasználóknak SSL-tanúsítványt (ajánlott) vagy helyileg létrehozott önaírt tanúsítványt kell használniuk (nem ajánlott)
- A tanúsítványérvényesítés a friss telepítések esetén alapértelmezés szerint be van kapcsolva.

#### <a name="password-recovery"></a>Jelszó-helyreállítás
  
Az érzékelő és a helyszíni felügyeleti konzol A rendszergazda felhasználók mostantól helyreállíthatják a jelszavakat a Azure Defender for IoT portálról. Korábban a jelszó-helyreállításhoz a támogatási csapatnak kellett beavatkozást igényelni.

### <a name="onboarding"></a>Előkészítés

#### <a name="on-premises-management-console---committed-devices"></a>Helyszíni felügyeleti konzol – lekötött eszközök

A helyszíni felügyeleti konzolba való kezdeti bejelentkezés után a felhasználóknak most fel kell tölteniük egy aktiválási fájlt. A fájl a szervezeti hálózaton figyelendő eszközök összesített számát tartalmazza. Ezt a számot a lekötött eszközök számának nevezzük.
A lekötött eszközöket a rendszer a portálon, az aktiválási fájlt generáló Azure Defender for IoT során definiálja.
Az aktiválási fájl feltöltéséhez először frissíteni kell a felhasználókat és felhasználókat.
A kezdeti aktiválás után a hálózaton észlelt eszközök száma meghaladhatja a lekötött eszközök számát. Ez az esemény például akkor fordulhat elő, ha több érzékelőt csatlakoztat a felügyeleti konzolhoz. Ha eltérés van az észlelt eszközök száma és a lekötött eszközök száma között, egy figyelmeztetés jelenik meg a felügyeleti konzolon. Ha ez az esemény bekövetkezik, töltsön fel egy új aktiválási fájlt.

#### <a name="pricing-page-options"></a>Díjszabási lap beállításai

A díjszabási oldalon új előfizetéseket is Azure Defender for IoT a hálózaton található, lekötött eszközök meghatározásához.  
Emellett a Díjszabás lapon kezelheti az érzékelőhöz társított meglévő előfizetéseket, és frissítheti az eszköz kötelezettségvállalását.

#### <a name="view-and-manage-onboarded-sensors"></a>Az alaplapi érzékelők megtekintése és kezelése

A Site and Sensors portál új oldala a következő lehetőségeket teszi lehetővé:

- Adjon meg leíró információkat az érzékelőről. Például az érzékelőhöz társított zóna vagy szabad szöveges címkék.
- Az érzékelő információinak megtekintése és szűrése. Megtekintheti például a felhőhöz csatlakoztatott vagy helyileg felügyelt érzékelők adatait, vagy megtekintheti egy adott zónában található érzékelők adatait.  

### <a name="usability"></a>Használhatóság

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel összekötők lapja

A Azure Defender for IoT adat-összekötő oldalát Azure Sentinel újratervezték. Az adat-összekötő mostantól előfizetésen, és nem IoT Hubon alapul; így az ügyfelek jobban kezelhetik a konfigurációs kapcsolatukat a Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal engedélyfrissítések  

Biztonsági olvasói és biztonsági rendszergazdai támogatás lett hozzáadva.

### <a name="other-updates"></a>Egyéb frissítések

#### <a name="access-group---zone-permissions"></a>Hozzáférési csoport – zónaengedélyek
  
A helyszíni felügyeleti konzol hozzáférésicsoport-szabályai nem tartalmazzák az adott zónához való hozzáférés megadásának beállítását. A helyeket, régiókat és üzleti egységeket használó szabályok definiálása nem változik.   A frissítés után az adott zónákhoz hozzáférést engedélyező szabályokat tartalmazó hozzáférési csoportok úgy módosulnak, hogy engedélyezték a hozzáférést a szülőhelyhez, beleértve annak összes zónáját is.

#### <a name="terminology-changes"></a>Terminológiai változások

Az eszköz kifejezés az érzékelő és a helyszíni felügyeleti konzolon, jelentések és más megoldásillesztők használatával lett átnevezve.
Az érzékelő és a helyszíni felügyeleti konzol Riasztások területén az Esemény kezelése kifejezés neve Szervizelési lépések.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés a Defender for IoT-val](getting-started.md)
