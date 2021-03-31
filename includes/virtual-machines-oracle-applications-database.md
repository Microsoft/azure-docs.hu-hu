---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317741"
---
### <a name="database-tier"></a>Adatbázis szintje

Az adatbázis szintje tartalmazza az alkalmazás adatbázis-példányait. Az adatbázis lehet Oracle DB, Oracle RAC vagy Oracle Exadata Database rendszer. 

Ha a választás a Oracle DB használata, az adatbázis-példány az Azure piactéren elérhető Oracle DB rendszerképeken keresztül is üzembe helyezhető az Azure-ban. Azt is megteheti, hogy az összekötőt az Azure és a OCI között használja, hogy a Oracle DBt a OCI-on található egy Pásti modellben telepítse.

Az Oracle RAC esetében a OCI a Pásti modellben használható. Ajánlott két csomópontos RAC-rendszer használata. Habár az Oracle RAC az Azure CloudSimple-ben is üzembe helyezhető a IaaS-modellben, nem támogatott az Oracle által. A jogosult [felhőalapú környezetekhez jogosult Oracle-programokért](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)tekintse meg a következőt:.

Végül a Exadata rendszerek esetében használja a OCI interconnectt, és telepítse a Exadata rendszert a OCI-ben. A fenti architektúra-diagramon egy, a OCI-ben üzembe helyezett Exadata-rendszer látható két alhálózaton belül.

Éles forgatókönyvek esetén az adatbázis több példányát is üzembe helyezheti két rendelkezésre állási zónában (ha az Azure-ban üzembe helyezhető), vagy két rendelkezésre állási tartomány (a OCI-ben). Az Oracle Active Database Guard használatával szinkronizálhatja az elsődleges és a készenléti adatbázist.

Az adatbázis-rétegek csak a középső szintű kérelmeket kapják meg. Javasoljuk, hogy hozzon létre egy hálózati biztonsági csoportot (biztonsági lista, ha az adatbázist a OCI-ben telepíti), hogy csak a középső szinten és a 22-es porton engedélyezze a kérelmeket rendszergazdai okokból a megerősített kiszolgálóról a 1521-as porton.

A OCI-ben üzembe helyezett adatbázisok esetében egy különálló virtuális felhőalapú hálózatot kell beállítani egy olyan dinamikus útválasztási átjáróval (DRG), amely a FastConnect-áramkörhöz csatlakozik.