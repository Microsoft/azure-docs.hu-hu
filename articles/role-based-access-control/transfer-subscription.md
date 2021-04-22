---
title: Azure-előfizetés átvitele másik Azure AD-címtárba
description: Megtudhatja, hogyan továbbítható egy Azure-előfizetés és az ismert kapcsolódó erőforrások egy Azure Active Directory (Azure AD) címtárába.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868176"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Azure-előfizetés átvitele másik Azure AD-címtárba

Előfordulhat, hogy a szervezetek több Azure-előfizetéssel is rendelkezik. Minden előfizetés egy adott Azure Active Directory (Azure AD) címtárhoz van társítva. A felügyelet egyszerűbbé válik, ha egy előfizetést egy másik Azure AD-címtárba szeretne áthelyezni. Amikor egy előfizetést egy másik Azure AD-címtárba továbbít, egyes erőforrások nem kerülnek át a célkönyvtárba. Például az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) összes szerepkör-hozzárendelése és egyéni szerepköre véglegesen törlődik a forráskönyvtárból, és nem kerül át a célkönyvtárba. 

Ez a cikk ismerteti az előfizetés másik Azure AD-címtárba való átviteléhez és az erőforrások egy része az átvitel utáni újra létrehozásához szükséges alapvető lépéseket.

> [!NOTE]
> Az Azure Cloud Solution Providers (CSP) előfizetések esetében az előfizetés Azure AD-címtárának módosítása nem támogatott.

## <a name="overview"></a>Áttekintés

Az Azure-előfizetések másik Azure AD-címtárba való átadása összetett folyamat, amely körültekintően megtervez és végrehajt. Számos Azure-szolgáltatás megköveteli a rendszerbiztonsági tagokat (identitásokat) a normál működéshez, vagy akár más Azure-erőforrások kezeléséhez. Ez a cikk a legtöbb olyan Azure-szolgáltatást próbálja lefedni, amely nagy mértékben függ a rendszerbiztonsági tagtól, de nem átfogó.

> [!IMPORTANT]
> Bizonyos esetekben az előfizetés átvitele a folyamat befejezéséhez állásidőt vehet igénybe. Gondos tervezés szükséges annak kiértékelése érdekében, hogy szükség lesz-e állásidőre az átvitelhez.

Az alábbi ábrán az előfizetés másik címtárba való átvitelekor szükséges alapvető lépések láthatóak.

1. Felkészülés az átvitelre

1. Azure-előfizetés átvitele másik címtárba

