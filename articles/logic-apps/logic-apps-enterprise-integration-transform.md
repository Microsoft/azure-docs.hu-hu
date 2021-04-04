---
title: XML átalakítása formátumok között
description: Olyan átalakításokat vagy térképeket hozhat létre, amelyek az XML formátumot Azure Logic Apps formátumai között Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654132"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Leképezések létrehozása, amelyek az XML-t különböző formátumokká alakítják át az Azure Logic Appsben az Enterprise Integration Packkel

A vállalati integráció Átalakítás összekötője egy adott formátumból más formátumba konvertálja az adatokat. Előfordulhat például, hogy van egy bejövő üzenete, amely az aktuális dátumot tartalmazza az ÉvHónapNap formátumban. A dátumot átalakítással formázhatja át a HónapNapÉv formátumra.

## <a name="what-does-a-transform-do"></a>Mit tesz az átalakítás?
A térképként is ismert átalakítás egy forrás XML-sémából (a bemenetből) és egy célként megadott XML-sémából (a kimenetből) áll. A különböző beépített függvények segítségével kezelheti vagy szabályozhatja az adatok kezelését, beleértve a karakterlánc-manipulációkat, a feltételes hozzárendeléseket, a aritmetikai kifejezéseket, a dátum-és időformázó szerkezeteket, valamint a hurkokat is.

