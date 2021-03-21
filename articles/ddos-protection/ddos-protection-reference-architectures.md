---
title: Azure DDoS Protection hivatkozási architektúrák
description: Ismerje meg az Azure DDoS Protection hivatkozási architektúráit.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992437"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection hivatkozási architektúrák

DDoS Protection standard a [virtuális hálózatban üzembe helyezett szolgáltatásokhoz](../virtual-network/virtual-network-for-azure-services.md)lett tervezve. Más szolgáltatások esetében az alapértelmezett DDoS Protection alapszintű szolgáltatás érvényes. A következő hivatkozási architektúrákat forgatókönyvek rendezik, és az architektúra mintázatai együtt vannak csoportosítva.

## <a name="virtual-machine-windowslinux-workloads"></a>Virtuális gépek (Windows/Linux) számítási feladatok

### <a name="application-running-on-load-balanced-vms"></a>Elosztott terhelésű virtuális gépeken futó alkalmazás

Ez a hivatkozási architektúra bevált eljárásokat mutat be több Windows rendszerű virtuális gép egy terheléselosztó mögötti méretezési csoportba való futtatásához a rendelkezésre állás és a méretezhetőség javítása érdekében. Ez az architektúra bármely állapot nélküli számítási feladathoz használható, például egy webkiszolgálóhoz.

![Elosztott terhelésű virtuális gépeken futó alkalmazás hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image-9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Egyetlen nyilvános IP-cím van, és az internetes forgalom a virtuális gépek számára a terheléselosztó segítségével történik. DDoS Protection standard engedélyezve van az Azure-beli (internetes) terheléselosztó virtuális hálózatán, amelyhez hozzá van rendelve a nyilvános IP-cím.

A terheléselosztó elosztja a bejövő internetes kérelmeket a virtuálisgép-példányok számára. A virtuálisgép-méretezési csoportok lehetővé teszik a virtuális gépek számának manuális méretezését vagy kikapcsolását, vagy automatikusan előre meghatározott szabályok alapján. Ez akkor fontos, ha az erőforrás DDoS-támadás alatt áll. További információ erről a hivatkozási architektúráról: [ebben a cikkben](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Alkalmazás Windows N szintű rendszeren

Egy N szintű architektúra számos módon implementálható. Az alábbi ábrán egy tipikus, háromrétegű webalkalmazás látható. Ez az architektúra a [elosztott terhelésű virtuális gépek a méretezhetőség és a rendelkezésre állás érdekében történő futtatását](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)ismerteti. A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![A Windows N szintű platformon futó alkalmazások hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image-10.png)

Ebben az architektúrában a DDoS Protection standard engedélyezve van a virtuális hálózaton. A virtuális hálózatban lévő összes nyilvános IP-cím DDoS-védelmet kap a 3. és a 4. rétegben. A 7. rétegbeli védelem esetében telepítse a Application Gatewayt a WAF SKU-ban. További információ erről a hivatkozási architektúráról: [ebben a cikkben](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Azok a forgatókönyvek, amelyekben egyetlen virtuális gép fut egy nyilvános IP-cím mögött, nem támogatottak.

### <a name="paas-web-application"></a>PaaS webalkalmazás

Ez a hivatkozási architektúra egy Azure App Service alkalmazás egyetlen régióban történő futtatását mutatja be. Ez az architektúra bevált eljárásokat mutat be [Azure app Service](https://azure.microsoft.com/documentation/services/app-service/) és [Azure SQL Databaset](https://azure.microsoft.com/documentation/services/sql-database/)használó webalkalmazásokhoz.
A készenléti régió feladatátvételi forgatókönyvekhez van beállítva.

![A Pásti-webalkalmazások hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image-11.png)

Az Azure Traffic Manager a bejövő kérelmeket a régiók egyik régiójában Application Gateway irányítja. A normál működés során a a kérelmeket az aktív régióban lévő Application Gatewayra irányítja. Ha a régió elérhetetlenné válik, Traffic Manager átadja a feladatátvételt a készenléti régióban lévő Application Gatewaynak.

Az internetről a webalkalmazás felé irányuló összes forgalom a [Application Gateway nyilvános IP-címére](../application-gateway/application-gateway-web-app-overview.md) van irányítva Traffic Manager használatával. Ebben az esetben az App Service (webalkalmazás) nem közvetlenül a külső felé irányul, és Application Gateway védi. 

Javasoljuk, hogy konfigurálja a Application Gateway WAF SKU-t (megakadályozza a módot) a 7. rétegbeli (HTTP/HTTPS/WebSocket) támadások elleni védelemhez. Emellett a webalkalmazások úgy vannak konfigurálva, hogy [csak a Application Gateway IP-címről érkező forgalmat fogadják el](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) .

A hivatkozási architektúrával kapcsolatos további információkért tekintse meg [ezt a cikket](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="mitigation-for-non-web-paas-services"></a>A nem webes Pásti szolgáltatásainak enyhítése

### <a name="hdinsight-on-azure"></a>HDInsight az Azure-ban

Ez a hivatkozási architektúra egy [Azure HDInsight-fürt](../hdinsight/index.yml)DDoS Protection szabványának konfigurálását mutatja be. Győződjön meg arról, hogy a HDInsight-fürt egy virtuális hálózathoz van csatolva, és hogy a DDoS Protection engedélyezve van a virtuális hálózaton.

!["HDInsight" és "speciális beállítások" ablaktábla virtuális hálózati beállításokkal](./media/ddos-best-practices/image-12.png)

![DDoS Protection engedélyezésének kiválasztása](./media/ddos-best-practices/image-13.png)

Ebben az architektúrában az internetről érkező HDInsight-fürtre irányuló forgalmat a rendszer a HDInsight Gateway Load Balancerhez társított nyilvános IP-címhez irányítja. Az átjáró terheléselosztó ezt követően közvetlenül a főcsomópontokra vagy a feldolgozó csomópontokra küldi a forgalmat. Mivel DDoS Protection a standard engedélyezve van a HDInsight virtuális hálózaton, a virtuális hálózat összes nyilvános IP-címe lekéri a DDoS Protectiont a 3. és a 4. rétegben. Ez a hivatkozási architektúra kombinálható az N szintű és a többrégiós hivatkozási architektúrával is.

További információ erről a hivatkozási architektúráról: az [Azure-HDInsight kiterjesztése azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dokumentáció használatával.


> [!NOTE]
> A nyilvános IP-címmel rendelkező virtuális hálózatok PowerApps-vagy API-kezelésének Azure App Service Environmenti nem natív módon támogatottak.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre DDoS Protection-tervet](manage-ddos-protection.md).