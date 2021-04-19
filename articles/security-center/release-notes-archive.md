---
title: A Azure Security Center
description: A hat hónappal ezelőtti és korábbi Azure Security Center újdonságok és módosult funkciók leírása.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/04/2021
ms.author: memildin
ms.openlocfilehash: 9d376a374d1934f55b6a6fb15f1642c81b30b2fc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718664"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiválás a Azure Security Center?

Az [újdonságok az Azure Security Center?](release-notes.md) kibocsátási megjegyzések oldalon az elmúlt hat hónap frissítései, míg ez az oldal régebbi elemeket tartalmaz.

Ez az oldal a következő információkkal rendelkezik:

- Új funkciók
- Hibajavítások
- Elavult funkciók


## <a name="october-2020"></a>2020. október

Októberi frissítések:
- [Biztonságirés-felmérés a többfelhős és a többfelhős gépeken (előzetes verzió)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall hozzáadott javaslat (előzetes verzió)](#azure-firewall-recommendation-added-preview)
- [Az engedélyezett IP-címtartományokat a Kubernetes Services-javaslatban kell definiálni, gyorsjavítással frissítve](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [A jogszabályi megfelelési irányítópult mostantól tartalmazza a szabványok eltávolításának beállítását](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft.Security/securityStatuses tábla eltávolítva a Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Biztonságirés-felmérés a többfelhős és a többfelhős gépeken (előzetes verzió)

[Azure Defender](defender-for-servers-introduction.md)qualys által működtetett integrált biztonsági rések felmérésére használható ellenőrzőeszköz mostantól Azure Arc kiszolgálókon.

Ha engedélyezte a Azure Arc a nem Azure-beli gépeken, a Security Center lehetővé fogja tenni az integrált biztonsági rések ellenőrzőeszközének manuális és nagy léptékű üzembe helyezését.

Ezzel a frissítéssel felszabadíthatja  a kiszolgálók Azure Defender áramot, hogy összevonja a sebezhetőségkezelési programot az összes Azure-beli és nem Azure-beli eszközre.

Fő képességek:

- A biztonsági rések felmérése (VA) olvasó kiépítési állapotának monitorozása Azure Arc gépeken
- Az integrált VA-ügynök üzembe Azure Arc Windows és Linux rendszerű virtuális gépeken (manuálisan és nagy léptékben)
- Az üzembe helyezett ügynökök által észlelt biztonsági rések fogadása és elemzése (manuálisan és nagy léptékben)
- Egységes felhasználói élmény Azure-beli virtuális gépekhez és Azure Arc gépekhez

[További információ az integrált sebezhetőségi ellenőrzőeszköz hibrid gépekre való telepítésével kapcsolatban.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[További információ a Azure Arc kiszolgálókról.](../azure-arc/servers/index.yml)


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall javaslat hozzáadása (előzetes verzió)

Új javaslat lett hozzáadva, amely az összes virtuális hálózat védelmét Azure Firewall.

Javasoljuk, hogy a virtuális **hálózatokat** az Azure Firewall javasolja, hogy korlátozza a virtuális hálózatokhoz való hozzáférést, és az esetleges fenyegetések megelőzése érdekében használja Azure Firewall.

További információ a [Azure Firewall.](https://azure.microsoft.com/services/azure-firewall/)


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Az engedélyezett IP-címtartományokat a Gyorsjavítással frissített Kubernetes Services-javaslatban kell meghatározni

A **Kubernetes Servicesben** most már van egy gyorsjavítási lehetőség az Engedélyezett IP-tartományok beállítás definiálva.

A javaslatról és az egyéb javaslatokról további Security Center lásd: Biztonsági [javaslatok – referencia-útmutató.](recommendations-reference.md)

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Az engedélyezett IP-címtartományokat a Kubernetes Services-javaslatban kell meghatározni a gyorsjavítási beállítással":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>A jogszabályi megfelelési irányítópult mostantól tartalmazza a szabványok eltávolításának beállítását

Security Center megfelelőségi irányítópultja betekintést nyújt a megfelelőségi környezetbe az alapján, hogy hogyan felel meg az adott megfelelőségi vezérlőknek és követelményeknek.

Az irányítópult tartalmaz egy alapértelmezett szabályozási szabványkészletet. Ha a megadott szabványok bármelyike nem releváns a szervezet számára, mostantól egyszerű folyamat eltávolítani őket az előfizetés felhasználói felületéből. A szabványok csak az előfizetés szintjén *távolíthatók* el; nem a felügyeleti csoport hatókörét.

További információ: [Standard eltávolítása az irányítópultról.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft.Security/securityStatuses tábla eltávolítva a Azure Resource Graph (ARG)

Azure Resource Graph szolgáltatás az Azure-ban, amely hatékony erőforrás-feltárást biztosít, és lehetővé teszi a nagy léptékű lekérdezést az előfizetések egy adott készletében, így hatékonyan irányíthatja a környezetet. 

A Azure Security Center az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával számos biztonsági adatokat lekérdezhet. Például:

- Az eszközleltár kihasznált eszközei (ARG)
- Dokumentáltunk egy minta ARG-lekérdezést a többtényezős hitelesítés [(MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled) nélküli fiókok azonosításához

Az ARG-ban adattáblákat használhat a lekérdezésekben.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer és az elérhető táblák":::

> [!TIP]
> Az ARG dokumentációja felsorolja a táblázatban Azure Resource Graph és erőforrástípus-referencia [összes elérhető tábláját.](../governance/resource-graph/reference/supported-tables-resources.md)

Ebben a frissítésben a **Microsoft.Security/securityStatuses tábla** el lett távolítva. A securityStatuses API továbbra is elérhető.

Az adatcserét a Microsoft.Security/Assessments tábla használhatja.

A Microsoft.Security/securityStatuses és a Microsoft.Security/Assessments közötti fő különbség az, hogy bár az első az értékelések összesítését jeleníti meg, a másodpercek mindegyikhez egyetlen rekordot rögzítnek.

A Microsoft.Security/securityStatuses például két szabályzatot tartalmazó tömböt ad visszaÉrtékelések:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Míg a Microsoft.Security/Assessments minden ilyen szabályzatértékeléshez rekordot rögzít, az alábbiak szerint:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Példa meglévő ARG-lekérdezés securityStatuses használatával való átalakítására az assessments tábla használatára:**

A SecurityStatuses lekérdezésre hivatkozó lekérdezés:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

A Assessments tábla helyettesítő lekérdezése:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

További információért olvassa el az alábbi hivatkozásokat:
- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020. szeptember

Szeptemberi frissítések a következők:
- [Security Center új megjelenést kap!](#security-center-gets-a-new-look)
- [Azure Defender megjelent](#azure-defender-released)
- [Azure Defender a Key Vault általánosan elérhető](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender fájlok és fájlok storage-ADLS Gen2 általánosan elérhető](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Általánosan elérhetők az eszközleltár-eszközök](#asset-inventory-tools-are-now-generally-available)
- [Adott biztonsági rések keresésének letiltása tárolóregisztrálók és virtuális gépek keresésére](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Erőforrás kivonása egy javaslatból](#exempt-a-resource-from-a-recommendation)
- [AWS- és GCP-összekötők Security Center többfelhős környezetben](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes számítási feladatok védelmének javaslatcsomagja](#kubernetes-workload-protection-recommendation-bundle)
- [A sebezhetőségi felmérés eredményei már elérhetők a folyamatos exportálásban](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [A biztonsági hibás konfigurációk megelőzése a javaslatok kényszerítése új erőforrások létrehozásakor](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Továbbfejlesztett hálózati biztonsági csoportokra vonatkozó javaslatok](#network-security-group-recommendations-improved)
- [Elavult előzetes verziójú AKS-javaslat: "Podbiztonsági szabályzatokat kell meghatározni a Kubernetes Servicesben"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Továbbfejlesztett e-Azure Security Center e-mail-értesítések](#email-notifications-from-azure-security-center-improved)
- [A biztonsági pontszám nem tartalmazza az előzetes verzióra vonatkozó javaslatokat](#secure-score-doesnt-include-preview-recommendations)
- [A javaslatok mostantól tartalmaznak egy súlyossági jelzőt és a frissességi időközt](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center új megjelenést kap!

Frissített felhasználói felületet adtunk ki a Security Center portáloldalakhoz. Az új oldalak új áttekintő oldalt és irányítópultokat tartalmaznak a biztonsági pontszám, az eszközleltár és a Azure Defender.

Az újratervezett áttekintő oldal már tartalmaz egy csempét a biztonsági pontszám, az eszközleltár és a Azure Defender eléréséhez. Emellett egy csempe is található rajta, amely a jogszabályi megfelelési irányítópultra hivatkozik.

További információ az [áttekintő oldalról:](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender megjelent

**Azure Defender** az Azure-beli és a hibrid számítási feladatok fejlett, intelligens védelme érdekében Security Center felhőalapú számítási feladatok védelmi platformja (CWPP). A Security Center tarifacsomagot váltja fel. 

Ha engedélyezi Azure Defender-t  a Azure Security Center Díjszabás és beállítások területén, a következő Defender-csomagok egyidejűleg vannak engedélyezve, és átfogó védelmet biztosítanak a környezet számítási, adat- és szolgáltatási rétegei számára:

- [Azure Defender kiszolgálókhoz](defender-for-servers-introduction.md)
- [Azure Defender App Service-hez](defender-for-app-service-introduction.md)
- [Azure Defender tároláshoz](defender-for-storage-introduction.md)
- [Azure Defender SQL-hez](defender-for-sql-introduction.md)
- [Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)
- [Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)
- [Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)

A fenti tervek mindegyikét külön ismertetjük az Security Center dokumentációjában.

A dedikált irányítópulttal a Azure Defender biztonsági riasztásokat és fejlett fenyegetésvédelmet biztosít a virtuális gépek, AZ SQL-adatbázisok, a tárolók, a webalkalmazások és a hálózat számára.

[További információ a Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender a Key Vault általánosan elérhető

Azure Key Vault egy felhőszolgáltatás, amely védi a titkosítási kulcsokat és titkos kulcsokat, például a tanúsítványokat, a kapcsolati sztringeket és a jelszavakat. 

**Azure Defender a Key Vault** azure-natív, fejlett fenyegetésvédelmet biztosít a Azure Key Vault számára, és egy további biztonsági intelligenciát biztosít. Ez azt is Azure Defender, Key Vault a fióktól függő számos erőforrás védelme Key Vault függ.

A választható csomag mostantól GA. Ez a funkció előzetes verzióban "advanced threat protection for Azure Key Vault" volt.

Emellett a Key Vault oldalának Azure Portal egy dedikált Biztonsági oldalt  is Security Center **javaslatokhoz** és riasztásokhoz.

További információ: [Azure Defender a Key Vault.](defender-for-key-vault-introduction.md)


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender fájlok és fájlok storage-ADLS Gen2 általánosan elérhető 

**Azure Defender Storage szolgáltatás észleli** az Azure Storage-fiókok potenciálisan káros tevékenységét. Adatai védve vannak akár blobtárolóként, fájlmegosztásként, akár data lake-ként.

Általánosan [elérhető a Azure Files](../storage/files/storage-files-introduction.md) és [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) támogatása.

2020. október 1-től megkezdjük a szolgáltatások erőforrásainak védelmét.

További információ: [Azure Defender Storage.](defender-for-storage-introduction.md)


### <a name="asset-inventory-tools-are-now-generally-available"></a>Általánosan elérhetők az eszközleltár-eszközök

A Azure Security Center eszközleltárlapja egyetlen oldalt biztosít a csatlakoztatott erőforrások biztonsági állásának Security Center.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a lehetséges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldásával kapcsolatban.

Ha valamelyik erőforrás függőben lévő javaslatokkal rendelkezik, azok megjelennek a leltárban.

További információ: Erőforrások feltárása és [kezelése az eszközleltárával.](asset-inventory.md)



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Adott biztonsági rések keresésének letiltása tárolóregisztrálók és virtuális gépek keresésére

Azure Defender biztonsági réseket ellenőrző képolvasókat tartalmaz, amelyek a Azure Container Registry virtuális gépeken található rendszerképeket vizsgálnak.

Ha a cégnek nem kell figyelembe vennie a kereséseket, és nem kell orvosolni őket, letilthatja azt. A letiltott eredmények nem befolyásolják a biztonsági pontszámot vagy nem kívánt zajt generálnak.

Ha egy eredmény megfelel a tiltó szabályokban meghatározott feltételeknek, az nem jelenik meg az eredmények listájában.

Ez a lehetőség a javaslatok részleteit tartalmazó oldalakon érhető el a következő hez:

- **A biztonsági Azure Container Registry rendszerképek biztonsági réseit kell orvosolni**
- **A virtuális gépek biztonsági réseit helyre kell ásni**

További információ: Specifikus eredmények letiltása a tároló [rendszerképei számára](defender-for-container-registries-usage.md#disable-specific-findings-preview) és Adott eredmények letiltása a virtuális [gépekhez.](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)


### <a name="exempt-a-resource-from-a-recommendation"></a>Erőforrás kivonása egy javaslatból

Előfordulhat, hogy egy erőforrás nem megfelelőnek tűnik egy adott javaslathoz (és ezáltal a biztonsági pontszám csökkentéséhez) még akkor is, ha úgy érzi, hogy nem lenne szabad. Előfordulhat, hogy egy olyan folyamat orvosolta, amelyet nem követnek nyomon Security Center. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrásra vonatkozó kockázatot. 

Ilyen esetekben létrehozhat egy kivételszabályt, és biztosíthatja, hogy az erőforrás ne legyen listázva a nem megfelelő erőforrások között a jövőben. Ezek a szabályok az alábbiakban ismertetett, dokumentált indoklásokat tartalmazhatnak.

További információ: [Erőforrás kivétele javaslatok és biztonsági pontszám alól.](exempt-resource.md)


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- és GCP-összekötők Security Center többfelhős felhasználói élményt kínálnak

A gyakran több felhőplatformra kiterjedő felhőalapú számítási feladatok esetében a felhőbiztonsági szolgáltatásoknak ugyanezt kell megtenniük.

Azure Security Center az Azure, a Amazon Web Services (AWS) és a Google Cloud Platform (GCP) számítási feladatait.

Az AWS- és GCP-fiókok Security Center, integrálja az AWS Security Hubot, a GCP biztonsági parancsot és a Azure Security Center. 

További [információ: AWS-fiókok](quickstart-onboard-aws.md) csatlakoztatása Azure Security Center és [GCP-fiókok csatlakoztatása a Azure Security Center.](quickstart-onboard-gcp.md)


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes számítási feladatok védelmének javaslatcsomagja

Annak érdekében, hogy a Kubernetes számítási feladatai alapértelmezés szerint biztonságosak, a Security Center Kubernetes-szintű ellenőrző javaslatokat ad hozzá, beleértve a Kubernetes belépésvezérlésének kényszerítési lehetőségeit is.

Miután telepítette a Kuberneteshez szükséges Azure Policy-bővítményt az AKS-fürtön, a rendszer a Kubernetes API-kiszolgálóra vonatkozó összes kérést figyeli az ajánlott eljárások előre meghatározott készlete alapján, mielőtt a fürtön megőrzné őket. Ezután konfigurálhatja a-t, hogy kikényszeríteni tudja az ajánlott eljárásokat, és a jövőbeli számítási feladatokhoz is be tudja őket állítani.

Például megadhatja, hogy a rendszer-jogosultságú tárolók ne hoznak létre, és a további erre vonatkozó kérések le lesznek tiltva.

További információ: Ajánlott eljárások [a számítási feladatok védelméhez a Kubernetes belépésvezérlésének használatával.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>A sebezhetőségi felmérés eredményei már elérhetők a folyamatos exportálásban

A folyamatos exportálással valós időben streamelhet riasztásokat és javaslatokat a Azure Event Hubs, Log Analytics-munkaterületek vagy Azure Monitor. Innen integrálhatja ezeket az adatokat SIEM-ekkel (például Azure Sentinel, Power BI, Azure Data Explorer stb.

Security Center integrált sebezhetőség-felmérési eszközei az erőforrásokra vonatkozó megállapításokat ad vissza a "szülő" javaslatban, például"A virtuális gépek biztonsági réseit helyre kelligazító" javaslatként. 

A biztonsági eredmények mostantól folyamatos exportálással exportálhatók, ha javaslatokat választ, és engedélyezi a biztonsági eredményekre vonatkozó **megállapításokat** is.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Biztonsági megállapítások be- és bekapcsolása a folyamatos exportálási konfigurációban" :::

Kapcsolódó oldalak:

- [Security Center Azure-beli virtuális gépek biztonsági rések felmérésére vonatkozó integrált megoldása](deploy-vulnerability-assessment-vm.md)
- [Security Center biztonsági rések felmérésére használható integrált megoldás Azure Container Registry képekhez](defender-for-container-registries-usage.md)
- [Folyamatos exportálás](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>A biztonsági hibás konfigurációk megelőzése a javaslatok kényszerítése új erőforrások létrehozásakor

A biztonsági incidensek fő oka a helytelen biztonsági konfiguráció. Security Center már képes megelőzni az  új erőforrások helytelen konfigurációját az egyes javaslatok kapcsán. 

Ez a funkció segít biztonságban tartani a számítási feladatokat, és stabilizálni a biztonsági pontszámot.

A biztonságos konfiguráció kényszerítés egy adott javaslat alapján két módban létezik:

- A **megtagadási** hatás használatával Azure Policy, hogy a rendszer ne hoz létre nem megfelelő erőforrásokat

- A **Kényszerítés** lehetőséggel kihasználhatja az Azure Policy **DeployIfNotExist** hatását, és létrehozáskor automatikusan szervizelődhet a nem megfelelő erőforrásokon
 
Ez a kiválasztott biztonsági javaslatokhoz érhető el, és az erőforrás részleteit tartalmazó oldal tetején található.

További [információ: Helytelen konfigurációk megakadályozása kényszerítés/megtagadás javaslatokkal.](prevent-misconfigurations.md)

###  <a name="network-security-group-recommendations-improved"></a>Továbbfejlesztett hálózati biztonsági csoportokra vonatkozó javaslatok

Továbbfejlesztettük a hálózati biztonsági csoportokra vonatkozó alábbi biztonsági javaslatokat, hogy csökkentsük a téves riasztások néhány előfordulását.

- Minden hálózati portot korlátozni kell a virtuális géphez társított NSG-n
- A felügyeleti portokat be kell zárni a virtuális gépeken
- Az internetkapcsolattal elérhető virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Elavult előzetes verziójú AKS-javaslat: "Podbiztonsági szabályzatokat kell meghatározni a Kubernetes Servicesben"

A "Pod security policies should be defined on Kubernetes Services" (Podbiztonsági szabályzatok definiálása a Kubernetes Servicesben) előzetes verziójú javaslat elavult, amint azt az Azure Kubernetes Service [ismerteti.](../aks/use-pod-security-policies.md)

A podok biztonsági szabályzatának (előzetes verzió) szolgáltatása elajátításra van beállítva, és 2020. október 15. után már nem lesz elérhető az AKS Azure Policy használata érdekében.

Miután a podok biztonsági szabályzata (előzetes verzió) elavult, le kell tiltania a funkciót minden meglévő fürtön az elavult funkcióval, hogy a későbbi fürtfrissítések végrehajtása és a fürtfrissítések Azure-támogatás.


### <a name="email-notifications-from-azure-security-center-improved"></a>Továbbfejlesztett e-Azure Security Center e-mail-értesítések

Továbbfejlesztettük a biztonsági riasztásokkal kapcsolatos e-mailek alábbi területeit: 

- E-mail-értesítések küldése a riasztásokkal kapcsolatban minden súlyossági szinthez
- A különböző Azure-szerepkörökkel rendelkező felhasználók értesítésének lehetősége hozzáadva az előfizetésben
- Alapértelmezés szerint proaktívan értesítjük az előfizetések tulajdonosait a nagy súlyosságú riasztások esetén (amelyek nagy valószínűséggel valódi incidensek)
- Eltávolítottuk a telefonszám mezőt az e-mail-értesítések konfigurációs oldalról

További információ: [E-mail-értesítések beállítása biztonsági riasztások számára.](security-center-provide-security-contact-details.md)


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A biztonsági pontszám nem tartalmaz előzetes verzióra vonatkozó javaslatokat 

Security Center értékeli az erőforrásokat, az előfizetéseket és a szervezetet biztonsági problémák esetén. Ezután egyetlen pontszámban összesíti az eredményeket, így egy pillantással meg tudja határozni az aktuális biztonsági helyzetet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint.

Az új fenyegetések észlelése után új biztonsági tanácsokat tesz elérhetővé a Security Center új javaslatokon keresztül. Annak érdekében, hogy elkerülje a meglepetések változását a biztonsági pontszámon, és türelmi időszakot  biztosítson az új javaslatok feltárására, mielőtt azok hatással lenne a pontszámra, az Előzetes verzióként megjelölt javaslatok már nem szerepelnek a biztonsági pontszám számításaiban. Ahol csak lehetséges, ezeket is helyre kell tenni, hogy az előzetes verzió időszakának végén hozzájáruljanak a pontszámhoz.

Emellett az **előzetes verzió** javaslatai nem "Nem megfelelően" renderelik az erőforrásokat.

Példa egy előzetes verzióra vonatkozó javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat az előzetes verzió jelzővel":::

[További információ a biztonsági pontszámról.](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>A javaslatok mostantól tartalmaznak egy súlyossági jelzőt és a frissességi időközt

A javaslatok részletező oldala mostantól tartalmaz egy frissességi intervallumjelzőt (ha van ilyen), valamint a javaslat súlyosságának egyértelmű megjelenítését.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Javaslatoldal a frissességről és a súlyosságról":::


## <a name="august-2020"></a>2020. augusztus

Augusztusi frissítések többek között a következők:

- [Eszközleltár – az eszközök biztonsági biztonságának hatékony új nézete](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Támogatás hozzáadva Azure Active Directory alapértelmezett biztonsági beállításokhoz (többtényezős hitelesítéshez)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Szolgáltatásnév-javaslat hozzáadva](#service-principals-recommendation-added)
- [Biztonsági rések felmérése virtuális gépeken – javaslatok és szabályzatok konszolidálva](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Új AKS biztonsági szabályzatok hozzáadva az ASC_default kezdeményezéshez – csak privát előzetes verziójú ügyfelek számára](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Eszközleltár – az eszközök biztonsági biztonságának hatékony új nézete

Security Center eszközleltára (amely jelenleg előzetes verzióban érhető el) lehetővé teszi a csatlakoztatott erőforrások biztonsági állásának Security Center.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a lehetséges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldásával kapcsolatban. Ha valamelyik erőforrás függőben lévő javaslatokkal rendelkezik, azok megjelennek a leltárban.

A nézet és szűrői segítségével feltárhatja a biztonsági adatokat, és további műveleteket is alkalmazhat az eredmények alapján.

További információ az [eszközleltárról.](asset-inventory.md)


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Támogatás hozzáadva Azure Active Directory alapértelmezett biztonsági beállításokhoz (többtényezős hitelesítéshez)

Security Center a biztonsági alapértelmezések [](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)teljes körű támogatása, a Microsoft ingyenes identitásbiztonsági védelmei.

A biztonsági alapértelmezések előre konfigurált identitásbiztonsági beállításokat biztosítanak, amelyek megvédik a szervezetet az identitással kapcsolatos gyakori támadásoktól. A biztonsági alapértelmezések már összesen több mint 5 millió bérlőt védenek; 50 000 bérlőt is véd a Security Center.

Security Center biztonsági javaslatot tesz, ha a biztonsági alapértelmezések engedélyezése nélkül azonosít egy Azure-előfizetést. Eddig ajánlott Security Center a többtényezős hitelesítés feltételes hozzáféréssel való engedélyezését, amely az Azure Active Directory (AD) prémium szintű licenc részét képezi. Az Ingyenes Azure AD-t használó ügyfelek számára javasoljuk az alapértelmezett biztonsági beállítások engedélyezését. 

A célunk az, hogy több ügyfelet ösztönözzük arra, hogy MFA-val biztosítják a felhőkörnyezeteiket, és mérsékeljük az egyik legnagyobb kockázatot, amely szintén a legnagyobb hatással van a biztonsági [pontszámra.](secure-score-security-controls.md)

További információ a [biztonsági alapértékekkel kapcsolatban.](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)


### <a name="service-principals-recommendation-added"></a>Szolgáltatásnév-javaslat hozzáadva

Új javaslat lett hozzáadva, amely azt javasolja, Security Center az ügyfelek felügyeleti tanúsítványokat használjanak az előfizetéseik kezeléséhez, váltson szolgáltatásnévre.

A felügyeleti **tanúsítványok** helyett a szolgáltatásnévvel kell védeni az előfizetéseket, és a szolgáltatásnév vagy a Azure Resource Manager használatát javasolja az előfizetések biztonságosabb kezeléséhez. 

További információ az [alkalmazás- és szolgáltatásnév-objektumokról a Azure Active Directory.](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Biztonsági rések felmérése virtuális gépeken – javaslatok és szabályzatok konszolidálva

Security Center a virtuális gépeket annak észlelésére, hogy futtatnak-e biztonsági rések felmérésére megoldást. Ha nem található sebezhetőség-felmérési megoldás, Security Center az üzembe helyezés egyszerűsítésére vonatkozó javaslatot tesz.

Ha biztonsági réseket talál, Security Center javaslatot tesz az eredmények összegzéséhez, hogy szükség szerint kivizsgálja és orvosolja azokat.

Annak érdekében, hogy az összes felhasználó egységes felhasználói élményben legyen része, a beolvasó típusától függetlenül négy javaslatot egyesítettünk a következő kettőben:

|Egyesített javaslat|Módosítások ismertetése|
|----|:----|
|**Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken**|A következő két javaslatot váltja fel:<br> A (már elavult) Qualys által működtetett virtuális gépek beépített sebezhetőségi felmérési megoldásának engedélyezése (a standard szint része)<br> A sebezhetőségi felmérési megoldást telepíteni kell a virtuális gépekre (amelyek elavultak) (Standard és ingyenes szintek)|
|**A virtuális gépek biztonsági réseit helyre kell ásni**|A következő két javaslatot váltja fel:<br>A (Qualys által működtetett) virtuális gépeken talált biztonsági rések orvoslása (elavult)<br>A biztonsági réseket egy (már elavult) sebezhetőségi felmérési megoldásnak kell orvosolni|
|||

Most ugyanezt a javaslatot fogja használni az Security Center biztonsági rések felmérésére vonatkozó bővítményének vagy egy privát licenccel rendelkező megoldásnak (BYOL) egy olyan partnertől való üzembe helyezéséhez, mint a Qualys vagy a Rapid7.

Emellett ha biztonsági réseket talál és Security Center, egyetlen javaslat figyelmezteti az eredményekre, függetlenül attól, hogy a sebezhetőség-felmérési megoldás azonosította-e őket.

#### <a name="updating-dependencies"></a>Függőségek frissítése

Ha olyan szkriptekkel, lekérdezésekkel vagy automatizálásokkal kapcsolatban van, amelyek az előző javaslatokra vagy szabályzatkulcsok/nevekre hivatkoznak, az alábbi táblázatokkal frissítheti a hivatkozásokat:

##### <a name="before-august-2020"></a>2020 augusztusa előtt

| Ajánlás|Hatókör|
|----|:----|
|**A (Qualys által működtetett) virtuális gépek beépített sebezhetőségi felmérési megoldásának engedélyezése**<br>Kulcs: 550e890b-e652-4d22-8274-60b3bdb24c63|Beépített|
|**A (Qualys által működtetett) virtuális gépeken talált biztonsági rések orvoslása**<br>Kulcs: 1195afff-c881-495e-9bc5-1486211ae03f|Beépített|
|**A sebezhetőségi felmérési megoldást telepíteni kell a virtuális gépekre**<br>Kulcs: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**A biztonsági réseket egy sebezhetőségi felmérési megoldásnak kell orvosolni**<br>Kulcs: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Szabályzat|Hatókör|
|----|:----|
|**A sebezhetőségi felmérést engedélyezni kell a virtuális gépeken**<br>Szabályzat azonosítója: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Beépített|
|**A biztonsági réseket egy sebezhetőségi felmérési megoldásnak kell orvosolni**<br>Szabályzat azonosítója: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>2020 augusztusa óta

|Ajánlás|Hatókör|
|----|:----|
|**Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken**<br>Kulcs: ffff0522-1e88-47fc-8382-2a80ba848f5d|Beépített + BYOL|
|**A virtuális gépek biztonsági réseit helyre kell ásni**<br>Kulcs: 1195afff-c881-495e-9bc5-1486211ae03f|Beépített + BYOL|
|||

|Szabályzat|Hatókör|
|----|:----|
|[**A sebezhetőségi felmérést engedélyezni kell a virtuális gépeken**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Szabályzat azonosítója: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Beépített + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Új AKS biztonsági szabályzatok hozzáadva az ASC_default kezdeményezéshez – csak privát előzetes verziójú ügyfelek számára

Annak érdekében, hogy a Kubernetes számítási feladatai alapértelmezés szerint biztonságosak legyen, a Security Center Kubernetes-szintű szabályzatokat és javaslatokat ad hozzá, beleértve a Kubernetes belépésvezérlésének kényszerítési lehetőségeit is.

A projekt korai fázisa egy privát előzetes verzióból áll, és új (alapértelmezés szerint letiltott) szabályzatokat ad a ASC_default kezdeményezéshez.

Ezeket a szabályzatokat nyugodtan figyelmen kívül hagyhatja, és ez nem lesz hatással a környezetre. Ha engedélyezni szeretné őket, regisztráljon az előzetes verzióra a következő lehetőségek https://aka.ms/SecurityPrP közül:

1. **Egyetlen előzetes verzió** – Ha csak ehhez a privát előzetes verzióhoz szeretne csatlakozni. Explicit módon említse meg az "ASC continuous Scan" (Folyamatos ASC-vizsgálat) előzetes verzióként a csatlakozást.
1. **Folyamatban lévő program** – Ezt és a jövőbeli privát előzetes verziókat is hozzá kell adni. Létre kell hoznunk egy profilt és egy adatvédelmi szerződést.


## <a name="july-2020"></a>2020. július

Júliusi frissítések:
- [A virtuális gépek sebezhetőségi felmérése mostantól elérhető a nem Marketplace-ről elérhető rendszerképekhez](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Az Azure Storage veszélyforrások elleni védelme a Azure Files és Azure Data Lake Storage Gen2 (előzetes verzió)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Nyolc új javaslat a fenyegetésvédelmi funkciók engedélyezéséhez](#eight-new-recommendations-to-enable-threat-protection-features)
- [A tárolóbiztonság fejlesztései – gyorsabb beállításjegyzék-vizsgálat és frissített dokumentáció](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Az adaptív alkalmazásvezérlők új javaslattal frissültek, és támogatják a helyettesítő karaktereket az elérésiút-szabályokban](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Hat szabályzat elavult a speciális SQL-adatbiztonsághoz](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>A virtuális gépek sebezhetőségi felmérése mostantól elérhető a nem Marketplace-ről elérhető rendszerképekhez

A sebezhetőségi felmérési megoldás üzembe helyezésekor a Security Center korábban ellenőrzést végzett az üzembe helyezés előtt. Az ellenőrzés a cél virtuális gép piactéri termékváltozatának ellenőrzése volt. 

A frissítésből az ellenőrzés el lett távolítva, és mostantól üzembe helyezhet sebezhetőségi felmérési eszközöket az "egyéni" Windows- és Linux-gépeken. Az egyéni rendszerképek azok, amelyek a piactér alapértelmezései közül módosultak.

Bár a (Qualys által működtetett) integrált sebezhetőség-felmérési bővítményt már sok más gépen is üzembe helyezheti, a támogatás csak akkor érhető el, ha az Integrált biztonsági rések ellenőrzőeszközének üzembe helyezése standard szintű virtuális gépeken fejezetben felsorolt operációs rendszert [használ.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

További információ a virtuális gépek integrált sebezhetőségi [ellenőrzőeszközével kapcsolatban (ehhez Azure Defender szükséges).](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)

További információ a Qualys vagy Rapid7 saját, privát licenccel rendelkező sebezhetőségi felmérési megoldásának használatával [kapcsolatban: Deploying a partner vulnerability scanning solution ..](deploy-vulnerability-assessment-vm.md)


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Az Azure Storage veszélyforrások elleni védelme az Azure Files és Azure Data Lake Storage Gen2 (előzetes verzió)

Az Azure Storage veszélyforrások elleni védelme észleli az Azure Storage-fiókok potenciálisan káros tevékenységét. Security Center riasztásokat jelenít meg, amikor a tárfiókok elérésére vagy kihasználására tett kísérleteket észlel. 

Adatai védve vannak akár blobtárolóként, fájlmegosztásként, akár data lake-ként.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Nyolc új javaslat a fenyegetésvédelmi funkciók engedélyezéséhez

Nyolc új javaslat lett hozzáadva, amelyek egyszerű lehetőséget biztosítanak az Azure Security Center fenyegetésvédelmi funkcióinak engedélyezésére a következő erőforrástípusokhoz: virtuális gépek, App Service-csomagok, Azure SQL Database-kiszolgálók, gépeken lévő SQL-kiszolgálók, Azure Storage-fiókok, Azure Kubernetes Service-fürtök, Azure Container Registry-beállításregisztrálók és Azure Key Vault-tárolók.

Az új javaslatok a következőek:

- **A speciális adatbiztonságot engedélyezni kell a Azure SQL Database kiszolgálókon**
- **Engedélyezni kell a magas szintű adatbiztonságot a gépeken található SQL-kiszolgálókon**
- **Az Advanced Threat Protectiont engedélyezni kell a Azure App Service terveken**
- **Az Advanced Threat Protectiont engedélyezni kell a Azure Container Registry beállításregisztrálókban**
- **Az Advanced Threat Protectiont engedélyezni kell a Azure Key Vault tárolókban**
- **A komplex veszélyforrások elleni védelmet engedélyezni kell Azure Kubernetes Service fürtökön**
- **Az Advanced Threat Protectiont engedélyezni kell az Azure Storage-fiókokon**
- **Az Advanced Threat Protectiont engedélyezni kell a virtuális gépeken**

Ezek az új javaslatok a Biztonsági beállítások **Azure Defender** tartoznak.

A javaslatok a gyorsjavítási képességet is tartalmazzák. 

> [!IMPORTANT]
> Ezen javaslatok bármelyikének szervizlését a megfelelő erőforrások védelme díj fogja eredményezni. Ezek a díjak azonnal kezdődnek, ha a kapcsolódó erőforrások az aktuális előfizetésben vannak. Vagy a későbbiekben, ha később adja hozzá őket.
> 
> Ha például nincsenek hálózati Azure Kubernetes Service az előfizetésében, és engedélyezi a fenyegetésvédelmet, a rendszer nem számít fel díjat. Ha a jövőben egy fürtöt ad hozzá ugyanannak az előfizetésnek a számára, az automatikusan védelem alatt áll, és a díjak csak akkor kezdődnek meg.

Ezekről a biztonsági javaslatok [referenciaoldalán olvashat bővebben.](recommendations-reference.md)

További információ a [fenyegetések elleni védelemről a Azure Security Center.](azure-defender.md)




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>A tárolóbiztonság fejlesztései – gyorsabb beállításjegyzék-vizsgálat és frissített dokumentáció

A tárolóbiztonsági tartományba való folyamatos befektetés részeként örömmel osztjuk meg a Security Center tároló-lemezképek dinamikus vizsgálatának jelentős teljesítménybeli javulását a Azure Container Registry. A vizsgálatok általában körülbelül két perc alatt befejeződnek. Egyes esetekben akár 15 percet is igénybe vehet.

A tárolók biztonsági képességeivel kapcsolatos Azure Security Center és útmutatás javítása érdekében frissítettük a tárolók biztonságával kapcsolatos dokumentációs oldalakat is. 

A tárolók Security Center az alábbi cikkekben talál további információt:

- [A Security Center tárolóbiztonsági funkcióinak áttekintése](container-security.md)
- [Az integráció részletei a Azure Container Registry](defender-for-container-registries-introduction.md)
- [Az integráció részletei a Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [A beállításregisztrálók vizsgálatának és a Docker-gazdagépek merevlemezének ellenőrzésével](container-security.md)
- [Biztonsági riasztások a biztonsági fürtök fenyegetésvédelmi Azure Kubernetes Service funkcióiról](alerts-reference.md#alerts-akscluster)
- [Biztonsági riasztások a gazdagépek fenyegetésvédelmi Azure Kubernetes Service riasztásai](alerts-reference.md#alerts-containerhost)
- [Biztonsági javaslatok tárolókhoz](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Az adaptív alkalmazásvezérlők új javaslattal frissültek, és támogatják a helyettesítő karaktereket az elérésiút-szabályokban

Az adaptív alkalmazásvezérlő funkció két jelentős frissítést kapott:

* Egy új javaslat azonosítja azokat a potenciálisan megbízható viselkedéseket, amelyek korábban nem voltak engedélyezve. Frissíteni kell az adaptív alkalmazásvezérlési szabályzat engedélyezési szabályainak új szabályokkal való hozzáadását a meglévő szabályzathoz, hogy csökkentse a téves riasztások számát az adaptív alkalmazásvezérlők szabálysértési riasztásainál.

* Az elérésiút-szabályok mostantól támogatják a helyettesítő karaktereket. Ezzel a frissítéssel helyettesítő karakterek használatával konfigurálhatja az engedélyezett elérésiút-szabályokat. Két támogatott forgatókönyv íme:

    * Helyettesítő karakter használata az elérési út végén a mappában és az almappákban található összes végrehajtható fájl engedélyezése érdekében

    * Helyettesítő karakter használata egy elérési út közepén egy mappanévvel (pl. személyes felhasználói mappák egy ismert végrehajtható fájlval, automatikusan létrehozott mappanevekkel stb.) egy ismert végrehajtható fájlnév engedélyezéséhez.


[További információ az adaptív alkalmazásvezérlőkről.](security-center-adaptive-application.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Az SQL speciális adatbiztonságának hat szabályzata elavult

Az SQL-gépek fejlett adatbiztonságával kapcsolatos hat szabályzat elavult:

- A komplex veszélyforrások elleni védelem típusait "Mind" beállításra kell állítani a felügyelt SQL-példány speciális adatbiztonsági beállításaiban
- Az Advanced Threat Protection-típusokat "Mind" beállításra kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- A felügyelt SQL-példány speciális adatbiztonsági beállításainak tartalmaznia kell egy e-mail-címet a biztonsági riasztások fogadására
- Az SQL Server speciális adatbiztonsági beállításainak tartalmaznia kell egy e-mail-címet a biztonsági riasztások fogadására
- A rendszergazdáknak és előfizetés-tulajdonosoknak küldött e-mailes értesítéseket engedélyezni kell a felügyelt SQL-példány speciális adatbiztonsági beállításaiban
- A rendszergazdáknak és előfizetés-tulajdonosoknak küldött e-mailes értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

További információ a [beépített szabályzatokkal kapcsolatban:](./policy-reference.md).



## <a name="june-2020"></a>2020. június

A júniusi frissítések a következők:

- [Biztonsági pontszám API (előzetes verzió)](#secure-score-api-preview)
- [Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez Azure Arc gépeken (előzetes verzió)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk nagy léptékű létrehozásához](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Új javaslat NSG-k használatával a nem internetkapcsolattal elérhető virtuális gépek védelméhez](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Új szabályzatok a fenyegetésvédelem és a speciális adatbiztonság engedélyezéséhez](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Biztonsági pontszám API (előzetes verzió)

Most már elérheti a pontszámát a [biztonsági pontszám API-n](/rest/api/securitycenter/securescores/) keresztül (jelenleg előzetes verzióban érhető el). Az API-metódusokkal rugalmasan lekérdezheti az adatokat, és saját jelentéskészítési mechanizmust építhet ki a biztonsági pontszámok alapján. A Secure **Scores** API-val például lekért egy adott előfizetés pontszámát. Emellett a Secure **Score Controls** API-val listhatja a biztonsági vezérlőket és az előfizetések aktuális pontszámát.

A biztonsági pontszám API-val elérhetővé tett külső eszközök példáiért tekintse meg a GitHub-közösség biztonságos pontszámokkal [kapcsolatos területét.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

További információ a biztonsági pontszámról és a biztonsági [vezérlőkről a Azure Security Center.](secure-score-security-controls.md)



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)

Azure Security Center SQL-gépek fejlett adatbiztonsága mostantól védelmet nyújt az Azure-ban, más felhőkörnyezetekben vagy akár helyszíni gépeken üzemeltetett SQL-kiszolgálók számára. Ez kiterjeszti az Azure-natív SQL-kiszolgálók védelmét, hogy teljes mértékben támogassa a hibrid környezeteket.

Az Advanced Data Security biztonsági rések felmérését és fejlett fenyegetésvédelmet biztosít az SQL-gépeknek, bárhol is vannak.

A beállítás két lépésből áll:

1. A Log Analytics-ügynök üzembe helyezése a SQL Server gazdagépére, hogy kapcsolatot biztosítson az Azure-fiókkal.

1. A választható csomag engedélyezése Security Center díjszabási és beállítási oldalán.

További információ az [SQL-gépek fejlett adatbiztonságával kapcsolatban.](defender-for-sql-usage.md)



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez Azure Arc gépeken (előzetes verzió)

Két új javaslat lett hozzáadva, amelyek segítségével üzembe helyezheti a [Log Analytics-ügynököt](../azure-monitor/agents/log-analytics-agent.md) a Azure Arc gépeken, és biztosíthatja, hogy az Azure Security Center:

- **A Log Analytics-ügynököt Windows-alapú virtuális gépekre kell Azure Arc (előzetes verzió)**
- **A Log Analytics-ügynököt Linux-alapú virtuális gépekre kell Azure Arc (előzetes verzió)**

Ezek az új javaslatok ugyanabban a négy biztonsági vezérlőben jelennek meg, mint a meglévő (kapcsolódó) javaslat, a Monitoring **Agentet** telepíteni kell a gépekre: a biztonsági konfigurációk szervizlése, adaptív alkalmazásvezérlés alkalmazása, rendszerfrissítések alkalmazása és végpontvédelem engedélyezése.

A javaslatok között szerepel a Gyorsjavítás funkció is, amely segít felgyorsítani az üzembe helyezési folyamatot. 

Erről a két új javaslatról a Számítás és alkalmazásra vonatkozó javaslatok [táblázatban olvashat bővebben.](recommendations-reference.md#recs-compute)

A Mi az a Log Analytics Azure Security Center ügynök? oldalon további információt olvashat arról, hogyan használja az [ügynök az ügynököt.](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

További információ a [virtuális gépek bővítményeiről Azure Arc gépekhez.](../azure-arc/servers/manage-vm-extensions.md)


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk nagy léptékű létrehozásához

A szervezet figyelési és incidensválasz-folyamatainak automatizálása jelentősen megnövelheti a biztonsági incidensek kivizsgálásának és enyhítésének idejét.

Az automatizálási konfigurációk szervezeten belül történő üzembe helyezéséhez használja a beépített DeployIfdNotExist Azure-szabályzatokat a folyamatos exportálási és munkafolyamat-automatizálási eljárások létrehozásához és [](continuous-export.md) [konfigurálásához:](workflow-automation.md)

A szabályzatok az Azure Policyben találhatók:


|Cél  |Szabályzat  |Szabályzatazonosító  |
|---------|---------|---------|
|Folyamatos exportálás eseményközpontba|[Az Azure Security Center-riasztások és -javaslatok Event Hubsba való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Folyamatos exportálás Log Analytics-munkaterületre|[Az Azure Security Center-riasztások és -javaslatok Log Analytics-munkaterületre való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Munkafolyamat-automatizálás biztonsági riasztásokhoz|[Azure Security Center-riasztásokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Azure Security Center-javaslatokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

A [munkafolyamat-automatizálási sablonok – első lépések.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

A két exportálási szabályzat [](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) használatával kapcsolatos további információkért olvassa el a Munkafolyamat-automatizálás konfigurálása nagy méretekben a megadott szabályzatokkal és [a Folyamatos exportálás beállítása szakaszt.](continuous-export.md#set-up-a-continuous-export)


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Új javaslat NSG-k használatával a nem internetkapcsolattal elérhető virtuális gépek védelméhez

Az "ajánlott biztonsági eljárások megvalósítása" biztonsági vezérlő mostantól a következő új javaslatot tartalmazza:

- **A nem internetkapcsolattal elérhető virtuális gépeket hálózati biztonsági csoportokkal kell védeni**

Egy meglévő javaslat szerint az **internetre** néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni, amelyek nem tesznek különbséget az internetkapcsolattal és a nem internetkapcsolattal elérhető virtuális gépek között. Mindkét esetben magas súlyossági szintű javaslat lett létrehozva, ha egy virtuális gép nincs hálózati biztonsági csoporthoz rendelve. Ez az új javaslat elkülöníti a nem internetes gépeket a téves riasztások számának csökkentése és a szükségtelen, magas súlyosságú riasztások elkerülése érdekében.

További információt a Hálózati [javaslatok táblázatban](recommendations-reference.md#recs-networking) olvashat.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Új szabályzatok a fenyegetésvédelem és a fejlett adatbiztonság engedélyezéséhez

Az alábbi új szabályzatok hozzá vannak adva az ASC alapértelmezett kezdeményezéséhez, és úgy vannak kialakítva, hogy segítsenek a fenyegetésvédelem vagy a speciális adatbiztonság engedélyezésében a megfelelő erőforrástípusokhoz.

A szabályzatok az Azure Policyben találhatók:


| Szabályzat                                                                                                                                                                                                                                                                | Szabályzatazonosító                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A speciális adatbiztonságot engedélyezni kell a Azure SQL Database kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A magas szintű adatbiztonságot engedélyezni kell a gépeken található SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Az Advanced Threat Protectiont engedélyezni kell az Azure Storage-fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Az Advanced Threat Protectiont engedélyezni kell a Azure Key Vault tárolókban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Az Advanced Threat Protectiont engedélyezni kell a Azure App Service terveken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Az Advanced Threat Protectiont engedélyezni kell a Azure Container Registry beállításregisztrálókban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Az Advanced Threat Protectiont engedélyezni kell a Azure Kubernetes Service fürtökön](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Az Advanced Threat Protectiont engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

További információ a [veszélyforrások elleni védelemről a Azure Security Center.](azure-defender.md)


## <a name="may-2020"></a>2020. május

A májusi frissítései a következők lehetnek:
- [Riasztáselfojtó szabályok (előzetes verzió)](#alert-suppression-rules-preview)
- [Általánosan elérhető a virtuális gépek sebezhetőségi felmérése](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Igény szerinti (JIT) virtuálisgép-hozzáférés változásai](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Az egyéni javaslatok átkerültek egy külön biztonsági vezérlőbe](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Váltógomb hozzáadva a vezérlők javaslatainak megtekintéséhez vagy egyszerű listához](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Kibővített biztonsági vezérlő – "Ajánlott biztonsági eljárások megvalósítása"](#expanded-security-control-implement-security-best-practices)
- [Mostantól általánosan elérhetők egyéni szabályzatok egyéni metaadatokkal](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Összeomlási memóriakép-elemzési képességek áttelepítése fájl nélküli támadásészlelésre](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Riasztáselfojtó szabályok (előzetes verzió)

Ez az új (jelenleg előzetes verzióban elérhető) funkció segít csökkenteni a riasztások elszenyedettségét. A szabályokkal automatikusan elrejtheti a szervezet szokásos tevékenységeihez kapcsolódó, ártalmatlanként ismert riasztásokat. Így a legfontosabb fenyegetésekre összpontosíthat. 

Az engedélyezett mellőzés szabályainak megfelelő riasztások továbbra is létrejönnek, de az állapotuk elvetve lesz. Láthatja az állapotokat a Azure Portal, vagy ahogy hozzáfér Security Center biztonsági riasztáshoz.

A mellőzés szabályai határozzák meg azokat a feltételeket, amelyek alapján a riasztásokat automatikusan el kell utasítani. A mellőzés szabálya általában a következőre használható:

- téves riasztásként azonosított riasztások mellőzése

- a túl gyakran kiváltott riasztások mellőzése, hogy hasznosak

További információ a [riasztások letiltásról Azure Security Center fenyegetések elleni védelemből.](alerts-suppression-rules.md)


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Általánosan elérhető a virtuális gépek sebezhetőségi felmérése

Security Center standard szint mostantól beépített sebezhetőségi felmérést is tartalmaz a virtuális gépekhez további költségek nélkül. Ezt a bővítményt a Qualys működteti, de az eredményeket közvetlenül a Security Center. Nincs szükség Qualys-licencre vagy akár Qualys-fiókra – minden zökkenőmentesen kezelhető a Security Center.

Az új megoldás folyamatosan képes a virtuális gépek vizsgálatával biztonsági réseket találni, és az eredményeket a Security Center. 

A megoldás üzembe helyezéséhez használja az új biztonsági javaslatot:

"A (Qualys által működtetett) virtuális gépek beépített sebezhetőségi felmérési megoldásának engedélyezése"

További információ Security Center virtuális gépek integrált [sebezhetőségi felmérésével kapcsolatban.](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Igény szerinti (JIT) virtuálisgép-hozzáférés változásai

Security Center egy opcionális funkciót is tartalmaz a virtuális gépek felügyeleti portjainak védelméhez. Ez védelmet nyújt a találgatásos támadások leggyakoribb formája ellen.

Ez a frissítés a következő módosításokat tartalmazza a szolgáltatásban:

- A JIT virtuális gépen való engedélyezését javasoló javaslat át lett nevezve. Korábban az "Időben elérhető hálózati hozzáférés-vezérlést a virtuális gépeken kell alkalmazni", ez a következő: "A virtuális gépek felügyeleti portjainak a hálózat hozzáférés-vezérlésével kell védeni".

- A javaslat csak akkor aktiválódik, ha vannak nyitott felügyeleti portok.

További információ a [JIT hozzáférési funkcióról.](security-center-just-in-time.md)


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Az egyéni javaslatok átkerültek egy külön biztonsági vezérlőre

A továbbfejlesztett biztonsági pontszámmal bevezetett egyik biztonsági vezérlő a "Bevált biztonsági eljárások megvalósítása" volt. Az előfizetéséhez létrehozott egyéni javaslatok automatikusan ebben a vezérlőben helyezték el. 

Annak érdekében, hogy könnyebben megtalálja az egyéni javaslatokat, áthelyezte őket egy "Egyéni javaslatok" dedikált biztonsági vezérlőbe. Ez a vezérlő nincs hatással a biztonsági pontszámra.

További információ a biztonsági vezérlőkről: Továbbfejlesztett biztonsági [pontszám (előzetes verzió) a Azure Security Center.](secure-score-security-controls.md)


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Váltógomb hozzáadva a vezérlők javaslatainak megtekintéséhez vagy egyszerű listához

A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai. Ezek a sebezhető támadási felületeket tükrözik. A vezérlők biztonsági javaslatokból és utasításokból áll, amelyek segítségével megvalósíthatja ezeket a javaslatokat.

Annak azonnali áttekintéséhez, hogy a szervezet mennyire jól biztosítja az egyes támadási felületeket, tekintse át az egyes biztonsági vezérlők pontszámait.

Alapértelmezés szerint a javaslatok a biztonsági vezérlőkben jelennek meg. Ebből a frissítésből listaként is megjelenítheti őket. Ha az érintett erőforrások állapota szerint rendezve, egyszerű listaként megtekinteni őket, használja az új "Csoportosítás vezérlők szerint" kapcsolót. A váltógomb a portálon a lista felett található.

A biztonsági vezérlők – és ez a váltógomb – az új biztonsági pontszámok részét képezi. Ne felejtsen el visszajelzést küldeni a portálon.

További információ a biztonsági vezérlőkről: Továbbfejlesztett biztonsági [pontszám (előzetes verzió) a Azure Security Center.](secure-score-security-controls.md)

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Csoportosítás vezérlők szerint váltógomb javaslatokhoz":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Kibővített biztonsági vezérlő – "Ajánlott biztonsági eljárások megvalósítása" 

A továbbfejlesztett biztonsági pontszámmal bevezetett egyik biztonsági vezérlő az "Ajánlott biztonsági eljárások megvalósítása". Ha egy javaslat ebben a vezérlőben van, az nem befolyásolja a biztonsági pontszámot. 

Ezzel a frissítéssel három javaslatot helyeztünk át az eredetileg alkalmazott vezérlők közül, és átkerültek ebbe az ajánlott eljárásba. Azért léptünk ezzel a lépéssel, mert megállapítottuk, hogy a három javaslat kockázata alacsonyabb, mint amit eredetileg gondoltunk.

Emellett két új javaslat is bevezetésre került, és hozzáadta ezt a vezérlőt.

A három javaslat a következő:

- **Az MFA-t engedélyezni** kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon (eredetileg az "MFA engedélyezése" vezérlőben)
- **Az olvasási engedélyekkel rendelkező** külső fiókokat el kell távolítani az előfizetésből (eredetileg a "Hozzáférés és engedélyek kezelése" vezérlőben)
- **Az előfizetéshez legfeljebb 3** tulajdonost kell kijelölni (eredetileg a "Hozzáférés és engedélyek kezelése" vezérlőben)

A vezérlő két új javaslatával bővült:

- **A vendégkonfigurációs** bővítményt Windows rendszerű virtuális gépekre kell telepíteni (előzetes verzió) – A Azure Policy Vendégkonfiguráció használatával láthatóvá teszi [a](../governance/policy/concepts/guest-configuration.md) virtuális gépeken a kiszolgáló- és alkalmazásbeállításokat (csak Windows rendszeren).

- **Windows Defender Exploit Guardnak engedélyezve** kell lennie a gépeken (előzetes verzió) – Windows Defender Exploit Guard a vendégkonfigurációs Azure Policy használja. Az Exploit Guard négy összetevőből áll, amelyek célja az eszközök számos különböző támadási vektorsal szembeni zárolása és a kártevők elleni támadásokban gyakran használt viselkedések blokkolása, miközben lehetővé teszi a vállalatok számára a biztonsági kockázatokkal és hatékonysággal kapcsolatos követelmények egyensúlyba hozásait (csak Windows esetén).

Az Exploit Guard Windows Defender az Exploit Guard-szabályzatok létrehozásáról és [üzembe helyezéséről.](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)

További információ a biztonsági vezérlőkről: [Továbbfejlesztett biztonsági pontszám (előzetes verzió).](secure-score-security-controls.md)



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Általánosan elérhetőek az egyéni metaadatokkal is elérhető egyéni szabályzatok

Az egyéni szabályzatok mostantól a Security Center felhasználói élmény, a biztonsági pontszám és a jogszabályi megfelelőségi szabványok irányítópultjának részei. Ez a funkció általánosan elérhető, és lehetővé teszi a szervezet biztonsági felmérési lefedettségének kiterjesztését Security Center. 

Hozzon létre egy egyéni kezdeményezést az Azure Policyban, adjon hozzá szabályzatokat, majd készítse Azure Security Center, és vizualizálja javaslatként.

Az egyéni javaslat metaadatainak szerkesztésére vonatkozó lehetőséget is hozzáadtunk. A metaadat-beállítások közé tartozik többek között a súlyosság, a szervizelési lépések, a fenyegetésekre vonatkozó információk.  

További információ az [egyéni javaslatok részletes leírással való javításáról.](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Összeomlási memóriakép-elemzési képességek áttelepítése fájl nélküli támadásészlelésre 

A Windows összeomlási memóriakép-elemzési (CDA)-észlelési képességeit integráljuk a fájl [nélküli támadásészlelésbe.](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers) A fájl nélküli támadásészlelési elemzések a következő biztonsági riasztások továbbfejlesztett verzióit biztosítják Windows rendszerű gépekhez: Kódinjektálás észlelhető, Álcázásos Windows-modul észlelhető, Héjkód észlelhető és Gyanús kódszegmens észlelhető.

A váltás néhány előnye:

- **Proaktív és időben történő kártevőészlelés** – A CDA megközelítése az összeomlás bekövetkeztére való várakozást, majd az elemzés futtatását a kártékony összetevők megkeresése érdekében. A fájl nélküli támadásészlelés használatával proaktív módon lehet azonosítani a memóriában található fenyegetéseket futás közben. 

- **Bővített riasztások** – A fájl nélküli támadásészlelés biztonsági riasztásai közé tartoznak a CDA-ból nem elérhető fejlesztések, például az aktív hálózati kapcsolatok adatai. 

- **Riasztások összesítése** – Ha a CDA több támadási mintát észlelt egy összeomlási memóriaképen belül, több biztonsági riasztást aktivált. A fájl nélküli támadásészlelés egyetlen riasztásban egyesíti az ugyanannak a folyamatnak az összes azonosított támadási mintáit, így nincs szükség több riasztás korrelálítására.

- **Csökkentett követelmények a Log Analytics-munkaterületen** – A potenciálisan bizalmas adatokat tartalmazó összeomlási memóriaképeket a rendszer többé nem tölti fel a Log Analytics-munkaterületre.






## <a name="april-2020"></a>2020. április

Áprilisi frissítések:
- [Mostantól általánosan elérhetők a dinamikus megfelelőségi csomagok](#dynamic-compliance-packages-are-now-generally-available)
- [Az identitásra vonatkozó javaslatok mostantól az ingyenes Azure Security Center tartalmazzák](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Mostantól általánosan elérhetők a dinamikus megfelelőségi csomagok

A Azure Security Center megfelelőségi irányítópult mostantól dinamikus **megfelelőségi** csomagokat tartalmaz (mostantól általánosan elérhető) a további iparági és szabályozási szabványok nyomon követéséhez.

A dinamikus megfelelőségi csomagokat a biztonsági szabályzat lapján adva lehet hozzáadni Security Center felügyeleti csoporthoz. Ha elő van készítve egy szabvány vagy teljesítményteszt, a szabvány megjelenik a jogszabályi megfelelési irányítópulton, és az összes kapcsolódó megfelelőségi adat értékelésként van leképezve. Az összes bevetett szabvány összefoglaló jelentése letölthető.

Mostantól a következő szabványokat használhatja:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official és uk NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (új)** (az Azure CIS 1.1.0 teljesebb reprezentációja)

Emellett nemrégiben hozzáadtunk egy [Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)biztonsági teljesítménytesztet is, amely a Microsoft által írt, a biztonsági és megfelelőségi ajánlott eljárásokra vonatkozó, általános megfelelőségi keretrendszeren alapuló Azure-specifikus irányelveket tartalmaz. Az irányítópult további szabványokat is támogat, amint azok elérhetővé válnak.  
 
További információ a [szabványok testreszabásáról a jogszabályi megfelelési irányítópulton.](update-regulatory-compliance-packages.md)


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Az identitásra vonatkozó javaslatok mostantól az ingyenes Azure Security Center tartalmazzák

Általánosan elérhetők az identitásra és a hozzáférésre vonatkozó Azure Security Center az ingyenes szinten. Ez része annak a törekvésnek, hogy a felhőbeli biztonsági funkciókat (CSPM) ingyenesen elérhető legyen. Ezek a javaslatok eddig csak a standard tarifacsomagban voltak elérhetők.

Példák az identitásra és hozzáférésre vonatkozó javaslatokra:

- "A többtényezős hitelesítést engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban."
- "Az előfizetéshez legfeljebb három tulajdonost kell kijelölni."
- "Az elavult fiókokat el kell távolítani az előfizetésből."

Ha az ingyenes tarifacsomagban vannak előfizetései, a változás hatással lesz a biztonsági pontszámaikra, mert a rendszer soha nem értékeli ki őket az identitásuk és a hozzáférésük biztonsága szempontjából.

További információ az [identitással és hozzáféréssel kapcsolatos javaslatokról.](recommendations-reference.md#recs-identityandaccess)

További információ a többtényezős hitelesítés [(MFA)](security-center-identity-access.md)kényszerítési folyamatának előfizetésen való kezeléséről.



## <a name="march-2020"></a>2020. március

A márciusi frissítések a következők:

- [Általánosan elérhető a munkafolyamat-automatizálás](#workflow-automation-is-now-generally-available)
- [Integráció a Azure Security Center és Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Védelem a Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Továbbfejlesztett, időponthoz időben való kezelés](#improved-just-in-time-experience)
- [A webalkalmazások két biztonsági ajánlása elavult](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Általánosan elérhető a munkafolyamat-automatizálás

Általánosan elérhető a Azure Security Center munkafolyamat-automatizálási funkciója. Ezzel automatikusan aktiválhatja a Logic Apps riasztásokkal és javaslatokkal kapcsolatos riasztásokat. Emellett manuális eseményindítók is elérhetők a riasztások és az összes olyan javaslathoz, amelyekhez elérhető a gyorsjavítási lehetőség.

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek reagálásához. Ezek a folyamatok magukban foglalhatják az érintett felek értesítését, a változáskezelési folyamat elindítását és a konkrét javítási lépések alkalmazását. A biztonsági szakértők azt javasolják, hogy az eljárások lehető legtöbb lépését automatizálják. Az automatizálás csökkenti a többletterhelést, és javíthatja a biztonságot azáltal, hogy biztosítja a folyamat lépéseit gyorsan, konzisztensen és az előre meghatározott követelményeknek megfelelően.

A munkafolyamatok futtatásának automatikus és manuális Security Center kapcsolatos további információkért lásd: [munkafolyamat-automatizálás.](workflow-automation.md)

További információ a [Logic Apps létrehozásáról.](../logic-apps/logic-apps-overview.md)


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integráció a Azure Security Center és Windows Admin Center

Most már áthelyezheti a helyszíni Windows-kiszolgálókat a Windows Admin Center a Azure Security Center. Security Center lesz az egyetlen ablaktábla, amely az összes Windows Admin Center-erőforrás biztonsági információit tartalmazza, beleértve a helyszíni kiszolgálókat, a virtuális gépeket és a további PaaS számítási feladatokat.

Miután átköltöztet egy kiszolgálót Windows Admin Center-Azure Security Center, a következőre lesz képes:

- A biztonsági riasztásokat és javaslatokat a Security Center bővítményében Windows Admin Center.
- Megtekintheti a biztonsági adatokat, és további részletes információkat Windows Admin Center felügyelt kiszolgálókról a Security Center -Azure Portal (vagy egy API-n keresztül).

További információ a [Azure Security Center és Windows Admin Center.](windows-admin-center-integration.md)


### <a name="protection-for-azure-kubernetes-service"></a>A Azure Kubernetes Service

Azure Security Center a tárolók biztonsági funkcióit is bővíti az Azure Kubernetes Service (AKS) védelme érdekében.

A népszerű, nyílt forráskódú Kubernetes platformot olyan széles körben fogadták el, hogy ma már a tárolóvezénylés iparági szabványa. A széles körű megvalósítás ellenére továbbra sem értjük a Kubernetes-környezetek védelmét. A tárolóba helyezni képes alkalmazások támadási felületének védelméhez szakértelemre van szükség ahhoz, hogy az infrastruktúra biztonságosan legyen konfigurálva, és folyamatosan monitorozni legyen a potenciális fenyegetésekre.

A Security Center védelmi csomag a következőket tartalmazza:

- **Felderítés és láthatóság** – Felügyelt AKS-példányok folyamatos felderítése az Security Center.
- **Biztonsági javaslatok** – Az AKS-hez ajánlott biztonsági eljárásoknak való megfelelést segítő, beavatkozást segítő javaslatok. Ezeket a javaslatokat a biztonsági pontszám tartalmazza, így biztosítva, hogy a szervezet biztonsági útjára is beleszámítsuk őket. Egy AKS-sel kapcsolatos javaslatra a következő példa láthat: "Szerepköralapú hozzáférés-vezérlést kell használni a Kubernetes-szolgáltatásfürtökhöz való hozzáférés korlátozására".
- **Fenyegetésvédelem** – Az AKS üzemelő példányának folyamatos elemzésével riasztásokat Security Center a gazdagép és az AKS-fürt szintjén észlelt fenyegetésekről és rosszindulatú tevékenységekről.

További információ az [Azure Kubernetes Services és](defender-for-kubernetes-introduction.md)a Security Center.

További információ a [tárolók biztonsági funkcióiról a Security Center.](container-security.md)


### <a name="improved-just-in-time-experience"></a>Továbbfejlesztett, időponthoz időben való kezelés

A felügyeleti portokat biztonságossá Azure Security Center eszközök funkcióit, működését és felhasználói felületét a következőképpen bővítettük: 

- **Justification (Indoklás)** mező – Amikor egy virtuális géphez (VM) való hozzáférést kér a Azure Portal igény szerint lapján, egy új nem kötelező mezőben megadhatja a kérelem indoklását. Az ebbe a mezőbe bevitt adatok nyomon követhetők a tevékenységnaplóban. 
- **Redundáns** igény szerinti (JIT) szabályok automatikus tisztítása – A JIT-szabályzatok frissítésekkor automatikusan lefut egy tisztítási eszköz, amely ellenőrzi a teljes szabálykészlet érvényességét. Az eszköz a szabályzatban és az NSG-ben a szabályok közötti különbségeket keres. Ha a tisztítási eszköz eltérést talál, akkor meghatározza az okot, és ha biztonságos, eltávolítja azokat a beépített szabályokat, amelyekre már nincs szükség. A tisztítás sohasem törli a létrehozott szabályokat. 

További információ a [JIT hozzáférési funkcióról.](security-center-just-in-time.md)


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>A webalkalmazások két biztonsági ajánlása elavult

A webalkalmazásokkal kapcsolatos két biztonsági javaslat elavult: 

- Az IaaS NSG-k webalkalmazásaira vonatkozó szabályokat fel kell dolgozni.
    (Kapcsolódó szabályzat: Az IaaS-alapú webalkalmazások NSG-szabályait meg kell fektetni)

- A hozzáférés App Services kell lennie.
    (Kapcsolódó szabályzat: A App Services korlátozva kell lennie [előzetes verzió])

Ezek a javaslatok a továbbiakban nem jelennek meg Security Center javaslatok listájában. A kapcsolódó szabályzatok a továbbiakban nem szerepelnek a "Default" (Alapértelmezett) Security Center kezdeményezésben.

További információ a [biztonsági javaslatokról.](recommendations-reference.md)




## <a name="february-2020"></a>2020. február

### <a name="fileless-attack-detection-for-linux-preview"></a>Fájl nélküli támadásészlelés Linux rendszeren (előzetes verzió)

Ahogy a támadók egyre több módszerrel kerülik el az észlelést, Azure Security Center a Windows mellett a Linuxra is kiterjesztik a fájl nélküli támadásészlelést. A fájl nélküli támadások kihasználják a szoftver biztonsági réseit, rosszindulatú hasznos kódot injektálnak a jóindulatú rendszerfolyamatokbe, és elrejtik a memóriát. Ezek a technikák:

- a kártevők nyomkövetésének minimalizálása vagy megszüntetése a lemezen
- nagymértékben csökkenti a lemezalapú kártevő-ellenőrzési megoldások észlelésének esélyét

A fenyegetés elleni Azure Security Center 2018 októberében megjelent a fájl nélküli támadásészlelés a Windows rendszerben, és mostantól Linuxon is kibővített fájl nélküli támadásészlelést is elérhető. 



## <a name="january-2020"></a>2020. január

### <a name="enhanced-secure-score-preview"></a>Továbbfejlesztett biztonsági pontszám (előzetes verzió)

A biztonsági pontszám funkció továbbfejlesztett verziója Azure Security Center előzetes verzióban érhető el. Ebben a verzióban több javaslat van biztonsági vezérlőkbe csoportosítva, amelyek jobban tükrözik a sebezhető támadási felületeket (például korlátozzák a felügyeleti portokhoz való hozzáférést).

Ismerkedjen meg a biztonsági pontszám változásaival az előzetes verzió fázisában, és határozza meg a környezet további védelmét segítő egyéb megoldásokat.

További információ a [továbbfejlesztett biztonsági pontszámról (előzetes verzió).](secure-score-security-controls.md)



## <a name="november-2019"></a>2019. november

Novemberi frissítések többek között a következők:
 - [Fenyegetések elleni védelem Azure Key Vault régiókban Észak-Amerika (előzetes verzió)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők hírnevének szűrését](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Munkafolyamat-automatizálás Logic Apps (előzetes verzió)](#workflow-automation-with-logic-apps-preview)
 - [gyorsjavítás általánosan elérhető a tömeges erőforrásokhoz](#quick-fix-for-bulk-resources-generally-available)
 - [Tároló rendszerképének biztonsági rések kereséséhez (előzetes verzió)](#scan-container-images-for-vulnerabilities-preview)
 - [További szabályozási megfelelőségi szabványok (előzetes verzió)](#additional-regulatory-compliance-standards-preview)
 - [Veszélyforrások elleni védelem Azure Kubernetes Service (előzetes verzió)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Virtuális gépek sebezhetőségi felmérése (előzetes verzió)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security azure-beli SQL-kiszolgálókhoz Virtual Machines (előzetes verzió)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Egyéni szabályzatok támogatása (előzetes verzió)](#support-for-custom-policies-preview)
 - [A Azure Security Center és partnerek platformján való lefedettség kiterjesztése](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Speciális integráció javaslatok és riasztások exportálásával (előzetes verzió)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [On-prem servers to Security Center from Windows Admin Center (preview)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Fenyegetésvédelem a Azure Key Vault régiókban Észak-Amerika (előzetes verzió)

Azure Key Vault egy alapvető szolgáltatás az adatok védelméhez és a felhőalkalmazások teljesítményének javításához azáltal, hogy lehetővé teszi a kulcsok, titkos kulcsok, titkosítási kulcsok és szabályzatok központi kezelését a felhőben. Mivel Azure Key Vault és üzleti szempontból kritikus fontosságú adatokat tárol, maximális biztonságot igényel a kulcstartók és a bennük tárolt adatok számára.

Azure Security Center a Azure Key Vault veszélyforrások elleni védelem további biztonsági intelligenciát biztosít, amely észleli a kulcstartók hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérleteket. Ez az új védelmi réteg lehetővé teszi az ügyfelek számára a kulcstartók elleni fenyegetések kezelését anélkül, hogy biztonsági szakértő vagy biztonsági monitorozási rendszerek felügyelete volna. A funkció nyilvános előzetes verzióban érhető el Észak-Amerika régiókban.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők hírnevének szűrését

Az Azure Storage veszélyforrások elleni védelme a Microsoft Threat Intelligence által működtetett új észleléseket kínál az Azure Storage-ba történő kártevőfeltöltések észleléséhez a kivonatok jó hírnevének elemzésével és egy aktív Tor kilépési csomópontból (anonimizálási proxy) származó gyanús hozzáféréssel. Most már megtekintheti az észlelt kártevőket a tárfiókok között a Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Munkafolyamat-automatizálás Logic Apps (előzetes verzió)

A központilag felügyelt biztonsággal és it-/üzemeltetési folyamatokkal felváltó szervezetek belső munkafolyamatokat alkalmaznak, amelyek a szükséges műveleteket hajtják végre a szervezeten belül, ha eltéréseket fedeznek fel a környezetekben. Sok esetben ezek a munkafolyamatok ismételhető folyamatok, az automatizálás pedig nagy mértékben leegyszerűsítheti a folyamatokat a szervezeten belül.

Ma egy új képességet vezetünk be az Security Center-ban, amely lehetővé teszi az ügyfelek számára, hogy az Azure Logic Apps használatával automatizálási konfigurációkat hozzanak létre, és olyan szabályzatokat hozzanak létre, amelyek automatikusan aktiválják őket bizonyos ASC-eredmények, például javaslatok vagy riasztások alapján. Az Azure Logic App konfigurálható úgy, hogy a Logic App-összekötők széles közössége által támogatott bármely egyéni műveletet el tudja látni, vagy használja az Security Center által biztosított sablonok valamelyikét, például e-mail küldését vagy ServiceNow-jegy &trade; megnyitását.

A munkafolyamatok futtatásának automatikus és manuális Security Center kapcsolatos további információkért lásd: [munkafolyamat-automatizálás.](workflow-automation.md)

További információ a Logic Apps létrehozásáról: [Azure Logic Apps.](../logic-apps/logic-apps-overview.md)


### <a name="quick-fix-for-bulk-resources-generally-available"></a>gyorsjavítás általánosan elérhető a tömeges erőforrásokhoz

A felhasználók által a biztonsági pontszám részeként biztosított számos feladat miatt kihívást jelenthet a nagy flotta problémáinak hatékony orvoslása.

A hibás biztonsági konfigurációk egyszerűbb szervizelése, valamint a nagy mennyiségű erőforrásra vonatkozó javaslatok gyors szervizelése és a biztonsági pontszám javítása érdekében használjon gyorsjavítás szervizelést.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni szeretné a szervizelést, és elindíthatja a javítási műveletet, amely konfigurálja a beállítást az Ön nevében.

A gyorsjavítás általánosan elérhető az ügyfelek számára a Security Center oldalán.

A biztonsági javaslatokra vonatkozó referencia-útmutatóban tekintse meg, hogy mely javaslatokhoz van engedélyezve a [gyorsjavítás.](recommendations-reference.md)


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Tároló rendszerképének biztonsági rések kereséséhez (előzetes verzió)

Azure Security Center a tároló rendszerképeit a Azure Container Registry biztonsági rések kereséséhez.

A rendszerkép-vizsgálat úgy működik, hogy elemezi a tároló rendszerképfájlját, majd ellenőrzi, hogy vannak-e ismert (Qualys által működtetett) biztonsági rések.

Maga a vizsgálat automatikusan aktiválódik, amikor új tároló rendszerképeket Azure Container Registry. A talált biztonsági rések Security Center javaslatokként fognak szerepelni, és szerepelnek az Azure biztonsági pontszámában, valamint információt tartalmaznak a javításukról az engedélyezett támadási felület csökkentése érdekében.


### <a name="additional-regulatory-compliance-standards-preview"></a>További szabályozási megfelelőségi szabványok (előzetes verzió)

A Jogszabályi megfelelőség irányítópult az értékelések alapján betekintést nyújt a megfelelőségi Security Center adataiba. Az irányítópult bemutatja, hogyan felel meg a környezete az adott szabályozási szabványok és iparági teljesítménytesztek által meghatározott vezérlőknek és követelményeknek, valamint részletes javaslatokat tesz a követelmények betartására.

A jogszabályi megfelelési irányítópult eddig négy beépített szabványt támogatott: Azure CIS 1.1.0, PCI-DSS, ISO 27001 és SOC-TSP. A következő további támogatott szabványok nyilvános előzetes kiadását jelentjük be: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM és UK Official az Egyesült Királyság Nemzeti Biztonsági Hivatalával együtt. Az Azure CIS 1.1.0 egy frissített verzióját is kiadjuk, amely több vezérlőt fed le a szabványból, és növeli a rendelkezésrehetőséget.

[További információ a szabványok testreszabásáról a jogszabályi megfelelési irányítópulton.](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Veszélyforrások elleni védelem Azure Kubernetes Service (előzetes verzió)

A Kubernetes gyorsan új szabványsá válik a szoftverek felhőben való üzembe helyezéséhez és kezeléséhez. Kevésen rendelkeznek széles körű tapasztalattal a Kubernetes terén, és sok csak az általános tervezésre és felügyeletre összpontosít, és figyelmen kívül hagyja a biztonsági aspektust. A Kubernetes-környezetet a biztonság érdekében körültekintően kell konfigurálni, hogy a tárolóközpontú támadási felület ne legyen nyitva, és ne legyen kitéve a támadóknak. Security Center a tárolóhely támogatását az Azure egyik leggyorsabban növekvő szolgáltatására , a Azure Kubernetes Service (AKS) bővíti.

A nyilvános előzetes kiadás új képességei a következők:

- **Felderítési & láthatósága** – Felügyelt AKS-példányok folyamatos felderítése Security Center regisztrált előfizetések között.
- **Biztonsági pontszámra** vonatkozó javaslatok – Olyan beavatkozást segítő elemek, amelyek segítenek az ügyfeleknek megfelelni az AKS-hez ajánlott biztonsági eljárásoknak, és növelik a biztonsági pontszámukat. A javaslatok közé tartoznak például a "Szerepköralapú hozzáférés-vezérlés használata a Kubernetes Service-fürthöz való hozzáférés korlátozására".
- **Fenyegetésészlelés** – Gazdagép- és fürtalapú elemzések, például "Emelt szintű tároló észlelhető".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Virtuális gépek sebezhetőségi felmérése (előzetes verzió)

A virtuális gépekre telepített alkalmazások gyakran biztonsági réseket is tartalmaznak, amelyek a virtuális gép biztonságának megsértéséhez vezethetnek. Bejelentjük, hogy a Security Center csomag beépített sebezhetőségi felmérést is tartalmaz a virtuális gépekhez további díj nélkül. A Qualys által a nyilvános előzetes verzióban elérhető sebezhetőségi felmérés lehetővé teszi, hogy folyamatosan vizsgálja a virtuális gépen telepített összes alkalmazást, és megtalálja a sebezhető alkalmazásokat, és bemutatja az eredményeket az Security Center portál felhasználói élményében. Security Center az összes üzembe helyezési műveletet, így a felhasználónak nincs szüksége további munkára. Tervezzük a sebezhetőségi felmérési lehetőségeket az ügyfeleink egyedi üzleti igényeinek támogatásához.

[További információ az Azure-beli biztonsági](deploy-vulnerability-assessment-vm.md)rések felmérése Virtual Machines.


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security az Azure Virtual Machines SQL-kiszolgálókhoz (előzetes verzió)

Azure Security Center IaaS virtuális gépeken futó SQL-adatbázisokkal kapcsolatos fenyegetésvédelem és biztonsági rések felmérésének támogatása előzetes verzióban érhető el.

A [sebezhetőségi felmérés](../azure-sql/database/sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Betekintést nyújt a biztonsági helyzetbe az Azure biztonsági pontszámának részeként, és tartalmazza a biztonsági problémák megoldásának és az adatbázis-egyesítés továbbfejlesztése lépéseit.

[Az Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) észleli az SQL-kiszolgálóhoz való hozzáférésre vagy annak kihasználására tett szokatlan és potenciálisan rosszindulatú kísérleteket jelző rendellenes tevékenységeket. Folyamatosan figyeli az adatbázis gyanús tevékenységeit, és műveletorientált biztonsági riasztásokat biztosít a rendellenes adatbázis-hozzáférési mintákról. Ezek a riasztások biztosítják a gyanús tevékenységek részleteit, valamint a fenyegetés kivizsgálására és elhárítására javasolt műveleteket.


### <a name="support-for-custom-policies-preview"></a>Egyéni szabályzatok támogatása (előzetes verzió)

Azure Security Center már támogatja az egyéni szabályzatokat (előzetes verzióban).

Ügyfeleink szeretnék kiterjeszteni a jelenlegi biztonsági felmérések lefedettségét a Security Center a saját biztonsági értékeléseikre a saját szabályzataik alapján, amelyek a Azure Policy. Az egyéni szabályzatok támogatásával ez már lehetséges.

Ezek az új szabályzatok a javaslatok Security Center, a biztonsági pontszám és a jogszabályi megfelelőségi szabványok irányítópultjának részei lesznek. Az egyéni szabályzatok támogatásával egyéni kezdeményezést hozhat létre a Azure Policy-ban, majd hozzáadhatja szabályzatként a Security Center és javaslatként vizualizálhatja azt.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>A Azure Security Center és partnerek platformján való lefedettség kiterjesztése

A Security Center nemcsak a Microsofttól kaphat javaslatokat, hanem olyan partnerektől származó meglévő megoldásoktól is, mint a Check Point, a Tenable és a CyberArk, amelyek számos további integrációval is elérhetőek.  Security Center egyszerű beépítési folyamatával összekapcsolhatja meglévő megoldásait az Security Center-hoz, így egyetlen helyen megtekintheti a biztonsági javaslatokat, egységes jelentéseket futtathat, és az Security Center összes funkcióját kihasználhatja a beépített és a partneri javaslatokhoz is. A partnertermékekbe Security Center is exportálhat javaslatokat.

[További információ a Microsoft Intelligent Security Associationről.](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Speciális integráció javaslatok és riasztások exportálásával (előzetes verzió)

A nagyvállalati szintű forgatókönyveknek a Security Center-n felüli engedélyezéséhez mostantól további helyeken is fel lehet használni Security Center riasztásokat és javaslatokat, kivéve az Azure Portal API-t. Ezek közvetlenül exportálhatók egy eseményközpontba és Log Analytics-munkaterületre. Az alábbi munkafolyamatok az alábbi új képességek köré hozhatók létre:

- A Log Analytics-munkaterületre való exportálással egyéni irányítópultokat hozhat létre a Power BI.
- Az Event Hubba való exportálással valós időben vagy más megoldásba exportálhatja az Security Center-riasztásokat és -javaslatokat a külső SIEM-Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Onboard on-prem servers to Security Center from Windows Admin Center (preview)

Windows Admin Center egy felügyeleti portál olyan Windows-kiszolgálókhoz, amelyek nincsenek telepítve az Azure-ban, több Azure-beli felügyeleti képességet is biztosítanak számukra, például a biztonsági mentést és a rendszerfrissítéseket. Nemrég hozzáadtunk egy képességet, hogy ezeket a nem Azure-beli kiszolgálókat közvetlenül a felhőből Windows Admin Center ASC védje.

Ezzel az új felhasználói élmével a felhasználók egy WAC-kiszolgálót fognak Azure Security Center a biztonsági riasztások és javaslatok megtekintésének engedélyezéséhez közvetlenül a Windows Admin Center felhasználói élményben.


## <a name="september-2019"></a>2019. szeptember

A szeptemberi frissítések a következők:

 - [Adaptív alkalmazásvezérlőkkel kapcsolatos szabályok kezelése – fejlesztések](#managing-rules-with-adaptive-application-controls-improvements)
 - [Tárolóbiztonsági javaslatok szabályozása a Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Adaptív alkalmazásvezérlőkkel kapcsolatos szabályok kezelése – fejlesztések

A virtuális gépekre vonatkozó szabályok adaptív alkalmazásvezérlőkkel való kezelésének élménye javult. Azure Security Center adaptív alkalmazásvezérlői segítségével szabályozhatja, hogy mely alkalmazások futtassanak a virtuális gépeken. A szabálykezelés általános fejlesztése mellett egy új előny lehetővé teszi annak szabályozását, hogy mely fájltípusok lesznek védve egy új szabály hozzáadásakor.

[További információ az adaptív alkalmazásvezérlőkről.](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Tárolóbiztonsági javaslatok szabályozása Azure Policy

Azure Security Center tárolóbiztonság biztonsági réseit javító javaslat mostantól engedélyezhető vagy letiltható a Azure Policy.

Az engedélyezett biztonsági szabályzatok megtekintéséhez nyissa Security Center a Biztonsági szabályzat lapot.


## <a name="august-2019"></a>2019. augusztus

Augusztusi frissítések a következők:

 - [Igény szerinti (JIT) virtuálisgép-hozzáférés Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Egykattintásos szervizelés a biztonsági biztonság növelése érdekében (előzetes verzió)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Bérlők közötti felügyelet](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Igény szerinti (JIT) virtuálisgép-hozzáférés Azure Firewall 

Mostantól általánosan elérhető az igény szerinti (JIT) Azure Firewall virtuális gépekhez. Az NSG által védett Azure Firewall mellett a védett környezetek védelmére is használhatja.

A JIT VM-hozzáférés azáltal csökkenti a hálózati mennyiségi támadásoknak való kitettséget, hogy szabályozott hozzáférést biztosít a virtuális gépekhez, csak szükség esetén, az NSG és a Azure Firewall használatával.

Amikor engedélyezi a JIT-t a virtuális gépek számára, létrehoz egy házirendet, amely meghatározza a védeni szükséges portokat, azt, hogy mennyi ideig kell nyitva tartaniuk, valamint a jóváhagyott IP-címeket, amelyekről ezek a portok elérhetők. Ez a szabályzat segít szabályozni, hogy a felhasználók milyen elérést kaphatnak a hozzáférés kérésekor.

A kérések az Azure-tevékenységnaplóban vannak naplózva, így könnyedén figyelheti és naplózhatja a hozzáférést. Az igény szerinti oldalon emellett gyorsan azonosíthatja azokat a meglévő virtuális gépeket, amelyeken engedélyezve van a JIT, valamint azokat a virtuális gépeket, amelyeken a JIT használata javasolt.

[További információ a Azure Firewall.](../firewall/overview.md)


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Egykattintásos szervizelés a biztonsági biztonság növelése érdekében (előzetes verzió)

A biztonsági pontszám egy olyan eszköz, amely segít felmérni a számítási feladatok biztonsági környezetét. Áttekinti és rangsorolja a biztonsági javaslatokat, hogy tudja, mely javaslatokat kell elsőként elvégeznie. Ez segít megtalálni a súlyos biztonsági réseket a vizsgálat rangsorolása érdekében.

A biztonsági konfigurációk egyszerűbb szervizelése és a biztonsági pontszám gyors javítása érdekében egy új funkcióval egészül ki, amely lehetővé teszi, hogy több erőforrásra vonatkozó javaslatot egyetlen kattintással orvosoljon.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni szeretné a szervizelést, és elindíthatja a javítási műveletet, amely konfigurálja a beállítást az Ön nevében.

A biztonsági javaslatokra vonatkozó referencia-útmutatóban tekintse meg, hogy mely javaslatokhoz van engedélyezve a [gyorsjavítás.](recommendations-reference.md)


### <a name="cross-tenant-management"></a>Bérlők közötti felügyelet

Security Center már támogatja a bérlők közötti felügyeleti forgatókönyveket a Azure Lighthouse. Így láthatóvá és kezelhetővé válik több bérlő biztonsági Security Center. 

[További információ a bérlők közötti felügyeleti élményről.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>2019. július

### <a name="updates-to-network-recommendations"></a>Hálózati javaslatok frissítései

Azure Security Center (ASC) új hálózatra vonatkozó javaslatokat indított, és továbbfejleszt néhány meglévőt. Mostantól a Security Center még nagyobb hálózati védelmet biztosít az erőforrások számára. 

[További információ a hálózati javaslatokról.](recommendations-reference.md#recs-networking)


## <a name="june-2019"></a>2019. június

### <a name="adaptive-network-hardening---generally-available"></a>Adaptív hálózat-megsokolás – általánosan elérhető

A nyilvános felhőben futó számítási feladatok egyik legnagyobb támadási felülete a nyilvános internethez való csatlakozás és az onnan való ki- és bekapcsolat. Ügyfeleink nehezen tudják, hogy melyik hálózati biztonsági csoport (NSG) szabályai szükségesek annak érdekében, hogy az Azure-beli számítási feladatok csak a szükséges forrástartományok számára érhetők el. Ezzel a funkcióval a Security Center megismeri az Azure-beli számítási feladatok hálózati forgalmi és kapcsolati mintáit, és NSG-szabályokra vonatkozó javaslatokat tesz az internetre néző virtuális gépekhez. Ez segít az ügyfeleinknek jobban konfigurálni a hálózati hozzáférési szabályzatokat, és korlátozni a támadásoknak való kitettségüket. 

[További információ az adaptív hálózat-megsokolásról.](security-center-adaptive-network-hardening.md)