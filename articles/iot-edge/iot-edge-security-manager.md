---
title: Azure IoT Edge Security Manager – Azure IoT Edge
description: Felügyeli a IoT Edge eszköz biztonsági irányvonalát és a biztonsági szolgáltatások integritását.
services: iot-edge
keywords: biztonság, biztonságos elem, enklávé, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 468f1b91d6c6157cd2af6de9599bad7f43c1ad8f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492557"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge Security Manager

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

A Azure IoT Edge Security Manager egy jól körülhatárolt biztonsági mag, amely az IoT Edge eszköz és annak összes összetevőjének védelmét biztosítja a biztonságos szilícium-hardver eltulajdonítása révén. A Security Manager a biztonság megerősítésének középpontja, és technológiai integrációs pontot biztosít az eredeti berendezésgyártó (OEM) számára.

![Azure IoT Edge Security Manager](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge Security Manager célja, hogy megvédje a IoT Edge eszköz integritását és az összes benne rejlő szoftveres műveletet. A Security Manager megbízik az alapul szolgáló hardver gyökerétől (ha elérhető) a IoT Edge futtatókörnyezet rendszerindításához és a folyamatban lévő műveletek figyeléséhez.  A IoT Edge Security Manager olyan szoftver, amely biztonságos szilícium-hardverrel (ahol elérhető) segíti a lehető legmagasabb szintű biztonsági garanciák nyújtását.  

A IoT Edge Security Manager feladatai közé tartoznak a következők:

* A Azure IoT Edge eszköz biztonságos és mért rendszerindítását.
* Az eszköz identitásának kiépítés és a megbízhatóság átállása, ahol lehetséges.
* A Cloud Services, például a Device kiépítési szolgáltatás eszköz-összetevőinek üzemeltetése és biztosítása.
* IoT Edge-modulok biztonságos kiépítése egyedi identitásokkal.
* Az eszköz hardveres megbízhatósági gyökeréhez a közjegyzői szolgáltatásokon keresztül.
* IoT Edge műveletek integritásának figyelése futásidőben.

A IoT Edge Security Manager három összetevőt tartalmaz:

* IoT Edge biztonsági démon.
* Hardveres biztonsági modul platform absztrakciós rétege (HSM PAL).
* Nem kötelező, de kifejezetten ajánlott hardveres szilícium-gyökér vagy HSM.

## <a name="the-iot-edge-security-daemon"></a>A IoT Edge biztonsági démon

A IoT Edge biztonsági démon felelős a IoT Edge Security Manager logikai műveleteiért. Ez a IoT Edge eszköz megbízható számítástechnikai alapjainak jelentős részét képezi.

### <a name="design-principles"></a>Tervezési alapelvek

A IoT Edge biztonsági démon két alapvető alapelvet követ: maximalizálja a működési integritást, és csökkentse a duzzadás és a forgalom minimalizálását.

#### <a name="maximize-operational-integrity"></a>Az operatív integritás maximalizálása

A IoT Edge biztonsági démon a lehető legnagyobb integritással működik a megbízhatósági kapcsolatok bármelyik gyökerének védelmi képességein belül. A megfelelő integráció révén a megbízhatósági kapcsolati hardver mértékei megmérik a biztonságot, és a biztonsági démont statikusan és futásidőben figyelik, hogy ellenálljon a hamisításnak.

A fizikai hozzáférés mindig fenyegetést jelent az eszközök IoT. A megbízható hardver gyökerének fontos szerepet játszik a IoT Edge biztonsági démon integritásának védelmében.  A megbízható hardver gyökerének két fajtája van:

* biztonságos elemek a bizalmas információk, például a titkok és a titkosítási kulcsok védelméhez.
* biztonságos enklávék a titkok, például a kulcsok védelméhez, valamint az olyan érzékeny munkaterhelésekhez, mint a mérés és a számlázás.

A megbízhatósági kapcsolat gyökerének kétféle végrehajtási környezete létezik:

* A bizalmas adatok védelme érdekében a biztonságos elemek használatára támaszkodó standard vagy sokoldalú végrehajtási környezet (REE).
* Az a megbízható végrehajtási környezet (TEE), amely a biztonságos enklávé technológiájának használatára támaszkodik a bizalmas információk védelme érdekében, és védelmet nyújt a szoftverek végrehajtásához.

Az olyan eszközök esetében, amelyekben a biztonságos enklávék a megbízhatóság gyökerének minősülnek, IoT Edge biztonsági démonon belüli bizalmas logikának az enklávéban kell lennie.  A biztonsági démon nem bizalmas részei a PÓLÓn kívül is lehetnek.  Az eredeti tervezési gyártók (ODM-EK) és az eredeti berendezésgyártók (OEM-ek) minden esetben kiterjesztik a megbízhatóságot a HSM-ből a rendszerindítási és futtatókörnyezeti IoT Edge biztonsági démon integritásának méréséhez és védelméhez.

#### <a name="minimize-bloat-and-churn"></a>A duzzadás és a forgalom csökkentése

A IoT Edge biztonsági démon egy másik alapelve, hogy csökkentse a forgalom minimalizálását.  A legmagasabb szintű megbízhatóság érdekében a IoT Edge biztonsági démon szorosan összekapcsolhatja az eszköz hardveres megbízhatósági gyökerét, és natív kóddal működhet.  Az ilyen típusú felismerések esetében gyakori, hogy a démoni szoftvereket a megbízhatósági kapcsolatok biztonságos frissítésének elérési útjai (az operációs rendszer által megadott frissítési mechanizmusok helyett) segítségével frissítsék, ami bizonyos helyzetekben kihívást jelenthet.  Míg a IoT-eszközök biztonsági megújítása ajánlott, a túlzott frissítési követelmények és a nagyméretű frissítési adattartalom számos módon kiterjeszthető a veszélyforrások felületére.  Ilyenek például a frissítések kihagyása az üzemelő példányok rendelkezésre állásának maximalizálása érdekében, vagy a megbízhatósági kapcsolat gyökerének gyökere a nagyméretű frissítési adattartalom feldolgozásához.  Ennek megfelelően a IoT Edge biztonsági démon kialakítása tömören tartja a lábnyomot és a megbízható számítástechnikai alapot, és a frissítési követelmények minimalizálására.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge biztonsági démon architektúrája

![Azure IoT Edge biztonsági démon](media/edge-security-manager/iot-edge-security-daemon.png)

A IoT Edge biztonsági démon kihasználja a megbízhatósági technológia összes rendelkezésre álló hardveres gyökerét a biztonság megerősítéséhez.  Emellett lehetővé teszi a megosztott működést a standard/sokoldalú végrehajtási környezet (REE) és egy megbízható végrehajtási környezet (TEE) között, amikor a hardveres technológiák megbízható végrehajtási környezeteket kínálnak. A szerepkör-specifikus felületek lehetővé teszik a IoT Edge fő összetevőinek a IoT Edge eszköz és a műveletei integritásának biztosítását.

#### <a name="cloud-interface"></a>Felhőalapú felület

A Cloud Interface lehetővé teszi, hogy a IoT Edge biztonsági démon hozzáférjen a Cloud Serviceshez, például a Felhőbeli bókohoz, hogy az eszköz biztonsága, például a biztonsági megújítás.  Például a IoT Edge biztonsági démon jelenleg ezt a felületet használja az Azure IoT Hub [Device kiépítési szolgáltatáshoz](../iot-dps/index.yml) való hozzáféréshez az eszköz identitásának életciklus-kezeléséhez.  

#### <a name="management-api"></a>Felügyeleti API

IoT Edge Security Daemon egy felügyeleti API-t kínál, amelyet az IoT Edge ügynök hív meg egy IoT Edge modul létrehozásakor/elindításakor/leállításakor vagy eltávolításakor. A biztonsági démon az összes aktív modul "regisztrációját" tárolja. Ezek a regisztrációk egy modul identitását képezik le a modul egyes tulajdonságaihoz. Ilyenek például például a tárolóban futó folyamat azonosítója (PID) és a Docker-tároló tartalmának kivonata.

Ezeket a tulajdonságokat a munkaterhelés API (lásd alább) használja annak ellenőrzéséhez, hogy a hívó jogosult-e a művelet végrehajtására.

A felügyeleti API egy kiemelt API, amely csak a IoT Edge ügynöktől hívható meg.  Mivel a IoT Edge biztonsági démon rendszerindítást kezdeményez, és elindítja a IoT Edge ügynököt, ellenőrzi, hogy az IoT Edge-ügynök nem lett-e illetéktelenül módosítva, majd létrehozhatja az IoT Edge ügynök implicit regisztrációját. Ugyanez az igazolási folyamat, amelyet a számítási feladatok API használ, korlátozza a felügyeleti API elérését is, hogy csak a IoT Edge ügynökhöz.

#### <a name="container-api"></a>Container API

A Container API a modul felügyeletéhez használt tárolórendszer (például a Moby vagy a Docker) használatával működik együtt.

#### <a name="workload-api"></a>Számítási feladatok API

A számítási feladatok API minden modul számára elérhető. Az identitást a HSM-feltört aláírt tokenként vagy egy X509-tanúsítványként, valamint a megfelelő megbízhatósági csomagban adja meg egy modulnak. A megbízhatósági csomag HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmaz az összes többi kiszolgálónak, amelyeknek a moduloknak megbízhatónak kell lenniük.

A IoT Edge Security Daemon egy igazolási folyamattal fogja védeni ezt az API-t. Amikor egy modul meghívja ezt az API-t, a biztonsági démon megpróbálja megkeresni az identitás regisztrációját. Ha a művelet sikeres, a a regisztráció tulajdonságait használja a modul méréséhez. Ha a mérési folyamat eredménye megegyezik a regisztrációval, a rendszer új identitást hoz létre. A rendszer visszaadja a megfelelő HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat (megbízhatósági csomagot) a modulnak.  A modul ezt a tanúsítványt használja a IoT Hubhoz, más modulokhoz való kapcsolódáshoz vagy a kiszolgáló indításához. Ha az aláírt jogkivonat vagy tanúsítvány hamarosan lejár, a modul feladata az új tanúsítvány igénylése.

### <a name="integration-and-maintenance"></a>Integráció és karbantartás

A Microsoft a [githubon a IoT Edge biztonsági démon](https://github.com/Azure/iotedge/tree/master/edgelet)fő kódját tárolja.

#### <a name="installation-and-updates"></a>Telepítés és frissítés

A IoT Edge biztonsági démon telepítése és frissítései az operációs rendszer csomagkezelő rendszerán keresztül kezelhetők. IoT Edge az eszközöket, amelyekhez a megbízhatósági kapcsolat szükséges, meg kell adnia a démon integritását azáltal, hogy az életciklusát a biztonságos rendszerindítási és frissítési felügyeleti rendszerek segítségével kezeli. Az eszközöknek a megfelelő eszköz képességei alapján kell megvizsgálniuk ezeket az eszközöket.

#### <a name="versioning"></a>Verziókezelés

A IoT Edge futtatókörnyezet nyomon követi és jelentést készít a IoT Edge biztonsági démon verziószámáról. A verzió jelentése a IoT Edge Agent modul jelentett tulajdonságának *futtatókörnyezet. platform. Version* attribútuma.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardveres biztonsági modul platform absztrakciós rétege (HSM PAL)

A HSM PAL elvonta a megbízhatósági kapcsolatok összes gyökerét, hogy elkülönítse a IoT Edge fejlesztőjét vagy felhasználóját a bonyolultságuk alapján.  Az API-k és a tartományok közötti kommunikációs eljárások kombinációját foglalja magában, például a szabványos végrehajtási környezet és a biztonságos enklávé közötti kommunikációt.  A HSM-PAL tényleges megvalósítása a használt biztonságos hardvertől függ. Létezése lehetővé teszi a gyakorlatilag bármilyen biztonságos szilícium-hardver használatát.

## <a name="secure-silicon-root-of-trust-hardware"></a>A megbízható szilícium gyökerének védelme hardver

A biztonságos szilícium szükséges ahhoz, hogy a megbízhatóságot a IoT Edge eszköz hardverén belül rögzíteni lehessen.  A biztonságos szilícium sokféle platformmegbízhatósági modul (TPM), beágyazott biztonságos elem (eSE), ARM TrustZone, Intel SGX ENKLÁVÉHOZ és egyéni biztonságos szilícium-technológiák közé tartozik.  Az eszközök biztonságos szilícium-megbízhatósági gyökerének használata ajánlott a IoT-eszközök fizikai hozzáférhetőségével kapcsolatos fenyegetések miatt.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>A Security Manager integrációja és karbantartása IoT Edge

A IoT Edge Security Manager célja a Azure IoT Edge platform biztonságának és integritásának védelmét biztosító összetevők azonosítása és elkülönítése az egyéni megerősítés érdekében. A harmadik feleknek, például az eszközök gyártóinak az eszköz hardverével elérhető egyéni biztonsági funkciókat kell használniuk.  Az Azure IoT Security Manager a Linux és a Windows rendszerű platformon platformmegbízhatósági modul (TPM) használatával történő megerősítését bemutató hivatkozások a következő lépések szakaszban olvashatók. Ezek a példák szoftvereket vagy virtuális TPM használnak, de közvetlenül a különálló TPM-eszközök használatára vonatkoznak.  

## <a name="next-steps"></a>Következő lépések

Olvassa el a blogot az [intelligens peremhálózat biztonságossá](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)tételéről.

Virtuális TPM-sel rendelkező IoT Edge-eszköz létrehozása és kiépítése [Linux rendszerű virtuális gépen](how-to-auto-provision-simulated-device-linux.md).

IoT Edge eszköz létrehozása és kiépítése [szimulált TPM](how-to-auto-provision-simulated-device-windows.md)-sel Windows rendszeren.