1. Erőforrások, például szerepkör-hozzárendelések, egyéni szerepkörök és felügyelt identitások újralétrehozása a célkönyvtárban

    ![Előfizetés átvitele diagram](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Annak eldöntése, hogy át kell-e ruházni egy előfizetést egy másik címtárba

Az alábbiakban néhány okból kifolyólag érdemes lehet egy előfizetést áthelyezni:

- Egy vállalat összeolvadása vagy felvásárlása miatt egy megvásárolt előfizetést szeretne kezelni az elsődleges Azure AD-címtárban.
- A szervezet egyik tagja létrehozott egy előfizetést, ön pedig egy adott Azure AD-címtárba szeretné konszolidálni a felügyeletét.
- Olyan alkalmazásai vannak, amelyek egy adott előfizetés-azonosítótól vagy URL-címtől függnek, és nem könnyű módosítani az alkalmazás konfigurációját vagy kódját.
- A vállalkozás egy része különálló vállalatra lett felosztva, és egyes erőforrásait egy másik Azure AD-címtárba kell áthelyezni.
- Egyes erőforrásokat egy másik Azure AD-címtárban szeretne kezelni biztonsági elkülönítés céljából.

### <a name="alternate-approaches"></a>Alternatív megközelítések

Az előfizetés átvitele állásidőt igényel a folyamat befejezéséhez. A forgatókönyvtől függően a következő alternatív megközelítéseket érdemes megfontolni:

- Hozza létre újra az erőforrásokat, és másolja az adatokat a célkönyvtárba és az előfizetésbe.
- Több címtárból álló architektúra elfogadása, és az előfizetést hagyja a forráskönyvtárban. A Azure Lighthouse erőforrások delegálhatóak, így a célkönyvtár felhasználói hozzáférhetnek a forráskönyvtárban található előfizetéshez. További információ: Azure Lighthouse [forgatókönyvekben.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Az előfizetések átvitelének hatásai

Számos Azure-erőforrás függ egy előfizetéstől vagy egy címtártól. A helyzettől függően az alábbi táblázat az előfizetések átvitelének ismert hatását sorolja fel. A cikkben található lépések elvégzésével újra létrehozhat néhányat az előfizetés átvitele előtt meglévő erőforrásokból.

> [!IMPORTANT]
> Ez a szakasz felsorolja az előfizetéstől függő ismert Azure-szolgáltatásokat vagy -erőforrásokat. Mivel az Azure-ban az erőforrástípusok folyamatosan fejlődnek, előfordulhat, hogy további, itt nem szereplő függőségek is vannak, amelyek a környezete feltörését okozhatják. 

| Szolgáltatás vagy erőforrás | Hatással | Visszaszerezhető | Érintett? | Miket végezhet el? |
| --------- | --------- | --------- | --------- | --------- |
| Szerepkör-hozzárendelések | Igen | Yes | [Szerepkör-hozzárendelések felsorolása](#save-all-role-assignments) | Az összes szerepkör-hozzárendelés véglegesen törlődik. Felhasználókat, csoportokat és szolgáltatásnévket kell leképeznie a célkönyvtár megfelelő objektumaihoz. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Egyéni szerepkörök | Igen | Yes | [Egyéni szerepkörök listázása](#save-custom-roles) | Az összes egyéni szerepkör véglegesen törlődik. Újra létre kell hoznia az egyéni szerepköröket és a szerepkör-hozzárendeléseket. |
| Rendszer által hozzárendelt felügyelt identitások | Igen | Yes | [Felügyelt identitások listosítása](#list-role-assignments-for-managed-identities) | Le kell tiltania, majd újra engedélyeznie kell a felügyelt identitásokat. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Felhasználó által hozzárendelt felügyelt identitások | Igen | Yes | [Felügyelt identitások listosítása](#list-role-assignments-for-managed-identities) | Törölnie, újra létre kell hoznia, majd hozzá kell csatolnia a felügyelt identitásokat a megfelelő erőforráshoz. Újra létre kell hoznia a szerepkör-hozzárendeléseket. |
| Azure Key Vault | Igen | Yes | [A Key Vault szabályzatok listája](#list-key-vaults) | Frissítenie kell a kulcstartókhoz társított bérlőazonosítót. El kell távolítania és hozzá kell adni új hozzáférési szabályzatokat. |
| Azure SQL Azure AD-hitelesítés integrációjával rendelkező adatbázisok létrehozása | Igen | Nem | [Adatbázisok Azure SQL Azure AD-hitelesítéssel](#list-azure-sql-databases-with-azure-ad-authentication) | Nem továbbíthat olyan Azure SQL adatbázist, amely számára engedélyezve van az Azure AD-hitelesítés egy másik címtárba. További információ: [Azure Active Directory használata.](../azure-sql/database/authentication-aad-overview.md) | 
| Azure Storage és Azure Data Lake Storage Gen2 | Igen | Yes |  | Minden ACL-t újra létre kell hoznia. |
| Azure Data Lake Storage Gen1 | Igen | Yes |  | Minden ACL-t újra létre kell hoznia. |
| Azure Files | Igen | Yes |  | Minden ACL-t újra létre kell hoznia. |
| Azure File Sync | Igen | Yes |  | A társzinkronizálási szolgáltatás és/vagy tárfiók másik címtárba is áthelyezható. További információ: Gyakori kérdések [(GYIK) a Azure Files](../storage/files/storage-files-faq.md#azure-file-sync) |
| Azure Managed Disks | Igen | Yes |  |  Ha lemeztitkosítási készleteket használ a Managed Disks által felügyelt kulcsokkal való titkosításához, le kell tiltania és újra engedélyeznie kell a Lemeztitkosítási készletekhez társított rendszer által hozzárendelt identitásokat. És újra létre kell hoznia a szerepkör-hozzárendeléseket, azaz ismét meg kell adni a szükséges engedélyeket a Kulcstartók lemeztitkosítási készletei számára. |
| Azure Kubernetes Service | Igen | Nem |  | Az AKS-fürtöt és a hozzá tartozó erőforrásokat nem lehet másik könyvtárba áthelyezni. További információ: Gyakori kérdések a Azure Kubernetes Service [(AKS)](../aks/faq.md) |
| Azure Policy | Igen | Nem | Minden Azure Policy objektum, beleértve az egyéni definíciókat, hozzárendeléseket, kivételeket és megfelelőségi adatokat. | A [definíciókat exportálni,](../governance/policy/how-to/export-resources.md)importálni és újra hozzá kell rendelni. Ezután hozzon létre új szabályzat-hozzárendeléseket és a szükséges [szabályzat kivételeket.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory tartományi szolgáltatások | Igen | Nem |  | Felügyelt tartomány nem Azure AD Domain Services másik címtárba. További információ: Gyakori kérdések az Azure Active Directory [(AD) tartományi szolgáltatásokról](../active-directory-domain-services/faqs.md) |
| Alkalmazásregisztrációk | Igen | Yes |  |  |

> [!WARNING]
> Ha olyan erőforráshoz, például tárfiókhoz vagy SQL-adatbázishoz használ titkosítást, amely olyan  kulcstartótól függ, amely nem ugyanabban az előfizetésben van, mint az átvitel alatt áll, akkor az egy nem állítható vissza. Ilyen esetben érdemes egy másik kulcstartót használni, vagy ideiglenesen letiltani az ügyfél által kezelt kulcsokat, hogy elkerülje ezt a nem elérhető forgatókönyvet.

## <a name="prerequisites"></a>Előfeltételek

A lépések befejezéséhez a következőre lesz szüksége:

- [Bash az Azure Cloud Shell](../cloud-shell/overview.md) [vagy az Azure CLI-ban](/cli/azure)
- Az áthelyezni kívánt előfizetés fiók-rendszergazdája a forráskönyvtárban
- [Tulajdonosi](built-in-roles.md#owner) szerepkör a célkönyvtárban

## <a name="step-1-prepare-for-the-transfer"></a>1. lépés: Felkészülés az átvitelre

### <a name="sign-in-to-source-directory"></a>Bejelentkezés a forráskönyvtárba

1. Jelentkezzen be rendszergazdaként az Azure-ba.

1. Az az account list paranccsal lekérte [az előfizetések](/cli/azure/account#az_account_list) listáját.

    ```azurecli
    az account list --output table
    ```

1. Az [az account set használatával](/cli/azure/account#az_account_set) állítsa be az áthelyezni kívánt aktív előfizetést.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Az Azure Resource Graph bővítmény telepítése

 A [(Azure Resource Graph-graph)](../governance/resource-graph/index.yml)Azure *CLI-bővítménye* lehetővé teszi, hogy az [az graph](/cli/azure/graph) paranccsal lekérdezheti a Azure Resource Manager. Ezt a parancsot a későbbi lépésekben fogja használni.

1. Az [az extension list használatával](/cli/azure/extension#az_extension_list) nézze meg, hogy telepítve van-e a *resource-graph* bővítmény.

    ```azurecli
    az extension list
    ```

1. Ha nem, telepítse a *resource-graph* bővítményt.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Az összes szerepkör-hozzárendelés mentése

1. Használja az az role assignment list (az az [role assignment list)](/cli/azure/role/assignment#az_role_assignment_list) listában az összes szerepkör-hozzárendelést (beleértve az örökölt szerepkör-hozzárendeléseket is).

    A lista könnyebb áttekintése érdekében exportálhatja a kimenetet JSON, TSV vagy tábla formátumban. További információkért lásd: [Szerepkör-hozzárendelések felsorolása az Azure RBAC és az Azure CLI használatával.](role-assignments-list-cli.md)

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Mentse a szerepkör-hozzárendelések listáját.

    Előfizetés átvitelekor az összes szerepkör-hozzárendelés véglegesen törlődik, ezért fontos menteni egy másolatot. 

1. Tekintse át a szerepkör-hozzárendelések listáját. Előfordulhat, hogy a célkönyvtárban nem lesz szüksége szerepkör-hozzárendelésre.

### <a name="save-custom-roles"></a>Egyéni szerepkörök mentése

1. Használja az [az role definition list (az az role definition list)](/cli/azure/role/definition#az_role_definition_list) listában az egyéni szerepköröket. További információ: [Egyéni Azure-szerepkörök létrehozása](custom-roles-cli.md)vagy frissítése az Azure CLI használatával.

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Mentse az összes szükséges egyéni szerepkört a célkönyvtárban külön JSON-fájlként.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Másolatok létrehozása az egyéni szerepkörfájlokról.

1. Módosítsa az egyes másolatokat úgy, hogy az alábbi formátumot használják.

    Később ezekkel a fájlokkal fogja újra létrehozni az egyéni szerepköröket a célkönyvtárban.

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

### <a name="determine-user-group-and-service-principal-mappings"></a>Felhasználó-, csoport- és szolgáltatásnév-leképezések meghatározása

1. A szerepkör-hozzárendelések listája alapján határozza meg azokat a felhasználókat, csoportokat és szolgáltatásnévket, amelyekhez a célkönyvtárban leképezni fog.

    A rendszerbiztonsági tag típusát az egyes szerepkör-hozzárendelések `principalType` tulajdonságának segítségével azonosíthatja.

1. Szükség esetén a célkönyvtárban hozzon létre minden szükséges felhasználót, csoportot vagy szolgáltatásnévt.

### <a name="list-role-assignments-for-managed-identities"></a>Felügyelt identitások szerepkör-hozzárendelésének felsorolása

A felügyelt identitások nem frissülnek, amikor egy előfizetést egy másik címtárba továbbít. Ennek eredményeképpen a rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitások hibásak lesznek. Az átvitel után újra engedélyezheti a rendszer által hozzárendelt felügyelt identitásokat. Felhasználó által hozzárendelt felügyelt identitások esetében újra létre kell hoznia és csatolnia kell őket a célkönyvtárban.

1. Tekintse át [a felügyelt identitásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) támogató Azure-szolgáltatások listáját, és jegyezze fel, hogy hol használhat felügyelt identitásokat.

1. A rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitások felsorolásához használja az [az ad sp](/cli/azure/ad/sp#az_ad_sp_list) listát.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. A felügyelt identitások listájában határozza meg, hogy melyek a rendszer által hozzárendelt és a felhasználó által hozzárendelt identitások. A típus meghatározásához az alábbi feltételeket használhatja.

    | Feltételek | Felügyelt identitás típusa |
    | --- | --- |
    | `alternativeNames` tulajdonság tartalmazza a következőt: `isExplicit=False` | Rendszer által hozzárendelt |
    | `alternativeNames` tulajdonság nem tartalmazza a következőt: `isExplicit` | Rendszer által hozzárendelt |
    | `alternativeNames` tulajdonság tartalmazza a következőt: `isExplicit=True` | Felhasználó által hozzárendelt |

    A felhasználó által hozzárendelt felügyelt identitások listához használhatja az [az identity listát](/cli/azure/identity#az_identity_list) is. További információkért lásd: Felhasználó által hozzárendelt felügyelt identitás létrehozása, listába való felsorolása vagy [törlése az Azure CLI használatával.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

    ```azurecli
    az identity list
    ```

1. Szerezze be a felügyelt `objectId` identitások értékeinek listáját.

1. A szerepkör-hozzárendelések listájában keresse meg, hogy vannak-e szerepkör-hozzárendelések a felügyelt identitásokhoz.

### <a name="list-key-vaults"></a>Kulcstartók listába sorolva

Amikor létrehoz egy kulcstartót, az automatikusan ahhoz az előfizetéshez Azure Active Directory alapértelmezett bérlőazonosítóhoz lesz kötve, amelyben létre van hozva. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. További információkért lásd: [Azure Key Vault másik előfizetésbe.](../key-vault/general/move-subscription.md)

> [!WARNING]
> Ha olyan erőforráshoz, például tárfiókhoz vagy SQL-adatbázishoz használ titkosítást, amely olyan  kulcstartótól függ, amely nem ugyanabban az előfizetésben van, mint az átvitel alatt áll, akkor az egy nem állítható vissza. Ilyen esetben érdemes egy másik kulcstartót használni, vagy ideiglenesen letiltani az ügyfél által kezelt kulcsokat, hogy elkerülje ezt a nem elérhető forgatókönyvet.

- Ha rendelkezik kulcstartóval, használja [az az keyvault show](/cli/azure/keyvault#az_keyvault_show) használhatja a hozzáférési szabályzatok listához. További információ: [Key Vault hozzárendelése.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Adatbázisok Azure SQL Azure AD-hitelesítéssel

- Használja [az az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) és az az [graph](/cli/azure/graph) bővítményt, hogy lássa, használ-e Azure SQL adatbázisokat, amelyeken engedélyezve van az Azure AD-hitelesítés integrációja. További információ: [Sql-hitelesítés konfigurálása Azure Active Directory kezelése.](../azure-sql/database/authentication-aad-configure.md)

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>List ACL-ek

1. Ha a fájlokat használja Azure Data Lake Storage Gen1 listába sorolja fel a fájlokra alkalmazott ACL-eket a Azure Portal Vagy a PowerShell használatával.

1. Ha az alkalmazásokat Azure Data Lake Storage Gen2 listázó listát ad a fájlokra alkalmazott ACL-ek listájáról a Azure Portal Vagy a PowerShell használatával.

1. Ha a fájlokat Azure Files, listába sorolja fel az összes fájlra alkalmazott ACL-eket.

### <a name="list-other-known-resources"></a>Egyéb ismert erőforrások listása

1. Az [az account show használatával](/cli/azure/account#az_account_show) lekérte az előfizetés azonosítóját.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Az [az graph bővítmény](/cli/azure/graph) használatával felsorolja az ismert Azure AD-címtárfüggőségekkel kapcsolatos egyéb Azure-erőforrásokat.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>2. lépés: Az előfizetés átvitele

Ebben a lépésben áthelyezi az előfizetést a forráskönyvtárból a célkönyvtárba. A lépések eltérőek lesznek attól függően, hogy a számlázási tulajdonjogot is át szeretné-e ruházni.

> [!WARNING]
> Az előfizetés átvitelekor a forráskönyvtárban  található összes szerepkör-hozzárendelés véglegesen törlődik, és nem állítható vissza. Az előfizetés átvitele után nem lehet visszamenni. A lépés végrehajtása előtt végezze el az előző lépéseket.

1. Döntse el, hogy a számlázási tulajdonjogot is át szeretné-e ruházni egy másik fiókra.

1. Az előfizetés átvitele egy másik címtárba.

    - Ha meg szeretné tartani az aktuális számlázási tulajdonjogot, kövesse az Azure-előfizetés társítása vagy hozzáadása a saját Azure Active Directory [lépéseit.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
    - Ha a számlázási tulajdonjogot is át szeretné ruházni, kövesse az Azure-előfizetés számlázási tulajdonjogának átadása [másik fiókra lépéseit.](../cost-management-billing/manage/billing-subscription-transfer.md) Az előfizetés másik címtárba való átviteléhez be kell jelölje be az **Előfizetés Azure AD-bérlője** jelölőnégyzetet.

1. Miután befejezte az előfizetés átvitelét, térjen vissza ehhez a cikkhez, és hozza létre újra az erőforrásokat a célkönyvtárban.

## <a name="step-3-re-create-resources"></a>3. lépés: Erőforrások újra létrehozása

### <a name="sign-in-to-target-directory"></a>Bejelentkezés a célkönyvtárba

1. A célkönyvtárban jelentkezzen be az átadási kérelmet elfogadó felhasználóként.

    Csak az átadási kérelmet elfogadó új fiók felhasználója férhet hozzá az erőforrások kezeléséhez.

1. Az az account list paranccsal lekérte [az előfizetések](/cli/azure/account#az_account_list) listáját.

    ```azurecli
    az account list --output table
    ```

1. Az [az account set használatával](/cli/azure/account#az_account_set) állítsa be a használni kívánt aktív előfizetést.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Egyéni szerepkörök létrehozása
        
- Az [az role definition create használatával](/cli/azure/role/definition#az_role_definition_create) hozzon létre minden egyes egyéni szerepkört a korábban létrehozott fájlokból. További információ: [Egyéni Azure-szerepkörök](custom-roles-cli.md)létrehozása vagy frissítése az Azure CLI használatával.

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Szerepkörök hozzárendelése

- Az [az role assignment create használatával](/cli/azure/role/assignment#az_role_assignment_create) szerepköröket rendelhet felhasználókhoz, csoportokhoz és szolgáltatásnévhez. További információ: [Azure-szerepkörök hozzárendelése az Azure CLI használatával.](role-assignments-cli.md)

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Rendszer által hozzárendelt felügyelt identitások frissítése

1. Tiltsa le és engedélyezze újra a rendszer által hozzárendelt felügyelt identitásokat.

    | Azure-szolgáltatás | További információ | 
    | --- | --- |
    | Virtual machines (Virtuális gépek) | [Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Virtuálisgép-méretezési csoportok | [Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési készleten az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Egyéb szolgáltatások | [Az Azure-erőforrások felügyelt identitását támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Az [az role assignment create használatával](/cli/azure/role/assignment#az_role_assignment_create) rendelhet szerepköröket a rendszer által hozzárendelt felügyelt identitásokhoz. További információ: Felügyelt identitás [hozzáférésének hozzárendelése erőforráshoz az Azure CLI használatával.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások frissítése

1. Felhasználó által hozzárendelt felügyelt identitások törlése, újralétrehozás és csatolása.

    | Azure-szolgáltatás | További információ | 
    | --- | --- |
    | Virtual machines (Virtuális gépek) | [Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Virtuálisgép-méretezési csoportok | [Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési készleten az Azure CLI használatával](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Egyéb szolgáltatások | [Az Azure-erőforrások felügyelt identitását támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Felhasználó által hozzárendelt felügyelt identitás létrehozása, listának vagy törlése az Azure CLI használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Az [az role assignment create használatával](/cli/azure/role/assignment#az_role_assignment_create) rendelhet szerepköröket a felhasználó által hozzárendelt felügyelt identitásokhoz. További információ: Felügyelt identitás [hozzáférésének hozzárendelése erőforráshoz az Azure CLI használatával.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Kulcstartók frissítése

Ez a szakasz a kulcstartók frissítésének alapvető lépéseit ismerteti. További információ: [Előfizetés Azure Key Vault másik előfizetésbe.](../key-vault/general/move-subscription.md)

1. Frissítse az előfizetésben lévő összes meglévő kulcstartóhoz társított bérlőazonosítót a célkönyvtárra.

1. Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.

1. Adjon hozzá új, a célkönyvtárhoz társított hozzáférési házirend-bejegyzéseket.

### <a name="update-acls"></a>ACL-ek frissítése

1. Ha a megfelelő ACL-Azure Data Lake Storage Gen1 használja, rendelje hozzá a megfelelő ACL-eket. További információkért lásd: [Az](../data-lake-store/data-lake-store-secure-data.md)Azure Data Lake Storage Gen1.

1. Ha a megfelelő ACL-Azure Data Lake Storage Gen2 használja, rendelje hozzá a megfelelő ACL-eket. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](../storage/blobs/data-lake-storage-access-control.md).

1. Ha a megfelelő ACL-Azure Files használja, rendelje hozzá a megfelelő ACL-eket.

### <a name="review-other-security-methods"></a>Egyéb biztonsági módszerek áttekintése

Bár az átvitel során a szerepkör-hozzárendelések el vannak távolítva, az eredeti tulajdonosi fiók felhasználói továbbra is hozzáférhetnek az előfizetéshez más biztonsági módszerekkel, például:

- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai.
- [Felügyeleti tanúsítványok,](../cloud-services/cloud-services-certs-create.md) amelyek rendszergazdai hozzáférést engedélyeztek a felhasználónak az előfizetés erőforrásaihoz.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a cél a forráskönyvtárban található felhasználók hozzáférésének eltávolítása, hogy ne legyen hozzáférésük a célkönyvtárban, érdemes lehet a hitelesítő adatokat rotolni. A hitelesítő adatok frissítésáig a felhasználók továbbra is hozzáférhetnek az átvitel után.

1. Tárfiók hozzáférési kulcsának elforgatása. További információ: [Manage storage account access keys (Tárfiók hozzáférési kulcsának kezelése).](../storage/common/storage-account-keys-manage.md)

1. Ha más szolgáltatásokhoz, például a Azure SQL Database vagy az Azure Service Bus használ hozzáférési kulcsokat, váltsa át a hozzáférési kulcsokat.

1. A titkos okat használó erőforrásoknál nyissa meg az erőforrás beállításait, és frissítse a titkos gombra.

1. A tanúsítványokat használó erőforrások esetében frissítse a tanúsítványt.

## <a name="next-steps"></a>Következő lépések

- [Azure-előfizetés számlázási tulajdonjogának átadása másik fióknak](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Azure-előfizetések átvitele az előfizetők és a CSP-k között](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Az Azure Lighthouse nagyvállalati forgatókönyvekben](../lighthouse/concepts/enterprise.md)
