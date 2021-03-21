---
title: Felhasználók meghívása Video Indexerra – Azure
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan hívhat meg felhasználókat a Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
ms.openlocfilehash: 7d3415ffad4820d8651841398ec6d47352869ce2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100534739"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Gyors útmutató: felhasználók meghívása Video Indexer

A munkatársakkal való együttműködéshez meghívhatja őket a Video Indexer-fiókjába. 

> [!NOTE]
> Csak a fiók rendszergazdája adhat hozzá vagy távolíthat el felhasználókat.

## <a name="invite-new-users"></a>Új felhasználók meghívása

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre. Győződjön meg arról, hogy rendszergazdai fiókkal csatlakozik.
1. Ha Ön a rendszergazda, megjelenik a **fiók megosztása** gomb a jobb felső sarokban. Kattintson a gombra, és meghívhatja a felhasználókat. 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="Fiók megosztása":::
1. A **fiók megosztása másokkal** párbeszédpanelen adja meg annak a személynek az e-mail-címét, akit meg szeretne hívni a video Indexer-fiókjába:

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Felhasználók meghívása erre a fiókra":::  
1. A **meghívás** megnyomása után a rendszer hozzáadja a személyt a függőben lévő meghívók listájához. <br/>Két lehetőség közül választhat az egyes meghívottak közül, akik még nem csatlakoztak a fiókhoz: a **meghívás eltávolítása** vagy a **meghívó URL-címének másolása**.

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="Meghívók függőben":::  
1. Ha a meghívó csatlakozik a fiókhoz, három lehetőség közül választhat:. Két lehetőség a szerepkörökhöz: **közreműködő** (alapértelmezett) vagy **tulajdonos**. A alkalmazásban a meghívást a **Remove** parancs megnyomásával távolíthatja el.

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="Meghíváshoz csatlakozott":::  

    A felhasználók nem kapnak értesítést az eltávolítás után. Az eltávolítást követően a felhasználók nem kapnak engedélyt a bejelentkezésre.

## <a name="manage-roles-invite-more-users"></a>Szerepkörök kezelése, további felhasználók meghívása

Azon kívül, hogy a **fiók megosztása más** párbeszédpanelre, kattintson a **megosztási fiók** elemre (a fent leírtak szerint), ezt megteheti a **Beállítások** menüpontban.

1. Nyomja meg a **Beállítások** gombot a fiók megnyitása elemnél. 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="Fiókbeállítások":::  
1. Kattintson a **Szerepkörök kezelése** gombra.
1. Egy másik felhasználó meghívásához kattintson a **több személy meghívása erre a fiókra** lehetőségre.

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="További felhasználók meghívása":::  
1. Miután megnyomja a **több személy meghívása ehhez a fiókhoz** lehetőséget, megjelenik a meghívás párbeszédablak
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Felhasználók meghívása erre a fiókra":::  

## <a name="next-steps"></a>Következő lépések

Most már használhatja a [video Indexer webhelyet](video-indexer-view-edit.md) , vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videó információit.

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Regisztráció és az első videó feltöltése](video-indexer-get-started.md)
- [Az API-k használatának megkezdése](video-indexer-use-apis.md)
