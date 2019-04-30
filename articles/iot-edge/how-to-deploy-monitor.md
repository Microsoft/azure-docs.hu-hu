---
title: Az automatikus központi telepítés létrehozása az Azure portal – Azure IoT Edge |} A Microsoft Docs
description: Eszközök automatikus csoportok az IoT Edge üzemelő példány létrehozása az Azure portal használatával
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 69ba0a882c0e52e7c0d063b8f77e7a0fe22526a1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126364"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Üzembe helyezés és monitorozás az Azure portal használatával nagy mennyiségű IoT Edge-modulok

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Az Azure IoT Edge segítségével elemzési áthelyezheti az Edge-ben, és a egy felhőbeli felületet biztosít, kezelheti és figyelheti az IoT Edge-eszközök anélkül, hogy mindegyikhez fizikailag el kellene. Távolról kezelheti az eszközöket a funkció fontos egyre, IOT-megoldások nagyobb és összetettebb növekszik. Az Azure IoT Edge célja az üzleti céljaihoz, függetlenül attól, hogy hány eszköz hozzáadása támogatása.

Egyes eszközök kezelése és a modulok üzembe őket egyenként. Azonban ha nagy méretű eszközök módosításokat szeretne, létrehozhat egy **IoT Edge automatikus központi telepítési**, amely az IoT Hub automatikus kezelés része. Központi telepítések dinamikus folyamatokat, amelyek lehetővé teszik, hogy helyezze üzembe egyszerre több modul több eszközön, nyomon követheti az állapotát és a modulok állapotát, és szükség esetén módosítható. 

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Központi telepítés létrehozásához, akkor megadhatja, mely eszközöket szeretné befolyásolni. Az Azure IoT Edge használatával eszközök azonosítja **címkék** az ikereszközben. Minden eszköz rendelkezhet több címkét, és meghatározhatja azokat bármilyen módon, amely logikus a megoldáshoz. Például ha Ön kezeli a telephelyi intelligens épületek, előfordulhat, hogy hozzá a következő címkék eszköz:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Ikereszközök és címkékkel kapcsolatos további információkért lásd: [ikereszközök megismerése és használata az IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

1. Az a [az Azure portal](https://portal.azure.com), keresse fel az IoT hubnak. 
1. Válassza ki **IoT Edge**.
1. Válassza ki **üzemelő IoT Edge-példány hozzáadása**.

Vannak egy központi telepítést hozhat létre öt lépést. A következő szakaszok egyenként végig. 

### <a name="step-1-name-and-label"></a>1. lépés: Név és címke

1. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűk használata a központi telepítés. Kerülje a tárolóhelyek és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. Címkék, kulcs-érték párok nyomon követéséhez az üzemelő példányok is hozzáadhat. Például **HostPlatform** és **Linux**, vagy **verzió** és **3.0.1-es**.
1. Válassza ki **tovább** áthelyezése két lépést. 

### <a name="step-2-add-modules-optional"></a>2. lépés: (Nem kötelező) modulok hozzáadása

Amelyek egy központi telepítést adhat hozzá két típusa van. Az első az alapján az Azure-szolgáltatások, például a Storage-fiók vagy a Stream Analytics modul. A második pedig a saját kód használata modul. Több modul mindkét központi telepítés is hozzáadhat. 

Modulok nélküli egy központi telepítést hoz létre, ha az eszközök aktuális modulokat eltávolítása. 

>[!NOTE]
>Az Azure Functions automatizált Azure szolgáltatás üzembe helyezésének még nem támogatja. A modul egyéni központi telepítés segítségével manuálisan adja hozzá a szolgáltatás a központi telepítés. 

Az Azure Stream Analytics modul hozzáadásához kövesse az alábbi lépéseket:

1. Az a **üzembe helyezési modulok** szakaszban kattintson az oldal **Hozzáadás**.
1. Válassza ki **Azure Stream Analytics modul**.
1. Válassza ki a **előfizetés** a legördülő menüből.
1. Válassza ki a **Edge-feladat** a legördülő menüből.
1. Válassza ki **mentése** a modul hozzáadása a központi telepítést. 

Egyéni kód hozzáadása modulként, vagy manuálisan adja hozzá az Azure-szolgáltatások modul, kövesse az alábbi lépéseket:

1. Az a **tároló-beállításjegyzékek beállításai** szakasz az oldal, adja meg a neveket és a hitelesítő adatok bármelyik privát tárolójegyzékek, amelyek tartalmazzák a modul képek az üzembe helyezés. Az Edge Agent küld jelentést 500-as hiba, ha egy Docker-rendszerkép a container registry hitelesítő adat nem található.
1. Az a **üzembe helyezési modulok** szakaszban kattintson az oldal **Hozzáadás**.
1. Válassza ki **IoT Edge-modul**.
1. A modul adjon egy **neve**.
1. Az a **rendszerkép URI** mezőben adja meg a tároló rendszerképét a modul. 
1. Bármely **tároló létrehozása beállítások** , amely a tárolóhoz kell átadni. További információkért lásd: [docker létrehozása](https://docs.docker.com/engine/reference/commandline/create/).
1. Válassza ki a legördülő menü használatával egy **újraindítási házirend**. Az alábbi lehetőségek közül választhat: 
   * **Mindig** – a modul mindig újraindul, ha valamilyen okból vannak leáll.
   * **Soha ne** – a modul soha nem indul újra, ha valamilyen okból vannak leáll.
   * **A sikertelen** – a modul újraindítása, ha azt összeomlik, de nem Ha, szabályszerűen álljon le. 
   * **A sérült** – a modul újraindul, ha összeomlik, vagy egy nem megfelelő állapotot ad vissza. Szolgáltatás minden modulnak a health állapot függvény végrehajtásához. 
1. A legördülő menü segítségével válassza ki a **kívánt állapot** modul. Az alábbi lehetőségek közül választhat:
   * **Futó** – Ez az alapértelmezett beállítás. A modul futtatása után azonnal parancsfájlműveletekkel elindul.
   * **Leállítva** -után üzembe helyezéséhez, a modul tétlen mindaddig, amíg Ön vagy egy másik modul első lépésként alapján kell marad.
1. Válassza ki **Set ikermodul kívánt tulajdonságai** , ha a címkék vagy egyéb tulajdonságok hozzáadása az ikermodul kívánt.
1. Adja meg **környezeti változók** a modulhoz. A környezeti változók a konfigurációs folyamat megkönnyítése modul supplement információt tartalmaznak.
1. Válassza ki **mentése** a modul hozzáadása a központi telepítést. 

Ha már konfigurált összes modult, válassza ki a **tovább** szeretne váltani a harmadik lépése.

### <a name="step-3-specify-routes-optional"></a>3. lépés: Adja meg az útvonalakat (nem kötelező)

Útvonalak megadása, hogyan modulok kommunikálnak egymással egy telepítésben található. A varázsló lehetővé teszi, hogy alapértelmezés szerint egy útvonal neve **útvonal** és meghatározott, **FROM /* be felső $**, ami azt jelenti, kimenetét kapcsolt modulok üzeneteket az IoT hubnak küldött.  

Hozzáadása vagy frissítése az útvonalak származó adatokkal [útvonalak deklarálja](module-composition.md#declare-routes), majd **tovább** továbbra is a felülvizsgálati szakaszban.

### <a name="step-4-specify-metrics-optional"></a>4. lépés: Adja meg a metrikák (nem kötelező)

Metrikák adja meg az eszköz lehet, hogy jelentéseket küldhetnek vissza alkalmazása konfigurációjának tartalma eredményeként állapotait összesítő számát.

1. Adjon meg egy nevet **metrika neve**.

1. Adjon meg egy lekérdezést a **metrika feltételek**. A lekérdezés alapul IoT Edge hubot ikermodul [jelentett tulajdonságokként](module-edgeagent-edgehub.md#edgehub-reported-properties). A metrika a lekérdezés által visszaadott sorok számát jelöli.

Példa:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="step-5-target-devices"></a>5. lépés: Céleszközök

Az eszközökről a tags tulajdonság használatával a kívánt eszközök, amelyek megkapják a központi telepítés céljaként. 

Több központi telepítést megcélozhatnak ugyanarra az eszközre, mivel egy prioritást minden egyes üzembe helyezési kell adnia. Minden eddiginél van ütközés, ha az üzemelő példány (a magasabb értékek azt jelzik a magasabb prioritású) a legmagasabb prioritású wins. Ha két üzembe helyezés azonos prioritású számot, azt, amelyik a legtöbb készült nemrégiben wins. 

1. Adja meg egy pozitív egész számot a központi telepítés **prioritású**. Abban az esetben, ha két vagy több üzemelő példány célzott ugyanarra az eszközre, az üzembe helyezés a legnagyobb numerikus értékkel prioritás érvényes lesz.
1. Adjon meg egy **feltétel cél** meghatározni, hogy mely eszközök érinteni fog a központi telepítés. A feltétel device twin címkék alapján vagy az ikereszköz jelentett tulajdonságait, és meg kell egyeznie a kifejezés formátuma. Ha például `tags.environment='test'` vagy `properties.reported.devicemodel='4000x'`. 
1. Válassza ki **tovább** , továbbléphet az utolsó lépés.

### <a name="step-6-review-deployment"></a>6. lépés: Üzembe helyezés áttekintése

Tekintse át a telepítési adatokat, majd válassza a **küldés**.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace-ről modulok telepítése

Az Azure Marketplace-en, tallózhat a vállalati alkalmazások és megoldások, hitelesített és optimalizált futtatásához az Azure-ban számos online alkalmazások és szolgáltatások piactérről köztük [IoT Edge-modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Az Azure Marketplace-en is elérhető az Azure Portalon a **erőforrás létrehozása**.

Azure Marketplace-en vagy az Azure Portalon lehet telepíteni az IoT Edge-modul:

1. Keresse meg a modult, és a telepítési folyamat.

   * Az Azure Portalon: Egy modul megkeresése és kiválasztása **létrehozás**.

   * Az Azure Marketplace-en:

     1. Egy modul megkeresése és kiválasztása **Letöltés most**.
     1. Tudomásul veszi a szolgáltató használati feltételei és adatvédelmi szabályzat kiválasztásával **Folytatás**.

1. Válassza ki az előfizetés és az IoT Hub, az eszköznek, amelyhez csatlakozik.

1. Válasszon **üzembe helyezés**.

1. Döntse el, hogy a modul hozzáadása egy új központi telepítést, vagy egy meglévő központi telepítése; klónja Ha a klónozás, válassza ki a listából a meglévő üzemelő példányt.

1. Válassza ki **létrehozás** folytatja a központi telepítés ipari méretekben történő létrehozásának folyamatán. Fogja tudni adja meg azonos módon környezethez.

## <a name="monitor-a-deployment"></a>Egy központi telepítésének figyelése

A központi telepítés a részletek megtekintéséhez és az azt futtató eszközök figyelését, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot. 
1. Válassza ki **IoT Edge**.
1. Válassza ki **IoT Edge-telepítések**. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vizsgálja meg a központi telepítés listában. Minden egyes üzemelő példányhoz a következő adatokat tekintheti meg:
   * **ID** – a központi telepítés nevét.
   * **A feltétel cél** – a címke segítségével határozhatók meg a megcélzott eszközökön.
   * **Prioritás** – a prioritást az üzemelő példányhoz társítva.
   * **Rendszermérőszámokat** - **célzott** ikereszközök számát adja meg, amely a célcsoport-kezelési feltételnek megfelelő IoT hub és **alkalmazott** rendelkező eszközök számát adja meg a telepítési tartalom alkalmazza kellett az ikermodulokkal az IoT hubon. 
   * **Eszközmetrikák** – a központi telepítés sikeres vagy hibákat az IoT Edge-ügyfél futtatókörnyezet reporting Edge-eszközök számát.
   * **Egyéni metrikák** – a központi telepítés minden mérőszámadatok, amelyet a központi telepítés megadott reporting Edge-eszközök számát.
   * **Létrehozás ideje** – a központi telepítés létrehozásakor időbélyege. Az időbélyegző ties megszüntetése, ha a két üzembe helyezés azonos prioritású szolgál. 
1. Válassza ki a figyelni kívánt telepítést.  
1. Vizsgálja meg az üzembe helyezés részleteiről. Lapok segítségével ellenőrizze az üzemelő példány részleteit.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Ha egy eszköz nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és a központi telepítés rendszer a legmagasabb prioritású az eszközön, a központi telepítéshez az eszköz van alkalmazva. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célfeltétel, eltávolítja a központi telepítés és veszi fel a következő legmagasabb prioritású üzembe helyezés. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célfeltétel más központi telepítések, majd nincs változás történik az eszközön. Az eszköz addig a jelenlegi modulok fut, a jelenlegi állapotuk, de nem felügyelt már a központi telepítés részeként. Megfelel a célfeltétel, bármely más konfigurációért, miután eltávolítja a központi telepítés, és az új kiszolgálón vesz igénybe. 

Központi telepítés módosításához használja az alábbi lépéseket: 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot. 
1. Válassza ki **IoT Edge**.
1. Válassza ki **IoT Edge-telepítések**. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Válassza ki a telepítést, amelyet módosítani szeretne. 
1. Hajtsa végre a frissítéseket a következő mezőket: 
   * Célfeltétel
   * Metrika -, módosíthatják vagy törölhetik metrikák meghatározta, vagy újakat vehet fel.
   * Címkék
   * Prioritás
1. Kattintson a **Mentés** gombra.
1. Kövesse a [központi telepítésének figyeléséről](#monitor-a-deployment) megkezdik a módosítások megtekintéséhez. 

## <a name="delete-a-deployment"></a>Üzemelő példányának törlése

Ha töröl egy központi telepítést, a következő legmagasabb prioritású üzembe helyezés az egyik eszközön sem igénybe vehet. Ha az eszközök nem felelnek meg a célfeltétel, bármely más konfigurációért, majd a modulok nem lesznek eltávolítva az üzemelő példány törlése. 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot. 
1. Válassza ki **IoT Edge**.
1. Válassza ki **IoT Edge-telepítések**. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. A jelölőnégyzet segítségével válassza ki a törölni kívánt központi telepítés. 
1. Válassza a **Törlés** elemet.
1. Kérdés megtudhatja, hogy, hogy ez a művelet törli a központi telepítés és minden eszköz korábbi állapotára visszaállítani.  Ez azt jelenti, hogy egy alacsonyabb prioritású üzembe helyezés során vonatkoznak.  Ha nincs másik üzemelő példány vonatkozik, nem modulok távolítja el. Ha el kívánja távolítani az összes modult az eszközről, nulla modulok egy központi telepítés létrehozásához, és telepítheti az ugyanazokkal az eszközökkel. Válassza ki **Igen** folytatásához. 

## <a name="next-steps"></a>További lépések

Tudjon meg többet [modulok üzembe helyezéséhez a peremhálózati eszközökre](module-deployment-monitoring.md).
