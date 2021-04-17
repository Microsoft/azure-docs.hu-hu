---
title: Az Azure-beli virtuális gépekhez használt biztonsági funkciók
titleSuffix: Azure security
description: Ez a cikk áttekintést nyújt az Azure-beli biztonsági szolgáltatásokról, amelyek az Azure-beli virtuális Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 60f67ea618746c9f2b0cd65a9fbc7fb2b5fbfe86
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520003"
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure Virtual Machines biztonsági áttekintése
Ez a cikk áttekintést nyújt a virtuális gépekkel használható alapvető Azure biztonsági funkciókról.

Az Azure Virtual Machines számítástechnikai megoldások széles skáláját helyezheti üzembe agilis módon. A szolgáltatás a Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP és Azure BizTalk Services. Így szinte bármilyen számítási feladatot és nyelvet üzembe helyezhet szinte bármilyen operációs rendszeren.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. Az alkalmazásokat azzal a biztonsággal építheti ki és helyezheti üzembe, hogy adatai védve vannak és biztonságban vannak a rendkívül biztonságos adatközpontokban.

Az Azure-ral olyan, biztonságra továbbfejlesztett, megfelelő megoldásokat építhet, amelyek:

* A virtuális gépek vírusok és kártevők elleni védelme.
* Titkosítsa a bizalmas adatokat.
* Biztonságos hálózati forgalom.
* Fenyegetések azonosítása és észlelése.
* Megfelelés a megfelelőségi követelményeknek.  

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ral olyan biztonsági szállítóktól származó kártevőirtó szoftvereket használhat, mint a Microsoft, a Symantec, a Trend Micro és a Micro. Ez a szoftver segít megvédeni a virtuális gépeket a rosszindulatú fájloktól, adware-től és más fenyegetésektől.

Microsoft Antimalware a Azure Cloud Services és Virtual Machines valós idejű védelmi képesség, amely segít a vírusok, kémprogramok és más kártékony szoftverek azonosításában és eltávolításában.  Microsoft Antimalware Az Azure-hoz használható alkalmazás konfigurálható riasztásokat biztosít, ha az ismert rosszindulatú vagy nemkívánatos szoftverek megkísérlik telepíteni magát vagy futtatni az Azure-rendszereken.

Microsoft Antimalware Azure-hoz készült alkalmazás egy együgynök-alapú megoldás alkalmazásokhoz és bérlői környezetekhez. Úgy tervezték, hogy emberi beavatkozás nélkül fusson a háttérben. A védelmet az alkalmazás számítási feladatainak igényei alapján telepítheti alapszintű, alapértelmezés szerint biztonságos vagy speciális egyéni konfigurációval, beleértve a kártevőirtó-monitorozást is.

További információ az [Azure Microsoft Antimalware hoz való használatról](antimalware.md) és az elérhető alapvető funkciókról.

