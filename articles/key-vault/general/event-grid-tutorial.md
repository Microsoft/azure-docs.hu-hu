---
title: Kulcstartó-értesítések fogadása és válaszadás a Azure Event Grid
description: Megtudhatja, hogyan integrálhatja Key Vault a Azure Event Grid.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e46161812d122a1d5647e8589c58f9528578b878
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749833"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Kulcstartó-értesítések fogadása és válaszadás a Azure Event Grid

Azure Key Vault integrációja Azure Event Grid lehetővé teszi a felhasználói értesítéseket, ha egy kulcstartóban tárolt titkos kulcs állapota megváltozott. A szolgáltatás áttekintését lásd: [Monitoring Key Vault with Event Grid.](event-grid-overview.md)

Ez az útmutató ismerteti, hogyan fogadhat Key Vault értesítéseket a Event Grid, és hogyan válaszolhat az állapotváltozásra a Azure Automation.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy kulcstartó az Azure-előfizetésben. Gyorsan létrehozhat egy új kulcstartót a Titkos kulcs beállítása és lekérése az Azure CLI használatával [Azure Key Vault lépéseit követve.](../secrets/quick-create-cli.md)

## <a name="concepts"></a>Alapelvek

Event Grid egy eseményszolgáltatás a felhőben. Az útmutató lépéseit követve feliratkozhat az eseményekre a Key Vault és az Automationbe irányíthat eseményeket. Amikor a kulcstartóban található titkos kulcsok egyike hamarosan lejár, Event Grid értesítést kap az állapotváltozásról, és HTTP POST-bejegyzést hoz a végpontra. A web hook aktivál egy Automation-végrehajtást egy PowerShell-szkriptben.

