---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 343321b43d219401f0b494ab3f6a54074fbc36f3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279296"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs elindítja a függvényalkalmazást. Az alkalmazás ugyanazt az Azure Functions-futtatókörnyezetet használja a futáshoz, amelyik az Azure-ban van. A Start parancs a projekt nyelvétől függően változhat.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

A functions gazdagép indításakor a következő kimenetnek kell megadnia, amelyet az olvashatóság érdekében csonkolt:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a GET kérelemre a helyi függvény által visszaadott válasz:

![Helyi tesztelés a böngészőben](./media/functions-run-function-test-local/functions-test-local-browser.png)

Most, hogy már futtatta a függvényt helyben, létrehozhatja az Azure-ban a függvényalkalmazást és az egyéb szükséges erőforrásokat.
