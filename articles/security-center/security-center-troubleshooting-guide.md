---
title: Azure Security Center – Hibaelhárítási útmutató | Microsoft Docs
description: Ez az útmutató olyan informatikai szakembereknek, biztonsági elemzőknek és felhőalapú rendszergazdáknak szól, akiknek szükségük van a Azure Security Center kapcsolatos problémák elhárítására.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: e49d8bcc55036b21f53720aab17be02edb114d4b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100253"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center – Hibaelhárítási útmutató

Ez az útmutató olyan informatikai (IT) szakemberek, információbiztonsági elemzők és felhőrendszergazdák számára készült, akik szervezetei az Azure Security Centert használják, és el kell hárítaniuk a használathoz kapcsolódó problémákat.

Security Center a Log Analytics ügynök használatával gyűjti és tárolja az adatokat. További információk: [Az Azure Security Center Platform migrálása](./security-center-enable-data-collection.md). A jelen cikkben található információk a Log Analytics-ügynökre való áttérés után Security Center funkciót jelentenek.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató a Security Center használatához kapcsolódó problémák hibaelhárítását mutatja be.

Riasztási típusok:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* SQL Database és az Azure szinapszis Analytics elemzése
* Környezeti információk

A riasztástípusoktól függően az ügyfelek a riasztás vizsgálatához szükséges információkat az alábbi erőforrások használatával gyűjthetik össze:

* A virtuális gép (VM) eseménymegtekintőjében elérhető biztonsági naplók Windows esetén
* Az AuditD Linux esetén
* Az Azure-tevékenységnaplók és a diagnosztikai naplók engedélyezése a megtámadott erőforráson.

Az ügyfelek visszajelzéseket adhatnak a riasztás leírásával és relevanciájával kapcsolatban. Lépjen a riasztásra, kattintson a **Hasznos volt ez az információ?** gombra, adja meg az okot, és írja be a megjegyzését a visszajelzés indoklására. A riasztások javítása érdekében folyamatosan figyeljük ezeket a visszajelzéseket.

## <a name="audit-log"></a>Az auditnaplóban

A Security Center hibaelhárítása többnyire a meghibásodott összetevőhöz tartozó [auditnapló](../azure-monitor/essentials/platform-logs-overview.md) bejegyzéseinek áttekintésével kezdődik. A naplókból a következők állapíthatók meg:

* A végrehajtott műveletek
* A művelet kezdeményezője
* A művelet végrehajtásának időpontja
* A művelet állapota
* A művelet felderítése során hasznosítható egyéb tulajdonságok értékei

A napló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE), nem tartalmazza azonban az olvasási műveleteket (GET).

## <a name="log-analytics-agent"></a>Log Analytics-ügynök

A Security Center a Log Analytics-ügynököt használja – ez ugyanaz az ügynök, amelyet az Azure Monitor Service használ – az Azure-beli virtuális gépekről származó biztonsági adatok gyűjtésére. Ha az adatgyűjtés engedélyezve van, és az ügynök megfelelően van telepítve a célgépen, elkezdődik az alábbi folyamat végrehajtása:

* HealthService.exe

Ha megnyitja a szolgáltatások kezelése konzolt (Services. msc), akkor a Log Analytics ügynök szolgáltatás fut, ahogy az alábbi ábrán is látható:

![Szolgáltatások](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Az ügynök adott verziójának megtekintéséhez nyissa meg a **Feladatkezelő eszközt** a **folyamatok** lapon, keresse meg a **log Analytics ügynök szolgáltatást**, kattintson rá a jobb gombbal, majd kattintson a **Tulajdonságok** parancsra. A **Részletek** lapon keresse meg a fájlverziót az alábbi módon:

![Fájl](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Log Analytics ügynök telepítési forgatókönyvei

A Log Analytics ügynöknek a számítógépre való telepítésekor két telepítési forgatókönyvnek lehet különböző eredményei. A támogatott forgatókönyvek:

* **A Security Center automatikusan telepítette az ügynököt**: ebben a forgatókönyvben a Security Centerben és a naplóbeli kereséssel egyaránt megtekintheti a riasztásokat. E-mail-értesítést fog kapni az előfizetés biztonsági szabályzatában konfigurált e-mail-címre, amelyhez az erőforrás tartozik.

* Az **ügynök manuális telepítése az Azure-ban található virtuális gépen**: ebben a forgatókönyvben, ha az ügynököket az 2017 február előtt manuálisan letöltötte és telepítette, akkor a riasztásokat csak akkor tekintheti meg a Security Center portálon, ha a munkaterülethez tartozó előfizetésre szűr. Ha az erőforráshoz tartozó előfizetésre szűr, nem jelenik meg riasztás. E-mail-értesítést fog kapni az előfizetés biztonsági szabályzatában konfigurált e-mail-címre, amelyhez a munkaterület tartozik.

> [!NOTE]
> A második forgatókönyvben ismertetett viselkedés elkerülése érdekében figyeljen arra, hogy az ügynök legújabb verzióját töltse le.

## <a name="monitoring-agent-health-issues"></a>A Monitoring Agent állapotproblémái <a name="mon-agent"></a>

A **Figyelés állapota** megmutatja, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre beállított virtuális gépeket és számítógépeket. Az alábbi táblázatban a **Figyelés állapota** értékei, leírásai és a megoldás lépései láthatók.

| Figyelés állapota | Leírás | A megoldás lépései |
|---|---|---|
| Ügynök függőben lévő telepítése | A Log Analytics ügynök telepítése még fut.  A telepítés akár néhány órát is igénybe vehet. | Várjon, amíg az automatikus telepítés befejeződik. |
| Energiaállapot: ki | A virtuális gép leállt.  A Log Analytics ügynök csak a rendszert futtató virtuális gépekre telepíthető. | Indítsa újra a virtuális gépet. |
| Hiányzó vagy érvénytelen azure-os virtuálisgép-ügynök | A Log Analytics ügynök még nincs telepítve.  Ahhoz, hogy a Security Center telepítse a bővítményt, érvényes azure-os virtuálisgép-ügynök szükséges. | Telepítse, telepítse újra vagy frissítse a virtuális gépen található azure-os virtuálisgép-ügynököt. |
| A virtuális gép állapota nem áll készen a telepítésre  | A Log Analytics ügynök még nincs telepítve, mert a virtuális gép nem áll készen a telepítésre. A virtuális gép a virtuálisgép-ügynökkel vagy a virtuális gép üzembe helyezésével kapcsolatos probléma miatt nem áll készen a telepítésre. | Ellenőrizze a virtuális gép állapotát. Térjen vissza a **Virtuális gépek** szakaszra a portálon, és jelölje ki a virtuális gépet az állapotra vonatkozó információk megtekintéséhez. |
|A telepítés nem sikerült – általános hiba | A Log Analytics ügynök telepítve van, de hiba miatt nem sikerült. | [Telepítse manuálisan a bővítményt](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) vagy távolítsa el, és a Security Center megpróbálja újból telepíteni. |
| A telepítés nem sikerült – a helyi ügynök már telepítve van | Log Analytics ügynök telepítése nem sikerült. Security Center azonosított egy helyi ügynököt (Log Analytics vagy System Center Operations Manager), amely már telepítve van a virtuális gépen. Ha el szeretné kerülni a többsoros konfigurációt, ahol a virtuális gép két különálló munkaterületre jelent jelentést, a Log Analytics ügynök telepítése leállt. | Két megoldás létezik: az egyik [a bővítmény manuális telepítése](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) és csatlakoztatása a kívánt munkaterülethez. A másik a kívánt munkaterület alapértelmezettként való beállítása, és az ügynök automatikus üzembe helyezésének engedélyezése.  Lásd az [automatikus üzembe helyezés engedélyezését](security-center-enable-data-collection.md) ismertető részt. |
| Az ügynök nem tud csatlakozni a munkaterülethez | A Log Analytics ügynök telepítve van, de a hálózati kapcsolat miatt nem sikerült.  Ellenőrizze az internetkapcsolatot, és hogy érvényes HTTP proxy van-e konfigurálva az ügynökhöz. | Lásd a Monitoring Agent hálózati követelményeit ismertető részt. |
| Az ügynök hiányzó vagy ismeretlen munkaterülethez van csatlakoztatva | Security Center azonosította, hogy a virtuális gépre telepített Log Analytics-ügynök egy olyan munkaterülethez csatlakozik, amelyhez nincs hozzáférése. | Ez két esetben fordulhat elő. A munkaterületet törölték, és már nem létezik. Telepítse újra az ügynököt a megfelelő munkaterülettel, vagy távolítsa el az ügynököt, és engedélyezze a Security Centernek az automatikus üzembe helyezési telepítés végrehajtását. A másik eset, amikor a munkaterület egy olyan előfizetés része, amelyhez a Security Center nem rendelkezik engedéllyel. A Security Center működéséhez az előfizetéseknek engedélyezniük kell a hozzáférést a Microsoft Security erőforrás-szolgáltató számára. Az engedélyezéshez regisztrálja az előfizetést a Microsoft Security erőforrás-szolgáltatóban. Ezt megteheti egy API, a PowerShell vagy a portál segítségével, vagy a Security Center **Áttekintés** irányítópultján az előfizetésre történő szűréssel. További információ: [Erőforrás-szolgáltatók és típusaik](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Az ügynök nem válaszol, vagy hiányzik az azonosító | A Security Center annak ellenére sem tudja lekérni a virtuális gépről beolvasott biztonsági adatokat, hogy az ügynök telepítve van. | Az ügynök nem jelent semmilyen adatot, például szívverést sem. Előfordulhat, hogy az ügynök sérült, vagy valami blokkolja a forgalmat. Vagy az ügynök jelentéskészítési adatforrást tartalmaz, de hiányzik az Azure-erőforrás azonosítója, így nem lehet az Azure-beli virtuális géphez igazodni. A Linux hibaelhárításával kapcsolatban lásd: a [Linux rendszerhez készült log Analytics-ügynök hibaelhárítási útmutatója](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Windows hibaelhárítása: [Windows rendszerű virtuális gépek hibaelhárítása](../virtual-machines/extensions/oms-windows.md#troubleshoot-and-support). |
| Az ügynök nincs telepítve | Az adatgyűjtés le van tiltva. | Kapcsolja be az adatgyűjtést a biztonsági házirendben, vagy telepítse manuálisan a Log Analytics-ügynököt. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>A figyelési ügynök hálózati követelményeinek hibaelhárítása <a name="mon-network-req"></a>

Ahhoz, hogy az ügynökök kapcsolódni és regisztrálni tudjanak a Security Centerben, hozzáféréssel kell rendelkezniük a hálózati erőforrásokhoz, beleértve a portszámokat és a tartományok URL-címét.

* Proxykiszolgálók esetében biztosítania kell, hogy a megfelelő proxykiszolgáló-erőforrások konfigurálva vannak az ügynök beállításaiban. További információ: [a proxybeállítások módosítása](../azure-monitor/agents/agent-windows.md).
* Ha tűzfallal korlátozza az internet-hozzáférést, akkor a tűzfalat úgy kell beállítania, hogy engedélyezze a Log Analytics hozzáférését. Az ügynök beállításait nem kell módosítania.

Az alábbi táblázat a kommunikációhoz szükséges erőforrásokat tartalmazza.

| Ügynök erőforrása | Portok | HTTPS-ellenőrzés kihagyása |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Igen |
| *.oms.opinsights.azure.com | 443 | Igen |
| *.blob.core.windows.net | 443 | Igen |
| *.azure-automation.net | 443 | Igen |

Ha problémába ütközik az ügynök előkészítése során, olvassa el a következő cikket: [Az Operations Management Suite előkészítési problémáinak hibaelhárítása](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Az Endpoint Protection hibaelhárítása nem működik megfelelően

A vendégügynök a [Microsoft Antimalware](../security/fundamentals/antimalware.md) bővítmény minden műveletének szülőfolyamata. Ha a vendégügynök-folyamat meghibásodik, az annak gyermekfolyamataként futó Microsoft Antimalware is meghibásodhat.  Ilyen esetekben a következők ellenőrzése javasolt:

* A cél virtuális gép egyéni rendszerkép-e, és a virtuális gép létrehozója nem telepítette-e a vendégügynököt.
* Ha a cél nem egy Windows-, hanem egy Linux-alapú virtuális gép, a kártevőírtó bővítmény Windows-verziójának telepítése egy Linux-alapú virtuális gépre sikertelen lesz. A Linux-vendégügynöknek meg kell felelnie az operációs rendszer verziójára és a szükséges csomagokra vonatkozó követelményeknek, és ha ezek a követelmények nem teljesülnek, a virtuálisgép-ügynök sem fog működni.
* A virtuális gépet nem a vendégügynök egy régebbi verziójával hozták-e létre. Ha azzal hozták létre, vegye figyelembe, hogy néhány régebbi ügynök nem frissül automatikusan az újabb verzióra, és ez ehhez a problémához vezethet. Mindig a vendégügynök legfrissebb verzióját használja saját rendszerképek létrehozásához.
* Néhány külső felügyeleti szoftver letilthatja a vendégügynököt, vagy blokkolhatja bizonyos fájlhelyek elérését. Ha külső szoftvert telepített a virtuális gépre, ellenőrizze, hogy az ügynök szerepel-e a kizárási listán.
* Bizonyos tűzfalbeállítások vagy hálózati biztonsági csoportok (NSG) blokkolhatják a vendégügynöktől érkező, illetve az ügynök felé irányuló hálózati forgalmat.
* Bizonyos hozzáférés-vezérlési listák (ACL) megakadályozhatják a lemezhez való hozzáférést.
* Ha nincs elég hely a lemezen, az akadályozhatja a vendégügynök megfelelő működését.

Alapértelmezés szerint a Microsoft Antimalware felhasználói felülete le van tiltva. További információkat az engedélyezéséről [a Microsoft Antimalware felhasználói felületének az Azure Resource Manager-alapú virtuális gépeken üzembe helyezés utáni engedélyezésével](/archive/blogs/azuresecurity/enabling-microsoft-antimalware-user-interface-post-deployment) kapcsolatos cikkben olvashat.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Az irányítópult betöltési hibáinak elhárítása

Ha problémákat tapasztal a Security Center irányítópultjának betöltése során, bizonyosodjon meg róla, hogy az előfizetést a Security Centerre regisztráló felhasználó (azaz az első felhasználó, aki megnyitotta a Security Centert az előfizetéssel) és az adatgyűjtést bekapcsolni kívánó felhasználó *Tulajdonos* vagy *Közreműködő* az előfizetésen. Ettől a pillanattól az előfizetés *Olvasó* jogú felhasználói is látják az irányítópultot/riasztásokat/ajánlásokat/házirendeket.

## <a name="contacting-microsoft-support"></a>Kapcsolatfelvétel a Microsoft támogatási szolgálatával

Bizonyos problémák a jelen cikkben ismertetett irányelvek alapján azonosíthatók, másokat a Security Center nyilvános [Microsoft Q&oldalon](/answers/topics/azure-security-center.html)találhat. Ha azonban további hibaelhárításra van szüksége, az alábbi ábrán látható módon nyithat meg egy új támogatási kérést **Azure Portal** használatával:

![Microsoft támogatási szolgálat](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Lásd még

Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md) – megtudhatja, hogyan tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához
* [Az Azure Security Center biztonsági riasztásainak megismerése](./security-center-alerts-overview.md)
* [Oktatóanyag: Reagálás a biztonsági incidensekre](tutorial-security-incident.md)
* [Riasztások érvényesítése az Azure Security Centerben](security-center-alert-validation.md)
* [E-mail-értesítések az Azure Security Centerben](security-center-provide-security-contact-details.md)
* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](./security-center-alerts-overview.md)
* [Partneri megoldások monitorozása Azure Security Centerokkal](./security-center-partner-integration.md) – megtudhatja, hogyan figyelheti a partneri megoldások állapotát.
* [Azure Security Center GYIK](faq-general.md) – gyakori kérdések a szolgáltatás használatával kapcsolatban
* [Azure Security blog](/archive/blogs/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.