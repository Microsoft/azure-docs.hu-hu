---
title: Azure Red Hat OpenShift hozzárendelési mátrix
description: Megismeri a felelősségi körök tulajdonjogát a fürtök Azure Red Hat OpenShift számára
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, támogatás
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537008"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>A feladatkörök áttekintése Azure Red Hat OpenShift

Ez a dokumentum a Microsoft, a Red Hat és az ügyfelek feladatait ismerteti a Azure Red Hat OpenShift fürtök esetében. További információ a Azure Red Hat OpenShift összetevőiről: Azure Red Hat OpenShift Service Definition (Szolgáltatás Azure Red Hat OpenShift).

Míg a Microsoft és a Red Hat kezelik Azure Red Hat OpenShift szolgáltatást, az ügyfél osztozik a fürt funkcióiért. Bár Azure Red Hat OpenShift fürtök az ügyfél Azure-előfizetésében található Azure-erőforrásokon üzemelnek, távolról érhetők el. A mögöttes platform- és adatbiztonság a Microsoft és a Red Hat tulajdonában van.

## <a name="overview"></a>Áttekintés
<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Incidens- és üzemeltetéskezelés</a></strong>
   </td>
   <td><strong><a href="#change-management">Változáskezelés</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Identitás- és hozzáférés-kezelés</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Biztonság és szabályozás megfelelősége</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Ügyféladatok</a>
   </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Ügyfélalkalmazások</a>
   </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Fejlesztői szolgáltatások </a>
   </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
   <td>Ügyfél </td>
  </tr>
  <tr>
   <td>Platformfigyelés </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Naplózás </td>
   <td>Microsoft és Red Hat </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
  </tr>
  <tr>
   <td>Alkalmazáshálózat </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Fürthálózat </td>
   <td>Microsoft és Red Hat </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Virtuális hálózat </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
   <td>Megosztott </td>
  </tr>
  <tr>
   <td>Vezérlősík-csomópontok </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Munkavégző csomópontok </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Fürt verziója </td>
   <td>Microsoft és Red Hat </td>
   <td>Megosztott </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Kapacitáskezelés </td>
   <td>Microsoft és Red Hat </td>
   <td>Megosztott </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Virtuális tároló </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
  <tr>
   <td>Fizikai infrastruktúra és biztonság </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
   <td>Microsoft és Red Hat </td>
  </tr>
</table>


1. táblázat Felelősségek erőforrás szerint


## <a name="tasks-for-shared-responsibilities-by-area"></a>Megosztott felelősségi körök feladatai terület szerint 

### <a name="incident-and-operations-management"></a>Incidens- és üzemeltetéskezelés 

