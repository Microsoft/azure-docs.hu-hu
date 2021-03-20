---
title: Nem HTTP-triggerű Azure Functions manuális futtatása
description: HTTP-kérelem használata nem HTTP-alapú aktivált Azure Functions futtatásához
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537701"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Nem HTTP által aktivált függvény manuális futtatása

Ez a cikk bemutatja, hogyan futtathat manuálisan egy nem HTTP-triggert futtató függvényt a speciálisan formázott HTTP-kérelem használatával.

Bizonyos kontextusokban előfordulhat, hogy "igény szerinti" műveletet kell futtatnia egy közvetve aktivált Azure-függvényhez.  A közvetett eseményindítók például egy [olyan függvényt](./functions-create-scheduled-function.md) tartalmaznak, amely egy [másik erőforrás műveletének](./functions-create-storage-blob-triggered-function.md)eredményeképpen futó ütemezett vagy függvényeket tartalmaz. 

A [Poster](https://www.getpostman.com/) a következő példában szerepel, de a [curl](https://curl.haxx.se/), a [Hegedűs](https://www.telerik.com/fiddler) vagy más hasonló eszköz is használható a HTTP-kérelmek küldéséhez.

## <a name="define-the-request-location"></a>A kérelem helyének megadása

A nem HTTP-triggert futtató függvények futtatásához az Azure-ba irányuló kérést kell küldenie a függvény futtatásához. A kérelem elvégzéséhez használt URL-cím egy adott űrlapot használ.

![A kérelem helyének meghatározása: állomásnév + mappa elérési útja + függvény neve](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Állomásnév:** A Function alkalmazás nyilvános helye, amely a Function alkalmazás nevéből és *azurewebsites.net* , illetve az egyéni tartományból is elérhető.
- **Mappa elérési útja:** Ha HTTP-kéréssel kívánja elérni a HTTP-triggert, akkor a kérést a mappák *adminisztrátora/függvényei* között kell elküldeni.
- **Függvény neve:** A futtatni kívánt függvény neve.

Ezt a kérést a Poster-ben, a függvény főkulcsával együtt használhatja az Azure-ba a függvény futtatásához.

> [!NOTE]
> Helyileg futtatva a függvény főkulcsa nem szükséges. Közvetlenül [hívhatja a](#call-the-function) fejlécet kihagyó függvényt `x-functions-key` .

## <a name="get-the-functions-master-key"></a>A függvény főkulcsának beolvasása

1. Keresse meg a Function alkalmazást a [Azure Portalban](https://portal.azure.com), majd válassza az **alkalmazások kulcsai** elemet, majd a `_master` kulcsot. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Keresse meg a másolandó főkulcsot." border="true":::

1. A **kulcs szerkesztése** szakaszban másolja a kulcs értékét a vágólapra, majd kattintson **az OK gombra**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Másolja a főkulcsot a vágólapra." border="true":::

1. A *_master* kulcs másolását követően válassza a **Code + test (kód + teszt**) lehetőséget, majd válassza a **naplók** lehetőséget. Az itt bejelentkezett függvény üzeneteit akkor láthatja, amikor manuálisan futtatja a függvényt a Poster szolgáltatásból.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="A &quot;Code + test&quot; (kód + teszt) lapot megjelenítő képernyőkép, amely a naplók által megjelenített üzenettel rendelkezik." border="true":::

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt egy alkalmazásban. A kulcsot csak HTTPS-végpontnak kell elküldeni.

## <a name="call-the-function"></a>A függvény meghívása

Nyissa meg a Poster-t, és kövesse az alábbi lépéseket:

1. Adja meg a **kérelem helyét az URL-cím** szövegmezőben.
1. Győződjön meg arról, hogy a HTTP-metódus a **post** értékre van beállítva.
1. Válassza a **fejlécek** fület.
1. Írja be az **x-functions-Key** értéket az első kulcsként, és illessze be a főkulcsot (a vágólapról) értékként.
1. Írja be a **Content-Type** karakterláncot második kulcsként, és írja be az **Application/JSON** értéket.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="A Poster-fejlécek beállításai." border="true":::

1. Válassza a **törzs** fület.
1. Írja be a (z) **{"input": "test"}** kifejezést a kérelem törzse.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Poster-törzs beállításai." border="true":::

1. Kattintson a **Küldés** gombra.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Kérelem küldése a Poster-nek." border="true":::

    A Poster ekkor a 202-es állapotot **fogadja el**.

1. Ezután térjen vissza a függvényhez a Azure Portal. Tekintse át a naplókat, és megtekintheti a függvénynek a kézi hívásból érkező üzeneteket.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Tekintse meg a naplókat a főkulcs tesztelési eredményeinek megtekintéséhez." border="true":::

## <a name="next-steps"></a>Következő lépések

- [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](./functions-test-a-function.md)
- [Az Azure Function Event Grid helyi hibakeresést indít](./functions-debug-event-grid-trigger-local.md)
