---
title: Beépített szabályzatdefiníciók a Azure Security Center
description: A Azure Policy beépített szabályzatdefinícióit Azure Security Center. Ezek a beépített szabályzatdefiníciók gyakori megközelítéseket biztosítanak az Azure-erőforrások kezeléséhez.
ms.date: 04/21/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 0fc5e2ed183020f32b59fec7f4436754467968e0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872418"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Beépített Azure Policy-definíciók az Azure Security Centerben

Ez az oldal a Azure Policy [beépített](../governance/policy/overview.md) szabályzatdefiníciók Azure Security Center indexe. A szabályzatdefiníciók következő csoportosításai érhetők el:

- A [kezdeményezési](#azure-security-center-initiatives) csoport felsorolja a Azure Policy "Security Center" kategóriában.
- Az [alapértelmezett kezdeményezési](#azure-security-center-initiatives) csoport felsorolja Azure Policy összes olyan definíciót, amely Security Center [azure-biztonsági](https://docs.microsoft.com/security/benchmark/azure/introduction)teljesítményteszt részét képezi. Ez a Microsoft által írt, széles körben alkalmazott teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.
- A [kategóriacsoport](#azure-security-center-category) a "Azure Policy" kategóriában található összes Security Center sorolja fel.

További információ a biztonsági szabályzatokkal kapcsolatban: [Working with security policies ..](./tutorial-security-policy.md) Az egyéb Azure Policy további beépített szolgáltatásaival kapcsolatban lásd a Azure Policy [definíciókat.](../governance/policy/samples/built-in-policies.md)

Az egyes beépített szabályzatdefiníciók neve a szabályzatdefinícióra mutató hivatkozásokat tartalmaz a Azure Portal. A Verzió oszlopban található **hivatkozással** megtekintheti a forrást a [GitHub Azure Policy tárban.](https://github.com/Azure/azure-policy)

## <a name="azure-security-center-initiatives"></a>Azure Security Center kezdeményezések

A következő táblázatban további információt Security Center beépített kezdeményezésekről:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center alapértelmezett kezdeményezése (Azure-biztonsági teljesítményteszt)

A következő táblázatban további információt Security Center figyelt beépített szabályzatokkal kapcsolatban:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategória

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a biztonsági szabályzatdefiníciók Azure Policy a Security Center. A kezdeményezésekkel, szabályzatokkal és a Security Center javaslataival kapcsolatos további információkért lásd: Mik azok a biztonsági szabályzatok, kezdeményezések és [javaslatok?](security-policy-concept.md).