![HTTP POST folyamatábra](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Automation-fiók létrehozása

Hozzon létre egy Automation-fiókot a [Azure Portal:](https://portal.azure.com)

1.  Lépjen a portal.azure.com, és jelentkezzen be az előfizetésbe.

1.  A keresőmezőbe írja be az **Automation-fiókok kifejezéseket.**

1.  A **keresősáv** legördülő listájának Szolgáltatások szakaszában válassza az **Automation-fiókok lehetőséget.**

1.  Válassza a **Hozzáadás** lehetőséget.

    ![Automation-fiókok panel](../media/event-grid-tutorial-2.png)

1.  Adja meg a szükséges adatokat az **Automation-fiók hozzáadása panelen,** majd válassza a **Létrehozás lehetőséget.**

## <a name="create-a-runbook"></a>Runbook létrehozása

Miután az Automation-fiók készen áll, hozzon létre egy runbookot.

![Runbook felhasználói felületének létrehozása](../media/event-grid-tutorial-3.png)

1.  Válassza ki az előbb létrehozott Automation-fiókot.

1.  A **Folyamatautomatizálás alatt válassza** a **Runbookok lehetőséget.**

1.  Válassza **a Runbook létrehozása lehetőséget.**

1.  Nevezze el a runbookot, és válassza **a PowerShell** lehetőséget runbooktípusként.

1.  Válassza ki a létrehozott runbookot, majd kattintson a **Szerkesztés gombra.**

1.  Adja meg a következő kódot (tesztelési célokra), és válassza a **Közzététel gombot.** Ez a művelet a kapott POST-kérés eredményét adja vissza.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook felhasználói felületének közzététele](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Webhook létrehozása

Hozzon létre egy webhookot az újonnan létrehozott runbook aktiváláshoz.

1.  A most közzétett runbook **Erőforrások** szakaszában válassza a **Webhooks** (Webhookok) lehetőséget.

1.  Válassza **a Webhook hozzáadása lehetőséget.**

    ![Webhook hozzáadása gomb](../media/event-grid-tutorial-5.png)

1.  Válassza **az Új webhook létrehozása lehetőséget.**

1. Nevezze el a webhookot, állítson be lejárati dátumot, és másolja ki az URL-címet.

    > [!IMPORTANT] 
    > A létrehozás után nem tudja megtekinteni az URL-címet. A másolatokat biztonságos helyre mentse, ahol az útmutató hátralévő részében hozzáférhet.

1. Válassza a **Paraméterek és futtatás beállításait,** majd kattintson az **OK gombra.** Ne adjon meg paramétereket. Ezzel engedélyezi a **Létrehozás** gombot.

1. Válassza **az OK,** majd a **Létrehozás lehetőséget.**

    ![Új webhook felhasználói felület létrehozása](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy Event Grid előfizetést a [Azure Portal.](https://portal.azure.com)

1.  A Kulcstartóban válassza az **Események lapot.**

    ![Események lap a Azure Portal](../media/event-grid-tutorial-7.png)

1.  Válassza az **Esemény-előfizetés** gombot.

1.  Hozzon létre egy leíró nevet az előfizetéshez.

1.  Válassza **Event Grid sémát.**

1.  **A témakör** erőforrásának az állapotváltozásokat figyelni kívánt kulcstartónak kell lennie.

1.  A **Szűrés eseménytípusokra beállításnál** hagyja bejelölve az összes beállítást **(9 kiválasztva).**

1.  A **Végpont típusa** mezőben válassza a **Webhook** lehetőséget.

1.  Válassza **a Végpont kiválasztása lehetőséget.** Az új helyi panelen illessze be a webhook URL-címét [a Webhook létrehozása](#create-a-webhook) lépésből az **Előfizetői végpont mezőbe.**

1.  Válassza **a Kijelölés megerősítése** lehetőséget a helyi panelen.

1.  Válassza a **Létrehozás** lehetőséget.

    ![Esemény-előfizetés létrehozása](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Ellenőrizze, hogy az Event Grid-előfizetés megfelelően van-e konfigurálva. Ez a teszt feltételezi, hogy előfizetett a Create [an Event Grid subscription (A Event Grid-előfizetés](#create-an-event-grid-subscription)létrehozása) dokumentumban a "Secret New Version Created" (Létrehozott titkos kulcs új verziója) értesítésre, és hogy rendelkezik a kulcstartóban a titkos kulcs új verziójának létrehozásához szükséges engedélyekkel.

![Előfizetés konfigurációja Event Grid tesztelése](../media/event-grid-tutorial-9.png)

![Titkos adat létrehozása panel](../media/event-grid-tutorial-10.png)

1.  A kulcstartóhoz a Azure Portal.

1.  Hozzon létre egy új titkos gombra. Tesztelési célból állítsa a lejárati időt a következő napra.

1.  A **kulcstartó Események** lapján válassza ki a Event Grid előfizetést.

1.  A **Metrikák alatt** ellenőrizze, hogy rögzített-e egy eseményt. Két esemény várható: SecretNewVersion és SecretNearExpiry. Ezek az események ellenőrzik, Event Grid sikeresen rögzítette-e a titkos kulcs állapotváltozását a kulcstartóban.

    ![Metrikák panel: rögzített események ellenőrzése](../media/event-grid-tutorial-11.png)

1.  Ugrás az Automation-fiókra.

1.  Válassza a **Runbookok** lapot, majd válassza ki a létrehozott runbookot.

1.  Válassza a **Webhookok** lapot, és győződjön meg arról, hogy az "utolsó aktivált" időbélyeg az új titkos gombra való létrehozástól számított 60 másodpercen belül van. Ez az eredmény megerősíti, Event Grid egy POST-et a webhookba a kulcstartóban történt állapotváltozás esemény részleteivel, és hogy a webhook aktiválva lett.

    ![Webhookok lap, Utolsó aktivált időbélyeg](../media/event-grid-tutorial-12.png)

1. Térjen vissza a runbookhoz, és válassza az **Áttekintés** lapot.

1. Nézze meg a **Legutóbbi feladatok** listát. Látnia kell, hogy létrejött egy feladat, és hogy az állapot befejeződött. Ez megerősíti, hogy a webhook elindította a runbookot a szkript futtatásának elkezdésében.

    ![Webhook legutóbbi feladatok listája](../media/event-grid-tutorial-13.png)

1. Válassza ki a legutóbbi feladatot, és nézze meg a webhooknak Event Grid post kérést. Vizsgálja meg a JSON-t, és győződjön meg arról, hogy a kulcstartó és az eseménytípus paraméterei helyesek. Ha a JSON-objektum "eseménytípus" paramétere megegyezik a kulcstartóban (ebben a példában Microsoft.KeyVault.SecretNearExpiry) történt eseményekkel, a teszt sikeres volt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="you-cant-create-an-event-subscription"></a>Nem hozhat létre esemény-előfizetést

Regisztrálja újra Event Grid kulcstartó-szolgáltatót az Azure-előfizetés erőforrás-szolgáltatóiban. Lásd: [Azure-erőforrásszolgáltatók és -típusok.](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Ha ezeket a lépéseket megfelelően követte, most már készen áll arra, hogy programozott módon válaszoljon a kulcstartóban tárolt titkos kulcsok állapotváltozásaira.

Ha lekérdezésalapú rendszert használt a titkos kulcsok állapotváltozásának keresésére a kulcstartókban, most már használhatja ezt az értesítési funkciót. A runbookban a tesztelési szkriptet olyan kódra is lecserélheti, amely programozott módon megújítja a titkos kódokat, amikor azok hamarosan lejárnak.

További információ:


- Áttekintés: [A Key Vault monitorozása Azure Event Grid](event-grid-overview.md)
- How to: [Receive email when a key vault secret changes](event-grid-logicapps.md)
- [Azure Event Grid eseménysémát a Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault áttekintés](overview.md)
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- [Azure Automation áttekintés](../../automation/index.yml)
