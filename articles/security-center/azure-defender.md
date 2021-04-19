---
title: A Azure Defender és az elérhető csomagok áttekintése
description: A Azure Defender, védelmi és riasztási csomagokat. Ezután engedélyezze Azure Defender az előfizetései számára a speciális biztonság érdekében.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0bd0d9c2230b8400aa3197044f944daceb93c715
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718520"
---
# <a name="introduction-to-azure-defender"></a>Bevezetés az Azure Defender használatába

Azure Security Center funkciói a felhőbiztonság két alappillérét fedik le:

- **Felhőbiztonsági környezet kezelése (CSPM)** – a Security Center minden Azure-felhasználó **számára** ingyenesen elérhető. Az ingyenes felhasználói élmény olyan CSPM-funkciókat tartalmaz, mint például a biztonsági pontszám, az Azure-beli gépek biztonsági hibás konfigurációinak észlelése, az eszközleltár stb. Ezekkel a CSPM-funkciókkal megerősítheti a hibrid felhő környezetét, és nyomon követheti a beépített szabályzatok megfelelőségét.

- Felhőbeli számítási feladatok védelme **(CWP)** – Security Center integrált felhőalapú számítási feladatvédelmi platformja (CWPP), **a Azure Defender**, fejlett, intelligens védelmet nyújt az Azure- és hibrid erőforrások és számítási feladatok számára. A Azure Defender ezen az oldalon leírtak szerint számos további biztonsági funkciót is elérhető. A beépített szabályzatok mellett, ha engedélyezte a Azure Defender, egyéni szabályzatokat és kezdeményezéseket is hozzáadhat. Szabályozási szabványokat – például az NIST-t és az Azure CIS-t – adhat hozzá, valamint az [Azure biztonsági](https://docs.microsoft.com/security/benchmark/azure/introduction) teljesítménytesztet a megfelelőség valóban testre szabott nézetének megtekintéséhez.

A Azure Defender irányítópultja Security Center biztosítja a CWP-funkciók láthatóságát és szabályozását a környezet számára:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Példa a Azure Defender irányítópultra" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Milyen erőforrástípusok Azure Defender biztonságossá?

Azure Defender biztonsági riasztásokat és fejlett fenyegetésvédelmet biztosít a virtuális gépekhez, SQL-adatbázisokhoz, tárolókhoz, webalkalmazásokhoz, a hálózathoz és sok máshoz.

Ha engedélyezi Azure Defender-t  a Azure Security Center Díjszabás és beállítások területén, a következő Defender-csomagok egyidejűleg vannak engedélyezve, és átfogó védelmet biztosítanak a környezet számítási, adat- és szolgáltatási rétegei számára:

- [Azure Defender kiszolgálókhoz](defender-for-servers-introduction.md)
- [Azure Defender App Service-hez](defender-for-app-service-introduction.md)
- [Azure Defender tároláshoz](defender-for-storage-introduction.md)
- [Azure Defender SQL-hez](defender-for-sql-introduction.md)
- [Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)
- [Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)
- [Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)
- [Azure Defender a Resource Managerhez](defender-for-resource-manager-introduction.md)
- [Azure Defender DNS-hez](defender-for-dns-introduction.md)

A fenti tervek mindegyikét külön ismertetjük az Security Center dokumentációjában.

> [!TIP]
> Azure Defender for IoT (előzetes verzió) egy különálló termék. Az összes részletet a [Introducing Azure Defender for IoT (Preview) (Az](../defender-for-iot/overview.md)előzetes verzió Azure Defender for IoT) rész tartalmazza. 

## <a name="hybrid-cloud-protection"></a>Hibrid felhővédelem

Az Azure-környezet védelme érdekében új Azure Defender is hozzáadhat a hibrid felhőkörnyezethez:

- Nem Azure-beli kiszolgálók védelme
- Más felhőkben (például az AWS-ben és a GCP-ben) lévő virtuális gépek védelme

Az adott környezetnek megfelelően testreszabott fenyegetésintelligencia- és rangsorolási riasztásokat kap, így a legfontosabb dolgokra összpontosíthat.

Ha ki szeretné terjeszteni a védelmet a más felhőkben vagy [](https://azure.microsoft.com/services/azure-arc/) a helyszínen található virtuális gépekre és SQL-adatbázisokra, üzembe kell helyeznie Azure Arc és engedélyeznie kell Azure Defender. kiszolgálói Azure Arc ingyenes szolgáltatás, de az Arc-kompatibilis kiszolgálókon (például Azure Defender) használt szolgáltatásokért a szolgáltatás díjszabása alapján kell fizetni. További információ: [Nem Azure-beli](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)gépek hozzáadása Azure Arc.

> [!TIP]
> Az AWS natív összekötői transzparens módon kezelik Azure Arc üzembe helyezést. További információ: [Az AWS-fiókok csatlakoztatása a Azure Security Center.](quickstart-onboard-aws.md)



## <a name="azure-defender-security-alerts"></a>Azure Defender biztonsági riasztások 

Amikor Azure Defender a környezet bármely területén fenyegetést észlel, biztonsági riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt javítási lépéseket, és bizonyos esetekben egy logikai alkalmazás aktiválásának lehetőségét.

Exportálhatja, hogy egy riasztást egy Security Center generált, Security Center egy integrált biztonsági terméktől kapott riasztás. Ha a riasztásokat Azure Sentinel, külső SIEM-be vagy bármely más külső eszközbe exportálni, kövesse a Stream-riasztások [SIEM-, SOAR- vagy IT-szolgáltatáskezelési](export-to-siem.md)megoldásba történő küldését.

> [!NOTE]
> A különböző forrásokból származó riasztások megjelenése különböző mennyiségű időt is vehet. A hálózati forgalom elemzését igénylő riasztások például hosszabb ideig is megjelenhetnek, mint a virtuális gépeken futó gyanús folyamatokhoz kapcsolódó riasztások.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender védelmi képességek

Azure Defender speciális elemzéseket használ az erőforrásokhoz kapcsolódó testre szabott javaslatokhoz. 

A védelmi intézkedések közé tartozik a virtuális gépek felügyeleti portjainak védelme a megfelelő időben való hozzáféréssel és adaptív alkalmazásvezérlőkkel, amelyek lehetővé teszik listák létrehozásához, hogy mely alkalmazások fusson és mit ne futtassanak a gépeken. 

Az irányítópulton található speciális védelemcsempék Azure Defender és konfigurálják ezeket a védelmi funkciókat. 

## <a name="vulnerability-assessment-and-management"></a>Biztonsági rések felmérése és kezelése

Azure Defender virtuális gépek és tárolóregisztrálók biztonsági rések vizsgálatával is jár többletköltség nélkül. A képolvasókat a Qualys működteti, de nincs szükség Qualys-licencre vagy Akár Qualys-fiókra sem – minden zökkenőmentesen kezelhető a Security Center. 

Tekintse át a biztonsági rések ellenőrzőeszközeiből származó eredményeket, és mindegyikre válaszoljon a Security Center. Ez közelebb Security Center, hogy az összes felhőbiztonsági erőfeszítés számára egy-egy ablaktáblát ássunk.

További információ a következő oldalakon található:

- [Security Center Azure-beli virtuális gépek biztonsági rések felmérésére vonatkozó integrált megoldása](deploy-vulnerability-assessment-vm.md)
- [Az Azure-beli tárolóregisztrálókban található rendszerképek biztonsági résének azonosítása](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudtuk, milyen előnyökkel jár a Azure Defender. 

> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](enable-azure-defender.md)