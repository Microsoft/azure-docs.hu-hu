---
title: Tudnivalók a Forescout-integrációról
description: A Forescout platformmal való IoT-integrációt biztosító Azure Defender új szintet biztosít a IoT és az OT-tájkép központosított láthatóságának, monitorozásának és vezérlésének.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784067"
---
# <a name="about-the-forescout-integration"></a>Tudnivalók a Forescout-integrációról

Az Azure Defender for IoT egy Blue Team-szakértők által készített ICS-és IoT kiberbiztonsági platformot biztosít a kritikus nemzeti infrastruktúra védelmének nyomon követésével. A IoT Defender az egyetlen olyan platform, amelyen a szabadalmaztatott INTERNETKAPCSOLATtal rendelkező fenyegetések elemzése és a gépi tanulás van. A IoT Defender a következőket biztosítja:

- Az attribútumok széles körének részletes ismertetésével megtudhatja, hogyan biztosítható az internetkapcsolat megosztása az eszközön.
- IC-Aware – az OT-protokollok, az eszközök, az alkalmazások és a viselkedésük mély beágyazott ismerete.
- Azonnali ismeretek a sebezhetőségekről, valamint az ismert és a nulla napi fenyegetésekről.
- Automatizált ICS-veszélyforrások modellezési technológiája, amellyel előre jelezheti a célként megadott IC-támadások legvalószínűbb elérési útját a saját elemzések révén

A Forescout platformmal való IoT-integrációs Defender új szintű központosított láthatóságot, monitorozást és vezérlést biztosít a IoT és az OT-környezet számára.

Ezek a Áthidalt platformok lehetővé teszik az eszközök automatikus láthatóságát és felügyeletét a korábban nem elérhető ICS-eszközök és a silózott munkafolyamatok számára.

Az integráció olyan SOC-elemzőket biztosít, amelyek többszintű láthatóságot biztosítanak az ipari környezetekben üzembe helyezett OT-protokollokhoz. A részletek az olyan elemekhez érhetők el, mint a belső vezérlőprogram, az eszközök típusai, az operációs rendszerek és a kockázatelemzési pontszámok a IoT-technológiák tulajdonosi Azure Defender-technológiái alapján.

> [!Note]
> A CyberX mutató hivatkozások az Azure Defender for IoT szolgáltatásra vonatkoznak.
## <a name="devices"></a>Eszközök

### <a name="device-visibility-and-management"></a>Eszközök láthatósága és kezelése

Az eszköz leltározása a Defender for IoT platform által észlelt kritikus attribútumokkal gazdagítva. Ez a következőket biztosítja:

- Átfogó és folyamatos láthatóságot nyerhet az OT eszközön egy egyablakos környezetben.
- Valós idejű intelligenciát kaphat az OT érintő kockázatokról.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Eszköz leltározása":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Eszköz adatai":::

### <a name="device-control"></a>Eszköz vezérlése

A Forescout-integráció segít csökkenteni az iparági és kritikus fontosságú infrastrukturális szervezetek számára a számítógépes fenyegetések észlelését, kivizsgálását és működésének szükségességét.

- Az Azure Defender használata a IoT OT-eszköz intelligenciával a Forescout házirend műveleteinek elindításával zárhatja be a biztonsági ciklust. Automatikusan riasztási e-mailt küldhet például a SOC-rendszergazdáknak, amikor a rendszer adott protokollokat észlel, vagy ha megváltoznak a belső vezérlőprogram adatai.

- A Defender összekapcsolása a IoT és más *Forescout eyeExtended* -modulokkal, amelyek a figyelést, az incidensek kezelését és az eszközök vezérlését felügyelik.

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Azure Defender a IoT 2,4-es vagy újabb verziójával
- Forescout 8,0-es vagy újabb verzió
- Az Azure Defender for IoT platform *Forescout eyeExtend* moduljának licence.

### <a name="getting-more-forescout-information"></a>További Forescout adatok beolvasása

