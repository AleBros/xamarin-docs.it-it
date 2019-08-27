---
title: Compilazione di APK specifici di ABI
description: Questo documento illustra come compilare un file APK destinato a un singolo ABI usando Xamarin.Android.
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 4a3ba970f8ca32f0bfa2e5297e8052f3eb572ed0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525723"
---
# <a name="building-abi-specific-apks"></a>Compilazione di APK specifici di ABI

_Questo documento illustra come compilare un file APK destinato a un singolo ABI usando Xamarin.Android._



## <a name="overview"></a>Panoramica

In alcune situazioni può risultare utile per un'applicazione avere più APK. Ogni APK è firmato con lo stesso archivio chiavi e condivide lo stesso nome di pacchetto, ma viene compilato per una configurazione di Android o un dispositivo specifico. Questo non è l'approccio consigliato: è molto più semplice avere un solo file APK che può supportare più dispositivi e configurazioni. Esistono alcune situazioni in cui la creazione di più APK può essere utile, ad esempio:

- **Ridurre le dimensioni dell'APK**: Google Play impone un limite di 100 MB per le dimensioni dei file APK. Creando APK specifici dei dispositivi, è possibile ridurre le dimensioni del file APK perché è necessario specificare solo un subset di asset e risorse per l'applicazione.

- **Supportare architetture CPU diverse**: se l'applicazione ha librerie condivise per CPU specifiche, è possibile distribuire solo le librerie condivise per tali CPU.


Più APK possono complicare la distribuzione, ma questo problema viene risolto da Google Play. Google Play verificherà che a un dispositivo venga distribuito l'APK corretto in base al codice della versione dell'applicazione e ad altri metadati contenuti in **AndroidManifest.XML**. Per informazioni dettagliate specifiche e per conoscere le restrizioni al supporto di più APK per un'applicazione in Google Play, vedere la [documentazione di Google sul supporto di più APK](https://developer.android.com/google/play/publishing/multiple-apks.html).

Questa guida illustra come creare lo script di compilazione di più APK per un'applicazione Xamarin.Android e come fare in modo che ogni APK specifichi come destinazione un determinato ABI. Verranno illustrati gli argomenti seguenti:

1. Creare un *codice versione* univoco per l'APK.
1. Creare una versione temporanea di **AndroidManifest.XML** che verrà usata per questo APK.
1. Compilare l'applicazione usando il file **AndroidManifest.XML** del passaggio precedente.
1. Preparare l'APK per il rilascio firmandolo e allineandolo con zipalign.


