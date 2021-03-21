---
title: Oktatóanyag – a tanúsítvány automatikus elforgatási gyakoriságának frissítése Key Vaultban | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet frissíteni a tanúsítvány automatikus rotációs gyakoriságát Azure Key Vault a Azure Portal használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 78cdc0b59fd178049e755cca5e1e909ac24483f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204048"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Oktatóanyag: a tanúsítvány automatikus elforgatásának konfigurálása Key Vaultban

Azure Key Vault használatával könnyedén kiépítheti, kezelheti és telepítheti a digitális tanúsítványokat. A tanúsítványok lehetnek nyilvános és magánhálózati SSL (SSL)/Transport Layer Security (TLS) tanúsítványok, amelyeket a hitelesítésszolgáltató (CA) vagy egy önaláírt tanúsítvány írt alá. A Key Vault a CAs-vel való partneri kapcsolaton keresztül is kérheti és megújíthatja a tanúsítványokat, így robusztus megoldást biztosít a tanúsítványok életciklusának kezelésére. Ebben az oktatóanyagban frissíteni fogja a tanúsítvány érvényességi időtartamát, az automatikus rotációs gyakoriságot és a HITELESÍTÉSSZOLGÁLTATÓI attribútumokat.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Tanúsítvány kezelése a Azure Portal használatával.
> * HITELESÍTÉSSZOLGÁLTATÓI szolgáltatói fiók hozzáadása.
> * A tanúsítvány érvényességi idejének frissítése.
> * Frissítse a tanúsítvány automatikus rotációs gyakoriságát.
> * Frissítse a tanúsítvány attribútumait Azure PowerShell használatával.

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](../general/basic-concepts.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

Hozzon létre egy Azure Key Vaultt a [Azure Portal](../general/quick-create-portal.md), az [Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell](../general/quick-create-powershell.md)használatával. A példában a kulcstároló neve **például a-Vault**.

![Kimenet a Key Vault létrehozásának befejeződése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tanúsítvány létrehozása Key Vaultban

Hozzon létre egy tanúsítványt, vagy importáljon egy tanúsítványt a kulcstartóba (lásd: a [tanúsítvány létrehozásának lépései a Key Vault](../secrets/quick-create-portal.md)). Ebben az esetben egy **ExampleCertificate** nevű tanúsítványon fog dolgozni.

## <a name="update-certificate-lifecycle-attributes"></a>A tanúsítvány életciklus-attribútumainak frissítése

Azure Key Vault a tanúsítvány életciklus-attribútumait a tanúsítvány létrehozásának időpontja előtt és után is frissítheti.

Key Vault-ben létrehozott tanúsítvány a következőket teheti:

- Önaláírt tanúsítvány.
- Egy Key Vault-vel összekötött HITELESÍTÉSSZOLGÁLTATÓval létrehozott tanúsítvány.
- Olyan HITELESÍTÉSSZOLGÁLTATÓval rendelkező tanúsítvány, amely nem Key Vaulthoz van társítva.

A következő hitelesítésszolgáltatók jelenleg a Key Vaultkal rendelkező partneri szolgáltatók:

- DigiCert: Key Vault OV TLS/SSL-tanúsítványokat kínál.
- GlobalSign: Key Vault OV TLS/SSL-tanúsítványokat kínál.

Key Vault automatikusan elforgatja a tanúsítványokat a CAs használatával létesített partnerségek segítségével. Mivel Key Vault a tanúsítványok automatikus kérése és megújítása a partnerségen keresztül történik, az automatikus rotációs képesség nem alkalmazható olyan hitelesítésszolgáltatókkal létrehozott tanúsítványokra, amelyek nem a Key Vault-mel vannak társítva.

> [!NOTE]
> A HITELESÍTÉSSZOLGÁLTATÓI szolgáltatói fiók rendszergazdája olyan hitelesítő adatokat hoz létre, amelyeket Key Vault használ a TLS/SSL-tanúsítványok létrehozásához, megújításához és használatához.
![Hitelesítésszolgáltató](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>A tanúsítvány életciklus-attribútumainak frissítése a létrehozás időpontjában

1. A Key Vault tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
1. Válassza a **készítés/importálás** lehetőséget.
1. A **tanúsítvány létrehozása** képernyőn frissítse a következő értékeket:

   - **Érvényességi időszak**: adja meg az értéket (hónapokban). A rövid élettartamú tanúsítványok létrehozása ajánlott biztonsági eljárás. Alapértelmezés szerint az újonnan létrehozott tanúsítvány érvényességi értéke 12 hónap.
   - **Élettartam művelet típusa**: válassza ki a tanúsítvány automatikus megújítási és riasztási műveletét, majd frissítse a **százalékos élettartamot** vagy **a lejárat előtti napok számát**. Alapértelmezés szerint a tanúsítvány automatikus megújítása az élettartamának 80%-ában van beállítva. A legördülő menüben válassza az alábbi lehetőségek egyikét.

        |  Automatikus megújítás egy adott időpontban| Minden Névjegy küldése egy adott időpontban |
        |-----------|------|
        |Ha kiválasztja ezt a lehetőséget, *bekapcsolja* az autorotációt. | Ha ezt a beállítást választja, *nem* fog automatikusan elforgatni, de csak a névjegyeket fogja riasztani.|

1. Válassza a **Létrehozás** lehetőséget.

![Tanúsítvány életciklusa](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Tárolt tanúsítvány életciklus-attribútumainak frissítése

1. Válassza ki a kulcstartót.
1. A Key Vault tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
1. Válassza ki a frissíteni kívánt tanúsítványt. Ebben az esetben egy **ExampleCertificate** nevű tanúsítványon fog dolgozni.
1. Válassza ki a **kiállítási szabályzat** elemet a felső menüsorban.

   ![Képernyőfelvétel: a kiállítási szabályzat gombjának kiemelése.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. A **kiállítási szabályzat** képernyőn frissítse a következő értékeket:

   - **Érvényességi időszak**: frissítse az értéket (hónapokban).
   - **Élettartam művelet típusa**: válassza ki a tanúsítvány automatikus megújítási és riasztási műveletét, majd frissítse a **százalékos élettartamot** vagy a **lejárat előtti napok számát**.

   ![Tanúsítvány tulajdonságai](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Ha módosítja egy tanúsítvány élettartamának típusát, a rendszer azonnal rögzíti a meglévő tanúsítványok módosításait.


### <a name="update-certificate-attributes-by-using-powershell"></a>Tanúsítvány attribútumainak frissítése a PowerShell használatával

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> A tanúsítványok listájának megújítási szabályzatának módosításához írja be `File.csv` a `VaultName,CertName` következőket a következő példában látható módon:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
A paraméterekkel kapcsolatos további tudnivalókért tekintse meg az [az Key Vault Certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Más Key Vault oktatóanyagok erre az oktatóanyagra épülnek. Ha ezeket az oktatóanyagokat szeretné használni, érdemes lehet a meglévő erőforrásokat helyben hagyni.
Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, amely törli a kulcstartót és a kapcsolódó erőforrásokat.

Az erőforráscsoport törlése a portál használatával:

1. Adja meg az erőforráscsoport nevét a portál felső részén található **keresőmezőbe** . Ha az ebben a rövid útmutatóban használt erőforráscsoport megjelenik a keresési eredmények között, válassza ki.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. A **írja be az ERŐFORRÁSCSOPORT nevét:** mezőbe írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítette a tanúsítvány életciklus-attribútumait. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa a következő cikkekkel:

- További információ a [tanúsítványok létrehozásáról Azure Key Vaultban](./create-certificate-scenarios.md).
- Tekintse át a [Key Vault áttekintését](../general/overview.md).
