---
title: Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport vertikális fel-és leskálázása a CLI-vel (azdata vagy kubectl)
description: Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport vertikális fel-és leskálázása a CLI-vel (azdata vagy kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687549"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport vertikális fel-és leskálázása a CLI-vel (azdata vagy kubectl)



Időnként előfordulhat, hogy módosítania kell a kiszolgálócsoport jellemzőit vagy definícióját. Például:

- A koordinátor vagy a munkavégző csomópont által használt virtuális mag számának vertikális felskálázása
- Vertikális fel-vagy leskálázás a memóriát, amelyet a koordinátor vagy a munkavégző csomópont használ

Ez az útmutató ismerteti, hogyan méretezhető a virtuális mag és/vagy a memória.

A virtuális mag vagy a memória beállításainak méretezése azt jelenti, hogy lehetősége van minimális és/vagy maximális értéket beállítani az egyes virtuális mag és memória-beállításokhoz. Ha úgy szeretné konfigurálni a virtuális mag, hogy egy adott számú vagy egy adott mennyiségű memóriát használjon, a minimális beállításokat a maximális beállítások értékkel kell megadnia.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>A kiszolgálócsoport aktuális definíciójának megjelenítése

A kiszolgálócsoport aktuális definíciójának megjelenítéséhez és az aktuális virtuális mag és memória-beállítások megtekintéséhez futtassa a következő parancsot:

### <a name="cli-with-azdata"></a>CLI és azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI és kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Ha a PostgreSQL 11-es verzióját hozta létre, futtassa a parancsot `kubectl describe postgresql-11/<server group name>` .

A kiszolgálói csoport konfigurációját adja vissza. Ha a csoportot az alapértelmezett beállításokkal hozta létre, a definíciót a következőképpen kell megtekinteni:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>A kiszolgálócsoport definíciójának értelmezése

A kiszolgálócsoport definíciójában a csomópontok min/max virtuális mag és a percenkénti minimális/maximális memória beállításait tartalmazó szakasz az **"ütemezés"** szakasz. Ebben a szakaszban a maximális beállítások a **"korlátok"** nevű alszakaszban maradnak, és a minimális beállítások a **"kérelmek"** nevű alszakaszban maradnak.

Ha a minimális beállításokat a maximális beállításoktól eltérő értékre állítja, a konfiguráció garantálja, hogy a kiszolgálói csoport a szükséges erőforrásokat lefoglalta. Nem lépi túl a beállított korlátokat.

A kiszolgálócsoport által ténylegesen használt erőforrások (virtuális mag és memória) a maximális beállításoktól függenek, a munkaterheléstől és a fürtön elérhető erőforrástól függően. Ha nem a maximális értékre korlátozza a beállításokat, a kiszolgálócsoport felhasználhatja az összes olyan erőforrást, amelyet a Kubernetes-fürt lefoglal ahhoz a Kubernetes-csomóponthoz, amelyhez a kiszolgálócsoport be van ütemezve.

Ezek a virtuális mag-és memóriahasználat a PostgreSQL nagy kapacitású-csomópontjaira (koordinátori csomópontra és munkavégző csomópontokra) vonatkoznak. A koordinátori csomópont és a feldolgozó csomópontok definícióinak külön történő beállítása még nem támogatott.

Alapértelmezett konfigurációban csak a minimális memória van beállítva a 256Mi értékre, mert a PostgreSQL-nagy kapacitású futtatásához ajánlott minimális memória.

> [!NOTE]
> A minimális beállítás nem jelenti azt, hogy a kiszolgálócsoport ezt a minimális értéket fogja használni. Ez azt jelenti, hogy ha a kiszolgálócsoport szüksége van rá, garantált, hogy legalább ezt a minimumot le kell osztani. Vegyük például a következőt: `--minCpu 2` . Nem jelenti azt, hogy a kiszolgálócsoport legalább 2 virtuális mag fog használni. Ehelyett ez azt jelenti, hogy a sever-csoport kevesebb mint 2 virtuális mag-t használhat, ha nem igényel ennyit, és garantáltan legalább 2 virtuális mag kell lefoglalni, ha később szükség van rájuk. Ez azt jelenti, hogy a Kubernetes-fürt erőforrásokat foglal le más számítási feladatokhoz oly módon, hogy a kiszolgáló csoport számára is kioszthatja a két virtuális mag, ha az szüksége van rájuk.

>[!NOTE]
>A System konfigurációjának módosítása előtt győződjön meg arról, hogy megismerkedjen a Kubernetes- [erőforrás modelljével](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>A kiszolgálócsoport vertikális felskálázása

A beállítani kívánt beállításokat figyelembe kell venni a Kubernetes-fürthöz beállított konfiguráción belül. Ügyeljen arra, hogy ne állítson be olyan értékeket, amelyeket a Kubernetes-fürt nem tud kielégíteni. Ez hibákhoz vagy kiszámíthatatlan viselkedéshez vezethet. Ha például a kiszolgálócsoport állapota hosszabb ideig frissül az állapot _frissítése_ után, a konfiguráció módosítása után előfordulhat, hogy az alábbi paramétereket úgy állítja be, hogy a Kubernetes-fürt nem tudja kielégíteni az értékeket. Ha ez a helyzet, állítsa be a módosítást, vagy olvassa el a _troubleshooting_section.

Tegyük fel például, hogy a következőre szeretné méretezni a kiszolgálócsoport definícióját:

- Minimális virtuális mag = 2
- Max. virtuális mag = 4
- Minimális memória = 512 MB
- Maximális memória = 1Gb

A következő módszerek egyikét kellene használnia:

### <a name="cli-with-azdata"></a>CLI és azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Alább látható egy példa, amely bemutatja, hogyan használhatja a parancsot. A szerkesztési parancs végrehajtása előtt ügyeljen arra, hogy a paramétereket olyan értékekre állítsa be, amelyeket a Kubernetes-fürt megtarthat.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

A parancs sikeresen lefut, amikor megjelenik:

```console
<name of your server group> is Ready
```

> [!NOTE]
> A paraméterekkel kapcsolatos részletekért futtassa a parancsot `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>CLI és kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Ekkor megjelenik a VI-szerkesztő, ahol megtekintheti és módosíthatja a konfigurációt. A következő paranccsal rendelje hozzá a kívánt beállítást a mező nevére a specifikációban:

> [!CAUTION]
> Alább látható egy példa, amely bemutatja, hogyan szerkesztheti a konfigurációt. A konfiguráció frissítése előtt ügyeljen arra, hogy a paramétereket olyan értékekre állítsa be, amelyeket a Kubernetes-fürt megtarthat.

Például:
- Min. virtuális mag = 2 – > scheduling\default\resources\requests\cpu
- Max virtuális mag = 4 – > scheduling\default\resources\limits\cpu
- Minimális memória = 512 MB – > scheduling\default\resources\requests\cpu
- Maximális memória = 1Gb – > scheduling\default\resources\limits\cpu

Ha nem ismeri a VI szerkesztőt, tekintse meg [a szükséges parancsok](https://www.computerhope.com/unix/uvi.htm)leírását:
- szerkesztési mód: `i`
- mozgatás a nyilakkal
- _stop szerkesztés: `esc`
- _exit mentés nélkül: `:qa!`
- _exit mentés után: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>A kiszolgálócsoport Felskálázási definíciójának megjelenítése

Futtassa újra a parancsot a kiszolgálócsoport definíciójának megjelenítéséhez, és ellenőrizze, hogy a kívánt módon van-e beállítva:

### <a name="cli-with-azdata"></a>CLI és azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI és kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Ha a PostgreSQL 11-es verzióját hozta létre, futtassa a parancsot `kubectl describe postgresql-11/<server group name>` .


Ekkor megjelenik a kiszolgálócsoport új definíciója:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>A kiszolgálócsoport méretezése

A kiszolgálócsoport méretezéséhez hajtsa végre ugyanazt a parancsot, de állítsa be a kisebb értékeket a méretezni kívánt beállításokhoz. Ha el szeretné távolítani a kérelmeket és/vagy a korlátozásokat, az értékét üres sztringként kell megadni.

## <a name="reset-to-default-values"></a>Visszaállítás az alapértelmezett értékekre
A Core/Memory Limits/Request paraméterek alapértelmezett értékre való visszaállításához szerkessze őket, és adjon meg egy üres karakterláncot a tényleges érték helyett. Ha például alaphelyzetbe kívánja állítani a Core limit (CL) paramétert, futtassa a következő parancsokat:
- Linux-ügyfélen:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- Windows-ügyfélen: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>Következő lépések

- [A rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoport horizontális felskálázása](scale-out-postgresql-hyperscale-server-group.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
