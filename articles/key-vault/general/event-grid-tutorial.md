---
title: A Key Vault-értesítések fogadása és reagálás Azure Event Grid
description: Ismerje meg, hogyan integrálhatja a Key Vaultt a Azure Event Gridsal.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187374"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>A Key Vault-értesítések fogadása és reagálás Azure Event Grid

Azure Key Vault integrációja Azure Event Grid lehetővé teszi a felhasználó értesítését, ha a Key vaultban tárolt titkos kód állapota megváltozott. A szolgáltatás áttekintését lásd: [Key Vault figyelése Event Gridsal](event-grid-overview.md).

Ez az útmutató ismerteti, hogyan fogadhat Key Vault értesítéseket a Event Gridon keresztül, és hogyan reagálhat az állapot változásaira a Azure Automation használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy kulcstartó az Azure-előfizetésében. Az Azure CLI használatával gyorsan létrehozhat egy új kulcstartót az [Azure Key Vault titkos kód beállítása és lekérése](../secrets/quick-create-cli.md)a következő lépésekkel.

## <a name="concepts"></a>Alapelvek

Event Grid a felhőhöz tartozó esemény-szolgáltatás. Az útmutató lépéseinek követésével előfizethet a Key Vault eseményeire, és átirányítja az eseményeket az Automation szolgáltatásba. Ha a Key Vault egyik titka hamarosan lejár, Event Grid értesítést kap az állapot változásáról, és egy HTTP-BEJEGYZÉST küld a végpontnak. Egy webhook elindít egy PowerShell-parancsfájl automatizálási végrehajtását.

