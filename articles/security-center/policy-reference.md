---
title: Beépített szabályzatdefiníciók a Azure Security Center
description: A Azure Policy beépített szabályzatdefinícióit Azure Security Center. Ezek a beépített szabályzatdefiníciók gyakori megközelítéseket biztosítanak az Azure-erőforrások kezeléséhez.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 34a839eb6996f2b37c07bb2e54f81a1d21044fa9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502690"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Beépített Azure Policy-definíciók az Azure Security Centerben

Ez az oldal a Azure Policy [beépített](../governance/policy/overview.md) szabályzatdefiníciók Azure Security Center indexe. A szabályzatdefiníciók következő csoportosításai érhetők el:

- A [kezdeményezési](#azure-security-center-initiatives) csoport felsorolja a Azure Policy "Security Center" kategóriában.
- Az [alapértelmezett kezdeményezési](#azure-security-center-initiatives) csoport felsorolja Azure Policy összes olyan definíciót, amely Security Center [azure-biztonsági](../security/benchmarks/introduction.md)teljesítményteszt részét képezi. Ez a Microsoft által írt, széles körben alkalmazott teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.
- A [kategóriacsoport](#azure-security-center-category) a "Azure Policy" kategóriában található összes Security Center sorolja fel.

További információ a biztonsági szabályzatokkal kapcsolatban: [Working with security policies ..](./tutorial-security-policy.md) Az egyéb Azure Policy további beépített szolgáltatásaiért lásd a Azure Policy [definíciókat.](../governance/policy/samples/built-in-policies.md)

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
