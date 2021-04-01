---
title: Önálló Service Fabric-fürtök áttekintése
description: A Service Fabric-fürtök Windows Serveren és Linuxon futnak, ami azt jelenti, hogy a Windows Servert vagy Linuxot futtató bárhonnan telepítheti és futtathatja Service Fabric alkalmazásokat.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683998"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Önálló fürtök Service Fabric áttekintése

A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. A fürt részét képező számítógépet vagy virtuális gépet fürtcsomópont-csomópontnak nevezzük. A fürtök több ezer csomópontra is méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, Service Fabric a csomópontok számának megnövekedésével kiegyensúlyozza a szolgáltatás partíciójának replikáit és példányait. Az alkalmazások teljes teljesítményének növelése és a memória-hozzáférés csökkentése. Ha a fürt csomópontjait nem használják hatékonyan, csökkentheti a fürt csomópontjainak számát. Service Fabric újra kiegyenlíti a partíciók replikáit és példányait a csomópontok számának csökkenésével, hogy jobban használhassa a hardvert az egyes csomópontokon.

A csomópont típusa határozza meg a fürt csomópontjainak méretét, számát és tulajdonságait. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik.

A helyszíni Service Fabric-fürt létrehozási folyamata hasonló ahhoz a folyamathoz, amellyel bármilyen felhőben létrehozhat egy fürtöt a virtuális gépek halmazával. A virtuális gépek kiépítésének kezdeti lépéseit a felhőalapú szolgáltató vagy a használt helyszíni környezet szabályozza. Ha olyan virtuális gépekkel rendelkezik, amelyeken engedélyezve van a hálózati kapcsolat, akkor a Service Fabric csomag beállításához, a fürt beállításainak szerkesztéséhez, valamint a fürt létrehozási és felügyeleti parancsfájljainak futtatásához szükséges lépéseket. Ez biztosítja, hogy a Service Fabric-fürtök működtetésének és kezelésének ismerete és tapasztalata átvihető legyen, ha úgy dönt, hogy új üzemeltetési környezeteket céloz meg.

## <a name="cluster-security"></a>Fürtbiztonság

A Service Fabric-fürt a saját erőforrása.  Az Ön feladata, hogy biztosítsa a fürtök védelmét, hogy megakadályozza a jogosulatlan felhasználók csatlakozását. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön.

> [!NOTE]
> A Windows-hitelesítés a Kerberoson alapul. Az NTLM hitelesítés típusa nem támogatott.
>
> Ha lehetséges, használjon X. 509 tanúsítványalapú hitelesítést Service Fabric-fürtökhöz.

### <a name="node-to-node-security"></a>Csomópontok közötti biztonság

A csomópontok közötti biztonság biztosítja a fürtben lévő virtuális gépek vagy számítógépek közötti kommunikációt. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében. A Service Fabric X. 509 tanúsítványokat használ a fürt biztonságossá tételéhez és az alkalmazás biztonsági funkcióinak biztosításához.  A fürt forgalmának biztonságossá tételéhez, valamint a fürt és a kiszolgáló hitelesítésének biztosításához a fürt tanúsítványa szükséges.  Önaláírt tanúsítványok is használhatók tesztelési fürtökhöz, de megbízható hitelesítésszolgáltatótól származó tanúsítványt kell használni az üzemi fürtök biztonságossá tételéhez.

A Windows rendszerű biztonság is engedélyezhető egy önálló Windows-fürthöz. Ha Windows Server 2012 R2 és Windows Active Directory operációs rendszert használ, javasoljuk, hogy a Windows-biztonságot csoportosan felügyelt szolgáltatásfiókok használatával használja. Ellenkező esetben használja a Windows-biztonságot Windows-fiókokkal.

További információkért olvassa el a [csomópontok közötti biztonsági](service-fabric-cluster-security.md#node-to-node-security) tudnivalókat.

### <a name="client-to-node-security"></a>Az ügyfél és a csomópont közötti biztonság

Az ügyfél és a csomópont közötti biztonság hitelesíti az ügyfeleket, és segít a fürtben lévő ügyfelek és egyes csomópontok közötti kommunikáció biztonságossá tételében. Ez a típusú biztonság biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön üzembe helyezett alkalmazásokhoz. Az ügyfeleket egyedileg azonosítják az X. 509 tanúsítvány biztonsági hitelesítő adataival. Tetszőleges számú választható ügyféltanúsítvány használható a rendszergazda vagy a felhasználói ügyfelek hitelesítésére a fürttel.

Az ügyféltanúsítványok mellett Azure Active Directory is konfigurálható úgy, hogy az ügyfeleket a fürttel hitelesítse.

További információért olvassa el az [ügyfél és a csomópont közötti biztonság](service-fabric-cluster-security.md#client-to-node-security)

### <a name="service-fabric-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés Service Fabric
A Service Fabric a hozzáférés-vezérlést is támogatja, hogy korlátozza a hozzáférést bizonyos fürt műveleteihez a különböző felhasználói csoportok esetében. Ez segít a fürt biztonságosabbá tételében. A fürthöz csatlakozó ügyfelek esetében két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.  

További információért olvassa el [Service Fabric szerepköralapú hozzáférés-vezérlés](service-fabric-cluster-security.md#service-fabric-role-based-access-control)című témakört.

## <a name="scaling"></a>Méretezés

Az alkalmazás iránti igények időbeli változása. Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen az alkalmazások megnövekedett munkaterhelésének vagy a hálózati forgalomnak, vagy csökkentenie kell a fürterőforrások mennyiségét. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt (a csomópontok számának módosítása) vagy függőlegesen (a csomópontok erőforrásainak módosítása). A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön. A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

További információért olvassa el az [önálló fürtök méretezését](service-fabric-cluster-scaling-standalone.md)ismertető témakört.

## <a name="upgrading"></a>Frissítés

Az önálló fürt olyan erőforrás, amely teljesen saját maga. A mögöttes operációs rendszer javítása és a háló frissítéseinek kezdeményezése felelős. Beállíthatja, hogy a fürt automatikusan megkapja az automatikus futtatókörnyezet-frissítéseket, amikor a Microsoft új verziót szabadít fel, vagy egy támogatott futásidejű verziót szeretne kiválasztani. A háló frissítésein kívül az operációs rendszert és a fürt konfigurációját is frissítheti, például a tanúsítványokat vagy az alkalmazás portját. 

További információért olvassa el az [önálló fürtök frissítését](service-fabric-cluster-upgrade-standalone.md)ismertető témakört.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Létrehozhat fürtöket a virtuális gépeken vagy az ezeket az operációs rendszereket futtató számítógépeken (a Linux még nem támogatott):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Következő lépések

További információ az önálló fürtök [biztonságossá](service-fabric-cluster-security.md)tételéről, [méretezéséről](service-fabric-cluster-scaling-standalone.md)és [frissítéséről](service-fabric-cluster-upgrade-standalone.md) .

További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).
