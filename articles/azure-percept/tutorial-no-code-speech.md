---
title: Hangsegéd létrehozása az Azure Percept DK és az Azure Percept audio használatával
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe kód nélküli beszédfelismerési megoldást az Azure Percept DK-be
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: de85c4f8cdcd9781345ee1488549aab23e38ec5c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664917"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Hangsegéd létrehozása az Azure Percept DK és az Azure Percept audio használatával

Ebben az oktatóanyagban létre fog hozni egy hangsegédet egy sablonból, amelyet az Azure Percept DK és az Azure Percept hang használatával használhat. A hangsegéd bemutatója az [Azure Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819) fut, és a hang által vezérelt virtuális objektumokat is tartalmazza. Egy objektum vezérléséhez tegyük fel a kulcsszót, amely egy szó vagy rövid kifejezés, amely felébreszti az eszközt, majd egy parancs után. Minden sablon adott parancsokra válaszol.

Ebből az útmutatóból megtudhatja, hogyan állíthatja be az eszközöket, hogyan hozhat létre hangsegédet és a szükséges [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) -erőforrásokat, hogyan tesztelheti a hangsegédet, hogyan konfigurálhatja a kulcsszót, és hogyan hozhat létre egyéni kulcsszavakat.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- Azure Percept hang
- Beszélő vagy fejhallgató (nem kötelező)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub

## <a name="device-setup"></a>Eszköz beállítása

1. (Opcionálisan) csatlakoztasson hangszórót vagy fejhallgatót a hanghoz a fejhallgató-összekötőn keresztül, amely "line out" címkével van ellátva. Ez lehetővé teszi, hogy meghallgassa a hangsegéd hangválaszait. Ha nem csatlakozik hangszóróhoz vagy fejhallgatóhoz, akkor továbbra is megtekintheti a válaszokat szövegként a bemutató ablakban.

1. Csatlakoztassa a hang SoM-t a fejlesztői készlet Carrier táblához a mellékelt USB-A-ről a Micro B-kábellel.

1. Kapcsolja be a fejlesztői készlet.

    - Az audio SoM LED-L01 a tömör zöldre vált, jelezve, hogy az eszköz be van kapcsolva.
    - A LED L02 a villogó zöldre változik, jelezve, hogy a hang SoM hitelesítve van.

1. Várjon, amíg a hitelesítési folyamat befejeződik – ez akár 3 percet is igénybe vehet.

1. Folytassa a következő szakasszal, ha a következők egyikét látja:

    - A LED L01 kikapcsol, és a L02 fehérre vált. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet még nem lett konfigurálva kulcsszóval.
    - Mindhárom LED kékre vált. Ez azt jelzi, hogy a hitelesítés befejeződött, és a fejlesztői készlet egy kulcsszóval van konfigurálva.

    > [!NOTE]
    > Ha a fejlesztői készlet nem végzi el a hitelesítést, forduljon az ügyfélszolgálathoz.

## <a name="create-a-voice-assistant-using-an-available-template"></a>Hangsegéd létrehozása elérhető sablon használatával

