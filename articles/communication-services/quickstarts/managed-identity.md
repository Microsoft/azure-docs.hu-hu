---
title: Felügyelt identitások használata a kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: A felügyelt identitások lehetővé teszik az Azure kommunikációs szolgáltatások elérésének engedélyezését az Azure-beli virtuális gépeken, a Function apps-ben és más erőforrásokon futó alkalmazásokból.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307439"
---
# <a name="use-managed-identities"></a>Felügyelt identitások használata
Az Azure kommunikációs szolgáltatások használatának első lépései felügyelt identitások használatával. A kommunikációs szolgáltatások identitása és az SMS SDK-k támogatják a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md).

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a hozzáférést a felügyelt identitásokat támogató Azure-környezetből származó identitáshoz és SMS SDK-hoz. Azt is leírja, hogyan tesztelheti a kódot egy fejlesztési környezetben.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free)
- Aktív Azure kommunikációs szolgáltatások erőforrás: [hozzon létre egy kommunikációs szolgáltatások erőforrást](./create-communication-resource.md) , ha még nem rendelkezik ilyennel.
- SMS küldéséhez [telefonszámra](./telephony-sms/get-phone-number.md)lesz szüksége.
- A telepítő felügyelt identitása egy fejlesztési környezethez: [hozzáférés engedélyezése felügyelt identitással](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szerepköralapú hozzáférés-vezérléséről](../../../articles/role-based-access-control/index.yml)
- [További információ a .NET-hez készült Azure Identity Library-ről](/dotnet/api/overview/azure/identity-readme)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [SMS küldése](../quickstarts/telephony-sms/send.md)
- [További információ az SMS-ről](../concepts/telephony-sms/concepts.md)
