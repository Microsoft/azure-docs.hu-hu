---
title: Fizikai kiszolgáló vész-helyreállítási architektúrája Azure Site Recovery
description: Ez a cikk áttekintést nyújt azokról az összetevőkről és architektúráról, amelyeket a helyszíni fizikai kiszolgálók az Azure-ba történő, a Azure Site Recovery szolgáltatással történő helyreállításakor használtak.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654727"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fizikai kiszolgálókról Azure-ba történő vészhelyreállítás architektúrája

Ez a cikk a fizikai Windows-és Linux-kiszolgálók egy helyszíni hely és az Azure közötti replikálására, feladatátvételére és helyreállítására használt architektúrát és folyamatokat ismerteti a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.

## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra áttekintést nyújt az Azure-ba való fizikai kiszolgálók replikálásához használt összetevőkről.

| **Összetevő** | **Követelmény** | **Részletek** |
| --- | --- | --- |
| **Azure** | Egy Azure-előfizetés és egy Azure-hálózat. | A helyszíni fizikai gépekről replikált adatok tárolása az Azure Managed Disks szolgáltatásban történik. Az Azure-beli virtuális gépek a replikált adatokkal jönnek létre, amikor feladatátvételt hajt végre a helyszínen az Azure-ba. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz. |
| **Folyamatkiszolgáló** | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt települ. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A Process Server is telepíti a mobilitási szolgáltatást a replikálni kívánt kiszolgálókra.<br/><br/> Az üzembe helyezés során további, különálló folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez. |
| **Fő célkiszolgáló** | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt települ. | Kezeli a replikálási adatait az Azure-ban végzett feladat-visszavétel során.<br/><br/> Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez. |
| **Replikált kiszolgálók** | A mobilitási szolgáltatás minden replikált kiszolgálóra telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a folyamat-kiszolgálóról. Vagy manuálisan is telepítheti a szolgáltatást, vagy használhat automatikus telepítési módszert, például Configuration Manager. |

**Fizikairól Azure-ra architektúra**

![Összetevők](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Kimenő hálózati kapcsolat beállítása

Ahhoz, hogy a Site Recovery a várt módon működjön, módosítania kell a kimenő hálózati kapcsolatot, hogy a környezet replikálása engedélyezve legyen.

> [!NOTE]
> A Site Recovery nem támogatja a hitelesítési proxy használatát a hálózati kapcsolat vezérléséhez.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi URL-címek elérését:

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Tárolás                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| Replikáció               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |


## <a name="replication-process"></a>Replikációs folyamat

1. Be kell állítania a központi telepítést, beleértve a helyszíni és az Azure-összetevőket is. A Recovery Services-tárolóban adja meg a replikálás forrását és célját, állítsa be a konfigurációs kiszolgálót, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
1. A gépek replikálása a replikációs házirend segítségével történik, és a kiszolgáló adatainak kezdeti másolatát az Azure Storage-ba replikálja a rendszer.
1. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A gép nyomon követett módosításait a _. HRL_ kiterjesztésű fájlban kell tárolni.
   - A számítógépek a 443 Bejövő HTTPS-porton keresztül kommunikálnak a konfigurációs kiszolgálóval a replikálás kezeléséhez.
   - A gépek replikációs adatküldést küldenek a Process Servernek a 9443 Bejövő HTTPS-porton (módosítható).
   - A konfigurációs kiszolgáló az Azure-ba irányuló replikációs felügyeletet a 443 kimenő HTTPS-porton keresztül hangolja össze.
   - A Process Server adatokat fogad a forrásoldali gépekről, optimalizálja és titkosítja azt, majd az Azure Storage-ba küldi az 443 kimenő HTTPS-porton keresztül.
   - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ezek a csoportok akkor hasznosak, ha a gépek ugyanazt a számítási feladatot futtatják, és konzisztensnek kell lenniük.
1. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/about-public-peering.md) szolgáltatását is használhatja.

   > [!NOTE]
   > A replikáció nem támogatott helyek közötti VPN-kapcsolaton keresztül egy helyszíni helyről vagy az Azure ExpressRoute-alapú [privát társáról](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute).

**Fizikai – Azure replikálási folyamat**

![Replikációs folyamat](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

A replikáció beállítása után a vész-helyreállítási gyakorlat (feladatátvételi teszt) futtatásával ellenőrizheti, hogy minden a várt módon működik-e. Ezt követően szükség szerint visszaküldheti és visszaküldheti a feladatátvételt. Vegye figyelembe a következő elemeket:

- A tervezett feladatátvétel nem támogatott.
- A helyszíni VMware virtuális géphez való visszatérésre van szükség. Helyszíni VMware-infrastruktúrára van szükség, még akkor is, ha helyszíni fizikai kiszolgálókat replikál az Azure-ba.
- Egyetlen gép feladatátvételét hajthatja végre, vagy helyreállítási terveket hozhat létre a több gép feladatátvételéhez.
- Feladatátvétel futtatásakor az Azure-beli virtuális gépek a replikált adatokból jönnek létre az Azure Storage-ban.
- A kezdeti feladatátvétel elindítása után véglegesíteni kell az Azure-beli virtuális gép számítási feladatainak eléréséhez.
- Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
- Olyan feladat-visszavételi infrastruktúra beállítása, amely a következőket tartalmazza:
  - **Ideiglenes Process Server az Azure-ban**: az Azure-ból való feladatátvételhez beállíthat egy Azure-beli virtuális gépet, amely folyamat-kiszolgálóként működik az Azure-ból történő replikáció kezeléséhez. Ezt a virtuális gépet a feladat-visszavétel befejezése után törölheti.
  - **VPN-kapcsolat**: a feladat-visszavétel érdekében VPN-kapcsolatra (vagy Azure ExpressRoute) van szükség az Azure-hálózatról a helyszíni helyre.
  - **Különálló fő célkiszolgáló**: alapértelmezés szerint a feladatátvételt a helyszíni VMWare virtuális gépen a konfigurációs kiszolgálóval telepített fő célkiszolgáló kezeli. Ha nagy mennyiségű forgalmat kell visszaadnia, állítson be egy különálló helyszíni fő célkiszolgáló-kiszolgálót.
  - **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A rendszer automatikusan létrehozta a szabályzatot, amikor a helyi környezetből az Azure-ba hozta létre a replikációs házirendet.
  - **VMware-infrastruktúra**: a feladat-visszavétel érdekében VMware-infrastruktúrára van szükség. Fizikai kiszolgáló nem használható a feladat-visszavételhez.
- Az összetevők betartása után a feladat-visszavétel három lépésben történik:
  - **1. lépés**: az Azure-beli virtuális gépek ismételt védetté tételével, hogy az Azure-ból vissza lehessen térni a helyszíni VMWare virtuális gépekre.
  - **2. fázis**: feladatátvétel futtatása a helyszíni helyre.
  - **3. fázis**: a munkaterhelések visszaállítása után engedélyezze újra a replikációt.

**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Következő lépések

A fizikai kiszolgálók Azure-ba való vész [-](physical-azure-disaster-recovery.md)helyreállításának beállításához tekintse meg a útmutató útmutatását.
