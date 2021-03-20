---
title: Adatok rögzítése Event Hubsból Azure Data Lake Storage Gen1
description: Megtudhatja, hogyan rögzíthet Azure Data Lake Storage Gen1 az Azure Event Hubs által fogadott adatgyűjtéshez. Először ellenőrizze az előfeltételeket.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578240"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Adatok rögzítése a Azure Data Lake Storage Gen1 használatával Event Hubs

Megtudhatja, hogyan rögzíthet Azure Data Lake Storage Gen1 az Azure Event Hubs által fogadott adatgyűjtéshez.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépései](data-lake-store-get-started-portal.md)című témakört.

*  **Egy Event Hubs névtér**. Útmutatásért tekintse meg [Event Hubs névtér létrehozása](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)című témakört. Győződjön meg arról, hogy a Data Lake Storage Gen1 fiók és a Event Hubs névtér ugyanahhoz az Azure-előfizetéshez tartoznak.


## <a name="assign-permissions-to-event-hubs"></a>Engedélyek kiosztása Event Hubshoz

Ebben a szakaszban egy mappát hoz létre azon a fiókon belül, ahová az Event Hubs-ból származó adatok rögzítését szeretné. A Event Hubshoz is rendelhet engedélyeket, így az adatData Lake Storage Gen1-fiókba írhat. 

