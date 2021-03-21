---
title: A hálózati forgalom mintáinak megjelenítése nyílt forráskódú eszközökkel
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan használható a Capanalysis és a virtuális gépek közötti adatforgalmi minták megjelenítése a Network Watcher-csomagok rögzítésével.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709512"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>A hálózati forgalom mintáinak megjelenítése a virtuális gépekről és a nyílt forráskódú eszközök használatával

A csomagok rögzítése olyan hálózati adatokkal rendelkezik, amelyek lehetővé teszik a hálózati kriminalisztika és a mélyreható csomagok vizsgálatát. Számos nyílt forráskódú eszközt használhat a csomagok rögzítésének elemzésére a hálózattal kapcsolatos információk megszerzéséhez. Az egyik ilyen eszköz a CapAnalysis, egy nyílt forráskódú csomagkapcsolt vizualizációs eszköz. A csomagok rögzítésével kapcsolatos adatmegjelenítés értékes módszert nyújt a hálózatán belüli minták és anomáliák gyors elemzésére. A vizualizációk az ilyen megállapítások egyszerű és könnyen megosztható módon történő megosztását is lehetővé teszik.

Az Azure Network Watcher lehetővé teszi az adatok rögzítését azáltal, hogy elvégzi a csomagok rögzítését a hálózaton. Ez a cikk bemutatja, hogyan jelenítheti meg és elemezheti a CapAnalysis és a Network Watcher használatával betekintést a csomagokba.

## <a name="scenario"></a>Eset

Az Azure-beli virtuális gépen üzembe helyezett egyszerű webalkalmazások nyílt forráskódú eszközöket szeretnének használni a hálózati forgalom megjelenítéséhez, hogy gyorsan azonosítsák a folyamatok mintáit és az esetleges rendellenességeket. A Network Watcher segítségével beszerezheti a hálózati környezet csomagjainak rögzítését, és közvetlenül tárolhatja azt a Storage-fiókjában. A CapAnalysis ezután közvetlenül a Storage-blobból betöltheti a csomagokat, és megjelenítheti annak tartalmát.

![forgatókönyv][1]

## <a name="steps"></a>Lépések

### <a name="install-capanalysis"></a>A CapAnalysis telepítése

A CapAnalysis virtuális gépen való telepítéséhez a hivatalos utasításokat itt tekintheti meg https://www.capanalysis.net/ca/how-to-install-capanalysis .
Ha távolról szeretné elérni a CapAnalysis, nyissa meg a 9877-es portot a virtuális gépen egy új bejövő biztonsági szabály hozzáadásával. A hálózati biztonsági csoportokban található szabályok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [szabályok létrehozása meglévő NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule)című témakört. A szabály sikeres hozzáadása után hozzáférhet a CapAnalysis-hez `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>A csomagok rögzítési munkamenetének elindítása az Azure Network Watcher használatával

Network Watcher lehetővé teszi a csomagok rögzítését a virtuális gépek és a forgalom nyomon követésére. A csomagok rögzítéseinak [kezelése a Network Watcher](network-watcher-packet-capture-manage-portal.md) használatával című témakör útmutatását követve elindíthatja a csomag rögzítési munkamenetét. A csomagok rögzítése a CapAnalysis által elérhető tárolási blobokban tárolható.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Csomagok rögzítésének feltöltése a CapAnalysis-be
A Network Watcher által készített csomagok rögzítését közvetlenül is feltöltheti az "Importálás URL-címről" lapról, és megadhat egy hivatkozást arra a tárolási blobra, ahol a csomag rögzítését tárolja.

A CapAnalysis mutató hivatkozás megadásakor ügyeljen arra, hogy egy SAS-tokent Fűzzön hozzá a Storage-blob URL-címéhez.  Ehhez navigáljon a megosztott hozzáférési aláíráshoz a Storage-fiókból, jelölje ki az engedélyezett engedélyeket, majd nyomja le az SAS létrehozása gombot a jogkivonat létrehozásához. Ezután hozzáfűzheti az SAS-tokent a csomag rögzítési tárolójának blob URL-címéhez.

Az eredményül kapott URL-cím a következő URL-címhez hasonlóan fog kinézni: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>A csomagok rögzítésének elemzése

A CapAnalysis számos lehetőséget kínál a csomagok rögzítésének megjelenítésére, amelyek mindegyike különböző perspektívából származó elemzéseket biztosít. Ezekkel a vizualizációs összefoglalókkal megismerheti a hálózati forgalom tendenciáit, és gyorsan megtalálhatja a szokatlan tevékenységeket. Néhány ilyen funkció az alábbi listában látható:

1. Folyamatok táblái

    Ez a táblázat a csomagok adatait, a folyamatokhoz társított időbélyegzőt, valamint a folyamathoz társított különböző protokollokat, valamint a forrás és a cél IP-címét tartalmazza.

    ![capanalysis folyamat lapja][5]

1. Protokollok áttekintése

    Ezen ablaktábla segítségével gyorsan megtekintheti a hálózati forgalom eloszlását a különböző protokollokon és földrajzi területeken.

    ![capanalysis protokoll – áttekintés][6]

1. Statisztika

    Ezen a panelen megtekintheti a hálózati forgalom statisztikáit – a forrás-és cél IP-címekről küldött és fogadott bájtokat, a forrás-és cél IP-címeinek folyamatait, a különböző folyamatokhoz használt protokollokat, valamint a folyamatok időtartamát.

    ![capanalysis statisztikája][7]

1. Földrajzi térképet

    Ez a panel a hálózati forgalom térképes nézetét jeleníti meg, és az egyes országokból/régiókból érkező forgalom mennyiségére vonatkozó színeket tartalmaz. A Kiemelt országok/régiók lehetőség kiválasztásával megtekintheti a folyamat további statisztikáit, például az adott országban vagy régióban az IP-címekről küldött és fogadott adatok arányát.

    ![földrajzi térképet][8]

1. Szűrők

    A CapAnalysis szűrőket biztosít az adott csomagok gyors elemzéséhez. Például úgy is dönthet, hogy a protokoll alapján szűri az adatokat, hogy bizonyos információkhoz jussanak a forgalom adott részhalmazán.

    ![szűrők][11]

    További információ az [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) összes CapAnalysis képességről.

## <a name="conclusion"></a>Összegzés

Network Watcher a csomag rögzítési funkciója lehetővé teszi a hálózati kriminalisztikai műveletek elvégzéséhez szükséges adatok rögzítését és a hálózati forgalom jobb megismerését. Ebben az esetben megmutatjuk, hogyan lehet a csomagok rögzítését Network Watcher egyszerűen integrálható a nyílt forráskódú vizualizációs eszközökkel. A nyílt forráskódú eszközökkel, például a CapAnalysis a csomagok rögzítésének megjelenítéséhez a részletes csomagok vizsgálatát és a hálózati forgalomon belüli trendek gyors felismerését teszi lehetővé.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a NSG folyamatokról, látogasson el a [NSG flow naplóiba](network-watcher-nsg-flow-logging-overview.md)

Ismerje meg, hogyan jelenítheti meg a NSG-flow-naplókat a Power BI a [NSG flow-naplók megjelenítése a Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
