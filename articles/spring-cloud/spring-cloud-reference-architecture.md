---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Azure Spring Cloud-referenciák architektúrája
ms.author: akaleshian
ms.service: spring-cloud
description: Ez a viszonyítási architektúra egy tipikus nagyvállalati központ, és az Azure Spring Cloud használatának az alapja.
ms.openlocfilehash: c87462c8d6ab86299b6202acaa23f93d19240e0e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735488"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure Spring Cloud-referenciák architektúrája

Ez a viszonyítási architektúra egy tipikus nagyvállalati központ, és az Azure Spring Cloud használatának az alapja. A tervben az Azure Spring Cloud üzembe helyezése egyetlen küllőn történik, amely a központban üzemeltetett megosztott szolgáltatástól függ. Az architektúra a [Microsoft Azure Well-Architected-keretrendszer][16]alapelveinek eléréséhez készült összetevőket használ.

Ennek az architektúrának a megvalósításához tekintse meg az [Azure Spring Cloud Reference Architecture][10] -tárházat a githubon.

Az architektúra üzembe helyezési lehetőségei közé tartozik a Azure Resource Manager (ARM), a Terraform és az Azure CLI. Az ebben a tárházban található összetevők olyan alapot biztosítanak, amelyet testreszabhat a környezetében. Más erőforrás-csoportokra vagy előfizetésekre is csoportosíthatja az erőforrásokat, például Azure Firewall vagy Application Gateway. Ez a csoportosítás segíti a különböző funkciók elkülönítését, például az informatikai infrastruktúrát, a biztonságot, az üzleti alkalmazások csapatait és így tovább.

## <a name="planning-the-address-space"></a>Címterület megtervezése

Az Azure Spring Cloud két dedikált alhálózatot igényel:

* Szolgáltatás futtatókörnyezete
* Spring boot-alkalmazások

Ezen alhálózatok mindegyike dedikált fürtöt igényel. Több fürt nem tudja ugyanazt az alhálózatot megosztani. Az egyes alhálózatok minimális mérete/28. Az Azure Spring Cloud által támogatott alkalmazások példányainak száma az alhálózat méretétől függően változhat. Az [Azure Spring Cloud üzembe helyezése virtuális hálózatban][17]című témakör részletes Virtual Network (VNET) követelményeit a [virtuális hálózati követelmények][11] című szakaszban találja.

> [!WARNING]
> A kiválasztott alhálózati méret nem fedi át a meglévő VNET, és nem fedi át az átfedésben lévő, illetve a helyszíni alhálózati címtartományt.

## <a name="use-cases"></a>Használati esetek

Az architektúra gyakori használati módjai többek között a következők:

* Hibrid felhőalapú környezetekben üzembe helyezett belső alkalmazások
* Külsőleg szembenéző alkalmazások

Ezek a használati esetek hasonlóak, kivéve a biztonsági és hálózati forgalmi szabályaikat. Ez az architektúra úgy lett kialakítva, hogy támogassa az egyes árnyalatait.

## <a name="private-applications"></a>Magánjellegű alkalmazások

A következő lista ismerteti a magánjellegű alkalmazások infrastruktúrára vonatkozó követelményeit. Ezek a követelmények a jól szabályozott környezetekben jellemzőek.

