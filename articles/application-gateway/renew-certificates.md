---
title: Azure Application Gateway-tanúsítvány megújítása
description: Útmutató az Application Gateway-figyelőhöz társított tanúsítvány megújításához.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622180"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway tanúsítványok megújítása

Egy bizonyos ponton meg kell újítania a tanúsítványokat, ha a TLS/SSL titkosításhoz konfigurálta az Application Gateway-t.

A figyelőhöz társított tanúsítvány megújítása a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával történhet:

## <a name="azure-portal"></a>Azure Portal

A figyelő tanúsítványának a portálról való megújításához navigáljon az Application Gateway-figyelőkhöz. Válassza ki azt a figyelőt, amelynek meg kell újítania a tanúsítványt, majd válassza a **megújítása vagy a kijelölt tanúsítvány szerkesztése** lehetőséget.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Tanúsítvány megújítása":::

Töltse fel az új PFX-tanúsítványt, adjon meg egy nevet, írja be a jelszót, majd kattintson a **Mentés** gombra.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha Azure PowerShell használatával szeretné megújítani a tanúsítványt, használja a következő parancsfájlt:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan konfigurálhatja a TLS-kiszervezést az Azure Application Gateway használatával, tekintse meg a [TLS-kiszervezés](./create-ssl-portal.md)