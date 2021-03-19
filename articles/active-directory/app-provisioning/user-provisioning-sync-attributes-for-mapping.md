---
title: Attribútumok szinkronizálása az Azure AD-vel leképezéshez
description: Ha az SaaS-alkalmazásokhoz konfigurálja a felhasználók üzembe helyezését, a címtár-kiterjesztési funkcióval adhat hozzá olyan forrás-attribútumokat, amelyek alapértelmezés szerint nincsenek szinkronizálva.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579433"
---
# <a name="syncing-extension-attributes-attributes"></a>A bővítmény attribútumai attribútumainak szinkronizálása

Ha testreszabja a felhasználók kiosztásához szükséges attribútum-hozzárendeléseket, akkor előfordulhat, hogy a leképezni kívánt attribútum nem jelenik meg a **forrás attribútum** listában. Ebből a cikkből megtudhatja, hogyan adhatja hozzá a hiányzó attribútumot úgy, hogy szinkronizálja azt a helyszíni Active Directory (AD)-ből Azure Active Directory (Azure AD) vagy az Azure AD-ban lévő bővítmény-attribútumok létrehozásával a Felhőbeli felhasználók számára. 

Az Azure AD-nek tartalmaznia kell az összes olyan adatforrást, amely felhasználói profil létrehozásához szükséges az Azure AD-ből egy SaaS-alkalmazásba való kiépítés során. Bizonyos esetekben előfordulhat, hogy az adatok elérhetővé tételéhez szükség lehet az attribútumok szinkronizálására a helyszíni AD-ből az Azure AD-be. Azure AD Connect automatikusan szinkronizál bizonyos attribútumokat az Azure AD-be, de nem minden attribútumot. Emellett előfordulhat, hogy az alapértelmezés szerint szinkronizált attribútumok (például SAMAccountName) nem érhetők el az Azure AD Graph API használatával. Ezekben az esetekben a Azure AD Connect Directory-bővítmény funkcióval szinkronizálhatja az attribútumot az Azure AD-vel. Így az attribútum látható lesz az Azure AD Graph API és az Azure AD kiépítési szolgáltatása számára. Ha az üzembe helyezéshez szükséges adatmennyiség Active Directory, de a fent ismertetett okok miatt nem érhető el a kiépítés során, akkor a Azure AD Connect használatával hozhat létre bővítmény-attribútumokat. 

A legtöbb felhasználó valószínűleg a Active Directory-ból szinkronizált hibrid felhasználók számára is létrehozhat bővítményeket a csak felhőalapú felhasználók számára, Azure AD Connect használata nélkül is. A PowerShell vagy a Microsoft Graph használatával kiterjesztheti egy csak Felhőbeli felhasználó sémáját. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Extension attribútum létrehozása csak Felhőbeli felhasználónál
Az ügyfelek a Microsoft Graph és a PowerShell használatával bővíthetik a felhasználói sémát. Ezek a bővítmény-attribútumok a legtöbb esetben automatikusan fel lesznek derítve, de a több mint 1000 egyszerű szolgáltatással rendelkező ügyfelek megtalálják a forrás attribútum listában hiányzó bővítményeket. Ha az alábbi lépésekkel létrehozott attribútumok nem jelennek meg automatikusan a forrás attribútum listában, ellenőrizze, hogy a Graph használatával sikeresen létrejött-e a bővítmény attribútuma, majd adja hozzá [manuálisan](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes)a sémához. Ha az alábbi gráf-kérelmeket végzi, kattintson a továbbiak gombra a kérések elvégzéséhez szükséges engedélyek ellenőrzéséhez. A kérelmeket a [Graph Explorer](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) használatával teheti meg. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Bővítmény-attribútum létrehozása csak Felhőbeli felhasználóhoz Microsoft Graph használatával
A felhasználók sémájának kiterjesztéséhez egy alkalmazást kell használnia. Sorolja fel a bérlőben lévő alkalmazásokat annak az alkalmazásnak a azonosításához, amelyet a felhasználói séma kibővítéséhez használni kíván. [Részletek](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Hozza létre a kiterjesztés attribútumot. Cserélje le az alábbi **ID** tulajdonságot az előző lépésben lekért **azonosítóra** . Az **"id"** attribútumot kell használnia, nem a "AppID". [Részletek](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

Az előző kérelem létrehozta a "extension_appID_extensionName" formátumú kiterjesztési attribútumot. Egy felhasználó frissítése a Extension attribútummal. [Részletek](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Ellenőrizze, hogy a felhasználó sikeresen frissítette-e az attribútumot. [Részletek](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Következő lépések

* [A kiépítés hatókörében lévő felhasználók meghatározása](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
