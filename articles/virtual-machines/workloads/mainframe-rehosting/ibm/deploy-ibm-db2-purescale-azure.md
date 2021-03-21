---
title: Az IBM DB2-pureScale üzembe helyezése az Azure-ban
description: Megtudhatja, hogyan helyezhet üzembe egy közelmúltbeli példát a vállalatnak az IBM DB2-környezetből, amely a z/OS-ben fut az Azure-ban az IBM DB2-pureScale.
author: njray
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 481816ca0c1fecfee9396f6cc40582695b0952f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550195"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Az IBM DB2-pureScale üzembe helyezése az Azure-ban

Ez a cikk azt ismerteti, hogyan lehet üzembe helyezni egy olyan [példa architektúrát](ibm-db2-purescale-azure.md) , amelyet a vállalati ügyfél nemrégiben használt az IBM DB2-környezetből a z/os-ben, az Azure-on futó IBM DB2-pureScale.

Az áttelepítéshez használt lépések követéséhez tekintse meg a [DB2onAzure](https://aka.ms/db2onazure) adattár telepítési parancsfájljait a githubon. Ezek a parancsfájlok egy tipikus, közepes méretű online tranzakció-feldolgozási (OLTP) munkaterhelés architektúráján alapulnak.

## <a name="get-started"></a>Bevezetés

Az architektúra üzembe helyezéséhez töltse le és futtassa a [DB2onAzure](https://aka.ms/db2onazure) -tárházban található Deploy.sh-parancsfájlt a githubon.

A tárház parancsfájlokat is tartalmaz a Grafana-irányítópult beállításához. Az irányítópult segítségével lekérdezheti a Prometheus-t, a nyílt forráskódú figyelési és riasztási rendszer részét képező DB2-t.

> [!NOTE]
> Az ügyfélen található deploy.sh-parancsfájl titkos SSH-kulcsokat hoz létre, és a központi telepítési sablonhoz HTTPS-kapcsolaton keresztül továbbítja azokat. A nagyobb biztonság érdekében javasoljuk, hogy az [Azure Key Vault](../../../../key-vault/general/overview.md) használatával tárolja a titkokat, a kulcsokat és a jelszavakat.

## <a name="how-the-deployment-script-works"></a>Az üzembe helyezési parancsfájl működése

A deploy.sh szkript létrehozza és konfigurálja az architektúrához tartozó Azure-erőforrásokat. A parancsfájl kéri a célként megadott környezetben használt Azure-előfizetést és virtuális gépeket, majd a következő műveleteket hajtja végre:

-   Beállítja az Azure-beli erőforráscsoportot, virtuális hálózatot és alhálózatokat a telepítéshez.

-   Beállítja a hálózati biztonsági csoportokat és az SSH-t a környezethez.

-   Több hálózati adaptert állít be a megosztott tárolón és a DB2 pureScale virtuális gépeken is.

-   Létrehozza a megosztott tároló virtuális gépeket. Ha Közvetlen tárolóhelyek vagy egy másik tárolási megoldást használ, tekintse meg a [közvetlen tárolóhelyek áttekintése](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)című témakört.

-   Létrehozza a Jumpbox virtuális gépet.

-   Létrehozza a DB2 pureScale virtuális gépeket.

-   Létrehoz egy olyan tanúsító virtuális gépet, amely DB2 pureScale kopog. A központi telepítés ezen részének kihagyása, ha a DB2-pureScale nem igényel tanúsító példányt.

-   Létrehoz egy Windows rendszerű virtuális gépet, amelyet tesztelésre használ, de nem telepít semmit.

Ezt követően az üzembe helyezési parancsfájlok egy iSCSI virtuális tárolóhely hálózatot (vSAN) állítanak be a megosztott tárolóhoz az Azure-ban. Ebben a példában az iSCSI csatlakozik a megosztott tároló fürthöz. Az eredeti ügyfél-megoldásban a GlusterFS használta. Az IBM azonban már nem támogatja ezt a megközelítést. Az IBM támogatásának fenntartása érdekében egy támogatott iSCSI-kompatibilis fájlrendszert kell használnia. A Microsoft a Közvetlen tárolóhelyek (S2D) lehetőséget kínálja.

Ez a megoldás lehetővé teszi az iSCSI-tárolók egyetlen Windows-csomópontként történő telepítését is. az iSCSI egy megosztott blokk tárolási felületet biztosít a TCP/IP protokollon keresztül, amely lehetővé teszi, hogy a DB2 pureScale telepítési eljárása egy eszköz felületét használja a megosztott tárolóhoz való kapcsolódáshoz.

Az üzembe helyezési parancsfájlok az alábbi általános lépéseket futtatják:

1.  Hozzon létre egy megosztott Storage-fürtöt az Azure-ban. Ez a lépés legalább két Linux-csomópontot foglal magában.

2.  Hozzon létre egy iSCSI Direct-felületet a cél linuxos kiszolgálókon a megosztott tároló fürthöz.

3.  Állítsa be az iSCSI-kezdeményezőt a Linux rendszerű virtuális gépeken. A kezdeményező iSCSI-tároló használatával fogja elérni a megosztott tároló fürtöt. A telepítés részleteiért lásd: [iSCSI-tároló és kezdeményező konfigurálása Linux rendszeren](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) a RootUsers dokumentációjában.

4.  Telepítse az iSCSI-interfész megosztott tárolási rétegét.

Miután a parancsfájlok létrehozták az iSCSI-eszközt, az utolsó lépés a DB2-pureScale telepítése. A DB2 pureScale-telepítés részeként az [IBM Spectrum](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (korábbi nevén GPFS) a GlusterFS-fürtön van lefordítva és telepítve. Ez a fürtözött fájlrendszer lehetővé teszi a DB2-pureScale számára az adatmegosztást a DB2 pureScale motort futtató virtuális gépek között. További információ: [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentáció az IBM webhelyén.

## <a name="db2-purescale-response-file"></a>DB2 pureScale-válaszfájl

A GitHub-tárház tartalmazza a DB2server. RSP, a Response (. RSP) fájlt, amely lehetővé teszi, hogy automatizált parancsfájlt állítson elő a DB2 pureScale telepítéséhez. A következő táblázat a válaszfájl által a telepítőhöz használt DB2 pureScale-beállításokat sorolja fel. A válaszfájlt igény szerint testreszabhatja a környezetében.

> [!NOTE]
> A DB2server. RSP egy minta válaszfájlt tartalmaz a GitHubon található [DB2onAzure](https://aka.ms/db2onazure) -tárházban. Ha ezt a fájlt használja, szerkesztenie kell, mielőtt működni fog a környezetben.

| Képernyő neve               | Mező                                        | Érték                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Üdvözlőképernyő                   |                                              | Új telepítés                                                                                           |
| Termék kiválasztása          |                                              | A DB2 verziója 11.1.3.3. Kiszolgálói kiadások DB2 pureScale                                              |
| Konfiguráció             | Címtár                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | A telepítés típusának kiválasztása                 | Tipikus                                                                                               |
|                           | Elfogadom az IBM használati feltételeit                     | Jelölje be                                                                                               |
| Példány tulajdonosa            | Meglévő felhasználó, példány, Felhasználónév        | DB2sdin1                                                                                              |
| Bekerített felhasználó               | Meglévő felhasználó, felhasználó neve                     | DB2sdfe1                                                                                              |
| Fürt fájlrendszere       | Megosztott lemezpartíció eszközének elérési útja            | /dev/dm-2                                                                                             |
|                           | Csatlakoztatási pont                                  | /DB2sd \_ 1804a                                                                                         |
|                           | Megosztott lemez az adattároláshoz                         | /dev/dm-1                                                                                             |
|                           | Csatlakoztatási pont (adatcsatorna)                           | /DB2fs/datafs1                                                                                        |
|                           | Naplóhoz tartozó megosztott lemez                          | /dev/dm-0                                                                                             |
|                           | Csatlakoztatási pont (napló)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2-fürtszolgáltatások tiebreaker. Eszköz elérési útja | /dev/dm-3                                                                                             |
| Gazdagépek listája                 | D1 [eth1], D2 [eth1], cf1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Elsődleges CF                         | cf1                                                                                                   |
|                           | Előnyben részesített másodlagos CF                       | cf2                                                                                                   |
| Válaszfájl és összefoglalás | első lehetőség                                 | Telepítse a DB2 Server Editiont az IBM DB2 pureScale szolgáltatással, és mentse a beállításokat egy válaszfájlban |
|                           | Válaszfájl neve                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Megjegyzések az üzemelő példányról

- A/dev-dm0, a/dev-DM1, a/dev-dm2 és a/dev-dm3 értékeit a rendszer újraindítása után módosíthatja a virtuális gépen, ahol a telepítés történik (D0 az automatizált parancsfájlban). A megfelelő értékek megkereséséhez adja ki a következő parancsot a válaszfájl azon a kiszolgálón való befejezése előtt, amelyen a telepítő futni fog:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- A telepítési parancsfájlok aliasokat használnak az iSCSI-lemezekhez, hogy a tényleges nevek könnyen megtalálhatók legyenek.

- Ha a d0 futtatja a telepítési parancsfájlt, **a \* /dev/DM-** -értékek a D1, a cf0 és a cf1 értéktől eltérőek lehetnek. Az értékek különbsége nem befolyásolja a DB2 pureScale telepítését.

## <a name="troubleshooting-and-known-issues"></a>Hibaelhárítás és ismert problémák

A GitHub-tárház tartalmaz egy tudásbázist, amelyet a szerzők tartanak fenn. Felsorolja az esetlegesen felmerülő esetleges problémákat és a kipróbálható megoldásokat. Az ismert problémák például a következő esetekben fordulnak elő:

-   Az átjáró IP-címét próbálja elérni.

-   Általános nyilvános licenc (GPL) fordítását végzi.

-   A gazdagépek közötti biztonsági kézfogás meghiúsul.

-   A DB2-telepítő egy meglévő fájlrendszert észlel.

-   Manuálisan telepíti az IBM Spectrum-méretezést.

-   A DB2-pureScale telepítésekor már létrejött az IBM Spectrum skálázása.

-   A DB2-pureScale és az IBM Spectrum-méretezést távolítja el.

További információ ezekről és az egyéb ismert problémákról: kb.md-fájl a [DB2onAzure](https://aka.ms/DB2onAzure) -tárházban.

## <a name="next-steps"></a>Következő lépések

-   [Szükséges felhasználók létrehozása a DB2 pureScale szolgáltatás telepítéséhez](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale-fürtök adatmegoldása](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM-es adatstúdió](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Az Azure Virtual adatközpontjának átemelési és átváltási útmutatója](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
