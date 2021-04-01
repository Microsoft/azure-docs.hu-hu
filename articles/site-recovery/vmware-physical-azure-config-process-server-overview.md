---
title: Tudnivalók Azure Site Recovery konfiguráció/folyamat/fő célkiszolgáló
description: Ez a cikk áttekintést nyújt azokról a konfigurációs, feldolgozási és fő célkiszolgáló-kiszolgálókról, amelyek a helyszíni VMware virtuális gépek vész-helyreállításának beállításakor az Azure-ba Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062090"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Tudnivalók Site Recovery összetevőkről (konfiguráció, folyamat, fő cél)

Ez a cikk ismerteti a [site Recovery](site-recovery-overview.md) szolgáltatás által a VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba történő replikálásához használt konfigurációs, folyamat-és fő célkiszolgáló-kiszolgálókat.

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

A helyszíni VMware virtuális gépek és fizikai kiszolgálók vész-helyreállításához helyezzen üzembe egy helyszíni Site Recovery konfigurációs kiszolgálót.

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Összetevők**  | A konfigurációs kiszolgáló számítógép az összes helyszíni Site Recovery-összetevőt futtatja, amely tartalmazza a konfigurációs kiszolgálót, a feldolgozási kiszolgálót és a fő célkiszolgáló-kiszolgálót.<br/><br/> A konfigurációs kiszolgáló beállításakor a rendszer az összes összetevőt automatikusan telepíti. | [Olvassa el](vmware-azure-common-questions.md#configuration-server) a konfigurációs kiszolgálóval kapcsolatos gyakori kérdéseket.
**Szerepkör** | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. | További információ a [VMware](vmware-azure-architecture.md) és a [fizikai kiszolgáló](physical-azure-architecture.md) által az Azure-ba való vész-helyreállítás architektúráján.
**VMware-követelmények** | A helyszíni VMware virtuális gépek vész-helyreállításához a konfigurációs kiszolgálót helyszíni, magasan elérhető VMware virtuális gépre kell telepítenie és futtatnia. | [További](vmware-azure-deploy-configuration-server.md#prerequisites) információ az előfeltételekről.
**VMware üzemelő példány** | Javasoljuk, hogy a konfigurációs kiszolgálót egy letöltött PETESEJT-sablonnal telepítse. Ez a módszer egyszerűen beállítható egy olyan konfigurációs kiszolgáló beállítására, amely megfelel az összes követelménynek és előfeltételnek.<br/><br/> Ha valamilyen okból kifolyólag nem tudja telepíteni a VMware virtuális gépet egy PETESEJT-sablonnal, manuálisan is beállíthatja a konfigurációs kiszolgáló-gépeket, az alábbiakban leírtak szerint a fizikai gép vész-helyreállítási feladataihoz. | [Üzembe helyezés](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) petesejt-sablonnal.
**Fizikai kiszolgáló követelmények** | A helyszíni fizikai kiszolgálókon a vész-helyreállításhoz manuálisan kell telepíteni a konfigurációs kiszolgálót. | [További](physical-azure-set-up-source.md#prerequisites) információ az előfeltételekről.
**Fizikai kiszolgáló üzembe helyezése** | Ha nem telepíthető VMware virtuális gépként, akkor telepítheti azt egy fizikai kiszolgálóra. | [Telepítse](physical-azure-set-up-source.md#set-up-the-source-environment) manuálisan a konfigurációs kiszolgálót.

## <a name="process-server"></a>Folyamatkiszolgáló

A folyamat-kiszolgáló a feladatátvétel és a feladat-visszavétel során kezeli a replikációs adatreplikációt, és telepíti a mobilitási szolgáltatást a helyszíni VMware virtuális gépekhez és fizikai kiszolgálókhoz.

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Üzembe helyezés**  | Alapértelmezés szerint a konfigurációs kiszolgáló központi telepítésekor a rendszer telepíti a Process Servert. <br/><br/> A helyszíni VMware virtuális gépek és a fizikai kiszolgálók helyreállításához és replikálásához helyszíni folyamat-kiszolgáló szükséges. | [További információ](vmware-azure-architecture.md#architectural-components).
**Szerepkör (** helyszíni) | Replikációs adatok fogadása a replikációhoz engedélyezett gépekről. <br/><br/> A replikációs adatokat a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és elküldi az Azure Storage-nak. <br/><br/> Elvégzi a Site Recovery mobilitási szolgáltatás leküldéses telepítését a replikálni kívánt helyszíni VMware virtuális gépeken és fizikai kiszolgálókon. <br/><br/> A helyszíni gépek automatikus felderítését végzi. | [További információ](vmware-azure-enable-replication.md).
**Szerepkör (feladat-visszavétel az Azure-ból)** | A helyszíni helyről történő feladatátvétel után az Azure-beli Process Servert egy Azure-beli virtuális gépként állíthatja be a helyszíni helyre történő feladat-visszavétel kezeléséhez.<br/><br/> Az Azure-beli Process Server ideiglenes. Az Azure-beli virtuális gép a feladat-visszavétel befejezése után törölhető. | [További információ](vmware-azure-set-up-process-server-azure.md).
**Méretezés** | A helyszíni nagyobb telepítések esetén további, kibővíthető folyamat-kiszolgálókat is beállíthat. A további kiszolgálók kibővítik a kapacitást, így nagyobb számú replikáló gépet kezelnek, és nagyobb mennyiségű replikációs forgalmat bonyolítanak le.<br/><br/> A gépeket áthelyezheti két folyamat kiszolgálója között a replikálási forgalom terheléselosztásához. | [További információ](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Fő célkiszolgáló

A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

- Alapértelmezés szerint a fő célkiszolgáló telepítve van a konfigurációs kiszolgálón.
- Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási [architektúráját](vmware-azure-architecture.md) .
- Tekintse át a VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállításának [követelményeit és előfeltételeit](vmware-physical-azure-support-matrix.md) .
