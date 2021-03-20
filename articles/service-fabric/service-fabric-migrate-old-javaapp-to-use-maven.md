---
title: Migrálás a Java SDK-ból a Mavenbe
description: Frissítse a még a Service Fabric Java SDK-t használó régebbi Java-alkalmazásokat, hogy a Service Fabric Java-függőségeiket a Mavenből kérjék le. A beállítás elvégzését követően a régebbi Java-alkalmazások fel tudnak majd épülni.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652296"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Korábbi Java Service Fabric-alkalmazások frissítése a Java-kódtárak a Mavenből történő lekérésére
Service Fabric Java-bináris fájlok átkerültek a Service Fabric Java SDK-ból a Maven-üzemeltetésbe. A **mavencentral** a legújabb Service Fabric Java-függőségek beolvasására használható. Ez az útmutató segítséget nyújt a Service Fabric Java SDK-hoz létrehozott meglévő Java-alkalmazások frissítéséhez a Yeoman sablon vagy az Eclipse használatával, hogy kompatibilis legyen a Maven-alapú buildtel.

## <a name="prerequisites"></a>Előfeltételek

1. Először távolítsa el a meglévő Java SDK-t.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Telepítse a legújabb Service Fabric parancssori felületet az [itt](service-fabric-cli.md) leírt lépések végrehajtásával.

3. A Service Fabric Java-alkalmazások létrehozásához és működéséhez győződjön meg arról, hogy a JDK 1,8 és a Gradle telepítve van. Ha a JDK 1.8 (openjdk-8-jdk) és a Gradle még nincsenek telepítve, a következő futtatásával telepítheti azokat –

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Az [itt](service-fabric-application-lifecycle-sfctl.md) leírt lépések végrehajtásával frissítse az alkalmazás telepítési/eltávolítási szkriptjeit, hogy azok az új Service Fabric parancssori felületet használják. Referenciaként tekintse meg az első lépéseket bemutató [példákat](https://github.com/Azure-Samples/service-fabric-java-getting-started).

>[!TIP]
> A Service Fabric Java SDK eltávolítása után a Yeoman nem fog működni. Az [itt](service-fabric-create-your-first-linux-application-with-java.md) leírt előfeltételeket követve helyezze üzembe a Service Fabric Yeoman Java-sablongenerátort.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-kódtárak a Mavenben

A Service Fabric Java-kódtárak a Mavenben üzemelnek. A függőségeket a projektek ``pom.xml`` vagy ``build.gradle`` fájljában adhatja hozzá a **mavenCentral** Service Fabric Java-kódtárainak használatához.

### <a name="actors"></a>Aktorok

A Service Fabric Reliable Actor támogatása az alkalmazáshoz.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Szolgáltatások

A Service Fabric állapotmentes szolgáltatás támogatása az alkalmazáshoz.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Egyéb

#### <a name="transport"></a>Átvitel

Az átviteli réteg támogatása a Service Fabric Java-alkalmazáshoz. Ezt a függőséget nem kell kifejezetten hozzáadnia a Reliable Actor- vagy Service-alkalmazásaihoz, hacsak a programozást nem az átviteli réteg szintjén végzi.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-támogatás

A natív Service Fabric-futtatókörnyezettel kommunikáló Service Fabric rendszerszintű támogatása. Ezt a függőséget nem kell kifejezetten hozzáadnia a Reliable Actor- vagy Service-alkalmazásaihoz. A rendszer automatikusan lekéri azt a Mavenből a fent említett további függőségek hozzáadásakor.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>A Service Fabric állapotmentes szolgáltatás migrálása

A meglévő Service Fabric állapotmentes Java-szolgáltatás a Mavenből lekért Service Fabric-függőségek használatával való létrehozásához frissítenie kell a ``build.gradle`` fájlt a szolgáltatásban. Korábban ez az alábbihoz hasonló volt –

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

Most azonban a függőségek a Mavenből való lekéréséhez a **frissített** ``build.gradle`` fájlban a vonatkozó részek a következőképp néznek ki –

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Általánosságban véve, ha szeretne átfogó képet kapni arról, hogy a felépítési szkriptek hogyan néznek ki egy Service Fabric állapotmentes Java-szolgáltatásban, tekintse meg az első lépéseket bemutató példáinkban szereplő bármelyik mintát. Itt van például a [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) az EchoServer-mintához.

## <a name="migrating-service-fabric-actor-service"></a>A Service Fabric aktorszolgáltatás migrálása

A meglévő Service Fabric-aktor Java-alkalmazás a Mavenből lekért Service Fabric-függőségek használatával való létrehozásához frissítenie kell a ``build.gradle`` fájlt az illesztőcsomagban és a Service csomagban. Ha TestClient csomaggal is rendelkezik, azt is frissítenie kell. Tehát például a ``Myactor`` aktor esetében az alábbi helyeken kell frissítést végeznie –

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Az illesztőprojekt felépítési szkriptjének frissítése

Korábban ez az alábbihoz hasonló volt –

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

Most azonban a függőségek a Mavenből való lekéréséhez a **frissített** ``build.gradle`` fájlban a vonatkozó részek a következőképp néznek ki –

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Az aktorprojekt felépítési szkriptjének frissítése

Korábban ez az alábbihoz hasonló volt –

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

Most azonban a függőségek a Mavenből való lekéréséhez a **frissített** ``build.gradle`` fájlban a vonatkozó részek a következőképp néznek ki –

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>A tesztügyfélprojekt felépítési szkriptjének frissítése

Ezek a módosítások hasonlóak az előző szakasz, azaz az aktorprojekt esetében leírt módosításokhoz. Korábban a Gradle-szkript az alábbihoz hasonló volt –

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

Most azonban a függőségek a Mavenből való lekéréséhez a **frissített** ``build.gradle`` fájlban a vonatkozó részek a következőképp néznek ki –

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Következő lépések

* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Service Fabric-fürtök használata a Service Fabric parancssori felületén](service-fabric-cli.md)
