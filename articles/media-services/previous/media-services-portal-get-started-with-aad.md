---
title: Az Azure AD-hitelesítés használatának első lépései a Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Portal az Azure Active Directory (Azure AD) hitelesítés elérésére a Azure Media Services API felhasználásához.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013057"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Bevezetés az Azure AD-hitelesítés használatába az Azure Portalon

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Megtudhatja, hogyan érheti el a Azure Portal az Azure Active Directory (Azure AD) hitelesítés eléréséhez a Azure Media Services API eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](media-services-portal-create-account.md).

Ha az Azure AD-hitelesítést Azure Media Services használatával használja, két hitelesítési lehetőség közül választhat:

- **Egyszerű szolgáltatás hitelesítése**. Szolgáltatás hitelesítése. Az ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok futtatására szolgálnak: webalkalmazások, Function apps, Logic apps, API-k vagy egy szolgáltatás.
- **Felhasználói hitelesítés**. Az alkalmazást használó személy hitelesítése Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Ilyen alkalmazás lehet például egy olyan felügyeletikonzol-alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő streamelés monitorozására használnak. 

## <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Ezen a lapon kiválaszthatja az API-hoz való kapcsolódáshoz használni kívánt hitelesítési módszert. Az oldalon az API-hoz való kapcsolódáshoz szükséges értékeket is megadja.

1. A [Azure Portal](https://portal.azure.com/)válassza ki Media Services-fiókját.
2. Válassza ki, hogyan szeretne csatlakozni a Media Services API-hoz.
3. A **kapcsolódás Media Services API**-hoz területen válassza ki azt a Media Services API-verziót, amelyhez csatlakozni szeretne.

## <a name="service-principal-authentication--recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)

Egy Azure Active Directory (Azure AD) alkalmazás és titok használatával hitelesíti a szolgáltatást. Ez a Media Services API-t hívó középső szintű szolgáltatások esetében ajánlott. Ilyenek például a Web Apps, a functions, a Logic Apps, az API-k és a-szolgáltatások. Ez az ajánlott hitelesítési módszer.

### <a name="manage-your-azure-ad-app-and-secret"></a>Az Azure AD-alkalmazás és a titkos kód kezelése

A **HRE-alkalmazás és a titkos kód kezelése** szakasz lehetővé teszi, hogy új Azure ad-alkalmazást válasszon ki vagy hozzon létre, és létrehoz egy titkos kulcsot. Biztonsági okokból a titkos kód nem jeleníthető meg a panel bezárása után. Az alkalmazás az alkalmazás AZONOSÍTÓját és a titkos kulcsot használja a Media Services érvényes jogkivonat beszerzéséhez.

Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel az alkalmazás Azure AD-bérlőben való regisztrálásához és az alkalmazás az Azure-előfizetésben lévő szerepkörhöz való hozzárendeléséhez. További információ: [szükséges engedélyek](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

A **kapcsolódás Media Services API** -hoz a szolgáltatás egyszerű alkalmazásának összekapcsolásához használt értékeket adja meg. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

## <a name="user-authentication"></a>Felhasználóhitelesítés

Ezzel a beállítással lehet hitelesíteni egy alkalmazottat vagy egy olyan Azure Active Directory tagját, aki egy alkalmazást használ az Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a felhasználó hitelesítő adatainak megadására. Ezt a hitelesítési módszert csak felügyeleti alkalmazásokhoz lehet használni.

### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

Másolja a hitelesítő adatait a felhasználói alkalmazás összekapcsolásához a **kapcsolódás Media Services API-hoz** szakaszhoz. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltésével a fiókjába](media-services-portal-upload-files.md).
