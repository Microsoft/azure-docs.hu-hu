---
title: Azure Security Center készültségi ütemterv | Microsoft Docs
description: Ez a dokumentum egy készültségi ütemtervet biztosít, amelynek segítségével gyorsan megismerkedhet az Azure Security Centerrel.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: e4f905b7a08564eadb20d65d651c137ef3741376
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100321"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center készültségi ütemterv
Ez a dokumentum egy készültségi ütemtervet tartalmaz, amely az Azure Security Center használatának első lépéseiben nyújt segítséget.

## <a name="understanding-security-center"></a>A Security Center megismerése
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. 

Használja az alábbi erőforrásokat a Security Center használatának elkezdéséhez.

Cikkek
- [Az Azure Security Center bemutatása](security-center-introduction.md)
- [Azure Security Center – rövid útmutató](security-center-get-started.md)

Videók
- [Rövid bemutatóvideó](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [A Security Center megelőzési, észlelési és kezelési képességeinek áttekintése](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Tervezés és műveletek

Ahhoz, hogy a lehető legnagyobb mértékben kihasználhassa a Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan használják a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos működésre, monitorozásra, irányításra és incidensmegoldásra vonatkozó követelményeket.

A következő forrásanyagok segítségére lehetnek a tervezési és műveleti folyamatok során.

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Számítógépek regisztrációja a Security Centerben
Security Center automatikusan észleli az Azure Defender által nem védett Azure-előfizetéseket vagy-munkaterületeket. Ebbe beletartozik az Azure-előfizetések Security Center ingyenes és munkaterületek használata, amelyeken nincs engedélyezve a biztonsági megoldás.

A következő forrásanyagok segítségére lehetnek a regisztrációs folyamatok során.

- [Nem Azure-beli számítógépek előkészítése](quickstart-onboard-machines.md)
- [Hibrid Azure Security Center – Áttekintés](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Biztonsági hibák csökkentése a Security Centerrel
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében.

A következő forrásanyagok segítségére lehetnek a biztonsági riasztások kezelésében és az erőforrások védelmében.

Cikkek    
- [Hálózat védelme az Azure Security Centerben](./security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és -adatok védelme az Azure Security Centerben](./security-center-remediate-recommendations.md)


Videó    
- [Biztonsági hibák csökkentése az Azure Security Center használatával](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>A Security Center használata incidensmegoldásra
A költségek és a károk csökkentése érdekében fontos, hogy a támadás megkezdése előtt egy incidensre vonatkozó válaszintézkedések legyenek érvényben. Az Azure Security Center az incidensmegoldás több szakaszában is alkalmazható.

Az alábbi forrásanyagokból megtudhatja, hogyan építhető be a Security Center az incidensmegoldási folyamatba.

Videók    
* [Az Azure Security Center használata incidensmegoldásra](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Gyors válasz a fenyegetésekre az új generációs biztonsági műveletekkel és vizsgálattal](https://youtu.be/e8iFCz5RM4g)

Cikkek    
* [Az Azure Security Center használata incidensmegoldásra](./tutorial-security-incident.md)
* [Az Automation használata Security Center eseményindítók megválaszolására](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Speciális felhővédelem

Az Azure-beli virtuális gépek kihasználhatják a Security Center speciális felhővédelmi képességeit. Ezen képességek közé tartozik az igény szerinti virtuális gépekhez való hozzáférés, valamint az adaptív alkalmazások vezérlése.

Az alábbi forrásanyagokból megismerheti, hogyan használhatja ezeket a képességeket a Security Centerben.

Videók    
* [Azure Security Center – igény szerinti virtuális gépekhez való hozzáférés](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Adaptív alkalmazásvezérlők](https://youtu.be/wWWekI1Y9ck)

Cikkek    
* [A virtuális gépekhez való hozzáférés kezelése igény szerint](./security-center-just-in-time.md)
* [Adaptív alkalmazás-vezérlőelemek Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Gyakorlati tevékenységek

* [Security Center – gyakorlati labor](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Webalkalmazási tűzfal (WAF) javasolt forgatókönyve a Security Centerben](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center-forgatókönyv: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>További források
* [Security Center – dokumentációs oldal](./index.yml)
* [Security Center – REST API-dokumentációs oldal](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure Security Center – gyakori kérdések (GYIK)](./faq-general.md)
* [Security Center – díjszabási oldal](https://azure.microsoft.com/pricing/details/security-center/)
* [Az identitásbiztonságra vonatkozó ajánlott eljárások](../security/fundamentals/identity-management-best-practices.md)
* [Ajánlott hálózati biztonsági eljárások](../security/fundamentals/network-best-practices.md)
* [PaaS-javaslatok](../security/fundamentals/paas-deployments.md)
* [Megfelelőség](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [A log Analytics-ügyfelek mostantól a Azure Security Center használatával védik a hibrid felhőalapú számítási feladatait](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Közösségi forrásanyagok

* [Security Center – UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Q&Security Center](/answers/topics/azure-security-center.html)