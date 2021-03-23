---
title: A monitorozandó forgalom szabályozása
description: Az érzékelők automatikusan elvégzik a nagy csomagok észlelését, valamint a forgalom és a hálózati eszközök, például az eszközök attribútumai és a hálózati működés információinak elhárítását. Számos eszköz áll rendelkezésre az egyes érzékelők által észlelt forgalom típusának szabályozására.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784441"
---
# <a name="control-what-traffic-is-monitored"></a>A monitorozandó forgalom szabályozása

Az érzékelők automatikusan elvégzik a nagy csomagok észlelését, valamint a forgalom és a hálózati eszközök, például az eszközök attribútumai és viselkedése adatainak megoldását. Számos eszköz áll rendelkezésre az egyes érzékelők által észlelt forgalom típusának szabályozására.

## <a name="learning-and-smart-it-learning-modes"></a>Tanulási és intelligens informatikai képzési módok

A tanulási mód arra utasítja az érzékelőt, hogy ismerkedjen meg a hálózat szokásos tevékenységével. Ilyenek például a hálózatban felderített eszközök, a hálózatban észlelt protokollok, az egyes eszközök közötti fájlátvitel stb. Ez a tevékenység lesz a hálózati alapterv.

A tanulás mód automatikusan engedélyezve van a telepítés után, és a rendszer csak a kikapcsolás után marad engedélyezve. A hozzávetőleges tanulási mód időtartama két – hat hét, a hálózat méretétől és összetettségtől függően. Ha ezt az időtartamot követően a tanulási mód le van tiltva, az észlelt új tevékenységek riasztást váltanak ki. A riasztások akkor aktiválódnak, ha a házirend-motor felfedi a megtanult alaptervtől való eltéréseket.

A tanulási időszak befejezése és a tanulási mód letiltását követően az érzékelő szokatlanul magas szintű alapkonfigurációt érzékel, amely a normál informatikai tevékenység, például a DNS és a HTTP-kérések eredményeként következik be. A tevékenység neve determinált IT-viselkedés. Előfordulhat, hogy a viselkedés szükségtelen házirend-megsértési riasztásokat és rendszerértesítéseket is aktivál. A riasztások és értesítések mennyiségének csökkentése érdekében engedélyezheti az **intelligens it learning** funkciót.

Ha az intelligens informatikai tanulás engedélyezve van, az érzékelő nyomon követi a hálózati forgalmat, amely adott riasztási forgatókönyvek alapján determinált informatikai viselkedést generál.

Az érzékelő hét napig figyeli ezt a forgalmat. Ha a hét napon belül ugyanazt a determinált-forgalmat észleli, továbbra is figyeli a forgalmat egy másik hét napig. Ha a rendszer nem észleli a forgalmat a teljes hét napig, az intelligens IT-tanulás le van tiltva az adott forgatókönyv esetében. Az adott forgatókönyvhöz észlelt új forgalom csak riasztásokat és értesítéseket fog előállítani.

Az intelligens IT learning használata segít csökkenteni a zajos IT-forgatókönyvek által okozott szükségtelen riasztások és értesítések számát.

Ha az érzékelőt a helyszíni felügyeleti konzol vezérli, nem tilthatja le a tanulási módokat. Ilyen esetekben a tanulási mód csak a felügyeleti konzolról tiltható le.

A tanulási képességek (tanulási és intelligens informatikai tanulás) alapértelmezés szerint engedélyezve vannak.

A tanulás engedélyezése vagy letiltása:

- Válassza ki a **Rendszerbeállítások** lehetőséget, és kapcsolja be a **tanulási** és az **intelligens informatikai tanulási** lehetőségeket.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Rendszerbeállítások pecek képernyő.":::

## <a name="configure-subnets"></a>Alhálózatok konfigurálása

Az alhálózati konfigurációk hatással vannak az eszközök megjelenítésére az eszköz térképén.

Alapértelmezés szerint az érzékelő felfedi az alhálózat beállítását, és feltölti az **alhálózat konfigurációja** párbeszédpanelt ezen információkkal.

Ha engedélyezni szeretné a fókuszt az OT-eszközökön, az IT-eszközöket a rendszer automatikusan összesíti az eszköz térképén lévő alhálózat alapján. Az egyes alhálózatok egyetlen entitásként jelennek meg a térképen, beleértve az interaktív összeomló/bővülő képességet, amely az IT-alhálózatokra és vissza.

Ha alhálózatokat használ, válassza ki az ICS-alhálózatokat az OT alhálózatok azonosításához. Ezután a Térkép nézetre összpontosíthat az OT-ra és az ICS-hálózatokra, és összecsukhatja az IT hálózati elemek minimális megjelenítését. Ez a tevékenység csökkenti a térképen látható eszközök teljes számát, és az OT és az INTERNETKAPCSOLATtal rendelkező hálózati elemek egyértelmű képét biztosítja.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="A hálózat szűrését bemutató képernyőkép.":::

Módosíthatja a konfigurációt, vagy manuálisan is módosíthatja az alhálózati adatokat a felderített adatok exportálásával, manuálisan történő módosításával, majd a manuálisan definiált alhálózatok listájának importálásával. Az exportálással és az importálással kapcsolatos további információkért lásd: az [eszköz adatainak importálása](how-to-import-device-information.md).

Bizonyos esetekben, például belső tartományként nyilvános tartományokat használó környezetekben az érzékelőt arra utasíthatja, hogy az összes alhálózatot belső alhálózatként oldja fel, ha az **internetes tevékenység nem észlelhető** lehetőséget választja. Ha ezt a lehetőséget választja:

- A nyilvános IP-címek helyi címekként lesznek kezelve.

- Nem küldenek riasztást a jogosulatlan internetes tevékenységekről, ami csökkenti a külső címeken fogadott értesítéseket és riasztásokat.

Alhálózatok konfigurálása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítás** ablakban válassza az **alhálózatok** lehetőséget.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Az alhálózati konfiguráció képernyőjét bemutató képernyőkép."::: 

3. Ha új eszközök felderítése után szeretné automatikusan felvenni az alhálózatokat, válassza a **tanulás automatikus alhálózatok** lehetőséget.

4. Ha az összes alhálózatot belső alhálózatként szeretné feloldani, jelölje be az **internetes tevékenységek nem észlelhetők**.

5. Válassza a **hálózat hozzáadása** lehetőséget, és adja meg a következő paramétereket az egyes alhálózatokhoz:

    - Az alhálózat IP-címe.
    - Az alhálózati maszk címe.
    - Az alhálózat neve. Azt javasoljuk, hogy az egyes alhálózatokat egy könnyen azonosítható névvel láthatja el, így megkülönböztetheti az IT és az OT hálózatok közötti különbséget. A név legfeljebb 60 karakter hosszú lehet.

6. Az alhálózat OT alhálózatként való megjelöléséhez válassza az **ICS-alhálózat** elemet.

7. Ha az alhálózatot külön szeretné bemutatni, amikor a Purdue-szint alapján rendezi a térképet, válassza az **elkülönített** lehetőséget.

8. Alhálózat törléséhez válassza a elemet :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Az összes alhálózat törléséhez válassza **az összes törlése** lehetőséget.

10. A konfigurált alhálózatok exportálásához válassza az **Exportálás** lehetőséget. Az alhálózati tábla le van töltve a munkaállomásra.

11. Kattintson a **Mentés** gombra.

### <a name="importing-information"></a>Adatok importálása 

Az alhálózati adatok importálásához válassza az **Importálás** lehetőséget, és válasszon ki egy IMPORTÁLNI kívánt CSV-fájlt. Az alhálózat adatai az importált adatokkal frissülnek. Ha fontos egy üres mező, elveszíti az adatait.

## <a name="detection-engines"></a>Észlelési motorok

Az öntanuló elemzési motorok nem szükségesek az aláírások frissítéséhez vagy a szabályok definiálásához. A motorok az IC-specifikus viselkedés-elemzést és adatelemzést használják a rendellenességek, kártevők, működési problémák, protokollok megsértése és az alapkonfiguráció hálózati tevékenység-eltérések folyamatos elemzéséhez.

> [!NOTE]
> Javasoljuk, hogy engedélyezze az összes biztonsági motort.

Ha egy motor eltérést észlel, riasztást vált ki. A riasztásokat megtekintheti és kezelheti a riasztási képernyőről vagy egy partneri rendszerről.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Képernyőkép, amely a szórás észlelését jeleníti meg.":::

A riasztást kiváltó motor neve a riasztás címe alatt jelenik meg.

### <a name="protocol-violation-engine"></a>Protokoll-megsértési motor 

A protokollok megsértése akkor fordul elő, ha a csomag szerkezete vagy a mező értéke nem felel meg a protokoll specifikációjának.

