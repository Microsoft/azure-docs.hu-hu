---
title: KeyVaultCertificateSelector FELHASZNÁLÓIFELÜLET-elem
description: Ismerteti a Microsoft. kulcstartó. KeyVaultCertificateSelector felhasználói felületi elemét Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101221"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft. kulcstartó. KeyVaultCertificateSelector FELHASZNÁLÓIFELÜLET-elem

Egy Key Vault-tanúsítvány kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

A felhasználó a rendelkezésre álló tanúsítvány kiválasztására szolgáló lehetőséggel jelenik meg.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. kulcstartó. KeyVaultCertificateSelector":::

A **tanúsítvány kiválasztása** után a felhasználó megadhat egy kulcstartót és egy tanúsítványt a kulcstartóból.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. kulcstartó. KeyVaultCertificateSelector – tanúsítvány kiválasztása":::

A vezérlő frissült a kiválasztott kulcstartó és a tanúsítvány nevének megjelenítéséhez.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. kulcstartó. KeyVaultCertificateSelector – kijelölt tanúsítvány megjelenítése":::

## <a name="schema"></a>Séma

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
