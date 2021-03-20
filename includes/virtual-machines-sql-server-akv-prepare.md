---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558388"
---
## <a name="prepare-for-akv-integration"></a>Felkészülés a AKV-integrációra
Ha Azure Key Vault-integrációt szeretne használni a SQL Server VM konfigurálásához, több előfeltétel van: 

1. [Az Azure PowerShell telepítése](#install)
2. [Azure Active Directory létrehozása](#register)
3. [Kulcstartó létrehozása](#createkeyvault)

Az alábbi szakaszok ismertetik ezeket az előfeltételeket, valamint azokat az információkat, amelyeket a PowerShell-parancsmagok későbbi futtatásához kell gyűjteni.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Azure PowerShell telepítése
Győződjön meg arról, hogy telepítette a legújabb Azure PowerShell modult. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-az-ps) foglalkozó témakörben talál.

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Alkalmazás regisztrálása a Azure Active Directoryban

Először is rendelkeznie kell egy [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (HRE) az előfizetésében. Számos előnnyel jár, így bizonyos felhasználók és alkalmazások számára engedélyezheti a kulcstartóhoz való hozzáférést.

Következő lépésként regisztráljon egy alkalmazást a HRE. Ez egy egyszerű szolgáltatásnév-fiókot biztosít, amely hozzáfér a kulcstartóhoz, amelyhez a virtuális gépnek szüksége lesz. A Azure Key Vault cikkben ezeket a lépéseket az [alkalmazás regisztrálása Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) szakaszban találhatja meg, vagy a [jelen blogbejegyzésben](/archive/blogs/kv/azure-key-vault-step-by-step)tekintheti meg az **alkalmazás identitásának beolvasása című szakaszának** lépéseit. A lépések elvégzése előtt össze kell gyűjtenie a következő információkat a regisztráció során, amely később szükséges, amikor engedélyezi Azure Key Vault integrációt az SQL-alapú virtuális gépen.

* Az alkalmazás hozzáadása után keresse meg az **alkalmazás azonosítóját** (más néven HRE ClientID vagy AppID) a **regisztrált alkalmazás** panelen.
    Az alkalmazás AZONOSÍTÓját később a PowerShell-parancsfájl **$spName** (egyszerű szolgáltatásnév) paramétereként rendeli hozzá a Azure Key Vault-integráció engedélyezéséhez.

   ![Alkalmazásazonosító](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* A kulcs létrehozásakor a következő lépések során másolja a kulcs titkát az alábbi képernyőképen látható módon. Ezt a titkos kulcsot később a PowerShell-parancsfájl **$spSecret** (egyszerű szolgáltatás titkos kulcsa) paramétereként rendeli hozzá a rendszer.

   ![Titkos HRE](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* A rendszer az alkalmazás AZONOSÍTÓját és a titkos kulcsot is felhasználja a hitelesítő adatok létrehozásához SQL Serverban.

* A következő hozzáférési engedélyekkel kell engedélyeznie az új alkalmazás-azonosítót (vagy ügyfél-azonosítót): **Get**, **wrapKey**, **unwrapKey**. Ez a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmaggal történik. További információ: [Azure Key Vault Overview (áttekintés](../articles/key-vault/general/overview.md)).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Kulcstartó létrehozása
Ahhoz, hogy a virtuális gépen a titkosításhoz használni kívánt kulcsokat a Azure Key Vault használatával lehessen tárolni, hozzá kell férnie egy kulcstartóhoz. Ha még nem állította be a kulcstartót, hozzon létre egyet a [Első lépések Azure Key Vault](../articles/key-vault/general/overview.md) című cikkben ismertetett lépéseket követve. A lépések elvégzése előtt néhány információt be kell gyűjtenie a beállítás során, amelyre később szükség van, amikor engedélyezi Azure Key Vault integrációt az SQL-alapú virtuális gépen.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Amikor megjelenik a Key Vault létrehozása lépés, jegyezze fel a visszaadott **vaultUri** tulajdonságot, amely a Key Vault URL-címe. Az ebben a lépésben megadott példában a Key Vault neve ContosoKeyVault, ezért a Key Vault URL-címe lenne https://contosokeyvault.vault.azure.net/ .

A Key Vault URL-címét később a PowerShell-parancsfájl **$akvURL** paramétereként rendeli hozzá a Azure Key Vault-integráció engedélyezéséhez.

A Key Vault létrehozása után fel kell venni egy kulcsot a kulcstartóba, ezt a kulcsot akkor kell megadnia, amikor az SQL Server később létrehozunk egy aszimmetrikus kulcsot.