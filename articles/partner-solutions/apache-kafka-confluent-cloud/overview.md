---
title: Apache Kafka az Azure-beli felhőalapú áttekintés – Azure partneri megoldások
description: Ismerje meg, hogyan használhatja a Apache Kafkat az Azure Marketplace-en az Fluent felhőben.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: fefbc21c385e3beacbf570c31ffbf97238c780fc
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109084"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Mi az Apache Kafka az Fluent felhőhöz?

A Apache Kafka for Fluent Cloud egy Azure Marketplace-ajánlat, amely szolgáltatásként Apache Kafka biztosít. Teljes körűen felügyelt, így a fürtök kezelése helyett az alkalmazások kiépítésére koncentrálhat.

A többplatformos felügyelet terhének csökkentése érdekében a Microsoft a közösen felhővel együttműködve integrált kiépítési réteget épít ki az Azure-ból a felhőre. Összevont élményt nyújt az Azure-beli Fluent felhő használatához. Az Azure-alkalmazásokkal könnyedén integrálhat és kezelhet Fluent-felhőket.

Korábban meg kellett vásárolnia a Fluent Felhőbeli ajánlatot a piactéren, és külön kell beállítania a fiókot a felhőben. A konfigurációk és erőforrások kezeléséhez navigáljon a portálok között az Azure-hoz és a felhőhöz.

Most pedig a **Microsoft. Fluent** nevű erőforrás-szolgáltatón keresztül helyezheti üzembe az Fluent Felhőbeli erőforrásokat. A felhőalapú szervezeti erőforrásokat a [Azure Portal](https://portal.azure.com/), az [Azure CLI](/cli/azure/)vagy az [Azure SDK](/azure/#languages-and-tools)-k segítségével hozhatja létre és kezelheti. A felhő tulajdonosa és futtatja a szolgáltatott szoftver (SaaS) alkalmazást, beleértve a környezeteket, a fürtöket, a témákat, az API-kulcsokat és a felügyelt összekötőket.

## <a name="capabilities"></a>Képességek

Az Fluent felhő és az Azure közötti mély integráció a következő képességeket biztosítja:

- Hozzon létre egy új, a teljes körűen felügyelt infrastruktúrával rendelkező felhőalapú szervezeti erőforrást a Azure Portal.
- Az egyszeri bejelentkezés (SSO) egyszerűsítése az Azure-ból a Azure Active Directory (Azure AD) szolgáltatással való együttműködésre. Az Fluent felhő-portálon nincs szükség külön hitelesítésre.
- Az Azure-előfizetések számlázása révén egységes számlázást érhet el a felhőben.
- Felügyelheti a Azure Portal a felhőalapú erőforrásokat, és nyomon követheti azokat az **összes erőforrás** lapon a többi Azure-erőforrással.

## <a name="confluent-organization"></a>Fluent szervezet

A Fluent szervezet egy olyan erőforrás, amely az Azure és a felhőalapú erőforrások közötti leképezést biztosítja. Ez a szülő-erőforrás más, a felhőalapú erőforrásokhoz.

Minden egyes Azure-előfizetés több Fluent-csomagot is tartalmazhat. Az egyes Fluent-tervek egy felhasználói fiókra és egy szervezetre vannak leképezve az Fluent portálon. Minden egyes Fluent szervezeten belül több környezetet, fürtöt, témakört és összekötőt is létrehozhat.

Ha az Azure-ban egy Fluent Felhőbeli erőforrást épít ki, akkor egy Fluent szervezeti azonosító, alapértelmezett környezet és felhasználói fiók fog megjelenni. További információ: gyors üzembe helyezés [Az Azure](create.md)-ban

Számlázás esetén minden egyes, a piactéren megvásárolt, interaktív Felhőbeli ajánlat egy egyedi, Fluent szervezetre mutat.

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

Amikor bejelentkezik a Azure Portalba, a rendszer a hitelesítő adatokat is felhasználja az Fluent Cloud SaaS-portálra való bejelentkezéshez. A felhasználói élmény az [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) és az [Azure ad SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) használatával biztonságos és kényelmes módot biztosít a bejelentkezéshez.

További információ: [egyszeri bejelentkezés](manage.md#single-sign-on).

## <a name="billing"></a>Számlázás

Két számlázási lehetőség érhető el: utólagos elszámolású havi csomag és kötelezettségvállalási terv.

- Az utólagos elszámolású **havi csomaggal** megtekintheti az Azure-beli havi számlán megjelenő felhőalapú használati díjakat.
- A kötelezettségvállalási terv alapján a minimálisan szükséges **költségekre** regisztrálhat, és kedvezményes áron veheti igénybe az elkötelezett Felhőbeli használatát.

Ön dönti el, hogy melyik számlázási lehetőséget szeretné használni a szolgáltatás létrehozásakor.

## <a name="connector-to-azure-cosmos-db"></a>Összekötő Azure Cosmos DB

A Fluent hub-ügyfélen telepítse az Cosmos DB-összekötőt a [Fluent hub-lista](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)által javasolt módon. 

Az összekötő manuális telepítéséhez először töltse le az Über JAR-t a [Cosmos db releases lapról](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Azt is megteheti, hogy létrehoz [egy saját über jar-t közvetlenül a forráskódból](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Fejezze be a telepítést a következő témakörben ismertetett útmutatást követve: az [Összekötők manuális telepítése manuálisan](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="confluent-links"></a>Fluent hivatkozások

Ha további segítségre van a Apache Kafka az Fluent felhőhöz való használatával kapcsolatban, tekintse meg a következő hivatkozásokat a [Fluent webhelyre](https://docs.confluent.io/home/overview.html).

A számlázási beállításokkal kapcsolatos további tudnivalókért lásd:

* [Az Azure Marketplace fizetési lehetősége](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace és kötelezettségvállalások](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

A megoldások kezelésével kapcsolatos további információkért lásd:

* [Fürt létrehozása az Fluent-felhőben](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Felhőalapú környezetek](https://docs.confluent.io/current/cloud/using/environments.html)
* [A felhő alapjai](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Támogatás és feltételek esetén lásd:

* [Fluent támogatás](https://support.confluent.io)
* [Szolgáltatási feltételek](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Következő lépések

Ha Apache Kafka-példányát szeretné létrehozni a Fluent felhőhöz, tekintse meg a gyors útmutató [: az Azure-ban való együttműködés a felhőben](create.md)című témakört.
