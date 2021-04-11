---
title: 'Útmutató: regisztrált alkalmazás eltávolítása a Microsoft Identity platformról | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a útmutatóban megtudhatja, hogyan távolíthat el egy, a Microsoft Identity platformmal regisztrált alkalmazást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080213"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>A Microsoft Identity platformmal regisztrált alkalmazások eltávolítása

Előfordulhat, hogy a Microsoft Identity platformmal regisztrált alkalmazásokkal rendelkező nagyvállalati fejlesztők és szoftveres (SaaS) szolgáltatók el kell távolítaniuk az alkalmazások regisztrációját.

A következő fejezetekben megismerheti a következőket:

* A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása
* Más szervezet által fejlesztett alkalmazás eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Az [Azure ad-bérlőben regisztrált alkalmazás](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása

A saját maga vagy a szervezete által regisztrált alkalmazásokat egy alkalmazás- és egy szolgáltatásnév-objektum is jelöli a bérlőn. További információkért tekintse meg [az alkalmazás- és szolgáltatásnév-objektumokat](./app-objects-and-service-principals.md) ismertető cikket.

> [!NOTE]
> Egy alkalmazás törlésével a szolgáltatás egyszerű objektuma is törlődik az alkalmazás saját könyvtárában. A több-bérlős alkalmazások esetében a más címtárakban található szolgáltatásnév-objektumok nem lesznek törölve.

Egy alkalmazás törléséhez az alkalmazás tulajdonosaként vagy rendszergazdai jogosultságokkal kell szerepelnie.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben az alkalmazás regisztrálva van.
1. Keresse meg és válassza ki a **Azure Active Directory**. 
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  lehetőséget, majd válassza ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik annak **Áttekintés** lapja.
1. Az **Áttekintés** lapon válassza a **Törlés** elemet.
1. Olvassa el a törlés következményeit.  Jelölje be a jelölőnégyzetet, ha a panel alján megjelenik egy.
1. A **Törlés** lehetőség kiválasztásával erősítse meg, hogy törölni kívánja az alkalmazást.

## <a name="remove-an-application-authored-by-another-organization"></a>Más szervezet által fejlesztett alkalmazás eltávolítása

Ha az **alkalmazásregisztrációkat** egy bérlő kontextusában tekinti meg, a **Minden alkalmazás** lapon szereplő alkalmazások egy része más bérlőről származik, és a hozzájárulási folyamat során lettek regisztrálva a bérlőjében. Pontosabban ezeket az alkalmazásokat csak egy szolgáltatásnév-objektum jelöli a bérlőben, és nem tartozik hozzájuk alkalmazásobjektum. Az alkalmazás- és szolgáltatásnév-objektumok különbségeivel kapcsolatos további információért tekintse meg [az Azure AD-ben használt alkalmazás- és szolgáltatásnév-objektumokkal](./app-objects-and-service-principals.md) foglalkozó cikket.

Egy alkalmazás az Ön könyvtárához való hozzáférésének eltávolításához (a hozzájárulás megadása után) a vállalati rendszergazdának el kell távolítania az alkalmazás szolgáltatásnevét. A rendszergazdának globális rendszergazdai-hozzáféréssel kell rendelkeznie, és el is távolíthatja az alkalmazást a Azure Portal keresztül, vagy az [Azure ad PowerShell-parancsmagok](/previous-versions/azure/jj151815(v=azure.100)) használatával eltávolíthatja a hozzáférést.

## <a name="next-steps"></a>Következő lépések

További információ az [alkalmazások és szolgáltatások egyszerű objektumairól](app-objects-and-service-principals.md) a Microsoft Identity platformon.
