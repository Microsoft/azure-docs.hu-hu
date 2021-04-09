---
title: Webes API-kat meghívó Daemon-alkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó Daemon-alkalmazást
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 00a70b585ddf522a25e81703fe5bdf55efbcb7e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582774"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Forgatókönyv: a webes API-kat meghívó alkalmazás

A webes API-kat meghívó Daemon-alkalmazások létrehozásához szükséges tudnivalók.

## <a name="overview"></a>Áttekintés

Az alkalmazás képes jogkivonatot beszerezni egy webes API saját nevében történő meghívására (nem a felhasználó nevében). Ez a forgatókönyv a Daemon-alkalmazások esetében hasznos. A standard OAuth 2,0 ügyfél- [hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) engedélyezését használja.

![Démonalkalmazások](./media/scenario-daemon-app/daemon-app.svg)

Íme néhány példa a Daemon-alkalmazások használati eseteire:

- A felhasználók üzembe helyezéséhez vagy felügyeletéhez, illetve a Batch-folyamatok címtárban való végrehajtásához használt webalkalmazások
- Asztali alkalmazások (például Windows-szolgáltatások Windows-vagy Daemon-folyamatokhoz Linux rendszeren), amely batch-feladatokat hajt végre, vagy a háttérben futó operációsrendszer-szolgáltatás.
- Webes API-k, amelyeknek a címtárakat kell kezelniük, nem meghatározott felhasználóknak

Van egy másik gyakori eset, amikor a nem Daemon-alkalmazások az ügyfél hitelesítő adatait használják: még akkor is, ha a felhasználók nevében cselekszenek, a webes API-t vagy az erőforrást a saját identitásuk alapján, technikai okokból kell elérniük. Ilyen például a Azure Key Vault titkos kulcsaihoz való hozzáférés, vagy a gyorsítótár Azure SQL Database.

Olyan alkalmazások, amelyek jogkivonatot szerzik be a saját identitásuk számára:

- Bizalmas ügyfélalkalmazások. Ezek az alkalmazások, mivel a felhasználóktól függetlenül férnek hozzá az erőforrásokhoz, bizonyítaniuk kell identitásukat. Ők is eléggé bizalmas alkalmazások. A Azure Active Directory (Azure AD) bérlői rendszergazdáinak jóvá kell hagyniuk.
- Titkos (alkalmazás jelszava vagy tanúsítvány) van regisztrálva az Azure AD-ben. Ezt a titkot a rendszer átadja az Azure AD-nek a jogkivonat beszerzésére irányuló hívásakor.

## <a name="specifics"></a>Sajátosságai

> [!IMPORTANT]
>
> - A felhasználók nem tudnak kommunikálni egy démon alkalmazással. Egy démon-alkalmazáshoz saját identitás szükséges. Az ilyen típusú alkalmazás hozzáférési jogkivonatot kér az alkalmazás identitásával, és megjeleníti az alkalmazás AZONOSÍTÓját, hitelesítő adatait (jelszavát vagy tanúsítványát), valamint az alkalmazás AZONOSÍTÓjának URI-JÁT az Azure AD-hez. A sikeres hitelesítés után a démon egy hozzáférési jogkivonatot (és egy frissítési jogkivonatot) kap a Microsoft Identity platformból. Ezt a tokent a rendszer a webes API meghívására használja (és szükség szerint frissíti).
> - Mivel a felhasználók nem tudnak kommunikálni a Daemon-alkalmazásokkal, a növekményes beleegyező engedély nem lehetséges. Az összes szükséges API-engedélyt be kell állítani az alkalmazás regisztrálásakor. Az alkalmazás kódja csak statikusan meghatározott engedélyeket kér. Ez azt is jelenti, hogy a Daemon-alkalmazások nem támogatják a növekményes hozzájárulásukat.

A fejlesztők számára a forgatókönyv teljes körű tapasztalata a következő szempontokat öleli fel:

- A Daemon-alkalmazások csak az Azure AD-bérlők számára működhetnek. Nem érdemes olyan Daemon-alkalmazást létrehozni, amely megkísérli a személyes Microsoft-fiókok kezelését. Ha Ön üzletági (LOB) alkalmazás fejlesztője, a démoni alkalmazást a bérlőben hozza létre. Ha Ön ISV-t használ, érdemes lehet több-bérlős démon alkalmazást létrehoznia. Minden bérlői rendszergazdának meg kell adnia a beleegyező jogosultságokat.
- Az [alkalmazás regisztrálása](./scenario-daemon-app-registration.md)során a válasz URI-ja nem szükséges. Megoszthatja a titkos kulcsokat vagy tanúsítványokat vagy aláírt állításokat az Azure AD-vel. Emellett meg kell adnia az alkalmazás engedélyeit, és rendszergazdai jóváhagyást kell adnia az alkalmazások használatához.
- Az [alkalmazás konfigurációjának](./scenario-daemon-app-configuration.md) az Azure ad-ben megosztott ügyfél-hitelesítő adatokat kell megadnia az alkalmazás regisztrációja során.
- Az ügyfél hitelesítő adataival rendelkező token beszerzéséhez használt [hatókörnek](scenario-daemon-acquire-token.md#scopes-to-request) statikus hatókörre van szüksége.

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](./scenario-daemon-app-registration.md)ismerteti.
