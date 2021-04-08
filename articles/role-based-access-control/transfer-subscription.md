---
title: Azure-előfizetés átvitele egy másik Azure AD-címtárba
description: Ismerje meg, hogyan vihet át egy Azure-előfizetést és egy ismert kapcsolódó erőforrást egy másik Azure Active Directory (Azure AD) könyvtárba.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2020
ms.author: rolyon
ms.openlocfilehash: 5a4be6052e72c27ad83b5af64f1acb3ad8d4e3be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555905"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Azure-előfizetés átvitele egy másik Azure AD-címtárba

A szervezetek több Azure-előfizetéssel is rendelkezhetnek. Minden előfizetés egy adott Azure Active Directory (Azure AD) címtárral van társítva. A felügyelet egyszerűbbé tételéhez érdemes lehet egy másik Azure AD-címtárba továbbítani az előfizetést. Ha egy előfizetést másik Azure AD-címtárba ruház át, bizonyos erőforrások nem kerülnek át a célkiszolgálóra. Például az Azure szerepköralapú hozzáférés-vezérlésben (Azure RBAC) lévő összes szerepkör-hozzárendelés és egyéni szerepkör **véglegesen** törlődik a forrás-címtárból, és nem kerül át a célhelyre.

Ez a cikk az előfizetés egy másik Azure AD-címtárba való átviteléhez és az átvitel után az egyes erőforrások újbóli létrehozásához szükséges alapvető lépéseket ismerteti.

> [!NOTE]
> Az Azure Cloud Solution Providers (CSP) előfizetések esetében az előfizetés Azure AD-címtárának módosítása nem támogatott.

## <a name="overview"></a>Áttekintés

Az Azure-előfizetés egy másik Azure AD-címtárba való átvitele összetett folyamat, amelyet körültekintően kell megtervezni és végrehajtani. Számos Azure-szolgáltatás megköveteli a rendszerbiztonsági tag (identitások) használatát, hogy szabályosan működjenek, vagy akár más Azure-erőforrások kezeléséhez is. Ez a cikk az Azure-szolgáltatások nagy részét próbálja megtekinteni, amelyek nagy mértékben függenek a rendszerbiztonsági tagtől, de nem átfogóak.

> [!IMPORTANT]
> Bizonyos esetekben előfordulhat, hogy az előfizetés átadása állásidőt igényel a folyamat befejezéséhez. Alapos tervezés szükséges annak megállapításához, hogy az átvitelhez szükség van-e az állásidőre.

Az alábbi ábra azokat az alapszintű lépéseket mutatja be, amelyeket az előfizetés másik könyvtárba való átvitele során kell végrehajtania.

1. Felkészülés az átvitelre

1. Az Azure-előfizetés átvitele egy másik könyvtárba

