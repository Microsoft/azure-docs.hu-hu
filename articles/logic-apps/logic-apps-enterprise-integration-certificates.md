---
title: Biztonságos B2B-üzenetek tanúsítványokkal
description: Tanúsítványok hozzáadása a Azure Logic Appsban található B2B-üzenetek biztonságossá tételéhez a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 03fc17c0d071cef4c8de92c6b50d60d961d18aef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565259"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>A B2B-üzenetek biztonságának fokozása tanúsítványokkal

Ha továbbra is meg kell őriznie a B2B-kommunikációt, a vállalati integrációs alkalmazásokban, különösen a Logic Appsben növelheti a B2B-kommunikáció biztonságát, ha tanúsítványokat ad hozzá az integrációs fiókjához. A tanúsítványok olyan digitális dokumentumok, amelyek az elektronikus kommunikáció résztvevői identitásait ellenőrizzék, és segítenek a kommunikációban az alábbi módokon:

* Üzenet tartalmának titkosítása.
* Üzenetek digitális aláírása.

Ezeket a tanúsítványokat a vállalati integrációs alkalmazásokban használhatja:

* [Nyilvános tanúsítványok](https://en.wikipedia.org/wiki/Public_key_certificate), amelyeket nyilvános internetes [hitelesítésszolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority) kell megvásárolnia, de nincs szükség kulcsokra. 

* Privát tanúsítványok vagy [*önaláírt tanúsítványok*](https://en.wikipedia.org/wiki/Self-signed_certificate), amelyeket Ön hoz létre és állít ki, de titkos kulcsokra is szükség van. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Ha olyan logikai alkalmazásokban szeretne *nyilvános tanúsítványt* használni, amelyek B2B-képességekkel rendelkeznek, először fel kell töltenie a tanúsítványt az integrációs fiókjába. Miután definiálta a tulajdonságokat a létrehozott [szerződésekben](logic-apps-enterprise-integration-agreements.md) , a tanúsítvány elérhetővé válik a B2B-üzenetek biztonságossá tételéhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjében válassza az **összes erőforrás** lehetőséget. A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Integrációs fiók megkeresése és kiválasztása](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Az **összetevők** területen válassza a **tanúsítványok** csempét.

   ![Válassza a tanúsítványok lehetőséget.](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. A **tanúsítványok** területen válassza a **Hozzáadás** lehetőséget. A **tanúsítvány hozzáadása** területen adja meg a tanúsítványhoz tartozó adatokat. Ha elkészült, válassza az **OK** gombot.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "publicCert". | 
   | **Tanúsítvány típusa** | Nyilvános | A tanúsítvány típusa |
   | **Tanúsítvány** | <*tanúsítvány-fájl neve*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kiválasztásához válassza a **tanúsítvány** mező melletti mappa ikont. |
   ||||

   ![Képernyőfelvétel: a Hozzáadás lehetőség kiválasztása a tanúsítvány részleteinek megadásához.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Miután az Azure érvényesíti a kiválasztott beállításokat, az Azure feltölti a tanúsítványt.

   ![Képernyőfelvétel: itt látható, hogy az Azure hol jelenítse meg az új tanúsítványt.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Ha a logikai alkalmazásokban a B2B-képességekkel rendelkező *privát tanúsítványt* szeretne használni, először fel kell töltenie a tanúsítványt az integrációs fiókjába. Szükség van egy titkos kulcsra is, amelyet először a [Azure Key Vaulthoz](../key-vault/general/overview.md)kell hozzáadnia. 

Miután definiálta a tulajdonságokat a létrehozott [szerződésekben](logic-apps-enterprise-integration-agreements.md) , a tanúsítvány elérhetővé válik a B2B-üzenetek biztonságossá tételéhez.

> [!NOTE]
> Privát tanúsítványok esetén ügyeljen arra, hogy adjon hozzá egy megfelelő nyilvános tanúsítványt, amely megjelenik az [AS2-egyezmény](logic-apps-enterprise-integration-as2.md) **küldési és fogadási** beállításaiban az üzenetek aláírásához és titkosításához.

1. [Adja hozzá a titkos kulcsot Azure Key Vaulthoz](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) , és adja meg a **kulcs nevét**.
   
2. Engedélyezi Azure Logic Apps számára, hogy műveleteket hajtson végre a Azure Key Vaulton. Ha hozzáférést szeretne biztosítani az Logic Apps egyszerű szolgáltatáshoz, használja a következő PowerShell [-parancsot: set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), például:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjében válassza az **összes erőforrás** lehetőséget. A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Integrációs fiók megkeresése](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Az **összetevők** területen válassza a **tanúsítványok** csempét.  

   ![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. A **tanúsítványok** területen válassza a **Hozzáadás** lehetőséget. A **tanúsítvány hozzáadása** területen adja meg a tanúsítványhoz tartozó adatokat. Ha elkészült, válassza az **OK** gombot.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "privateCert". | 
   | **Tanúsítvány típusa** | Személyes | A tanúsítvány típusa |
   | **Tanúsítvány** | <*tanúsítvány-fájl neve*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kiválasztásához válassza a **tanúsítvány** mező melletti mappa ikont. Ha kulcstartót használ a titkos kulcshoz, a feltöltött fájl lesz a nyilvános tanúsítvány. | 
   | **Erőforráscsoport** | <*integráció – fiók – erőforrás-csoport*> | Az integrációs fiókhoz tartozó erőforráscsoport, amely ebben a példában a "MyResourceGroup". | 
   | **Key Vault** | <*Key-Vault-név*> | Az Azure Key Vault neve |
   | **Kulcs neve** | <*kulcs neve*> | A kulcs neve |
   ||||

   ![Válassza a Hozzáadás lehetőséget, adja meg a tanúsítvány részleteit](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Miután az Azure érvényesíti a kiválasztott beállításokat, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítványt jelenít meg](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Következő lépések

* [B2B-szerződés létrehozása](logic-apps-enterprise-integration-agreements.md)
