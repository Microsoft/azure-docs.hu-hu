---
title: Felhőalapú tárolási alkalmazások monitorozása és hibaelhárítása az Azure-ban | Microsoft Docs
description: Diagnosztikai eszközök, mérőszámok és riasztások használata a felhőalapú alkalmazások hibaelhárításához és monitorozásához.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544785"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Felhőalapú tárolási alkalmazások monitorozása és hibaelhárítása

Ez az oktatóanyag egy sorozat negyedik, utolsó része. Megismerheti a felhőalapú tárolási alkalmazások monitorozásának és hibaelhárításának módjával.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A naplózás és a mérőszámok bekapcsolása
> * Hitelesítési hibák riasztásainak engedélyezése
> * Tesztelési célú adatforgalom futtatása helytelen SAS-tokenekkel
> * Naplók letöltése és elemzése

Az [Azure Storage Analytics](../common/storage-analytics.md) naplózási funkciót és mérőszámadatokat biztosít a tárfiókok számára. Ezen adatok révén betekintést kaphat a tárfiók állapotába. Az Azure Storage elemzéseiből való adatgyűjtéshez konfigurálhat naplókat, metrikákat és riasztásokat. E folyamat magában foglalja a naplózás bekapcsolását, a mérőszámok konfigurálását és a riasztások engedélyezését.

A tárfiókok naplózásának és mérőszámainak engedélyezése az Azure Portal **Diagnosztika** lapján történik. A Storage naplózási szolgáltatásával a sikeres és a sikertelen kérelmek adatai is rögzíthetők a tárfiókban. Ezekben a naplókban az Azure-táblák, -üzenetsorok és -blobok olvasási, írási és törlési műveleteinek részletei tekinthetők meg. Ezen kívül lehetővé teszik a kérelmek meghiúsulási okának (például időtúllépések, szabályozás vagy engedélyezési hibák) megtekintését is.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be a [Azure Portalba](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>A naplózás és a mérőszámok bekapcsolása

A bal oldali menüben válassza az **Erőforráscsoportok**, a **myResourceGroup** elemet, majd válassza ki a tárfiókját az erőforrások listájából.

A **Diagnosztikai beállítások (klasszikus)** területen állítsa az **Állapot** beállítást **Be** értékre. Győződjön meg arról, hogy a **Blob tulajdonságai** területen lévő összes beállítás engedélyezve van.

Ha elkészült, kattintson a **Mentés** gombra.

![Képernyőfelvétel: a naplózás és a metrikák bekapcsolásának konfigurációs beállításait tartalmazó szakasz.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Riasztások engedélyezése

Ha egy adott mérőszám értéke meghalad egy küszöbértéket, erről a rendszergazdák e-mail formájában értesülhetnek, illetve a riasztások webhookokat is aktiválhatnak. Ebben a példában az `SASClientOtherError` mérőszámhoz tartozó riasztást fogja engedélyezni.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>A tárfiók megkeresése az Azure Portalon

A **Monitorozás** szakaszban válassza a **Riasztások (klasszikus)** elemet.

Válassza a **Metrikariasztás hozzáadása (klasszikus)** lehetőséget, és töltse ki a **Szabály hozzáadása** űrlapot a szükséges információkkal. A **Metrika** legördülő listából válassza a `SASClientOtherError` lehetőséget. Ahhoz, hogy az első hiba aktiválja a riasztást, az **Állapot** legördülő listából válassza a **Nagyobb vagy egyenlő** lehetőséget.

![Diagnosztika panel](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Hiba szimulálása

Egy érvényes riasztás szimulálásához próbáljon lekérni egy nem létező blobot a tárfiókból. Az alábbi parancshoz szükség van egy tárolónévre. Megadhatja egy meglévő tároló nevét, vagy létrehozhat egy új tárolót a példához.

Helyettesítse be a helyőrzőket valós értékekkel (ügyeljen arra, hogy az `<INCORRECT_BLOB_NAME>` értéke ne létező érték legyen), majd futtassa a parancsot.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

Az alábbi képen példa látható egy olyan riasztásra, amely az előző példában szereplő, szimulált sikertelen futtatáson alapul.

 ![Példa riasztásra](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Naplók letöltése és megtekintése

A Storage-naplók az adatok tárolását blobkészletek formájában végzik a tárfiók **$logs** nevű blobtárolójában. Ez a tároló nem jelenik meg a fiók összes blobtárolóját felsoroló listában, de ha közvetlenül nyitja meg, akkor megtekintheti annak tartalmát.

Ebben az esetben a [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) használatával kommunikálhat az Azure Storage-fiókkal.

### <a name="download-microsoft-message-analyzer"></a>A Microsoft Message Analyzer letöltése

Töltse le és telepítse a [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) alkalmazást.

Indítsa el az alkalmazást, és válassza a **fájl**  >  **megnyitása**  >  **más forrásokból** lehetőséget.

A **File Selector** (Fájl kiválasztása) párbeszédablakban válassza a **+ Add Azure Connection** (+ Azure-kapcsolat hozzáadása) lehetőséget. Adja meg a **tárfiók nevét** és a **fiókkulcsot**, majd kattintson az **OK** gombra.

![Microsoft Message Analyzer – Azure Storage-kapcsolat hozzáadása párbeszédpanel](media/storage-monitor-troubleshoot-storage-application/figure3.png)

A csatlakozást követően bontsa ki a tárolókat a Storage fanézetében a naplóblobok megtekintéséhez. Válassza ki a legutóbbi naplóbejegyzést, és kattintson az **OK** gombra.

![Képernyőkép, amely a Microsoft Message Analyzert jeleníti meg, és kiemeli a kiválasztott naplófájlt.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

A **New Session** (Új munkamenet) párbeszédpanelen kattintson a **Start** (Indítás) gombra a napló megtekintéséhez.

Miután a naplót megnyitotta, megtekintheti a Storage-eseményeket. Ahogy az alábbi képen is láthatja, a rendszer egy `SASClientOtherError` műveletet aktivált a tárfiókon. További információk a Storage-naplózásról: [Storage Analytics](../common/storage-analytics.md).

![Microsoft Message Analyzert – Események megtekintése](media/storage-monitor-troubleshoot-storage-application/figure5.png)

A [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) szintén egy olyan eszköz, amelyet a tárfiókkal való kommunikációhoz használhat, beleértve a **$logs** tárolót és az abban található naplókat is.

## <a name="next-steps"></a>Következő lépések

A sorozat negyedik, utolsó részében megismerkedett a tárfiókok monitorozásával és hibaelhárításával, például a következőkkel:

> [!div class="checklist"]
> * A naplózás és a mérőszámok bekapcsolása
> * Hitelesítési hibák riasztásainak engedélyezése
> * Tesztelési célú adatforgalom futtatása helytelen SAS-tokenekkel
> * Naplók letöltése és elemzése

Kövesse ezt a hivatkozást az előre felépített tárolóminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Storage-szkriptminták](storage-samples-blobs-cli.md)