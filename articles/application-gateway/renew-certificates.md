---
title: Azure Application Gateway-tanúsítvány megújítása
description: Útmutató az Application Gateway-figyelőhöz társított tanúsítvány megújításához.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 413ae2ee19f0b8e427de9167b52971e413cdf573
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397212"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway tanúsítványok megújítása

Egy bizonyos ponton meg kell újítania a tanúsítványokat, ha a TLS/SSL titkosításhoz konfigurálta az Application Gateway-t.

A figyelőhöz társított tanúsítvány megújítása a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával történhet:

## <a name="azure-portal"></a>Azure Portal

A figyelő tanúsítványának a portálról való megújításához navigáljon az Application Gateway-figyelőkhöz. Kattintson arra a figyelőre, amelynek meg kell újítania a tanúsítványát, majd kattintson a **megújítás vagy a kijelölt tanúsítvány szerkesztése** elemre.

![Tanúsítvány megújítása](media/renew-certificate/ssl-cert.png)

Töltse fel az új PFX-tanúsítványt, adjon meg egy nevet, írja be a jelszót, majd kattintson a **Save (Mentés** ) gombra.

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

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan konfigurálhatja a TLS-kiszervezést az Azure Application Gateway használatával, tekintse meg a [TLS-kiszervezés](./create-ssl-portal.md)