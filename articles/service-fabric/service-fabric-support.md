---
title: További tudnivalók az Azure Service Fabric támogatási lehetőségeiről
description: Az Azure Service Fabric-fürt támogatott verziói és a fájlokra vonatkozó támogatási jegyekre mutató hivatkozások
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316499"
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségei

Számos támogatási kérési lehetőséget hoztunk létre a Service Fabric-fürtök és az alkalmazások számítási feladatainak kezeléséhez. A szükséges támogatás sürgőssége és a probléma súlyossága alapján kiválaszthatja az Önnek legmegfelelőbb lehetőséget.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Termelési problémák jelentése vagy az Azure-ra vonatkozó fizetett támogatás kérése

Az Azure-on futó Service Fabric-fürttel kapcsolatos problémák jelentéséhez nyisson meg egy támogatási jegyet [a Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy a [Microsoft támogatási portálján](https://support.microsoft.com/oas/default.aspx?prid=16146).

További információk:
 
- A [Microsoft Azure-támogatásának támogatása](https://azure.microsoft.com/support/plans/?b=16.44).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

> [!Note]
> A bronz megbízhatósági szinten vagy egyetlen csomóponttal rendelkező fürtön futó fürtök csak a tesztelési feladatok futtatását teszik lehetővé. Ha a bronz vagy egy csomópontos fürtön futó fürttel kapcsolatos problémákat tapasztal, a Microsoft támogatási csapata segítséget nyújt a probléma megoldásában, de nem hajt végre kiváltó okokat. További információkért tekintse meg a [fürt megbízhatósági jellemzőit](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Ha további információt szeretne arról, hogy mi szükséges az éles használatra kész fürthöz, tekintse meg az [üzemi készültségi ellenőrzőlistát](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Termelési problémák jelentése vagy fizetett támogatás kérése önálló Service Fabric-fürtökhöz

A helyszíni vagy más felhőkben futó Service Fabric-fürtökkel kapcsolatos problémák jelentéséhez a [Microsoft támogatási portálján](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)megnyithatja a szakmai támogatásra vonatkozó jegyet.

További információk:

- [A Microsoft szakmai támogatása a helyszínen](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric-problémák jelentése

Egy GitHub-tárházat állítottunk be a Service Fabric problémák jelentéséhez.  A következő fórumokon is aktívan figyelik.

### <a name="github-repo"></a>GitHub-tárház 

Azure Service Fabric-problémák jelentése a [Service Fabric githubon](https://github.com/microsoft/service-fabric/issues). Ez a tárház a problémák jelentésére és nyomon követésére, valamint az Azure-Service Fabricokkal kapcsolatos kis funkciókra vonatkozó kérések készítésére szolgál. Ne **használja ezt a médiumot az élő helyekkel kapcsolatos problémák jelentésére**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow és MSDN-fórumok

A StackOverflow és az [MSDN Service Fabric forum][msdn-forum] [Service Fabric címkéje][stackoverflow] a legjobb megoldás, ha általános kérdéseket szeretne feltenni a platform működéséről, valamint arról, hogyan használhatja azt bizonyos feladatok végrehajtásához.

### <a name="azure-feedback-forum"></a>Azure visszajelzési fórum

Az [Service Fabric Azure visszajelzési fóruma][uservoice-forum] a legjobb hely a termékkel kapcsolatos fontos ötletek elküldéséhez. Áttekintjük a legnépszerűbb kéréseket, és a közepes és hosszú távú tervezéshez is felszámítjuk őket. Javasoljuk, hogy a Közösségen belüli javaslataihoz támogassa a támogatását.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>A Service Fabric előzetes verziójának verziója – nem támogatott éles használatra

Alkalmanként speciális előzetes kiadásokat is tartalmaz, amelyek jelentős funkcionalitásbeli változásokat tartalmaznak, amelyekkel a korai visszajelzéseket szeretnénk felmérni. Az előzetes verziókat csak elszigetelt tesztelési környezetekben érdemes használni, amelyek nem szolgálnak ki éles számítási feladatokat. Az üzemi fürtnek mindig támogatott, stabil Service Fabric verziót kell futtatnia. Ezekhez az előzetes verzióhoz nem biztosítunk fizetős támogatási lehetőséget.

Az előzetes verzió verziója mindig a fő és alverziószám 255-as számú. Ha például egy Service Fabric 255.255.5703.949-verziót lát, ez a verzió előzetes verzióban érhető el, és csak tesztelési fürtökben használható. Ezek az előzetes verziók a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) is bejelentve lesznek, és a szolgáltatással kapcsolatos részleteket is tartalmaznak. A kérdésekkel kapcsolatos kérdések és visszajelzések megadásához használja az [Azure-Service Fabrici problémák című](#report-azure-service-fabric-issues) részben felsorolt lehetőségek egyikét.

## <a name="next-steps"></a>Következő lépések

[Támogatott Service Fabric verziók](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform