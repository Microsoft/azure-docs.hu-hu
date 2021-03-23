---
title: Tudnivalók a ServiceNow-integrációról
description: A ServiceNow-hez készült IoT ICS-kezelő alkalmazása a SOC-elemzőket többdimenziós láthatósággal biztosítja az ipari környezetekben üzembe helyezett specializált és IoT-eszközökhöz, valamint az ICS-kompatibilis viselkedés-elemzésekkel a gyanús vagy rendellenes viselkedés gyors észlelése érdekében.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786005"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>A ServiceNow-hez készült IoT ICS-kezelési alkalmazás védelmezője

A ServiceNow-hez készült IoT ICS-kezelő alkalmazása a SOC-elemzőket többdimenziós láthatósággal biztosítja az ipari környezetekben üzembe helyezett specializált és IoT-eszközökhöz, valamint az ICS-kompatibilis viselkedés-elemzésekkel a gyanús vagy rendellenes viselkedés gyors észlelése érdekében. Ez egy fontos fejlemény, amely a folyamatos konvergenciát, valamint az új IoT-kezdeményezéseket, például az intelligens gépeket és a valós idejű intelligenciát támogatja.

Az alkalmazás a vállalati SOC-n belül is lehetővé teszi az IT-és az incidens-válaszokat.

## <a name="about-defender-for-iot"></a>A IoT-hez készült Defender ismertetése

A IoT Defender a Blue-Team Experts által készített IoT kiberbiztonsági platformot biztosítja, amely a kritikus nemzeti infrastruktúrát, valamint az egyetlen olyan platformot tartalmazza, amely a szabadalmaztatott INTERNETKAPCSOLATtal rendelkező fenyegetések elemzését és a gépi tanulást is támogatja. A IoT Defender a következőket biztosítja:

- Az attribútumok széles körének részletes ismertetésével megtudhatja, hogyan biztosítható az internetkapcsolat megosztása az eszközön.

- IC-Aware – az OT-protokollok, az eszközök, az alkalmazások és a viselkedésük mély beágyazott ismerete.

- Azonnali áttekintés a sebezhetőségekről és az ismert zéró napi fenyegetésekről.

- Automatizált ICS-veszélyforrások modellezési technológiája, amellyel előre jelezheti a célként megadott IC-támadások legvalószínűbb elérési útját a saját elemzések révén

> [!Note]
> A CyberX mutató hivatkozások az Azure Defender for IoT szolgáltatásra vonatkoznak.

## <a name="about-the-integration"></a>Tudnivalók az integrációról

A ServiceNow-nal való IoT-integrációs Defender a IoT és az OT tájkép központi láthatóságának, monitorozásának és vezérlésének új szintjét kínálja. Ezek a Áthidalt platformok lehetővé teszik az eszközök automatikus láthatóságát és a fenyegetések kezelését a korábban nem elérhető ICS & IoT-eszközök számára.

### <a name="threat-management"></a>Fenyegetések kezelése

A IoT ICS felügyeleti alkalmazás védelmezője a következőket segíti:

- Csökkentse az iparági és a kritikus fontosságú infrastruktúra-szervezetek számára a számítógépes fenyegetések észlelését, kivizsgálását és működésének időtartamát.

- Valós idejű intelligenciát kaphat az OT érintő kockázatokról.

- A Defender IoT-riasztások korrelációja a ServiceNow-fenyegetések figyelésével és az incidensek kezelési munkafolyamataival.

- ServiceNow jegyek és munkafolyamatok elindítása más szolgáltatásokkal és alkalmazásokkal a ServiceNow platformon.

