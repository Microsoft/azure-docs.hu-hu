---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84731011"
---
A Visual Studióban Azure Functions Project sablon létrehoz egy projektet, amelyet közzétehet egy Azure-beli Function alkalmazásban. A függvények használatával logikai egységként csoportosíthatja a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése, skálázása és megosztása érdekében.

1. A Visual Studio menüjében válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

1. A **create a New Project (új projekt létrehozása**) mezőbe írja be a *functions* kifejezést a keresőmezőbe, válassza ki a **Azure functions** sablont, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** területen adja meg a projekt **nevét** , majd válassza a **Létrehozás** lehetőséget. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Az **új Azure functions alkalmazás beállításainak létrehozásához** használja az alábbi táblázatban szereplő értékeket:

    | Beállítás      | Érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Függvények futtatókörnyezete** | **Azure Functions v3 <br /> (.net Core)** | Ez az érték létrehoz egy Function projektet, amely az Azure Functions 3. x futtatókörnyezetét használja, amely támogatja a .NET Core 3. x verziót. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Azure functions Runtime Versions – áttekintés](../articles/azure-functions/functions-versions.md).   |
    | **Függvény sablonja** | **HTTP-eseményindító** | Ez az érték egy HTTP-kérelem által aktivált függvényt hoz létre. |
    | **Storage-fiók (AzureWebJobsStorage)**  | **Storage-emulátor** | Mivel egy Azure-függvényhez Storage-fiók szükséges, az egyiket a rendszer a projekt Azure-ba való közzétételekor rendeli hozzá vagy hozza létre. A HTTP-triggerek nem használnak Azure Storage-fiókhoz tartozó kapcsolatok karakterláncát; minden más trigger-típushoz érvényes Azure Storage-fiókhoz tartozó összekapcsolási karakterlánc szükséges.  |
    | **Engedélyszint** | **Névtelen** | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokkal és az engedélyezéssel kapcsolatos további információkért lásd: [engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) és [http-és webhook-kötések](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Functions a projekt beállításait](./media/functions-vs-tools-create/functions-project-settings.png)

    Ügyeljen arra, hogy az **engedélyezési szintet** **Névtelen** értékre állítsa. Ha az alapértelmezett **függvényt** választja, akkor a függvény-végpont eléréséhez a kérelmekben be kell mutatnia a [függvény kulcsát](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) .

1. Válassza a **Létrehozás** lehetőséget a függvény projekt és a http-trigger függvény létrehozásához.