Példa: *"szabálytalan Modbus művelet (kód: Zero)"* riasztás. Ez a riasztás azt jelzi, hogy egy elsődleges eszköz 0 értékű kérelmet küld egy másodlagos eszköznek. Ez a művelet a protokoll specifikációja szerint nem engedélyezett, és előfordulhat, hogy a másodlagos eszköz nem tudja megfelelően kezelni a bemenetet.

### <a name="policy-violation-engine"></a>Házirend-megsértési motor

A szabályzat megsértése a megtanult vagy konfigurált beállításokban meghatározott alapkonfigurációi viselkedéstől való eltéréssel történik.

Példa: *"jogosulatlan http-felhasználói ügynök"* riasztás. Ez a riasztás azt jelzi, hogy a szabályzat által nem megtanult vagy jóváhagyott alkalmazás HTTP-ügyfélként van használatban az eszközön. Ez lehet egy új böngésző vagy alkalmazás az eszközön.

### <a name="malware-engine"></a>Kártevő motor

A kártevő motor kártékony hálózati tevékenységet észlel.

Példa: *"rosszindulatú tevékenység gyanúja (Stuxnet)"* riasztás. Ez a riasztás azt jelzi, hogy az érzékelő gyanús hálózati tevékenységet észlelt, amely a Stuxnet kártevő szoftverrel való kapcsolatban ismert. Ez a kártevő egy fejlett, az ipari szabályozást és a SCADA hálózatokat célzó állandó fenyegetés.

### <a name="anomaly-engine"></a>Anomália motor

Az anomália motor észleli a hálózati viselkedésben észlelt rendellenességeket.

Példa: *"rendszeres viselkedés a kommunikációs csatornán"* riasztás. Az összetevő megvizsgálja a hálózati kapcsolatokat, és megkeresi az adatátvitel időszakos és ciklikus viselkedését. Ez a viselkedés az ipari hálózatokban gyakori.

### <a name="operational-engine"></a>Működési motor

Az operatív motor észleli a működési incidenseket vagy az üzemzavart okozó entitásokat.

Példa: *"az eszköz gyanús, hogy leválasztott (nem válaszol)"* riasztást küld. Ez a riasztás akkor jön létre, ha egy eszköz nem válaszol az előre meghatározott időszakra vonatkozó bármilyen típusú kérelemre. Ez a riasztás egy eszköz leállítását, leválasztását vagy meghibásodását jelezheti.

### <a name="enable-and-disable-engines"></a>Motorok engedélyezése és letiltása

Ha letilt egy házirend-motort, a motor által generált információk nem lesznek elérhetők az érzékelő számára. Ha például letiltja az anomália motort, nem kap riasztást a hálózati rendellenességekről. Ha létrehozott egy továbbítási szabályt, a motor által megtanulott rendellenességek nem lesznek elküldve. Egy házirend-motor engedélyezéséhez vagy letiltásához válassza az **engedélyezve** vagy a **Letiltva** lehetőséget az adott motornál.

A teljes pontszám a **Rendszerbeállítások** képernyő jobb alsó sarkában jelenik meg. A pontszám azt jelzi, hogy az elérhető védelem hány százalékát engedélyezte a veszélyforrások elleni védelmi motor. Minden motor a rendelkezésre álló védelem 20%-ában érhető el.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="A pontszámot bemutató képernyőkép.":::

## <a name="configure-dhcp-address-ranges"></a>DHCP-címtartományok konfigurálása

A hálózat statikus és dinamikus IP-címekből is állhat. A rendszer általában statikus címeket talál a (z) és a (z), a (z) és a (z), a (z) és a (z), a A dinamikus IP-címek kiosztása jellemzően a laptopokkal, számítógépekkel, okostelefonokkal és egyéb hordozható eszközökkel (Wi-Fi vagy helyi hálózati fizikai kapcsolatok használatával különböző helyszíneken) megvalósított vendég hálózatokon történik.

Dinamikus hálózatok használata esetén az új IP-címek hozzárendelésekor megjelenő IP-címek módosításait kezelheti. Ezt a DHCP-címtartományok definiálásával végezheti el.

A módosítások például akkor fordulnak elő, ha egy DHCP-kiszolgáló IP-címeket rendel hozzá.

Az egyes érzékelők dinamikus IP-címeinek meghatározása lehetővé teszi az IP-címek változásainak az átfogó, átlátható támogatását. Ez átfogó jelentéskészítést biztosít minden egyes egyedi eszközhöz.