További információ a virtuális gépek védelmét segítő kártevőirtó szoftverekről:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Trend Micro Deep Security telepítése és konfigurálása szolgáltatásként Windows rendszerű virtuális gépen](/previous-versions/azure/virtual-machines/extensions/trend)
* [A Symantec Endpoint Protection telepítése és konfigurálása Windows rendszerű virtuális gépen](../../virtual-machines/extensions/symantec.md)
* [Biztonsági megoldások a Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

A még hatékonyabb védelem érdekében fontolja meg a [Windows Defender Advanced Threat Protection használatával.](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) Az Windows Defender ATP-val a következőt kapja meg:

* [Támadási felület csökkentése](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Következő generációs védelem](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Végpontvédelem és -válasz](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizált vizsgálat és szervizelés](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Biztonsági pontszám](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Speciális keresés](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Felügyelet és API-k](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft veszélyforrások elleni védelem](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

További információ:

* [A WDATP első lépések](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [A WDATP képességeinek áttekintése](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A kulcsbiztonság javítása javíthatja a titkosítás és a hitelesítés védelmét. A kritikus fontosságú titkos kulcsok és kulcsok kezelését és biztonságát leegyszerűsítheti, ha a kulcsokat egy Azure Key Vault.

Key Vault lehetőséget biztosít a kulcsok FIPS 140-2 2. szintű szabványoknak megfelelő hardveres biztonsági modulokban (HSM-ek) való tárolására. A SQL Server biztonsági mentés vagy [](/sql/relational-databases/security/encryption/transparent-data-encryption) a transzparens adattitkosítás titkosítási kulcsai mind tárolhatók a Key Vault az alkalmazásokból származó kulcsokkal vagy titkos kulcsokkal. Az engedélyeket és a védett elemekhez való hozzáférést a következő [Azure Active Directory.](https://azure.microsoft.com/documentation/services/active-directory/)

További információ:

* [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault blog](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemeztitkosítása

Azure Disk Encryption a Windows és Linux rendszerű virtuális gépek lemezei titkosításának új képessége. Azure Disk Encryption a Windows iparági szabványnak megfelelő [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját használja az operációs rendszer és az adatlemezek kötettitkosításához.

A megoldás integrálva van a Azure Key Vault, hogy vezérelje és kezelje a lemeztitkosítási kulcsokat és titkos kulcsokat a Key Vault-előfizetésben. Ez biztosítja, hogy a virtuális gépek lemezén lévő összes adat titkosítva van az Azure Storage-ban.

További információ:

* [Azure Disk Encryption IaaS virtuális gépekhez](./azure-disk-encryption-vms-vmss.md)
* [Rövid útmutató: Windows IaaS virtuális gép titkosítása az Azure PowerShell használatával](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése

Azure Backup egy skálázható megoldás, amely nulla tőkebefektetéssel és minimális üzemeltetési költségekkel védi az alkalmazásadatokat. Az alkalmazáshibák az adatok sérülését okozhatják, az emberi hibák pedig hibákat okozhatnak az alkalmazásokban. A Azure Backup Windowst és Linuxot futtató virtuális gépek védettek.

További információ:

* [Mi az az Azure Backup?](../../backup/backup-overview.md)
* [Azure Backup szolgáltatás – gyakori kérdések](../../backup/backup-azure-backup-faq.yml)

## <a name="azure-site-recovery"></a>Azure Site Recovery

A szervezet BCDR-stratégiájának fontos része annak kiderítésében, hogyan lehet a vállalati számítási feladatokat és alkalmazásokat futni a tervezett és nem tervezett leállások esetén. Azure Site Recovery segít a számítási feladatok és alkalmazások replikálásának, feladatátvételének és helyreállításának vezénylésében, hogy elérhetők egy másodlagos helyről, ha az elsődleges hely leáll.

Site Recovery:

* **Leegyszerűsíti a BCDR-stratégiát Site Recovery** megkönnyíti több üzleti számítási feladat és alkalmazás replikálása, feladatátvétele és helyreállítása egyetlen helyről történő kezelését. Site Recovery a replikációt és a feladatátvételt, de nem fogja el az alkalmazásadatokat, és nem tartalmaz róla semmilyen információt.
* **Rugalmas replikációt** biztosít: A Site Recovery hyper-V virtuális gépeken, VMware virtuális gépeken és Windows/Linux fizikai kiszolgálókon futó számítási feladatok replikálhatók.
* **Támogatja a feladatátvételt és a helyreállítást:** Site Recovery teszt feladatátvételt biztosít a vészhelyreállítási próbák támogatásához anélkül, hogy ez hatással lenne az éles környezetre. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel után visszaveheti a feladatátvételt az elsődleges helyekre. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Kiküszöböli a másodlagos adatközpontokat:** Replikálhat egy másodlagos helyszíni helyszínre vagy az Azure-ba. Az Azure vészhelyreállítási célként való használata kiküszöböli a másodlagos hely fenntartásával jár költségeket és összetettséget. A replikált adatokat az Azure Storage tárolja.
* **Integrálható a meglévő BCDR-technológiákkal:** Site Recovery más alkalmazások BCDR-funkcióival. Használhatja például a Site Recovery, hogy SQL Server a vállalati számítási feladatok háttér-rendszerét. Ez magában foglalja a SQL Server Always On natív támogatását a rendelkezésre állási csoportok feladatátvételének kezeléséhez.

További információ:

* [Mi az Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Hogy működik az Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Milyen számítási feladatokat véd a Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuális hálózat

A virtuális gépeknek hálózati kapcsolatra van szükségük. Ennek a követelménynek a támogatásához az Azure megköveteli a virtuális gépek Azure-beli virtuális hálózathoz való csatlakozását.

Az Azure-beli virtuális hálózat egy logikai szerkezet, amely a fizikai Azure-hálózati hálóra épül. Minden logikai Azure-beli virtuális hálózat el van különítve a többi Azure-beli virtuális hálózattól. Ezzel az elkülönítéssel biztosítható, hogy az üzemelő példányok hálózati forgalma ne legyen elérhető más Microsoft Azure ügyfelek számára.

További információ:

* [A nagyvállalati hálózati biztonság áttekintése](network-overview.md)
* [Virtual Network áttekintés](../../virtual-network/virtual-networks-overview.md)
* [Hálózati funkciók és partneri kapcsolatok nagyvállalati forgatókönyvekhez](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági szabályzatok kezelése és jelentése

Az Azure Security Center lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Security Center nagyobb rálátást és irányítást biztosít az Azure-erőforrások biztonságába. Integrált biztonsági monitorozást és szabályzatkezelést biztosít az Összes Azure-előfizetésben. Segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak, és a biztonsági megoldások széles ökoszisztémáját is képes együttműködni.

Security Center segítségével optimalizálhatja és figyelheti a virtuális gépek biztonságát a következővel:

* Biztonsági [javaslatok biztosítása a](../../security-center/security-center-recommendations.md) virtuális gépekhez. A javaslatok közé tartoznak például a rendszerfrissítések alkalmazása, az ACL-végpontok konfigurálása, a kártevőirtó engedélyezése, a hálózati biztonsági csoportok engedélyezése és a lemeztitkosítás alkalmazása.
* A virtuális gépek állapotának figyelése.

További információ:

* [Az Azure Security Center bemutatása](../../security-center/security-center-introduction.md)
* [Azure Security Center gyakori kérdések](../../security-center/faq-general.md)
* [Azure Security Center és üzemeltetés](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Megfelelőség

Az Azure Virtual Machines a FISMA, a FedRAMP, a HIPAA, PCI DSS Level 1 és egyéb kulcs megfelelőségi programok minősítésére is rendelkezik. Ezzel a tanúsítvánnyal a saját Azure-alkalmazásai könnyebben teljesítik a megfelelőségi követelményeket, és a vállalat számos belföldi és nemzetközi szabályozási követelményt is megfeleltethet.

További információ:

* [Microsoft Biztonsági és biztonsági központ: Megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Megbízható felhő: Microsoft Azure, adatvédelem és megfelelőség](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential Computing

Bár a bizalmas számítástechnika technikailag nem része a virtuális gépek biztonságának, a virtuális gépek biztonságának témaköre a "számítási" biztonság magasabb szintű témájához tartozik. A bizalmas számítástechnika a "számítási" biztonság kategóriájába tartozik.

A bizalmas számítástechnika biztosítja, hogy ha az adatok "egyértelműek", ami a hatékony feldolgozáshoz szükséges, akkor az adatok egy megbízható végrehajtási környezetben (TEE – más néven enklávéban) vannak védve, amely az alábbi ábrán látható  https://en.wikipedia.org/wiki/Trusted_execution_environment példa.  

A TEE-k biztosítják, hogy az adatokat és a műveleteket kívülről, még hibakeresővel sem lehet megtekinteni. Még azt is biztosítják, hogy csak az engedélyezett kódok férnek hozzá az adatokhoz. Ha a kódot módosítják vagy illetéktelenül módosítják, a rendszer megtagadja a műveleteket, és letiltja a környezetet. A TEE kikényszeríteni ezeket a védelmet a benne található kód végrehajtása során.

További információ:

* [Az Azure Confidential Computing bevezetése](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure Confidential Computing](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Következő lépések

Ismerje meg [a virtuális gépekhez](iaas.md) és operációs rendszerekhez ajánlott biztonsági eljárásokat.