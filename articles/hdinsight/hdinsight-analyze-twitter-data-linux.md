---
title: Twitter-alapú adatelemzés Apache Hive-Azure HDInsight
description: Ismerje meg, hogyan alakíthatja át a nyers TWitter-információkat a HDInsight a Apache Hive és a Apache Hadoop használatával egy kereshető struktúra-táblázatba.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: 74a18be37608b2ac0c8a64655fa833753111706a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942771"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Twitter-adataik elemzése Apache Hive és Apache Hadoop használatával a HDInsight-on

Megtudhatja, hogyan dolgozhat a Twitter-adatfeldolgozási [Apache Hive](https://hive.apache.org/) használatával. Az eredmény azon Twitter-felhasználók listája, akik az adott szót tartalmazó legtöbb tweetet elküldötték.

> [!IMPORTANT]  
> A dokumentumban ismertetett lépéseket a HDInsight 3,6-es verziójában tesztelték.

## <a name="get-the-data"></a>Az adatok lekérése

A Twitter lehetővé teszi az egyes tweetek JavaScript Object Notation (JSON) dokumentumként való lekérését egy REST APIon keresztül. Az API-hoz való hitelesítéshez [OAuth](https://oauth.net) szükséges.

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

1. Egy webböngészőből jelentkezzen be a következőbe: [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) . Ha nem rendelkezik Twitter-fiókkal, válassza a **regisztráció most** hivatkozást.

2. Válassza az **új alkalmazás létrehozása** lehetőséget.

3. Adja meg a **nevet**, a **leírást**, a **webhelyet**. Létrehozhat egy URL-címet a **webhely** mezőhöz. A következő táblázat a használandó példákat tartalmazza:

   | Mező | Érték |
   |--- |--- |
   | Név |MyHDInsightApp |
   | Leírás |MyHDInsightApp |
   | Webhely |`https://www.myhdinsightapp.com` |

4. Válassza az **Igen, elfogadom** lehetőséget, majd válassza **a Twitter-alkalmazás létrehozása** lehetőséget.

5. Válassza az **engedélyek** fület. Az alapértelmezett engedély **csak olvasható**.

6. Válassza a **kulcsok és hozzáférési tokenek** fület.

7. Válassza **a saját hozzáférési jogkivonat létrehozása** lehetőséget.

8. A lap jobb felső sarkában válassza a **teszt OAuth** elemet.

9. Jegyezze fel a fogyasztói kulcsot, a **fogyasztói titkos** kulcsot, a **hozzáférési tokent** és a **hozzáférési jogkivonat titkos** **kulcsát**.

### <a name="download-tweets"></a>Tweetek letöltése

A következő Python-kód letölti az 10 000 tweeteket a Twitterről, és menti azokat egy **tweets.txt** nevű fájlba.

> [!NOTE]  
> A HDInsight-fürtön a következő lépések végezhetők el, mivel a Python már telepítve van.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A következő parancsokkal telepítheti a [Tweepy](https://www.tweepy.org/), a [folyamatjelző sávot](https://pypi.python.org/pypi/progressbar/2.2)és az egyéb szükséges csomagokat:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. A következő parancs használatával hozzon létre egy **gettweets.py** nevű fájlt:

   ```bash
   nano gettweets.py
   ```

1. Szerkessze az alábbi kódot a `Your consumer secret` `Your consumer key` Twitter- `Your access token` `Your access token secret` alkalmazásból származó releváns információk kiváltásával. Ezután illessze be a szerkesztett kódot a **gettweets.py** -fájl tartalmába.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Módosítsa a témakörök szűrőt az utolsó sorban a népszerű kulcsszavak nyomon követéséhez. A parancsfájl futtatásának időpontjában népszerű kulcsszavakat használva gyorsabb adatrögzítést tesz lehetővé.

1. A fájl mentéséhez használja a **CTRL + X billentyűkombinációt**, majd az **Y** billentyűt.

1. Futtassa a következő parancsot a fájl futtatásához és a tweetek letöltéséhez:

    ```bash
    python gettweets.py
    ```

    Megjelenik egy folyamatjelző. A tweetek letöltése akár 100%-ot is megszámol.

   > [!NOTE]  
   > Ha hosszabb ideig tart, amíg a folyamatjelző előre be nem következik, módosítania kell a szűrőt, hogy nyomon követhesse a trendek témakörét. Ha sok Tweet van a szűrőben szereplő témakörhöz, gyorsan lekérheti az 100-tweetek szükségesek.

### <a name="upload-the-data"></a>Adatok feltöltése

Az adatok HDInsight-tárolóba való feltöltéséhez használja a következő parancsokat:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Ezek a parancsok olyan helyen tárolják az adattárakat, amelyet a fürt összes csomópontja elérhet.

## <a name="run-the-hiveql-job"></a>A HiveQL-feladatok futtatása

1. A következő parancs használatával hozzon létre egy [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) -utasításokat tartalmazó fájlt:

   ```bash
   nano twitter.hql
   ```

    Használja a következő szöveget a fájl tartalmának:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Nyomja le a **CTRL + X** billentyűkombinációt, majd nyomja le az **Y** billentyűt a fájl mentéséhez.

1. A következő parancs használatával futtassa a fájlban található HiveQL:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Ez a parancs futtatja a **Twitter. HQL** fájlt. A lekérdezés befejeződése után megjelenik egy `jdbc:hive2//localhost:10001/>` üzenet.

1. A Beeline parancssorból a következő lekérdezéssel ellenőrizheti, hogy az adatok importálása megtörtént-e:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Ez a lekérdezés legfeljebb 10 olyan tweetet ad vissza, amely az üzenet szövege tartalmazza az **Azure** szót.

    > [!NOTE]  
    > Ha módosította a szűrőt a `gettweets.py` parancsfájlban, cserélje le az **Azure** -t a használt szűrők egyikére.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan alakíthat át strukturálatlan JSON-adatkészletet egy strukturált [Apache Hive](https://hive.apache.org/) táblázatba. Ha többet szeretne megtudni a HDInsight-beli Kaptárról, tekintse meg a következő dokumentumokat:

* [Ismerkedés a HDInsight szolgáltatással](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Repülési késleltetési idő elemzése az HDInsight használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)