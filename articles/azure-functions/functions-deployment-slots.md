---
title: Azure Functions üzembe helyezési pontok
description: Ismerje meg, hogyan hozhat létre és használhat üzembe helyezési tárolóhelyeket Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172409"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions üzembe helyezési pontok

Azure Functions üzembe helyezési pontok lehetővé teszik, hogy a Function alkalmazás különböző példányokat futtasson "bővítőhelyek" néven. A tárolóhelyek egy nyilvánosan elérhető végponton keresztül kitett különböző környezetek. Az egyik alkalmazás példánya mindig az üzemi tárolóhelyre van leképezve, és igény szerint egy tárolóhelyhez rendelt példányok is felcserélhetők. Az Apps Service-csomag alatt futó Function apps több tárolóhelytel rendelkezhet, míg a használati terv csak egy tárolóhelyet engedélyez.

A következő, a függvények a tárolóhelyek cseréjével kapcsolatos hatásait mutatja be:

- A forgalom átirányítása zökkenőmentes; a swap miatt a rendszer nem távolítja el a kérelmeket.
- Ha egy függvény egy csere közben fut, a végrehajtás folytatódik, és a következő eseményindítók átirányítva lesznek a felcserélt alkalmazás-példányra.

> [!NOTE]
> A Linux-használati csomag jelenleg nem érhető el.

## <a name="why-use-slots"></a>Miért érdemes tárolóhelyeket használni?

Számos előnnyel jár az üzembe helyezési pontok használata. A következő forgatókönyvek a bővítőhelyek gyakori felhasználási módjait ismertetik:

- Különböző **környezetek különböző célokra**: a különböző tárolóhelyek használata lehetővé teszi az alkalmazás-példányok megkülönböztetését, mielőtt az éles környezetbe vagy egy átmeneti tárolóhelyre cseréli őket.
- **Előmelegítés**: a közvetlenül az éles környezetbe való üzembe helyezése lehetővé teszi az alkalmazás számára, hogy az élő működés előtt belépjen. Emellett a tárolóhelyek használata csökkenti a HTTP-triggert használó munkaterhelések késését. A példányok az üzembe helyezés előtt melegszik fel, ami csökkenti az újonnan üzembe helyezett függvények hideg indítását.
- **Egyszerű tartalékok**: az éles használat után a korábban előkészített alkalmazáshoz tartozó tárolóhely már az előző éles alkalmazásban van. Ha az éles tárolóhelyre való váltás nem a várt módon történik, akkor a swap azonnal visszafordítható az "utolsó ismert jó példány" visszaszerzéséhez.

## <a name="swap-operations"></a>Swap-műveletek

A csere során az egyik tárolóhely a forrás és a másik cél. A forrás tárolóhely az alkalmazás azon példányát alkalmazza, amely a cél tárolóhelyre van alkalmazva. A következő lépések gondoskodnak arról, hogy a cél tárolóhely ne tapasztaljon állásidőt a swap során:

