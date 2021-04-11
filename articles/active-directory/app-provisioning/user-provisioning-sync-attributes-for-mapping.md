---
title: Attribútumok szinkronizálása Azure Active Directory megfeleltetéshez
description: Ha Azure Active Directory-és SaaS-alkalmazásokkal konfigurálja a felhasználók kiosztását, akkor a címtár-bővítmény szolgáltatással adhat hozzá olyan forrás-attribútumokat, amelyek alapértelmezés szerint nincsenek szinkronizálva.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120792"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Az alkalmazás kiépítés bővítmény-attribútumainak szinkronizálása

Azure Active Directory (Azure AD) tartalmaznia kell az összes olyan adatforrást (attribútumot), amely felhasználói profil létrehozásához szükséges ahhoz, hogy a felhasználói fiókokat az Azure AD-ből egy [SaaS-alkalmazásba](../saas-apps/tutorial-list.md)lehessen kiépíteni. Ha testreszabja a felhasználók kiosztásához szükséges attribútum-hozzárendeléseket, akkor előfordulhat, hogy a leképezni kívánt attribútum nem jelenik meg a **forrás attribútum** listában. Ez a cikk bemutatja, hogyan adhatja hozzá a hiányzó attribútumot.

Az Azure AD-ben csak a felhasználók [hozhatnak létre sémákat a PowerShell vagy a Microsoft Graph használatával](#create-an-extension-attribute-on-a-cloud-only-user).

A helyszíni Active Directoryban lévő felhasználók számára szinkronizálnia kell a felhasználókat az Azure AD-vel. A felhasználók és az attribútumok szinkronizálása [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md)használatával végezhető el. Azure AD Connect automatikusan szinkronizál bizonyos attribútumokat az Azure AD-be, de nem minden attribútumot. Emellett előfordulhat, hogy az alapértelmezés szerint szinkronizált attribútumok (például SAMAccountName) nem érhetők el az Azure AD Graph API használatával. Ezekben [az esetekben a Azure ad Connect Directory-bővítmény funkcióval szinkronizálhatja az attribútumot az Azure ad-vel](#create-an-extension-attribute-using-azure-ad-connect). Így az attribútum látható lesz az Azure AD Graph API és az Azure AD kiépítési szolgáltatása számára.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Extension attribútum létrehozása csak Felhőbeli felhasználónál
A Microsoft Graph és a PowerShell használatával kiterjesztheti a felhasználói sémát a felhasználók számára az Azure AD-ben. Ezek a bővítmény-attribútumok a legtöbb esetben automatikusan fel lesznek derítve.

Ha több mint 1000 egyszerű szolgáltatást használ, előfordulhat, hogy a forrás attribútum listában hiányzó bővítmények találhatók. Ha egy létrehozott attribútum nem jelenik meg automatikusan, ellenőrizze, hogy létrejött-e az attribútum, és adja hozzá manuálisan a sémához. Annak ellenőrzéséhez, hogy létrejött-e, használja a Microsoft Graph és a [Graph Explorert](/graph/graph-explorer/graph-explorer-overview.md). Ha manuálisan szeretné hozzáadni a sémához, tekintse meg [a támogatott attribútumok listájának szerkesztésével](customize-application-attributes.md#editing-the-list-of-supported-attributes)foglalkozó témakört.

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Bővítmény-attribútum létrehozása csak Felhőbeli felhasználóhoz Microsoft Graph használatával
[Microsoft Graph](/graph/overview.md)használatával kiterjesztheti az Azure ad-felhasználók sémáját. 

Először sorolja fel a bérlőben lévő alkalmazásokat annak az alkalmazásnak az AZONOSÍTÓjának lekéréséhez, amelyen dolgozik. További információt a [ExtensionProperties listázása](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)című témakörben talál.

```json
GET https://graph.microsoft.com/v1.0/applications
```

Ezután hozza létre a kiterjesztés attribútumot. Cserélje le az alábbi **ID** tulajdonságot az előző lépésben lekért **azonosítóra** . Az **"id"** attribútumot kell használnia, nem a "AppID". További információ: [Create extensionProperty]/Graph/API/Application-post-extensionproperty.MD? View = Graph-Rest-1.0&tabs = http&megőrzése-View = true).

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

Az előző kérelem létrehozott egy kiterjesztési attribútumot a formátummal `extension_appID_extensionName` . Most már frissítheti ezt a kiterjesztési attribútummal rendelkező felhasználót is. További információ: [Update User (felhasználó frissítése](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true)).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Végezetül ellenőrizze a felhasználó attribútumát. További információért lásd: [felhasználó beolvasása](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Bővítmény-attribútum létrehozása csak Felhőbeli felhasználó számára a PowerShell használatával
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

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Bővítmény-attribútum létrehozása Azure AD Connect használatával

1. Nyissa meg a Azure AD Connect varázslót, válassza a feladatok, majd a **szinkronizálási beállítások testreszabása lehetőséget**.

   ![Azure Active Directory Connect varázsló további feladatok lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Jelentkezzen be Azure AD globális rendszergazdaként. 

3. A **választható szolgáltatások** lapon válassza a **címtár-kiterjesztés attribútumának szinkronizálása** lehetőséget.
 
   ![Azure Active Directory Connect varázsló választható funkciók lapja](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Válassza ki az Azure AD-ra kiterjeszteni kívánt attribútum (oka) t.
   > [!NOTE]
   > A Keresés az **elérhető attribútumok** területen kis-és nagybetűket érint.

   ![A "Directory-bővítmények" kiválasztási lapot megjelenítő képernyőkép](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Fejezze be a Azure AD Connect varázslót, és engedélyezze a teljes szinkronizálási ciklus futtatását. A ciklus befejezésekor a séma ki van bővítve, és a rendszer szinkronizálja az új értékeket a helyszíni AD és az Azure AD között.
 
6. Ha a Azure Portal a [felhasználói attribútumok leképezéseit szerkeszti](customize-application-attributes.md), a **forrás attribútum** lista mostantól a hozzáadott attribútumot fogja tartalmazni a formátumban `<attributename> (extension_<appID>_<attributename>)` . Válassza ki az attribútumot, és rendelje hozzá a kiépítés céljának alkalmazásához.

   ![Azure Active Directory Connect varázsló Directory-bővítmények kiválasztási lapja](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A helyszíni AD-ből (például **többé** vagy **DN/DistinguishedName**) származó hivatkozási attribútumok kiépítésének lehetősége jelenleg nem támogatott. Ezt a funkciót [felhasználói hangon](https://feedback.azure.com/forums/169401-azure-active-directory)is kérheti. 


## <a name="next-steps"></a>Következő lépések

* [A kiépítés hatókörében lévő felhasználók meghatározása](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
