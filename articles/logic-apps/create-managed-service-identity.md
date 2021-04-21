---
title: Hitelesítés felügyelt identitásokkal
description: Hozzáférés az Azure Active Directory által védett erőforrásokhoz anélkül, hogy hitelesítő adatokkal vagy titkos adatokat használ egy felügyelt identitással
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 8e081257d70c9bc9c9f75df18b30f8dcf119e48e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763342"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával a Azure Logic Apps

Az Azure Active Directory (Azure AD) által védett egyéb erőforrások egyszerű eléréséhez és az identitás hitelesítéséhez a logikai alkalmazás használhat felügyelt identitást [(korábban](../active-directory/managed-identities-azure-resources/overview.md) Felügyeltszolgáltatás-identitás vagy MSI) hitelesítő adatok, titkos kulcsok vagy Azure AD-jogkivonatok helyett. Az Azure kezeli Ön számára ezt az identitást, és segít megvédeni a hitelesítő adatokat, mivel önnek nem kell titkos adatokat kezelnie, és nem kell közvetlenül Azure AD-jogkivonatokat használnia.

Azure Logic Apps a rendszer [*által hozzárendelt és*](../active-directory/managed-identities-azure-resources/overview.md) a [*felhasználó*](../active-directory/managed-identities-azure-resources/overview.md) által hozzárendelt felügyelt identitásokat is támogatja. A logikai alkalmazás vagy az egyes kapcsolatok a  rendszer által hozzárendelt identitást vagy egyetlen, felhasználó által hozzárendelt identitást használhatnak, amelyet megoszthat a logikai alkalmazások egy csoportjában, de nem mindkettőt.

<a name="triggers-actions-managed-identity"></a>

## <a name="where-can-logic-apps-use-managed-identities"></a>Hol használhatnak a logikai alkalmazások felügyelt identitásokat?

Jelenleg csak bizonyos [beépített eseményindítók](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) [](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) és műveletek, valamint az Azure AD OAuth-hitelesítést támogató meghatározott felügyelt összekötők használhatnak felügyelt identitást a hitelesítéshez. Itt például a következő kijelölést adhatja meg:

<a name="built-in-managed-identity"></a>

**Beépített eseményindítók és műveletek**

* Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* HTTP + Webhook

> [!NOTE]
> Bár a HTTP-eseményindító és -művelet a rendszer által hozzárendelt felügyelt identitással hitelesítheti a kapcsolatokat az Azure-tűzfalak mögötti Azure Storage-fiókokkal, nem használhatja a felhasználó által hozzárendelt felügyelt identitást ugyanazon kapcsolatok hitelesítésére.

<a name="managed-connectors-managed-identity"></a>

**Felügyelt összekötők**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Azure Resource Manager
* HTTP az Azure AD-vel

