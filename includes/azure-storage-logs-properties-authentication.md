---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750403"
---
| Tulajdonság | Leírás |
|:--- |:---|
|**identitás/típus** | A kérelem elvégzéséhez használt hitelesítés típusa. Például: `OAuth` ,, `Kerberos` `SAS Key` , `Account Key` , vagy `Anonymous` |
|**identitás/tokenHash**|Ez a mező csak belső használatra van fenntartva. |
|**engedélyezés/művelet** | A kérelemhez rendelt művelet. |
|**engedélyezés/roleAssignmentId** | A szerepkör-hozzárendelés azonosítója. Példa: `4e2521b7-13be-4363-aeda-111111111111`.|
|**engedélyezés/roleDefinitionId** | A szerepkör-definíció azonosítója. Példa: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**rendszerbiztonsági tag/azonosító** | A rendszerbiztonsági tag azonosítója. Példa: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**rendszerbiztonsági tag/típus** | A rendszerbiztonsági tag típusa. Példa: `ServicePrincipal`. |
|**kérelmező/appID** | A kérelmezőként használt Open Authorization-(OAuth-) alkalmazás azonosítója. <br> Példa: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**kérelmező/célközönség** | A kérelem OAuth célközönsége. Példa: `https://storage.azure.com`. |
|**kérelmező/objectId** | A kérelmező OAuth-objektumának azonosítója. Kerberos-hitelesítés esetén a a Kerberos által hitelesített felhasználó objektumazonosítót jelöli. Példa: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**kérelmező/tenantId** | Az identitás OAuth-bérlő azonosítója. Példa: `72f988bf-86f1-41af-91ab-222222222222`.|
|**kérelmező/tokenIssuer** | Az OAuth jogkivonat kiállítója. Példa: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**kérelmező/UPN** | A kérelmező egyszerű felhasználóneve (UPN). Példa: `someone@contoso.com`. |
|**kérelmező/Felhasználónév** | Ez a mező csak belső használatra van fenntartva.|
