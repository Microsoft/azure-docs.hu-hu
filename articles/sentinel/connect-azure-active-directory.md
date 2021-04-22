---
title: Adatok Azure Active Directory csatlakoztatása Azure Sentinel | Microsoft Docs
description: Megtudhatja, hogyan gyűjthet adatokat a Azure Active Directory, és hogyan streamelhet Azure AD bejelentkezési, naplózási és kiépítési naplókat a Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872370"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Összekapcsolhatja Azure Active Directory (Azure AD)-adatokat a Azure Sentinel

A Azure Sentinel összekötővel adatokat gyűjthet a Azure Active Directory [](../active-directory/fundamentals/active-directory-whatis.md) és streamelheti őket a Azure Sentinel. Az összekötő lehetővé teszi a következő naplótípusok streamelését:

- [**Bejelentkezési naplók,**](../active-directory/reports-monitoring/concept-all-sign-ins.md)amelyek információkat tartalmaznak az [interaktív](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) felhasználói bejelentkezésekkel kapcsolatban, ahol a felhasználó hitelesítési tényezőt biztosít.

    Az Azure AD-összekötő mostantól a bejelentkezési naplók következő három további kategóriáját tartalmazza, amelyek jelenleg előzetes verzióban **érhetőek el:**
    
    - [**Nem interaktív felhasználói**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)bejelentkezési naplók, amelyek az ügyfél által a felhasználó nevében, a felhasználó beavatkozása vagy hitelesítési tényezője nélkül végzett bejelentkezésekkel kapcsolatos információkat tartalmaznak.
    
    - [**A szolgáltatásnév bejelentkezési naplói,**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)amelyek a felhasználókkal nem kapcsolatos alkalmazások és szolgáltatásnév-bejelentkezések adatait tartalmazzák. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás hitelesítő adatokat ad meg a saját nevében az erőforrások hitelesítéséhez vagy eléréséhez.
    
    - [**Felügyelt identitás bejelentkezési naplói,**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)amelyek az Azure által felügyelt titkos adatokat tartalmazó Azure-erőforrások bejelentkezéseiről tartalmaznak adatokat. További információ: Mik azok az [Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Auditnaplók,**](../active-directory/reports-monitoring/concept-audit-logs.md)amelyek a felhasználó- és csoportkezelésre, a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozó rendszertevékenységekkel kapcsolatos információkat tartalmaznak.

- [**Kiépítési naplók**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (szintén **előzetes** verzióban), amelyek az Azure AD kiépítési szolgáltatás által kiépített felhasználókra, csoportokra és szerepkörökre vonatkozó rendszertevékenység-információkat tartalmaznak. 

> [!IMPORTANT]
> Ahogy azt fent jelezték, az elérhető naplótípusok némelyike jelenleg előzetes verzióban **érhető el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy más, általánosan elérhető Azure-funkciókra vonatkozó további jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.
## <a name="prerequisites"></a>Előfeltételek

- A Azure Active Directory P1 vagy P2 licenc szükséges a bejelentkezési naplóknak az Azure Sentinel. Bármely Azure AD-licenc (Ingyenes/O365/P1/P2) elegendő a többi naplótípus be számára. További gigabájtonkénti díjakat lehet fizetni a Azure Monitor (Log Analytics) és a Azure Sentinel.

- A felhasználóhoz hozzá kell rendelni Azure Sentinel közreműködői szerepkört a munkaterületen.

- A felhasználónak globális rendszergazdai vagy biztonsági rendszergazdai szerepkört kell hozzárendelnie ahhoz a bérlőhöz, amelyről a naplókat streamelni szeretné.

- A felhasználónak olvasási és írási engedéllyel kell rendelkeznie az Azure AD diagnosztikai beállításaihoz, hogy látható legyen a kapcsolat állapota. 

## <a name="connect-to-azure-active-directory"></a>Csatlakozás Azure Active Directory

1. A Azure Sentinel navigációs **menüben** válassza az Adat-összekötők lehetőséget.

1. Az adat-összekötők katalógusában válassza **a** Azure Active Directory majd az **Összekötő oldalának megnyitása lehetőséget.**

1. Jelölje be a Azure Sentinel naplóba streamelni kívánt naplótípusok melletti jelölőnégyzeteket (lásd fent), majd kattintson a **Csatlakozás gombra.**

## <a name="find-your-data"></a>Az adatok megkeresása

A sikeres kapcsolat létrejötte után az adatok a Naplók területen, a **LogManagement** szakaszban, a következő táblázatokban jelennek meg:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Az Azure AD-naplók lekérdezéséhez írja be a megfelelő táblanevet a lekérdezési ablak tetején.

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathat Azure Active Directory Azure Sentinel. A további Azure Sentinel a következő cikkekben talál:
- Megtudhatja, hogyan [olvashatja be az adatokat és a lehetséges fenyegetéseket.](quickstart-get-visibility.md)
- A fenyegetések [észlelésének első Azure Sentinel.](tutorial-detect-threats-built-in.md)
