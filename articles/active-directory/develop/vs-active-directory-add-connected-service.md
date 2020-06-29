---
title: A Active Directory csatlakoztatott szolgáltatás (Visual Studio) használata
description: Azure Active Directory hozzáadása a Visual Studio csatlakoztatott szolgáltatások hozzáadása párbeszédpanel használatával
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: b77241b1413fea09e5a16e8bd5cbcda8db5b2327
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477124"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Azure Active Directory hozzáadása csatlakoztatott szolgáltatások használatával a Visual Studióban

Azure Active Directory (Azure AD) használatával a ASP.NET MVC-webalkalmazásokhoz vagy a webes API-szolgáltatásokban való Active Directory hitelesítéshez is támogathatja az egyszeri bejelentkezést (SSO). Az Azure AD-hitelesítéssel a felhasználók a fiókjaikat Azure Active Directory használatával csatlakozhatnak a webalkalmazásokhoz. Az Azure AD-hitelesítésnek a webes API-val való használatának előnyei közé tartozik a fokozott adatbiztonság, amikor egy webalkalmazásból kiteszi az API-t. Az Azure AD-vel nem kell külön hitelesítési rendszereket kezelnie a saját fiókjával és a felhasználói felügyelettel.

Ez a cikk és a hozzá tartozó cikkek részletesen ismertetik a Visual Studio Connected Service szolgáltatás használatát a Active Directoryhoz. A funkció a Visual Studio 2015-es és újabb verzióiban érhető el.

A Active Directory csatlakoztatott szolgáltatás jelenleg nem támogatja ASP.NET Core alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

- Azure-fiók: Ha nem rendelkezik Azure-fiókkal, [regisztrálhat az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) , vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** vagy újabb verzió. [Töltse le most a Visual studiót](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Csatlakozás Azure Active Directory a csatlakoztatott szolgáltatások párbeszédpanel használatával

1. A Visual Studióban hozzon létre vagy nyisson meg egy ASP.NET MVC-projektet vagy egy ASP.NET webes API-projektet. Használhatja az MVC, a web API, egy egyoldalas alkalmazást, az Azure API app, az Azure Mobile App és az Azure Mobile Service-sablonokat is.

1. Válassza ki a **projektet > csatlakoztatott szolgáltatás hozzáadása...** menü parancsát, vagy kattintson duplán a projektben található **csatlakoztatott szolgáltatások** csomópontra megoldáskezelő.

1. A **csatlakoztatott szolgáltatások** lapon válassza a **hitelesítés Azure Active Directorykal**lehetőséget.

    ![Csatlakoztatott szolgáltatások lap](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. A **Bevezetés** lapon válassza a **tovább**lehetőséget. Ha a lapon hibákat lát, tekintse meg a [hibák diagnosztizálása a Azure Active Directory csatlakoztatott szolgáltatással](vs-active-directory-error.md)című témakört.

    ![Bevezetés oldal](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Az **egyszeri bejelentkezés** lapon válasszon ki egy tartományt a **tartomány** legördülő listából. A lista tartalmazza a Visual Studio Fiókbeállítások párbeszédpanelén felsorolt fiókok által elérhető összes tartományt (a**fájl > Fiókbeállítások...**). Másik lehetőségként megadhatja a tartománynevet, ha nem találja a keresett nevet, például: `mydomain.onmicrosoft.com` . Lehetőség van Azure Active Directory alkalmazás létrehozására, vagy egy meglévő Azure Active Directory alkalmazás beállításainak használatára. Ha elkészült, válassza a **tovább** lehetőséget.

    ![Egyszeri bejelentkezés lap](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. A **címtár-hozzáférés** lapon válassza a kívánt módon a **Címtáradatok olvasása** lehetőséget. A fejlesztők általában ezt a lehetőséget tartalmazzák.

    ![Címtár-hozzáférési lap](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Válassza a **Befejezés** lehetőséget a projekt módosításainak megkezdéséhez az Azure ad-hitelesítés engedélyezéséhez. A Visual Studio ebben az időszakban a következő eredményeket jeleníti meg:

    ![Active Directory csatlakoztatott szolgáltatás állapota](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. A folyamat befejezésekor a Visual Studio a következő cikkek valamelyikére nyitja meg a böngészőt a projekt típusának megfelelően:

    - [Bevezetés a .NET MVC-projektek használatába](vs-active-directory-dotnet-getting-started.md)
    - [Bevezetés a WebAPI-projektek használatába](vs-active-directory-webapi-getting-started.md)

1. A [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)Active Directory tartományt is megtekintheti.

## <a name="how-your-project-is-modified"></a>A projekt módosításának módja

Amikor hozzáadja a csatlakoztatott szolgáltatást a varázslóhoz, a Visual Studio Azure Active Directory és a projekthez kapcsolódó hivatkozásokat ad hozzá. A projektben a konfigurációs fájlok és a programkódok is módosulnak az Azure AD támogatásának hozzáadásához. A Visual Studio által végrehajtott módosítások a projekt típusától függenek. A részletekért tekintse meg a következő cikkeket:

- [Mi történt a .NET MVC-projektemmel?](vs-active-directory-dotnet-what-happened.md)
- [Mi történt a webes API-projekttel?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)