További információ a Forescout platformról: [Forescout Resource Center](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Rendszerbeállítás

Ez a cikk a Defender for IoT platform és a Forescout platform közötti kommunikáció beállítását ismerteti.

### <a name="set-up-the-defender-for-iot-platform"></a>A Defender beállítása a IoT platformhoz

Ha biztosítani szeretné, hogy a Defender a IoT a Forescout-hez kommunikáljon, állítson be egy hozzáférési jogkivonatot a Defenderben a IoT.

A hozzáférési jogkivonatok lehetővé teszik a külső rendszerek számára, hogy hozzáférjenek a Defender által felderített adatokhoz a IoT számára, és műveleteket hajtanak végre a külső REST API használatával az SSL-kapcsolatokon keresztül Hozzáférési jogkivonatokat is létrehozhat a IoT REST API Azure Defender eléréséhez.

Jogkivonat létrehozása:

1. Jelentkezzen be a Defender for IoT-érzékelőre, amelyet a Forescout fog lekérdezni.

1. Válassza a **Rendszerbeállítások** lehetőséget, majd az **általános** szakaszban válassza a **hozzáférési jogkivonatok** lehetőséget. Megnyílik a **hozzáférési tokenek** párbeszédpanel.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Hozzáférési jogkivonatok":::
1. Válassza az **új jogkivonat előállítása** lehetőséget a **hozzáférési jogkivonatok** párbeszédpanelen.
1. Adja meg a jogkivonat leírását az **új hozzáférési jogkivonat** párbeszédpanelen.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Új hozzáférési jogkivonat":::
1. Kattintson a **Tovább** gombra. A jogkivonat megjelenik a párbeszédpanelen. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Token megtekintése":::
   > [!NOTE]
   > *Rögzítse a tokent egy biztonságos helyen. A Forescout platform konfigurálásakor szüksége lesz rá*.
1. Válassza a **Befejezés** gombot.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Token hozzáadásának befejezése":::

### <a name="set-up-the-forescout-platform"></a>A Forescout platform beállítása

Beállíthatja a Forescout platformot a Defender for IoT érzékelővel való kommunikációhoz.

A beállításhoz:

1. Telepítse *a Forescout eyeExtend modult a CyberX-hez* a Forescout platformon.

1. Jelentkezzen be az ellensúlyozására konzolra, és válassza az **eszközök** menü **Beállítások** elemét. Megnyílik a **Beállítások** párbeszédpanel.

1. Navigáljon a **modulokhoz** , és válassza ki a modulok mappát.

1. A **modulok** ablaktáblán válassza a **CyberX platform** elemet. Megnyílik a Defender for IoT platform panel.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender IoT-modul beállításai":::

   Adja meg a következő információkat:

   - **Kiszolgáló címe** – adja meg a Forescout-berendezés által lekérdezett IoT-érzékelőhöz tartozó Defender IP-címét.
   - **Hozzáférési jogkivonat** – adja meg a Defender for IoT érzékelőhöz generált hozzáférési jogkivonatot, amely csatlakozni fog a Forescout-berendezéshez. A jogkivonat létrehozásával kapcsolatban tekintse [meg a Defender beállítása IoT platformhoz](#set-up-the-defender-for-iot-platform)című témakört.

1. Kattintson az **Alkalmaz** gombra.

Ha azt szeretné, hogy a Forescout platform kommunikáljon egy másik érzékelővel:

1. Hozzon létre egy új hozzáférési jogkivonatot a megfelelő Defender for IoT-érzékelőben.

1. A **Forescout-modulok**  >  **CyberX platformja** párbeszédpanelen:

   - Törölje a megjelenő információkat.
   
   - Adja meg az új érzékelő IP-címét és az új hozzáférési jogkivonat információit.

### <a name="verify-communication"></a>Kommunikáció ellenőrzése

Miután konfigurálta a Defendert a IoT és a Forescout, nyissa meg az érzékelő hozzáférési jogkivonatok párbeszédpanelt a Defender for IoT.

Ha **N/A megjelenik** a jogkivonat **használt** mezőjében, az érzékelő és a Forescout készülék közötti kapcsolat nem működik.

A **használat** azt jelzi, hogy a rendszer mikor fogadta el a tokenhez tartozó külső hívást.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="A jogkivonat fogadásának ellenőrzése":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>IoT-észlelések Defender megtekintése a Forescout-ben

Az eszköz attribútumainak megtekintése:

1. Jelentkezzen be a Forescout platformra, majd navigáljon az **adatkészlethez**.

1. Navigáljon a **CyberX platformra**. A következő eszközbeállítások láthatók a Defender által a IoT számára észlelt eszközökön.

   | Elem | Leírás |
   |--|--|
   | A IoT-hez készült Azure Defender által felhatalmazott | A hálózati tanulási időszakban a IoT Defender által a hálózaton észlelt eszköz. |
   | Belső vezérlőprogram | Az eszköz belső vezérlőprogram adatai. Például a modell és a verzió részleteit. |
   | Name | Az eszköz neve. |
   | Operációs rendszer | Az eszköz operációs rendszere. |
   | Típus | Az eszköz típusa. Például egy PLC, történész vagy mérnöki állomás. |
   | Szállító | Az eszköz gyártója. Például: Rockwell Automation. |
   | Kockázati szint | A Defender által a IoT számára kiszámított kockázati szint. |
   | Protokollok | Az eszköz által generált forgalomban észlelt protokollok. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Megtekintheti a belső vezérlőprogram attribútumait.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Megtekintheti a szállítók attribútumait.":::

### <a name="viewing-more-details"></a>További részletek megtekintése

A Defender által a IoT-hez irányított eszközök további eszköz-információinak megtekintése. Például Forescout megfelelőségi és házirend-információkat.

Ennek elvégzéséhez kattintson a jobb gombbal egy eszközre az **eszközök leltári gazdagépek** szakaszban. Megnyílik a gazdagép adatai párbeszédpanel, ahol további információkat talál.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Gazdagép adatai":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Azure Defender létrehozása IoT-szabályzatokhoz a Forescout-ben

A Forescout szabályzatok segítségével automatizálhatja a Defender által a IoT számára észlelt eszközök felügyeletét és felügyeletét. Példa:

- A SOC-rendszergazdák automatikus e-mail-címe, ha a rendszer adott belső vezérlőprogram-verziókat észlel.

- Az incidensek és biztonsági munkafolyamatok további kezeléséhez, például más SIEM-integrációk esetén a IoT által észlelt eszközökhöz hozzáadhat egy Forescout-csoportot.

Hozzon létre egy egyéni Forescout-szabályzatot a IoT Defender használatával a feltétel tulajdonságaival.

A Defender IoT tulajdonságainak elérése:

1. Navigáljon a **Tulajdonságok fájára** a **házirend feltételei** párbeszédpanelen.

1. Bontsa ki a **CyberX platform** mappát a **Tulajdonságok fában**. A IoT Defender a következő tulajdonságokat veheti igénybe.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Tulajdonságok":::

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
