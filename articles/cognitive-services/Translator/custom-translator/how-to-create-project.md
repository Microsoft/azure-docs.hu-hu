---
title: Projekt létrehozása – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy projektet az Azure Cognitive Services Custom Translator szolgáltatásban.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3043ec0600f802a409387f0114aae5f56151da16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510979"
---
# <a name="create-a-project"></a>Projekt létrehozása

A projekt egy modell, dokumentum és teszt tárolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik.

A projekt létrehozása az első lépés a modell felépítése felé.

## <a name="create-a-project"></a>Projekt létrehozása:

1.  Az [Egyéni Translator](https://portal.customtranslator.azure.ai) portálon kattintson a projekt létrehozása lehetőségre.

    ![Projekt létrehozása](media/how-to/how-to-create-project.png)

2.  Adja meg a következő adatokat a projekthez a párbeszédpanelen:

    a.  Projekt neve (kötelező): adjon egy egyedi, értelmes nevet a projektnek. A címben nem szükséges megemlíteni a nyelveket.

    b.  Leírás: rövid összefoglalás a projektről. Ez a leírás nem befolyásolja az egyéni fordító vagy az eredményül kapott egyénirendszer viselkedését, de segíthet a különböző projektek közötti különbségtételben.

    c.  Nyelvi pár (kötelező): válassza ki azt a nyelvet, amelynek a és a rendszerből való fordítását végzi.

    d.  Kategória (kötelező): válassza ki a projektnek leginkább megfelelő kategóriát. A kategória a lefordítani kívánt dokumentumok terminológiáját és stílusát írja le.

    e.  Kategória leírása: ebben a mezőben jobban leírhatja, hogy az adott mező vagy iparág hogyan működik. Ha például a kategóriája gyógyszert tartalmaz, hozzáadhat egy adott dokumentumot, például egy operációt vagy egy gyermekgyógyászati feladathoz. A leírás nem befolyásolja az egyéni fordító vagy az Ön által létrehozott egyéni rendszerek viselkedését.

    f.  Projekt felirata: a [projekt felirata](workspace-and-project.md#project-labels) megkülönbözteti a projekteket ugyanazzal a nyelvi párral és kategóriával. Ajánlott eljárásként *csak* akkor használjon címkét, ha több projektet szeretne felépíteni ugyanahhoz a nyelvi párra és kategóriára, és egy másik Kategóriakód használatával szeretné elérni ezeket a projekteket. Ne használja ezt a mezőt, ha csak egy kategóriához hoz létre rendszereket. Nem szükséges a projekt címkéje, és nem lehet különbséget tenni a nyelvi párok között. Ugyanazt a címkét több projekt esetében is használhatja.

    ![Projekt létrehozása párbeszédpanel](media/how-to/how-to-create-project-dialog.png)

3.  Kattintson a Létrehozás gombra

## <a name="view-project-details"></a>Projekt részleteinek megtekintése

Az egyéni fordító kezdőlapja a munkaterület első 10 projektjét jeleníti meg. Megjeleníti a projekt nevét, a nyelvi párokat, a kategóriát, az állapotot és a BLEU pontszámot.

A projekt kiválasztása után a következő jelenik meg a projekt oldalon:

- Kategóriakód: A Kategóriakód létrehozása a munkaterület azonosítója, a projekt címkéjének és a kategória kódjának összefűzésével történik. A Kategóriakód és a Text Translator API használatával egyéni fordításokat kaphat. A másoláshoz kattintson a másolás ikonra.

- Betanítás gomb: ezzel a gombbal elindítható egy [modell képzése](how-to-train-model.md).

- Dokumentumok hozzáadása gomb: ezzel a gombbal [tölthet fel dokumentumokat](how-to-upload-document.md).

- Dokumentumok szűrése gomb: ezzel a gombbal szűrheti és keresheti meg az adott dokumentum (oka) t.

    ![Projekt részleteinek megtekintése](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan keresheti meg, szerkesztheti és törölheti a projektet](how-to-search-edit-delete-projects.md).
- Megtudhatja [, hogyan tölthet fel dokumentumokat](how-to-upload-document.md) a fordítási modellek létrehozásához.