1. Erőforrások újbóli létrehozása a cél címtárban, például szerepkör-hozzárendelések, egyéni szerepkörök és felügyelt identitások

    ![Előfizetés-diagram átvitele](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Annak eldöntése, hogy az előfizetést át kell-e vinni egy másik könyvtárba

A következőkben néhány ok, amiért érdemes lehet átvinni egy előfizetést:

- Egy vállalat fúziója vagy beszerzése miatt az elsődleges Azure AD-címtárban egy beszerzett előfizetést szeretne kezelni.
- A munkahelyen valaki létrehozott egy előfizetést, és egy adott Azure AD-címtárhoz szeretné összevonni a felügyeletet.
- Olyan alkalmazásokkal rendelkezik, amelyek egy adott előfizetés-AZONOSÍTÓtól vagy URL-címről függenek, és nem könnyű módosítani az alkalmazás konfigurációját vagy kódját.
- Az üzleti tevékenység egy része egy különálló vállalatra oszlik, és néhány erőforrást egy másik Azure AD-címtárba kell áthelyeznie.
- A biztonsági elkülönítés érdekében egy másik Azure AD-címtárban lévő erőforrásokat szeretne kezelni.

### <a name="alternate-approaches"></a>Alternatív megoldások

Az előfizetés átadásához állásidő szükséges a folyamat befejezéséhez. A forgatókönyvtől függően a következő alternatív módszereket veheti fontolóra:

- Hozza létre újra az erőforrásokat, és másolja az Adatmásolást a cél könyvtárba és az előfizetésbe.
- Hozzon be egy többkönyvtáras architektúrát, és hagyja el az előfizetést a forrás címtárban. Az Azure Lighthouse használatával delegálhatja az erőforrásokat, hogy a célként megadott könyvtárban lévő felhasználók hozzáférhessenek az előfizetéshez a forrás címtárban. További információ: [Az Azure Lighthouse nagyvállalati forgatókönyvekben](../lighthouse/concepts/enterprise.md).

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Az előfizetés átadásának következményeinek megismerése

Számos Azure-erőforrás függőséget tartalmaz egy előfizetéshez vagy egy címtárhoz. A helyzettől függően a következő táblázat felsorolja az előfizetés átadásának ismert hatását. A cikkben ismertetett lépések végrehajtásával újra létrehozhatja az előfizetés átvitele előtt meglévő erőforrásokat.

> [!IMPORTANT]
> Ez a szakasz felsorolja azokat az ismert Azure-szolgáltatásokat és-erőforrásokat, amelyek az előfizetéstől függenek. Mivel az Azure-beli erőforrástípusok folyamatosan fejlődnek, előfordulhat, hogy az itt felsorolt további függőségek is felmerülhetnek, így a környezet megszakadhat. 

| Szolgáltatás vagy erőforrás | Érintett | Helyreállítható | Hatással van? | Miket végezhet el? |
| --------- | --------- | --------- | --------- | --------- |
| Szerepkör-hozzárendelések | Igen | Yes | [Szerepkör-hozzárendelések felsorolása](#save-all-role-assignments) | Az összes szerepkör-hozzárendelés véglegesen törölve lesz. A felhasználókat, csoportokat és egyszerű szolgáltatásokat le kell képeznie a cél könyvtár megfelelő objektumaira. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Egyéni szerepkörök | Igen | Yes | [Egyéni szerepkörök listázása](#save-custom-roles) | Az összes egyéni szerepkör véglegesen törölve lesz. Újra létre kell hoznia az egyéni szerepköröket és a szerepkör-hozzárendeléseket. |
| Rendszer által hozzárendelt felügyelt identitások | Igen | Yes | [Felügyelt identitások listázása](#list-role-assignments-for-managed-identities) | Le kell tiltania, majd újra engedélyeznie kell a felügyelt identitásokat. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Felhasználó által hozzárendelt felügyelt identitások | Igen | Yes | [Felügyelt identitások listázása](#list-role-assignments-for-managed-identities) | Törölnie kell, újra létre kell hoznia és csatolnia kell a felügyelt identitásokat a megfelelő erőforráshoz. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Azure Key Vault | Igen | Yes | [Hozzáférési szabályzatok listázása Key Vault](#list-key-vaults) | Frissítenie kell a kulcstartóhoz társított bérlői azonosítót. Az új hozzáférési házirendeket el kell távolítania és hozzá kell adnia. |
| Azure SQL Database-adatbázisok engedélyezve az Azure AD-hitelesítés integrációja | Igen | Nem | [Azure SQL-adatbázisok keresése az Azure AD-hitelesítéssel](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Azure Storage és Azure Data Lake Storage Gen2 | Igen | Yes |  | Az ACL-eket újra létre kell hoznia. |
| Azure Data Lake Storage Gen1 | Igen | Yes |  | Az ACL-eket újra létre kell hoznia. |
| Azure Files | Igen | Yes |  | Az ACL-eket újra létre kell hoznia. |
| Azure File Sync | Igen | Yes |  |  |
| Azure Managed Disks | Igen | Yes |  |  Ha az ügyfél által felügyelt kulcsokkal rendelkező Managed Disks titkosítását használja, le kell tiltania, majd újra engedélyeznie kell a lemezes titkosítási csoportokhoz társított rendszerhez rendelt identitásokat. És újra létre kell hoznia a szerepkör-hozzárendeléseket, azaz újra meg kell adnia a szükséges engedélyeket a lemez titkosítási csoportjai számára a Kulcstartókban. |
| Azure Kubernetes Service | Igen | Yes |  |  |
| Azure Policy | Igen | Nem | Minden Azure Policy objektum, beleértve az egyéni definíciókat, a hozzárendeléseket, a kivételeket és a megfelelőségi adatok. | A definíciók [exportálására](../governance/policy/how-to/export-resources.md), importálására és újbóli hozzárendelésére van szükség. Ezután hozzon létre új szabályzat-hozzárendeléseket és a szükséges [házirend-kivételeket](../governance/policy/concepts/exemption-structure.md). |
| Azure Active Directory tartományi szolgáltatások | Igen | Nem |  |  |
| Alkalmazásregisztrációk | Igen | Yes |  |  |

> [!WARNING]
> Ha olyan erőforráshoz (például egy Storage-fiókhoz vagy SQL-adatbázishoz) használ titkosítást, amely **nem** ugyanahhoz az előfizetéshez tartozik, mint az átvitt egyik kulcstartó, akkor az egy helyreállíthatatlan forgatókönyvhöz vezethet. Ha ez a helyzet áll fenn, hajtson végre egy másik kulcstartó használatát, vagy átmenetileg tiltsa le az ügyfél által felügyelt kulcsokat a nem helyreállítható forgatókönyv elkerüléséhez.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

- [Bash Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure) -ben
- Annak az előfizetésnek a fiók rendszergazdája, amelyet át szeretne vinni a forrás-könyvtárba
- [Tulajdonosi](built-in-roles.md#owner) szerepkör a célként megadott könyvtárban

## <a name="step-1-prepare-for-the-transfer"></a>1. lépés: Felkészülés az átvitelre

### <a name="sign-in-to-source-directory"></a>Bejelentkezés a forrás könyvtárába

1. Jelentkezzen be az Azure-ba rendszergazdaként.

1. Szerezze be az előfizetések listáját az az [Account List](/cli/azure/account#az_account_list) paranccsal.

    ```azurecli
    az account list --output table
    ```

1. Az az [Account set](/cli/azure/account#az_account_set) paranccsal állíthatja be az átvinni kívánt aktív előfizetést.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Az Azure Resource Graph bővítmény telepítése

 Az Azure-erőforrás- [gráfhoz](../governance/resource-graph/index.yml)készült Azure CLI-bővítmény, az *erőforrás-gráf* lehetővé teszi, hogy az az [Graph](/cli/azure/ext/resource-graph/graph) paranccsal lekérdezze Azure Resource Manager által kezelt erőforrásokat. Ezt a parancsot a későbbi lépésekben fogja használni.

1. Az [az Extension List](/cli/azure/extension#az_extension_list) paranccsal ellenőrizze, hogy telepítve van *-e az erőforrás-gráf* bővítmény.

    ```azurecli
    az extension list
    ```

1. Ha nem, telepítse az *erőforrás-gráf* bővítményt.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Az összes szerepkör-hozzárendelés mentése

1. Az [az szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az_role_assignment_list) használatával listázhatja az összes szerepkör-hozzárendelést (beleértve az örökölt szerepkör-hozzárendeléseket is).

    Annak érdekében, hogy könnyebb legyen áttekinteni a listát, a kimenet JSON-ként, TSV-ként vagy táblázatként is exportálható. További információ: szerepkör- [hozzárendelések listázása az Azure RBAC és az Azure CLI használatával](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Mentse a szerepkör-hozzárendelések listáját.

    Az előfizetés átvitele után az összes szerepkör-hozzárendelés **véglegesen** törlődik, ezért fontos a másolat mentése.

1. Tekintse át a szerepkör-hozzárendelések listáját. Lehetséges, hogy szerepkör-hozzárendelések nem szükségesek a cél könyvtárában.

### <a name="save-custom-roles"></a>Egyéni szerepkörök mentése

1. Az egyéni szerepkörök listázásához használja az az [role definition List listát](/cli/azure/role/definition#az_role_definition_list) . További információ: [Azure-beli egyéni szerepkörök létrehozása vagy frissítése az Azure CLI használatával](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Mentse az egyes egyéni szerepköröket, amelyekre szüksége lesz a célkiszolgálón külön JSON-fájlként.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Készítsen másolatot az egyéni szerepkör fájljairól.

1. Módosítsa az egyes példányokat a következő formátum használatára.

    Később ezeket a fájlokat fogja használni, hogy újra létrehozza az egyéni szerepköröket a cél könyvtárában.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Felhasználó-, csoport-és szolgáltatásnév-hozzárendelések meghatározása

1. A szerepkör-hozzárendelések listája alapján határozza meg, hogy mely felhasználókra, csoportokra és egyszerű szolgáltatásokra fog leképezni a cél könyvtárban.

    A rendszerbiztonsági tag típusának azonosításához tekintse meg az `principalType` egyes szerepkör-hozzárendelések tulajdonságát.

1. Ha szükséges, a cél könyvtárban hozzon létre minden olyan felhasználót, csoportot vagy egyszerű szolgáltatást, amelyre szüksége lesz.

### <a name="list-role-assignments-for-managed-identities"></a>Felügyelt identitások szerepkör-hozzárendeléseinek listázása

A felügyelt identitások nem frissülnek, ha egy előfizetést egy másik könyvtárba továbbítanak. Ennek eredményeképpen minden meglévő, rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás meg lesz szakítva. Az átvitel után újra engedélyezheti a rendszerhez rendelt felügyelt identitásokat. A felhasználó által hozzárendelt felügyelt identitások esetén újra létre kell hoznia és csatolnia kell őket a célként megadott könyvtárban.

1. Tekintse át a [felügyelt identitásokat támogató Azure-szolgáltatások listáját](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) , és jegyezze fel, hogy hol lehet felügyelt identitásokat használni.

1. Az [az ad SP List](/cli/azure/ad/sp#az_ad_sp_list) használatával listázhatja a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitásokat.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. A felügyelt identitások listájában határozza meg, hogy mely rendszerek vannak hozzárendelve, és mely felhasználókhoz van rendelve. A típus meghatározásához a következő feltételek használhatók.

    | Feltételek | Felügyelt identitás típusa |
    | --- | --- |
    | `alternativeNames` tulajdonság tartalma `isExplicit=False` | Rendszer által hozzárendelt |
    | `alternativeNames` a tulajdonság nem tartalmazza `isExplicit` | Rendszer által hozzárendelt |
    | `alternativeNames` tulajdonság tartalma `isExplicit=True` | Felhasználó által hozzárendelt |

    Az [az Identity List](/cli/azure/identity#az_identity_list) használatával is egyszerűen listázhatja a felhasználó által hozzárendelt felügyelt identitásokat. További információ: [felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése az Azure CLI használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. A `objectId` felügyelt identitások értékeit tartalmazó lista beolvasása.

1. Keresse meg a szerepkör-hozzárendelések listáját, és ellenőrizze, hogy vannak-e szerepkör-hozzárendelések a felügyelt identitásokhoz.

### <a name="list-key-vaults"></a>Kulcstartók listázása

Kulcstartó létrehozásakor a rendszer automatikusan az alapértelmezett Azure Active Directory bérlői AZONOSÍTÓhoz kapcsolódik ahhoz az előfizetéshez, amelyben létrehozták. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. További információ: [Azure Key Vault áthelyezése másik előfizetésre](../key-vault/general/move-subscription.md).

> [!WARNING]
> Ha olyan erőforráshoz (például egy Storage-fiókhoz vagy SQL-adatbázishoz) használ titkosítást, amely **nem** ugyanahhoz az előfizetéshez tartozik, mint az átvitt egyik kulcstartó, akkor az egy helyreállíthatatlan forgatókönyvhöz vezethet. Ha ez a helyzet áll fenn, hajtson végre egy másik kulcstartó használatát, vagy átmenetileg tiltsa le az ügyfél által felügyelt kulcsokat a nem helyreállítható forgatókönyv elkerüléséhez.

- Ha rendelkezik kulcstartóval, az az Key [Vault show](/cli/azure/keyvault#az_keyvault_show) paranccsal listázhatja a hozzáférési házirendeket. További információ: [Key Vault hozzáférési szabályzatok társítása](../key-vault/general/assign-access-policy-cli.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure SQL Database-adatbázisok listázása az Azure AD-hitelesítéssel

- Az [az SQL Server ad-admin List](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) és az az [Graph](/cli/azure/ext/resource-graph/graph) bővítmény használatával ellenőrizze, hogy az Azure SQL Database-adatbázisok engedélyezve vannak-e az Azure ad-hitelesítés integrálásával. További információ: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL](../azure-sql/database/authentication-aad-configure.md)-sel.

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL-ek listázása

1. Azure Data Lake Storage Gen1 használata esetén a Azure Portal vagy a PowerShell használatával sorolja fel a fájlokra vonatkozó ACL-eket.

1. Azure Data Lake Storage Gen2 használata esetén a Azure Portal vagy a PowerShell használatával sorolja fel a fájlokra vonatkozó ACL-eket.

1. Ha Azure Files használ, sorolja fel a fájlokra alkalmazott ACL-eket.

### <a name="list-other-known-resources"></a>Más ismert erőforrások listázása

1. Az az [Account show](/cli/azure/account#az_account_show) paranccsal kérheti le az előfizetés-azonosítóját.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Az az [Graph](/cli/azure/ext/resource-graph/graph) bővítmény használatával listázhat más Azure-erőforrásokat az ismert Azure ad-címtár függőségeivel.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>2. lépés: az előfizetés átvitele

Ebben a lépésben átviszi az előfizetést a forrás könyvtárából a cél könyvtárba. A lépések eltérőek lehetnek attól függően, hogy a számlázási tulajdonjogot is át kívánja-e vinni.

> [!WARNING]
> Az előfizetés átvitele után a rendszer az összes szerepkör-hozzárendelést **véglegesen** törli, és nem állítható vissza. Az előfizetés átvitele után nem mehet vissza. Ennek a lépésnek a végrehajtása előtt győződjön meg róla, hogy elvégezte az előző lépéseket.

1. Döntse el, hogy a számlázási tulajdonjogot is át kívánja-e vinni egy másik fiókba.

1. Vigye át az előfizetést egy másik könyvtárba.

    - Ha meg szeretné tartani az aktuális számlázási tulajdonost, kövesse a [hozzárendelés vagy az Azure-előfizetés hozzáadása a Azure Active Directory bérlőhöz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)című témakör lépéseit.
    - Ha a számlázási tulajdonjogot is át szeretné vinni, kövesse az [Azure-előfizetés számlázási tulajdonjogának átadása másik fiókba](../cost-management-billing/manage/billing-subscription-transfer.md)című témakör lépéseit. Az előfizetés másik könyvtárba való átadásához ellenőriznie kell az **előfizetés Azure ad-bérlőt** jelölőnégyzetet.

1. Miután befejezte az előfizetés átadását, térjen vissza ehhez a cikkhez, és hozza létre újra az erőforrásokat a cél könyvtárában.

## <a name="step-3-re-create-resources"></a>3. lépés: erőforrások ismételt létrehozása

### <a name="sign-in-to-target-directory"></a>Bejelentkezés a célhely könyvtárba

1. A cél könyvtárában jelentkezzen be az átviteli kérelmet fogadó felhasználóként.

    Csak az adatátviteli kérést elfogadó új fiókban lévő felhasználó férhet hozzá az erőforrások kezeléséhez.

1. Szerezze be az előfizetések listáját az az [Account List](/cli/azure/account#az_account_list) paranccsal.

    ```azurecli
    az account list --output table
    ```

1. Az az [Account set](/cli/azure/account#az_account_set) paranccsal állíthatja be a használni kívánt aktív előfizetést.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Egyéni szerepkörök létrehozása
        
- Az [az role definition Create](/cli/azure/role/definition#az_role_definition_create) paranccsal hozzon létre minden egyéni szerepkört a korábban létrehozott fájlokból. További információ: [Azure-beli egyéni szerepkörök létrehozása vagy frissítése az Azure CLI használatával](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Szerepkörök hozzárendelése

- A szerepkörök felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz való hozzárendeléséhez használja [az az role hozzárendelés Create](/cli/azure/role/assignment#az_role_assignment_create) paranccsal. További információ: Azure- [szerepkörök kiosztása az Azure CLI használatával](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Rendszerhez rendelt felügyelt identitások frissítése

1. A rendszer által hozzárendelt felügyelt identitások letiltása és újbóli engedélyezése.

    | Azure-szolgáltatás | További információ | 
    | --- | --- |
    | Virtual machines (Virtuális gépek) | [Felügyelt identitások konfigurálása Azure-beli virtuális gépen lévő Azure-erőforrásokhoz az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Virtuálisgép-méretezési csoportok | [Felügyelt identitások konfigurálása Azure-erőforrásokhoz virtuálisgép-méretezési csoportokban az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Egyéb szolgáltatások | [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Az [az role hozzárendelés Create](/cli/azure/role/assignment#az_role_assignment_create) paranccsal rendelhet hozzá szerepköröket a rendszerhez rendelt felügyelt identitásokhoz. További információ: [felügyelt identitás-hozzáférés társítása az erőforrásokhoz az Azure CLI használatával](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások frissítése

1. Felhasználó által hozzárendelt felügyelt identitások törlése, újbóli létrehozása és csatolása.

    | Azure-szolgáltatás | További információ | 
    | --- | --- |
    | Virtual machines (Virtuális gépek) | [Felügyelt identitások konfigurálása Azure-beli virtuális gépen lévő Azure-erőforrásokhoz az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Virtuálisgép-méretezési csoportok | [Felügyelt identitások konfigurálása Azure-erőforrásokhoz virtuálisgép-méretezési csoportokban az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Egyéb szolgáltatások | [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése az Azure CLI használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Az [az szerepkör-hozzárendelési létrehozás](/cli/azure/role/assignment#az_role_assignment_create) használatával rendeljen hozzá szerepköröket a felhasználókhoz rendelt felügyelt identitásokhoz. További információ: [felügyelt identitás-hozzáférés társítása az erőforrásokhoz az Azure CLI használatával](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Kulcstartók frissítése

Ez a szakasz a kulcstartók frissítésének alapvető lépéseit ismerteti. További információ: [Azure Key Vault áthelyezése másik előfizetésre](../key-vault/general/move-subscription.md).

1. Frissítse az előfizetésben lévő összes meglévő kulcstartóhoz társított bérlői azonosítót a cél könyvtárba.

1. Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.

1. Adja hozzá a cél könyvtárhoz társított új hozzáférési szabályzatok bejegyzéseit.

### <a name="update-acls"></a>ACL-ek frissítése

1. Ha Azure Data Lake Storage Gen1 használ, rendelje hozzá a megfelelő ACL-eket. További információ: [Azure Data Lake Storage Gen1 tárolt adatok védelme](../data-lake-store/data-lake-store-secure-data.md).

1. Ha Azure Data Lake Storage Gen2 használ, rendelje hozzá a megfelelő ACL-eket. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](../storage/blobs/data-lake-storage-access-control.md).

1. Ha Azure Files használ, rendelje hozzá a megfelelő ACL-eket.

### <a name="review-other-security-methods"></a>Egyéb biztonsági módszerek áttekintése

Annak ellenére, hogy a szerepkör-hozzárendelések el lettek távolítva az átvitel során, előfordulhat, hogy az eredeti tulajdonos fiókban lévő felhasználók továbbra is hozzáférhetnek az előfizetéshez más biztonsági módszerekkel, például:

- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai.
- [Felügyeleti tanúsítványok](../cloud-services/cloud-services-certs-create.md) , amelyek hozzáférést biztosítanak a felhasználónak rendszergazdai hozzáféréshez az előfizetési erőforrásokhoz.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha szeretné eltávolítani a hozzáférést a forrás címtárban lévő felhasználóktól, hogy azok ne férhessenek hozzá a címtárhoz, érdemes megfontolnia a hitelesítő adatok elforgatását. A hitelesítő adatok frissítése után a felhasználók továbbra is hozzáférhetnek az átvitel után.

1. A Storage-fiók hozzáférési kulcsainak elforgatása. További információ: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md).

1. Ha más szolgáltatásokhoz (például Azure SQL Database vagy Azure Service Bus üzenetkezeléshez) használ hozzáférési kulcsokat, forgassa el a hozzáférési kulcsokat.

1. A titkos kulcsokat használó erőforrások esetében nyissa meg az erőforrás beállításait, és frissítse a titkot.

1. A tanúsítványokat használó erőforrások esetében frissítse a tanúsítványt.

## <a name="next-steps"></a>Következő lépések

- [Azure-előfizetés számlázási tulajdonjogának átadása másik fióknak](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Azure-előfizetések átvitele az előfizetők és a CSP-k között](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Az Azure Lighthouse nagyvállalati forgatókönyvekben](../lighthouse/concepts/enterprise.md)