1. Nyissa meg azt a Data Lake Storage Gen1 fiókot, ahová az adatok rögzítését Event Hubs, majd kattintson a **adatkezelő** gombra.

    ![Data Lake Storage Gen1 adatkezelő](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 adatkezelő")

1.  Kattintson az **új mappa** elemre, majd adja meg annak a mappának a nevét, ahová rögzíteni szeretné az adatmennyiséget.

    ![Új mappa létrehozása a Data Lake Storage Gen1ban](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Új mappa létrehozása a Data Lake Storage Gen1ban")

1. Engedélyeket rendelhet Data Lake Storage Gen1 gyökeréhez. 

    a. Kattintson a **adatkezelő** elemre, válassza ki a Data Lake Storage Gen1-fiók gyökerét, majd kattintson a **hozzáférés** elemre.

    ![Képernyőkép az adatkezelőről a fiók gyökerével és az elnevezett hozzáférési lehetőséggel.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Engedélyek kiosztása a Data Lake Storage Gen1 gyökeréhez")

    b. A **hozzáférés** területen kattintson a **Hozzáadás**, majd a **felhasználó vagy csoport kiválasztása** elemre, és keresse meg a következőt: `Microsoft.EventHubs` . 

    ![Képernyőfelvétel a hozzáférési oldalról a Hozzáadás lehetőséggel, válassza a felhasználó vagy csoport lehetőséget, és a Microsoft Eventhubs lehetőséget.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Engedélyek kiosztása a Data Lake Storage Gen1 gyökeréhez")
    
    Kattintson a **Kiválasztás** elemre.

    c. Az **engedélyek kiosztása** területen kattintson az **engedélyek kiválasztása** elemre. Állítsa be az **engedélyeket** a **végrehajtáshoz**. A **Hozzáadás** ehhez a **mappához és az összes gyermekhez** beállítás megadása. Adja meg a **Hozzáadás másként** **lehetőséget egy hozzáférési engedély bejegyzéséhez és egy alapértelmezett engedély bejegyzéshez**.

    > [!IMPORTANT]
    > Amikor új mappastruktúrát hoz létre az Azure Event Hubs által fogadott adatrögzítéshez, ez egy egyszerű módja annak, hogy hozzáférést biztosítson a célmappához.  Ha azonban egy legfelső szintű mappa összes gyermekéhez ad hozzá engedélyeket, és sok gyermek fájl és mappa is hosszú időt vehet igénybe.  Ha a gyökérmappa nagy mennyiségű fájlt és mappát tartalmaz, előfordulhat, hogy  `Microsoft.EventHubs` az elérési út a végső célmappához tartozó minden mappához külön végrehajtási engedélyeket adhat hozzá. 

    ![Képernyőkép az engedélyek kiosztásáról című részből, a kinevezett engedélyek kiválasztása lehetőséggel. Az engedélyek kiválasztása szakasz mellett a végrehajtás lehetőség, a Hozzáadás és a Hozzáadás lehetőség is látható.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Engedélyek kiosztása a Data Lake Storage Gen1 gyökeréhez")

    Kattintson az **OK** gombra.

1. Rendeljen engedélyeket a mappához abban a Data Lake Storage Gen1 fiókban, ahol az adatrögzítést szeretné.

    a. Kattintson a **adatkezelő** elemre, válassza ki a mappát a Data Lake Storage Gen1-fiókban, majd kattintson a **hozzáférés** elemre.

    ![Képernyőfelvétel: az adatkezelő mappája a fiókban, a hozzáférési lehetőség pedig kinevezett.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Engedélyek kiosztása a Data Lake Storage Gen1 mappához")

    b. A **hozzáférés** területen kattintson a **Hozzáadás**, majd a **felhasználó vagy csoport kiválasztása** elemre, és keresse meg a következőt: `Microsoft.EventHubs` . 

    ![Képernyőkép az adatkezelő-hozzáférés oldaláról a Hozzáadás lehetőséggel, válassza a felhasználó vagy csoport lehetőséget, és a Microsoft Eventhubs lehetőséget.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Engedélyek kiosztása a Data Lake Storage Gen1 mappához")
    
    Kattintson a **Kiválasztás** elemre.

    c. Az **engedélyek kiosztása** területen kattintson az **engedélyek kiválasztása** elemre. **Olvasási, írási** és **végrehajtási** **engedélyek** beállítása. A **Hozzáadás** ehhez a **mappához és az összes gyermekhez** beállítás megadása. Végül állítsa a **Hozzáadás másként** **lehetőséget egy hozzáférési engedély bejegyzéséhez és egy alapértelmezett engedély bejegyzéshez**.

    ![Képernyőkép az engedélyek kiosztásáról című részből, a kinevezett engedélyek kiválasztása lehetőséggel. Az engedélyek kiválasztása szakasz az olvasási, írási és végrehajtási beállításokkal, a Hozzáadás a következővel lehetőséggel, valamint a felismert Hozzáadás lehetőséggel egészül ki.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Engedélyek kiosztása a Data Lake Storage Gen1 mappához")
    
    Kattintson az **OK** gombra. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Event Hubs konfigurálása az adatData Lake Storage Gen1ba való rögzítéshez

Ebben a szakaszban egy Event Hubs névtéren belül hoz létre egy Event hubot. Az Event hub-t úgy is konfigurálhatja, hogy az Azure Data Lake Storage Gen1 fiókba rögzítse az adatmennyiséget. Ez a szakasz azt feltételezi, hogy már létrehozott egy Event Hubs névteret.

1. A Event Hubs névtér **Áttekintés** paneljén kattintson a **+ Event hub** elemre.

    ![Képernyőkép az Áttekintés panelről, az Event hub lehetőség kiválasztásával.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Eseményközpont létrehozása")

1. Adja meg a következő értékeket a Event Hubs konfigurálásához az adatok Data Lake Storage Gen1ba való rögzítéséhez.

    ![Képernyőfelvétel: az Event hub létrehozása párbeszédpanel neve szövegmezővel, a rögzítési lehetőséggel, a rögzítési szolgáltató lehetőséggel, a Data Lake Store kiválasztása lehetőséggel és az Data Lake Path lehetőséggel.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Eseményközpont létrehozása")

    a. Adja meg az Event hub nevét.
    
    b. Ebben az oktatóanyagban állítsa be a **partíciók darabszámát** és az **üzenetek megőrzését** az alapértelmezett értékekre.
    
    c. A **rögzítés** beállítása **a** következőre:. Állítsa be az **időablakot** (a rögzítés gyakorisága) és a **méret ablakot** (a rögzítendő adatméretet). 
    
    d. A **rögzítési szolgáltatónál** válassza a **Azure Data Lake Store** lehetőséget, majd válassza ki a korábban létrehozott Data Lake Storage Gen1 fiókot. A **Data Lake elérési útja** mezőbe írja be a Data Lake Storage Gen1 fiókban létrehozott mappa nevét. Csak a mappa relatív elérési útját kell megadnia.

    e. Hagyja meg a **minta rögzítési fájlnevének formátumát** az alapértelmezett értékre. Ezzel a beállítással szabályozható a rögzítési mappa alatt létrehozott mappastruktúrát.

    f. Kattintson a **Létrehozás** lehetőségre.

## <a name="test-the-setup"></a>A telepítés tesztelése

Most tesztelheti a megoldást úgy, hogy adatokat küld az Azure Event hub-nak. Kövesse az [események küldése az Azure Event Hubsba](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)című témakör útmutatását. Az adatok küldésének megkezdése után a Data Lake Storage Gen1ban látható adatok megjelennek a megadott mappastruktúrát használva. Például megtekintheti a mappa struktúráját, ahogyan az a következő képernyőképen látható a Data Lake Storage Gen1-fiókjában.

![Minta EventHub-adatData Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Minta EventHub-adatData Lake Storage Gen1")

> [!NOTE]
> Akkor is, ha nem rendelkezik a Event Hubsba érkező üzenetekkel, Event Hubs a Data Lake Storage Gen1 fiókba csak a fejléceket tartalmazó üres fájlokat ír. A fájlok a Event Hubs létrehozásakor megadott időintervallumban íródnak.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1ban lévő adatelemzés

Ha az adatok Data Lake Storage Gen1, az adatok feldolgozásához és összegyűjtéséhez futtathat analitikai feladatokat. Lásd: [USQL Avro-példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) a Azure Data Lake Analytics használatával történő végrehajtásához.
  

## <a name="see-also"></a>Lásd még
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md)