![HTTP POST folyamatábra](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Automation-fiók létrehozása

Hozzon létre egy Automation-fiókot a [Azure Portalon](https://portal.azure.com)keresztül:

1.  Nyissa meg a portal.azure.com, és jelentkezzen be az előfizetésbe.

1.  A keresőmezőbe írja be az **Automation-fiókok** kifejezést.

1.  A keresősáv legördülő listájának **szolgáltatások** szakaszában válassza az **Automation-fiókok** elemet.

1.  Válassza a **Hozzáadás** lehetőséget.

    ![Automation-fiókok ablaktábla](../media/event-grid-tutorial-2.png)

1.  Adja meg a szükséges információkat az **Automation-fiók hozzáadása** panelen, majd válassza a **Létrehozás** lehetőséget.

## <a name="create-a-runbook"></a>Runbook létrehozása

Miután elkészült az Automation-fiókkal, hozzon létre egy runbook.

![Runbook felhasználói felület létrehozása](../media/event-grid-tutorial-3.png)

1.  Válassza ki az imént létrehozott Automation-fiókot.

1.  Válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

1.  Válassza **a Runbook létrehozása** lehetőséget.

1.  Nevezze el a runbook, és válassza a **PowerShell** lehetőséget a runbook típusaként.

1.  Válassza ki a létrehozott runbook, majd kattintson a **Szerkesztés** gombra.

1.  Adja meg a következő kódot (tesztelési célokra), majd kattintson a **Közzététel** gombra. Ez a művelet a fogadott POST kérelem eredményét adja vissza.

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

Hozzon létre egy webhookot az újonnan létrehozott runbook elindításához.

1.  Válassza ki az imént közzétett runbook **erőforrások** szakaszának **webhookok** elemét.

1.  Válassza a **webhook hozzáadása** elemet.

    ![Webhook hozzáadása gomb](../media/event-grid-tutorial-5.png)

1.  Válassza az **új webhook létrehozása** lehetőséget.

1. Nevezze el a webhookot, állítsa be a lejárati dátumot, és másolja az URL-címet.

    > [!IMPORTANT] 
    > A létrehozás után nem tekintheti meg az URL-címet. Győződjön meg arról, hogy egy olyan biztonságos helyen menti a másolatot, ahol az útmutató hátralévő részében elérheti.

1. Válassza a **paraméterek és futtatási beállítások** lehetőséget, majd kattintson **az OK gombra**. Ne adjon meg paramétereket. Ez a **Létrehozás** gomb engedélyezését teszi lehetővé.

1. Válassza **az OK** , majd a **Létrehozás** lehetőséget.

    ![Új webhook felhasználói felület létrehozása](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre Event Grid-előfizetést a [Azure Portal](https://portal.azure.com)használatával.

1.  Nyissa meg a Key vaultot, és válassza az **események** lapot.

    ![Események lap a Azure Portal](../media/event-grid-tutorial-7.png)

1.  Válassza az **esemény-előfizetés** gombot.

1.  Hozzon létre egy leíró nevet az előfizetéshez.

1.  Válassza **Event Grid sémát**.

1.  A **témakör erőforrásának** az állapot változásaihoz figyelni kívánt kulcstartónak kell lennie.

1.  Az **események típusának szűréséhez** hagyja kiválasztva az összes beállítást (**9 kiválasztva**).

1.  A **Végpont típusa** mezőben válassza a **Webhook** lehetőséget.

1.  Válassza **a végpont kiválasztása** lehetőséget. Az új környezet ablaktáblán illessze be a webhook URL-címét a [webhook létrehozása](#create-a-webhook) lépésből az **előfizetői végpont** mezőbe.

1.  Válassza a **kijelölés megerősítése** elemet a helyi ablaktáblán.

1.  Válassza a **Létrehozás** lehetőséget.

    ![Esemény-előfizetés létrehozása](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Ellenőrizze, hogy a Event Grid-előfizetés megfelelően van-e konfigurálva. Ez a teszt feltételezi, hogy előfizetett a "titkos új verzió" értesítésre a [Event Grid előfizetés létrehozása](#create-an-event-grid-subscription)című cikkben, és hogy rendelkezik a szükséges engedélyekkel a titkos kulcs új verziójának létrehozásához a kulcstartóban.

![Event Grid előfizetés konfigurációjának tesztelése](../media/event-grid-tutorial-9.png)

![Create-a-Secret panel](../media/event-grid-tutorial-10.png)

1.  Nyissa meg a Azure Portal a Key vaultot.

1.  Hozzon létre egy új titkot. Tesztelési célból állítsa a lejárat dátumát a következő napra.

1.  A Key Vault **események** lapján válassza ki a létrehozott Event Grid előfizetést.

1.  A **metrikák** területen győződjön meg arról, hogy az esemény rögzítése megtörtént-e. A rendszer két eseményt vár: SecretNewVersion és SecretNearExpiry. Ezek az események ellenőrzik, hogy Event Grid sikeresen rögzítette-e a titkos kulcs állapotának változását a kulcstartóban.

    ![Metrikák ablaktábla: rögzített események keresése](../media/event-grid-tutorial-11.png)

1.  Nyissa meg az Automation-fiókját.

1.  Válassza a **runbookok** lapot, majd válassza ki a létrehozott runbook.

1.  Válassza a **webhookok** fület, és győződjön meg róla, hogy az "utolsó indítású" időbélyegző az új titok létrehozásakor 60 másodpercen belül van. Ez az eredmény megerősíti, hogy Event Grid közzétett egy BEJEGYZÉST a webhooknak a kulcstartóban lévő állapotadatok részleteivel és a webhook aktiválásával.

    ![Webhookok lap, utolsó aktivált időbélyegző](../media/event-grid-tutorial-12.png)

1. Térjen vissza a runbook, és válassza az **Áttekintés** lapot.

1. Tekintse meg a **legutóbbi feladatok** listáját. Látnia kell, hogy a rendszer létrehozta a feladatot, és befejezte az állapotot. Ezzel megerősíti, hogy a webhook elindította a runbook a parancsfájl végrehajtásának megkezdéséhez.

    ![Webhook legutóbbi feladatok listája](../media/event-grid-tutorial-13.png)

1. Válassza ki a legutóbbi feladatot, és tekintse meg az Event Grid által a webhooknak elküldett POST-kérelmet. Vizsgálja meg a JSON-t, és ellenőrizze, hogy helyesek-e a Key Vault és az eseménytípus paraméterei. Ha a JSON-objektum "eseménytípus" paramétere megegyezik a Key vaultban bekövetkezett eseménnyel (ebben a példában a Microsoft. kulcstartó. SecretNearExpiry), a teszt sikeres volt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="you-cant-create-an-event-subscription"></a>Nem hozható létre esemény-előfizetés

Regisztrálja újra Event Grid és a Key Vault-szolgáltatót az Azure-előfizetési erőforrás-szolgáltatókban. Lásd: [Azure Resource Providers és types](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Ha helyesen követte ezeket a lépéseket, most már készen áll arra, hogy programozott módon válaszoljon a Key vaultban tárolt titkos kódok állapotának változásaira.

Ha egy lekérdezésen alapuló rendszert használ a titkos kulcsok állapotának változására a kulcstartóban, most már elkezdheti használni ezt az értesítési funkciót. A runbook is lecserélheti a kód segítségével, hogy programozott módon megújítsa a titkokat, amikor hamarosan lejárnak.

További információ:


- Áttekintés: [Key Vault figyelése Azure Event Grid](event-grid-overview.md)
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault áttekintése](overview.md)
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- [Azure Automation áttekintése](../../automation/index.yml)