## <a name="how-to-create-a-transform"></a>Átalakítás létrehozása
A Visual Studio [Vállalati integráció SDK](https://aka.ms/vsmapsandschemas)használatával létrehozhat egy átalakítást/leképezést. Ha befejezte az átalakítás létrehozását és tesztelését, töltse fel az átalakítást az integrációs fiókjába. 

## <a name="how-to-use-a-transform"></a>Átalakítás használata
Miután feltöltötte az átalakítást/leképezést az integrációs fiókjába, felhasználhatja egy logikai alkalmazás létrehozásához. A logikai alkalmazás a logikai alkalmazás indításakor futtatja az átalakításokat (és van olyan bemeneti tartalom, amelyet át kell alakítani).

A **következő lépésekkel végezheti el a transzformáció használatát**:

### <a name="prerequisites"></a>Előfeltételek

* Integrációs fiók létrehozása és leképezés hozzáadása  

Most, hogy elvégezte az előfeltételeket, ideje létrehozni a logikai alkalmazást:  

1. Hozzon létre egy logikai alkalmazást, és [csatolja azt a térképet tartalmazó integrációs fiókhoz](./logic-apps-enterprise-integration-create-integration-account.md "Integrációs fiók összekapcsolása logikai alkalmazással") .
2. **Kérelem** -trigger hozzáadása a logikai alkalmazáshoz  
   ![Képernyőkép a "Microsoft felügyelt API-k megjelenítése" legördülő listáról a kérelem trigger kiválasztásával. A legördülő lista a Visual Studio Vállalati integráció SDK használatával létrehozott logikai alkalmazásban található.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Az **átalakítási XML-** művelet hozzáadásához először válassza a **művelet hozzáadása** lehetőséget.   
   ![Képernyőfelvétel: a kérelem trigger képernyőjén kiválasztott "művelet hozzáadása" gomb.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. A keresőmezőbe írja be a Word *átalakítás* kifejezést a használni kívánt összes művelet szűréséhez  
   ![Képernyőfelvétel: az átalakítási XML-művelet megkeresése a "Microsoft felügyelt API-k megjelenítése" legördülő menüben, hogy hozzá lehessen adni a kérelem-triggerhez.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. **XML-átalakítási** művelet kiválasztása   
6. Adja hozzá az átalakított XML- **tartalmat** . A HTTP-kérelemben a **tartalomként** kapott XML-adatokat is használhatja. Ebben a példában válassza ki a logikai alkalmazást indító HTTP-kérelem törzsét.

   > [!NOTE]
   > Győződjön meg arról, hogy az **átalakítás XML** -fájljának tartalma XML. Ha a tartalom nem XML vagy Base64 kódolású, akkor meg kell adnia egy kifejezést, amely feldolgozza a tartalmat. Használhatja például a [függvények](logic-apps-workflow-definition-language.md#functions)használatát, például ```@base64ToBinary``` a tartalom dekódolásához vagy ```@xml``` a tartalom XML-ként történő feldolgozásához.
 

7. Válassza ki az átalakítás végrehajtásához használni kívánt **Térkép** nevét. A térképnek már az integrációs fiókjában kell lennie. Egy korábbi lépésben már megadta a logikai alkalmazás hozzáférését a térképet tartalmazó integrációs fiókhoz.      
   ![Képernyőfelvétel: az átalakítási XML-képernyő tartalmának és leképezési mezőinek megjelenítése a kérelem-triggerhez.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Munkahelyi mentés  
    ![A Logic Apps Designer mentés gombjára mutató képernyőkép.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Ezen a ponton elkészült a Térkép beállítása. Egy valós alkalmazásban érdemes lehet egy LOB-alkalmazásban (például SalesForce) tárolni az átalakított adatfájlokat. Az átalakítás kimenetét egyszerűen elküldheti Salesforce. 

Mostantól a HTTP-végpontra irányuló kéréssel tesztelheti az átalakítást.  


## <a name="features-and-use-cases"></a>Funkciók és használati esetek
* A térképen létrehozott átalakítás egyszerűen elvégezhető, például egy név és egy dokumentum másolása egy másikba. Emellett összetettebb átalakításokat is létrehozhat a beépített leképezési műveletek használatával.  
* Több térképes művelet vagy függvény azonnal elérhető, beleértve a karakterláncokat, a dátum-és időfüggvényeket stb.  
* A sémák között közvetlen Adatmásolást is végezhet. Az SDK-ban található Mapper esetében ez olyan egyszerű, mintha egy olyan vonalat rajzoljon, amely összekapcsolja a forrás sémájának elemeit a célként megadott sémában lévő társaikkal.  
* Térkép létrehozásakor megtekintheti a Térkép grafikus ábrázolását, amely megjeleníti a létrehozott kapcsolatokat és hivatkozásokat.
* Egy minta XML-üzenet hozzáadásához használja a test Map funkciót. Egy egyszerű kattintással tesztelheti a létrehozott térképet, és megtekintheti a generált kimenetet.  
* Meglévő térképek feltöltése  
* Az XML-formátum támogatását tartalmazza.

## <a name="advanced-features"></a>Speciális funkciók

### <a name="reference-assembly-or-custom-code-from-maps"></a>Hivatkozás vagy egyéni kód a mapsből 
Az átalakítási művelet a leképezéseket és az átalakításokat is támogatja a külső szerelvényre való hivatkozással. Ez a funkció lehetővé teszi, hogy közvetlenül az XSLT-térképekről hívja meg az egyéni .NET-kódot. Itt láthatók a szerelvények Maps-ben való használatának előfeltételei.

* A térképre hivatkozott térképet és szerelvényt az [integrációs fiókba kell feltölteni](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > A leképezést és a szerelvényt egy adott sorrendben kell feltölteni. A szerelvényre hivatkozó Térkép feltöltése előtt fel kell töltenie a szerelvényt.

* A térképnek ezeket az attribútumokat és egy CDATA szakaszt is tartalmaznia kell, amely tartalmazza a szerelvény kódjának hívását:

    * a **név** az egyéni szerelvény neve.
    * a **névtér** a szerelvényben található névtér, amely tartalmazza az egyéni kódot.

  Ez a példa egy olyan Térképet mutat be, amely egy "XslUtilitiesLib" nevű szerelvényre hivatkozik, és a metódust hívja meg a `circumreference` szerelvényből.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Byte Order Mark
Alapértelmezés szerint az átalakítás válasza a byte Order Mark (BOM) karakterrel kezdődik. Ezt a funkciót csak a Code View Editor használata során érheti el. A funkció letiltásához `disableByteOrderMark` a következő tulajdonságot kell megadnia `transformOptions` :

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Tudjon meg többet
* [További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack")  
* [További információ a mapsről](../logic-apps/logic-apps-enterprise-integration-maps.md "További tudnivalók a vállalati integrációs térképekről")  