Az érzékelő konzol az eszközhöz társított legújabb IP-címet mutatja be, és jelzi, hogy mely eszközök dinamikusak. Például:

- Az adatbányászati jelentés és az eszköz leltározási jelentése összesíti az eszközről beszerzett összes tevékenységet egyetlen entitásként, az IP-címek változásaitól függetlenül. Ezek a jelentések jelzik, hogy mely címek lettek meghatározva DHCP-címként.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Képernyőkép, amely megjeleníti az eszközök leltárát.":::

- Az **eszköz tulajdonságai** ablak jelzi, hogy az eszköz DHCP-eszközként van-e meghatározva.

DHCP-címtartomány beállítása:

1.  Az oldalsó menüben válassza a **DHCP-tartományok** elemet a **Rendszerbeállítások** ablakban.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="A DHCP-tartományok kijelölését bemutató képernyőkép.":::

2.  Adjon meg egy új tartományt a és **az** **értékek értékének** beállításával.

3.  Opcionálisan: adja meg a tartomány nevét, amely legfeljebb 256 karakter hosszú lehet.

4.  Ha a tartományokat CSV-fájlba szeretné exportálni, válassza az **Exportálás** lehetőséget.

5. Ha manuálisan szeretne több tartományt felvenni egy CSV-fájlból, válassza az **Importálás** lehetőséget, majd válassza ki a fájlt.

    > [!NOTE]
    > A CSV-fájlból importált tartományok felülírják a meglévő tartomány beállításait.

6. Kattintson a **Mentés** gombra.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>DNS-kiszolgálók konfigurálása a névlekérdezési feloldáshoz

Az eszközök bővítésének növeléséhez több DNS-kiszolgálót is beállíthat, hogy carryout a fordított kereséseket. A hálózati alhálózatokban észlelt IP-címekhez társított állomásnevek vagy teljes tartománynevek feloldása is megoldható. Ha például egy érzékelő felfedi az IP-címet, akkor előfordulhat, hogy több DNS-kiszolgálót is lekérdez az állomásnév feloldásához.

A CIDR összes formátuma támogatott.

Az állomásnév megjelenik az eszköz leltárában, valamint az eszközök és a jelentések között.

Megadhatja a névkeresési feloldási ütemterveket adott óránkénti időközökre, például 12 óránként. Vagy ütemezhet egy adott időpontot is.

DNS-kiszolgálók meghatározása:

1. Válassza ki a **Rendszerbeállítások** elemet, majd válassza a **DNS-beállítások** lehetőséget.

2. Válassza a **DNS-kiszolgáló hozzáadása** lehetőséget.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="A DNS-kiszolgáló hozzáadását bemutató képernyőkép.":::

3. A **fordított DNS-címkeresés keresése** mezőben válassza a következők egyikét:

    - Intervallumok (óránként).
  
    - Egy adott időpont. Használja az Európai formázást. Tegyük fel például, hogy az **14:30** -es és nem **2:30 PM**-t használja.

4. A **DNS-kiszolgáló címe** mezőbe írja be a DNS IP-címét.

5. A **DNS-kiszolgáló portja** mezőben adja meg a DNS-portot.

6. Oldja fel a hálózati IP-címeket az eszköz teljes tartománynevére. A **címkék száma** mezőben adja meg a megjelenítendő tartományi címkék számát. Balról jobbra legfeljebb 30 karakter jelenik meg.

7. Az **alhálózatok** mezőben adja meg azokat az alhálózatokat, amelyeket a DNS-kiszolgálónak le szeretne kérdezni.

8. Jelölje be a váltás **engedélyezése** lehetőséget, ha a névkeresési lekérdezést el szeretné indítani.

### <a name="test-the-dns-configuration"></a>A DNS-konfiguráció tesztelése 

Tesztelési eszköz használatával ellenőrizze, hogy a megadott beállítások megfelelően működnek-e:

1. Engedélyezze a **DNS-címkeresés váltógomb értékét** .

2. Válassza a **Tesztelés** lehetőséget.

3. Adjon meg egy, a **kiszolgálóhoz tartozó DNS-névlekérdezési teszthez** tartozó címeket a **keresési címben** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="A keresési címtartományt megjelenítő képernyőkép.":::

4. Válassza a **Tesztelés** lehetőséget.

## <a name="configure-windows-endpoint-monitoring"></a>Windows-végpont figyelésének konfigurálása

