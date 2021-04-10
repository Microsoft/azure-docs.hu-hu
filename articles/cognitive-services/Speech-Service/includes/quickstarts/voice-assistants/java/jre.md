---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: 536b0f2554bc5a360b9e1a7e14fa7b5383aa5365
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106271693"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../overview.md#try-the-speech-service-for-free)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Hozzon létre egy olyan robotot, amely a [közvetlen vonalas beszéd csatornához](/azure/bot-service/bot-service-channel-connect-directlinespeech) csatlakozik
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

  > [!NOTE]
  > Tekintse meg [a támogatott régiók listáját a hangsegédek számára](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Emellett a naplózás engedélyezéséhez frissítse a _pom.xml_ fájlt, hogy az tartalmazza a következő függőséget:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új üres osztályt szeretne hozzáadni a Java-projekthez, válassza a **fájl**  >  **új**  >  **osztály** elemet.

1. Az **új Java-osztály** ablakban adja meg a _speechsdk._ gyors értéket a **csomag** mezőben _és a_ főértékben a **név** mezőben.

   ![A New Java Class varázsló képernyőképe](~/articles/cognitive-services/speech-service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Nyissa meg az újonnan létrehozott `Main` osztályt, és cserélje le a fájl tartalmát a `Main.java` következő kiindulási kódra:

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.BotFrameworkConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. A `main` metódusban először konfigurálja `DialogServiceConfig` és használja egy példány létrehozásához `DialogServiceConnector` . Ez a példány csatlakozik a közvetlen vonalas beszédfelismerési csatornához, hogy együttműködjön a robottal. `AudioConfig`A rendszer egy példányt is használ a hangbemenet forrásának megadására. Ebben a példában az alapértelmezett mikrofon van használatban `AudioConfig.fromDefaultMicrophoneInput()` .

   - Cserélje le a karakterláncot az `YourSubscriptionKey` előfizetési kulcsra, amelyet a [webhelyről](../../../../overview.md#try-the-speech-service-for-free)szerezhet be.
   - Cserélje le a karakterláncot `YourServiceRegion` az előfizetéséhez társított [régióra](~/articles/cognitive-services/speech-service/regions.md) .

   > [!NOTE]
   > Tekintse meg [a támogatott régiók listáját a hangsegédek számára](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Az összekötő `DialogServiceConnector` számos eseményre támaszkodik, hogy kommunikáljon a robot tevékenységével, a beszédfelismerés eredményeivel és egyéb információkkal. Adja hozzá ezeket az esemény-figyelőket a következőhöz.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Kapcsolódjon `DialogServiceConnector` a közvetlen vonalas beszédhez a metódus meghívásával `connectAsync()` . A robot teszteléséhez meghívja a `listenOnceAsync` metódust, hogy hangbemenetet küldjön a mikrofonból. Emellett az metódus használatával is `sendActivityAsync` elküldheti az egyéni tevékenységeket szerializált karakterláncként. Ezek az egyéni tevékenységek további, a beszélgetés során használt adatait is megadhatják.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Mentse a fájl módosításait `Main` .

1. A válaszok lejátszásának támogatásához adjon hozzá egy további osztályt, amely átalakítja a getAudio () API által visszaadott PullAudioOutputStream objektumot egy Java-InputStream a könnyű kezelhetőség érdekében. Ez `ActivityAudioStream` egy speciális osztály, amely a Direct line Speech Channel hangválaszait kezeli. Hozzáférést biztosít a lejátszáshoz szükséges hangformátum-információk lekéréséhez. Ehhez válassza a **fájl**  >  **új**  >  **osztály** elemet.

1. Az **új Java-osztály** ablakban írja be a _Speechsdk. Gyorsindítás_ nevet a **csomag** mezőbe, és a _ActivityAudioStream_ értéket a **név** mezőbe.

1. Nyissa meg az újonnan létrehozott `ActivityAudioStream` osztályt, és cserélje le az alábbi kódra:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. Mentse a fájl módosításait `ActivityAudioStream` .

## <a name="build-and-run-the-app"></a>Készítsen buildet és futtassa az alkalmazást

Válassza az F11 lehetőséget, vagy válassza a  >  **hibakeresés** futtatása lehetőséget.
A konzolon megjelenik a "Say valami" üzenet.
Ezen a ponton beszéljen egy angol nyelvű kifejezéssel vagy mondattal, amelyet a robotja tud megérteni. A beszédet a robot a közvetlen vonalas beszéd csatornán keresztül továbbítja, ahol a robot felismeri és dolgozza fel. A válasz tevékenységként lesz visszaadva. Ha a robot válaszként adja vissza a beszédet, a hang a osztály használatával lesz visszajátszva `AudioPlayer` .

![Képernyőkép a konzolról a sikeres felismerést követően](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]