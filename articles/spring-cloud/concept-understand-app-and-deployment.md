---
title: Alkalmazás és üzembe helyezés az Azure Spring Cloud-ban
description: Ez a témakör az alkalmazás és a telepítés közötti különbséget ismerteti az Azure Spring Cloud-ban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5473daedc8a7ad5a3b6ddffc65234160d4b3019d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878149"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>Alkalmazás és üzembe helyezés az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud erőforrás-modelljében az **alkalmazás** és a **telepítés** a két kulcsfontosságú fogalom. Az Azure Spring Cloud-ban az *alkalmazások* egy üzleti alkalmazás vagy egy szolgáltatás absztrakciója.  Egy *központi telepítésben* az *alkalmazás* futtatásakor telepített kód vagy bináris fájl egy verziója.  Az alkalmazások egy *Azure Spring Cloud Service-példányban*, vagy egyszerűen a *Service-példányban* futnak, ahogy azt a következő mutatja.

 ![Alkalmazások és központi telepítések](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Egyetlen Azure-előfizetésen belül több szolgáltatási példány is lehet, de az Azure Spring Cloud szolgáltatás a legegyszerűbben akkor használható, ha az üzleti alkalmazást vagy a szolgáltatást alkotó összes alkalmazás egyetlen szolgáltatási példányban található.

Az Azure Spring Cloud standard csomag lehetővé teszi, hogy egy alkalmazás egy éles üzembe helyezést és egy átmeneti üzembe helyezést biztosítson, így a kék/zöld üzembe helyezés egyszerűen elvégezhető.

## <a name="app"></a>Alkalmazás
Az alkalmazás szintjén az alábbi funkciók/tulajdonságok vannak meghatározva.

| Funkciók | Leírás |
|:--|:----------------|
| Nyilvános</br>Végpont | Az alkalmazás eléréséhez használt URL-cím |
| Egyéni</br>Tartomány | Az egyéni tartomány védelmét biztosító CNAME-rekord |
| Szolgáltatás</br>Kötés | Egyéb Azure-szolgáltatásokkal létesített kapcsolatok |
| Felügyelt</br>Identitás | A felügyelt identitás Azure Active Directory lehetővé teszi, hogy az alkalmazás könnyedén hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vault |
| Állandó</br>Tárolás | Beállítás, amely lehetővé teszi, hogy az alkalmazások az alkalmazás újraindítása után is megmaradjanak |

## <a name="deployment"></a>Üzembe helyezés

A következő funkciók/tulajdonságok a telepítési szinten vannak meghatározva, és az üzemi/átmeneti telepítés cseréjekor lesznek kicserélve.

| Funkciók | Leírás |
|:--|:----------------|
| CPU | Virtuális mag-példányok száma |
| Memória | GB memória/alkalmazás példánya|
| Példány</br>Darabszám | Az alkalmazás példányainak száma, manuális vagy automatikus beállítás |
| Automatikus méretezés | A példányok számának automatikus méretezése előre definiált szabályok és ütemtervek alapján |
| JVM</br>Beállítások | JVM beállítások megadása  |
| Környezet</br>Változók | Környezeti változók beállítása |
| Futtatókörnyezet</br>Verzió | Java 8/Java 11|

## <a name="restrictions"></a>Korlátozások

* Az **alkalmazásnak rendelkeznie kell egy éles üzembe helyezéssel**: az üzemi környezet törlését az API blokkolja. A törlés előtt ki kell cserélni az átmeneti állapotba.
* **Egy alkalmazás legfeljebb két üzemelő példányban lehet**: a kettőnél több üzemelő példány létrehozása le van tiltva az API-ban. Telepítse az új bináris fájlt a meglévő éles környezetbe vagy átmeneti üzembe helyezésre.
* Az **üzembe helyezési felügyelet alapszintű szinten nem érhető el**: a standard szintű csomag használata Blue-Green üzembe helyezési lehetőséghez.

## <a name="see-also"></a>Lásd még
* [Átmeneti környezet beállítása az Azure Spring Cloud-ban](spring-cloud-howto-staging-environment.md)