1. Navigáljon az [Azure Percept studióhoz](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Nyissa meg a **bemutatók & oktatóanyagok** lapot.

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Képernyőkép Azure Portal kezdőlapról.":::

1. A **beszédfelismerési oktatóanyagok és bemutatók** szakaszban kattintson a **hangsegéd-sablonok kipróbálása** elemre. Ekkor megnyílik egy ablak a képernyő jobb oldalán.

1. A következő ablakban tegye a következőket:

    1. A **IoT hub** legördülő menüben válassza ki azt az IoT hubot, amelyhez a fejlesztői készlet csatlakoztatva van.

    1. Az **eszköz** legördülő menüben válassza ki a fejlesztői készlet.

    1. Válassza ki az elérhető hangsegéd-sablonok egyikét.

    1. Kattintson az Elfogadom **& feltételek a projekthez** jelölőnégyzetre.

    1. Kattintson a **Létrehozás** lehetőségre.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Képernyőkép a hangsegéd-sablon létrehozásáról.":::

1. A **Létrehozás** gombra kattintva a portál egy másik ablakot nyit meg a beszédfelismerési téma erőforrásának létrehozásához. A következő ablakban tegye a következőket:

    1. Válassza ki az Azure-előfizetését az **előfizetés** mezőben.

    1. Válassza ki a kívánt erőforráscsoportot az **erőforráscsoport** legördülő menüből. Ha létre szeretne hozni egy új erőforráscsoportot, amelyet a hangsegédhez szeretne használni, kattintson a **Létrehozás** elemre a legördülő menüben, majd kövesse az utasításokat.

    1. Az **alkalmazás előtagja** mezőben adja meg a nevet. Ez lesz a projekt előtagja és az egyéni parancs neve.

    1. A **régió** területen válassza ki azt a régiót, amelybe erőforrásokat kíván telepíteni.

    1. A **Luis előrejelzési díjszabása** területen válassza a **standard** lehetőséget (az ingyenes szint nem támogatja a beszédfelismerési kérelmeket).

    1. Kattintson a **Létrehozás** gombra. A hangsegéd alkalmazáshoz tartozó erőforrások üzembe helyezése az előfizetésben történik.

        > [!WARNING]
        > Ne **zárjuk be** az ablakot, amíg a portál befejezte az erőforrás telepítését. Az ablak idő előtti bezárása a hangsegéd váratlan viselkedését eredményezheti. Miután telepítette az erőforrást, megjelenik a bemutató.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Képernyőkép az előfizetés és az erőforráscsoport kiválasztási ablakáról.":::

## <a name="test-out-your-voice-assistant"></a>A hangsegéd kipróbálása

A hangsegédtel való kommunikációhoz tegyük fel, hogy a kulcsszót egy parancs követi. Amikor a fül SoM felismeri a kulcsszót, az eszköz egy harangjátékot bocsát ki (amely hallható, ha egy beszélő vagy egy fejhallgató csatlakoztatva van), a LED-ek pedig kéken villognak. A LED-ek a kék versenyzésre válthatnak, miközben a parancs feldolgozása megtörténik. A hangsegéd válasza a parancsra a bemutató ablakában lesz kinyomtatva, és hallható hangon a hangszórókon/fejhallgatón keresztül. Az alapértelmezett kulcsszó (az **egyéni kulcsszó** mellett) a "számítógép" értékre van állítva, és mindegyik sablon kompatibilis parancsokkal rendelkezik, amelyek lehetővé teszik a bemutató ablakban lévő virtuális objektumok használatát. Ha például a vendéglátás vagy az egészségügyi bemutató szolgáltatást használja, a virtuális TV bekapcsolásához a "számítógép, a TV bekapcsolása" lehetőséget kell bekapcsolni.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Képernyőfelvétel a Hospitality bemutató ablakáról.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Vendéglátási és egészségügyi bemutató parancsok

Az egészségügyi és a vendéglátási bemutatók egyaránt használhatnak virtuális televíziókat, fényjelzőket, vakokat és termosztátokat. A következő parancsok (és további változatok) támogatottak:

* "A fények be-és kikapcsolása"
* "A televízió be-és kikapcsolása"
* "Az AC kikapcsolása/kikapcsolása"
* "A vakok megnyitása/lezárása".
* "A hőmérséklet beállítása X fok értékre" (X a kívánt hőmérséklet, például 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Képernyőfelvétel az egészségügyi bemutató ablakról.":::

### <a name="automotive-demo-commands"></a>Autóipari bemutató parancsai

Az autóipari bemutatóban a virtuális munkaállomás, a páramentesítő és a termosztát használható. A következő parancsok (és további változatok) támogatottak:

* "A leolvasztás be-és kikapcsolása"
* "Az ülés bekapcsolása/kikapcsolása."
* "A hőmérséklet beállítása X fok értékre" (X a kívánt hőmérséklet, például 75.)
* "A hőmérséklet növelése/csökkentése Y fokkal."

:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Képernyőkép az autóipari bemutató ablakról.":::

### <a name="inventory-demo-commands"></a>Leltározási bemutató parancsai

A leltár-bemutatóban a virtuális leltári alkalmazással együtt a virtuális kék, a sárga és a zöld mezők közül választhat. A következő parancsok (és további változatok) támogatottak:

* "X Box hozzáadása/eltávolítása" (X a dobozok száma, például 4.)
* "X mezők megrendelése/szállítása"
* "Hány mező van a készletben?"
* "Y Count mezők száma" (Y a dobozok színe, például sárga.)
* "Az összes raktárban lévő hajó".

:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Képernyőfelvétel a leltár-bemutató ablakról.":::

## <a name="configure-your-keyword"></a>A kulcsszó konfigurálása

A kulcsszó módosításához kattintson az **egyéni kulcsszó** melletti **módosítás** elemre a bemutató ablakban. Válasszon ki egy elérhető kulcsszót, majd kattintson a **Mentés** gombra. Az előre elkészített kulcsszavakat és a létrehozott egyéni kulcsszavakat is kiválaszthatja.

:::image type="content" source="./media/tutorial-no-code-speech/change-keyword.png" alt-text="Képernyőkép az elérhető kulcsszavak kiválasztásáról.":::

### <a name="create-a-custom-keyword"></a>Egyéni kulcsszó létrehozása

Egyéni kulcsszó létrehozásához kattintson az **+ egyéni kulcsszó létrehozása** elemre a bemutató ablak felső részén. Adja meg a kívánt kulcsszót, amely lehet egy szó vagy egy rövid kifejezés, válassza ki a **beszédfelismerési erőforrást** (ez a bemutató ablakban az **Egyéni parancs** mellett szerepel, és tartalmazza az alkalmazás előtagja), majd kattintson a **Save (Mentés**) gombra. Az egyéni kulcsszó betanítása néhány másodperc alatt elvégezhető.

:::image type="content" source="./media/tutorial-no-code-speech/custom-keyword.png" alt-text="Képernyőkép az egyéni kulcsszó-létrehozási ablakról.":::

## <a name="create-a-custom-command"></a>Egyéni parancs létrehozása

A portál olyan funkciókat is biztosít, amelyekkel egyéni parancsok hozhatók létre meglévő beszédfelismerési erőforrásokkal. Az "Egyéni parancs" magára a hangsegéd-alkalmazásra hivatkozik, nem pedig a meglévő alkalmazáson belüli adott parancsra. Egyéni parancs létrehozásával új Speech-projektet hoz létre, amelyet a [Speech Studióban](https://speech.microsoft.com/)tovább kell fejleszteni.

Ha új egyéni parancsot szeretne létrehozni a bemutató ablakban, kattintson az **+ Egyéni parancs létrehozása** lehetőségre a lap tetején, és tegye a következőket:

1. Adja meg az egyéni parancs nevét.

1. Adja meg a projekt leírását (nem kötelező).

1. Válassza ki a kívánt nyelvet.

1. Válassza ki a beszédfelismerési erőforrást.

1. Válassza ki a LUIS-erőforrást.

1. Válassza ki a LUIS authoring-erőforrást, vagy hozzon létre újat.

1. Kattintson a **Létrehozás** lehetőségre.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Képernyőkép az egyéni parancsok létrehozási ablakáról.":::

Miután létrehozta az egyéni parancsot, további fejlesztésért lépjen a [Speech studióba](https://speech.microsoft.com/) . Ha megnyitja a Speech studiót, és nem látja a felsorolt egyéni parancsot, kövesse az alábbi lépéseket:

1. Az Azure Percept Studio bal oldali menü paneljén kattintson az **AI-projektek** alatt található **beszéd** elemre.

1. Válassza a **parancsok** lapot.

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Képernyőkép a szerkesztésre elérhető egyéni parancsok listájáról.":::

1. Válassza ki a fejleszteni kívánt egyéni parancsot. Ekkor megnyílik a projekt a Speech Studióban.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Képernyőkép a Speech Studio kezdőképernyő képernyőjéről.":::

Az egyéni parancsok fejlesztésével kapcsolatos további információkért tekintse meg a [Speech Service dokumentációját](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>A hangsegéd létrejött, de nem válaszol a parancsokra

Győződjön meg arról, hogy az audio SoM LED-jelzőfényei:

* Három stabil kék fény jelzi, hogy a hangsegéd készen áll, és a kulcsszóra vár.
* Ha a középső LED (L02) fehér, a fejlesztői készlet befejezte az inicializálást, és egy kulcsszóval kell konfigurálni.
* A zöld fények bármely kombinációja azt jelzi, hogy a hang SoM még nem végezte el az inicializálást. Az inicializálás több percet is igénybe vehet.

A hang SoM LED-indikátorokkal kapcsolatos további információkért tekintse meg a LED-cikket.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>A hangsegéd nem válaszol a Speech Studióban létrehozott egyéni kulcsszóra

Ez akkor fordulhat elő, ha a beszédfelismerési modul elavult. Kövesse az alábbi lépéseket a beszédfelismerési modul legújabb verzióra való frissítéséhez:

1. Kattintson az **eszközök** elemre az Azure Percept Studio kezdőlapjának bal oldali menü paneljén.

1. Keresse meg és válassza ki az eszközt.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Képernyőkép az eszközök listájáról az Azure Percept Studióban.":::

1. Az eszköz ablakban válassza a **Speech (beszédfelismerés** ) lapot.

1. Keresse meg a beszédfelismerő modul verzióját. Ha egy frissítés elérhető, megjelenik egy **frissítés** gomb a verziószám mellett.

    :::image type="content" source="./media/tutorial-no-code-speech/devkit.png" alt-text="Képernyőkép az fejlesztői készlet-Beszédfelismerési beállítások ablakról.":::

1. Kattintson a **frissítés** elemre a beszédfelismerési modul frissítésének telepítéséhez. A frissítési folyamat általában 2-3 percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a hangsegéd-alkalmazással, kövesse az alábbi lépéseket az oktatóanyag során üzembe helyezett beszédfelismerési erőforrások tisztításához:

1. A [Azure Portal](https://ms.portal.azure.com/#home)válassza ki az **erőforráscsoportok** elemet a bal oldali menüből, vagy írja be a keresősávba.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Képernyőkép a Azure Portal kezdőlapról a bal oldali menü és az erőforráscsoportok panelről.":::

1. Válassza ki az erőforráscsoportot.

1. Válassza ki mind a hat erőforrást, amely tartalmazza az alkalmazás előtagját, és kattintson a felső menüsorban található **Törlés** ikonra.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="A törlésre kijelölt beszédfelismerési erőforrások képernyőképe.":::

1. A törlés megerősítéséhez írja be az **Igen** értéket a megerősítés mezőbe, ellenőrizze, hogy a megfelelő erőforrásokat választotta-e ki, majd kattintson a **Törlés** gombra.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Képernyőkép a Törlés megerősítése ablakról.":::

> [!WARNING]
> Ezzel eltávolítja a törölni kívánt beszédfelismerési erőforrásokkal létrehozott egyéni kulcsszavakat, és a hangsegéd bemutatója már nem fog működni.

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy kód nélküli beszédfelismerési megoldást, hozzon létre egy [kód nélküli megoldást](./tutorial-nocode-vision.md) az Azure Percept DK-hez.
