---
title: A hálózat beállítása
description: Ismerkedjen meg a megoldási architektúrával, a hálózati előkészítéssel, az előfeltételekkel és az egyéb információkkal, amelyek szükségesek ahhoz, hogy sikeresen beállította a hálózatot az Azure Defender IoT-készülékekkel való működéséhez.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3d5a29ed26d58f0cbaa57768d4b8ad236283deb9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523855"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Tudnivalók az Azure Defender for IoT Network telepítőről

Az Azure Defender for IoT folyamatos internetkapcsolatot biztosító veszélyforrások figyelését és észlelését teszi lehetővé. A platform a következő összetevőket tartalmazza:

**Defender a IoT érzékelőkhöz:** Az érzékelők a passzív (ügynök nélküli) figyelés használatával gyűjtik az INTERNETKAPCSOLATtal rendelkező hálózati forgalmat. A passzív és a nem befolyásolják, az érzékelők nulla teljesítménybeli hatással vannak az OT-ra és a IoT hálózatokra és eszközökre. Az érzékelő egy SPAN porthoz vagy egy hálózati KOPPINTÁShoz csatlakozik, és azonnal megkezdi a hálózat figyelését. Az észlelések az érzékelő konzolján jelennek meg. Itt megtekintheti, megvizsgálhatja és elemezheti a hálózati térképeken, az eszközök leltározásán és a jelentések széles skáláján. Ilyenek például a kockázatértékelési jelentések, az adatbányászati lekérdezések és a támadási vektorok. 

**Defender a IoT helyszíni felügyeleti konzolján**: a helyszíni felügyeleti konzol az összes hálózati eszköz összevont nézetét biztosítja. Valós idejű áttekintést nyújt a Key OT és a IoT kockázati indikátorokról és riasztásokról az összes létesítményben. Szorosan integrálva van a SOC-munkafolyamatokkal és-forgatókönyvekkel, így egyszerűen rangsorolhatja a kockázatcsökkentő tevékenységeket és a fenyegetések közötti összefüggéseket. 

**Defender a IoT-portál IoT:** A Defender for IoT alkalmazás segítséget nyújthat a megoldások megvásárlásához, a szoftverek telepítéséhez és frissítéséhez, valamint a TI csomagok frissítéséhez. 

Ez a cikk a megoldási architektúrával, a hálózati előkészítéssel, az előfeltételekkel és további információkkal segíti a hálózat sikeres beállítását a Defender IoT-készülékekkel való együttműködéshez. Az ebben a cikkben található információkkal dolgozó olvasóknak az OT és az IoT hálózatok működtetésében és kezelésében kell jártasnak lenniük. Ilyenek például az Automation-mérnökök, a berendezésgyártás, az OT hálózati infrastruktúra-szolgáltatók, a kiberbiztonsági-csapatok, a CISOs vagy a informatikai igazgatók.

Segítségért vagy támogatásért forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Helyszíni telepítési feladatok

A hely központi telepítési feladatai a következők:

- [A hely adatainak összegyűjtése](#collect-site-information)

- [Konfigurációs munkaállomás előkészítése](#prepare-a-configuration-workstation)

- [A rack telepítésének tervezése](#planning-rack-installation)

### <a name="collect-site-information"></a>A hely adatainak összegyűjtése

A hely adatainak rögzítése, például:

- Érzékelő-felügyeleti hálózati információk.

- Hely hálózati architektúrája.

- Fizikai környezet.

- Rendszerintegrációk.

- Tervezett felhasználói hitelesítő adatok.

- Konfigurációs munkaállomás.

- SSL-tanúsítványok (nem kötelező, de ajánlott).

- SMTP-hitelesítés (nem kötelező). Ha az SMTP-kiszolgálót hitelesítéssel szeretné használni, készítse elő a kiszolgálóhoz szükséges hitelesítő adatokat.

- DNS-kiszolgálók (nem kötelező). Készítse elő a DNS-kiszolgáló IP-címét és állomásnevét.

A fontos helyekkel kapcsolatos információk részletes listáját és leírását itt tekintheti meg: [példa a hely könyve](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Sikeres figyelési irányelvek

Ha szeretné megkeresni az összes éles hálózatban lévő berendezés összekapcsolásának optimális helyét, javasoljuk, hogy kövesse az alábbi eljárást:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Példa az üzemi hálózat beállítására.":::

### <a name="prepare-a-configuration-workstation"></a>Konfigurációs munkaállomás előkészítése

Készítse elő a Windows-munkaállomást, beleértve a következőket:

- Kapcsolódás az érzékelő felügyeleti felületéhez.

- Támogatott böngésző

- Terminál szoftver, például a Putty.

Győződjön meg arról, hogy a szükséges tűzfalszabályok nyitva vannak a munkaállomáson. A részletekért lásd a [hálózati hozzáférési követelmények](#network-access-requirements) című témakört.

#### <a name="supported-browsers"></a>Támogatott böngészők

A következő böngészők támogatottak az érzékelők és a helyszíni felügyeleti konzol webalkalmazásaiban:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Hálózati hozzáférési követelmények

Győződjön meg arról, hogy a szervezet biztonsági házirendje lehetővé teszi a hozzáférést a következőhöz:

| Protokoll | Átvitel | Be/ki | Port | Használt | Cél | Forrás | Cél |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | BE/KI | 443 | Az érzékelő és a helyszíni felügyeleti konzol webkonzolja | Hozzáférés a webkonzolhoz | Ügyfél | Érzékelő és helyszíni felügyeleti konzol |
| SSH | TCP | BE/KI | 22 | parancssori felület | Hozzáférés a parancssori felülethez | Ügyfél | Érzékelő és helyszíni felügyeleti konzol |
| SSL | TCP | BE/KI | 443 | Érzékelő és helyszíni felügyeleti konzol | A CyberX platform és a központi felügyeleti platform közötti kapcsolat | érzékelő | Helyszíni felügyeleti konzol |
| NTP | UDP | IN | 123 | Idő szinkronizálása | A helyszíni felügyeleti konzol NTP-ként használja az érzékelőt | érzékelő | helyszíni felügyeleti konzol |
| NTP | UDP | BE/KI | 123 | Idő szinkronizálása | Külső NTP-kiszolgálóhoz csatlakoztatott érzékelő, ha nincs telepítve helyszíni felügyeleti konzol | érzékelő | NTP |
| SMTP | TCP | KI | 25 | E-mail | A CyberX platform és a felügyeleti platform és a levelezési kiszolgáló közötti kapcsolat | Érzékelő és helyszíni felügyeleti konzol | Levelezési kiszolgáló |
| Rendszernapló | UDP | KI | 514 | LEEF | A helyszíni felügyeleti konzolról a syslog-kiszolgálóra küldött naplók | Helyszíni felügyeleti konzol és érzékelő | Syslog-kiszolgáló |
| DNS |  | BE/KI | 53 | DNS | DNS-kiszolgáló portja | Helyszíni felügyeleti konzol és érzékelő | DNS-kiszolgáló |
| LDAP | TCP | BE/KI | 389 | Active Directory | A CyberX platform és a felügyeleti platform közötti kapcsolat a Active Directory | Helyszíni felügyeleti konzol és érzékelő | LDAP-kiszolgáló |
| LDAPS | TCP | BE/KI | 636 | Active Directory | A CyberX platform és a felügyeleti platform közötti kapcsolat a Active Directory | Helyszíni felügyeleti konzol és érzékelő | LDAPs-kiszolgáló |
| SNMP | UDP | KI | 161 | Figyelés | Távoli SNMP-gyűjtők. | Helyszíni felügyeleti konzol és érzékelő | SNMP-kiszolgáló |
| WMI | UDP | KI | 135 | figyelés | Windows-végpont figyelése | Érzékelő | Kapcsolódó hálózati elem |
| Alagútkezelés | TCP | IN | 9000 <br /><br />– a 443-es porton felül <br /><br />A végfelhasználótól a helyszíni felügyeleti konzolig. <br /><br />– 22-es port az érzékelőről a helyszíni felügyeleti konzolra  | figyelés | Alagútkezelés | Érzékelő | Helyszíni felügyeleti konzol |

### <a name="planning-rack-installation"></a>A rack telepítésének tervezése

A rack telepítésének megtervezése:

1. Készítse elő a figyelőt és a billentyűzetet a berendezés hálózati beállításaihoz.

1. A készülékhez tartozó állvány területének lefoglalása.

1. Legyen elérhető AC Power a készülékhez.
1. Készítse elő a LAN-kábelt a felügyelet hálózati kapcsolóhoz való csatlakoztatásához.
1. Készítse elő a LAN-kábeleket a IoT-berendezéshez tartozó Defender-portok és-hálózati csapok csatlakoztatásához. 
1. Az architektúra-felülvizsgálati munkamenetben leírtak szerint konfigurálhatja, összekapcsolhatja és érvényesítheti az elterjedési portokat a tükrözött kapcsolókon.
1. Kapcsolja össze a konfigurált TARTOMÁNYhoz tartozó portot egy Wireshark-t futtató számítógéphez, és ellenőrizze, hogy a port megfelelően van-e konfigurálva.
1. Nyissa meg az összes kapcsolódó tűzfal-portot.

## <a name="about-passive-network-monitoring"></a>A passzív hálózat figyelése

A készülék több forrásból fogad forgalmat, akár a tükrözött portok (SPAN portok), akár a hálózati csapok használatával. A felügyeleti port a helyszíni felügyeleti konzolhoz vagy a IoT-portálhoz való kapcsolódást biztosító üzleti, vállalati vagy érzékelő felügyeleti hálózathoz csatlakozik.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="A felügyelt kapcsolók diagramja a portok tükrözésével.":::

### <a name="purdue-model"></a>Purdue-modell

A következő szakaszok ismertetik a Purdue-szinteket.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="A Purdue modell ábrája.":::

####  <a name="level-0-cell-and-area"></a>0. szint: cella és felszín  

A 0. szint az alapszintű gyártási folyamatban részt vevő érzékelők, működtetők és eszközök széles választékát tartalmazza. Ezek az eszközök az ipari automatizálás és vezérlés rendszer alapvető funkcióit hajtják végre, például:

- Motor vezetése.

- Változók mérése.
- Kimenet beállítása.
- Kulcsfontosságú függvények (például festés, hegesztés és hajlítás) végrehajtása.

#### <a name="level-1-process-control"></a>1. szint: folyamat-ellenőrzés

Az 1. szint olyan beágyazott vezérlőket tartalmaz, amelyek a 0. szintű eszközökkel folytatott kommunikációhoz szükséges gyártási folyamatot vezérlik és kezelik. A diszkrét gyártás során ezek az eszközök programozható logikai vezérlők (PLC-EK) vagy távoli telemetria egységek (RTUs-EK). A Process Manufacturing szolgáltatásban az alapszintű vezérlő neve Distributed Control System (DCS).

#### <a name="level-2-supervisory"></a>2. szint: felügyelet

A 2. szint az üzemi létesítmény egy területének futásidejű felügyeletéhez és működéséhez kapcsolódó rendszereket és funkciókat jelöli. Ezek általában a következőket tartalmazzák: 

- Kezelői felületek vagy HMIs

- Riasztások vagy riasztási rendszerek

- Történész és batch-felügyeleti rendszerek feldolgozása

- A munkaállomások vezérlése

Ezek a rendszerek az 1. szinten kommunikálnak a PLC és a RTUs. Bizonyos esetekben a webhellyel vagy a vállalattal (4-es szint és 5. szint) rendelkező rendszerekkel és alkalmazásokkal kommunikálnak vagy osztoznak. Ezek a rendszerek elsődlegesen a szabványos számítástechnikai eszközökön és operációs rendszereken (UNIX vagy Microsoft Windows rendszeren) alapulnak.

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>3. és 3,5. szint: hely szintű és ipari peremhálózati hálózat

A hely szintje a legmagasabb szintű ipari automatizálási és vezérlési rendszereket jelöli. Az ezen a szinten létező rendszerek és alkalmazások kezelik a helyekre kiterjedő ipari automatizálási és vezérlési funkciókat. A 0 és 3 közötti szintek kritikusnak számítanak a hely működésében. Az ezen a szinten létező rendszerek és függvények a következők lehetnek:

- Éles jelentéskészítés (például ciklus idejének, minőségi index, prediktív karbantartás)

- Növényi történész

- Részletes üzemi ütemezés

- Hely szintű Operations Management

- Eszközök és anyagok kezelése

- Javítócsomag-indító kiszolgáló

- Fájlkiszolgáló

- Ipari tartomány, Active Directory, terminálkiszolgáló

Ezek a rendszerek kommunikálnak az üzemi zónával, és megosztják az adatmegosztást a vállalattal (4-es szint és 5. szint) és az alkalmazásokkal.  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>4. és 5. szint: üzleti és vállalati hálózatok

A 4-es szint és az 5. szint azt a helyet vagy vállalati hálózatot jelöli, ahol a központi informatikai rendszerek és függvények léteznek. Az informatikai szervezet ezeket a szinteket közvetlenül kezeli a szolgáltatásokkal, rendszerekkel és alkalmazásokkal.

## <a name="planning-for-network-monitoring"></a>A hálózat figyelésének tervezése

Az alábbi példák különböző típusú topológiákat mutatnak az ipari vezérlési hálózatokhoz, valamint az érzékelők optimális monitorozásának és elhelyezésének szempontjait.

### <a name="what-should-be-monitored"></a>Mit kell figyelni?

Az 1. és 2. rétegen áthaladó forgalmat figyelni kell.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Mi a teendő, ha a IoT Appliance Defender csatlakozik?

A IoT készülék Defender-nak csatlakoznia kell az 1. és 2. réteg közötti ipari kommunikációt (bizonyos esetekben a 3. rétegben is) található felügyelt kapcsolókhoz.

A következő ábra egy többrétegű, több-bérlős hálózat általános absztrakciója, amelynek a kiterjedésű kiberbiztonsági-ökoszisztémája jellemzően egy SOC és egy MSSP által működtetett.

A NTA érzékelők jellemzően az OSI modell 0 és 3. rétegében vannak üzembe helyezve.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Az OSI modell ábrája.":::

#### <a name="example-ring-topology"></a>Példa: Ring topológia

A Ring Network olyan topológia, amelyben az egyes kapcsolók vagy csomópontok pontosan két másik kapcsolóhoz csatlakoznak, és egyetlen folyamatos utat képeznek a forgalom számára.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="A gyűrűs topológia ábrája":::

#### <a name="example-linear-bus-and-star-topology"></a>Példa: lineáris busz-és csillag-topológia

Egy csillag hálózatban minden gazdagép egy központi hubhoz csatlakozik. Legegyszerűbben az egyik központi hub az üzenetek továbbítására szolgáló adatcsatornaként működik. A következő példában az alacsonyabb kapcsolók nem figyelhetők meg, és az ezen kapcsolókon továbbra is helyi forgalom nem lesz látható. Lehetséges, hogy az eszközök az ARP-üzenetek alapján azonosíthatók, de a kapcsolatok adatai hiányoznak.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="A lineáris busz és a csillag topológiájának ábrája.":::

#### <a name="multisensor-deployment"></a>Többérzékelős telepítés

Íme néhány javaslat a több érzékelő üzembe helyezéséhez:

| **Szám** | **Méterre** | **Függőség** | **Érzékelők száma** |
|--|--|--|--|
| A kapcsolók közötti maximális távolság | 80 méter | Előkészített Ethernet-kábel | Több mint 1 |
| Az OT-hálózatok száma | Több mint 1 | Nincs fizikai kapcsolat | Több mint 1 |
| Kapcsolók száma | Használhatja a RSPAN-konfigurációt | Legfeljebb nyolc, helyi kiterjedésű kapcsoló az érzékelőhöz a kábelezési távolság alapján | Több mint 1 |

#### <a name="traffic-mirroring"></a>Forgalom tükrözése  

Ha csak a releváns adatokat szeretné látni a forgalom elemzéséhez, akkor a Defender for IoT platformot egy kapcsoló egy tükrözési portjához kell csatlakoznia, vagy egy olyan KOPPINTÁSsal, amely csak az iparági INTERNETKAPCSOLATot és a SCADA forgalmat tartalmazza. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Használja ezt a kapcsolót a beállításhoz.":::

A váltási forgalmat a következő módszerekkel figyelheti:

- [Váltás a SPAN portra](#switch-span-port)

- [Távoli tartomány (RSPAN)](#remote-span-rspan)

- [Aktív és passzív összesítési KOPPINTÁS](#active-and-passive-aggregation-tap)

A SPAN és a RSPAN a Cisco terminológiája. A kapcsolók egyéb márkái hasonló funkciókkal rendelkeznek, de más terminológiát is használhatnak.

#### <a name="switch-span-port"></a>Váltás a SPAN portra

A Switch port Analyzer a kapcsolón lévő illesztőfelületek helyi forgalmát tükrözi ugyanazon a kapcsolón. Íme néhány szempont:

- Ellenőrizze, hogy a megfelelő kapcsoló támogatja-e a port tükrözési funkciót.  

- A tükrözési beállítás alapértelmezés szerint le van tiltva.

- Azt javasoljuk, hogy konfigurálja az összes kapcsoló portjait, még akkor is, ha nincsenek hozzájuk kapcsolódó adatkapcsolatok. Ellenkező esetben előfordulhat, hogy egy szélhámos eszköz nem figyelt porthoz csatlakozik, és az érzékelő nem figyelmezteti.

- A szórásos vagy csoportos küldésű üzenetkezelést használó hálózatok esetében konfigurálja úgy a kapcsolót, hogy csak az RX (fogadás) típusú átviteleket tükrözze. Ellenkező esetben a csoportos küldésű üzeneteket a rendszer annyi aktív portnál fogja megismételni, a sávszélességet pedig megszorozza.

A következő konfigurációs példák csak referenciák, és az IOS-t futtató Cisco 2960 kapcsolón (24 porton) alapulnak. Csak tipikus példák, ezért nem használhatók utasításokként. A más Cisco operációs rendszerekben és más típusú kapcsolókon lévő portok tükrözése eltérő módon történik.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="A SPAN port konfigurációs termináljának ábrája.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="A SPAN port konfigurációs üzemmódjának ábrája.":::

##### <a name="monitoring-multiple-vlans"></a>Több VLAN figyelése

A IoT Defender lehetővé teszi a hálózatban konfigurált VLAN-ok figyelését. Nincs szükség a Defender IoT rendszerre való konfigurálására. A felhasználónak biztosítania kell, hogy a hálózati kapcsoló úgy legyen konfigurálva, hogy VLAN-címkéket küldjön a Defendernek a IoT.

A következő példa azokat a szükséges parancsokat mutatja be, amelyeket a Cisco kapcsolón kell konfigurálni, hogy lehetővé váljon a IoT a Defenderben való figyelése:

**Figyelő munkamenete**: Ez a parancs felelős a VLAN-ok a span portra való küldésének folyamatán.

- az 1. munkamenet-forrás felületének figyelése Gi1/2

- az 1. munkamenet szűrő-típusának figyelése – helyes RX

- a munkamenet 1. fastEthernet1/1 beágyazási dot1q figyelése

**Figyelő Trunk port F.E. Gi1/1**: a VLAN-ok konfigurálva vannak a Trunk porton.

- illesztő GigabitEthernet1/1

- switchport Trunk Encapsulation dot1q

- switchport mód törzse

#### <a name="remote-span-rspan"></a>Távoli tartomány (RSPAN)

A távoli tartomány munkamenete több elosztott forrású port forgalmát egy dedikált távoli VLAN-ra irányítja.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Távoli tartomány diagramja":::

A VLAN-ban lévő adategységeket a rendszer a több kapcsolón keresztül a fizikai célport részét képező kapcsolón keresztül továbbítja. Ez a port csatlakozik a Defender for IoT platformhoz.  

##### <a name="more-about-rspan"></a>További információ a RSPAN

- A RSPAN olyan speciális funkció, amely speciális VLAN-t igényel a kapcsolók közötti figyelők továbbítására. Az RSPAN nem támogatott az összes kapcsolón. Győződjön meg arról, hogy a kapcsoló támogatja a RSPAN függvényt.

- A tükrözési beállítás alapértelmezés szerint le van tiltva.

- A távoli VLAN-nak engedélyezve kell lennie a forrás-és a cél kapcsolói közötti, a törzsben található porton.

- Az ugyanahhoz a RSPAN-munkamenethez csatlakozó kapcsolóknak ugyanabból a szállítóból kell származnia.

> [!NOTE]
> Győződjön meg arról, hogy a távoli VLAN-t a kapcsolók közötti megosztást megosztó Trunk port nincs definiálva tükrözött munkamenet-forrásportként.
>
> A távoli VLAN a tükrözött munkamenet sávszélességének méretével növeli a befoglalt port sávszélességét. Ellenőrizze, hogy a kapcsoló Trunk portja támogatja-e.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="A távoli VLAN diagramja.":::

#### <a name="rspan-configuration-examples"></a>RSPAN-konfigurációs példák

RSPAN: a Cisco Catalyst 2960 (24 ports) alapján.

**A forrás kapcsoló konfigurációjának példája:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Képernyőkép a RSPAN-konfigurációról.":::

1. Adja meg a globális konfigurációs módot.

1. Hozzon létre egy dedikált VLAN-t.

1. Azonosítsa a VLAN-t RSPAN VLAN-ként.

1. Visszatérés a "terminál konfigurálása" módba.

1. Konfigurálja az összes 24 portot munkamenet-forrásként.

1. Konfigurálja úgy a RSPAN VLAN-t, hogy a munkamenet célja legyen.

1. Visszatérés a Privileged EXEC módba.

1. Ellenőrizze a port tükrözési konfigurációját.

**Példa a cél kapcsoló konfigurálására:**

1. Adja meg a globális konfigurációs módot.

1. Konfigurálja a RSPAN VLAN-t a munkamenet forrásaként.

1. Konfigurálja a 24. fizikai portot a munkamenet céljának.

1. Visszatérés a Privileged EXEC módba.

1. Ellenőrizze a port tükrözési konfigurációját.

1. Mentse a konfigurációt.

#### <a name="active-and-passive-aggregation-tap"></a>Aktív és passzív összesítési KOPPINTÁS

Aktív vagy passzív összesítési KOPPINTÁS van telepítve a hálózati kábelre. Az RX és a TX is megismétli a figyelési érzékelőt.

A terminál-hozzáférési pont (TAP) olyan hardvereszköz, amely lehetővé teszi, hogy a hálózati forgalom az A portról a B portra, illetve a B portról az A portra lépjen, megszakítás nélkül. A folyamat a forgalom mindkét oldalának pontos másolatát hozza létre folyamatosan, a hálózat integritásának veszélyeztetése nélkül. Egyes csapok a forgalom továbbításával és fogadásával összesítik a forgalmat, ha szükséges. Ha az Összesítés nem támogatott, minden KOPPINTÁS két érzékelő-portot használ a küldési és fogadási forgalom figyelésére.

A csapok számos okból előnyösek. Hardver-alapúak, és nem veszélyeztethetik. Minden forgalmat továbbítanak, akár sérült üzeneteket is, amelyek gyakran eldobják a kapcsolókat. Nem érzékenyek a processzorra, ezért a csomagok időzítése pontos, ahol a kapcsolók kezelik a tükrözött függvényt alacsony prioritású tevékenységként, amely hatással lehet a tükrözött csomagok időzítésére. Törvényszéki célokra a legjobb eszköz a KOPPINTÁS.

A portok figyeléséhez KOPPINTson az aggregators lehetőségre. Ezek az eszközök processzor-alapúak, és nem annyira biztonságosak, mint a hardveres csapok. Előfordulhat, hogy nem tükrözik a csomagok pontos időzítését.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Aktív és passzív csapok diagramja":::

##### <a name="common-tap-models"></a>Gyakori KOPPINTÁSi modellek

Ezeket a modelleket tesztelték a kompatibilitás érdekében. Más gyártók és modellek is kompatibilisek lehetnek.

| Kép | Modellezés |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Képernyőfelvétel a Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Az IXIA TPA2-CU3 képernyőképe."::: | IXIA TPA2 – CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Képernyőkép a US Robotics USR 4503-ről."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Speciális KOPPINTó konfiguráció

| Garland KOPPINTÁS | US Robotics KOPPINTÁS |
| ----------- | --------------- |
| Győződjön meg arról, hogy a jumperek a következőképpen vannak beállítva:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Képernyőkép a US Robotics kapcsolóról.":::| Ellenőrizze, hogy az összesítési mód aktív-e. |

## <a name="deployment-validation"></a>Központi telepítés ellenőrzése

### <a name="engineering-self-review"></a>Mérnöki önértékelés  

Az OT és az INTERNETKAPCSOLATtal rendelkező hálózati diagram áttekintése a leghatékonyabb módszer a legjobb hely megadására a kapcsolódáshoz, ahol a figyeléshez a legmegfelelőbb forgalmat érheti el.

A hely szakemberei tudják, hogy a hálózat hogyan néz ki. A helyi hálózattal és operatív csapatokkal való felülvizsgálati munkamenet általában tisztázza az elvárásokat, és meghatározza a legjobb helyet a berendezés összekapcsolásához.

Kapcsolódó információk:

- Ismert eszközök listája (táblázat)  

- Az eszközök becsült száma  

- Szállítók és ipari protokollok

- Kapcsolók modellje annak ellenőrzéséhez, hogy elérhető-e a port tükrözése lehetőség

- Információ arról, hogy kik felügyelik a kapcsolókat (például IT), és hogy külső erőforrások-e

- A helyen az OT-hálózatok listája

#### <a name="common-questions"></a>Gyakori kérdések

- Mik a megvalósítás általános céljai? Fontos a teljes leltár és pontos hálózati Térkép?

- Van több vagy redundáns hálózat az internetkapcsolat-megosztásban? Az összes figyelt hálózat?

- Van-e kommunikáció az internetkapcsolat-megosztás és a vállalati (üzleti) hálózat között? Figyelik ezeket a kommunikációkat?

- A VLAN-ok konfigurálva vannak a hálózat kialakításában?

- Hogyan történik az ICS karbantartása rögzített vagy átmeneti eszközökkel?

- Hol vannak telepítve a tűzfalak a figyelt hálózatokon?

- Van-e útválasztás a figyelt hálózatokban?

- Milyen OT-protokollok aktívak a figyelt hálózatokon?

- Ha csatlakozik ehhez a kapcsolóhoz, akkor a HMI és a PLC közötti kommunikáció látható?

- Mi a fizikai távolság az ICS-kapcsolók és a vállalati tűzfal között? 

- A nem felügyelt kapcsolók lecserélhetők a felügyelt kapcsolókra, vagy a hálózati csaptelepek használata is lehetséges?

- Van-e soros kommunikáció a hálózaton? Ha igen, jelenítse meg a hálózati diagramon.

- Ha ehhez a kapcsolóhoz csatlakozni kell a IoT készülékhez, van-e fizikai rendelkezésre állási terület az adott szekrényben?

#### <a name="other-considerations"></a>További szempontok

A Defender for IoT berendezés célja az 1. és 2. réteg forgalmának figyelése.

Egyes architektúrák esetén a Defender for IoT készülék a 3. réteget is figyeli, ha az e rétegen van ilyen forgalom. A hely architektúrájának áttekintése és a kapcsoló figyelésének eldöntése során vegye figyelembe a következő változókat:

- Milyen előnyökkel jár a váltás, illetve milyen jelentőséggel bír ennek a kapcsolónak a figyelése?

- Ha egy kapcsoló nem felügyelt, akkor lehetséges, hogy egy magasabb szintű kapcsolóról figyeli a forgalmat?

  Ha az ICS-architektúra gyűrűs topológia, akkor a gyűrűben csak egy kapcsolót kell figyelni.

- Mi a biztonsági vagy működési kockázat ebben a hálózatban?

- Lehetséges figyelni a kapcsoló VLAN-át? A VLAN látható egy másik kapcsolóban, amelyet figyelni tudunk?

#### <a name="technical-validation"></a>Technikai ellenőrzés

A rögzített adatforgalom (PCAP-fájl) a Switch SPAN (vagy Mirror) portból való fogadása a következő segítségére nyújt segítséget:

- Ellenőrizze, hogy a kapcsoló megfelelően van-e konfigurálva.

- Ellenőrizze, hogy a kapcsolón áthaladó forgalom releváns-e a figyeléshez (OT forgalom).

- Azonosítsa a sávszélességet és az eszközök becsült számát ebben a kapcsolóban.

Egy PCAP-fájlt (néhány percet) rögzíthet úgy, hogy csatlakoztat egy laptopot egy már konfigurált SPAN-porthoz a Wireshark alkalmazással.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="A SPAN porthoz csatlakoztatott laptop képernyőképe.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Képernyőkép a minta PCAP-fájl rögzítéséről.":::

#### <a name="wireshark-validation"></a>Wireshark érvényesítése

- Győződjön meg arról, hogy az *egyedi küldésű csomagok* szerepelnek a rögzítési forgalomban. Az egyedi küldés az egyik címből a másikba történik. Ha a forgalom nagy része ARP-üzenet, akkor a kapcsoló beállítása helytelen.

- Ugrás a **statisztikai**  >  **protokoll-hierarchiára**. Ellenőrizze, hogy jelen vannak-e az ipari t-protokollok.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Képernyőkép a Wireshark érvényesítéséről.":::

## <a name="troubleshooting"></a>Hibaelhárítás

A következő szakaszban hibaelhárítási problémák léphetnek fel:

- [Nem lehet kapcsolatot létesíteni webes felületen keresztül](#cant-connect-by-using-a-web-interface)

- [A készülék nem válaszol](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Nem lehet kapcsolatot létesíteni webes felületen keresztül

1. Ellenőrizze, hogy a csatlakoztatni kívánt számítógép ugyanazon a hálózaton van-e, mint a készülék.

2. Ellenőrizze, hogy a GUI-hálózat csatlakozik-e az érzékelő felügyeleti portjához.

3. Pingelje a berendezés IP-címét. Ha nincs válasz a pingelésre:

    1. Csatlakoztasson egy figyelőt és egy billentyűzetet a készülékhez.

    1. A bejelentkezéshez használja a **támogatási** felhasználót és a jelszót.

    1. Az aktuális IP-cím megjelenítéséhez használja a parancs **hálózati listáját** .

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Képernyőkép a Network List parancsról.":::

4. Ha a hálózati paraméterek helytelenül vannak konfigurálva, a következő eljárással módosíthatja:

    1. Használja a **Network Edit-Settings** parancsot.

    1. A felügyeleti hálózat IP-címének módosításához válassza az **Y** lehetőséget.

    1. Az alhálózati maszk módosításához válassza az **Y** lehetőséget.

    1. A DNS módosításához válassza az **Y** lehetőséget.

    1. Az alapértelmezett átjáró IP-címének módosításához válassza az **Y** lehetőséget.

    1. A bemeneti felület változásához (csak az érzékelő esetében) válassza az **Y** lehetőséget.

    1. A Bridge felületén válassza az **N** lehetőséget.

    1. A beállítások alkalmazásához válassza az **Y** lehetőséget.

5. Az újraindítás után kapcsolódjon a felhasználó támogatási szolgálatához, és a **hálózati lista** paranccsal ellenőrizze, hogy a paraméterek módosultak-e.

6. Próbálkozzon újra a pingeléssel, és kapcsolódjon újra a grafikus felhasználói felületről.

### <a name="appliance-is-not-responding"></a>A készülék nem válaszol

1. Kapcsolódjon egy figyelővel és billentyűzettel a készülékhez, vagy használja a PuTTY-t a CLI-hez való távoli kapcsolódáshoz.

2. A bejelentkezéshez használja a támogatási hitelesítő adatokat.

3. Használja a **System józanság** parancsot, és győződjön meg arról, hogy az összes folyamat fut.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Képernyőkép a System józanság parancsról.":::

Bármilyen egyéb probléma esetén forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Példa a hely könyvére

A példa a telefonkönyv használatával lekérdezheti és áttekintheti a hálózati telepítéshez szükséges fontos információkat.

### <a name="site-checklist"></a>Hely ellenőrzőlista

A hely üzembe helyezése előtt tekintse át ezt a listát:

| **#** | **Feladat vagy tevékenység** | **Állapot** | **Megjegyzések** |
|--|--|--|--|
| 1 | Berendezések megrendelése. | ☐ |  |
| 2 | Készítse elő a hálózatban lévő alhálózatok listáját. | ☐ |  |
| 3 | Adja meg az üzemi hálózatok VLAN-listáját. | ☐ |  |
| 4 | Adja meg a hálózat kapcsoló modelljeinek listáját. | ☐ |  |
| 5 | Adja meg az ipari berendezések szállítóinak és protokolljainak listáját. | ☐ |  |
| 6 | Adja meg az érzékelők hálózati adatait (IP-cím, alhálózat, D-GW, DNS). | ☐ |  |
| 7 | Hozza létre a szükséges tűzfalszabályok és a hozzáférési listát. | ☐ |  |
| 8 | Hozzon létre átfedésben lévő portokat a portok figyelésére szolgáló kapcsolókon, vagy konfigurálja a hálózati csapokat a kívánt módon. | ☐ |  |
| 9 | Készítse elő a rack-területet az érzékelő berendezésekhez. | ☐ |  |
| 10 | Munkaállomások előkészítése a személyzet számára. | ☐ |  |
| 11 | Adjon meg egy billentyűzetet, egy figyelőt és egy egeret a Defender számára a IoT rack-eszközökhöz. | ☐ |  |
| 12 | A berendezések csatlakoztatása és a kábelek csatlakoztatása. | ☐ |  |
| 13 | A központi telepítés támogatására szolgáló hely erőforrásainak lefoglalása. | ☐ |  |
| 14 | Hozzon létre Active Directory csoportokat vagy helyi felhasználókat. | ☐ |  |
| 15 | Set-up Training (saját tanulás). | ☐ |  |
| 16 | Ugrás vagy nem ugrás. | ☐ |  |
| 17 | A központi telepítés dátumának beolvasása. | ☐ |  |


| **Date** | **Megjegyzés** | **Központi telepítés dátuma** | **Megjegyzés** |
|--|--|--|--|
| Defender IoT-hez |  | Hely neve * |  |
| Név |  | Név |  |
| Pozíció |  | Pozíció |  |

#### <a name="architecture-review"></a>Architektúra áttekintése

Az ipari hálózat diagramjának áttekintése lehetővé teszi, hogy meghatározza a Defender számára a IoT-berendezések megfelelő helyét.

1.  Tekintse meg az ipari OT-környezet globális hálózati diagramját. Például:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="A globális hálózat ipari OT-környezetének ábrája.":::

    > [!NOTE]
    > A IoT készülékhez tartozó Defendernek egy alacsonyabb szintű kapcsolóhoz kell csatlakoznia, amely a kapcsolón lévő portok közötti forgalmat látja.  

2. Adja meg a figyelni kívánt hálózati eszközök hozzávetőleges számát. Ezekre az információkra szüksége lesz, amikor előkészíti az előfizetést az Azure Defender for IoT portálra. A bevezetési folyamat során a rendszer felszólítja, hogy adja meg az eszközök számát a 1000-es növekményekben.

3. Adjon meg egy alhálózati listát az üzemi hálózatokhoz és egy leírást (nem kötelező). 

    |  **#**  | **Alhálózat neve** | **Leírás** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Adja meg az üzemi hálózatok VLAN-listáját.

    | **#** | **VLAN neve** | **Leírás** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Annak ellenőrzéséhez, hogy a kapcsolók rendelkeznek-e a port tükrözési képességével, adja meg, hogy a Defender for IoT platform milyen számú kapcsolóval kapcsolódjon:

    | **#** | **Kapcsoló (switch)** | **Modellezés** | **Forgalom tükrözésének támogatása (SPAN, RSPAN vagy none)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    A harmadik fél kezeli a kapcsolókat? Y vagy N 

    Ha igen, kik? __________________________________ 

    Mi a szabályzatuk? __________________________________ 

    Például:

    - Siemens

    - Rockwell Automation – Ethernet vagy IP

    - Emerson – DeltaV, Ovation
    
6. Vannak olyan eszközök, amelyek egy soros kapcsolaton keresztül kommunikálnak a hálózaton? Igen vagy nem 

    Ha igen, válassza ki a soros kommunikációs protokollt: ________________ 

    Ha igen, akkor a hálózati diagramon, hogy milyen eszközök kommunikálnak a soros protokollokkal, és hol vannak: 
 
    <Add your network diagram with marked serial connection> 

7. A QoS esetében az érzékelő alapértelmezett beállítása 1,5 Mbps. Itt adhatja meg, hogy szeretné-e módosítani: ________________ 

   Üzleti egység (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>A hely berendezéseinek specifikációi

#### <a name="network"></a>Network (Hálózat)  

Az érzékelő készülék hálózati adapteren keresztül csatlakozik a SPAN porthoz. Egy másik dedikált hálózati adapteren keresztül kapcsolódik az ügyfél vállalati hálózatához.

Adja meg a vállalati hálózatban csatlakoztatni kívánt érzékelő hálózati adapterhez tartozó címek részleteit: 

|  Elem               | 1. berendezés | 2. berendezés | 3. berendezés |
| --------------- | ------------- | ------------- | ------------- |
| Berendezés IP-címe    |               |               |               |
| Alhálózat          |               |               |               |
| Alapértelmezett átjáró |               |               |               |
| DNS             |               |               |               |
| Állomásnév       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/kiszolgáló kezelése

|       Elem          | 1. berendezés | 2. berendezés | 3. berendezés |
| --------------- | ------------- | ------------- | ------------- |
| Berendezés IP-címe     |               |               |               |
| Alhálózat          |               |               |               |
| Alapértelmezett átjáró |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Helyszíni felügyeleti konzol  

|       Elem          | Aktív | Passzív (ha a ha-t használja) |
| --------------- | ------ | ----------------------- |
| IP-cím             |        |                         |
| Alhálózat          |        |                         |
| Alapértelmezett átjáró |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Elem              | Részletek |
| --------------- | ------ |
| IP              |        |
| IP-cím | |
| Felhasználónév | |
| Jelszó | |
| Hitelesítéstípus | MD5 vagy SHA |
| Titkosítás | DES vagy AES |
| Titkos kulcs | |
| SNMP v2 közösségi karakterlánc |

### <a name="on-premises-management-console-ssl-certificate"></a>Helyszíni felügyeleti konzol SSL-tanúsítványa

SSL-tanúsítvány használatát tervezi? Igen vagy nem

Ha igen, milyen szolgáltatást fog használni a létrehozásához? Milyen attribútumokat fog tartalmazni a tanúsítványban (például tartomány vagy IP-cím)?

### <a name="smtp-authentication"></a>SMTP-hitelesítés

Azt tervezi, hogy az SMTP protokollal továbbítja a riasztásokat egy e-mail-kiszolgálónak? Igen vagy nem

Ha igen, milyen hitelesítési módszert fog használni?  

### <a name="active-directory-or-local-users"></a>Active Directory vagy helyi felhasználók

Vegye fel a kapcsolatot egy Active Directory rendszergazdájával, és hozzon létre egy Active Directory hely felhasználói csoportot, vagy hozzon létre helyi felhasználókat. Ügyeljen arra, hogy a felhasználók készen álljanak az üzembe helyezés napján. 

### <a name="iot-device-types-in-the-network"></a>IoT a hálózaton

| Eszköz típusa | A hálózatban lévő eszközök száma | Átlagos sávszélesség |
| --------------- | ------ | ----------------------- |
| Kamera | |
| X-ray gép | |

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a IoT-beli Defender-telepítésről](how-to-install-software.md)
