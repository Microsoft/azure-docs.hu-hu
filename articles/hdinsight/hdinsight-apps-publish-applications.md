---
title: Azure HDInsight-alkalmazások közzététele
description: Ismerje meg, hogyan hozhat létre HDInsight-alkalmazásokat, és hogyan teheti közzé azt az Azure Marketplace-en.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: 73d291fd76d9adbe2a095406b56bd879d497d3b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942664"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>HDInsight-alkalmazás közzététele az Azure Marketplace-en
Egy Azure HDInsight-alkalmazást Linux-alapú HDInsight-fürtön is telepíthet. Ebből a cikkből megtudhatja, hogyan tehet közzé egy HDInsight-alkalmazást az Azure Marketplace-en. Az Azure Marketplace-en való közzétételsel kapcsolatos általános információkért tekintse meg az [ajánlat közzététele az Azure piactéren](../marketplace/overview.md)című témakört.

A HDInsight-alkalmazások a *saját licenc használata (BYOL)* modellt használják. Egy BYOL-forgatókönyvben az alkalmazás szolgáltatója felelős az alkalmazások alkalmazás-felhasználói számára történő licencelésért. Az alkalmazás felhasználói csak az általuk létrehozott Azure-erőforrásokért, például a HDInsight-fürtön, valamint a fürt virtuális gépein és csomópontjain lesznek felszámítva. Jelenleg az alkalmazás számlázása nem történik meg az Azure-ban.

További információkért tekintse meg az alábbi HDInsight-alkalmazásokkal kapcsolatos cikkeket:

* [Telepítse a HDInsight-alkalmazásokat](hdinsight-apps-install-applications.md). Ismerje meg, hogyan telepíthet HDInsight-alkalmazásokat a fürtökön.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md). Ismerje meg, hogyan telepítheti és tesztelheti az egyéni HDInsight-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek
Ha egyéni alkalmazást szeretne elküldeni a piactéren, először [hozzon létre és tesztelje egyéni alkalmazását](hdinsight-apps-install-custom-applications.md).

Regisztrálnia kell a fejlesztői fiókját is. További információkért lásd: [ajánlat közzététele az Azure piactéren](../marketplace/overview.md) , és [hozzon létre egy Microsoft fejlesztői fiókot](../marketplace/overview.md).

## <a name="define-the-application"></a>Az alkalmazás megadása
Két lépés vesz részt az alkalmazások piactéren való közzétételében. Először Definiáljon egy *createUiDef.jst a* fájlhoz. A createUiDef.jsfájl azt jelzi, hogy az alkalmazás mely fürtökkel kompatibilis. Ezután tegye közzé a sablont a Azure Portal. Íme egy minta createUiDef.jsa következő fájlon:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Mező | Leírás | Lehetséges értékek |
| --- | --- | --- |
| types |Azok a fürttípusok, amelyekkel az alkalmazás kompatibilis. |Hadoop, HBase, Storm, Spark (vagy ezek bármely kombinációja) |
| versions |Azok a HDInsight-fürttípusok, amelyekkel az alkalmazás kompatibilis. |3.4 |

