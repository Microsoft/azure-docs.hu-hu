---
title: A Azure Defender for IoT
description: Ez a cikk a Defender for IoT legújabb kiadásának újdonságát teszi elérhetővé.
ms.topic: overview
ms.date: 4/6/2021
ms.openlocfilehash: df6a43dc68acd025b1c65877c65d1b7e947f210b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739384"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>A Azure Defender for IoT újdonsága

Ez a cikk a Defender for IoT új funkcióit és funkcióinak fejlesztéseit sorolja fel.

A feljegyzett funkciók előzetes verzióban érhetőek el. Az [Azure előzetes verzió kiegészítő](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) feltételei további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkoznak.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Verziószámozás és támogatás Azure Defender for IoT 

Alább látható a Defender for IoT támogatása, a használszavasztás-változtatási szabályzatok, valamint a Azure Defender for IoT elérhető verziói. 

### <a name="servicing-information-and-timelines"></a>Karbantartási információk és ütemtervek 

A Microsoft azt tervezi, hogy Azure Defender for IoT legalább negyedévente egyszer kiadja a frissítéseket. A Azure Defender for IoT érzékelő és a helyszíni felügyeleti konzolok minden általánosan elérhető (GA) verziója a kiadást követően legfeljebb kilenc hónapig támogatott. Javítások és új funkciók lesznek alkalmazva a jelenleg támogatott aktuális ga ga verzióra, és nem lesznek alkalmazva a régebbi GA verziókra.

### <a name="versions-and-support-dates"></a>Verziók és támogatási dátumok

| Verzió | A felszabadítás dátuma | Támogatás végének dátuma |
|--|--|--|
| 10,0 | 01/2021 | 10/2021 |
## <a name="march-2021"></a>2021. március

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Érzékelő – továbbfejlesztett egyéni riasztási szabályok (nyilvános előzetes verzió)

Most már létrehozhat egyéni riasztási szabályokat a nap, a napcsoport és az időszak hálózati tevékenységének észlelése alapján.  A napi és idő szabályokra vonatkozó feltételek hasznosak, például olyan esetekben, amikor a riasztás súlyosságát a riasztási esemény beeseményének ideje alapján adhatja meg. Létrehozhat például egy egyéni szabályt, amely nagy súlyosságú riasztást vált ki, ha a rendszer a hálózati tevékenységet hétvégen vagy este észleli.

Ez a funkció a 10.2-es verziójú érzékelőn érhető el.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Helyszíni felügyeleti konzol – riasztások exportálása (nyilvános előzetes verzió)

A riasztási adatok mostantól exportálhatók egy .csv-fájlba a helyszíni felügyeleti konzolról. Exportálhatja az összes észlelt riasztás adatait, vagy exportálhatja a szűrt nézet alapján.

Ez a funkció a helyszíni felügyeleti konzolon, a 10.2-es verzió kiadásával érhető el.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Második hálózati adapter hozzáadása a helyszíni felügyeleti konzolhoz (nyilvános előzetes verzió)

Most már javíthatja az üzemelő példány biztonságát, ha hozzáad egy második hálózati adaptert a helyszíni felügyeleti konzolhoz. Ez a funkció lehetővé teszi, hogy a helyszíni felügyelet egy biztonságos hálózaton csatlakoztatasa az érzékelőket, a felhasználók pedig egy második külön hálózati adapteren keresztül férnek hozzá a helyszíni felügyeleti konzolhoz.

Ez a funkció a helyszíni felügyeleti konzolon, a 10.2-es verzió kiadásával érhető el.
### <a name="device-builder---new-micro-agent-public-preview"></a>Eszközszerkesztő – új mikroügynök (nyilvános előzetes verzió)

Elérhető egy új eszközszerkesztő modul. A modul, más néven a mikroügynök a következőt teszi lehetővé:

- **Integráció a Azure IoT Hub** és Azure Defender for IoT – erősebb végpontbiztonságot építhet be közvetlenül az IoT-eszközeibe, ha integrálja azt az Azure IoT Hub és a Azure Defender for IoT.
- **Rugalmas üzembehelyezési** lehetőségek standard IoT operációs rendszerek támogatásával – bináris csomagként vagy módosítható forráskódként telepíthetők a standard IoT operációs rendszerek, például a Linux és a Azure RTOS.
- **Minimális erőforrás-követelmények operációsrendszer-kernelfüggőségek** nélkül – kis erőforrásigény, alacsony CPU-használat és operációsrendszer-kernelfüggőségek nélkül.
- **Biztonsági állapotkezelés** – proaktívan monitorja az IoT-eszközök biztonsági rendszerét.
- **Folyamatos, valós idejű IoT/OT** fenyegetésészlelés – olyan fenyegetések észlelése, mint a botnetek, a találgatásos kísérletek, a kriptográfiai minánok és a gyanús hálózati tevékenységek

