---
title: Az Oracle WebLogic Server Azure Kubernetes szolgáltatásban való futtatásának megoldásai
description: Ismerje meg, hogyan futtathatja az Oracle WebLogic Servert az Azure Kubernetes szolgáltatásban.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669025"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Milyen megoldások futnak az Oracle WebLogic Server Azure Kubernetes Service-ben való futtatásához?

Ez az oldal az Oracle WebLogic Server (WLS) Azure Kubernetes szolgáltatásban (ak) való futtatásának megoldásait ismerteti. Ezeket a megoldásokat az Oracle és a Microsoft közösen fejleszti és támogatja.

A WebLogic-kiszolgálót az Azure Virtual Machines is futtathatja. Ennek a megoldásnak a leírását [ebben a Microsoft-cikkben](./oracle-weblogic.md)találja.

A WebLogic Server egy vezető Java-alkalmazáskiszolgáló, amely a legtöbb kritikus vállalati Java-alkalmazást futtatja szerte a világon. A WebLogic-kiszolgáló az Oracle Software Suite-hoz készült middleware Foundation-t képezi. Az Oracle és a Microsoft elkötelezte magát arra, hogy a WebLogic-kiszolgáló ügyfeleinek az Azure-ban vezető felhőalapú platformként futtatott számítási feladatokat és rugalmasságot biztosítanak.

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AK Certified és supported
A WebLogic-kiszolgálót az Oracle és a Microsoft is tanúsítja, hogy jól fusson az AK-on. Az AK-alapú megoldások WebLogic-kiszolgálója úgy van kialakítva, hogy a lehető legkönnyebben fusson a tárolók és a felkészített Java-alkalmazások a Docker-és a Kubernetes-infrastruktúrában. A megoldások a megbízhatóságra, a méretezhetőségre, a kezelhetőségre és a nagyvállalati támogatásra összpontosítanak.

A WebLogic-kiszolgáló fürtök teljes mértékben engedélyezve vannak, hogy a WebLogic Kubernetes operátoron keresztül fussanak a Kubernetes-on (a továbbiakban az "operátor" Innentől kezdve). Az operátor a standard Kubernetes operátori mintát követi. Leegyszerűsíti a WebLogic-tartományok és-telepítések felügyeletét és működését a Kubernetes-ben, ha automatizálja a manuális feladatokat, és további működési megbízhatósági funkciókat ad hozzá. A kezelő támogatja az Oracle WebLogic Server 12c, az Oracle Fusion middleware infrastruktúra 12c és azon kívül. A WebLogic Server 12.2.1.3 és a 12.2.1.4 hivatalos Docker-rendszerképeit teszteltük a kezelővel. Az operátorral kapcsolatos részletekért tekintse meg az [Oracle hivatalos dokumentációját](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Útmutatás, parancsfájlok és minták az WLS-on AK-on
A WebLogic-kiszolgáló az AK-on, az Oracle és a Microsoft közösen is biztosít részletes útmutatást, parancsfájlokat és mintákat a WebLogic-kiszolgáló AK-on való futtatásához. Az útmutató az [operátor dokumentációjának](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)Azure Kubernetes Service Sample szakaszába került. Az útmutató célja, hogy a lehető legegyszerűbbé váljon a termelési WebLogic-kiszolgáló használata az AK-beli központi telepítésekben. Az útmutató az Oracle által biztosított hivatalos WebLogic Server Docker-rendszerképeket használja. A feladatátvétel a Kubernetes állandó mennyiségi jogcímek által elért Azure Files keresztül érhető el. Az Azure Load Balancer a "terheléselosztó" típusú Kubernetes szolgáltatással való kiépítés esetén támogatott. A Azure Container Registry (ACR) a WLS-tartományok egyéni Docker-rendszerképeken belüli üzembe helyezése esetén támogatott. Az útmutató nagy fokú konfigurációt és testreszabást tesz lehetővé.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="A WebLogic-kiszolgáló az AK-on való üzembe helyezéséhez használhatja a minta-parancsfájlokat.":::

A megoldások két módon helyezhetik üzembe a WLS-tartományokat az AK-ban. A tartományok közvetlenül is üzembe helyezhetők az állandó kötetek Kubernetes. Ez az üzembe helyezési lehetőség akkor jó, ha AK-ra szeretne migrálni, de továbbra is szeretné felügyelni a WLS a felügyeleti konzol vagy a WebLogic Scripting Tool (WLST) használatával. A beállítás azt is lehetővé teszi, hogy a Docker-fejlesztés bevezetése nélkül váltson AK-ra. A WLS tartományok Kubernetes való üzembe helyezésének több natív módja, ha az Oracle Container Registry hivatalos WLS rendszerképein alapuló egyéni Docker-rendszerképeket hoz létre, közzéteheti az egyéni rendszerképeket az ACR-ben, és a tartományt a kezelő használatával helyezheti el az AK-ba. A megoldásban ez a lehetőség azt is lehetővé teszi, hogy a Kubernetes ConfigMaps keresztül frissítse a tartományt a telepítés befejezése után.

A jövőben további egyszerű használat és Azure-szolgáltatások integrációja is lehetséges a piactéren, az Oracle WebLogic Server Azure Virtual Machines-megoldásokkal való tükrözése révén.

_Ezek a megoldások saját licenccel rendelkeznek_. Feltételezik, hogy már megkapta a megfelelő licenceket az Oracle-vel, és megfelelő licenccel rendelkezik az ajánlatok Azure-beli futtatásához.

_Ha szeretné, hogy az áttelepítési forgatókönyvek szorosan összhangban legyenek a megoldások fejlesztéséhez használt mérnöki csapattal, töltse ki [ezt a rövid kérdőívet](https://aka.ms/wls-on-azure-survey) , és adja meg a kapcsolattartási adatait_. A program-menedzserek, az építészek és a mérnökök hamarosan megközelítik Önt, és megkezdik a szoros együttműködést. Az áttelepítési forgatókönyvben való együttműködés lehetősége ingyenes, amíg a megoldások aktív kezdeti fejlesztés alatt állnak.

## <a name="deployment-architectures"></a>Üzembe helyezési architektúrák

Az Oracle WebLogic Server Azure Kubernetes szolgáltatásban való futtatására szolgáló megoldások széles körben lehetővé teszik az éles környezetben felépíthető üzembe helyezési architektúrák viszonylag könnyen elérhetővé tételét.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Az összetett WebLogic-kiszolgálók üzembe helyezése engedélyezve van az AK-ban":::

A megoldások az ügyfelek számára teljes rugalmasságot biztosítanak az üzemelő példányok testreszabásához. Valószínű, hogy az alkalmazások üzembe helyezése során az ügyfelek a további Azure-erőforrásokat is integrálják az üzemelő példányokkal. Javasoljuk, hogy visszajelzéseket nyújtson a felmérésben a megoldások továbbfejlesztése érdekében.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Oracle WebLogic Server futtatását az Azure Kubernetes szolgáltatásban.

> [!div class="nextstepaction"]
> [Útmutató, parancsfájlok és minták a WLS az AK-on való futtatásához](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes operátor](https://oracle.github.io/weblogic-kubernetes-operator/)