Alla fine di questa guida è disponibile una procedura dettagliata che illustra come creare lo script di questi passaggi usando [Rake](http://martinfowler.com/articles/rake.html).



### <a name="creating-the-version-code-for-the-apk"></a>Creazione del codice versione per l'APK

Google consiglia un particolare algoritmo che usa un codice versione a sette cifre. Vedere la sezione *Using a version code scheme* (Uso di uno schema di codice versione) nel documento [Multiple APK support](https://developer.android.com/google/play/publishing/multiple-apks.html) (Supporto di più APK).
Espandendo a otto cifre questo schema del codice versione, è possibile includere nel codice versione alcune informazioni sull'ABI che garantiranno che Google Play distribuisca il file APK corretto in un dispositivo. L'elenco seguente illustra questo formato del codice versione a otto cifre (indicizzato da sinistra a destra):

- **Indice 0** (in rosso nel diagramma sotto): intero per l'ABI:
    - 1 &ndash; `armeabi`
    - 2 &ndash; `armeabi-v7a`
    - 6 &ndash; `x86`

- **Indice 1-2** (in arancione nel diagramma sotto): livello API minimo supportato dall'applicazione.

- **Indice 3-4** (in blu nel diagramma sotto): dimensioni della schermata supportate:
    - 1: piccole
    - 2: normali
    - 3: grandi
    - 4: molto grandi

- **Indice 5-7** (in verde nel diagramma sotto): numero univoco per il codice versione. 
    È impostato dallo sviluppatore. Deve aumentare per ogni versione pubblica dell'applicazione.

Il diagramma seguente illustra la posizione di ogni codice descritto nell'elenco precedente:

[![Diagramma del formato del codice versione a otto cifre, codificato in base al colore](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)


Google Play verificherà che al dispositivo venga distribuito l'APK corretto in base a `versionCode` e alla configurazione dell'APK. Il file APK con il codice versione più alto verrà distribuito al dispositivo. Ad esempio, un'applicazione può includere tre APK con i codici versione seguenti:

- 11413456: l'ABI è `armeabi`, la destinazione è il livello API 14, le dimensioni della schermata sono da piccole a grandi, il numero di versione è 456.
- 21423456: l'ABI è `armeabi-v7a`, la destinazione è il livello API 14, le dimensioni della schermata sono normali e grandi, il numero di versione è 456.
- 61423456: l'ABI è `x86`, la destinazione è il livello API 14, le dimensioni della schermata sono normali e grandi, il numero di versione è 456.

Per continuare con questo esempio, si supponga che sia stato corretto un bug specifico di `armeabi-v7a`. La versione dell'app aumenta a 457 e viene compilato un nuovo APK con `android:versionCode` impostato su 21423457. I codici per le versioni `armeabi` e `x86` rimarranno gli stessi.

Si supponga ora che la versione x86 riceva alcuni aggiornamenti o correzioni destinate a un'API più recente (API livello 19) e che quindi la versione dell'app sia 500. Il nuovo `versionCode` diventerà 61923500 mentre armeabi/armeabi-v7a rimangono invariati. A questo punto, i codici versione saranno:

- 11413456: l'ABI è `armeabi`, la destinazione è il livello API 14, le dimensioni della schermata sono da piccole a grandi, il nome della versione è 456.
- 21423457: l'ABI è `armeabi-v7a`, la destinazione è il livello API 14, le dimensioni della schermata sono normali e grandi, il nome della versione è 457.
- 61923500: l'ABI è `x86`, la destinazione è il livello API 19, le dimensioni della schermata sono normali e grandi, il nome della versione è 500.


La gestione manuale di questi codici versione può comportare un carico notevole per lo sviluppatore. Il processo per calcolare l'elemento `android:versionCode` corretto e quindi compilare gli APK dovrebbe essere automatizzato.
Un esempio di come eseguire questa operazione verrà descritto nella procedura dettagliata alla fine di questo documento.


### <a name="create-a-temporary-androidmanifestxml"></a>Creare un file AndroidManifest.XML temporaneo

Anche se non strettamente necessaria, la creazione di un file **AndroidManifest.XML** temporaneo per ogni ABI consente di evitare problemi che potrebbero verificarsi con le informazioni che passano da un APK all'altro. È ad esempio fondamentale che l'attributo `android:versionCode` sia univoco per ogni APK.

La procedura dipende dal sistema di scripting coinvolto, ma in genere è necessario aprire una copia del manifesto Android usata durante lo sviluppo, modificarla e quindi usare tale manifesto modificato durante il processo di compilazione.



### <a name="compiling-the-apk"></a>Compilazione dell'APK

Il modo migliore per compilare l'APK per ogni ABI consiste nell'usare `xbuild` o `msbuild`, come illustrato nella riga di comando di esempio seguente:

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

L'elenco seguente descrive ogni parametro della riga di comando:

- `/t:Package`: crea un APK Android che viene firmato usando l'archivio chiavi di debug

- `/p:AndroidSupportedAbis=<TARGET_ABI>`: ABI da specificare come destinazione. Deve essere `armeabi`, `armeabi-v7a` o `x86`

- `/p:IntermediateOutputPath=obj.<TARGET_ABI>/`: directory che conterrà i file intermedi creati durante la compilazione. Se necessario, Xamarin.Android creerà una directory denominata in base all'ABI, ad esempio `obj.armeabi-v7a`. È consigliabile usare una cartella per ogni ABI, per evitare problemi che causano il passaggio dei file da una compilazione a un'altra. Si noti che questo valore termina con un separatore di directory (`/` nel caso di OS X).

- `/p:AndroidManifest`: questa proprietà specifica il percorso del file **AndroidManifest.XML** che verrà usato durante la compilazione.

- `/p:OutputPath=bin.<TARGET_ABI>`: directory che ospiterà l'APK finale. Xamarin.Android creerà una directory denominata in base all'ABI, ad esempio `bin.armeabi-v7a`.

- `/p:Configuration=Release`: esegue una build di rilascio dell'APK. Le build Debug potrebbero non essere caricate in Google Play.

- `<CS_PROJ FILE>`: percorso del file `.csproj` per il progetto Xamarin.Android.



### <a name="sign-and-zipalign-the-apk"></a>Firmare ed eseguire Zipalign per il file APK

È necessario firmare il file APK prima che possa essere distribuito tramite Google Play. A questo scopo, è possibile usare l'applicazione `jarsigner` che fa parte del kit dello sviluppatore Java. La riga di comando seguente illustra come usare `jarsigner` nella riga di comando:

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

Tutte le applicazioni Xamarin.Android devono essere allineate con zipaling prima di poter essere eseguite in un dispositivo. Questo è il formato della riga di comando da usare:

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```


## <a name="automating-apk-creation-with-rake"></a>Automazione della creazione di file APK con Rake

Il progetto di esempio [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) è un semplice progetto Android che illustra come calcolare un numero di versione specifico di un ABI e compilare tre APK distinti per ognuno degli ABI seguenti:

- armeabi
- armeabi-v7a
- x86


Il [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) nel progetto di esempio esegue ogni passaggio descritto nelle sezioni precedenti:

1. [Creare un elemento android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30) per l'APK.

1. [Scrivere l'elemento android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55) in un file **AndroidManifest.XML** personalizzato per tale APK.

1. [Compilare una build di rilascio](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63) del progetto Xamarin.Android che specificherà come singola destinazione l'ABI e userà il file **AndroidManifest.XML** creato nel passaggio precedente.

1. [Firmare l'APK ](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66) con un archivio chiavi di produzione.

1. [Eseguire Zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67) per il file APK.


Per compilare tutti gli APK per l'applicazione, eseguire l'attività di Rake `build` dalla riga di comando:

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

Al termine dell'attività di Rake, saranno disponibili tre cartelle `bin` con il file `xamarin.helloworld.apk`. Lo screenshot seguente illustra ognuna di queste cartelle con il relativo contenuto:

[![Posizioni delle cartelle specifiche della piattaforma contenenti xamarin.helloworld.apk](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)


> [!NOTE]
> Il processo di compilazione illustrato in questa guida può essere implementato in uno dei numerosi sistemi di compilazione. Anche se non è disponibile un esempio già scritto, dovrebbe essere possibile eseguirlo anche con [Powershell](https://technet.microsoft.com/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) o [Fake](http://fsharp.github.io/FAKE/).


## <a name="summary"></a>Riepilogo

Questa guida ha fornito alcuni suggerimenti su come creare APK Android per un ABI specifico. Ha anche illustrato un possibile schema per la creazione di `android:versionCodes` che identificheranno l'architettura CPU a cui è destinato l'APK. Nella procedura dettagliata è stato incluso un progetto per la compilazione del quale è stato creato uno script con Rake.



## <a name="related-links"></a>Collegamenti correlati

- [OneABIPerAPK (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/oneabiperapk) (OneABIPerAPK - Esempio)
- [Publishing an Application](~/android/deploy-test/publishing/index.md) (Pubblicazione di un'applicazione)
- [Multiple APK Support for Google Play](https://developer.android.com/google/play/publishing/multiple-apks.html) (Supporto di più APK per Google Play)
