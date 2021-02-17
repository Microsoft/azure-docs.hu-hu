---
title: Példány és hitelesítés beállítása (szkriptelt)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása automatikus telepítési parancsfájl futtatásával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 5ac41ecc068238cbb20c05b253dc071dfc4a8138
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560805"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure digitális Twins-példány és-hitelesítés beállítása (parancsfájlba foglalt)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának** lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A jelen cikk ezen verziója a lépéseket egy [ **automatizált üzembehelyezési parancsfájl**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) futtatásával hajtja végre, amely egyszerűsíti a folyamatot. 
* A parancsfájlnak a háttérben való futtatásához szükséges manuális parancssori lépések megtekintéséhez tekintse meg a jelen cikk CLI-verzióját: [*útmutató: példány és hitelesítés (CLI) beállítása*](how-to-set-up-instance-cli.md).
* A Azure Portal szerinti manuális lépések megtekintéséhez tekintse meg a jelen cikk portáljának verzióját: [*útmutató: példány és hitelesítés beállítása (portál)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Előfeltételek: töltse le a szkriptet

A minta parancsfájl a PowerShellben van megírva. Része az [**Azure digitális Twins végpontok közötti mintáknak**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), amelyeket letöltheti a gépre, ehhez lépjen a minta hivatkozásra, és válassza a cím alatt található *tallózási kód* gombot. Ekkor megnyílik a minták GitHub-tárháza, amelyet a-ként tölthet le *. ZIP* -t a *Code (kód* ) gomb kiválasztásával és a *zip letöltésével*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="A Digital-Twins-Samples tárház áttekintése a GitHubon. A kód gomb be van jelölve, és létrehoz egy kis párbeszédpanelt, ahol ki van emelve a ZIP-Letöltés gomb." lightbox="media/includes/download-repo-zip.png":::

Ezzel letölti a-t *. A ZIP* -mappát **digital-twins-samples-master.zipként**. Navigáljon a számítógép mappájához, és csomagolja ki a fájlokat a fájlok kibontásához.

A kibontott mappában az üzembe helyezési parancsfájl a következő helyen található: _Digital-Twins-Samples-master > scripts > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Az üzembe helyezési szkript futtatása

Ez a cikk egy Azure digitális Twins-kód minta használatával helyez üzembe egy Azure digitális Twins-példányt és a szükséges hitelesítést félig automatikusan. Kiindulási pontként is használható a saját megírt interakciók írásához.

Az üzembe helyezési parancsfájl Cloud Shell-ben való futtatásának lépései.
1. Nyissa meg a böngésző [Azure Cloud Shell](https://shell.azure.com/) ablakát. Jelentkezzen be a következő parancs használatával:
    ```azurecli-interactive
    az login
    ```
    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el. Ellenkező esetben nyisson meg egy böngészőt, *https://aka.ms/devicelogin* és adja meg a terminálon megjelenő engedélyezési kódot.
 
2. Győződjön meg arról, hogy a Cloud Shell a PowerShell-verzió futtatására van beállítva a Cloud Shell ikon sávján.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell a PowerShell-verzió kijelölését bemutató ablak":::

1. Válassza a "fájlok feltöltése/letöltése" ikont, és válassza a "feltöltés" lehetőséget.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell a feltöltés ikon kijelölését bemutató ablak":::

    Navigáljon a számítógépen lévő _**deploy.ps1**_ fájlra (a _digitális Twins-Samples-Master > szkriptek > **deploy.ps1**_), és nyomja meg a "Megnyitás" lehetőséget. Ezzel feltölti a fájlt a Cloud Shellba, hogy az Cloud Shell ablakban is futtatható legyen.

4. Futtassa a szkriptet a Cloud Shell ablakban a parancs elküldésével `./deploy.ps1` . Az alábbi parancsot másolhatja (emlékeztetheti rá, hogy beilleszti Cloud Shellba, a **CTRL + SHIFT + v billentyűkombinációt** használhatja Windows és Linux rendszeren, vagy a **cmd + SHIFT + v** MacOS rendszeren. Használhatja a helyi menüt is).

    ```azurecli-interactive
    ./deploy.ps1
    ```

    A szkript létrehoz egy Azure Digital Twins-példányt, és hozzárendeli az Azure-felhasználót az *Azure Digital Twins adattulajdonosi* szerepköréhez a példányon.

    Ahogy a parancsfájl az automatikus telepítés lépésein fut, a rendszer a következő értékek megadását kéri:
    * A példány: a használni kívánt Azure-előfizetés *előfizetés-azonosítója*
    * A példány esetében: egy *hely* , ahová a példányt telepíteni szeretné. Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [*Azure-termékek területre*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * A példány esetében: az *erőforráscsoport* neve. Használhat egy meglévő erőforráscsoportot, vagy megadhat egy új nevet a létrehozáshoz.
    * A példány esetében: az Azure Digital Twins-példány *neve* . Ha az előfizetése egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már a megadott nevet használja, a rendszer arra kéri, hogy válasszon másik nevet.

Íme egy részlet a kimenet naplóból a szkriptből:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell ablak, amely az üzembe helyezési parancsfájl futtatásával mutatja be a bemeneti és a kimeneti naplót" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Ha a parancsfájl sikeresen befejeződik, a rendszer a végső kinyomatot fogja mondani `Deployment completed successfully` . Ellenkező esetben oldja meg a hibaüzenetet, és futtassa újra a parancsfájlt. A művelet megkerüli a már elvégzett lépéseket, és ismét megkezdi a bevitelt arra a pontra, ahol abbahagyta a műveletet.

> [!NOTE]
> A parancsfájl jelenleg a szükséges felügyeleti szerepkört rendeli hozzá az Azure Digital Twins-ben (*Azure digitális Twins-adattulajdonos*) ugyanahhoz a felhasználóhoz, amely a szkriptet futtatja Cloud Shellról. Ha ezt a szerepkört a példányt kezelő másnak szeretné hozzárendelni, ezt most a Azure Portal ([utasítások](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) vagy a CLI ([utasítások](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) segítségével teheti meg.

>[!NOTE]
>Jelenleg létezik egy **ismert probléma** a parancsfájlokkal ellátott telepítővel, amelyben egyes felhasználók (különösen a személyes [Microsoft-fiókokkal (MSAs)](https://account.microsoft.com/account)rendelkező felhasználók) **nem hozták létre a szerepkör-hozzárendelést az _Azure Digital Twins-beli adattulajdonoshoz_**.
>
>A szerepkör-hozzárendelést a jelen cikk későbbi, [*felhasználói szerepkör-hozzárendelés ellenőrzése*](#verify-user-role-assignment) szakaszában ellenőrizheti, és – szükség esetén – a szerepkör-hozzárendelés manuális beállítása a [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) vagy a [parancssori](how-to-set-up-instance-cli.md#set-up-user-access-permissions)felület használatával.
>
>A problémáról további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)című témakörben talál.

## <a name="verify-success-and-collect-important-values"></a>Sikeres ellenőrzés és fontos értékek összegyűjtése

A parancsfájl által beállított erőforrások és engedélyek létrehozásának ellenőrzéséhez tekintse meg a [Azure Portal](https://portal.azure.com) az alábbi utasításokat. Ha nem tudja ellenőrizni a lépés sikerességét, próbálja megismételni a lépést. A lépéseket külön is végrehajthatja a [Azure Portal](how-to-set-up-instance-portal.md) vagy a [parancssori](how-to-set-up-instance-cli.md) felület utasításait használva.

Ebből a szakaszból megtudhatja, hogyan találhatja meg az Azure Digital Twins-példánnyal folytatott munka során esetlegesen szükséges parancsfájl által beállított erőforrások fontos értékeit. Ezeket az értékeket biztonságos helyen kell menteni, vagy vissza kell térnie erre a szakaszra, hogy később megkeresse őket, amikor szüksége van rájuk. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket is meg kell osztania velük.

### <a name="verify-instance"></a>Példány ellenőrzése

A példány létrejöttének ellenőrzéséhez lépjen a Azure Portal [Azure digitális Twins oldalára](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) . Ezt a lapot saját maga is elérheti, ha az *Azure Digital ikreket* keresi a portál keresési sávjában.

Ezen az oldalon az összes Azure-beli digitális Twins-példány szerepel. Keresse meg az újonnan létrehozott példány nevét a listában.

Ha az ellenőrzés sikertelen volt, megpróbálkozhat egy példány létrehozásával a [portál](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) vagy a [parancssori](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)felület használatával.

### <a name="collect-instance-values"></a>A példányhoz tartozó értékek összegyűjtése

A példány *áttekintő* oldalának megnyitásához válassza ki a példány nevét az [Azure digitális Twins oldaláról](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) . Jegyezze fel a *nevét*, az *erőforráscsoportot* és az *állomásnév nevét*. Erre később szükség lehet a példány azonosításához és a hozzá való kapcsolódáshoz.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="A példány Áttekintés oldalának fontos értékeinek kiemelése":::

### <a name="verify-user-role-assignment"></a>Felhasználói szerepkör-hozzárendelés ellenőrzése

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Ne felejtse el, hogy a parancsfájl jelenleg hozzárendeli ezt a szükséges szerepkört ugyanahhoz a felhasználóhoz, amely a parancsfájlt a Cloud Shellról futtatja. Ha ezt a szerepkört a példányt kezelő másnak szeretné hozzárendelni, ezt most a Azure Portal ([utasítások](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) vagy a CLI ([utasítások](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) segítségével teheti meg.

Ha az ellenőrzés nem sikerült, a saját szerepkör-hozzárendelését is megismételheti a [portál](how-to-set-up-instance-portal.md#set-up-user-access-permissions) vagy a [parancssori](how-to-set-up-instance-cli.md#set-up-user-access-permissions)felület használatával.

## <a name="next-steps"></a>Következő lépések

Tesztelje az egyes REST API hívásokat a példányon az Azure Digital Twins CLI parancsaival: 
* [az DT Reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Útmutató: az Azure digitális Twins parancssori felületének használata*](how-to-use-cli.md)

Vagy tekintse meg a következő témakört: ügyfélalkalmazás összekötése a példánnyal a hitelesítési kóddal:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)