---
title: Az Azure igazolási naplói
description: Az Azure-igazoláshoz gyűjtött teljes naplók leírása
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95758994"
---
# <a name="audit-logs-for-azure-attestation"></a>Az Azure-igazolás naplózási naplói

A naplófájlok biztonságos, nem módosítható, időközben megjelenő, diszkrét eseményekből álló rekordok. Ezek a naplók olyan fontos eseményeket rögzítenek, amelyek befolyásolhatják az igazolási példány funkcióit.

Az Azure igazolása kezeli az igazolási példányokat és a hozzájuk társított házirendeket. A rendszer naplózza és naplózza a példányok kezeléséhez és a házirendek változásaihoz kapcsolódó műveleteket.

Ez a cikk a naplózott eseményekről, a gyűjtött adatokról és a naplók helyéről tartalmaz információkat.

## <a name="about-audit-logs"></a>A naplók ismertetése

Az Azure-igazolás kód használatával hozza létre a naplókat az igazolás végrehajtásának módját befolyásoló események esetében. Ez általában arra szolgál, hogy az igazolási példányra és a rendszergazdai műveletekre vonatkozó házirend-változások hogyan vagy mikor történnek.

### <a name="auditable-events"></a>Naplózható események
Íme néhány az összegyűjtött naplók közül:

|     Esemény/API                              |     Esemény leírása                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Példány létrehozása                        |     Létrehoz egy igazolási szolgáltatás új példányát. |
|     Példány megsemmisítése                       |     Megsemmisíti az igazolási szolgáltatás egy példányát. |
|     Házirend-tanúsítvány hozzáadása                 |     Tanúsítvány hozzáadása a jelenlegi házirend-felügyeleti tanúsítványokhoz. |
|     Házirend-tanúsítvány eltávolítása              |     Tanúsítvány eltávolítása a jelenlegi házirend-felügyeleti tanúsítványok közül. |
|     Aktuális házirend beállítása                     |     Beállítja egy adott TEE-típus igazolási szabályzatát. |
|     Igazolási házirend alaphelyzetbe állítása               |     Visszaállítja egy adott TEE-típus igazolási szabályzatát. |
|     Felkészülés a szabályzat frissítésére               |     Felkészülés az adott TEE-típushoz tartozó igazolási szabályzat frissítésére. |
|     A bérlők rehidratálása katasztrófa után       |     Az igazolási szolgáltatás ezen példányán lévő összes igazolási bérlő ismételt lezárása. Ezt csak az igazolási szolgáltatás rendszergazdái végezheti el. |

### <a name="collected--information"></a>Összegyűjtött információk
Az Azure igazolása minden egyes esemény esetében a következő információkat gyűjti:

- Művelet neve
- Sikeres művelet
- A művelet hívója, amely a következők bármelyike lehet:
    - Azure AD UPN
    - Objektumazonosító
    - Tanúsítvány
    - Az Azure AD-bérlő azonosítója
- A műveleti cél, amely a következők bármelyike lehet:
    - Környezet
    - Szolgáltatási régió
    - Szolgáltatási szerepkör
    - Szolgáltatás szerepkör-példánya
    - Erőforrás-azonosító
    - Erőforrás-régió

### <a name="sample-audit-log"></a>Minta napló

A naplófájlok JSON formátumban vannak megadva. Íme egy példa arra, hogy a napló milyen módon nézhet ki.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Hozzáférés-naplózási naplók

Ezek a naplók az Azure-ban tárolódnak, és nem érhetők el közvetlenül. Ha el kell érnie ezeket a naplókat, a támogatási jegyet. További információ: [Contact Microsoft ügyfélszolgálata](https://azure.microsoft.com/support/options/). 

A támogatási jegy benyújtása után a Microsoft letölti és megadja a hozzáférést ezekhez a naplókhoz.
