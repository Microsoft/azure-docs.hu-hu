---
title: Példák a Azure Lab Services-osztályokra | Microsoft Docs
description: Bizonyos típusú osztályokat biztosít, amelyekhez a Azure Lab Services használatával állíthatja be a laborokat.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627433"
---
# <a name="class-types-overview---azure-lab-services"></a>Osztályok típusai – áttekintés – Azure Lab Services

Azure Lab Services lehetővé teszi, hogy gyorsan be lehessen állítani a Felhőbeli tantermi tesztkörnyezet környezeteit. Az ebben a szakaszban található cikkek segítséget nyújtanak a különböző típusú laborok beállításához Azure Lab Services használatával.

## <a name="arcgis"></a>ArcGIS
A [ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) egy földrajzi információs rendszer (GIS) típusa.  Beállíthat egy olyan labort, amely a ArcGIS asztal különböző alkalmazásait használja, például a [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) a 2D-térképek létrehozásához, szerkesztéséhez és elemzéséhez.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [labor beállítása a ArcMap\ArcGIS Desktophoz](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Big data elemzése
Beállíthat egy GPU-labort big data elemzési osztály tanításához. Ilyen típusú osztályok esetén a tanulók megismerhetik a nagy adatmennyiségek kezelését, valamint gépi és statisztikai tanulási algoritmusokat alkalmazhatnak az adatelemzések kinyeréséhez. A tanulók számára a fő cél az, hogy megtanulja az adatelemzési eszközök használatát, mint például a Apache Hadoop nyílt forráskódú szoftvercsomag, amely eszközöket biztosít a big data tárolásához, kezeléséhez és feldolgozásához. 

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása Big Data-elemzéshez a HortonWorks-adatplatform Docker-alapú üzembe helyezésével](class-type-big-data-analytics.md).

