---
title: Azure Functions üzembe helyezési tárolóhelyek
description: Megtudhatja, hogyan hozhat létre és használhat üzembehelyhelyeket a Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 5f003b68283217f7877dc650ae4f07ddc5a31012
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789338"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions üzembe helyezési tárolóhelyek

Azure Functions üzembehely helyezési tárolóhelyek lehetővé teszik, hogy a függvényalkalmazás különböző példányokat futtatassa "slots" néven. A tárolóhelyek különböző környezetek, amelyek nyilvánosan elérhető végponton keresztül érhetők el. Az egyik alkalmazáspéldány mindig az éles tárolóhelyre van leképezve, és igény szerint felcserélheti az adott tárolóhelyhez rendelt példányokat. Az Apps Service-csomag alatt futó függvényalkalmazások több pontból is lehetnek, míg a Használat alapján csak egy pont engedélyezett.

Az alábbiak azt tükrözik, hogy a függvényeket hogyan befolyásolja a felcserélési tárolóhelyek:

- A forgalom átirányítása zökkenőmentes; felcserélés miatt a kérések nem dobottak el.
- Ha egy függvény a felcserélés során fut, a végrehajtás folytatódik, és a következő eseményindítók a felcserélett alkalmazáspéldányhoz vannak irányítva.

## <a name="why-use-slots"></a>Miért érdemes pontokat használni?

Az üzembe helyezési tárolóhelyek használata számos előnnyel jár. Az alábbi forgatókönyvek a tárolóhelyek gyakori használati esetét írják le:

- **Különböző környezetek különböző célokra:** A különböző pontokkal megkülönböztethetők az alkalmazáspéldányok, mielőtt éles vagy előkészítési pontra váltanának.
- **Előzés:** Az üzembe helyezés közvetlenül éles környezet helyett egy üzembehelyezéssel az alkalmazás bemelegíthető az éles üzembe helyezés előtt. Emellett a tárolóhelyek használata csökkenti a HTTP által aktivált számítási feladatok késését. A példányok bemelegítve vannak az üzembe helyezés előtt, ami csökkenti az újonnan üzembe helyezett függvények hideg kezdési időét.
- **Egyszerű tartalékok:** Az éles környezetben való felcserélés után a korábban létrehozott alkalmazással felcserélhető ponton már az előző éles alkalmazás is található. Ha az éles tárolóhelyre felcserélt módosítások nem a vártnak megfelelőek, azonnal megfordíthatja a felcserélést, hogy az "utolsó ismert jó példány" visszajöjjön.

## <a name="swap-operations"></a>Felcserélési műveletek

A felcserélés során az egyik tárolóhely a forrásnak számít, a másik pedig a célnak. A forráshelyen található az alkalmazásnak a célhelyre alkalmazott példánya. A következő lépések biztosítják, hogy a célhely ne tapasztaljon állásidőt a felcserélés során:

