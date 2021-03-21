---
title: Önkiszolgáló regisztráció külső identitásokhoz – Azure AD
description: Megtudhatja, hogyan teheti lehetővé a külső felhasználók számára az alkalmazások regisztrálását az önkiszolgáló regisztráció engedélyezésével. Személyre szabott regisztrációs élményt hozhat létre az önkiszolgáló bejelentkezési felhasználói folyamat testreszabásával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653565"
---
# <a name="self-service-sign-up"></a>Önkiszolgáló regisztráció

Ha külső felhasználókkal oszt meg egy alkalmazást, előfordulhat, hogy nem mindig tudja előre, hogy kinek van szüksége az alkalmazáshoz való hozzáférésre. Az önkiszolgáló regisztráció engedélyezésével lehetővé teheti a külső felhasználók számára, hogy a meghívókat közvetlenül az egyéni felhasználóknak is regisztrálják. Személyre szabott regisztrációs élményt hozhat létre az önkiszolgáló bejelentkezési felhasználói folyamat testreszabásával. Például megadhatja az Azure AD-vel vagy a közösségi identitással való regisztrációhoz szükséges lehetőségeket, és adatokat gyűjthet a felhasználóról a regisztrációs folyamat során.

> [!NOTE]
> A felhasználói folyamatokat a szervezet által készített alkalmazásokkal társíthatja. A felhasználói folyamatok nem használhatók Microsoft-alkalmazásokhoz, például a SharePointhoz vagy a Teams szolgáltatáshoz.

## <a name="user-flow-for-self-service-sign-up"></a>Felhasználói folyamat önkiszolgáló regisztrációhoz

Az önkiszolgáló regisztrációs felhasználói folyamat létrehoz egy regisztrációs élményt a külső felhasználóknak a megosztani kívánt alkalmazáson keresztül. A felhasználói folyamat egy vagy több alkalmazáshoz is társítható. Először is engedélyeznie kell az önkiszolgáló regisztrációt a bérlő számára, és összevonása azokat az identitás-szolgáltatókat, amelyeknek engedélyezni szeretné a külső felhasználók számára a bejelentkezést. Ezután létrehozhatja és testreszabhatja a regisztrációs felhasználói folyamatot, és hozzárendelheti az alkalmazásait.
A felhasználói folyamat beállításait beállíthatja annak szabályozására, hogy a felhasználó hogyan regisztrálja az alkalmazást:

- A bejelentkezéshez használt fióktípus, például a Facebook vagy az Azure AD-fiókok
- A felhasználó által regisztrálható attribútumok, például Utónév, postai kód vagy lakhely szerinti ország/régió

Ha egy felhasználó be szeretne jelentkezni az alkalmazásba, legyen szó webes, mobil-, asztali vagy egyoldalas alkalmazásról (SPA), akkor az alkalmazás egy engedélyezési kérelmet kezdeményez a felhasználó által megadott végpont számára. A felhasználói folyamat meghatározza és szabályozza a felhasználó élményét. Amikor a felhasználó befejezi a regisztrációs felhasználói folyamatot, az Azure AD létrehoz egy jogkivonatot, és visszairányítja a felhasználót az alkalmazáshoz. A regisztráció befejezésekor a rendszer kiépít egy vendég fiókot a címtárban a felhasználó számára. Több alkalmazás is használhatja ugyanazt a felhasználói folyamatot.

## <a name="example-of-self-service-sign-up"></a>Önkiszolgáló regisztráció – példa

Az alábbi példa azt szemlélteti, hogyan történik a közösségi identitás-szolgáltatók benyújtása az Azure AD-be önkiszolgáló bejelentkezési képességekkel a vendég felhasználói számára.  
A Woodgrove partnere megnyitja a Woodgrove alkalmazást. Úgy döntenek, hogy regisztráljanak egy szállítói fiókra, így a beszállítói fiók igénylése lehetőséget választja, amely az önkiszolgáló bejelentkezési folyamatot indítja el.

![Példa önkiszolgáló regisztráció kiindulási oldalára](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Az általuk választott e-mailt használják a regisztrációhoz.

![Példa a Facebook kiválasztására a bejelentkezéshez](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Az Azure AD kapcsolatot létesít a Woodgrove a partner Facebook-fiókjának használatával, és a regisztráció után létrehoz egy új vendég fiókot a felhasználó számára.

A Woodgrove többet szeretne megtudni a felhasználóról, például a név, az üzleti név, az üzleti regisztrációs kód, a telefonszám.

![Példa a felhasználói regisztrációs attribútumok megjelenítésére](media/self-service-sign-up-overview/example-enter-user-attributes.png)

A felhasználó belép az adatokba, folytatja a regisztrációs folyamatot, és hozzáfér a szükséges erőforrásokhoz.

![Példa a bejelentkezett felhasználóra](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Következő lépések

 Részletekért lásd: önkiszolgáló [regisztráció hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md).