---
title: A konfigurációs kiszolgáló példányának beállítása az Azure Spring Cloud-ban
description: Megtudhatja, hogyan állíthat be egy Spring Cloud config Server-példányt az Azure Spring Cloud Azure Portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 52b3d902b2cbfdacfe92117dcf0057dab1fe9a83
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012209"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Egy Spring Cloud config Server-példány beállítása a szolgáltatáshoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez a cikk bemutatja, hogyan csatlakozhat egy Spring Cloud config Server-példányt az Azure Spring Cloud Service-hez.

A Spring Cloud config kiszolgáló-és ügyféloldali támogatást biztosít az elosztott rendszerekben található külső konfigurációhoz. A konfigurációs kiszolgáló példányával központi helyen kezelheti az alkalmazások külső tulajdonságait az összes környezetben. További információ: [Spring Cloud config Server Reference](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Már üzembe lett helyezve, és futtatja az Azure Spring Cloud Service-t. Azure Spring Cloud-szolgáltatás beállításához és elindításához lásd [: gyors útmutató: Java Spring-alkalmazás elindítása az Azure CLI használatával](spring-cloud-quickstart.md).

## <a name="restriction"></a>Korlátozás

A konfigurációs kiszolgáló és a git háttérrendszer használatával bizonyos korlátozások vonatkoznak. Bizonyos tulajdonságok automatikusan bekerülnek az alkalmazási környezetbe a konfigurációs kiszolgáló és a szolgáltatás felderítésének eléréséhez. Ha ezeket a tulajdonságokat a konfigurációs kiszolgáló fájljaiból is konfigurálja, az ütközéseket és a váratlan viselkedést tapasztalhatja. A tulajdonságok a következők: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> Azt javasoljuk, hogy ne _Helyezze el_ a fenti tulajdonságokat a konfigurációs kiszolgáló alkalmazás fájljaiban.

## <a name="create-your-config-server-files"></a>A konfigurációs kiszolgáló fájljainak létrehozása

Az Azure Spring Cloud támogatja az Azure DevOps, a GitHub, a GitLab és a bitbucket a konfigurációs kiszolgáló fájljainak tárolásához. Ha a tárháza elkészült, hozza létre a konfigurációs fájlokat az alábbi utasításokkal, és tárolja őket.

Emellett néhány konfigurálható tulajdonság csak bizonyos típusokhoz érhető el. A következő alszakaszok felsorolják az egyes adattár-típusok tulajdonságait.

### <a name="public-repository"></a>Nyilvános tárház

Ha nyilvános tárházat használ, a konfigurálható tulajdonságok korlátozottabbak.

A nyilvános git-tárház beállításához használt összes konfigurálható tulajdonság a következő táblázatban látható:

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használhatja például az *alapértelmezett címkét*, de nem *defaultLabel*.

| Tulajdonság        | Kötelező | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | A konfigurációs kiszolgálóként használt git-tárház URI-ja a *http://*, a *https://*, a *git@* és a *SSH://* karakterrel kezdődik. |
| `default-label` | No     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve* vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`  | No     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privát adattár SSH-hitelesítéssel

A privát git-tárház SSH-val történő beállításához használt összes konfigurálható tulajdonság a következő táblázatban látható:

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használhatja például az *alapértelmezett címkét*, de nem *defaultLabel*.

| Tulajdonság                   | Kötelező | Szolgáltatás                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Yes    | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT *http://*, *https://*, *git@* vagy *SSH://* kell elindítani. |
| `default-label`            | No     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve* vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`             | No     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `private-key`              | No     | A git-tárház eléréséhez _szükséges_ SSH titkos kulcs, ha az URI *git@* vagy *SSH://*-vel kezdődik. |
| `host-key`                 | No     | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a által lefedett algoritmus-előtagot `host-key-algorithm` . |
| `host-key-algorithm`       | No     | A host key algoritmusnak *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384* vagy *ECDSA-SHA2-nistp521* értékűnek kell lennie. Csak akkor *szükséges* , ha `host-key` létezik. |
| `strict-host-key-checking` | No     | Azt jelzi, hogy a konfigurációs kiszolgáló példánya nem fog-e elindulni a magánjellegű kihasználása során `host-key` . *Igaznak* kell lennie (alapértelmezett érték) vagy *false (hamis*). |

> [!NOTE]
> `master`Ha nincs megadva, a konfigurációs kiszolgáló (például az om git) alapértelmezett címkeként fog megjelenni. A GitHub azonban a közelmúltban módosította az alapértelmezett ágat `master` `main` . Az Azure Spring Cloud config Server meghibásodásának elkerülése érdekében figyeljen az alapértelmezett címkére, ha a konfigurációs kiszolgálót a GitHub használatával állítja be, különösen az új létrehozott Tárházak esetében.

-----

### <a name="private-repository-with-basic-authentication"></a>Egyéni adattár alapszintű hitelesítéssel

Az alapszintű hitelesítéssel rendelkező privát git-tárház beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használja például az *alapértelmezett-címkét*, ne *defaultLabel*.

| Tulajdonság        | Kötelező | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT a *http://*, a *https://*, a *git@* vagy a *SSH://* kell elindítani. |
| `default-label` | No     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve* vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`  | No     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `username`      | No     | A git-tárház kiszolgálójának eléréséhez használt Felhasználónév, amely a git-tárház kiszolgálójának támogatásához _szükséges_ `Http Basic Authentication` . |
| `password`      | No     | A git-tárház kiszolgálójának eléréséhez használt jelszó, amely a git-tárház kiszolgálójának támogatásához _szükséges_ `Http Basic Authentication` . |

> [!NOTE]
> Számos `Git` tárház-kiszolgáló támogatja a tokenek használatát a http alapszintű hitelesítéshez használt jelszavak helyett. Egyes Tárházak, például a GitHub, lehetővé teszik a tokenek határozatlan ideig való megőrzését. Bizonyos git-tárház-kiszolgálók, például az Azure DevOps, néhány óra múlva lejárnak. A jogkivonatok lejáratát okozó adattárak nem használhatnak jogkivonat-alapú hitelesítést az Azure Spring Cloud használatával.

### <a name="git-repositories-with-pattern"></a>Git-adattárak mintázattal

Az alábbi listában a git-adattárak beállításához használt összes konfigurálható tulajdonság szerepel.

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használja például az *alapértelmezett-címkét*, ne *defaultLabel*.

| Tulajdonság                           | Kötelező         | Szolgáltatás                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | No             | Egy adott névvel rendelkező git-tárház beállításait tartalmazó Térkép. |
| `repos."uri"`                      | Igen bekapcsolva `repos` | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT a *http://*, a *https://*, a *git@* vagy a *SSH://* kell elindítani. |
| `repos."name"`                     | Igen bekapcsolva `repos` | A git-tárházon azonosítható név, amely csak akkor _szükséges_ , ha `repos` létezik. Például: *Team-A*, *Team-B*. |
| `repos."pattern"`                  | No             | Az alkalmazás nevének megfeleltetéséhez használt karakterláncok tömbje. Minden mintához használja a `{application}/{profile}` formátumot helyettesítő karakterekkel. |
| `repos."default-label"`            | No             | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve* vagy a tárház *véglegesítő azonosítója* lehet. |
| `repos."search-paths`"             | No             | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `repos."username"`                 | No             | A git-tárház kiszolgálójának eléréséhez használt Felhasználónév, amely a git-tárház kiszolgálójának támogatásához _szükséges_ `Http Basic Authentication` . |
| `repos."password"`                 | No             | A git-tárház kiszolgálójának eléréséhez használt jelszó, amely a git-tárház kiszolgálójának támogatásához _szükséges_ `Http Basic Authentication` . |
| `repos."private-key"`              | No             | A git-tárház eléréséhez _szükséges_ SSH titkos kulcs, ha az URI *git@* vagy *SSH://*-vel kezdődik. |
| `repos."host-key"`                 | No             | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a által lefedett algoritmus-előtagot `host-key-algorithm` . |
| `repos."host-key-algorithm"`       | No             | A host key algoritmusnak *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384* vagy *ECDSA-SHA2-nistp521* értékűnek kell lennie. Csak akkor *szükséges* , ha `host-key` létezik. |
| `repos."strict-host-key-checking"` | No             | Azt jelzi, hogy a konfigurációs kiszolgáló példánya nem fog-e elindulni a magánjellegű kihasználása során `host-key` . *Igaznak* kell lennie (alapértelmezett érték) vagy *false (hamis*). |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>A konfigurációs kiszolgáló tárházának csatlakoztatása az Azure Spring Cloud-hoz

Most, hogy a konfigurációs fájlokat egy adattárba menti, hozzá kell csatlakoznia az Azure Spring Cloud-hoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.

3. A bal oldali navigációs panelen válassza a **konfigurációs kiszolgáló** elemet.

4. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** -t "" értékre https://github.com/Azure-Samples/piggymetrics-config .

5. Kattintson az **Érvényesítés** elemre.

    ![Navigáljon a konfigurációs kiszolgálóhoz](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. Ha az ellenőrzés befejeződött, kattintson az **alkalmaz** gombra a módosítások mentéséhez.

    ![A konfigurációs kiszolgáló érvényesítése](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. A konfiguráció frissítése néhány percet is igénybe vehet.
 
    ![Konfigurációs kiszolgáló frissítése](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

8. A konfigurálás befejeződése után értesítést kell kapnia.

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Adja meg a tárház adatait közvetlenül a Azure Portal

#### <a name="default-repository"></a>Alapértelmezett adattár

* **Nyilvános adattár**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját.  Állítsa be a **címkét** a **konfigurációra**. Győződjön meg arról, hogy a **hitelesítési** beállítás **nyilvános**, majd válassza az **alkalmaz** a befejezésre lehetőséget. 

* **Privát tárház**: az Azure Spring Cloud támogatja az alapszintű jelszó/jogkivonat-alapú hitelesítést és az SSH-t.

    * **Egyszerű hitelesítés**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját, majd válassza a **hitelesítés** ("ceruza" ikon) gombot. A **hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza a **http alapszintű** lehetőséget, majd adja meg felhasználónevét és jelszavát/jogkivonatát az Azure Spring Cloud elérésének biztosításához. Kattintson **az OK gombra**, majd válassza az **alkalmaz** lehetőséget a konfigurációs kiszolgálópéldány beállításának befejezéséhez.

    ![A hitelesítési panel alapszintű hitelesítésének szerkesztése](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Egyes git-tárház-kiszolgálók, például a GitHub, *személyes jogkivonatot* vagy *hozzáférési tokent*(például jelszót) használnak az **alapszintű hitelesítéshez**. Ezt a tokent használhatja jelszóként az Azure Spring Cloud-ban, mert soha nem jár le. Más git-tárház-kiszolgálók esetében, például a bitbucket és az Azure DevOps esetében a *hozzáférési token* egy vagy két órán belül lejár. Ez azt jelenti, hogy a beállítás nem életképes, ha ezeket az adattár-kiszolgálókat használja az Azure Spring Cloud használatával.

    * **SSH**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját, majd válassza a **hitelesítés** ("ceruza" ikon) gombot. A **hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza az **SSH** lehetőséget, majd adja meg a **titkos kulcsot**. Szükség esetén megadhatja a **gazdagép kulcsát** és a **gazdagép kulcsának algoritmusát** is. Ügyeljen arra, hogy a nyilvános kulcsot a konfigurációs kiszolgáló adattárában is tartalmazza. Kattintson **az OK gombra**, majd válassza az **alkalmaz** lehetőséget a konfigurációs kiszolgálópéldány beállításának befejezéséhez.

    ![A hitelesítési ablaktábla SSH-hitelesítésének szerkesztése](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Minta adattár

Ha a szolgáltatás konfigurálásához egy opcionális **minta-tárházat** szeretne használni, adja meg az **URI** -t és a **hitelesítést** ugyanúgy, mint az **alapértelmezett tárházat**. Ügyeljen arra, hogy tartalmazza a minta **nevét** , majd kattintson az **alkalmaz** gombra a példányhoz való csatlakoztatáshoz. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Adja meg a tárház adatait egy YAML-fájlba

Ha YAML-fájlt írt az adattár beállításaival, a fájlt közvetlenül a helyi gépről importálhatja az Azure Spring Cloud-ba. Az alapszintű hitelesítéssel rendelkező privát tárház egyszerű YAML-fájlja a következőképpen fog kinézni:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Válassza a **Beállítások importálása** gombot, majd válassza ki a YAML fájlt a projekt könyvtárából. Válassza az **Importálás** lehetőséget, majd az `async` **értesítések** közül egy művelet jelenik meg. 1-2 perc elteltével a sikeres jelentésnek kell lennie.

![A konfigurációs kiszolgáló értesítései panel](media/spring-cloud-tutorial-config-server/local-yml-success.png)


A YAML-fájlból származó adatoknak meg kell jelennie a Azure Portalban. A befejezéshez kattintson az **alkalmaz** gombra. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Az Azure Repos használata Azure Spring Cloud-konfigurációhoz

Az Azure Spring Cloud nyilvános, SSH által biztosított és alapszintű HTTP-hitelesítéssel védett Git-adattárakhoz is hozzáfér. Ezt az utolsó lehetőséget fogjuk használni, mivel egyszerűbb létrehozni és kezelni az Azure Repos-t.

### <a name="get-repo-url-and-credentials"></a>A tárház URL-címének és hitelesítő adatainak beolvasása
1. A projekthez tartozó Azure Repos-portálon kattintson a klónozás gombra:

    ![Klónozás gomb](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Másolja a klónozási URL-címet a szövegmezőből. Ez az URL-cím általában a következő formában jelenik meg:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    `https://`A és a előtt távolítson el mindent `dev.azure.com` , beleértve a következőt: `@` . Az eredményül kapott URL-címnek a következő formátumúnak kell lennie:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Mentse ezt az URL-címet a következő szakaszban való használatra.

1. Kattintson a "git-hitelesítő adatok előállítása" elemre. Ekkor megjelenik egy Felhasználónév és egy jelszó. Mentse ezeket a következő szakaszban való használatra.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Az Azure Spring Cloud konfigurálása a Git-adattár eléréséhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.

1. Válassza ki a konfigurálni kívánt szolgáltatást.

1. A szolgáltatás lap bal oldali paneljének **Beállítások** területén válassza a **konfigurációs kiszolgáló** fület. A korábban létrehozott adattár konfigurálása:
   - Adja hozzá az előző szakaszban mentett adattár URL-címét.
   - Kattintson a be gombra `Authentication` , és válassza a `HTTP Basic`
   - A __Felhasználónév__ az előző szakaszban mentett Felhasználónév
   - A __jelszó__ az előző szakaszban mentett jelszó.
   - Kattintson az Apply (alkalmaz) gombra, és várjon, amíg a művelet sikeres lesz.

   ![Spring Cloud Config-kiszolgáló](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-configuration"></a>Konfiguráció törlése

A **konfigurációs kiszolgáló** lapon megjelenő **Alaphelyzetbe állítás** gomb kiválasztásával teljesen törölheti a meglévő beállításokat. Ha a konfigurációs kiszolgáló példányát egy másik forráshoz szeretné kapcsolni, például a GitHubról az Azure DevOps-ra való áttéréssel, törölje a konfigurációs kiszolgáló beállításait.



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és konfigurálhatja a Spring Cloud config Server-példányát. További információ az alkalmazások kezeléséről: [alkalmazások méretezése az Azure Spring Cloud-ban](spring-cloud-howto-scale-manual.md).