1. **Beállítások alkalmazása:** A célhely beállításai a forráshely összes példányára érvényesek. Az éles beállítások például az átmeneti példányra vannak alkalmazva. Az alkalmazott beállítások a következő kategóriákat tartalmazzák:
    - [Pontspecifikus alkalmazásbeállítások](#manage-settings) és kapcsolati sztringek (ha vannak)
    - [Folyamatos üzembe helyezési](../app-service/deploy-continuous-deployment.md) beállítások (ha engedélyezve van)
    - [App Service beállításainak](../app-service/overview-authentication-authorization.md) megadása (ha engedélyezve van)

1. **Várjon az újraindításra és a rendelkezésre állásra:** A felcserélés megvárja, amíg a forráshelyen található összes példány újraindul, és elérhetővé válik a kérések számára. Ha valamelyik példány nem indul újra, a felcserélési művelet visszaállítja a forráshely módosításait, és leállítja a műveletet.

1. **Útválasztás frissítése:** Ha a forráshelyen található összes példány be van melegítve, a két tárolóhely az útválasztási szabályok váltása után befejezi a felcserélést. A lépés után a célként használt pont (például az éles pont) rendelkezik a korábban a forráshelyen bemelegített alkalmazással.

1. **Ismétlési művelet:** Most, hogy a forráshelyen korábban a felcserélés előtti alkalmazás volt a célhelyen, ugyanazt a műveletet az összes beállítás alkalmazásával és a forráshely példányainak újraindításával kell végrehajtani.

Tartsa szem előtt az alábbi szempontokat:

- A felcserélési művelet bármely pontján a felcserélett alkalmazások inicializálása a forrásponton történik. A célhely online állapotban marad, amíg a forráshely elő van készítve, függetlenül attól, hogy a csere sikeres vagy sikertelen.

- Ha fel kell cserélnie egy előkészítési helyet az éles pontra, győződjön meg arról, hogy az éles pont *mindig a* célhely. Így a felcserélési művelet nincs hatással az éles alkalmazásra.

- A felcserélés előtt az eseményforrásokkal [](#manage-settings) és kötésekkel kapcsolatos beállításokat üzembe helyezésihely-beállításként *kell konfigurálni.* Ha előre "ragadósként" megjelöli őket, az események és kimenetek a megfelelő példányhoz vannak irányítva.

## <a name="manage-settings"></a>Beállítások kezelése

Egyes konfigurációs beállítások helyspecifikusak. Az alábbi lista részletesen ismerteti, hogy mely beállítások változnak a felcseréléskor, és melyek maradnak változatlanok.

**Tárolóhely-specifikus beállítások:**

* Végpontok közzététele
* Egyéni tartománynevek
* Nem nyilvános tanúsítványok és TLS/SSL-beállítások
* Méretezési beállítások
* WebJobs-ütemezők
* IP-korlátozások
* Always On
* Diagnosztikai beállítások
* Eltérő eredetű erőforrások megosztása (CORS)

**Nem tárolóhely-specifikus beállítások:**

* Általános beállítások, például keretrendszer-verzió, 32/64 bites, webes szoftvercsatornák
* Alkalmazásbeállítások (konfigurálható úgy, hogy egy ponthoz tartsanak)
* Kapcsolati sztringek (konfigurálható úgy, hogy egy ponthoz tartsanak)
* Kezelőleképezések
* Nyilvános tanúsítványok
* WebJobs-tartalom
* Hibrid kapcsolatok *
* Virtuális hálózat integrációja *
* Szolgáltatásvégpont *
* Azure Content Delivery Network *

A csillaggal (*) jelölt funkciókat a tervek szerint nem lehet használni. 

> [!NOTE]
> A nem le nem cserélt beállításokra vonatkozó egyes alkalmazásbeállítások szintén nem cserélhetők fel. Például mivel a diagnosztikai beállítások nincsenek felcserélve, az olyan kapcsolódó alkalmazásbeállítások, mint a és a sem, akkor sem, ha nem fognak `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` helybeállításként adatokat mutatni.
>

### <a name="create-a-deployment-setting"></a>Üzembe helyezési beállítás létrehozása

A beállításokat megjelölheti központi telepítési beállításként, így az "ragadós" lesz. A ragadós beállítások nem cserélnek az alkalmazáspéldányra.

Ha az egyik tárolóhelyen hoz létre üzembe helyezési beállítást, ügyeljen arra, hogy ugyanazt a beállítást egyedi értékkel hozza létre a felcserélésben részt vevő bármely másik tárolóhelyen. Így, bár a beállítás értéke nem változik, a beállításnevek konzisztensek maradnak a tárolóhelyek között. Ez a névkonzisztencia biztosítja, hogy a kód ne az egyik tárolóhelyen definiált beállításhoz próbáljon hozzáférni, csak egy másikhoz.

Központi telepítési beállítás létrehozásához kövesse az alábbi lépéseket:

1. A **függvényalkalmazásban** lépjen az Üzembe helyezési tárolóhelyekre, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="A tárolóhelyeket a Azure Portal." border="true":::

1. Válassza **a Konfiguráció** lehetőséget, majd válassza ki azt a beállításnevet, amely az aktuális helyhez kíván maradni.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Konfigurálja az alkalmazásbeállítást a Azure Portal." border="true":::

1. Válassza **az Üzembe helyezésihely-beállítás lehetőséget,** majd kattintson az **OK gombra.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Konfigurálja az üzembe helyezési hely beállítását." border="true":::

1. Miután a beállítási szakasz eltűnik, a **Mentés lehetőséget választva** megtarthatja a módosításokat

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Mentse az üzembe helyezési hely beállítását." border="true":::

## <a name="deployment"></a>Üzembe helyezés

A tárolóhelyek üresek a tárolóhelyek létrehozásakor. A támogatott üzembe helyezési technológiák [bármelyikét használhatja](./functions-deployment-technologies.md) az alkalmazás üzembe helyezéséhez egy üzembehelyen.

## <a name="scaling"></a>Méretezés

Minden tárolóhely az éles tárolóhelypel azonos számú munkamunkásra van skálázva.

- A használat alapján csomagoknál a tárolóhely a függvényalkalmazás skálázása szerint skáláz.
- A App Service esetében az alkalmazás rögzített számú dolgozóra skálázható. A tárolóhelyek az alkalmazástervével azonos számú munkagépen futnak.

## <a name="add-a-slot"></a>Pont felvétele

A tárolóhelyeket a [CLI-n](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) vagy a portálon keresztül is hozzáadhatja. A következő lépések bemutatják, hogyan hozhat létre új tárolóhelyet a portálon:

1. Lépjen a függvényalkalmazáshoz.

1. Válassza **az Üzembe helyezési tárolóhelyek** lehetőséget, majd válassza **a + Tárolóhely hozzáadása lehetőséget.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Adja Azure Functions üzembe helyezési helyet." border="true":::

1. Írja be a tárolóhely nevét, és válassza a **Hozzáadás lehetőséget.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nevezze el az Azure Functions üzembe helyezési helyet." border="true":::

## <a name="swap-slots"></a>Felcserélési tárolóhelyek

A tárolóhelyeket felcserélheti a [CLI-n keresztül](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) vagy a portálon keresztül. A következő lépések bemutatják, hogyan cserélhetőek fel a tárolóhelyek a portálon:

1. Keresse meg a függvényalkalmazást.
1. Válassza **az Üzembe helyezési tárolóhelyek** lehetőséget, majd a **Felcserélés lehetőséget.**

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Képernyőkép az &quot;Üzembe helyezési hely&quot; lapról, a kiválasztott &quot;Üzembehely helyezési hely hozzáadása&quot; műveletsel." border="true":::

1. Ellenőrizze a felcserélés konfigurációs beállításait, majd válassza a **Felcserélés lehetőséget**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Cserélje le az üzembe helyezési helyet." border="true":::

A felcserélési művelet végrehajtása egy kis ideig is eltelhet.

## <a name="roll-back-a-swap"></a>Felcserélés visszaváltása

Ha a felcserélés hibát jelez, vagy egyszerűen csak "visszavonni" szeretné a felcserélést, akkor vissza lehet tért a kezdeti állapotba. Az előre felcserélett állapotba úgy térhet vissza, hogy megfordítja a felcserélést.

## <a name="remove-a-slot"></a>Tárolóhely eltávolítása

A tárolóhelyeket a [CLI-n](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) vagy a portálon keresztül távolíthatja el. A következő lépések bemutatják, hogyan távolíthat el egy tárolóhelyet a portálon:

1. A **függvényalkalmazásban** lépjen az Üzembe helyezési tárolóhelyekre, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="A tárolóhelyeket a Azure Portal." border="true":::

1. Válassza a **Törlés** elemet.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Képernyőkép az Áttekintés lapról, a kiválasztott &quot;Törlés&quot; műveletről." border="true":::

1. Írja be a törölni kívánt üzembe helyezési hely nevét, majd válassza a **Törlés lehetőséget.**

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Törölje az üzembe helyezési helyet a Azure Portal." border="true":::

1. Zárja be a törlést megerősítő panelt.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Az üzembe helyezési hely törlésének megerősítése." border="true":::

## <a name="automate-slot-management"></a>Tárolóhely-kezelés automatizálása

Az [Azure CLI használatával](/cli/azure/functionapp/deployment/slot)a következő műveleteket automatizálhatja egy adott tárolóhelyen:

- [Létrehozása](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [Töröl](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [Lista](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [Swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [automatikus felcserélés](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>A App Service módosítása

Ha egy függvényalkalmazás egy App Service alatt fut, módosíthatja a pont App Service mögöttes csomagját.

> [!NOTE]
> A használat alapján nem módosíthatja a pont App Service csomagját.

A következő lépésekkel módosíthatja egy tárolóhely App Service tervét:

1. A **függvényalkalmazásban lépjen** az Üzembe helyezési tárolóhelyekre, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Tárolóhelyeket keres a Azure Portal." border="true":::

1. A **App Service alatt** válassza **a Csomag módosítása App Service lehetőséget.**

1. Válassza ki azt a tervet, amelyre frissíteni szeretne, vagy hozzon létre egy újat.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Módosítsa a App Service tervét a Azure Portal." border="true":::

1. Válassza az **OK** lehetőséget.

## <a name="limitations"></a>Korlátozások

Azure Functions üzembehelyzőkre a következő korlátozások vonatkoznak:

- Az alkalmazás számára elérhető tárolóhelyek száma a csomagtól függ. A használatban van csomag csak egy üzembe helyezési hely számára engedélyezett. A csomag keretében futó alkalmazásokhoz további pontokat App Service is.
- A felcserélés visszaállítja a kulcsokat az olyan alkalmazásokhoz, amelyek `AzureWebJobsSecretStorageType` alkalmazásbeállítása megegyezik `files` a értékekkel.
- A csomaghoz nem érhetők el Linux-használat tárolóhelyek.

## <a name="support-levels"></a>Támogatási szintek

Az üzembe helyezési tárolóhelyek két szinten támogatottak:

- **Általánosan elérhető (GA):** Teljes körűen támogatott és jóváhagyott az éles használathoz.
- **Előzetes** verzió: Még nem támogatott, de a jövőben várható az ga állapot elérése.

| Operációs rendszer/üzemeltetési csomag           | Támogatási szint     |
| ------------------------- | -------------------- |
| Windows-használat       | Általános elérhetőség |
| Windows Premium           | Általános elérhetőség  |
| Windows Dedicated         | Általános elérhetőség |
| Linux-használat         | Előnézet          |
| Linux Premium             | Általános elérhetőség  |
| Dedikált Linux           | Általános elérhetőség |

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezési technológiák a Azure Functions](./functions-deployment-technologies.md)
