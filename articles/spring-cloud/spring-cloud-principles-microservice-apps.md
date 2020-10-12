---
title: Java és Base operációs rendszer Azure Spring Cloud Service-alkalmazásokhoz
description: Alapelvek az Azure Spring Cloud Service-alkalmazások kifogástalan Java-és alapszintű operációs rendszerének fenntartásához
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8cda46a011ae92f26a15a4e9a918559801ce299c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906865"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java és alap operációs rendszer a Spring mikroszolgáltatás-alapú alkalmazásokhoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Az alábbi alapelvek az egészséges Java és az alap operációs rendszer fenntartásához szükséges alapelveket a Spring Service-alkalmazásokhoz.
## <a name="principles-for-healthy-java-and-base-os"></a>Alapszintű Java és Base operációs rendszer alapelvei
* Azonos alapszintű operációs rendszernek kell lennie a rétegek között – alapszintű | Standard | Prémium szintű.
    * Az Azure Spring Cloud-alkalmazások jelenleg a Debian 10 és az Ubuntu 18,04 kombinációját használják.
    * A VMware Build szolgáltatás Ubuntu 18,04-et használ.
* Ugyanaz az alapszintű operációs rendszernek kell lennie, függetlenül a telepítés kiindulási pontjaitól – forrás | JAR
    * Az Azure Spring Cloud-alkalmazások jelenleg a Debian 10 és az Ubuntu 18,04 kombinációját használják.
* Az alapszintű operációs rendszernek szabad biztonsági réseket biztosítania.
    * A Debian 10 alapszintű operációs rendszer 147 nyitott CVEs rendelkezik.
    * Az Ubuntu 18,04 Base operációs rendszer 132 nyitott CVEs rendelkezik.
* Az a JRE-fej nélküli használatot használja.
    * Jelenleg az Azure Spring Cloud-alkalmazások a JDK-t használják. A JRE-fej egy kisebb rendszerkép.
* A a Java legújabb buildjét használja.
    * Az Azure Spring Cloud-alkalmazások jelenleg a Java 8 build 242-et használják. Ez egy elavult Build.
 
A Azul Systems folyamatosan ellenőrzi az alapoperációs rendszerek változásait, és naprakészen tartja az utolsó beépített rendszerképeket. Az Azure Spring Cloud megkeresi a lemezképek változásait, és folyamatosan frissíti őket az üzemelő példányok között.
 
## <a name="faq-for-azure-spring-cloud"></a>Az Azure Spring Cloudra vonatkozó gyakori kérdések

* A Java mely verziói támogatottak? Főverzió és létrehozási szám.
    * Az LTS-verziók támogatása – Java 8 és 11.
    * A legújabb buildet használja – például most, Java 8 build 252 és Java 11 Build 7.
* Kik építették ezeket a Java-futtatókörnyezeteket?
    * Azul rendszerek.
* Mi a lemezképek alap operációs rendszere?
    * Ubuntu 20,04 LTS (gyújtóponti Fossa). Az alkalmazások továbbra is az Ubuntu legújabb LTS-verziójában maradnak.
    * Lásd: [Ubuntu 20,04 LTS (gyújtóponti Fossa)](http://releases.ubuntu.com/focal/)
* Hogyan tölthetők le egy támogatott Java-futtatókörnyezet a helyi fejlesztési környezethez? 
    * Lásd: [Az Azure-hoz készült JDK telepítése és Azure stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Hogyan Kérhetek támogatást a Java futtatókörnyezeti szinten felmerülő problémákhoz?
    * Nyisson meg egy támogatási jegyet az Azure-támogatással.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Alapértelmezett üzembe helyezés az Azure Spring Cloud-on

> ![Alapértelmezett központi telepítés](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)
* [Java hosszú távú támogatás az Azure-hoz és Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
