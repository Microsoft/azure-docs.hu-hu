---
title: Bizalmas tárolók az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg a nem módosított tárolók támogatását a bizalmas tárolók esetében.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: a5db93f096c73679c1b6b6ae464897db843f2e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706265"
---
# <a name="confidential-containers"></a>Bizalmas tárolók

## <a name="overview"></a>Áttekintés

Lehetővé teheti a fejlesztők számára, hogy egy **meglévő Docker-alkalmazást (új vagy meglévő)** és biztonságosan futtassanak az Azure Kubernetes szolgáltatásban (ak) a bizalmas számítástechnikai csomópontok támogatásával.

A bizalmas tárolók segítenek a védelemben:

- adatok integritása 
- adatok titkossága
- kód integritása
- tároló-programkód védelme titkosítással
- hardver alapú garanciák
- meglévő alkalmazások futtatásának engedélyezése
- a megbízható hardver gyökerének létrehozása
- gazdagép-rendszergazda eltávolítása, Kubernetes-rendszergazda, hypervisor a megbízhatósági kapcsolat határán

A hardveres alapú megbízható végrehajtási környezet (TEE) egy fontos összetevő, amely erős biztosítékok biztosítására szolgál a megbízható számítástechnikai bázis (TCB) összetevőinek hardveres és szoftveres mérései révén. Ezeknek a méréseknek a ellenőrzése a várt számítás érvényesítését és a tároló alkalmazások illetéktelen módosításának ellenőrzését segíti.

A bizalmas tárolók támogatják a **Python, a Java, a Node js stb.** által fejlesztett egyéni alkalmazásokat, például az NGINX, a Redis cache, a Memcached és így tovább, a bizalmas számítástechnikai csomópontokkal való módosítást az AK-ban.

A bizalmas tárolók jelentik a tároló titkosságának leggyorsabb elérési útját, és csak a meglévő Docker-tároló alkalmazások újracsomagolását igénylik, és nem igénylik az alkalmazás kódjának módosítását. A bizalmas tárolók olyan Docker-tároló alkalmazások, amelyek egy PÓLÓn való futtatásra vannak csomagolva. A bizalmas tárolók egyes adottságai olyan tároló-titkosítást is biztosítanak, amely a tároló és a szállítás során, valamint a gazdagépen való csatlakoztatás során segít a tároló kódjának védelmében. A tárolók titkosítása lehetővé teszi, hogy tovább folytassa a tárolóban csomagolt kód/modell, valamint a TEE-hez csatolt visszafejtési kulcs védelemmel.

