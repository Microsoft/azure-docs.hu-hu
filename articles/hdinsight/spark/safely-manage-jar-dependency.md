---
title: Jar-függőségek biztonságos kezelése – Azure HDInsight
description: Ez a cikk a Java Archive (JAR) függőségeinek HDInsight-alkalmazásokhoz való kezelésének ajánlott eljárásait ismerteti.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942380"
---
# <a name="safely-manage-jar-dependencies"></a>Jar-függőségek biztonságos kezelése

A HDInsight-fürtökre telepített összetevők függőségekkel rendelkeznek a harmadik féltől származó tárakban. Általában a gyakori modulok (például a guava) egy adott verzióját a beépített összetevők hivatkoznak rá. Ha az alkalmazást a függőségeivel együtt küldi el, az ütközést eredményezhet ugyanazon modul különböző verziói között. Ha a osztályútvonal elsőként hivatkozott összetevő-verzió szerepel, a beépített összetevők kivételeket okozhatnak a verzió inkompatibilitása miatt. Ha azonban a beépített összetevők beinjektálják a függőségeiket a osztályútvonal, az alkalmazás hasonló hibákat eredményezhet `NoSuchMethod` .

A verziók ütközésének elkerülése érdekében érdemes megfontolni az alkalmazás függőségeinek árnyékolását.

## <a name="what-does-package-shading-mean"></a>Mit jelent a csomagok árnyékolása?
Az árnyékolás lehetővé teszi a függőségek belefoglalását és átnevezését. Visszakeresi az osztályokat, és átírja az érintett bytecode és erőforrásokat a függőségek privát másolatának létrehozásához.

## <a name="how-to-shade-a-package"></a>Hogyan lehet egy csomagot árnyékolni?

### <a name="use-uber-jar"></a>Az Über-jar használata
Az Über-jar egyetlen jar-fájl, amely az alkalmazás jar-t és annak függőségeit is tartalmazza. Az Über-jar függőségei alapértelmezés szerint nem árnyékoltak. Bizonyos esetekben ez a verziók ütközését eredményezheti, ha más összetevők vagy alkalmazások a könyvtárak egy másik verziójára hivatkoznak. Ennek elkerüléséhez létrehozhat egy Uber-Jar-fájlt a függőségek árnyékolt (vagy az összes) használatával.

### <a name="shade-package-using-maven"></a>Csomag árnyékolása a Maven használatával
A Maven a Java és a Scala nyelveken írt alkalmazásokat is felépítheti. A Maven-Shade-plugin segítségével egyszerűen hozhat létre árnyékolt über-jar-t.

Az alábbi példa egy olyan fájlt mutat be `pom.xml` , amely frissítve lett egy csomagnak a Maven-Shade-plugin használatával történő árnyékolására.  Az XML-szakasz a `<relocation>…</relocation>` csomagból csomagba helyezi az osztályokat a `com.google.guava` `com.google.shaded.guava` megfelelő JAR-fájlok bejegyzéseinek áthelyezésével és az érintett bytecode átírásával.

A módosítás után `pom.xml` végrehajthatja `mvn package` az árnyékolt über-jar kiépítését.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>SBT használatával árnyékolt csomag
A SBT egy, a Scala és a Java rendszerhez készült Build eszköz is. A SBT nem rendelkezik olyan árnyalatos beépülő modullal, mint a Maven-Shade-plugin. `build.sbt`A fájlt módosíthatja árnyalatos csomagokba. 

Például az `com.google.guava` alábbi parancs a `build.sbt` fájlhoz adható:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Ezután futtathatja és létrehozhatja `sbt clean` `sbt assembly` az árnyékolt jar-fájlt. 

## <a name="next-steps"></a>Következő lépések

* [HDInsight IntelliJ-eszközök használata](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Scala Maven-alkalmazás létrehozása a Sparkhoz a IntelliJ-ben](./apache-spark-create-standalone-application.md)