---
title: Azure API Management-szabályzatok hibakeresése a Visual Studio Code-ban | Microsoft Docs
description: Ismerje meg, hogyan lehet hibakeresést végezni az Azure API Management-szabályzatok használatával az Azure API Management Visual Studio Code bővítménnyel
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648016"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Azure API Management-szabályzatok hibakeresése a Visual Studio Code-ban

Az Azure API Management hatékony képességeket biztosítanak az API-közzétevők számára, például a hitelesítés, az engedélyezés, a szabályozás, a gyorsítótárazás és az [átalakítás terén.](api-management-policies.md) A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. 

Ez a cikk a Visual Studio Code-hoz készült [Azure API Management bővítménnyel](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)API Management szabályzatok hibakeresését ismerteti. 

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy API Management fejlesztői szintű [példányt ehhez a](get-started-create-service-instance.md) rövid útmutatóhoz elsőként.

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/) -ot és az [Azure API Management Extension legújabb verzióját a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)-hoz. 

* API importálása a API Management-példányba. A lépéseket például [az oktatóanyag: az API-k importálásához és kezeléséhez használja a Visual Studio Code API Management bővítményét](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Korlátozások

Ez a funkció csak a API Management fejlesztői szintjében érhető el. Minden API Management-példány csak egy egyidejű hibakeresési munkamenetet támogat.

## <a name="initiate-a-debugging-session"></a>Hibakeresési munkamenet kezdeményezése

1. Indítsa el a Visual Studio Code-ot
2. Navigáljon az API Management-bővítményre az Azure-bővítmények területen
3. A hibakereséshez API Management-példány keresése
4. A hibakereséshez használandó API és művelet megkeresése
5. Kattintson a jobb gombbal a műveletre, és válassza a **házirend-hibakeresés indítása** lehetőséget.

Ezen a ponton a bővítmény megpróbál kezdeményezni és létrehozni egy hibakeresési munkamenetet a API Management átjáróval.

![hibakeresés kezdeményezése](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Tesztelési kérelem küldése
A hibakeresési munkamenet létrejötte után a bővítmény egy új szerkesztőt nyit meg, amely lehetővé teszi, hogy egy teszt HTTP-kérést hozzon létre és küldjön a műveletnek a [Rest-ügyfél bővítményének](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)kihasználása érdekében.

Figyelje meg, hogy a **OCP-APIM-debug** fejléc már hozzá lett adva a kérelemhez. Ezt a fejlécet kötelező megadni, és az értéket a szolgáltatás szintű, az összes hozzáférési előfizetési kulcs értékre kell beállítani a API Management-átjáró hibakeresési funkciójának elindításához.

Módosítsa a HTTP-kérelmet a szerkesztőben a tesztelési forgatókönyvnek megfelelően. Ezután kattintson a **kérelem küldése** gombra a tesztelési kérelem küldéséhez a API Management átjárónak.

![tesztelési kérelem küldése](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Hibakeresési szabályzatok
A teszt HTTP-kérelem elküldése után a bővítmény megnyitja a hibakeresési időszakot, amely a művelet hatályos szabályzatait jeleníti meg, és az első érvényes házirend után leáll. 

![hibakeresési szabályzatok](media/api-management-debug-policies/main-window.png)

A házirend-folyamat követéséhez egyetlen lépéssel külön házirendeket adhat meg, vagy beállíthat egy töréspontot egy házirendben, és közvetlenül is megadhatja azt a házirendnek. 

A **változók** panelen ellenőrizheti a rendszer által létrehozott és a felhasználó által létrehozott változók értékeit. A **Töréspontok** panelen láthatja a beállított töréspontok listáját. A **hívási verem** panelen láthatja az aktuális hatályos házirend hatókörét. 

Ha a házirend végrehajtása során hiba lépett fel, a hiba részleteit a házirendben tekintheti meg, ahol ez történt. 

![kivételek](media/api-management-debug-policies/exception.png)

> [!TIP]
> Ha elkészült, a **Leállítás** gombra kattintva lépjen ki a hibakeresési munkamenetből.


## <a name="next-steps"></a>Következő lépések

+ További információ a [Visual Studio Code API Management-bővítményéről](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Problémák jelentése a [GitHub-tárházban](https://github.com/Microsoft/vscode-apimanagement)