A Windows-végpont figyelési képességével konfigurálhatja az Azure Defendert a IoT, hogy szelektíven figyelje a Windows rendszereit. Ez az eszközökről, például a szervizcsomag szintjeiről koncentrál, és pontosabb információkat nyújt.

A szondázás beállítható meghatározott tartományokkal és gazdagépekkel, és úgy is konfigurálható, hogy csak a kívánt gyakorisággal legyen elvégezhető. A szelektív tesztelést a Windows Management Instrumentation (WMI) használatával hajtja végre, amely a Microsoft szabványos parancsfájlkezelési nyelve a Windows rendszerek kezeléséhez.

> [!NOTE]
> - Egyszerre csak egy vizsgálatot lehet futtatni.
> - A legjobb eredményeket a tartományi vagy helyi rendszergazdai jogosultságokkal rendelkező felhasználók érhetik el.
> - A WMI-konfiguráció megkezdése előtt állítson be egy tűzfalszabály, amely az érzékelőről a beolvasott alhálózatra nyitja meg a kimenő forgalmat az 135-es UDP-port és a 1024-es TCP-portok használatával.

A mintavétel befejezésekor a rendszer az összes Szondázási kísérlettel rendelkező naplófájlt elérhetővé teszi a napló exportálásának lehetőségével. A napló tartalmazza az összes olyan IP-címet, amelyet a rendszer kipróbált. Az egyes IP-címek esetében a napló a sikeres és sikertelen műveletekre vonatkozó információkat jeleníti meg. Létezik egy hibakód is, amely a kivételből származtatott ingyenes karakterlánc. A rendszer csak az utolsó napló vizsgálatát tárolja.

Végrehajthat ütemezett vizsgálatokat vagy kézi vizsgálatokat is. A vizsgálat befejezésekor megtekintheti az eredményeket egy CSV-fájlban.

**Előfeltételek**

Olyan tűzfalszabály konfigurálása, amely a 135-es UDP-port és a 1024-es összes TCP-port használatával megnyitja a kimenő forgalmat az érzékelőről a beolvasott alhálózatra.

Automatikus vizsgálat konfigurálása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. Válassza a **Windows-végpont figyelése** lehetőséget :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="A Windows-végpontok figyelését bemutató képernyőkép.":::

3. Az **ellenőrzési ütemterv** panelen konfigurálja a beállításokat a következők szerint:

      - **Rögzített időközönként (órában)**: állítsa be a vizsgálat ütemtervét intervallumok szerint órában.

      - **Adott időpontok** szerint: állítsa be a vizsgálati ütemtervet adott időpontok szerint, és válassza a **vizsgálat mentése** lehetőséget.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="A vizsgálat mentése gombot megjelenítő képernyőkép.":::

4. A vizsgálati tartomány meghatározásához válassza a **vizsgálati tartományok beállítása** lehetőséget.

5. Állítsa be az IP-címtartományt, és adja hozzá a felhasználót és a jelszót.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="A felhasználó és a jelszó hozzáadását bemutató képernyőkép.":::

6. Ha ki szeretne zárni egy IP-címtartományt egy vizsgálatból, válassza a tartomány melletti **Letiltás** lehetőséget.

7. Tartomány eltávolításához válassza :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: a tartomány melletti lehetőséget.

8. Kattintson a **Mentés** gombra. A **keresési tartományok konfigurációjának szerkesztése** párbeszédpanel bezárul, és a tartományok száma megjelenik a **vizsgálati tartományok** ablaktáblán.

Manuális vizsgálat végrehajtása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. Válassza a **Windows-végpont figyelése** lehetőséget :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Képernyőkép, amely a Windows-végpont figyelése beállítás képernyőjét jeleníti meg.":::

3. A **műveletek** ablaktáblában válassza a **vizsgálat indítása** lehetőséget. Megjelenik egy állapotsor a **műveletek** ablaktáblán, és megjeleníti a beolvasási folyamat előrehaladását.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="A Start Scan (vizsgálat indítása) gombot megjelenítő képernyőkép.":::

A vizsgálati eredmények megtekintése:

1. Ha a vizsgálat befejeződött, a **műveletek** ablaktáblán válassza a **vizsgálat eredményeinek megtekintése** lehetőséget. A rendszer letölti a vizsgálat eredményeit tartalmazó CSV-fájlt a számítógépre.

## <a name="see-also"></a>Lásd még

[Érzékelő észlelésének vizsgálata egy eszköz leltározásakor](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Az érzékelő észlelésének vizsgálata az eszköz térképén](how-to-work-with-the-sensor-device-map.md)
