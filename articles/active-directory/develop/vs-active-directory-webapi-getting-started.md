---
title: Ismerkedés az Azure AD-vel a Visual Studio WebApi projektjeiben
description: A Azure Active Directory használatának első lépései a WebApi-projektekben az Azure AD-vel való csatlakozás vagy a Visual Studio csatlakoztatott szolgáltatásaival való létrehozása után
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: fb8c1f25c8a22c96679a0310353f474f6b9d5ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165430"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Ismerkedés a Azure Active Directoryrel (WebApi-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta Active Directoryt egy ASP.NET WebAPI projekthez a Visual Studio **project > Connected Services** parancsával. Ha még nem adta hozzá a szolgáltatást a projekthez, bármikor megteheti.

Megtudhatja [, mi történt a WebAPI-projekttel?](vs-active-directory-webapi-what-happened.md) a csatlakoztatott szolgáltatás hozzáadásakor a projekten végrehajtott módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a hozzáférés-vezérléshez

A projekt összes vezérlőjét az `[Authorize]` attribútummal díszítették. Ez az attribútum megköveteli a felhasználó hitelesítését, mielőtt hozzáfér az ezen vezérlők által definiált API-khoz. Annak engedélyezéséhez, hogy a vezérlő névtelenül legyen elérhető, távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebben szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely az engedélyezést igényli a vezérlő osztályra való alkalmazása helyett.

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](./authentication-vs-authorization.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)