Az alábbi eljárás a bizalmas tárolók fejlesztésből való üzembe helyezésének folyamata ![ a bizalmas tárolók feldolgozásához.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Bizalmas tárolók segítői
Ahhoz, hogy egy már meglévő Docker-tárolót futtasson, egy SGX ENKLÁVÉHOZ szoftverre van szükség, hogy az alkalmazás meghívja az elérhetővé tett speciális CPU-utasításokat, hogy csökkentse a csatolás felületét, és ne használja a vendég operációs rendszer függőségét. Miután becsomagolta az SGX ENKLÁVÉHOZ Runtime szoftverét, a tárolók automatikusan elindul a védett enklávékban, így eltávolítja a vendég operációs rendszert, a gazdagép operációs rendszerét vagy a Hypervisort a megbízhatósági kapcsolat határáról. Ez az elkülönített végrehajtás egy olyan csomópontban (virtuális gépen), amely a hardveres adattitkosítással támogatott a memóriában, csökkenti a felszíni támadási területeket, és csökkenti a biztonsági réseket az operációs rendszer vagy a hypervisor rétegek használatával.

A bizalmas tárolók teljes mértékben támogatottak az AK-ban, és az Azure-partnerek és a Open-Source szoftverek (OSS) projektjein keresztül engedélyezve vannak. A fejlesztők a szolgáltatások, az Azure-szolgáltatásokhoz való integráció és az eszközök támogatása alapján választhatják ki a szoftvergyártókat.

## <a name="partner-enablers"></a>Partneri segítők
> [!NOTE]
> Az alábbi megoldások Azure-partnereken keresztül érhetők el, és licencelési díjakat is igényelhetnek. Egymástól függetlenül ellenőrizze a partneri szoftverek feltételeit. 

### <a name="anjuna"></a>Anjuna

A [Anjuna](https://www.anjuna.io/) olyan SGX enklávéhoz platformot biztosít, amely lehetővé teszi a nem módosított tárolók futtatását az AK-on. Tudjon meg többet a funkcionalitásról, és tekintse meg a minta alkalmazásokat [itt](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Ismerkedjen meg a minta Redis Cache és a Python egyéni [alkalmazással](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Anjuna folyamat](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

A [Fortanix](https://www.fortanix.com/) lehetővé teszi a fejlesztők számára, hogy egy portált és CLI-alapú felületet hozzanak elérhetővé a tároló alkalmazások létrehozásához, és a SGX enklávéhoz képes bizalmas tárolókat az alkalmazás módosításának vagy újrafordításának szükségessége nélkül. A Fortanix rugalmasságot biztosít az alkalmazások széles körének futtatásához és kezeléséhez, beleértve a meglévő alkalmazásokat, az új enklávé-natív alkalmazásokat és az előre csomagolt alkalmazásokat. A felhasználók a [bizalmas számítástechnikai kezelő](https://em.fortanix.com/) felhasználói felületén vagy a [REST API](https://www.fortanix.com/api/em/) -kon keresztül hozhatnak létre bizalmas tárolókat az Azure Kubernetes szolgáltatás [gyorskonfigurálás](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) útmutatóját követve.

![Fortanix üzembe helyezési folyamata](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

A [Scone](https://scontain.com/index.html?lang=en) olyan biztonsági házirendeket támogat, amelyek tanúsítványokat, kulcsokat és titkokat generálnak, és gondoskodnak arról, hogy csak az alkalmazás tanúsított szolgáltatásai legyenek láthatók. Így az alkalmazások szolgáltatásai automatikusan hitelesítik egymást a TLS-n keresztül, anélkül, hogy módosítani kellene az alkalmazásokat és a TLS-t. Ezt egy egyszerű lombik-alkalmazás súgója ismerteti itt: https://sconedocs.github.io/flask_demo/  

A SCONE a meglévő legtöbb bináris alkalmazást a enklávékban futó alkalmazásokba alakíthatja anélkül, hogy módosítania kellene az alkalmazást, vagy újra le kellene fordítani az adott alkalmazást. A SCONE az adatfájlok és a Python-programkódok **titkosításával** is védi az értelmezett nyelveket, például a Pythont. A SCONE biztonsági szabályzatának segítségével az egyik képes megvédeni a titkosított fájlokat a jogosulatlan hozzáférések, a módosítások és a visszaállítások ellen. "Sconify" – egy meglévő Python-alkalmazás magyarázata [itt](https://sconedocs.github.io/sconify_image/)

![Scontain folyamat](./media/confidential-containers/scone-workflow.png)

A bizalmas számítástechnikai csomópontokon Scone üzemelő példányok teljes mértékben támogatottak, és integrálva vannak más Azure-szolgáltatásokkal. Ismerkedjen meg egy minta alkalmazással https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS-segítők 
> [!NOTE]
> Az alábbi megoldások Open-Source projektek keretében érhetők el, és nem kapcsolódnak közvetlenül az Azure bizalmas számítástechnika (ACC) vagy a Microsoft szolgáltatáshoz.  

### <a name="graphene"></a>Grafén

A [grafén](https://grapheneproject.io/) egy egyszerű vendég operációs rendszer, amelynek célja, hogy egyetlen linuxos alkalmazást futtasson minimális gazdagép-követelményekkel. A grafén elkülönített környezetekben futtathatják az alkalmazásokat, amelyek a teljes operációs rendszer futtatásához hasonló előnyökkel rendelkeznek, és jó eszköz-támogatást biztosítanak a meglévő Docker-tároló alkalmazás grafén védett tárolók (Főtitkárság) számára történő átalakításához.

Ismerkedjen meg egy minta alkalmazással és üzembe helyezéssel [az AK-](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) ban

### <a name="occlum"></a>Occlum
A [Occlum](https://occlum.io/) egy memória-biztonságos, többfolyamatos függvénytár operációs rendszer (LibOS) az Intel SGX enklávéhoz. Lehetővé teszi, hogy az örökölt alkalmazások SGX ENKLÁVÉHOZ fussanak, és a forráskód nem módosítható. A Occlum transzparens módon védi a felhasználói munkaterhelések titkosságát, és lehetővé teszi a meglévő Docker-alkalmazások egyszerű emelését és átváltását.

A Occlum támogatja az AK-alapú központi telepítéseket. Kövesse a telepítési utasításokat a különböző példákkal [rendelkező alkalmazásokkal](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Bizalmas tárolók bemutatója
Tekintse meg a bizalmas egészségügyi bemutatót bizalmas tárolókkal. A minta [itt](/azure/architecture/example-scenario/confidential/healthcare-inference)érhető el. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Segítség kérése

Kérdése van a megvalósítással kapcsolatban, vagy szeretne lenni az Ön számára? E-mail küldése a termék csapatának **acconaks@microsoft.com**

## <a name="reference-links"></a>Hivatkozásokra mutató hivatkozások

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)