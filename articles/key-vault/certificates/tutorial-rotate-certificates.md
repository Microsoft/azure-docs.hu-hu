---
title: Oktatóanyag – Tanúsítvány automatikus rotációs gyakoriságának frissítése a Key Vault | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan frissítheti egy tanúsítvány automatikus rotációs gyakoriságát Azure Key Vault a Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 48a531e054bf92c8ddc7761689b8fdf1df8a28a7
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750013"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Oktatóanyag: Tanúsítvány automatikus rotációja a Key Vault

A digitális tanúsítványokat egyszerűen kiépítheti, kezelheti és telepítheti a Azure Key Vault. A tanúsítványok egy hitelesítésszolgáltató (CA) által aláírt SSL (SSL)/Transport Layer Security (TLS) tanúsítványok vagy egy önaírt tanúsítvány is lehet. Key Vault tanúsítványokat is kérhetnek és újíthat meg a hitelesítésszolgáltatói partneri viszonyokkal, így robusztus megoldást kínálnak a tanúsítványok életciklusának kezelésére. Ebben az oktatóanyagban frissíteni fogja a tanúsítvány érvényességi időszakát, az automatikus rotáció gyakoriságát és a CA attribútumokat.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Tanúsítvány kezelése a Azure Portal.
> * Adjon hozzá egy hitelesítésszolgáltatói fiókot.
> * Frissítse a tanúsítvány érvényességi időszakát.
> * Frissítse a tanúsítvány automatikus rotációs gyakoriságát.
> * Frissítse a tanúsítvány attribútumát a Azure PowerShell.

