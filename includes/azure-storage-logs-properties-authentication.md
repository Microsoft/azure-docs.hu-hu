---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612290"
---
| Tulajdonság | Leírás |
|:--- |:---|
|**identitás/típus** | A kérelem elvégzéséhez használt hitelesítés típusa. Például: `OAuth` ,, `Kerberos` `SAS Key` , `Account Key` , vagy `Anonymous` |
|**identitás/tokenHash**|A kérelemben használt hitelesítési jogkivonat SHA-256 kivonata. <br>A hitelesítési típus esetén `Account Key` a formátum a következő: "key1 \| KEY2 (sha256 hash of the Key)". Példa: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>A hitelesítési típus esetén `SAS Key` a formátum a következő: "key1 \| KEY2 (SHA 256 hash of the Key), SASSIGNATURE (SHA 256 hash of the sas token)". Példa: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. A hitelesítés típusa esetén `OAuth` a formátum "SHA 256 hash of the OAuth token". Például: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Más hitelesítési típusok esetén nincs tokenHash mező. |
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