* Nincs közvetlen forgalom a nyilvános internetre a vezérlési sík forgalmának kivételével.
* A kimenő forgalomnak egy központi hálózati virtuális berendezésen (NVA) keresztül kell utaznia (például Azure Firewall).
* A nyugalmi állapotban lévő adatok titkosítása szükséges.
* Az átvitt adatforgalomnak titkosítottnak kell lennie.
* A DevOps üzembe helyezési folyamatai (például az Azure DevOps) és a hálózati kapcsolat megkövetelése az Azure Spring Cloud használatával.
* A Microsoft zéró megbízhatósági kapcsolatának biztonsági megközelítése megköveteli a biztonságos tárolóban tárolt titkokat, tanúsítványokat és hitelesítő adatokat. Az ajánlott szolgáltatás Azure Key Vault.
* Az Application Host Domain Name Service (DNS) rekordjait az Azure saját DNSban kell tárolni.
* A helyszíni és a felhőben lévő gazdagépek névfeloldásának kétirányúnak kell lennie.
* Legalább egy biztonsági teljesítményteszt betartását kényszeríteni kell.
* Az Azure-szolgáltatás függőségeinek szolgáltatási végpontokon vagy privát kapcsolaton keresztül kell kommunikálnia.
* Az Azure Spring Cloud üzemelő példány által kezelt erőforráscsoportok nem módosíthatók.
* Az Azure Spring Cloud üzemelő példány által kezelt alhálózatok nem módosíthatók.
* Az alhálózatnak csak egy Azure Spring Cloud-példánytal kell rendelkeznie.
* Ha az [Azure Spring Cloud config Server][8] használatával tölti be a konfigurációs tulajdonságokat egy adattárból, a tárháznak magánjellegűnek kell lennie.

A következő lista a kialakítást alkotó összetevőket mutatja be:

* Helyszíni hálózat
  * Tartománynév-szolgáltatás (DNS)
  * Átjáró
* Hub-előfizetés
  * Alhálózat Azure Firewall
  * Alhálózat Application Gateway
  * Megosztott szolgáltatások alhálózata
* Csatlakoztatott előfizetés
  * Virtual Network társ
  * Azure megerősített alhálózat

A következő lista ismerteti az Azure-szolgáltatásokat ebben a hivatkozási architektúrában:

* [Azure Spring Cloud][1]: felügyelt szolgáltatás, amely kifejezetten Java-alapú Spring boot-alkalmazásokhoz készült és optimalizált. NET-alapú [Steeltoe][9] -alkalmazások.

* [Azure Key Vault][2]: a hardveres Hitelesítőadat-kezelő szolgáltatás, amely szoros integrációt nyújt a Microsoft Identity Services és a számítási erőforrások között.

* [Azure monitor][3]: az Azure-ban és a helyszínen egyaránt üzembe helyezhető alkalmazások figyelési szolgáltatásainak teljes körű csomagja.

* [Azure Security Center][4]: egységes biztonsági felügyeleti és veszélyforrások elleni védelmi rendszer a helyszíni, több Felhőbeli és Azure-beli számítási feladatok ellátásához.

* [Azure-folyamatok][5]: teljes funkcionalitású folyamatos integrációs/folyamatos fejlesztési (CI/CD) szolgáltatás, amely képes automatikusan telepíteni a frissített Spring boot-alkalmazásokat az Azure Spring Cloud-ba.

Az alábbi ábrán egy jól felépített hub és küllős kialakítás látható, amely a fenti követelményekkel foglalkozik:

