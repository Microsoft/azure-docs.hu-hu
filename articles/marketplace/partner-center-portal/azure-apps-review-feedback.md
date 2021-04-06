---
title: Az Azure apps-ajánlatok visszajelzésének áttekintése – Microsoft kereskedelmi piactér
description: Az Azure-alkalmazással kapcsolatos visszajelzések kezelése a Microsoft Azure Marketplace felülvizsgálati csapattól. Az Azure DevOps-ben a partner Center hitelesítő adataival férhet hozzá a visszajelzésekhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436385"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Az Azure-alkalmazások ajánlati felülvizsgálati visszajelzésének feldolgozása

Ez a cikk azt ismerteti, hogyan férhet hozzá az [Azure DevOps](https://azure.microsoft.com/services/devops/)Microsoft Azure Marketplace felülvizsgálati csapatával kapcsolatos visszajelzésekhez. Ha a **Microsoft felülvizsgálati** lépése során kritikus problémák léptek fel az Azure-alkalmazás ajánlatában, bejelentkezhet a rendszerbe a problémák részletes adatainak megtekintéséhez (visszajelzés megtekintése). Az összes probléma megoldása után újból el kell küldenie az ajánlatát, hogy továbbra is közzé lehessen tenni az Azure Marketplace-en. A következő ábra azt szemlélteti, hogy a visszajelzési folyamat hogyan kapcsolódik a közzétételi folyamathoz.

![Visszajelzési folyamat áttekintése](./media/review-feedback-process.png)

A felülvizsgálati problémák általában lekéréses kérelemként (PR) vannak hivatkozva. Mindegyik PR egy online Azure DevOps-elemhez van társítva, amely a probléma részleteit tartalmazza. Az alábbi képen egy példa látható a partner Center-élményre, ha az ellenőrzések során problémák merülnek fel. 

![Közzététel állapota](./media/publishing-status.png)

A beküldéssel kapcsolatos konkrét adatokat tartalmazó PR-t a "tanúsítási jelentés megtekintése" hivatkozásra kattintva lehet megemlíteni. Összetett helyzetekben a felülvizsgálati és támogatási csapat is elküldheti e-mailben.

## <a name="azure-devops-access"></a>Azure DevOps-hozzáférés

A partner Centerben a "fejlesztői" szerepkörhöz hozzáféréssel rendelkező összes felhasználó hozzáférhet a felülvizsgálati visszajelzések című témakörben hivatkozott PR-elemek megtekintéséhez.

## <a name="reviewing-the-pull-request"></a>A lekéréses kérelem áttekintése

A következő eljárással ellenőrizheti a lekéréses kérelemben dokumentált problémákat.

1. A közzétételi lépések űrlap **Microsoft felülvizsgálati** szakaszaiban válasszon egy PR-hivatkozást a böngésző elindításához, és keresse meg a PR **Áttekintés** (Kezdőlap) lapját. Az alábbi ábrán egy példa látható a contoso minta app-ajánlat kritikus fontosságú kezdőlapján. Ez az oldal hasznos összegző információkat tartalmaz az Azure-alkalmazásban található felülvizsgálati problémákról.

    [![Lekéréses kérelem kezdőlapja](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kattintson erre a képre a kibontáshoz.*

1. Választható Az ablak jobb oldalán, a **szabályzatok** szakaszban válassza ki a problémát jelző üzenetet (ebben a példában a **házirend ellenőrzése nem sikerült**) a probléma alacsony szintű részleteinek vizsgálatához, beleértve a kapcsolódó naplófájlokat is. A hibák általában a naplófájlok alján jelennek meg.

1. A Kezdőlap bal oldali menüjében válassza a **fájlok** lehetőséget, hogy megjelenjenek az ajánlat technikai eszközeiből álló fájlok. A Microsoft felülvizsgálók számára a felderített kritikus problémák leírását ismertető megjegyzésekkel kell rendelkeznie. A következő példában két probléma merült fel.

    [![Képernyőkép, amely kiemeli a fájlokat és a felderített két problémát.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kattintson erre a képre a kibontáshoz.*

1. A bal oldali fában válassza ki a Megjegyzés csomópontot, és navigáljon a környező kód kontextusában található megjegyzéshez. Javítsa ki a forráskódját a csapat projektjeiben a megjegyzésben leírt probléma kijavításához.

>[!Note]
>Az ajánlat technikai eszközei nem szerkeszthetők a felülvizsgálati csapat Azure DevOps-környezetében. A kiadók esetében ez egy írásvédett környezet a foglalt forráskódhoz. A válaszokat azonban a Microsoft felülvizsgálati csapatának javára is elhagyhatja a megjegyzésekben.

   A következő példában a közzétevő áttekintette, kijavította és megválaszolta az első problémát.

   ![Első javítás és Megjegyzés válasza](./media/first-comment-reply.png)

## <a name="next-steps"></a>Következő lépések

Miután javította a PR (k) felülvizsgálatával kapcsolatos kritikus problémákat, újra közzé kell tennie az [Azure-alkalmazás ajánlatát](../create-new-azure-apps-offer.md).