Mielőtt hozzákezd, olvassa [el Key Vault alapfogalmakat.](../general/basic-concepts.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

Hozzon létre egy Azure Key Vault [az Azure Portal,](../general/quick-create-portal.md) [az Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell.](../general/quick-create-powershell.md) A példában a kulcstartó neve **Example-Vault.**

![Kimenet a Key Vault létrehozásának befejezése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tanúsítvány létrehozása a Key Vault

Hozzon létre egy tanúsítványt, vagy importálja a tanúsítványt a kulcstartóba [(lásd: Tanúsítvány](../secrets/quick-create-portal.md)létrehozása a Key Vault). Ebben az esetben egy **ExampleCertificate** nevű tanúsítvánnyal fog dolgozni.

## <a name="update-certificate-lifecycle-attributes"></a>Tanúsítvány életciklus-attribútumainak frissítése

A Azure Key Vault a tanúsítvány életciklus-attribútumait a tanúsítvány létrehozásakor vagy után is frissítheti.

A tanúsítványban létrehozott Key Vault a következő lehet:

- Önaírt tanúsítvány.
- Egy hitelesítésszolgáltatóval létrehozott tanúsítvány, amely a Key Vault.
- Olyan hitelesítésszolgáltatóval rendelkezik, amely nem partneri Key Vault.

Jelenleg az alábbi, felhőszolgáltatókkal partneri Key Vault:

- DigiCert: Key Vault OV TLS-/SSL-tanúsítványokat kínál.
- GlobalSign: Key Vault OV TLS-/SSL-tanúsítványokat kínál.

Key Vault automatikusan váltogatja a tanúsítványokat a hitelesítés tanúsítványokkal való partneri viszonyon keresztül. Mivel Key Vault tanúsítványokat a partnerkapcsolaton keresztül automatikusan leküld és újít meg, az automatikus rotáció nem alkalmazható olyan HITELESÍTÉS-szerződésekkel létrehozott tanúsítványokra, amelyek nincsenek partnerkapcsolatban a Key Vault.

> [!NOTE]
> A hitelesítésszolgáltató fiók-rendszergazdája olyan hitelesítő adatokat hoz létre, Key Vault TLS-/SSL-tanúsítványok létrehozására, megújítására és használatára használ.
![Hitelesítésszolgáltató](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>A tanúsítvány életciklus-attribútumainak frissítése a létrehozáskor

1. A tulajdonságokat Key Vault oldalon válassza a Tanúsítványok **lehetőséget.**
1. Válassza **a Generate/Import (Generálás/Importálás) lehetőséget.**
1. A Tanúsítvány **létrehozása képernyőn** frissítse a következő értékeket:

   - **Érvényességi időtartam:** Adja meg az értéket (hónapokban). A rövid életű tanúsítványok létrehozása ajánlott biztonsági eljárás. Alapértelmezés szerint az újonnan létrehozott tanúsítvány érvényességi értéke 12 hónap.
   - **Élettartam művelettípus:** Válassza ki a tanúsítvány automatikus megújítási  és riasztási műveletét, majd frissítse a százalékos élettartamot vagy a lejárat előtti **napok számát.** Alapértelmezés szerint a tanúsítvány automatikus megújítása az élettartam 80 százalékára van beállítva. A legördülő menüben válassza az alábbi lehetőségek egyikét.

      |  Automatikus megújítás egy adott időpontban| E-mail küldése az összes kapcsolattartónak egy adott időpontban |
      |-----------|------|
      |Ha ezt a lehetőséget *választja, bekapcsolja* az automatikus beállítást. | Ha ezt a lehetőséget választja, a rendszer *nem* váltja fel automatikusan, csak a kapcsolattartókat riasztja.|
      
      Az [e-mail-kapcsolattartó beállításával kapcsolatban itt olvashat.](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration)

1. Válassza a **Létrehozás** lehetőséget.

![Tanúsítvány-életciklus](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Tárolt tanúsítvány életciklus-attribútumainak frissítése

1. Válassza ki a kulcstartót.
1. A Tulajdonságok Key Vault oldalon válassza a Tanúsítványok **lehetőséget.**
1. Válassza ki a frissíteni kívánt tanúsítványt. Ebben az esetben egy **ExampleCertificate** nevű tanúsítvánnyal fog dolgozni.
1. A **felső menüsávon** válassza a Kiállítási szabályzat lehetőséget.

   ![A Kiállítási szabályzat gombot kikapcsoló képernyőkép.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. A **Kiállítási házirend képernyőn** frissítse a következő értékeket:

   - **Érvényességi időtartam:** Frissítse az értéket (hónapokban).
   - **Élettartam művelettípus:** Válassza ki a tanúsítvány automatikus megújítási és  riasztási műveletét, majd frissítse a lejárat előtti napok százalékos élettartamát vagy **számát.**

   ![Tanúsítvány tulajdonságai](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Egy tanúsítvány élettartam művelettípusának módosítása azonnal rögzíti a meglévő tanúsítványok módosításait.


### <a name="update-certificate-attributes-by-using-powershell"></a>Tanúsítványattribútumok frissítése a PowerShell használatával

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> A tanúsítványlista megújítási szabályzatának módosításához írja be a következőt: , amely a következő `File.csv` `VaultName,CertName` példában szerepel:
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
A paraméterekkel kapcsolatos további információkért lásd: [az keyvault certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Más Key Vault oktatóanyagok erre az oktatóanyagra épülnek. Ha azt tervezi, hogy ezekkel az oktatóanyagokkal dolgozik, érdemes ezeket a meglévő erőforrásokat a helyén hagyni.
Ha már nincs rájuk szüksége, törölje az erőforráscsoportot, amely törli a kulcstartót és a kapcsolódó erőforrásokat.

Az erőforráscsoport törlése a portál használatával:

1. Írja be az erőforráscsoport  nevét a portál tetején található Keresőmezőbe. Amikor az ebben a rövid útmutatóban használt erőforráscsoport megjelenik a keresési eredmények között, válassza ki.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítette egy tanúsítvány életciklus-attribútumát. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa a következő cikkekkel:

- További információ: [Tanúsítvány létrehozásának kezelése a Azure Key Vault.](./create-certificate-scenarios.md)
- Tekintse át a [Key Vault áttekintését.](../general/overview.md)