Az ügyfél és a Microsoft és a Red Hat osztoznak a fürtök figyelésében és Azure Red Hat OpenShift felügyeletében. Az ügyfél felelős az ügyfélalkalmazás [](#customer-data-and-applications) adatainak incidensek és műveletek kezeléséért, valamint az ügyfél által konfigurált egyéni hálózatkezelésért.

<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong>A Microsoft és a Red Hat feladatkörei</strong>
   </td>
   <td><strong>Ügyfél feladatkörei</strong>
   </td>
  </tr>
  <tr>
   <td>Alkalmazáshálózat </td>
   <td>
<ul>

<li>Figyelje a felhőalapú terheléselosztást és a natív OpenShift-útválasztó szolgáltatást, és válaszoljon a riasztásra.
</li>
</ul>
   </td>
   <td>
<ul>

<li>A szolgáltatás terheléselosztási végpontjainak állapotának figyelése.

<li>Az alkalmazásútvonalak és a mögöttes végpontok állapotának figyelése.

<li>Jelentse a Microsoftnak és a Red Hatnak a szolgáltatáskimaradásokat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuális hálózat
   </td>
   <td>
<ul>

<li>Az alapértelmezett platformhálózathoz szükséges felhőbeli terheléselosztások, alhálózatok és Azure-felhőösszetevők monitorozása, valamint a riasztások megválaszolása.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Figyelje a virtuális hálózatok és virtuális hálózatok között opcionálisan konfigurált hálózati forgalmat, a VPN-kapcsolatot vagy Private Link esetleges problémákat vagy biztonsági fenyegetéseket.
</li>
</ul>
   </td>
  </tr>
</table>


2. táblázat Az incidens- és üzemeltetéskezelés megosztott feladatai


### <a name="change-management"></a>Változáskezelés

A Microsoft és a Red Hat felelős a fürt infrastruktúrájának és az ügyfél által vezérelt szolgáltatások módosításainak engedélyezéséért, valamint a fő csomópontok, az infrastruktúra-szolgáltatások és a munkavégző csomópontok számára elérhető verziók fenntartásáért. Az ügyfél felelős az infrastruktúra módosításainak kezdeményezéséért, valamint a választható szolgáltatások és hálózati konfigurációk telepítéséért és karbantartásáért a fürtön, valamint az ügyféladatok és az ügyfélalkalmazások minden változásáért.


<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong>A Microsoft és a Red Hat feladatkörei</strong>
   </td>
   <td><strong>Ügyfél feladatkörei</strong>
   </td>
  </tr>
  <tr>
   <td>Naplózás </td>
   <td>
<ul>

<li>A platform auditnaplóinak központi összesítése és figyelése.

<li>Dokumentációt nyújt az ügyfél számára, hogy engedélyezze az alkalmazásnaplózást a Log Analytics használatával Azure Monitor tárolókhoz.

<li>Adja meg az auditnaplókat az ügyfél kérése esetén.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Telepítse a választható alapértelmezett alkalmazásnaplózási operátort a fürtön.

<li>Opcionális alkalmazásnaplózási megoldások telepítése, konfigurálása és karbantartása, például oldalkocsi tárolók naplózása vagy külső naplózási alkalmazások.

<li>Hangolja az ügyfélalkalmazások által előállított alkalmazásnaplók méretét és gyakoriságát, ha azok hatással vannak a fürt stabilitására.

<li>Platform-auditnaplók kérése egy támogatási eseten keresztül adott incidensek vizsgálatához.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Alkalmazáshálózat
   </td>
   <td>
<ul>

<li>Nyilvános felhőbeli terheléselosztások beállítása

<li>Natív OpenShift-útválasztó szolgáltatás beállítása. Lehetővé teszi az útválasztó privátként való beállítását, és egy további útválasztó-szegmens hozzáadását.

<li>OpenShift SDN-összetevők telepítése, konfigurálása és karbantartása az alapértelmezett belső podforgalomhoz.
</li>
</ul>
   </td>
   <td>
<ul>

<li>A NetworkPolicy objektumok használatával konfigurálja a nem alapértelmezett podhálózati engedélyeket a projekt- és podhálózatok, a podok bejövő és bejövő forgalom számára.

<li>Kérjen le és konfigurálja az adott szolgáltatásokhoz szükséges további szolgáltatás-terheléselosztásokat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Fürthálózat
   </td>
   <td>
<ul>

<li>Állítson be fürtkezelési összetevőket, például nyilvános vagy privát szolgáltatásvégpontokat, és a szükséges integrációt a virtuális hálózati összetevőkkel.

<li>A feldolgozó és a fő csomópontok közötti belső fürtkommunikációhoz szükséges belső hálózati összetevők beállítása.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Opcionális nem alapértelmezett IP-címtartományok megadása a gép CIDR-éhez, a szolgáltatás CIDR-éhez és szükség esetén pod CIDR-hez az OpenShift-fürtkezelőn keresztül a fürt kiépítésekor.

<li>Kérje meg, hogy az API-szolgáltatásvégpont nyilvános vagy privát legyen a fürt létrehozásakor vagy az Azure CLI-n keresztüli fürt létrehozása után.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuális hálózat
   </td>
   <td>
<ul>

<li>A fürt építéséhez szükséges virtuális hálózati összetevők( például virtuális magánfelhő, alhálózatok, terheléselosztások, internetes átjárók, NAT-átjárók stb.) beállítása és konfigurálása.

<li>Lehetővé teszi az ügyfél számára a VPN-kapcsolatok kezelését a helyszíni erőforrásokkal, a virtuális hálózatok és a virtuális hálózatok Private Link az OpenShift-fürtkezelővel.

<li>Lehetővé teheti, hogy az ügyfelek nyilvános felhőbeli terheléselosztást hozzanak létre és telepítsenek a szolgáltatás terheléselosztásával való használatra.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Opcionális nyilvános felhőbeli hálózati összetevők, például virtuális hálózatok és virtuális hálózatok között, VPN-kapcsolat vagy hálózati kapcsolat Private Link beállítása és fenntartása.

<li>Kérjen le és konfigurálja az adott szolgáltatásokhoz szükséges további szolgáltatás-terheléselosztásokat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Fürt verziója
   </td>
   <td>
<ul>

<li>Az alverziók és karbantartási verziók frissítésének ütemezése és állapota

<li>Változásnaplók és kibocsátási megjegyzések közzététele a kisebb és karbantartási frissítésekhez
</li>
</ul>
   </td>
   <td>
<ul>

<li>Fürtfrissítés kezdeményezése

<li>Ügyfélalkalmazások tesztelése al- és karbantartási verziókon a kompatibilitás biztosítása érdekében
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Kapacitáskezelés
   </td>
   <td>
<ul>

<li>Vezérlősík (fő csomópontok) kihasználtságának figyelése

<li>A szolgáltatásminőség fenntartása érdekében skálázhatja és/vagy átméretezheti a vezérlősík csomópontjainak méretét

<li>Az ügyfélerőforrások kihasználtságának figyelése, beleértve a hálózati, tárolási és számítási kapacitást. Ha az automatikus méretezési funkciók nincsenek engedélyezve, a rendszer riasztást küld az ügyfélnek a fürterőforrásokhoz (pl. új skálázható számítási csomópontok, további tárterület stb.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>A megadott OpenShift-fürtkezelő vezérlőkkel szükség szerint adhat hozzá vagy távolíthat el további munkavégző csomópontokat.

<li>Válaszadás a Fürterőforrásokkal kapcsolatos Microsoft- és Red Hat-értesítésekre.
</li>
</ul>
   </td>
  </tr>
</table>


3. táblázat Megosztott felelősségi körök a változáskezelésben


### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés 

Az identitás- és hozzáférés-kezelés minden felelősségi körre kiterjed annak biztosításához, hogy csak a megfelelő személyek férnek hozzá a fürt-, alkalmazás- és infrastruktúra-erőforrásokhoz. Ide tartoznak például a hozzáférés-vezérlési mechanizmusok biztosítása, a hitelesítés, az engedélyezés és az erőforrásokhoz való hozzáférés kezelése.


<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong>A Microsoft és a Red Hat feladatkörei</strong>
   </td>
   <td><strong>Ügyfél feladatkörei</strong>
   </td>
  </tr>
  <tr>
   <td>Naplózás </td>
   <td>
<ul>

<li>Iparági szabványokon alapuló rétegzett belső hozzáférési folyamat betartása a platform auditnaplóihoz.

<li>Natív OpenShift RBAC-képességeket biztosít.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurálja az OpenShift RBAC-t a projektekhez való hozzáférés és a projekt alkalmazásnaplói vezérléséhez.

<li>Külső vagy egyéni alkalmazásnaplózási megoldások esetén az ügyfél felelős a hozzáférés-kezelésért.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Alkalmazáshálózat
   </td>
   <td>
<ul>

<li>Natív OpenShift RBAC-képességeket biztosít.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurálja az OpenShift RBAC-t az útvonal-konfigurációhoz való hozzáférés szükség szerint való szabályozásához.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Fürthálózat
   </td>
   <td>
<ul>

<li>Natív OpenShift RBAC-képességeket biztosít.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Red Hat-fiókok Red Hat szervezeti tagságának kezelése.

<li>Red Hat-szervezet szervezeti rendszergazdáinak kezelése az OpenShift-fürtkezelőhöz való hozzáférés megadásához.

<li>Konfigurálja az OpenShift RBAC-t az útvonal-konfigurációhoz való hozzáférés szükség szerint való szabályozásához.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuális hálózat
   </td>
   <td>
<ul>

<li>Ügyfél-hozzáférés-vezérlést biztosít az OpenShift-fürtkezelőn keresztül.
</li>
</ul>
   </td>
   <td>
<ul>

<li>A nyilvános felhő összetevőihez való választható felhasználói hozzáférés kezelése az OpenShift-fürtkezelővel.
</li>
</ul>
   </td>
  </tr>
</table>


4. táblázat. Az identitás- és hozzáférés-kezelés megosztott feladatai


### <a name="security-and-regulation-compliance"></a>Biztonság és szabályozás megfelelősége 

A biztonság és a megfelelőség kiterjed minden olyan felelősségre és vezérlőre, amely biztosítja a vonatkozó törvények, szabályzatok és szabályozások betartását.


<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong>A Microsoft és a Red Hat feladatkörei</strong>
   </td>
   <td><strong>Ügyfél feladatkörei</strong>
   </td>
  </tr>
  <tr>
   <td>Naplózás </td>
   <td>
<ul>

<li>Fürtnaplók küldése a Microsoftnak és a Red Hat SIEM-nek biztonsági események elemzéséhez. A kriminalisztikai elemzés támogatása érdekében meghatározott ideig őrizze meg az auditnaplókat.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Biztonsági eseményeket keresve elemezheti az alkalmazásnaplókat. Alkalmazásnaplókat küldhet egy külső végpontnak naplózási oldalkocsi tárolókon vagy külső naplózási alkalmazásokon keresztül, ha hosszabb megőrzésre van szükség, mint amit az alapértelmezett naplózási verem kínál.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuális hálózat
   </td>
   <td>
<ul>

<li>A virtuális hálózat összetevőinek figyelése a lehetséges problémák és biztonsági fenyegetések észlelése érdekében.

<li>További nyilvános Microsoft- és Red Hat Azure-eszközök használata a további monitorozáshoz és védelemhez.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Opcionálisan konfigurált virtuális hálózati összetevők figyelése a lehetséges problémák és biztonsági fenyegetések észlelése érdekében.

<li>Konfigurálja szükség szerint a szükséges tűzfalszabályokat vagy adatközpont-védelmet.
</li>
</ul>
   </td>
  </tr>
</table>


5. táblázat Megosztott felelősségi körök a biztonság és a szabályozás megfelelőségében


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Az ügyfelek feladatkörei a Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Ügyféladatok és alkalmazások

Az ügyfél felelős az üzembe helyezett alkalmazásokért, számítási feladatokért és adatokért Azure Red Hat OpenShift. A Microsoft és a Red Hat azonban különböző eszközöket biztosít az ügyfélnek az adatok és alkalmazások platformon való kezeléséhez.


<table>
  <tr>
   <td><strong>Erőforrás</strong>
   </td>
   <td><strong>Hogyan segít a Microsoft és a Red Hat?</strong>
   </td>
   <td><strong>Ügyfél feladatkörei</strong>
   </td>
  </tr>
  <tr>
   <td>Ügyféladatok </td>
   <td>
<ul>

<li>Az adattitkosítás platformszintű szabványait az iparági biztonsági és megfelelőségi szabványoknak megfelelően tarthatja fenn. 

<li>OpenShift-összetevőket biztosít az alkalmazásadatok, például titkos kulcsok kezeléséhez.

<li>Külső adatszolgáltatásokkal (például Azure SQL) való integráció engedélyezése a fürtön és/vagy a Microsofton és a Red Hat Azure-n kívüli adatok tárolására és kezelésére.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Felelősséget kell vállalnia a platformon tárolt összes ügyféladatért, valamint arról, hogy az ügyfélalkalmazások hogyan használják és teszik elérhetővé az adatokat.

<li>Etcd titkosítás
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Ügyfélalkalmazások
   </td>
   <td>
<ul>

<li>Fürtök kiépítése telepített OpenShift-összetevőkkel, hogy az ügyfelek hozzáférnek az OpenShift és a Kubernetes API-khoz a tárolóba helyezett alkalmazások üzembe helyezéséhez és kezeléséhez.

<li>Hozzáférést biztosít az OpenShift API-khoz, amelyek segítségével az ügyfelek operátorokat állíthatnak be, hogy közösségi, külső, Microsoft- és Red Hat-szolgáltatásokat, valamint Red Hat-szolgáltatásokat adjanak a fürthöz. 

<li>Tárolási osztályokat és beépülő modulokat biztosít az állandó kötetek támogatásához az ügyfélalkalmazásokkal való használathoz.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Az ügyfelek és külső alkalmazások, adatok és azok teljes életciklusa felelősségének fenntartása.

<li>Ha egy ügyfél Operátorok vagy külső képek használatával ad hozzá Red Hat-, közösségi, külső, saját vagy egyéb szolgáltatásokat a fürthöz, az ügyfél felelős ezekért a szolgáltatásokért, valamint a megfelelő szolgáltatóval (beleértve a Red Hatot is) a problémák elhárításához.

<li>A megadott eszközökkel és szolgáltatásokkal <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">konfigurálhatja és telepítheti</a>a szolgáltatást. <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">naprakészen kell tartani;</a> <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">erőforrás-kérelmek és -korlátok beállítása;</a> <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">mérete a fürtöt úgy, hogy elegendő erőforrással rendelkezik az alkalmazások futtatásához;</a> <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">engedélyek beállítása;</a> integrálás más szolgáltatásokkal; kezelheti az ügyfél által üzembe helyezett rendszerképstreameket <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">vagy -sablonokat;</a> <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">külsőleg szolgál;</a> adatok mentése, biztonsági mentése és visszaállítása; és egyéb módon kezelheti a magas rendelkezésre állékonyságú és rugalmas számítási feladatokat.

<li>Az alkalmazások monitorozásának felelőssége a Azure Red Hat OpenShift; beleértve a metrikák gyűjtése és a riasztások létrehozásához szükséges szoftverek telepítését és üzemeltetési környezetét.
</li>
</ul>
   </td>
  </tr>
</table>


7. táblázat. Ügyfél feladatkörei az ügyféladatok, az ügyfélalkalmazások és a szolgáltatások tekintetében
