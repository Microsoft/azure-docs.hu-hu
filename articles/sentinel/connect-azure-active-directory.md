---
title: Azure Active Directory-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan gyűjthet adatokat Azure Active Directoryről, és hogyan továbbíthatja az Azure AD-bejelentkezést, a naplózást és a naplókat az Azure Sentinelbe.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99251981"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure Active Directory-(Azure AD-) adatkapcsolatok összekapcsolhatók az Azure Sentinel szolgáltatással

Az Azure Sentinel beépített összekötője segítségével adatokat gyűjthet a [Azure Active Directoryről](../active-directory/fundamentals/active-directory-whatis.md) , és továbbíthatja azt az Azure sentinelbe. Az összekötő lehetővé teszi a következő típusú naplók továbbítását:

- [**Bejelentkezési naplók**](../active-directory/reports-monitoring/concept-all-sign-ins.md), amelyek olyan [interaktív felhasználói bejelentkezésekkel](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) kapcsolatos információkat tartalmaznak, amelyekben a felhasználó hitelesítési tényezőt biztosít.

    Az Azure AD-összekötő immár a bejelentkezési naplók következő három további kategóriáját tartalmazza, melyek mindegyike jelenleg **előzetes** verzióban érhető el:
    
    - [**Nem interaktív felhasználói bejelentkezési naplók**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), amelyek olyan információkat tartalmaznak, amelyek a felhasználó nevében egy ügyfél által végzett bejelentkezésekhez szükségesek, a felhasználótól származó interakció vagy hitelesítési tényező nélkül.
    
    - [**Egyszerű bejelentkezési naplók**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), amelyek olyan alkalmazások és egyszerű szolgáltatások bejelentkezési adatait tartalmazzák, amelyek nem tartalmaznak felhasználókat. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás hitelesítő adatokat szolgáltat a saját nevében az erőforrások hitelesítéséhez vagy eléréséhez.
    
    - [**Felügyelt identitású bejelentkezési naplók**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), amelyek az Azure által felügyelt titkos kulcsokkal rendelkező Azure-erőforrások bejelentkezési adatait tartalmazzák. További információ: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Naplók**](../active-directory/reports-monitoring/concept-audit-logs.md), amelyek a felhasználók és a csoportok felügyeletére, a felügyelt alkalmazásokra és a címtárbeli tevékenységekre vonatkozó rendszertevékenységgel kapcsolatos információkat tartalmaznak.

- Az Azure AD kiépítési szolgáltatás által kiépített felhasználókra, csoportokra és szerepkörökre vonatkozó rendszertevékenységi adatokat tartalmazó [**naplók üzembe**](../active-directory/reports-monitoring/concept-provisioning-logs.md) helyezése ( **előzetes** verzióban is). 

> [!IMPORTANT]
> A fentiekben leírtak szerint néhány elérhető naplózási típus jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.
## <a name="prerequisites"></a>Előfeltételek

- Minden Azure AD-licenc (ingyenes/O365/P1/P2) elegendő a bejelentkezési naplók Azure Sentinelbe való betöltéséhez. A Azure Monitor (Log Analytics) és az Azure Sentinel további GB-os díjat is igényelhet.

- A felhasználónak hozzá kell rendelnie az Azure Sentinel közreműködő szerepkört a munkaterületen.

- A felhasználónak hozzá kell rendelnie a globális rendszergazdai vagy biztonsági rendszergazdai szerepköröket azon a bérlőn, amelyről a naplókat továbbítani kívánja.

- A felhasználónak olvasási és írási engedélyekkel kell rendelkeznie az Azure AD diagnosztikai beállításaihoz, hogy láthassa a kapcsolatok állapotát. 

## <a name="connect-to-azure-active-directory"></a>Kapcsolódás Azure Active Directoryhoz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Az adatösszekötők katalógusában válassza a **Azure Active Directory** lehetőséget, majd válassza az **összekötő lap megnyitása** lehetőséget.

1. Jelölje be az Azure Sentinelbe továbbítani kívánt naplók melletti jelölőnégyzeteket (lásd fent), majd kattintson a **Kapcsolódás** elemre.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a **LogManagement** szakaszban jelenik meg, az alábbi táblázatokban:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Az Azure AD-naplók lekérdezéséhez írja be a megfelelő táblanév nevet a lekérdezési ablak tetején.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Active Directory az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