## <a name="application-installation-script"></a>Alkalmazás telepítési parancsfájlja
Ha egy alkalmazás egy fürtre van telepítve (vagy egy meglévő fürtön vagy egy másikon), a rendszer egy peremhálózati csomópontot hoz létre. Az alkalmazás telepítési parancsfájlja a peremhálózati csomóponton fut.

  > [!IMPORTANT]  
  > Az alkalmazás telepítési parancsfájljának egyedinek kell lennie egy adott fürthöz. A parancsfájl nevének a következő formátumúnak kell lennie:
  > 
  > "Name": "[concat (' Hue-install-v0 ', '-', uniquestring (' applicationName ')])
  > 
  > A parancsfájl neve három részből áll:
  > 
  > * A parancsfájl nevének előtagja, amelynek tartalmaznia kell az alkalmazás nevét vagy az alkalmazáshoz kapcsolódó nevet.
  > * Kötőjel, az olvashatóság érdekében.
  > * Egy egyedi karakterlánc-függvény, amely az alkalmazás nevét adja meg paraméterként.
  > 
  > A megőrzött parancsfájl-művelet listán az előző példa **Hue-install-v0-4wkahss55hlas** jelenik meg. Tekintse meg a [JSON-adattartalom mintáját](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

A telepítési parancsfájlnak a következő jellemzőkkel kell rendelkeznie:
* A szkript idempotens. A parancsfájlnak több hívása is ugyanazt az eredményt eredményezi.
* A parancsfájl megfelelően van verziója. A változtatások frissítésekor vagy tesztelésekor használjon másik helyet a parancsfájlhoz. Ez biztosítja, hogy az alkalmazást telepítő ügyfeleknek ne befolyásolják a frissítések vagy a tesztelés. 
* A parancsfájlnak minden ponton megfelelő naplózással kell rendelkezniük. A parancsfájl-naplók általában az alkalmazás-telepítési problémák hibakeresésének egyetlen módja.
* A külső szolgáltatásokra vagy erőforrásokra irányuló hívások megfelelő újrapróbálkozásokkal rendelkeznek, hogy a telepítés ne legyen hatással az átmeneti hálózati problémákra.
* Ha a parancsfájl elindítja a szolgáltatásokat a csomópontokon, a szolgáltatások figyelése és konfigurálása automatikusan elindul, ha a csomópont újraindítása történik.

## <a name="package-the-application"></a>Az alkalmazás becsomagolása
Hozzon létre egy. zip fájlt, amely tartalmazza a HDInsight-alkalmazás telepítéséhez szükséges összes fájlt. Az alkalmazás közzétételéhez használja a. zip fájlt. A. zip fájl a következő fájlokat tartalmazza:

* createUiDefinition.jsbekapcsolva
* mainTemplate.json (a minta esetében lásd: [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).)
* Az összes szükséges parancsfájl

> [!NOTE]  
> Az alkalmazásfájlok (beleértve a webalkalmazás-fájlokat is) bármely nyilvánosan elérhető végponton tárolhatók.

## <a name="publish-the-application"></a>Az alkalmazás közzététele
HDInsight-alkalmazás közzététele:

1. Jelentkezzen be az [Azure Publishing](https://publish.windowsazure.com/)szolgáltatásba.
2. A bal oldali menüben válassza a **megoldás sablonok** lehetőséget.
3. Adjon meg egy címet, majd válassza **az új megoldás sablon létrehozása** lehetőséget.
4. Ha még nem regisztrálta a szervezetét, válassza **a Fejlesztői központ fiók létrehozása lehetőséget, és csatlakozzon az Azure-programhoz**.  További információt a [Microsoft fejlesztői fiók létrehozása](../marketplace/overview.md)című témakörben talál.
5. **A kezdéshez válassza az egyes topológiák megadása** lehetőséget. A megoldási sablon a "Parent" az összes topológiája számára. Egy ajánlat vagy megoldás sablonjában több topológiát is meghatározhat. Ha egy ajánlat átmeneti állapotba kerül, a rendszer minden topológiával leküldi. 
6. Adja meg a topológia nevét, majd válassza a elemet **+** .
7. Adjon meg egy új verziót, majd válassza a elemet **+** .
8. Töltse fel az alkalmazás csomagolásakor létrehozott. zip fájlt.  
9. Válassza a **tanúsítvány kérése** lehetőséget. A Microsoft minősítési csapata áttekinti a fájlokat, és tanúsítja a topológiát.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [telepíthet HDInsight-alkalmazásokat](hdinsight-apps-install-applications.md) a fürtökbe.
* Ismerje meg, hogyan [telepíthet egyéni HDInsight-alkalmazásokat](hdinsight-apps-install-custom-applications.md) , és hogyan helyezhet üzembe egy közzé nem tett HDInsight alkalmazást a HDInsight.
* Megtudhatja, hogyan [használható a parancsfájlok művelet a Linux-alapú HDInsight-fürtök testreszabásához](hdinsight-hadoop-customize-cluster-linux.md) és további alkalmazások hozzáadásához. 
* Megtudhatja, hogyan [hozhat létre Linux-alapú Apache Hadoop-fürtöket a HDInsight-ben Azure Resource Manager sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Megtudhatja, hogyan [használhatja a HDInsight egy üres peremhálózati csomópontot](hdinsight-apps-use-edge-node.md) a HDInsight-fürtök eléréséhez, a HDInsight-alkalmazások teszteléséhez és a gazdagép HDInsight-alkalmazásaihoz.