## <a name="database-management"></a>Adatbázis-kezelés
Az adatbázisok fogalmai az egyetemen bevezető tanfolyamok egyike. Beállíthat egy labort az alapszintű adatbázisok felügyeleti osztályához Azure Lab Servicesban. Beállíthat például egy virtuálisgép-sablont egy laborban egy [MySQL](https://www.mysql.com/) -adatbázis-kiszolgálóval vagy egy [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -kiszolgálóval.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a kapcsolódó adatbázisok adatbázis-kezelésének tanításához](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Mélytanulás a természetes nyelvfeldolgozásban
Beállíthat egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) formája, amely lehetővé teszi, hogy a számítógépek fordítással, beszédfelismeréssel és más nyelvi felismerési képességekkel rendelkeznek. Az NLP osztályba tartozó diákok linuxos virtuális gépet (VM) kapnak, amelyből megtudhatja, hogyan alkalmazhat neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mély tanulási modellek fejlesztéséhez.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a természetes nyelvi feldolgozás terén a Azure Lab Services használatával](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Etikus hackertevékenység
Beállíthat egy olyan osztályhoz tartozó labort, amely az etikai feltörések kriminalisztikai oldalára koncentrál. A behatolási teszt, amelyet az etikai hackelési Közösség használ, akkor következik be, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz a rosszindulatú támadó által kihasználható biztonsági rések bemutatására.

Egy etikai hackelési osztályban a tanulók modern technikákat tanulnak a biztonsági rések elleni védelemhez. Minden tanuló egy olyan Windows Server Host virtuális gépet kap, amely két beágyazott virtuális géppel rendelkezik – egy [Metasploitable3](https://github.com/rapid7/metasploitable3) -lemezképpel rendelkező virtuális géppel és egy másik, [Kali Linux](https://www.kali.org/) -lemezképpel rendelkező géppel. A Metasploitable virtuális gép felhasználási célokra szolgál.  A Kali Linux rendszerű virtuális gép hozzáférést biztosít a kriminalisztikai feladatok végrehajtásához szükséges eszközökhöz.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása az etikai Hacking osztály megtanításához](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
A mátrix-laboratóriumok számára készült [MATLAB](https://www.mathworks.com/products/matlab.html)a [MathWorks](https://www.mathworks.com/)programozási platformja.  A számítási teljesítményt és vizualizációt ötvözve a matematika, a mérnöki tudományok, a fizika és a kémia terén is népszerűvé teszi a számítástechnikai eszközöket.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a MATLAB tanításához](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Hálózatkezelés a GNS3
Beállíthat egy labort egy olyan osztályhoz, amely arra összpontosít, hogy a tanulók a virtuális és a valós hálózatok emulálása, konfigurálása, tesztelése és hibakeresése [GNS3](https://www.gns3.com/) szoftverrel. 

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkat a [tesztkörnyezet beállítása hálózati osztály megtanításához](class-type-networking-gns3.md)című témakörben talál.

## <a name="project-lead-the-way-pltw"></a>A projekt vezető módon (PLTW)
A [Project Lead the way (PLTW)](https://www.pltw.org/) egy nonprofit szervezet, amely PreK-12 tananyagot biztosít a számítógépes tudományok, a mérnöki tudományok és Egyesült Államok a biogyógyászat terén.  Minden PLTW osztályban a tanulók számos különböző alkalmazást használnak a gyakorlati tanulási élmény részeként.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkért lásd: [a laborok beállítása a projecthez, a módszer osztályai](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python-és Jupyter-jegyzetfüzetek
A [Jupyter-jegyzetfüzetek](http://jupyter-notebook.readthedocs.io)használatának megtanításához szükséges eszközökkel beállíthatja Azure Lab Servicesban a sablonhoz tartozó gépeket. A Jupyter notebookok egy nyílt forráskódú projekt, amely lehetővé teszi, hogy könnyedén kombinálja a Rich Text és a végrehajtható [Python](https://www.python.org/) -forráskódot egyetlen, jegyzetfüzet nevű vásznon. A jegyzetfüzetek futtatása a bemenetek és kimenetek lineáris rekordját eredményezi.  Ezek a kimenetek tartalmazhatnak szöveget, táblákat, valamint elszórt ábrákat és egyéb adatokat.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkat a [tesztkörnyezet beállítása az adatelemzéshez Python-és Jupyter-jegyzetfüzetekkel](class-type-jupyter-notebook.md)című témakörben talál.

## <a name="shell-scripting-on-linux"></a>Héjszkriptek használata Linuxon
Beállíthat egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához. A parancsfájlok hasznos részét képezik a rendszerfelügyeletnek, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a példában az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat tartalmazza. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek a bash-parancsokat és a Ruby-t ötvözik. Ez a módszer lehetővé teszi a Ruby számára, hogy a Shell használatával kommunikáljon a környékre és bash-parancsokra.

Az ilyen programozási osztályokat tartalmazó tanulók Linux rendszerű virtuális gépeket szereznek be a Linux alapjaihoz, és megismerhetik a bash rendszerhéj parancsfájljait is. A linuxos virtuális gépen engedélyezve van a távoli asztali hozzáférés, és telepítve van a [gedit](https://help.gnome.org/users/gedit/stable/) és a [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztő.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [rendszerhéj-parancsfájlok futtatása Linuxon](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks – számítógéppel segített tervezés (CAD)
Beállíthat egy GPU-labort, amely mérnöki tanulók számára biztosít hozzáférést a [solidworkshez](https://www.solidworks.com/).  A SolidWorks 3D CAD-környezetet biztosít a Solid Objects modellezéséhez.  A SolidWorks segítségével a mérnökök könnyedén létrehozhatják, megjeleníthetik, szimulálják és dokumentálják a terveiket.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása mérnöki osztályokhoz a SolidWorks használatával](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>SQL-adatbázis és-kezelés
A Structured Query Language (SQL) az adatbázis-kezelés szabványos nyelve, beleértve az adatbázisok tartalmának hozzáadását, elérését és kezelését.  Beállíthat egy labort, amely az adatbázis-fogalmakat a [MySQL](https://www.mysql.com/) adatbázis-kiszolgálóval és a [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -kiszolgálóval is tanítja.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a kapcsolódó adatbázisok adatbázis-kezelésének tanításához](class-type-database-management.md).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet létrehozása a természetes nyelvi feldolgozással kapcsolatos mély tanuláshoz Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Héjszkriptek használata Linuxon](class-type-shell-scripting-linux.md)
- [Etikus hackertevékenység](class-type-ethical-hacking.md)