Az IC és a SCADA biztonsági fenyegetéseket a Defender azonosítja a IoT biztonsági motoroknál, amelyek azonnali riasztási választ adnak a kártevők elleni támadásokra, a hálózatra és a biztonsági házirendtől való eltérésekre, valamint a működési és a protokollok rendellenességére. További információ a ServiceNow elküldett riasztások részleteiről: [riasztási jelentéskészítés](#alert-reporting).

### <a name="device-visibility-and-management"></a>Eszközök láthatósága és kezelése

A ServiceNow-konfiguráció felügyeleti adatbázisa (CMDB) bővítve van, és kiegészítve a Defender for IoT platform által leküldött, számos eszköz-attribútummal. Ez biztosítja az eszköz tájképének átfogó és folyamatos láthatóságát, és lehetővé teszi egy önálló ablaktábla figyelését és megválaszolását. A ServiceNowSee-re eljuttatott eszközök attribútumaival kapcsolatos részletekért lásd: [Defender megtekintése IoT-észlelésekhez a ServiceNow-ben](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Rendszerkövetelmények és architektúra

Ez a cikk a következőket ismerteti:

- **A szoftverre vonatkozó követelmények**  
- **Architektúra**

## <a name="software-requirements"></a>Szoftverkövetelmények

- A ServiceNow Service Management 3.0.2 verziója

- Defender for IoT patch 2.8.11.1 vagy újabb verzió

> [!Note]
> Ha már rendelkezik egy Defenderrel a IoT és az ServiceNow-integrációhoz, és a helyszíni felügyeleti konzolról frissít, akkor a Defender által a IoT-érzékelőktől kapott áteresztő-adatok törlését el kell törölni a ServiceNow-ből.

## <a name="architecture"></a>Architektúra

### <a name="on-premises-management-console-architecture"></a>Helyszíni felügyeleti konzol architektúrája

A helyszíni felügyeleti konzol egységes forrást biztosít az összes eszköz-és riasztási információ ServiceNow való elküldésekor.

A ServiceNow egy példányával folytatott kommunikációhoz beállíthat egy helyszíni felügyeleti konzolt. A helyszíni felügyeleti konzol a REST API használatával leküldi az érzékelői IoT alkalmazást a Defendernek.

Ha úgy állítja be a rendszerét, hogy a helyszíni felügyeleti konzollal működjön, tiltsa le a ServiceNow-szinkronizálást, a továbbítási szabályokat és a proxy konfigurációit az érzékelőkben, ha azokat beállították.

Ezeket a konfigurációkat be kell állítani a helyszíni felügyeleti konzolra. A konfigurációs utasításokat a cikk ismerteti.

### <a name="sensor-architecture"></a>Érzékelő architektúrája

Ha úgy szeretné beállítani a környezetet, hogy az érzékelők és a ServiceNow között közvetlen kommunikációt tartalmazzon, minden érzékelőnél adja meg a ServiceNow-szinkronizálást, a továbbítási szabályokat és a proxy konfigurációját (ha van szükség proxyra).

Ajánlott beállítani az integrációt a helyszíni felügyeleti konzollal a ServiceNow való kommunikációhoz.

## <a name="create-access-tokens-in-servicenow"></a>Hozzáférési jogkivonatok létrehozása a ServiceNow-ben

Ez a cikk azt ismerteti, hogyan hozható létre hozzáférési jogkivonat a ServiceNow-ben. A jogkivonat a IoT való kommunikációhoz szükséges.

Az **ügyfél-azonosító** és az **ügyfél titkos kulcsa** szükséges ahhoz, hogy a Defender létrehozza a IoT továbbítási szabályait, amelyek a riasztási adatokat ServiceNow továbbítják, valamint amikor a Defender for IoT-t a ServiceNow táblákra küldi az eszköz attribútumait.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>A Defender beállítása a IoT a ServiceNow való kommunikációhoz

Ez a cikk azt ismerteti, hogyan állítható be a Defender a IoT a ServiceNow való kommunikációhoz.

### <a name="send-defender-for-iot-alert-information"></a>IoT riasztási információk küldése a Defender számára

Ez a cikk azt ismerteti, hogyan konfigurálható a Defender a IoT a riasztási információk ServiceNow-táblákba való leküldéséhez. További információ az elküldett riasztási adatokról: [riasztási jelentéskészítés](#alert-reporting).

A IoT-riasztások védelmezői a ServiceNow biztonsági incidensként jelennek meg.

A IoT *továbbítási* szabályának megadása a riasztási információk ServiceNow való elküldéséhez.

A szabály meghatározása:

1. A IoT bal oldali ablaktábláján válassza a **továbbítás** lehetőséget.  

1. Válassza a :::image type="content" source="media/integration-servicenow/plus.png" alt-text="plusz ikon gombot."::: ikonnal. Megnyílik a továbbítási szabály létrehozása párbeszédpanel.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Továbbítási szabály létrehozása":::

1. Adja hozzá a szabály nevét.

1. Meghatározhatja azokat a feltételeket, amelyekben a IoT Defender elindítja a továbbítási szabályt. A továbbítási szabályok feltételeit a IoT és a ServiceNow között a Defender által továbbított adatok mennyiségének meghatározása és kezelése segíti. A következő lehetőségek érhetők el:

    - **Súlyossági szintek:** Ez az a minimális biztonsági szintű incidens, amely a továbbításra szolgál. Ha például a **másodlagos** elem van kiválasztva, a kisebb riasztások és a súlyossági szint feletti riasztások továbbítva lesznek. A IoT a Defender előre definiálja a szinteket.

    - **Protokollok:** Csak akkor aktiválja a továbbítási szabályt, ha a észlelt forgalom adott protokollokon keresztül futott. Válassza ki a szükséges protokollokat a legördülő listából, vagy válassza ki őket.

    - **Motorok:** Válassza ki a szükséges motorokat, vagy válassza ki őket. A rendszer elküldi a kiválasztott motorok riasztásait.

1. Ellenőrizze, hogy be van-e jelölve a **jelentés riasztási értesítései** .

1. A műveletek szakaszban válassza a **Hozzáadás** lehetőséget, majd válassza a **ServiceNow** lehetőséget.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="A legördülő listából válassza a ServiceNow lehetőséget.":::

1. Adja meg a ServiceNow művelet paramétereit:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Adja meg a ServiceNow művelet paramétereit":::

1. A **műveletek** ablaktáblán állítsa be a következő paramétereket:

  | Paraméter | Leírás |
  |--|--|
  | Tartomány | Adja meg a ServiceNow-kiszolgáló IP-címét. |
  | Felhasználónév | Adja meg a ServiceNow-kiszolgáló felhasználónevét. |
  | Jelszó | Adja meg a ServiceNow-kiszolgáló jelszavát. |
  | Ügyfél-azonosító | Adja meg az IoT Defender számára kapott ügyfél-azonosítót a ServiceNow **alkalmazás-nyilvántartók** lapján. |
  | Titkos ügyfélkulcs | Adja meg az IoT Defender számára létrehozott ügyfél titkos karakterláncot a ServiceNow **alkalmazás-nyilvántartók** lapján. |
  | Jelentés típusa | **Incidensek**: továbbítja a riasztások listáját, amelyek a ServiceNow-ben jelennek meg az incidens-azonosítóval és az egyes riasztások rövid leírásával.<br /><br />**Defender for IoT Application**: továbbítsa a teljes riasztási információkat, beleértve az érzékelő részleteit, a motort, a forrást és a cél címét. A rendszer továbbítja az adatokat a Defendernek a ServiceNow alkalmazás IoT. |

1. Válassza a **Mentés** lehetőséget. A IoT-riasztások védelmezői incidensként jelennek meg a ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Defender küldése IoT-eszköz attribútumaihoz

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Defendert a IoT-hez, hogy az eszköz számos attribútumát leküldse a ServiceNow táblákra. A ServiceNow leküldett információk részleteit a ***leltári információkban*** tekintheti meg.

Az attribútumok ServiceNow való elküldéséhez a helyszíni felügyeleti konzolt le kell képeznie egy ServiceNow-példányra. Ez biztosítja, hogy a Defender for IoT platform képes legyen kommunikálni és hitelesíteni a példányt.

ServiceNow-példány hozzáadása:

1. Jelentkezzen be a Defender IoT helyszíni felügyeleti konzolján.

1. Válassza ki a **Rendszerbeállítások** elemet, majd a **ServiceNow** a helyszíni felügyeleti konzol integrációja szakaszban.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Kattintson a ServiceNow gombra.":::

1. Adja meg a következő szinkronizálási paramétereket a ServiceNow szinkronizálása párbeszédpanelen.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="A ServiceNow szinkronizálása párbeszédpanel.":::

     Paraméter | Leírás |
    |--|--|
    | Szinkronizálás engedélyezése | A paraméterek meghatározása után engedélyezheti és tilthatja le a szinkronizálást. |
    | Szinkronizálás gyakorisága (perc) | Alapértelmezés szerint minden 60 percenként leküldi az adatokat a ServiceNow. A minimális érték 5 perc. |
    | ServiceNow-példány | Adja meg a ServiceNow-példány URL-címét. |
    | Ügyfél-azonosító | Adja meg az IoT Defender számára kapott ügyfél-azonosítót a ServiceNow **alkalmazás-nyilvántartók** lapján. |
    | Titkos ügyfélkulcs | Adja meg az IoT Defender számára létrehozott ügyfél titkos karakterláncot a ServiceNow **alkalmazás-nyilvántartók** lapján. |
    | Felhasználónév | Adja meg a példány felhasználónevét. |
    | Jelszó | Adja meg a példányhoz tartozó jelszót. |

1. Válassza a **Mentés** lehetőséget.

## <a name="verify-communication"></a>Kommunikáció ellenőrzése

Ellenőrizze, hogy a helyszíni felügyeleti konzol csatlakoztatva van-e a ServiceNow-példányhoz az *utolsó szinkronizálás* dátumának áttekintésével.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Ellenőrizze, hogy a kommunikáció a legutóbbi szinkronizálással történt-e.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Az integráció beállítása a HTTPS-proxy használatával

A IoT és a ServiceNow integrációjának beállításakor a helyszíni felügyeleti konzol és a ServiceNow-kiszolgáló a 443-es porton keresztül kommunikál. Ha a ServiceNow-kiszolgáló a proxy mögött van, az alapértelmezett port nem használható.

A IoT Defender támogatja a HTTPS-proxykat a ServiceNow-integrációban azáltal, hogy engedélyezi az integrációhoz használt alapértelmezett port módosítását.

A proxy konfigurálása:

1. Globális Tulajdonságok szerkesztése a helyszíni felügyeleti konzolon:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Adja hozzá a következő paramétereket:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Mentés és kilépés.

4. Futtassa a következő parancsot: `sudo monit restart all`

A konfigurálást követően a rendszer az összes ServiceNow-adattovábbítást a konfigurált proxy használatával továbbítja.

## <a name="download-the-defender-for-iot-application"></a>A Defender for IoT alkalmazás letöltése

Ez a cikk az alkalmazás letöltését ismerteti.

A Defender for IoT alkalmazás elérése:

1. Navigáljon ide: <https://store.servicenow.com/>

2. Keressen a vagy a kifejezésre `Defender for IoT` `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Keresse meg a CyberX a keresősáv alatt.":::

3. Válassza ki az alkalmazást.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Válassza ki az alkalmazást a listából.":::

4. Válassza a **kérelem alkalmazás lehetőséget.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Jelentkezzen be az alkalmazásba a hitelesítő adataival.":::

5. Jelentkezzen be, és töltse le az alkalmazást.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>IoT-észlelések Defender megtekintése a ServiceNow-ben

Ez a cikk a ServiceNow-ben bemutatott eszköz-attribútumokat és riasztási információkat ismerteti.

Az eszköz attribútumainak megtekintése:

1. Jelentkezzen be a ServiceNow.

2. Navigáljon a **CyberX platformra**.

3. Navigáljon a **leltárhoz** vagy a **riasztáshoz**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Leltár vagy riasztás":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Leltár adatai

A Configuration Management-adatbázist (CMDB) gazdagítják és kiegészítik a Defender által a IoT-ServiceNow számára továbbított adatszolgáltatások. Az ServiceNow CMDB-konfigurációs elem tábláiban szereplő, az eszköz attribútumainak hozzáadásával vagy frissítésével a Defender for IoT a ServiceNow munkafolyamatait és üzleti szabályait aktiválhatja.

A következő információk érhetők el:

- Az eszköz attribútumai, például az eszköz MAC, operációs rendszer, szállító vagy protokollja észlelhető.

- Belső vezérlőprogram adatai, például a belső vezérlőprogram verziója és sorozatszáma.

- Csatlakoztatott eszköz adatai, például a forrás és a cél közötti forgalom iránya.

### <a name="devices-attributes"></a>Eszközök attribútumai

Ez a cikk a ServiceNow leküldett eszköz-attribútumokat ismerteti.

| Elem | Leírás |
|--|--|
| Berendezés | A forgalmat észlelő érzékelő neve. |
| ID (Azonosító) | A Defender által a IoT-hez rendelt eszköz azonosítója. |
| Name | Az eszköz neve. |
| IP-cím | Az eszköz IP-címe vagy címei. |
| Típus | Az eszköz típusa, például egy kapcsoló, egy PLC, egy történész vagy egy tartományvezérlő. |
| MAC-cím | Az eszköz MAC-címe vagy címei. |
| Operációs rendszer | Az eszköz operációs rendszere. |
| Szállító | Az eszköz gyártója. |
| Protokollok | Az eszköz által generált forgalomban észlelt protokollok. |
| Tulajdonos | Adja meg az eszköz tulajdonosának nevét. |
| Hely | Adja meg az eszköz fizikai helyét. |

Az eszközhöz csatlakozó eszközök megtekintése ebben a nézetben.

A csatlakoztatott eszközök megtekintése:

1. Válasszon ki egy eszközt, majd válassza ki az eszközben **felsorolt készüléket** .

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Válassza ki a kívánt készüléket a listából.":::

1. Az **eszköz részletei** párbeszédpanelen válassza a **csatlakoztatott eszközök** elemet.

### <a name="firmware-details"></a>Belső vezérlőprogram részletei

Ez a cikk az eszköz belső vezérlőprogram-információit mutatja be a ServiceNow-be leküldve.

| Elem | Leírás |
|--|--|
| Berendezés | A forgalmat észlelő érzékelő neve. |
| Eszköz | Az eszköz neve. |
| Cím | Az eszköz IP-címe. |
| Modul címe | Az eszköz modellje és a tárolóhely száma vagy azonosítója. |
| Sorozatszám | Az eszköz sorozatszáma. |
| Modellezés | Az eszköz modelljének száma. |
| Verzió | A belső vezérlőprogram verziószáma. |
| Kiegészítő adatok | További információk a belső vezérlőprogram számára a szállító által meghatározott módon, például az eszköz típusa. |

### <a name="connection-details"></a>Kapcsolat adatai

Ez a cikk a ServiceNow leküldett eszköz kapcsolódási adatait ismerteti.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Az eszköz csatlakoztatási adatai":::

| Elem | Leírás |
|--|--|
| Berendezés | A forgalmat észlelő érzékelő neve. |
| Irány | A forgalom iránya. <br /> <br /> - Az **egyik módszer** azt jelzi, hogy a cél a kiszolgáló, a forrás pedig az ügyfél. <br /> <br /> - A **két módszer** azt jelzi, hogy a forrás és a cél kiszolgálók, vagy az ügyfél ismeretlen. |
| Forrásoldali eszköz azonosítója | Annak az eszköznek az IP-címe, amely a csatlakoztatott eszközzel kommunikált. |
| Forrásoldali eszköz neve | Annak az eszköznek a neve, amely a csatlakoztatott eszközzel kommunikált. |
| Cél eszköz azonosítója | A csatlakoztatott eszköz IP-címe. |
| Céleszköz neve | A csatlakoztatott eszköz neve. |

## <a name="alert-reporting"></a>Riasztási jelentéskészítés

A riasztások akkor aktiválódnak, ha a IoT-motorok védelmezői megfigyelik a hálózati forgalom és a beavatkozást igénylő viselkedés változásait. Az egyes motorok által generált riasztások típusairól további [információt a riasztási motorokkal](#about-alert-engines)foglalkozó témakörben talál.

Ez a cikk az eszköz riasztási információit írja le a ServiceNow.

| Elem | Leírás |
|--|--|
| Létrehozva | A riasztás létrehozásának időpontja és dátuma. |
| Motor | Az eseményt észlelő motor. |
| Cím | A riasztás címe. |
| Leírás | A riasztás leírása. |
| Protokoll | A protokoll a forgalomban észlelt. |
| Súlyosság | A Defender által a IoT számára meghatározott riasztás súlyossága. |
| Berendezés | A forgalmat észlelő érzékelő neve. |
| Forrás neve | A forrás neve. |
| Forrás IP-címe| A forrás IP-címe. |
| Cél neve | A cél neve. |
| Cél IP-cím | A cél IP-címe. |
| Megbízott | Adja meg a jegyhez rendelt személy nevét. |

### <a name="updating-alert-information"></a>Riasztási adatok frissítése

Válassza ki a létrehozott oszlopban lévő bejegyzést a riasztási információk űrlapon való megtekintéséhez. A riasztás részleteit frissítheti, és a riasztást hozzárendelheti egy személyhez, és áttekintheti és kezelheti azokat.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="A riasztás adatainak megtekintése.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Tudnivalók a riasztási motorokról

Ez a cikk az egyes motor-eseményindítók riasztási típusait ismerteti.

| Riasztástípus | Leírás |
|--|--|
| Szabályzat megsértésével kapcsolatos riasztások | Akkor aktiválódik, ha a házirend-megsértési motor észleli a korábban megszerzett forgalomtól való eltérést. Például: <br /><br />– A rendszer új eszközt észlelt. <br /><br />– Új konfiguráció észlelhető az eszközön. <br /><br />– A programozási eszközként nem definiált eszközök programozási változást végeznek. <br /><br />– A belső vezérlőprogram verziója megváltozott. |
| Protokollok megsértésével kapcsolatos riasztások | Akkor aktiválódik, ha a protokoll-megsértési motor olyan csomagok szerkezetét vagy mezőértékeket észlel, amelyek nem felelnek meg a protokoll specifikációjának. |
| Működési riasztások | Akkor aktiválódik, ha az operatív motor észleli a hálózati működési incidenseket vagy az eszköz meghibásodását. Például egy hálózati eszköz le lett állítva egy leállítási PLC-paranccsal, vagy az érzékelő felülete leállította a forgalom figyelését. |
| Kártevő-riasztások | Akkor aktiválódik, ha a kártevő-kezelő észleli a kártékony hálózati tevékenységeket, például a Conficker-et. |
| Anomáliák – riasztások | Akkor aktiválódik, ha a rendellenességet kezelő motor eltérést észlel. Például egy eszköz hálózati vizsgálatot végez, de nincs meghatározva ellenőrzési eszközként. |

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