A felügyelt összekötők támogatása jelenleg előzetes verzióban érhető el. Az aktuális listát lásd: Hitelesítési típusok a hitelesítést támogató [eseményindítókhoz és műveletekhez.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

Ez a cikk bemutatja, hogyan állíthatja be a logikai alkalmazás mindkét típusú felügyelt identitását. További információt az alábbi témakörökben talál:

* [Felügyelt identitásokat támogató eseményindítók és műveletek](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [A logikai alkalmazások felügyelt identitásának korlátai](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Az Azure AD-hitelesítést felügyelt identitásokkal támogató Azure-szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). A felügyelt identitásnak és annak a cél Azure-erőforrásnak, ahol hozzá kell férnie, ugyanazt az Azure-előfizetést kell használnia.

* Ha felügyelt identitásnak szeretne hozzáférést adni egy Azure-erőforráshoz, hozzá kell adni egy szerepkört az identitás célerőforrásához. Szerepkörök hozzáadásához [Olyan Azure AD-rendszergazdai](../active-directory/roles/permissions-reference.md) engedélyekre van szükség, amelyek szerepköröket rendelhetnek a megfelelő Azure AD-bérlő identitásaihoz.

* Az elérni kívánt Cél Azure-erőforrás. Ezen az erőforráson hozzáad egy szerepkört a felügyelt identitáshoz, amely segít a logikai alkalmazásnak hitelesíteni a hozzáférést a célerőforráshoz.

* A logikai alkalmazás, amelyben használni szeretné az eseményindítót vagy a felügyelt identitásokat [támogató műveleteket.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

## <a name="enable-managed-identity"></a>Felügyelt identitás engedélyezése

A használni kívánt felügyelt identitás beállításához kövesse az identitásra mutató hivatkozást:

* [Rendszer által hozzárendelt identitás](#system-assigned)
* [Felhasználó által hozzárendelt identitás](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Rendszer által hozzárendelt identitás engedélyezése

A felhasználó által hozzárendelt identitásokkal ellentétben nem kell manuálisan létrehoznia a rendszer által hozzárendelt identitást. A logikai alkalmazás rendszer által hozzárendelt identitásának beállításához a következő lehetőségeket használhatja:

* [Azure Portal](#azure-portal-system-logic-app)
* [Azure Resource Manager-sablonok](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Rendszer által hozzárendelt identitás engedélyezése a Azure Portal

1. A [Azure Portal](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében a Beállítások **alatt válassza** az **Identitás lehetőséget.** Válassza **a Rendszer által**  >  **hozzárendelt lehetőséget a** Mentés  >  **gombra.** Amikor az Azure megerősítést kér, válassza az **Igen lehetőséget.**

   ![A rendszer által hozzárendelt identitás engedélyezése](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Ha hibaüzenetet kap, hogy csak egyetlen felügyelt identitással rendelkezik, a logikai alkalmazás már társítva van a felhasználó által hozzárendelt identitással. A rendszer által hozzárendelt identitás hozzáadása  előtt el kell távolítania a felhasználó által hozzárendelt identitást a logikai alkalmazásból.

   A logikai alkalmazás most már használhatja a rendszer által hozzárendelt identitást, amely regisztrálva van az Azure AD-ban, és objektumazonosítóval van ábrázolva.

   ![A rendszer által hozzárendelt identitás objektumazonosítója](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Objektumazonosító** | <*identity-resource-ID*> | Globálisan egyedi azonosító (GUID), amely a logikai alkalmazás rendszer által hozzárendelt identitását jelöli egy Azure AD-bérlőben |
   ||||

1. Most kövesse a [lépéseket, amelyek hozzáférést](#access-other-resources) adnak az identitásnak az erőforráshoz a témakör későbbi, későbbi, lépéseit követve.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Rendszer által hozzárendelt identitás engedélyezése Azure Resource Manager sablonban

Az Azure-erőforrások, például logikai alkalmazások létrehozásának és üzembe helyezésének automatizálását a következő [sablonokkal Azure Resource Manager:](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Ha engedélyezni szeretné a rendszer által hozzárendelt felügyelt identitást a logikai alkalmazáshoz a sablonban, adja hozzá az objektumot és a gyermektulajdonságot a logikai alkalmazás erőforrás-definíciójában `identity` `type` a sablonban, például:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Amikor az Azure létrehozza a logikai alkalmazás erőforrás-definícióját, az `identity` objektum a következő további tulajdonságokat kapja meg:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Tulajdonság (JSON) | Érték | Leírás |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID (rendszerbiztonsági tag azonosítója)*> | A logikai alkalmazást az Azure AD-bérlőben képviselő felügyelt identitás szolgáltatásnév-objektumának globálisan egyedi azonosítója (GUID). Ez a GUID néha "objektumazonosítóként" vagyként jelenik `objectID` meg. |
| `tenantId` | <*Azure-AD-bérlőazonosító*> | A globálisan egyedi azonosító (GUID), amely azt az Azure AD-bérlőt jelöli, amelyben a logikai alkalmazás most tag. Az Azure AD-bérlőn belül a szolgáltatásnév neve megegyezik a logikaialkalmazás-példány nevével. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás engedélyezése

Ha felhasználó által hozzárendelt felügyelt identitást kell beállítania a logikai alkalmazáshoz, először különálló Azure-erőforrásként kell létrehoznia ezt az identitást. A következő lehetőségeket használhatja:

* [Azure Portal](#azure-portal-user-identity)
* [Azure Resource Manager-sablonok](#template-user-identity)
* Azure PowerShell
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Szerepkör-hozzárendelés hozzáadása](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Szerepkör-hozzárendelés hozzáadása](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Szerepkör-hozzárendelés hozzáadása](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Felhasználó által hozzárendelt identitás létrehozása a Azure Portal

1. A [Azure Portal](https://portal.azure.com)bármelyik oldal keresőmezőjében adja meg a következőt: , majd `managed identities` válassza a Managed **Identities (Felügyelt identitások) lehetőséget.**

   ![Keresse meg és válassza a "Felügyelt identitások" lehetőséget](./media/create-managed-service-identity/find-select-managed-identities.png)

1. A **Felügyelt identitások alatt válassza** a Hozzáadás **lehetőséget.**

   ![Új felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Adja meg a felügyelt identitás adatait, majd válassza a **Felülvizsgálat + létrehozás** lehetőséget, például:

   ![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Yes | <*Azure-előfizetés neve*> | A használni használt Azure-előfizetés neve |
   | **Erőforráscsoport** | Yes | <*Azure-erőforráscsoport neve*> | A használni használt erőforráscsoport neve. Hozzon létre egy új csoportot, vagy válasszon ki egy meglévőt. Ez a példa egy új csoportot hoz létre `fabrikam-managed-identities-RG` névvel. |
   | **Régió** | Yes | <*Azure-régió*> | Az Azure-régió, ahol az erőforrással kapcsolatos információkat tárolni kell. Ez a példa az "USA nyugati régiója" régiót használja. |
   | **Név** | Yes | <*felhasználó által hozzárendelt-identitás-név*> | A felhasználó által hozzárendelt identitás nevére. Ez a példa a következőt használja: `Fabrikam-user-assigned-identity`. |
   |||||

   Az adatok ellenőrzése után az Azure létrehozza a felügyelt identitást. Most hozzáadhatja a felhasználó által hozzárendelt identitást a logikai alkalmazáshoz. A logikai alkalmazáshoz nem adhat hozzá egynél több felhasználó által hozzárendelt identitást.

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében a Beállítások alatt **válassza** az **Identitás** lehetőséget, majd a Felhasználó **által hozzárendelt Hozzáadás**  >  **lehetőséget.**

   ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. A Felhasználó **által hozzárendelt felügyelt**  identitás hozzáadása panel Előfizetés listájából válassza ki Azure-előfizetését, ha még nincs kiválasztva. Az előfizetésben  található összes felügyelt identitást felsoroló listában keresse meg és válassza ki a kívánt felhasználó által hozzárendelt identitást. A lista szűréséhez a **Felhasználó** által hozzárendelt felügyelt identitások keresőmezőbe írja be az identitás vagy az erőforráscsoport nevét. Ha végzett, válassza a Hozzáadás **lehetőséget.**

   ![Válassza ki a használni kívánt, felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, hogy csak egyetlen felügyelt identitással rendelkezik, a logikai alkalmazás már társítva van a rendszer által hozzárendelt identitással. A felhasználó által hozzárendelt identitás hozzáadása előtt le kell tiltania a rendszer által hozzárendelt identitást a logikai alkalmazásban.

   A logikai alkalmazás most már társítva van a felhasználó által hozzárendelt felügyelt identitással.

   ![Társítás felhasználó által hozzárendelt identitással](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Most kövesse a [lépéseket, amelyek hozzáférést](#access-other-resources) adnak az identitásnak az erőforráshoz a témakör későbbi, későbbi, lépéseit követve.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Felhasználó által hozzárendelt identitás létrehozása Azure Resource Manager sablonban

Az Azure-erőforrások, például logikai alkalmazások [létrehozásának](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)és üzembe helyezésének automatizálásához használhatja a Azure Resource Manager sablonokat, amelyek támogatják a felhasználó által hozzárendelt identitásokat [a hitelesítéshez.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) A sablon szakaszában a logikai alkalmazás erőforrás-definíciója a következő `resources` elemeket igényli:

* Egy `identity` objektum, amely `type` tulajdonsága a következőre van állítva: `UserAssigned`

* Egy `userAssignedIdentities` gyermekobjektum, amely megadja a felhasználó által hozzárendelt erőforrást és nevet

Ez a példa egy HTTP PUT-kérés logikaialkalmazás-erőforrás-definícióját mutatja be, és nem paraméteres objektumot `identity` tartalmaz. A PUT kérésre és az azt követő GET műveletre adott válasz is a következő `identity` objektummal van meg:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Ha a sablon a felügyelt identitás erőforrás-definícióját is tartalmazza, paraméteresre is használhatja az `identity` objektumot. Ez a példa bemutatja, hogyan hivatkozik a gyermekobjektum egy, a sablon szakaszában `userAssignedIdentities` `userAssignedIdentity` definiálható `variables` változóra. Ez a változó a felhasználó által hozzárendelt identitás erőforrás-azonosítójára hivatkozik.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Hozzáférés az identitás számára az erőforrásokhoz

A logikai alkalmazás felügyelt identitásának hitelesítéshez való használata előtt állítsa be az identitáshoz való hozzáférést arra az Azure-erőforrásra, ahol használni tervezi az identitást. A feladat végrehajtásához rendelje hozzá a megfelelő szerepkört ehhez az identitáshoz a cél Azure-erőforráson. A következő lehetőségeket használhatja:

* [Azure Portal](#azure-portal-assign-access)
* [Azure Resource Manager-sablon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) – További információ: Szerepkör-hozzárendelés hozzáadása [az Azure RBAC](../role-based-access-control/role-assignments-powershell.md)és a Azure PowerShell.
* Azure CLI ([az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)) – További információ: Szerepkör-hozzárendelés hozzáadása az Azure RBAC és az Azure CLI [használatával.](../role-based-access-control/role-assignments-cli.md)
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Hozzáférés hozzárendelése a Azure Portal

Azon a cél Azure-erőforráson, ahol hozzáférést szeretne adni a felügyelt identitásnak, adjon szerepköralapú hozzáférést az identitásnak a célerőforráshoz.

1. A [Azure Portal](https://portal.azure.com)meg azt az Azure-erőforrást, amelyhez hozzáférést szeretne a felügyelt identitáshoz.

1. Az erőforrás menüjében válassza a Hozzáférés-vezérlés **(IAM)** szerepkör-hozzárendelések lehetőséget, ahol áttekintheti az adott erőforráshoz aktuális  >   szerepkör-hozzárendeléseket. Az eszköztáron válassza a **Szerepkör-hozzárendelés**  >  **hozzáadása lehetőséget.**

   ![A "Szerepkör-hozzárendelés hozzáadása" > válassza a "Hozzáadás" lehetőséget](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Ha a **Szerepkör-hozzárendelés hozzáadása** lehetőség le van tiltva, valószínűleg nem rendelkezik engedélyekkel. További információ az erőforrások szerepköreinek kezeléséhez szükséges engedélyekről: Rendszergazdai szerepkörök engedélyei a [Azure Active Directory.](../active-directory/roles/permissions-reference.md)

1. A **Szerepkör-hozzárendelés hozzáadása alatt** válasszon ki egy **szerepkört,** amely biztosítja az identitás számára a szükséges hozzáférést a célerőforráshoz.

   Ebben a témakörben használt példában az identitásnak olyan szerepkörre van szüksége, amely hozzáfér a [blobhoz](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)egy Azure Storage-tárolóban, ezért a felügyelt identitáshoz válassza a **Storage-blobadatok** közreműködője szerepkört.

   ![Válassza a "Storage-blobadatok közreműködője" szerepkört](./media/create-managed-service-identity/select-role-for-identity.png)

1. Kövesse az alábbi lépéseket a felügyelt identitáshoz:

   * **Rendszer által hozzárendelt identitás**

     1. A Hozzáférés **hozzárendelése mezőben** válassza a **Logikai alkalmazás lehetőséget.** Amikor **megjelenik az Előfizetés** tulajdonság, válassza ki az identitásához társított Azure-előfizetést.

        ![Hozzáférés kiválasztása a rendszer által hozzárendelt identitáshoz](./media/create-managed-service-identity/assign-access-system.png)

     1. A Kijelölés **mező** alatt válassza ki a logikai alkalmazást a listából. Ha a lista túl hosszú, a Kijelölés **mezővel** szűrheti a listát.

        ![Logikai alkalmazás kiválasztása rendszer által hozzárendelt identitáshoz](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Felhasználó által hozzárendelt identitás**

     1. A Hozzáférés **hozzárendelése mezőben** válassza a Felhasználó **által hozzárendelt felügyelt identitás lehetőséget.** Amikor **megjelenik az Előfizetés** tulajdonság, válassza ki az identitásához társított Azure-előfizetést.

        ![Felhasználó által hozzárendelt identitás hozzáférésének kiválasztása](./media/create-managed-service-identity/assign-access-user.png)

     1. A Kijelölés **mezőben** válassza ki az identitását a listából. Ha a lista túl hosszú, a Kijelölés **mezővel** szűrheti a listát.

        ![Válassza ki a felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   A célerőforrás szerepkör-hozzárendelési listája most már a kiválasztott felügyelt identitást és szerepkört jeleníti meg. Ez a példa bemutatja, hogyan használhatja a rendszer által hozzárendelt identitást egy logikai alkalmazáshoz és egy felhasználó által hozzárendelt identitást más logikai alkalmazások csoportjához.

   ![Felügyelt identitások és szerepkörök hozzáadva a célerőforráshoz](./media/create-managed-service-identity/added-roles-for-identities.png)

   További információ: Felügyelt identitás hozzáférésének hozzárendelése egy [erőforráshoz a Azure Portal.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. Most kövesse a [lépéseket a](#authenticate-access-with-identity) hozzáférés identitással való hitelesítéséhez egy eseményindítóban vagy műveletben, amely támogatja a felügyelt identitásokat.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Hozzáférés hitelesítése felügyelt identitással

Miután engedélyezi [a](#azure-portal-system-logic-app) felügyelt identitást a logikai alkalmazáshoz, és hozzáférést ad az identitásnak a célerőforráshoz vagy entitáshoz, [](#access-other-resources)ezt az identitást használhatja a felügyelt identitásokat támogató eseményindítókban és [műveletekben.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

> [!IMPORTANT]
> Ha rendelkezik olyan Azure-függvénysel, amelyben a rendszer által hozzárendelt identitást szeretné használni, először engedélyezze a [hitelesítést az Azure Functions számára.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)

Ezek a lépések azt mutatják be, hogyan használható a felügyelt identitás eseményindítóval vagy műveletekkel a Azure Portal. A felügyelt identitás eseményindítóban vagy művelet mögöttes JSON-definíciójában való megadásához lásd: Felügyelt identitás [hitelesítése.](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. A [Azure Portal](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. Ha még nem tette meg, adja hozzá a felügyelt identitásokat támogató eseményindítót [vagy műveletet.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

   > [!NOTE]
   > Nem minden eseményindító és művelet támogatja a hitelesítési típus hozzáadását. További információ: Hitelesítési típusok a hitelesítést támogató [eseményindítókhoz és műveletekhez.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. A hozzáadott eseményindítón vagy műveleten kövesse az alábbi lépéseket:

   * **Beépített eseményindítók és műveletek, amelyek támogatják a felügyelt identitások használatának támogatását**

     1. Adja hozzá **a Hitelesítés** tulajdonságot, ha a tulajdonság még nem jelenik meg.

     1. A **Hitelesítési típus alatt** válassza a Felügyelt identitás **lehetőséget.**

     További információ: Példa: Beépített eseményindító vagy művelet [hitelesítése felügyelt identitással.](#authenticate-built-in-managed-identity)
 
   * **Felügyelt összekötők felügyelt identitással való használatát támogató eseményindítók és műveletek**

     1. A bérlő kiválasztásának oldalán válassza a **Csatlakozás felügyelt identitással lehetőséget.**

     1. A következő lapon adja meg a kapcsolat nevét.

        Alapértelmezés szerint a felügyelt identitások listája csak a jelenleg engedélyezett felügyelt identitást jeleníti meg, mivel a logikai alkalmazás egyszerre csak egy felügyelt identitás engedélyezését támogatja, például:

        ![Képernyőkép a kapcsolat neve lapról és a kiválasztott felügyelt identitásról.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     További információkért lásd: [Példa: Felügyelt összekötő eseményindító vagy művelet hitelesítése felügyelt identitással.](#authenticate-managed-connector-managed-identity)

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Példa: Beépített eseményindító vagy művelet hitelesítése felügyelt identitással

A HTTP-eseményindító vagy -művelet használhatja a logikai alkalmazáshoz engedélyezett, rendszer által hozzárendelt identitást. A HTTP-eseményindító vagy -művelet általában az alábbi tulajdonságokkal határozza meg az elérni kívánt erőforrást vagy entitást:

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Metódus** | Yes | A futtatni kívánt művelet által használt HTTP-metódus |
| **URI** | Yes | A cél Azure-erőforrás vagy -entitás eléréséhez szükséges végponti URL-cím. Az URI szintaxis általában [tartalmazza](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) az Azure-erőforrás vagy -szolgáltatás erőforrás-azonosítóját. |
| **Fejlécek** | No | A kimenő kérelembe foglalni kívánt vagy tartalmazni kívánt fejlécértékek, például a tartalom típusa |
| **Lekérdezések** | No | A kérelembe foglalni kívánt vagy tartalmazni kívánt lekérdezési paraméterek, például egy adott művelet paramétere vagy a futtatni kívánt művelet API-verziója |
| **Hitelesítés** | Yes | A célerőforráshoz vagy entitáshoz való hozzáférés hitelesítéséhez használt hitelesítési típus |
||||

Konkrét példaként tegyük fel, hogy a [Snapshot Blob](/rest/api/storageservices/snapshot-blob) műveletet egy olyan blobon szeretné futtatni az Azure Storage-fiókban, ahol korábban beállította az identitáshoz való hozzáférést. A Azure Blob Storage [összekötő](/connectors/azureblob/) azonban jelenleg nem kínál ilyen műveletet. Ehelyett futtathatja ezt a [](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) műveletet a HTTP-művelettel vagy egy [másik Blob szolgáltatás REST API-ja művelettel.](/rest/api/storageservices/operations-on-blobs)

> [!IMPORTANT]
> Ha HTTP-kérésekkel és felügyelt identitásokkal fér hozzá a tűzfalak mögötti Azure Storage-fiókokhoz, győződjön meg arról, hogy a tárfiókot is beállította azzal a kivétellel, amely lehetővé teszi a megbízható [Microsoft-szolgáltatások.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

A [Pillanatképblob művelet futtatásához a](/rest/api/storageservices/snapshot-blob)HTTP-művelet a következő tulajdonságokat határozza meg:

| Tulajdonság | Kötelező | Példaérték | Description |
|----------|----------|---------------|-------------|
| **Metódus** | Yes | `PUT`| A Pillanatképblob művelet által használt HTTP-metódus |
| **URI** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Egy azure Azure Blob Storage globális (nyilvános) környezetben található erőforrás-azonosító, amely ezt a szintaxist használja |
| **Fejlécek** | Az Azure Storage-hoz | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Az `x-ms-blob-type` `x-ms-version` Azure Storage-műveletekhez a , a és a fejléc `x-ms-date` értékére van szükség. <p><p>**Fontos:** Az Azure Storage kimenő HTTP-eseményindítóiban és műveleti kéréseiben a fejléchez szükség van a futtatni kívánt művelet tulajdonságára és `x-ms-version` API-verziójára. A `x-ms-date` dátumnak az aktuális dátumnak kell lennie. Ellenkező esetben a logikai alkalmazás hibát `403 FORBIDDEN` jelez. A szükséges formátumban lévő aktuális dátum lekért értékéhez használhatja a kifejezést a példaértékben. <p>További információt az alábbi témakörökben talál: <p><p>- [Kérésfejlécek – Pillanatképblob](/rest/api/storageservices/snapshot-blob#request) <br>- [Az Azure Storage-szolgáltatások verziószámozása](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Lekérdezések** | Csak a Pillanatképblob művelethez | `comp` = `snapshot` | A művelet lekérdezési paraméterének neve és értéke. |
|||||

Itt látható a példa HTTP-művelet, amely az alábbi tulajdonságértékeket jeleníti meg:

![HTTP-művelet hozzáadása egy Azure-erőforrás eléréséhez](./media/create-managed-service-identity/http-action-example.png)

1. A HTTP-művelet hozzáadása után adja hozzá a **Hitelesítés tulajdonságot** a HTTP-művelethez. Az Új **paraméter hozzáadása listában** válassza a Hitelesítés **lehetőséget.**

   !["Hitelesítés" tulajdonság hozzáadása HTTP-művelethez](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem minden eseményindító és művelet támogatja a hitelesítési típus hozzáadását. További információ: Hitelesítési típusok a hitelesítést támogató [eseményindítókhoz és műveletekhez.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. A Hitelesítés **típusa listában** válassza a **Felügyelt identitás lehetőséget.**

   ![A "Hitelesítés" mezőben válassza a "Felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-managed-identity.png)

1. A felügyelt identitások listájából válasszon a forgatókönyvnek megfelelő elérhető lehetőségek közül.

   * Ha beállította a rendszer által hozzárendelt identitást, válassza a **Rendszer által** hozzárendelt felügyelt identitás lehetőséget, ha még nincs kiválasztva.

     ![Válassza a "Rendszer által hozzárendelt felügyelt identitás" lehetőséget](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Ha felhasználó által hozzárendelt identitást ad meg, válassza ki ezt az identitást, ha még nincs kiválasztva.

     ![Válassza ki a felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Ez a példa a rendszer által **hozzárendelt felügyelt identitással folytatódik.**

1. Egyes eseményindítók és műveletek esetén a **Célközönség** tulajdonság is megjelenik, hogy be tudja állítani a célként meghatározott erőforrás-azonosítót. Állítsa a **Célközönség** tulajdonságot a célerőforrás vagy szolgáltatás [erőforrás-azonosítójára.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) Ellenkező esetben a **Célközönség** tulajdonság alapértelmezés szerint az `https://management.azure.com/` erőforrás-azonosítót használja, amely a Azure Resource Manager.

   > [!IMPORTANT]
   > Győződjön meg arról,  hogy a célerőforrás-azonosító pontosan megegyezik az Azure Active Directory (AD) által várt értékkel, beleértve a záró perjeleket is. Az összes fiók erőforrás-azonosítója például Azure Blob Storage perjelet igényel. Egy adott tárfiók erőforrás-azonosítója azonban nem igényel záró perjelet. Ellenőrizze az [Azure AD-t támogató Azure-szolgáltatások erőforrás-hozzárendelését.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   Ez a példa úgy **állítja be a Célközönség** tulajdonságot, hogy a hitelesítéshez használt hozzáférési jogkivonatok minden tárfiókra `https://storage.azure.com/` érvényesek. Azonban megadhatja egy adott tárfiók gyökérszolgáltatásának `https://fabrikamstorageaccount.blob.core.windows.net` URL-címét is.

   ![A "Célközönség" tulajdonság beállítása célerőforrás-azonosítóra](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Az Azure Storage Azure AD-hez való hozzáférésének az alábbi témakörökben található további információ:

   * [Azure-blobok és -üzenetsorok hozzáférésének Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Az Azure Storage-hoz való hozzáférés Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Folytassa a logikai alkalmazás kívánt módon való építését.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Példa: Felügyelt összekötő eseményindító vagy művelet hitelesítése felügyelt identitással

Az Azure Resource Manager **erőforrás olvasása** művelet használhatja a logikai alkalmazáshoz engedélyezett felügyelt identitást. Ez a példa bemutatja, hogyan használhatja a rendszer által hozzárendelt felügyelt identitást.

1. Miután hozzáadta a műveletet a munkafolyamathoz, a bérlő kiválasztási oldalán válassza a Csatlakozás **felügyelt identitással lehetőséget.**

   ![A kiválasztott Azure Resource Manager és a "Csatlakozás felügyelt identitással" beállítást bemutató képernyőkép.](./media/create-managed-service-identity/select-connect-managed-identity.png)

   A művelet most megjeleníti a kapcsolatnév lapot a felügyelt identitások listájával, amely tartalmazza a logikai alkalmazásban jelenleg engedélyezett felügyelt identitástípust.

1. A kapcsolat neve lapon adja meg a kapcsolat nevét. A felügyelt identitások listájából válassza ki a felügyelt identitást, amely ebben a példában a Rendszer **által** hozzárendelt felügyelt identitás, majd válassza a **Létrehozás lehetőséget.** Ha engedélyezte a felhasználó által hozzárendelt felügyelt identitást, válassza ki azt az identitást.

   ![Képernyőkép a Azure Resource Manager műveletről, a megadott kapcsolatnévvel és a "Rendszer által hozzárendelt felügyelt identitás" kijelölve.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Ha a felügyelt identitás nincs engedélyezve, a következő hiba jelenik meg a kapcsolat létrehozásakor:

   *Engedélyeznie kell a felügyelt identitást a logikai alkalmazáshoz, majd meg kell adni a szükséges hozzáférést a célerőforrás identitásához.*

   ![Képernyőkép a Azure Resource Manager műveletről, ha nincs engedélyezve felügyelt identitás.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. A kapcsolat sikeres létrehozása után a tervező a felügyelt identitás hitelesítésének használatával bármilyen dinamikus értéket, tartalmat vagy sémát lekérhet.

1. Folytassa a logikai alkalmazás kívánt módon való építését.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>A logikai alkalmazás erőforrás-definíciója és a felügyelt identitást használó kapcsolatok

A felügyelt identitást lehetővé tő és használó kapcsolat egy speciális kapcsolattípus, amely csak felügyelt identitással működik. Futásidőben a kapcsolat a logikai alkalmazásban engedélyezett felügyelt identitást használja. Ez a konfiguráció a logikai alkalmazás erőforrás-definíciójának objektumában lesz mentve, amely tartalmazza a kapcsolat erőforrás-azonosítójára mutató mutatókat, valamint az identitás `parameters` erőforrás-azonosítóját, ha a felhasználó által hozzárendelt identitás `$connections` engedélyezve van.

Ez a példa bemutatja, hogyan néz ki a konfiguráció, ha a logikai alkalmazás engedélyezi a rendszer által hozzárendelt felügyelt identitást:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Ez a példa bemutatja, hogyan néz ki a konfiguráció, ha a logikai alkalmazás engedélyezi a felhasználó által hozzárendelt felügyelt identitást:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

A futásidőben a Logic Apps szolgáltatás ellenőrzi, hogy a felügyelt összekötő eseményindítója és a logikai alkalmazásban végzett műveletek be vannak-e állítva a felügyelt identitás használatára, és hogy az összes szükséges engedély be van állítva a felügyelt identitás használatára az eseményindító és a műveletek által meghatározott célerőforrások eléréséhez. Sikeres művelet esetén az Logic Apps szolgáltatás lekéri a felügyelt identitáshoz társított Azure AD-jogkivonatot, és ezt az identitást használja a célerőforráshoz való hozzáférés hitelesítéséhez, valamint a konfigurált művelet eseményindítókban és műveletekben való végrehajtásához.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>ARM-sablon felügyelt kapcsolatokhoz és felügyelt identitásokhoz

Ha ARM-sablonnal automatizálja az üzembe helyezést, és a logikai alkalmazás felügyelt identitást használó felügyelt összekötő-eseményindítót vagy műveletet tartalmaz, ellenőrizze, hogy a mögöttes kapcsolati erőforrás definíciója tartalmazza-e a tulajdonságot `parameterValueType` `Alternative` tulajdonságértékként. Ellenkező esetben az ARM üzemelő példánya nem fogja beállítani a kapcsolatot a felügyelt identitás hitelesítéshez való használatára, és a kapcsolat nem fog működni a logikai alkalmazás munkafolyamatában. Ez a követelmény csak azokra a felügyelt [összekötőkhöz](#managed-connectors-managed-identity) kapcsolódó eseményindítókra és műveletekre vonatkozik, amelyekben a Csatlakozás felügyelt [ **identitással**](#authenticate-managed-connector-managed-identity)lehetőséget választotta.

Itt található például egy felügyelt identitást használó Azure Automation-művelet mögöttes kapcsolati erőforrás-definíciója, amelyben a definíció tartalmazza a tulajdonságot, amelynek tulajdonságértéke a `parameterValueType` `Alternative` következő:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Felügyelt identitás letiltása

Ha nem használ felügyelt identitást a logikai alkalmazáshoz, a következő lehetőségek állnak rendelkezésre:

* [Azure Portal](#azure-portal-disable)
* [Azure Resource Manager-sablonok](#template-disable)
* Azure PowerShell
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-powershell.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-cli.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-rest.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Ha törli a logikai alkalmazást, az Azure automatikusan eltávolítja a felügyelt identitást az Azure AD-ból.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Felügyelt identitás letiltása a Azure Portal

A Azure Portal távolítsa el az identitás hozzáférését a [célerőforráshoz.](#disable-identity-target-resource) Ezután kapcsolja ki a rendszer által hozzárendelt identitást, vagy távolítsa el a felhasználó által hozzárendelt identitást a [logikai alkalmazásból.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identitás-hozzáférés eltávolítása az erőforrásokból

1. A [Azure Portal](https://portal.azure.com)meg a cél Azure-erőforrást, ahol el szeretné távolítani a felügyelt identitáshoz való hozzáférést.

1. A célerőforrás menüjében válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.** Az eszköztáron válassza a **Szerepkör-hozzárendelések lehetőséget.**

1. A szerepkörök listájában válassza ki az eltávolítani kívánt felügyelt identitásokat. Az eszköztáron válassza az Eltávolítás **lehetőséget.**

   > [!TIP]
   > Ha az **Eltávolítás** lehetőség le van tiltva, valószínűleg nem rendelkezik engedélyekkel. További információ az erőforrások szerepköreinek kezeléséhez szükséges engedélyekről: Rendszergazdai szerepkörök engedélyei a [Azure Active Directory.](../active-directory/roles/permissions-reference.md)

A felügyelt identitás el lett távolítva, és már nem rendelkezik hozzáféréssel a célerőforráshoz.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Felügyelt identitás letiltása a logikai alkalmazásban

1. A [Azure Portal](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében a Beállítások alatt **válassza** az **Identitás** lehetőséget, majd kövesse az identitáshoz szükséges lépéseket:

   * Válassza **a Rendszer**  >  **hozzárendelve a Mentés**  >  **gombra.** Amikor az Azure megerősítést kér, válassza az **Igen lehetőséget.**

     ![A rendszer által hozzárendelt identitás letiltása](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Válassza **a Felhasználóhoz rendelt** és a felügyelt identitás, majd az Eltávolítás **lehetőséget.** Amikor az Azure megerősítést kér, válassza az **Igen lehetőséget.**

     ![A felhasználó által hozzárendelt identitás eltávolítása](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A felügyelt identitás mostantól le van tiltva a logikai alkalmazásban.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Felügyelt identitás letiltása Azure Resource Manager sablonban

Ha a logikai alkalmazás felügyelt identitását egy Azure Resource Manager sablonnal hozta létre, állítsa az objektum gyermektulajdonságát a `identity` `type` következőre: `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Következő lépések

* [Biztonságos hozzáférés és adatok Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