![Hivatkozási architektúra diagram privát alkalmazásokhoz](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Nyilvános alkalmazások

A következő lista ismerteti a nyilvános alkalmazások infrastrukturális követelményeit. Ezek a követelmények a jól szabályozott környezetekben jellemzőek.

* A bejövő forgalom forgalmát legalább Application Gateway vagy az Azure bejárati Ajtójának kell kezelnie.
* Azure DDoS Protection a standardot engedélyezni kell.
* Nincs közvetlen forgalom a nyilvános internetre a vezérlési sík forgalmának kivételével.
* A kimenő forgalomnak meg kell haladnia egy központi hálózati virtuális berendezésen (NVA) (például Azure Firewall).
* A nyugalmi állapotban lévő adatok titkosítása szükséges.
* Az átvitt adatforgalomnak titkosítottnak kell lennie.
* A DevOps üzembe helyezési folyamatai (például az Azure DevOps) és a hálózati kapcsolat megkövetelése az Azure Spring Cloud használatával.
* A Microsoft zéró megbízhatósági kapcsolatának biztonsági megközelítése megköveteli a biztonságos tárolóban tárolt titkokat, tanúsítványokat és hitelesítő adatokat. Az ajánlott szolgáltatás Azure Key Vault.
* Az Application Host DNS-rekordjait az Azure saját DNSban kell tárolni.
* Az internetre irányítható címeket az Azure nyilvános DNS-ben kell tárolni.
* A helyszíni és a felhőben lévő gazdagépek névfeloldásának kétirányúnak kell lennie.
* Legalább egy biztonsági teljesítményteszt betartását kényszeríteni kell.
* Az Azure-szolgáltatás függőségeinek szolgáltatási végpontokon vagy privát kapcsolaton keresztül kell kommunikálnia.
* Az Azure Spring Cloud üzemelő példány által kezelt erőforráscsoportok nem módosíthatók.
* Az Azure Spring Cloud üzemelő példány által kezelt alhálózatok nem módosíthatók.
* Az alhálózatnak csak egy Azure Spring Cloud-példánytal kell rendelkeznie.

A következő lista a kialakítást alkotó összetevőket mutatja be:

* Helyszíni hálózat
  * Tartománynév-szolgáltatás (DNS)
  * Átjáró
* Hub-előfizetés
  * Alhálózat Azure Firewall
  * Alhálózat Application Gateway
  * Megosztott szolgáltatások alhálózata
* Csatlakoztatott előfizetés
  * Virtual Network társ
  * Azure megerősített alhálózat

A következő lista ismerteti az Azure-szolgáltatásokat ebben a hivatkozási architektúrában:

* [Azure Spring Cloud][1]: felügyelt szolgáltatás, amely kifejezetten Java-alapú Spring boot-alkalmazásokhoz készült és optimalizált. NET-alapú [Steeltoe][9] -alkalmazások.

* [Azure Key Vault][2]: a hardveres Hitelesítőadat-kezelő szolgáltatás, amely szoros integrációt nyújt a Microsoft Identity Services és a számítási erőforrások között.

* [Azure monitor][3]: az Azure-ban és a helyszínen egyaránt üzembe helyezhető alkalmazások figyelési szolgáltatásainak teljes körű csomagja.

* [Azure Security Center][4]: egységes biztonsági felügyeleti és veszélyforrások elleni védelmi rendszer a helyszíni, több Felhőbeli és Azure-beli számítási feladatok ellátásához.

* [Azure-folyamatok][5]: teljes funkcionalitású folyamatos integrációs/folyamatos fejlesztési (CI/CD) szolgáltatás, amely képes automatikusan telepíteni a frissített Spring boot-alkalmazásokat az Azure Spring Cloud-ba.

* [Azure Application Gateway][6]: a 7. rétegben üzemelő TRANSPORT Layer Security (TLS) kiszervezéssel történő alkalmazás-forgalomért felelős terheléselosztó.

* [Azure Application Firewall][7]: az Azure Application Gateway egyik funkciója, amely központosított védelmet nyújt az alkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen.

Az alábbi ábrán egy jól felépített hub és küllős kialakítás látható, amely a fenti követelményekkel foglalkozik:

![A nyilvános alkalmazások hivatkozási architektúrájának diagramja](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure Spring Cloud helyszíni kapcsolat

Az Azure Spring Cloud-on futó alkalmazások különböző Azure-, helyszíni és külső erőforrásokkal kommunikálhatnak. A hub és a küllős kialakítás használatával az alkalmazások az expressz útvonal vagy a helyek közötti virtuális magánhálózat (VPN) használatával átirányítják a forgalmat külsőleg vagy a helyszíni hálózatra.

## <a name="azure-well-architected-framework-considerations"></a>Az Azure Well-Architected Framework szempontjai

Az [Azure Well-Architected Framework][16] olyan irányadó elvek összessége, amelyek az erős infrastruktúra-alaprendszer létrehozásához szükségesek. A keretrendszer a következő kategóriákat tartalmazza: Cost Optimization, Operational Excellence, teljesítmény hatékonyság, megbízhatóság és biztonság.

### <a name="cost-optimization"></a>Költségoptimalizálás

Az elosztott rendszer kialakításának jellegéből adódóan az infrastruktúra terjeszkedése valóság. Ez a valóság váratlan és nem ellenőrizhető költségeket eredményez. Az Azure Spring Cloud olyan összetevők használatával lett felépítve, amelyek méretezhetők, hogy megfeleljenek az igényeknek és optimalizálják a költségeket. Ennek az architektúrának a lényege az Azure Kubernetes szolgáltatás (ak). A szolgáltatás úgy lett kialakítva, hogy csökkentse a Kubernetes kezelésének összetettségét és működési terhelését, ami magában foglalja a fürt működési költségeinek hatékonyságát is.

Különböző alkalmazásokat és alkalmazás-típusokat telepíthet az Azure Spring Cloud egyetlen példányára. A szolgáltatás támogatja a metrikák vagy ütemtervek által aktivált alkalmazások automatikus skálázását, amelyek javítják a kihasználtságot és a költséghatékonyságot.

Az üzemeltetési költségeket Application Insights és Azure Monitor is használhatja. Az átfogó naplózási megoldás által biztosított láthatósággal valós időben lehet megvalósítani a rendszerek összetevőinek méretezését. Elemezheti a naplózási adataikat is, hogy az alkalmazás kódjában felfedje a hatékonyságot, hogy javítsa a rendszer általános költségeit és teljesítményét.

### <a name="operational-excellence"></a>Működésbeli kiválóság

Az Azure Spring Cloud az operatív kiválóság több aspektusával foglalkozik. Ezeket a szempontokat kombinálva ellenőrizheti, hogy a szolgáltatás hatékonyan fut-e az éles környezetekben, az alábbi listában leírtak szerint:

* Az Azure-folyamatok használatával biztosíthatja, hogy az üzembe helyezések megbízhatóak és konzisztensek legyenek, miközben segít elkerülni az emberi hibákat.
* A napló-és telemetria tárolására Azure Monitor és Application Insights is használhatja.
  Az összegyűjtött napló-és metrikai adatokat felhasználhatja az alkalmazások állapotának és teljesítményének biztosítása érdekében. Az alkalmazás teljesítményének monitorozása (APM) a Java-ügynökön keresztül teljes mértékben integrálódik a szolgáltatásba. Ez az ügynök az összes telepített alkalmazáshoz és függőséghez külön kód nélkül nyújt láthatóságot. További információ: az [Azure Spring Cloud alkalmazásait és függőségeit könnyedén nyomon][15]követő blogbejegyzés.
* A Azure Security Center használatával biztosíthatja, hogy az alkalmazások biztonságban maradjanak azáltal, hogy platformot biztosítanak a megadott információk elemzéséhez és értékeléséhez.
* A szolgáltatás különböző üzembe helyezési mintákat támogat. További információ: [átmeneti környezet beállítása az Azure Spring Cloud-ban][14].

### <a name="reliability"></a>Megbízhatóság

Az Azure Spring Cloud alapszintű összetevőként van kialakítva az AK-val. Míg az AK rugalmassági szintet biztosít a fürtözéshez, ez a hivatkozási architektúra szolgáltatások és építészeti szempontokat is tartalmaz, amelyekkel növelheti az alkalmazás rendelkezésre állását, ha az összetevő meghibásodása van.

Egy jól definiált hub és küllő kialakításával az architektúra alapja biztosítja, hogy több régióban is üzembe helyezhető. A magánjellegű alkalmazás használati esetéhez az architektúra az Azure saját DNS használatával biztosítja a folyamatos rendelkezésre állást a földrajzi meghibásodások során. A nyilvános alkalmazás használati esetéhez az Azure bejárati ajtó és az Azure Application Gateway biztosítja a rendelkezésre állást.

### <a name="security"></a>Biztonság

Ennek az architektúrának a biztonsága az iparág által meghatározott vezérlők és referenciaértékek betartásával foglalkozik. Az ebben az architektúrában lévő vezérlőelemek a Cloud [Control Matrix][19] (CCM) a [Cloud Security Alliance][18] (CSA) és a [Microsoft Azure founds benchmark][20] (MAFB) által a [Center for Internet Security][21] (CIS) számára. Az alkalmazott vezérlőkben a hangsúly az irányítás, a hálózat és az alkalmazás biztonságának elsődleges biztonsági tervezési alapelvein alapul. Az Ön felelőssége, hogy kezelje az identitás, a hozzáférés-kezelés és a tárolás tervezési alapelveit, mivel azok a megcélzott infrastruktúrához kapcsolódnak.

#### <a name="governance"></a>Szabályozás

Az architektúra által a hálózati erőforrások elkülönítése révén elkülöníthető irányítás elsődleges aspektusa. A CCM-ben a DCS-08 az adatközpont bejövő és kimenő vezérlését javasolja. A vezérlő kielégítése érdekében az architektúra a hálózati biztonsági csoportokkal (NSG) elválasztott hub és küllős kialakítást használ az erőforrások közötti kelet-nyugati forgalom szűrésére. Az architektúra a központi szolgáltatások és a küllő erőforrásai közötti forgalmat is szűri. Az architektúra a Azure Firewall egy példányát használja az Internet és az architektúrán belüli erőforrások közötti forgalom kezelésére.

A következő lista azt a vezérlőt mutatja be, amely az adatközpontok biztonságával foglalkozik ebben a hivatkozásban:

| CSA CCM-vezérlő azonosítója | CSA CCM-vezérlő tartománya |
| :----------------- | :----------------------|
| DCS-08 | Adatközpontok biztonságának jogosulatlan személyek beléptetése |

#### <a name="network"></a>Network (Hálózat)

Az architektúrát támogató hálózati kialakítás a hagyományos hub és küllő modellből származik. Ez a döntés biztosítja, hogy a hálózati elkülönítés egy alapszerkezet. A CCM Control IVS-06 azt javasolja, hogy a hálózatok és a virtuális gépek közötti forgalom korlátozott és a megbízható és nem megbízható környezetek között legyen figyelve. Ez az architektúra a NSG a kelet-nyugati forgalom számára történő megvalósításával, valamint az észak-déli forgalom Azure Firewallét alkalmazza. A CCM Control IPY-04 azt javasolja, hogy az infrastruktúrának biztonságos hálózati protokollokat kell használnia a szolgáltatások közötti adatcseréhez. Az architektúrát támogató Azure-szolgáltatások mindegyike szabványos biztonságos protokollokat használ, például a TLS-t HTTP-re és SQL-re.

A következő lista a CCM azon vezérlőit mutatja be, amelyek a jelen hivatkozásban a hálózati biztonsággal foglalkoznak:

| CSA CCM-vezérlő azonosítója | CSA CCM-vezérlő tartománya |
| :----------------- | :----------------------|
| IPY – 04             | Hálózati protokollok      |
| IVS-06             | Hálózati biztonság       |

A hálózat implementációja a MAFB vezérlők meghatározásával továbbra is védve van. A vezérlők biztosítják, hogy a környezetbe irányuló forgalom csak a nyilvános internetről legyen korlátozva.

A következő lista azokat a CIS-vezérlőket mutatja be, amelyek a hálózati biztonsággal foglalkoznak ebben a hivatkozásban:

| CIS-vezérlő azonosítója | CIS-vezérlő leírása |
| :------------- | :---------------------- |
| 6,2 | Győződjön meg arról, hogy az SSH-hozzáférés korlátozva van az internetről. |
| 6.3 | Győződjön meg arról, hogy az SQL-adatbázisok nem engedélyezik a bejövő 0.0.0.0/0 (bármely IP-cím) használatát. |
| 6.5 | Győződjön meg arról, hogy a Network Watcher engedélyezve van. |
| 6.6 | Győződjön meg arról, hogy az UDP-t használó bejövő forgalom az internetről korlátozódik. |

Az Azure Spring Cloud a biztonságos környezetekben történő üzembe helyezéskor az Azure-ból kimenő forgalomra vonatkozó felügyeleti forgalmat igényel. Ennek elvégzéséhez engedélyeznie kell a hálózat és az alkalmazás az [Azure Spring Cloud VNET-ben való futtatásához szükséges felhasználói felelősségi körökben](./spring-cloud-vnet-customer-responsibilities.md)felsorolt szabályokat.

#### <a name="application-security"></a>Alkalmazások biztonsága

Ez a kialakítási tag az identitás, az adatvédelem, a kulcskezelő és az alkalmazások konfigurációjának alapvető összetevőit fedi le. A tervezés szerint az Azure Spring Cloud-ban üzembe helyezett alkalmazások legalább a működéshez szükséges jogosultságokkal rendelkeznek. Az engedélyezési vezérlők készlete közvetlenül kapcsolódik az adatvédelemhoz a szolgáltatás használatakor. A kulcskezelő szolgáltatás megerősíti ezt a többrétegű alkalmazás biztonsági megközelítését.

A következő lista az ebben a hivatkozásban található kulcskezelő vezérlőket mutatja:

| CSA CCM-vezérlő azonosítója | CSA CCM-vezérlő tartománya |
| :----------------- | :--------------------- |
| EKM-01 | Titkosítási és kulcskezelő jogosultság |
| EKM – 02 | Titkosítási és kulcskezelő kulcs létrehozása |
| EKM – 03 | A titkosítás és a kulcs kezelése bizalmas adatok védelme |
| EKM – 04 | Titkosítás és kulcskezelő tároló és hozzáférés |

A CCM, a EKM-02 és a EKM-03 ajánlott szabályzatokat és eljárásokat javasol a kulcsok kezeléséhez és titkosítási protokollok használatával a bizalmas adatok védelme érdekében. A EKM-01 azt javasolja, hogy minden kriptográfiai kulcs azonosítható tulajdonossal rendelkezzen, hogy kezelni lehessen őket. A EKM-04 standard algoritmusok használatát javasolja.

A következő lista azokat a CIS-vezérlőket mutatja be, amelyek a jelen hivatkozásban található kulcskezelő szolgáltatással foglalkoznak:

| CIS-vezérlő azonosítója | CIS-vezérlő leírása |
| :------------- | :---------------------- |
| 8.1 | Győződjön meg arról, hogy a lejárati dátum minden kulcson be van állítva. |
| 8.2 | Győződjön meg arról, hogy a lejárati dátum minden titoknál be van állítva. |
| 8.4 | Győződjön meg arról, hogy a kulcstartó helyreállítható. |

Az 8,1-es és 8,2-es CIS-vezérlők azt ajánlják, hogy a lejárati dátumok a hitelesítő adatokhoz legyenek beállítva, hogy az elforgatás A CIS Control 8,4 biztosítja, hogy a kulcstartó tartalma visszaállítható az üzletmenet folytonosságának fenntartása érdekében.

Az alkalmazások biztonságának szempontjai a jelen hivatkozási architektúra használatára alapozva támogatják az Azure-beli rugós munkaterhelést.

## <a name="next-steps"></a>Következő lépések

Fedezze fel ezt a hivatkozási architektúrát az [Azure Spring Cloud Reference architektúra][10] -tárházban elérhető ARM-, Terraform-és Azure CLI-telepítések segítségével.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: https://azure.microsoft.com/resources/cis-microsoft-azure-foundations-security-benchmark/
[21]: https://www.cisecurity.org/