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
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492353"
---
# <a name="use-managed-identities"></a>Felügyelt identitások használata
Az Azure kommunikációs szolgáltatások használatának első lépései felügyelt identitások használatával. A kommunikációs szolgáltatások identitás-és SMS-ügyféloldali kódtárai támogatják a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md).

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti az identitás-és SMS-ügyféloldali kódtárak hozzáférését egy olyan Azure-környezetből, amely támogatja a felügyelt identitásokat. Azt is leírja, hogyan tesztelheti a kódot egy fejlesztési környezetben.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szerepköralapú hozzáférés-vezérléséről](../../../articles/role-based-access-control/index.yml)
- [További információ a .NET-hez készült Azure Identity Library-ről](/dotnet/api/overview/azure/identity-readme)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [SMS küldése](../quickstarts/telephony-sms/send.md)
- [További információ az SMS-ről](../concepts/telephony-sms/concepts.md)
