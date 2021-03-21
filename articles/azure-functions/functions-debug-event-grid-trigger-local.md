---
title: Azure Functions Event Grid helyi hibakeresés
description: Ismerkedjen meg a Event Grid esemény által aktivált helyi hibakeresési Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94833061"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Az Azure Function Event Grid helyi hibakeresést indít

Ez a cikk bemutatja, hogyan lehet hibakeresést végezni egy helyi függvényben, amely egy Storage-fiók által kiváltott Azure Event Grid eseményt kezeli. 

## <a name="prerequisites"></a>Előfeltételek

- Meglévő Function-alkalmazás létrehozása vagy használata
- Meglévő Storage-fiók létrehozása vagy használata
- A [ngrok](https://ngrok.com/) letöltése lehetővé teszi, hogy az Azure meghívja a helyi függvényt

## <a name="create-a-new-function"></a>Új függvény létrehozása

Nyissa meg a Function alkalmazást a Visual Studióban, majd kattintson a jobb gombbal a projekt nevére a Megoldáskezelő, majd kattintson a **> új Azure-függvény hozzáadása** lehetőségre.

Az *új Azure-függvény* ablakban válassza ki **Event Grid triggert** , majd kattintson **az OK** gombra.

![Új függvény létrehozása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

A függvény létrehozása után nyissa meg a fájlt, és másolja ki a fájl elejére írt URL-címet. Ez a hely a Event Grid trigger konfigurálásakor használatos.

![Másolás helye](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ezután állítson be egy töréspontot a kezdetű sorban `log.LogInformation` .

![Töréspont beállítása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Ezután **nyomja le az F5** billentyűt a hibakeresési munkamenet elindításához.

## <a name="allow-azure-to-call-your-local-function"></a>A helyi függvény meghívásának engedélyezése az Azure számára

Ha egy, a gépen fellépő függvényt szeretne áttörni, engedélyeznie kell az Azure-nak, hogy kommunikáljon a helyi függvénnyel a felhőből.

A [ngrok](https://ngrok.com/) segédprogram lehetővé teszi az Azure számára a gépen futó függvény meghívását. Indítsa el a *ngrok* a következő parancs használatával:

```bash
ngrok http -host-header=localhost 7071
```
A segédprogram beállításakor a parancsablakban a következő képernyőképhez hasonlóan kell kinéznie:

![Képernyőkép, amely a "ngrok" segédprogram elindítása után a parancssort jeleníti meg.](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Másolja a *ngrok* futtatásakor generált **https** URL-címet. Ez az érték az Event Grid-esemény végpontjának konfigurálásakor használatos.

## <a name="add-a-storage-event"></a>Tárolási esemény hozzáadása

Nyissa meg a Azure Portal, és navigáljon egy Storage-fiókhoz, és kattintson az **események** lehetőségre.

![Storage-fiók eseményének hozzáadása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Az *események* ablakban kattintson az **esemény-előfizetés** gombra. Az *esemény-előfizetés* ablakban kattintson a *végpont típusa* legördülő menüre, és válassza a **web Hook** elemet.

![Előfizetés típusának kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

A végpont típusának konfigurálása után kattintson a végpont **kiválasztása** lehetőségre a végpont értékének konfigurálásához.

![Végpont típusának kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Az *előfizetői végpont* értéke három különböző értékből áll. Az előtag a *ngrok* által generált HTTPS URL-cím. Az URL-cím hátralévő része a függvény kódlapja fájljában található URL-címről származik, és a függvény neve a végén van hozzáadva. A függvény kódjának URL-címétől kezdve a *ngrok* URL-címe `http://localhost:7071` és a függvény neve lecserélve `{functionname}` .

A következő képernyőképen a végső URL-cím jelenik meg:

![Végpont kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Miután megadta a megfelelő értéket, kattintson a **kijelölés megerősítése** gombra.

> [!IMPORTANT]
> A *ngrok* minden indításakor újra LÉTREJÖN a HTTPS URL-cím, és megváltozik az érték. Ezért minden alkalommal létre kell hoznia egy új esemény-előfizetést, amikor a *ngrok*-on keresztül teszi elérhetővé az Azure-t.

## <a name="upload-a-file"></a>Fájl feltöltése

Most feltölthet egy fájlt a Storage-fiókjába, hogy elindítson egy Event Grid eseményt a helyi függvénynek a kezeléséhez. 

Nyissa meg [Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) , és kapcsolódjon a Storage-fiókjához. 

- **Blob-tárolók** kibontása 
- Kattintson a jobb gombbal, majd válassza a **blob-tároló létrehozása** lehetőséget.
- A tároló **tesztelésének** neve
- Válassza ki a *teszt* tárolót
- Kattintson a **feltöltés** gombra
- Kattintson a **fájlok feltöltése** elemre.
- Válasszon ki egy fájlt, és töltse fel a blob-tárolóba.

## <a name="debug-the-function"></a>A függvény hibakeresése

Miután a Event Grid felismerte, hogy a rendszer feltölt egy új fájlt a Storage-tárolóba, a rendszer a helyi függvényben megnyomja a töréspontot.

![Ngrok elindítása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikkben létrehozott erőforrások törléséhez törölje a tároló-fiókban található **teszt** tárolót.

## <a name="next-steps"></a>Következő lépések

- [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions Event Grid trigger](./functions-bindings-event-grid.md)
