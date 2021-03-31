---
title: API-összekötő kód minták felhasználói folyamatokhoz – Azure AD
description: A Azure Active Directory külső identitások önkiszolgáló bejelentkezési folyamataiban található API-összekötők kódjának mintái.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87905906"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Külső identitások önkiszolgáló regisztrációjának mintái

Az alábbi táblázatok a webes API-k önkiszolgáló bejelentkezési felhasználói folyamatokban [API-összekötők](api-connectors-overview.md)használatával történő kihasználására szolgáló kód-példákra mutató hivatkozásokat tartalmaznak.

## <a name="api-connector-azure-function-quickstarts"></a>API-összekötő Azure-függvény rövid útmutatói

| Sample                                                                                                                          | Leírás                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ez a .NET Core Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációt adott bérlői tartományokra, és érvényesíteni a felhasználó által megadott adatokat. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ez a Node.js Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációkat adott bérlői tartományokra, és érvényesíteni a felhasználó által megadott adatokat.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ez a Python Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációkat adott bérlői tartományokra, és érvényesíteni a felhasználó által megadott adatokat.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Egyéni jóváhagyási munkafolyamatok

| Sample | Leírás |
|--------| ----------- |
| [Manuális jóváhagyási munkafolyamat](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Ez a minta egy végpontok közötti jóváhagyási munkafolyamatot mutat be a vendég felhasználói fiókok létrehozásához önkiszolgáló regisztráció során |

## <a name="identity-verification"></a>Személyazonosság ellenőrzése

| Sample                                                                                                            | Leírás                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Ez a minta bemutatja, hogyan ellenőrizhető egy felhasználói identitás az önkiszolgáló regisztráció részeként egy API-összekötővel a IDology-vel való integrációhoz. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Ez a minta bemutatja, hogyan ellenőrizhető egy felhasználói identitás az önkiszolgáló regisztráció részeként egy API-összekötővel a Experian-vel való integrációhoz. |