Az elavult Defender-IoT-micro-agent dokumentáció átkerül az Eszközkészítők ügynökalapú *megoldásába, és>klasszikus mappába.*

Ez a funkciókészlet a jelenlegi nyilvános előzetes felhőalapú kiadással érhető el.

## <a name="january-2021"></a>2021. január

- [Biztonság](#security)
- [Előkészítés](#onboarding)
- [Használhatóság](#usability)
- [Egyéb frissítések](#other-updates)
### <a name="security"></a>Biztonság

Ebben a kiadásban továbbfejlesztettünk tanúsítványokat és jelszó-helyreállítási funkciókat.

#### <a name="certificates"></a>Tanúsítványok
  
Ez a verzió a következőt teszi lehetővé:

- Töltse fel az SSL-tanúsítványokat közvetlenül az érzékelőkre és a helyszíni felügyeleti konzolokra.
- Hajtsa végre az ellenőrzést a helyszíni felügyeleti konzol és a csatlakoztatott érzékelők, valamint a felügyeleti konzol és a magas rendelkezésre állású felügyeleti konzol között. Az érvényesítés alapja a lejárati dátum, a legfelső szintű hitelesítésszolgáltató hitelessége és a visszavont tanúsítványok listái.  Ha az érvényesítés sikertelen, a munkamenet nem folytatódik.

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

Miután bejelentkezik a helyszíni felügyeleti konzolra, a felhasználóknak most fel kell tölteniük egy aktiválási fájlt. A fájl a szervezeti hálózaton figyelendő eszközök összesített számát tartalmazza. Ezt a számot a lekötött eszközök számának nevezzük.
A lekötött eszközöket a portálon, ahol az aktiválási fájl létrejön, a Azure Defender for IoT meg kell határozni.
Az aktiválási fájl feltöltéséhez először frissíteni kell a felhasználókat és a felhasználókat.
A kezdeti aktiválás után a hálózaton észlelt eszközök száma meghaladhatja a lekötött eszközök számát. Ez az esemény például akkor fordulhat elő, ha több érzékelőt csatlakoztat a felügyeleti konzolhoz. Ha eltérés van az észlelt eszközök és a lekötött eszközök száma között, egy figyelmeztetés jelenik meg a felügyeleti konzolon. Ha ez az esemény bekövetkezik, töltsön fel egy új aktiválási fájlt.

#### <a name="pricing-page-options"></a>Díjszabási lap beállításai

A díjszabási oldalon új előfizetéseket is Azure Defender for IoT és meghatározhat a hálózaton található, lekötött eszközöket.  
Emellett a Díjszabás lapon mostantól kezelheti az érzékelőhöz társított meglévő előfizetéseket, és frissítheti az eszköz kötelezettségvállalását.

#### <a name="view-and-manage-onboarded-sensors"></a>Az alaplapi érzékelők megtekintése és kezelése

A Site and Sensors portál új oldala a következő lehetőségeket teszi lehetővé:

- Adjon meg leíró információkat az érzékelőről. Például egy, az érzékelőhöz társított zónát vagy szabad szöveges címkéket.
- Az érzékelő információinak megtekintése és szűrése. Megtekintheti például a felhőhöz csatlakoztatott vagy helyileg felügyelt érzékelők adatait, vagy megtekintheti egy adott zónában található érzékelők adatait.  

### <a name="usability"></a>Használhatóság

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel összekötők lapja

A Azure Defender for IoT adat-összekötő oldalát Azure Sentinel újratervezték. Az adat-összekötő mostantól előfizetésen, és nem IoT Hubon alapul; így az ügyfelek jobban kezelhetik a konfigurációs kapcsolatukat a Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal engedélyfrissítések  

A biztonsági olvasó és a biztonsági rendszergazda támogatása ki lett adva.

### <a name="other-updates"></a>Egyéb frissítések

#### <a name="access-group---zone-permissions"></a>Hozzáférési csoport – zónaengedélyek
  
A helyszíni felügyeleti konzol hozzáférésicsoport-szabályai nem tartalmazzák az adott zónához való hozzáférés megadásának beállítását. A helyeket, régiókat és üzleti egységeket használó szabályok definiálása nem változik.   A frissítés után az adott zónákhoz hozzáférést engedélyező szabályokat tartalmazó hozzáférési csoportok úgy módosulnak, hogy engedélyezték a szülőhelyhez való hozzáférést, beleértve annak összes zónáját is.

#### <a name="terminology-changes"></a>Terminológiai változások

Az eszköz kifejezés az érzékelő és a helyszíni felügyeleti konzolon, a jelentésekben és más megoldásillesztőkben lett átnevezve.
Az érzékelő és a helyszíni felügyeleti konzol Riasztások területén az Esemény kezelése kifejezés neve Szervizelési lépések.

## <a name="next-steps"></a>Következő lépések

[A Defender for IoT első lépések](getting-started.md)