1. **Beállítások alkalmazása:** A cél tárolóhelyről származó beállításokat a rendszer a forrás tárolóhely összes példányára alkalmazza. Az éles környezet beállításait például az átmeneti példányra alkalmazza a rendszer. Az alkalmazott beállítások a következő kategóriákat tartalmazzák:
    - A [tárolóhely-specifikus](#manage-settings) Alkalmazásbeállítások és a kapcsolatok karakterláncai (ha vannak ilyenek)
    - [Folyamatos üzembe helyezési](../app-service/deploy-continuous-deployment.md) beállítások (ha engedélyezve vannak)
    - [App Service hitelesítési](../app-service/overview-authentication-authorization.md) beállítások (ha engedélyezve vannak)

1. **Várakozás az újraindításra és a rendelkezésre állásra:** A swap megvárja a forrás tárolóhely minden példányát, hogy elvégezze az újraindítást, és hogy elérhető legyen a kérésekhez. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrás tárolóhelyének összes módosítását, és leállítja a műveletet.

1. **Útválasztás frissítése:** Ha a forrás tárolóhelyén lévő összes példány sikeresen befejeződik, a két bővítőhely az útválasztási szabályok váltásával hajtja végre a cserét. Ezt a lépést követően a cél tárolóhely (például az éles tárolóhely) azt az alkalmazást alkalmazza, amely korábban a forrás tárolóhelyen van bemelegítve.

1. **Ismétlési művelet:** Most, hogy a forrás tárolóhelye korábban a cél tárolóhelyen lévő előzetes swap alkalmazással rendelkezik, végezze el ugyanezt a műveletet úgy, hogy az összes beállítást alkalmazza, majd újraindítja a példányokat a forrás tárolóhelyén.

Tartsa szem előtt az alábbi szempontokat:

- A swap-művelet bármely pontján a felcserélt alkalmazások inicializálása a forrás tárolóhelyen történik. A cél tárolóhely online marad, amíg a forrás tárolóhelye fel van készítve, hogy a swap sikeres vagy sikertelen volt-e.

- Ha egy átmeneti tárolóhelyet szeretne cserélni az üzemi tárolóhelyre, győződjön meg arról, hogy az éles tárolóhely *mindig* a cél tárolóhely. Így a swap művelet nem befolyásolja az üzemi alkalmazást.

- Az esemény-forrásokhoz és kötésekhez kapcsolódó beállításokat a *swap elindítása előtt* [üzembe helyezési pont beállításainak](#manage-settings) megfelelően kell konfigurálni. Ha az idő előtt "Sticky"-ként jelöli meg őket, az események és a kimenetek a megfelelő példányra vannak irányítva.

## <a name="manage-settings"></a>Beállítások kezelése

Egyes konfigurációs beállítások tárolóhely-specifikusak. A következő felsorolás részletesen ismerteti, hogy mely beállítások változnak a tárolóhelyek cseréjekor, és amelyek változatlanok maradnak.

**Tárolóhelyekre vonatkozó beállítások**:

* Közzétételi végpontok
* Egyéni tartománynevek
* Nem nyilvános tanúsítványok és TLS/SSL-beállítások
* Méretezési beállítások
* Webjobs-ütemező
* IP-korlátozások
* Always on
* Diagnosztikai beállítások
* Eltérő eredetű erőforrások megosztása (CORS)

**Nem tárolóhelyekre vonatkozó beállítások**:

* Általános beállítások, mint például a keretrendszer verziója, 32/64 bites, webes szoftvercsatornák
* Alkalmazásbeállítások (beállítható egy tárolóhelyre való ragasztásra)
* Kapcsolódási karakterláncok (beállítható egy tárolóhelyre való ragasztásra)
* Kezelő-hozzárendelések
* Nyilvános tanúsítványok
* Webjobs-tartalom
* Hibrid kapcsolatok *
* Virtuális hálózati integráció *
* Szolgáltatási végpontok *
* Azure Content Delivery Network *

A csillaggal (*) jelölt funkciók nem lesznek lecserélve. 

> [!NOTE]
> A nem cserélt beállításokra vonatkozó bizonyos Alkalmazásbeállítások nem lesznek felcserélve. Mivel például a diagnosztikai beállítások nincsenek felcserélve, a kapcsolódó Alkalmazásbeállítások, például `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a és a nem lesznek `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` lecserélve, még akkor is, ha nem jelennek meg tárolóhely-beállításokként.
>

### <a name="create-a-deployment-setting"></a>Központi telepítési beállítás létrehozása

Megadhatja a beállításokat központi telepítési beállításként, ami "Sticky" lesz. A Sticky beállítás nem cseréli le az alkalmazás példányát.

Ha egy központi telepítési beállítást hoz létre egy tárolóhelyen, mindenképpen hozzon létre egy egyedi értéket a swap-ben részt vevő többi tárolóhelyen. Így a beállítások értéke nem változik, a beállítások nevei konzisztensek maradnak a tárolóhelyek között. Ez a név konzisztencia biztosítja, hogy a kód ne próbáljon meg hozzáférni egy olyan beállításhoz, amely egy tárolóhelyen van definiálva, de még nem.

A központi telepítési beállítás létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon az **üzembe helyezési** pontokhoz a Function alkalmazásban, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="A Azure Portal található tárolóhelyek." border="true":::

1. Válassza a **konfiguráció** lehetőséget, majd válassza ki azt a nevet, amelyet az aktuális tárolóhelyhez szeretne ragasztani.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Konfigurálja a Azure Portal egy tárolóhelyének alkalmazási beállításait." border="true":::

1. Válassza a **telepítési tárolóhely beállítása** lehetőséget, majd kattintson **az OK gombra**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Konfigurálja az üzembe helyezési tárolóhely beállítását." border="true":::

1. Ha a beállítás szakasz eltűnik, a módosítások megtartásához válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Mentse az üzembe helyezési tárolóhely beállítását." border="true":::

## <a name="deployment"></a>Üzembe helyezés

Tárolóhelyek létrehozásakor a tárolóhelyek üresek. A [támogatott üzembe helyezési technológiák](./functions-deployment-technologies.md) bármelyikével üzembe helyezheti az alkalmazást egy tárolóhelyen.

## <a name="scaling"></a>Méretezés

Az összes tárolóhely az üzemi tárolóhelytel azonos számú feldolgozóra méretezhető.

- A használati csomagok esetében a tárolóhely a függvény alkalmazási skálájának megfelelően méretezhető.
- App Service csomagok esetében az alkalmazás egy rögzített számú feldolgozóra méretezhető. A bővítőhelyek ugyanazon a számú feldolgozón futnak, mint az alkalmazási csomag.

## <a name="add-a-slot"></a>Pont felvétele

Hozzáadhat egy tárolóhelyet a [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) -n keresztül, vagy a portálon keresztül is. A következő lépések bemutatják, hogyan hozhat létre egy új tárolóhelyet a portálon:

1. Navigáljon a Function alkalmazáshoz.

1. Válassza az **üzembe helyezési** pontok lehetőséget, majd válassza a **+ tárolóhely hozzáadása** elemet.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Azure Functions üzembe helyezési pont hozzáadása." border="true":::

1. Írja be a tárolóhely nevét, majd válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nevezze el a Azure Functions üzembe helyezési tárolóhelyet." border="true":::

## <a name="swap-slots"></a>Tárolóhelyek cseréje

A tárolóhelyeket a [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) -n keresztül vagy a portálon keresztül lehet cserélni. A következő lépések bemutatják, hogyan lehet felcserélni a tárolóhelyeket a portálon:

1. Navigáljon a Function alkalmazáshoz.
1. Válassza az **üzembe helyezési** pontok lehetőséget, majd kattintson a **Csere** elemre.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Képernyőkép, amely az &quot;üzembe helyezési pont&quot; lapot jeleníti meg a &quot;tárolóhely hozzáadása&quot; művelet kiválasztásával." border="true":::

1. Ellenőrizze a swap konfigurációs beállításait, és válassza a **Csere** lehetőséget.
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Cserélje le az üzembe helyezési tárolóhelyet." border="true":::

A művelet eltarthat egy kis ideig, amíg a rendszer végrehajtja a swap-műveletet.

## <a name="roll-back-a-swap"></a>Swap visszaállítása

Ha a swap hibát jelez, vagy egyszerűen vissza kívánja állítani a cserét, visszaállíthatja a kezdeti állapotot. Ha vissza szeretne térni az előre lecserélt állapotba, hajtson végre egy újabb cserét a swap megfordításához.

## <a name="remove-a-slot"></a>Tárolóhely eltávolítása

Egy tárolóhelyet a [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) -n keresztül vagy a portálon lehet eltávolítani. A következő lépések bemutatják, hogyan távolíthat el egy tárolóhelyet a portálon:

1. Navigáljon az **üzembe helyezési** pontokhoz a Function alkalmazásban, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="A Azure Portal található tárolóhelyek." border="true":::

1. Válassza a **Törlés** elemet.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Az &quot;áttekintés&quot; oldalt megjelenítő képernyőkép, amelyen a &quot;Delete&quot; művelet van kiválasztva." border="true":::

1. Írja be a törölni kívánt telepítési tárolóhely nevét, majd válassza a **Törlés** lehetőséget.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Törölje a telepítési tárolóhelyet a Azure Portal." border="true":::

1. A Törlés megerősítése panel bezárásához.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Üzembe helyezési pont törlésének megerősítése." border="true":::

## <a name="automate-slot-management"></a>A tárolóhelyek kezelésének automatizálása

Az [Azure CLI](/cli/azure/functionapp/deployment/slot)használatával automatizálhatja a következő műveleteket egy tárolóhelyen:

- [létrehozása](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [listáját](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [swap](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [automatikus felcserélés](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service terv módosítása

Egy App Service csomag alatt futó Function alkalmazással módosíthatja a tárolóhely mögöttes App Service tervét.

> [!NOTE]
> A tárolóhelyek App Service terve nem módosítható a használati tervben.

A tárolóhelyek App Service tervének módosításához kövesse az alábbi lépéseket:

1. Navigáljon az **üzembe helyezési** pontokhoz a Function alkalmazásban, majd válassza ki a tárolóhely nevét.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="A Azure Portal található tárolóhelyek." border="true":::

1. A **app Servicei csomag** területen válassza a **app Service terv módosítása** lehetőséget.

1. Válassza ki azt a csomagot, amelyre frissíteni kíván, vagy hozzon létre egy új csomagot.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Módosítsa a Azure Portal App Service tervét." border="true":::

1. Válassza az **OK** lehetőséget.

## <a name="limitations"></a>Korlátozások

Azure Functions üzembe helyezési pontok a következő korlátozásokkal rendelkeznek:

- Az alkalmazás számára elérhető tárolóhelyek száma a csomagtól függ. A használati terv csak egy üzembe helyezési pont számára engedélyezett. További tárolóhelyek érhetők el a App Service csomag alatt futó alkalmazásokhoz.
- A tárolóhelyek cseréje visszaállítja a kulcsokat olyan alkalmazások számára, amelyeknek az `AzureWebJobsSecretStorageType` alkalmazás beállításai megegyeznek `files` .
- A Linux-használati tervhez nem érhetők el tárolóhelyek.

## <a name="support-levels"></a>Támogatási szintek

Az üzembe helyezési pontok két szinten támogatottak:

- **Általánosan elérhető (GA)**: teljes mértékben támogatott és jóváhagyott éles használatra.
- **Előzetes** verzió: még nem támogatott, de a jövőben várhatóan el kell érnie a ga-állapotot.

| Operációs rendszer/üzemeltetési csomag           | Támogatási szint     |
| ------------------------- | -------------------- |
| Windows-felhasználás       | Általános elérhetőség |
| Windows Premium           | Általános elérhetőség  |
| Dedikált Windows         | Általános elérhetőség |
| Linux-felhasználás         | Nem támogatott          |
| Linux Premium             | Általános elérhetőség  |
| Linux dedikált           | Általános elérhetőség |

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezési technológiák Azure Functions](./functions-deployment-technologies.md)
