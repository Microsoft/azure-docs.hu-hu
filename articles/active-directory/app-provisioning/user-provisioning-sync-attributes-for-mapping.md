---
title: Attribútumok szinkronizálása Azure Active Directory leképezéshez
description: Ha a felhasználókiépítést Azure Active Directory SaaS-alkalmazásokkal konfigurálja, a címtárbővítmény funkcióval olyan forrásattribútumokat adhat hozzá, amelyek alapértelmezés szerint nincsenek szinkronizálva.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388210"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Bővítményattribútumok szinkronizálása alkalmazáskiépítéshez

Azure Active Directory (Azure AD) szolgáltatásnak tartalmaznia kell a felhasználói profil létrehozásához szükséges összes adatot (attribútumot), amikor felhasználói fiókokat létesít az Azure AD-ból egy [SaaS-alkalmazásba.](../saas-apps/tutorial-list.md) A felhasználóátépítés attribútumleképezésének testreszabásakor előfordulhat, hogy a leképezni kívánt attribútum nem jelenik meg a **Forrásattribútum listában.** Ez a cikk bemutatja, hogyan adjuk hozzá a hiányzó attribútumot.

Csak az Azure AD-beli felhasználók számára hozhat létre sémabővítményeket [a PowerShell vagy a Microsoft Graph.](#create-an-extension-attribute-on-a-cloud-only-user)

A felhasználói helyi Active Directory szinkronizálni kell a felhasználókat az Azure AD-be. A felhasználók és attribútumok szinkronizálása a [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md) Azure AD Connect szinkronizál bizonyos attribútumokat az Azure AD-be, de nem minden attribútumot. Emellett előfordulhat, hogy egyes alapértelmezés szerint szinkronizált attribútumok (például a SAMAccountName) nincsenek elérhetővé téve az Azure AD Graph API. Ezekben az esetekben a címtárbővítmény Azure AD Connect szinkronizálhatja az attribútumot az [Azure AD-be.](#create-an-extension-attribute-using-azure-ad-connect) Így az attribútum látható lesz az Azure AD-Graph API az Azure AD kiépítési szolgáltatás számára.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Bővítményattribútum létrehozása csak felhőbeli felhasználón
A felhasználói Microsoft Graph és a PowerShell használatával kiterjesztheti a felhasználói sémát az Azure AD-beli felhasználókra. Ezek a bővítményattribútumok a legtöbb esetben automatikusan felderítve vannak.

Ha több mint 1000 szolgáltatásnévvel van, hiányzó bővítményeket találhat a forrásattribútumok listájában. Ha egy létrehozott attribútum nem jelenik meg automatikusan, ellenőrizze, hogy létrejött-e az attribútum, és adja hozzá manuálisan a sémához. A létrehozás ellenőrzéséhez használja a Microsoft Graph [és a Graph Explorert.](/graph/graph-explorer/graph-explorer-overview) Ha manuálisan hozzá szeretne adni a sémához, tekintse meg a támogatott [attribútumok listájának szerkesztését.](customize-application-attributes.md#editing-the-list-of-supported-attributes)

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Bővítményattribútum létrehozása csak felhőbeli felhasználón a Microsoft Graph
Az Azure AD-felhasználók sémáját a következővel [bővítheti Microsoft Graph.](/graph/overview) 

Először sorolja fel a bérlőben található alkalmazásokat, hogy le tudja szerezni a használt alkalmazás azonosítóját. További információ: [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Ezután hozza létre a bővítményattribútumot. Cserélje le **az alábbi ID** tulajdonságot az **előző** lépésben lekért azonosítóra. Az **"ID"** attribútumot kell használnia, és nem az "appId"-t. További információ: [ExtensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

Az előző kérés létrehozott egy kiterjesztésattribútumot a következő formátumban: `extension_appID_extensionName` . Ezzel a bővítményattribútummal frissítheti a felhasználót. További információ: [Felhasználó frissítése.](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Végül ellenőrizze a felhasználó attribútumát. További tudnivalókért lásd: [Felhasználó lekérte.](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Bővítményattribútum létrehozása csak felhőbeli felhasználón a PowerShell használatával
Hozzon létre egy egyéni bővítményt a PowerShell használatával, és rendeljen hozzá egy értéket egy felhasználóhoz. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Bővítményattribútum létrehozása a Azure AD Connect

1. Nyissa meg a Azure AD Connect varázslót, válassza a Feladatok, majd a **Szinkronizálási beállítások testreszabása lehetőséget.**

   ![Azure Active Directory Connect varázsló További feladatok lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Jelentkezzen be Azure AD globális rendszergazdaként. 

3. A Választható **szolgáltatások lapon** válassza a **Címtárbővítmény-attribútumok szinkronizálása lehetőséget.**
 
   ![Azure Active Directory Connect varázsló Választható szolgáltatások lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Válassza ki az Azure AD-be kiterjeszteni kívánt attribútum(ak)t.
   > [!NOTE]
   > A Rendelkezésre álló **attribútumok alatt a** keresés megkülönbözteti a kis- és nagybetűket.

   ![A "Címtárbővítmények" kiválasztási oldalt bemutató képernyőkép](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Fejezze be Azure AD Connect varázslót, és engedélyezze a teljes szinkronizálási ciklus futtatását. A ciklus befejeződése után a rendszer kiterjeszti a sémát, és szinkronizálja az új értékeket a helyszíni AD és az Azure AD között.
 
6. A Azure Portal a felhasználói attribútumleképezések szerkesztése közben a **Forrásattribútum** lista a hozzáadott attribútumot fogja tartalmazni [](customize-application-attributes.md)formátumban. `<attributename> (extension_<appID>_<attributename>)` Válassza ki az attribútumot, és leképezi a kiépítés célalkalmazásához.

   ![Azure Active Directory Connect varázsló Címtárbővítmények kiválasztási oldala](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A helyszíni AD-ről ( például **managedby** vagy **DN/DistinguishedName)** származó referenciaattribútumok kiépítése jelenleg nem támogatott. Ezt a funkciót a User Voice oldalon [kérheti.](https://feedback.azure.com/forums/169401-azure-active-directory) 


## <a name="next-steps"></a>Következő lépések

* [Annak meghatározása, hogy ki van a kiépítés hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
