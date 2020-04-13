---
title: Processo di compilazione
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: bce2b6f29129894ed446100c87b5e92d3572ed2f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78946267"
---
# <a name="build-process"></a>Processo di compilazione

## <a name="overview"></a>Panoramica

Il processo di compilazione Xamarin.Android è responsabile dell'incollaggio di tutto insieme: [ `Resource.designer.cs`generazione ](~/android/internals/api-design.md), supporto `AndroidAsset`di , `AndroidResource`e altre azioni di [compilazione](#Build_Actions), generazione di wrapper [richiamabili Android](~/android/platform/java-integration/android-callable-wrappers.md)e generazione di un `.apk` oggetto per l'esecuzione su dispositivi Android.

## <a name="application-packages"></a>Pacchetti dell'applicazione

In senso lato, esistono due tipi di pacchetti dell'applicazione Android (file `.apk`) che possono essere generati dal sistema di compilazione di Xamarin.Android:

- Build di **rilascio**, che sono completamente autonome e non richiedono l'esecuzione di pacchetti aggiuntivi. Questi sono i pacchetti che verrebbero forniti a un archivio app.

- Build di **debug**, che sono l'esatto contrario.

Non a caso corrispondono all'elemento `Configuration` MSBuild che genera il pacchetto.

### <a name="shared-runtime"></a>Runtime condiviso

Il *runtime condiviso* è una coppia di pacchetti Android aggiuntivi che forniscono la libreria di classi base (`mscorlib.dll` e così via) e la libreria di binding Android (`Mono.Android.dll` e così via). Le build di debug si basano sul runtime condiviso invece di includere gli assembly di librerie di classi base e gli assembly di binding nel pacchetto dell'applicazione Android, consentendo di ridurre le dimensioni del pacchetto di debug.

Il runtime condiviso può essere disabilitato nelle build di debug impostando la proprietà `$(AndroidUseSharedRuntime)` su `False`.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>Fast Deployment

*Fast Deployment* ben si integra con il runtime condiviso per ridurre ulteriormente le dimensioni del pacchetto dell'applicazione Android. A tale scopo non si aggregano gli assembly dell'app nel pacchetto, che vengono invece copiati nella destinazione tramite `adb push`. Questo processo accelera il ciclo di compilazione/distribuzione/debug perché, se vengono modificati *solo* gli assembly, il pacchetto non viene reinstallato, ma solo gli assembly aggiornati vengono nuovamente sincronizzati nel dispositivo di destinazione.

È noto che Fast Deployment non riesce nei dispositivi che bloccano la sincronizzazione di `adb` con la directory `/data/data/@PACKAGE_NAME@/files/.__override__`.

Fast Deployment è abilitato per impostazione predefinita e può essere disabilitato nelle build di debug impostando la proprietà `$(EmbedAssembliesIntoApk)` su `True`.

## <a name="msbuild-projects"></a>Progetti MSBuild

Il processo di compilazione di Xamarin.Android si basa su MSBuild, che è anche il formato del file di progetto usato da Visual Studio per Mac e Visual Studio.
Gli utenti di norma non dovranno modificare manualmente i file MSBuild perché l'IDE crea progetti completamente funzionali, li aggiorna con le modifiche apportate e richiama automaticamente le destinazioni di compilazione, se necessario.

Gli utenti avanzati potrebbero voler eseguire operazioni non supportate dall'interfaccia utente grafica dell'IDE, quindi il processo di compilazione si può personalizzare modificando direttamente il file di progetto.
Questa pagina documenta solo le funzionalità e le personalizzazioni specifiche di Xamarin.Android, ma è possibile eseguire molte altre operazioni con i normali elementi, proprietà e destinazioni MSBuild.

<a name="Build_Targets" />

## <a name="build-targets"></a>Destinazioni di compilazione

Per i progetti Xamarin.Android vengono definite le destinazioni di compilazione seguenti:

- **** Build&ndash;: compila il pacchetto.

- **BuildAndStartAotProfiling** &ndash; Compila l'app con un profiler AOT incorporato, imposta la porta TCP del profiler su `$(AndroidAotProfilerPort)`e avvia l'attività predefinita.

  La porta TCP `9999`predefinita è .

  Aggiunto in Xamarin.Android 10.2.

- **** Clean&ndash;: rimuove tutti i file generati dal processo di compilazione.

- **FinishAotProfiling** &ndash; Raccoglie i dati del profiler AOT dal `$(AndroidAotProfilerPort)` dispositivo o `$(AndroidAotCustomProfilePath)`dall'emulatore tramite la porta TCP e li scrive in .

  I valori predefiniti per la `9999` `custom.aprof`porta e il profilo personalizzato sono e .

  Per passare opzioni `aprofutil`aggiuntive a `$(AProfUtilExtraOptions)` , impostarle nella proprietà .

  Equivale a:

  ```
  aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
  ```

  Aggiunto in Xamarin.Android 10.2.

- **Installa** &ndash; Installa il pacchetto nel dispositivo predefinito o nel dispositivo virtuale.

- **SignAndroidPackage** &ndash; Crea e firma`.apk`il pacchetto ( ). Da usare con `/p:Configuration=Release` per generare i pacchetti "Release" (Rilascio) autonomi.

- **StartAndroidActivity** &ndash; Avvia l'attività predefinita nel dispositivo o nell'emulatore in esecuzione. Per avviare un'attività `$(AndroidLaunchActivity)` diversa, impostare la proprietà sul nome dell'attività.

  che equivale a `adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)`.

  Aggiunto in Xamarin.Android 10.2.

- **StopAndroidPackage** &ndash; arresta completamente il pacchetto dell'applicazione nel dispositivo o nell'emulatore in esecuzione.

  che equivale a `adb shell am force-stop @PACKAGE_NAME@`.

  Aggiunto in Xamarin.Android 10.2.

- **Disinstalla** &ndash; Disinstalla il pacchetto dal dispositivo predefinito o dal dispositivo virtuale.

- **UpdateAndroidResources** &ndash; Aggiorna `Resource.designer.cs` il file. Questa destinazione viene in genere chiamata dall'IDE quando vengono aggiunte nuove risorse al progetto.

## <a name="build-extension-points"></a>Punti di estensione di compilazione

Il sistema di compilazione Xamarin.Android espone alcuni punti di estensione pubblici per gli utenti che desiderano eseguire l'hook nel processo di compilazione. Per usare uno di questi punti di estensione, sarà necessario aggiungere la destinazione personalizzata alla proprietà MSBuild appropriata in un `PropertyGroup`. Ad esempio:

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

Una parola di cautela sull'estensione del processo di compilazione: se non è scritto correttamente, le estensioni di compilazione possono influire sulle prestazioni di compilazione, soprattutto se vengono eseguite a ogni compilazione. È consigliabile leggere la [documentazione](https://docs.microsoft.com/visualstudio/msbuild/msbuild) di MSBuild prima di implementare tali estensioni.

- **AfterGenerateAndroidManifest** &ndash; Le destinazioni elencate in questa proprietà vengono eseguite immediatamente dopo la destinazione `_GenerateJavaStubs` interna. Questa è la posizione in cui viene generato il file `AndroidManifest.xml` in `$(IntermediateOutputPath)`. Se si vogliono apportare modifiche al file `AndroidManifest.xml` generato, quindi, è possibile farlo usando questo punto di estensione.

  Aggiunta in Xamarin.Android 9.4.

- **BeforeGenerateAndroidManifest** &ndash; Le destinazioni elencate in questa proprietà verranno eseguite direttamente prima di `_GenerateJavaStubs`.

  Aggiunta in Xamarin.Android 9.4.

## <a name="build-properties"></a>Proprietà di compilazione

Le proprietà MSBuild controllano il comportamento delle destinazioni. Sono specificate nel file di progetto, ad esempio **MyApp.csproj**, in un [elemento PropertyGroup MSBuild](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **** Configuration&ndash;: specifica la configurazione della build da usare, ad esempio "Debug" o "Release" (Rilascio). La proprietà Configuration viene usata per determinare i valori predefiniti per le altre proprietà che determinano il comportamento delle destinazioni. Configurazioni aggiuntive possono essere create nell'IDE.

  *Per impostazione predefinita*, con la configurazione `Debug` le destinazioni `Install` e `SignAndroidPackage` creeranno un pacchetto Android più piccolo per il cui funzionamento è necessaria la presenza di altri file e pacchetti.

  Con la configurazione `Release` predefinita, le destinazioni `Install` e `SignAndroidPackage` creeranno un pacchetto Android *autonomo* e possono essere usate senza installare altri pacchetti o file.

- **DebugSymbols** &ndash; Un valore booleano che determina se il pacchetto `$(DebugType)` Android è in grado di eseguire il *debug,* in combinazione con la proprietà . Un pacchetto sottoponibile a debug contiene simboli di debug, imposta l'attributo `//application/@android:debuggable` su `true` e aggiunge automaticamente l'autorizzazione `INTERNET` in modo che un debugger possa connettersi al processo. Un'applicazione può essere sottoposta a debug se `DebugSymbols` è `True` *e* `DebugType` è una stringa vuota o `Full`.

- **DebugType** &ndash; Specifica il [tipo di simboli di debug](https://docs.microsoft.com/visualstudio/msbuild/csc-task) da generare come parte della compilazione, che influisce anche sul fatto che l'applicazione è possibile eseguire il debug. I valori possibili sono:

  - **Full**: vengono generati simboli completi. Se anche la proprietà MSBuild `DebugSymbols` è `True`, il pacchetto dell'applicazione può essere sottoposto a debug.

  - **PdbOnly**: vengono generati simboli "PDB". Il pacchetto dell'applicazione *non* potrà essere sottoposto a debug.

  Se `DebugType` non è impostato o è una stringa vuota, la proprietà `DebugSymbols` controlla se l'applicazione può essere sottoposta o meno a debug.

  - **AndroidGenerateLayoutBindings** &ndash; abilita la generazione di [layout code-behind](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) se impostato su `true` o la disabilita completamente se impostato su `false`. Il valore predefinito è `false`.

### <a name="install-properties"></a>Proprietà di installazione

Le proprietà di installazione controllano il comportamento delle destinazioni `Install` e `Uninstall`.

- **** AdbTarget&ndash;: specifica il dispositivo di destinazione Android in cui il pacchetto Android può essere installato o rimosso. Il valore di questa proprietà è lo stesso [ `adb` dell'opzione Dispositivo](https://developer.android.com/tools/help/adb.html#issuingcommands)di destinazione :

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>Proprietà per la creazione di pacchetti

Queste proprietà controllano la creazione del pacchetto Android e vengono usate dalle destinazioni `Install` e `SignAndroidPackage`.
Anche le [proprietà di firma](#Signing_Properties) sono rilevanti quando si creano pacchetti delle applicazioni di rilascio.

- **AndroidAotProfiles** &ndash; Una proprietà stringa che consente allo sviluppatore di aggiungere profili AOT dalla riga di comando. Si tratta di un elenco separato da punti e virgola o da virgole di percorsi assoluti.

  Aggiunto in Xamarin.Android 10.1.

- **AndroidApkDigestAlgorithm** &ndash; Valore stringa che specifica l'algoritmo di digest da usare con `jarsigner -digestalg`.

  Il valore predefinito è `SHA-256`. In Xamarin.Android 10.0 e versioni `SHA1`precedenti, il valore predefinito era .

  Aggiunta in Xamarin.Android 9.4.

- **AndroidApkSignerAdditionalArguments** &ndash; Proprietà stringa che consente allo sviluppatore di specificare argomenti aggiuntivi per lo strumento `apksigner`.

  Aggiunta in Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; Valore stringa che specifica l'algoritmo di firma da usare con `jarsigner -sigalg`.

  Il valore predefinito è `SHA256withRSA`. In Xamarin.Android 10.0 e versioni `md5withRSA`precedenti, il valore predefinito era .

  Aggiunta in Xamarin.Android 8.2.

- **AndroidApplication** &ndash; Un valore booleano che indica se il`True`progetto è per un'applicazione Android ( ) o per un progetto di libreria Android (`False` o non presente).

  In un pacchetto Android può essere presente un solo progetto con `<AndroidApplication>True</AndroidApplication>`. Questo purtroppo non è ancora stato verificato e possono presentarsi errori insoliti e difficili da rilevare relativi alle risorse Android.

- **AndroidApplicationJavaClass** &ndash; Il nome completo della classe `android.app.Application` Java da utilizzare al posto del momento in cui una classe eredita da [Android.App.Application](xref:Android.App.Application).

  Questa proprietà viene *other* in genere impostata `$(AndroidEnableMultiDex)` da altre proprietà, ad esempio il MSBuild proprietà.

  Aggiunto in Xamarin.Android 6.1.

- **AndroidBinUtilsPath** &ndash; Percorso di una directory contenente i `ld` [binutils][binutils] di Android, ad esempio , il linker nativo e `as`, l'assembler nativo. Questi strumenti fanno parte di Android NDK e sono inclusi anche nell'installazione di Xamarin.Android.

  Il valore predefinito è `$(MonoAndroidBinDirectory)\ndk\`.

  Aggiunto in Xamarin.Android 10.0.

  [binutils]: https://android.googlesource.com/toolchain/binutils/

- **AndroidBoundExceptionType** &ndash; Un valore stringa che specifica come devono essere propagate le eccezioni quando un tipo fornito da Xamarin.Android `Android.Runtime.JavaDictionary` implementa `System.Collections.IDictionary`un tipo o un'interfaccia .NET in termini di tipi Java, ad esempio `Android.Runtime.InputStreamInvoker` e `System.IO.Stream`, o e .

  - `Java`: il tipo di eccezione Java originale viene propagato così com'è.

    Ciò significa che, `InputStreamInvoker` ad esempio, `System.IO.Stream` non `Java.IO.IOException` implementa correttamente l'API perché può essere generata da `Stream.Read()` anziché `System.IO.IOException`.

    Questo è il comportamento di propagazione delle eccezioni in tutte le versioni di Xamarin.Android precedenti alla 10.2.This is the exception propagation behavior in all releases of Xamarin.Android prior to 10.2.

    Questo è il valore predefinito in Xamarin.Android 10.2.

  - `System`: il tipo di eccezione Java originale viene intercettato e di cui viene eseguito il wrapping in un tipo di eccezione .NET appropriato.

    Ciò significa che, `InputStreamInvoker` ad `System.IO.Stream`esempio, implementa correttamente e `Stream.Read()` *non* genererà `Java.IO.IOException` istanze.  (Si può invece lanciare un `System.IO.IOException` che ha un `Java.IO.IOException` come `Exception.InnerException` valore.)

    Questo diventerà il valore predefinito in Xamarin.Android 11.0.

  Aggiunto in Xamarin.Android 10.2.

- **** AndroidBuildApplicationPackage&ndash;: valore booleano indicante se creare e firmare il pacchetto (APK). Impostare questo valore su `True` equivale a usare la destinazione di compilazione [SignAndroidPackage](#Build_Targets).

  Il supporto per questa proprietà stato aggiunto dopo Xamarin.Android 7.1.

  Per impostazione predefinita, il valore della proprietà è `False`.

- **AndroidBundleConfigurationFile** &ndash; Specifica un nome file da utilizzare come file di configurazione per la compilazione di un pacchetto di app Android.AndroidBundleConfigurationFile Specifies a filename to use as a [configuration file][bundle-config-format] for `bundletool` when building an Android App Bundle. Questo file controlla alcuni aspetti della modalità di generazione degli APK dal bundle, ad esempio sulle dimensioni suddivise per produrre APK. Si noti che Xamarin.Android configura automaticamente alcune di queste impostazioni, incluso l'elenco delle estensioni di file per lasciare non compresso.

  Questa proprietà è `$(AndroidPackageFormat)` rilevante solo `aab`se è impostata su .

  Aggiunto in Xamarin.Android 10.3.

  [bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

- **AndroidDexTool** &ndash; Proprietà di tipo enum con valori validi di `dx` o `d8`. Indica quale compilatore [DEX][dex] di Android viene usato durante il processo di compilazione di Xamarin.Android.
  Attualmente il valore predefinito è `dx`. Per altre informazioni, vedere la documentazione su [D8 e R8][d8-r8].

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash; Proprietà booleana che determina se `desugar` è abilitato. Android attualmente non supporta tutte le funzionalità di Java 8 e la toolchain predefinita implementa nuove funzionalità del linguaggio eseguendo trasformazioni del bytecode, chiamate `desugar`, nell'output del compilatore `javac`. Il valore predefinito è `False` se si usa `AndroidDexTool=dx` e `True` se si usa `AndroidDexTool=d8`.

- **AndroidEnableGooglePlayStoreChecks** &ndash; Una proprietà bool che consente agli sviluppatori di disabilitare i seguenti controlli di Google Play Store: XA1004, XA1005 e XA1006. Questa operazione è utile per gli sviluppatori che non usano come destinazione Google Play Store e non vogliono eseguire tali controlli.

  Aggiunta in Xamarin.Android 9.4.

- **AndroidEnableMultiDex** &ndash; Proprietà booleana che determina se il supporto multi-dex verrà utilizzato o meno nell'oggetto finale. `.apk`

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

  Per impostazione predefinita, il valore della proprietà è `False`.

- **AndroidEnablePreloadAssemblies** &ndash; Proprietà booleana che controlla se tutti gli assembly gestiti inclusi all'interno del pacchetto dell'applicazione vengono o meno caricati durante l'avvio del processo.

  Se impostata su `True`, tutti gli assembly inclusi nel pacchetto dell'applicazione verranno caricati durante l'avvio del processo, prima che venga richiamato il codice dell'applicazione.
  Ciò è coerente con il comportamento di Xamarin.Android nelle versioni precedenti a Xamarin.Android 9.2.

  Se impostata su `False`, gli assembly verranno caricati solo all'occorrenza.
  Ciò consente un avvio più veloce delle applicazioni e il comportamento è anche più coerente con la semantica .NET desktop.  Per visualizzare il tempo risparmiato, impostare la proprietà di sistema `debug.mono.log` in modo da includere `timing` e cercare il messaggio `Finished loading assemblies: preloaded` all'interno di `adb logcat`.

  Le applicazioni o le librerie che usano l'inserimento delle dipendenze possono *richiedere* che questa proprietà sia `True`, se a loro volta richiedono che `AppDomain.CurrentDomain.GetAssemblies()` restituisca tutti gli assembly all'interno del bundle dell'applicazione, anche se l'assembly non sarebbe altrimenti necessario.

  Per impostazione predefinita, questo valore verrà impostato su `True`.

  Aggiunta in Xamarin.Android 9.2.

- **AndroidEnableProfiledAot** &ndash; Proprietà booleana che determina se i profili AOT vengono usati durante la compilazione anticipata.

  I profili sono elencati nel gruppo di elementi `AndroidAotProfile`. Questo ItemGroup contiene i profili predefiniti. Può essere sottoposto a override rimuovendo quelli esistenti e aggiungendo i propri profili AOT.

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 9.4.

  Per impostazione predefinita, il valore della proprietà è `False`.

- **AndroidEnableSGenConcurrent** &ndash; Proprietà booleana che determina se verrà utilizzato l'agente di [raccolta dati GC simultaneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) di Mono.

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 7.2.

  Per impostazione predefinita, il valore della proprietà è `False`.

- **AndroidErrorOnCustomJavaObject** &ndash; Una proprietà booleana che `Android.Runtime.IJavaObject` 
  determina se i tipi possono implementare *senza* ereditare anche da `Java.Lang.Object` o `Java.Lang.Throwable`:

  ```csharp
  class BadType : IJavaObject {
      public IntPtr Handle {
          get {return IntPtr.Zero;}
      }

      public void Dispose()
      {
      }
  }
  ```

  Se True, tali tipi genereranno un errore XA4212. In caso contrario, verrà generato un avviso XA4212.

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 8.1.

  Per impostazione predefinita, il valore della proprietà è `True`.

- **AndroidExtraAotOptions** &ndash; Una proprietà stringa che consente di passare `Aot` opzioni aggiuntive al `$(AndroidEnableProfiledAot)` `$(AotAssemblies)` compilatore `true`Mono durante l'attività per i progetti che sono stati impostati su . Il valore stringa della proprietà viene aggiunto al file di risposta quando si chiama il compilatore incrociato Mono.

  In generale, questa proprietà deve essere lasciata vuota, ma in alcuni scenari speciali potrebbe fornire flessibilità utile.

  Si noti che questa `$(AndroidAotAdditionalArguments)` proprietà è diversa dalla proprietà correlata. Tale proprietà inserisce argomenti delimitati da virgole nell'opzione `--aot` del compilatore Mono. `$(AndroidExtraAotOptions)`passa invece opzioni separate da `--verbose` spazi `--debug` autonomi completi come o al compilatore.

  Aggiunto in Xamarin.Android 10.2.

- **AndroidFastDeploymentType** &ndash; `:` Un elenco separato da (colon) di valori per controllare quali tipi possono essere `$(EmbedAssembliesIntoApk)` distribuiti nella `False`directory di [distribuzione rapida](#Fast_Deployment) nel dispositivo di destinazione quando la proprietà MSBuild è . Se una risorsa viene distribuita con Fast Deployment, *non* viene incorporata nel file `.apk` generato, con la possibilità di accelerare i tempi di distribuzione. (Più che viene distribuito rapidamente, `.apk` quindi meno frequentemente è necessario ricompilare e il processo di installazione può essere più veloce.) I valori validi includono:Valid values include:

  - `Assemblies`: distribuisce gli assembly dell'applicazione.

  - `Dexes`: distribuisce i file `.dex`, le risorse Android e gli asset Android. **Questo valore può essere usato *solo* nei dispositivi che eseguono Android 4.4 o versione successiva (API 19).**

  Il valore predefinito è `Assemblies`.

  **Esperimento**. Aggiunto in Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods** &ndash; Proprietà booleana che abilita la generazione di metodi di marshalling JNI come parte del processo di compilazione. Questo riduce notevolmente l'uso di System.Reflection nel codice dell'helper di binding.

  L'impostazione predefinita è False. Se gli sviluppatori vogliono usare la nuova funzionalità dei metodi di marshalling JNI, possono impostare

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  nel proprio file csproj. In alternativa, specificare la proprietà sulla riga di comando usando

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **Esperimento**. Aggiunta in Xamarin.Android 9.2.
  Il valore predefinito è False.

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; Proprietà stringa che può essere usata per aggiungere altri parametri alla chiamata di `jnimarshalmethod-gen.exe`.  Questo è utile per il debug, poiché si possono usare opzioni come `-v`, `-d` o `--keeptemp`.

  Il valore predefinito è una stringa vuota. Può essere impostato nel file csproj o nella riga di comando. Ad esempio:

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  oppure:

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  Aggiunta in Xamarin.Android 9.2.

- **AndroidHttpClientHandlerType** &ndash; Controlla l'implementazione predefinita di `System.Net.Http.HttpMessageHandler` che verrà usata dal costruttore predefinito `System.Net.Http.HttpClient`. Il valore è un nome di `HttpMessageHandler` tipo qualificato dall'assembly di una sottoclasse, adatto per l'utilizzo con [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).
  I valori più comuni per questa proprietà sono:

  - `Xamarin.Android.Net.AndroidClientHandler`: utilizzare le API Java Android per eseguire le richieste di rete. Ciò consente l'accesso agli URL TLS 1.2 quando la versione di Android sottostante supporta TLS 1.2. Solo Android 5.0 e versioni successive offrono un supporto affidabile di TLS 1.2 tramite Java.

    Corrisponde all'opzione **Android** nelle pagine delle proprietà di Visual Studio e all'opzione **AndroidClientHandler** nelle pagine delle proprietà Visual Studio per Mac.

    La creazione guidata nuovo progetto seleziona questa opzione per i nuovi progetti quando l'opzione **Versione minima di Android** viene impostata su **Android 5.0 (Lollipop)** o versioni successive in Visual Studio o quando l'opzione **Piattaforme di destinazione** viene impostata su **Più recente e migliore** in Visual Studio per Mac.

  - Non impostato/la stringa vuota: è equivalente a`System.Net.Http.HttpClientHandler, System.Net.Http`

    Corrisponde all'opzione **Predefinito** nelle pagine delle proprietà di Visual Studio.

    La creazione guidata nuovo progetto seleziona questa opzione per i nuovi progetti quando l'opzione **Versione minima di Android** viene impostata su **Android 4.4.87** o versioni precedenti in Visual Studio o quando l'opzione **Piattaforme di destinazione** viene impostata su **Sviluppo moderno** o **Compatibilità massima** in Visual Studio per Mac.

  - `System.Net.Http.HttpClientHandler, System.Net.Http`: utilizzare `HttpMessageHandler`il file gestito .

    Corrisponde all'opzione **Gestito** nelle pagine delle proprietà di Visual Studio.

  > [!NOTE]
  > se è richiesto il supporto di TLS 1.2 nelle versioni di Android precedenti alla 5.0 *oppure* se è richiesto il supporto di TLS 1.2 con `System.Net.WebClient` e le API correlate, è necessario usare `$(AndroidTlsProvider)`.

  > [!NOTE]
  > Il supporto per questa [ `XA_HTTP_CLIENT_HANDLER_TYPE` ](~/android/deploy-test/environment.md)proprietà funziona impostando la variabile di ambiente .
  > Un eventuale valore `$XA_HTTP_CLIENT_HANDLER_TYPE` trovato in un file con un'azione di compilazione `@(AndroidEnvironment)` avrà la precedenza.

  Aggiunto in Xamarin.Android 6.1.

- **AndroidLinkMode** &ndash; Specifica il tipo di collegamento deve essere eseguito sugli assembly contenuti nel pacchetto Android.AndroidLinkMode Specifies which type of [linking](~/android/deploy-test/linker.md) should be performed on assemblies contained within the Android package. Usato solo nei progetti Applicazione Android. Il valore predefinito è *SdkOnly*. I valori validi sono:

  - **None**: non verranno tentati collegamenti.

  - **SdkOnly**: il collegamento verrà eseguito solo nelle librerie di classi base e non negli assembly dell'utente.

  - **Full**: il collegamento verrà eseguito nelle librerie di classi base e negli assembly dell'utente.

    > [!NOTE]
    > se si usa *Full* come valore di `AndroidLinkMode`, spesso le app vengono interrotte, soprattutto quando viene usata la reflection. Evitarlo a meno che non se ne sia *davvero* certi.

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip** &ndash; Specifica un elenco delimitato`;`da punti e virgola ( ) di nomi di assembly, senza estensioni di file, di assembly che non devono essere collegati. Usato solo nei progetti Applicazione Android.

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool** &ndash; Proprietà di tipo enum con valori validi di `proguard` o `r8`. Indica quale strumento viene usato per compattare il codice Java. Al momento l'impostazione predefinita è una stringa vuota o `proguard` se `$(AndroidEnableProguard)` è `True`. Per altre informazioni, vedere la documentazione su [D8 e R8][d8-r8].

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** &ndash; Una proprietà bool che consente `lint` allo sviluppatore di eseguire lo strumento Android come parte del processo di creazione di pacchetti.

  - **AndroidLintEnabledIssues** &ndash; Elenco di valori delimitati da virgole per i problemi di lint da abilitare.

  - **AndroidLintDisabledIssues** &ndash; Elenco di valori delimitati da virgole per i problemi di lint da disabilitare.

  - **AndroidLintCheckIssues** &ndash; Elenco di valori delimitati da virgole per i problemi di lint da controllare.
    Nota: verranno controllati solo questi problemi.

  - **AndroidLintConfig** &ndash; Si tratta di un'azione di compilazione per un file di configurazione di tipo lint. Può essere usata per abilitare o disabilitare i problemi da controllare. L'azione di compilazione può essere usata da più file poiché i contenuti dei file verranno uniti.

  Vedere la [guida di Lint](https://developer.android.com/studio/write/lint) per maggiori dettagli sullo strumento `lint` di Android.

- **AndroidManagedSymbols** &ndash; Una proprietà booleana che controlla se i punti di sequenza `Release` vengono generati in modo che le informazioni sul nome file e sul numero di riga possano essere estratte dalle tracce dello stack.

  Aggiunto in Xamarin.Android 6.1.

- **AndroidManifest** &ndash; Specifica un nome file da utilizzare come [`AndroidManifest.xml`](~/android/platform/android-manifest.md)modello per l'app.
  Durante la compilazione, verranno uniti gli altri valori necessari per generare il file `AndroidManifest.xml` effettivo.
  `$(AndroidManifest)` deve contenere il nome del pacchetto nell'attributo `/manifest/@package`.

- **AndroidManifestMerger** &ndash; Specifica l'implementazione per l'unione di file *AndroidManifest.xml.* Si tratta di una proprietà `legacy` di tipo enum `manifestmerger.jar` in cui seleziona l'implementazione originale di C e seleziona l'implementazione Java di Google.

  Il valore predefinito `legacy`è attualmente . Questo cambierà `manifestmerger.jar` in una versione futura per allineare il comportamento con Android Studio.

  La fusione di `xmlns:tools="http://schemas.android.com/tools"` Google consente il supporto per come descritto nella [documentazione][manifest-merger]di Android .

  Introdotto in Xamarin.Android 10.2

  [manifest-merger]: https://developer.android.com/studio/build/manifest-merge

- **AndroidMultiDexClassListExtraArgs** &ndash; Una proprietà stringa che consente agli `com.android.multidex.MainDexListBuilder` sviluppatori di `multidex.keep` passare argomenti aggiuntivi per il durante la generazione del file.

  Un caso specifico è se si riceve l'errore seguente durante la compilazione di `dx`.

  ```
  com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
  ```

  Se si riceve questo errore, è possibile aggiungere quanto segue al file csproj.

  ```xml
  <DxExtraArguments>--force-jumbo </DxExtraArguments>
  <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
  ```

  in questo modo il passaggio `dx` ha esito positivo.

  Aggiunta in Xamarin.Android 8.3.

- **AndroidPackageFormat** &ndash; Proprietà di tipo enum con valori validi `apk` o `aab`. Indica se si vuole creare il pacchetto dell'applicazione Android come [file APK][apk] o come [bundle di app Android][bundle]. I bundle di app sono un nuovo formato per le compilazioni `Release` destinate all'invio in Google Play. L'impostazione predefinita di questo valore è attualmente `apk`.

  Quando `$(AndroidPackageFormat)` è impostato su `aab`, vengono impostate altre proprietà di MSBuild, necessarie per i bundle di app Android:

  - `$(AndroidUseAapt2)` è `True`.
  - `$(AndroidUseApkSigner)` è `False`.
  - `$(AndroidCreatePackagePerAbi)` è `False`.

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidPackageNamingPolicy** &ndash; Una proprietà di tipo enum per specificare i nomi dei pacchetti Java del codice sorgente Java generato.

  In Xamarin.Android 10.2 e versioni successive, l'unico valore supportato è `LowercaseCrc64`.

  In Xamarin.Android 10.1, `LowercaseMD5` era disponibile anche un valore di transizione che consentiva di tornare allo stile originale del nome del pacchetto Java utilizzato in Xamarin.Android 10.0 e versioni precedenti. Questa opzione è stata rimossa in Xamarin.Android 10.2 per migliorare la compatibilità con gli ambienti di compilazione per cui è stata applicata la conformità FIPS.

  Aggiunto in Xamarin.Android 10.1.

- **AndroidR8JarPath** &ndash; Il percorso a `r8.jar` per l'uso con il compilatore DEX r8 e lo strumento di compattazione. Il valore predefinito è un percorso nell'installazione di Xamarin.Android. Per altre informazioni, vedere la documentazione su [D8 e R8][d8-r8].

- **AndroidSdkBuildToolsVersion** &ndash; Il pacchetto degli strumenti di compilazione di Android SDK fornisce, tra gli altri, gli strumenti **aapt** e **zipalign.** Più versioni diverse del pacchetto di strumenti di compilazione possono essere installate contemporaneamente. Il pacchetto di strumenti di compilazione scelto per la creazione di pacchetti viene creato cercando e usando una versione "preferita" degli strumenti di compilazione, se presente. Se la versione "preferita" *non* è presente, viene usato il pacchetto di strumenti di compilazione installato con la versione superiore.

  La proprietà MSBuild `$(AndroidSdkBuildToolsVersion)` contiene la versione preferita degli strumenti di compilazione. Il sistema di compilazione Xamarin.Android specifica un valore predefinito in `Xamarin.Android.Common.targets` ed è possibile eseguire l'override del valore predefinito nel file di progetto per scegliere una versione alternativa degli strumenti di compilazione, se, ad esempio, lo strumento aapt più recente si arresta in modo anomalo mentre una versione di aapt precedente è sicuramente funzionante.

- **AndroidSupportedAbis** &ndash; Una proprietà stringa che`;`contiene un elenco delimitato da punti e `.apk`virgola ( ) di ACHE che deve essere incluso nell'oggetto .

  I valori supportati includono:

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a`: richiede Xamarin.Android 5.1 e versioni successive.
  - `x86_64`: richiede Xamarin.Android 5.1 e versioni successive.

- **** AndroidTlsProvider&ndash;: valore di stringa che specifica il provider TLS da usare in un'applicazione. I valori possibili sono:

  - Unset/la stringa vuota: in Xamarin.Android 7.3 e `btls`versioni successive, questo equivale a .

    in Xamarin.Android 7.1 equivale a `legacy`.

    Corrisponde all'impostazione **Predefinito** nelle pagine delle proprietà di Visual Studio.

  - `btls`: usa [Boring SSL](https://boringssl.googlesource.com/boringssl) per la comunicazione TLS con [HttpWebRequest](xref:System.Net.HttpWebRequest).

    Ciò consente l'uso di TLS 1.2 in tutte le versioni di Android.

    Corrisponde all'impostazione **TLS 1.2+ nativo** nelle pagine delle proprietà di Visual Studio.

  - `legacy`: in Xamarin.Android 10.1 e versioni precedenti, utilizzare l'implementazione SSL gestita storica per l'interazione di rete. *Non* supporta TLS 1.2.

    Corrisponde all'impostazione **TLS 1.0 gestito** nelle pagine delle proprietà di Visual Studio.

    In Xamarin.Android 10.2 e versioni successive, `btls` questo valore viene ignorato e viene utilizzata l'impostazione.

  - `default`: è improbabile che questo valore venga utilizzato nei progetti Xamarin.Android. Il valore consigliato da usare è invece la stringa vuota, che corrisponde all'impostazione **Predefinito** nelle pagine delle proprietà di Visual Studio.

    Il valore `default` non è disponibile nelle pagine delle proprietà di Visual Studio.

    Attualmente equivale a `legacy`.

  Aggiunto in Xamarin.Android 7.1.

- **AndroidUseApkSigner** &ndash; Una proprietà bool che consente allo `apksigner` sviluppatore `jarsigner`di utilizzare lo strumento anziché .

    Aggiunta in Xamarin.Android 8.2.

- **AndroidUseDefaultAotProfile** &ndash; proprietà che consente allo sviluppatore di sopprimere l'utilizzo dei profili AOT predefiniti.

  Per eliminare i profili AOT predefiniti, impostare la proprietà su `false`.

  Aggiunto in Xamarin.Android 10.1.

- **AndroidUseLegacyVersionCode** &ndash; Proprietà booleana che consente allo sviluppatore di ripristinare il comportamento precedente a Xamarin.Android 8.2 per i calcoli versionCode. Deve essere usata SOLO dagli sviluppatori con applicazioni esistenti in Google Play Store. È consigliabile usare la nuova proprietà `$(AndroidVersionCodePattern)`.

  Aggiunta in Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; Proprietà booleana che consente di passare all'uso del parser delle risorse gestito per le compilazioni in fase di progettazione invece di `aapt`.

  Aggiunta in Xamarin.Android 8.1.

- **AndroidUseSharedRuntime** &ndash; Una proprietà booleana che determina se i *pacchetti di runtime condivisi* sono necessari per eseguire l'applicazione nel dispositivo di destinazione. Basandosi sui pacchetti di runtime condiviso, è possibile ridurre le dimensioni del pacchetto dell'applicazione, velocizzando il processo di creazione e distribuzione del pacchetto e di conseguenza anche il ciclo di compilazione/distribuzione/debug.

  Questa proprietà deve essere `True` per le build di debug e `False` per i progetti di rilascio.

- **AndroidVersionCodePattern** &ndash; Una proprietà stringa che consente `versionCode` allo sviluppatore di personalizzare l'oggetto nel manifesto.
  Per informazioni utili per decidere riguardo a `versionCode`, vedere [Creazione del codice della versione per l'APK](~/android/deploy-test/building-apps/abi-specific-apks.md).

  Alcuni esempi: se `abi` è `armeabi` e `versionCode` nel manifesto è `123`, `{abi}{versionCode}` genererà un elemento versionCode `1123` quando `$(AndroidCreatePackagePerAbi)` è True. In caso contrario genererà il valore 123.
  Se `abi` è `x86_64` e `versionCode` nel manifesto è `44`, verrà generato `544` quando `$(AndroidCreatePackagePerAbi)` è True. In caso contrario, genererà il valore `44`.

  Se viene inclusa una stringa di formato con riempimento a sinistra `{abi}{versionCode:0000}`, genererà `50044` perché si riempie `versionCode` a sinistra con `0`. In alternativa, è possibile usare il riempimento decimale, ad esempio `{abi}{versionCode:D4}`
  che ha la stessa funzione dell'esempio precedente.

  Sono supportate solo le stringhe in formato con riempimento "0" e "Dx" perché il valore DEVE essere un intero.

  Elementi chiave predefiniti

  - **abi**  &ndash; Inserisce l'ABI di destinazione per l'app
    - 2 &ndash; Il nome del sistema`armeabi-v7a`
    - 3 &ndash; (COM del nome`x86`
    - 4 &ndash; DEL psu'`arm64-v8a`
    - 5 &ndash; Del numero 3(`x86_64`

  - **minSDK** &ndash; Inserisce il valore Sdk `AndroidManifest.xml` `11` minimo supportato da o se non è definito.  

  - **versionCode** &ndash; Usa il codice della versione direttamente da `Properties\AndroidManifest.xml`.

  È possibile definire gli elementi personalizzati usando la proprietà `$(AndroidVersionCodeProperties)` (definita successivamente).

  Per impostazione predefinita, il valore verrà impostato su `{abi}{versionCode:D6}`. Se uno sviluppatore vuole mantenere il comportamento precedente può ignorare l'impostazione predefinita impostando la proprietà `$(AndroidUseLegacyVersionCode)` su `true`

  Aggiunto in Xamarin.Android 7.2.

- **AndroidVersionCodeProperties** &ndash; Una proprietà stringa che consente allo sviluppatore `AndroidVersionCodePattern`di definire elementi personalizzati da utilizzare con l'oggetto . sotto forma di coppia `key=value`. Tutti gli elementi in `value` devono essere valori interi. Ad esempio: `screen=23;target=$(_AndroidApiLevel)`. Come si può osservare, è possibile usare nella stringa proprietà MSBuild esistenti o personalizzate.

  Aggiunto in Xamarin.Android 7.2.

- **AotAssemblies** &ndash; Una proprietà booleana che determina se gli assembly verranno compilati `.apk`in base al tempo in codice nativo e inclusi nell'oggetto .

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

  Per impostazione predefinita, il valore della proprietà è `False`.

- **EmbedAssembliesIntoApk** &ndash; Proprietà booleana che determina se gli assembly dell'app devono essere incorporati nel pacchetto dell'applicazione.

  Questa proprietà deve essere `True` per le build di rilascio e `False` per le build di debug. *Potrebbe* dover essere `True` nelle build di debug se Fast Deployment non supporta il dispositivo di destinazione.

  Quando questa proprietà è `False`, la proprietà MSBuild `$(AndroidFastDeploymentType)` controlla anche che cosa verrà incorporato in `.apk` e ciò può influire sui tempi di distribuzione e ricompilazione.

- **EnableLLVM** &ndash;Proprietà booleana che determina se LLVM verrà usato o meno durante la compilazione Ahead Of Time degli assembly nel codice nativo.

  Il NDK Android deve essere installato per compilare un progetto che ha questa proprietà abilitata.

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

  Per impostazione predefinita, il valore della proprietà è `False`.

  Questa proprietà viene ignorata a meno che la proprietà MSBuild `$(AotAssemblies)` non sia `True`.

- **EnableProguard** &ndash; Proprietà booleana che determina se [proguard](https://developer.android.com/tools/help/proguard.html) viene eseguito o meno come parte del processo di creazione del pacchetto per collegare il codice Java.

  Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

  Per impostazione predefinita, il valore della proprietà è `False`.

  Quando `True`, i file [ProguardConfiguration](#ProguardConfiguration) verranno utilizzati per controllare `proguard` l'esecuzione.

- **JavaMaximumHeapSize** &ndash; Specifica il valore del parametro **java** 
   `-Xmx` `.dex` da utilizzare durante la compilazione del file come parte del processo di creazione del pacchetto. Se il valore non è specificato, l'opzione `-Xmx` specifica per **java** il valore `1G`. Questi valori sono richiesti più comunemente in Windows rispetto ad altre piattaforme.

  Se si specifica questa proprietà, è necessario [ `_CompileDex` che `java.lang.OutOfMemoryError` ](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)la destinazione generi un oggetto .

  Personalizzare il valore modificando quanto segue:

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** &ndash; Specifica opzioni aggiuntive della riga **java** di comando `.dex` da passare a java durante la compilazione del file.

- **LinkerDumpDependencies** &ndash; Proprietà booleana che consente la generazione del file delle dipendenze del linker. Questo file può essere usato come input per lo strumento [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

  Il valore predefinito è False.

- **** MandroidI18n&ndash;: specifica il supporto di internazionalizzazione incluso con l'applicazione, ad esempio le regole di confronto e le tabelle di ordinamento. Il valore è un elenco separato da virgole o punti e virgola di uno o più dei valori seguenti senza distinzione tra maiuscole e minuscole:

  - **None**: non include codifiche aggiuntive.

  - **All**: include tutte le codifiche disponibili.

  - **CJK**: include le codifiche per cinese, giapponese e coreano, ad esempio *Giapponese (EUC)* \[enc-jp, CP51932\], *Giapponese (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *Giapponese (JIS)* \[CP50220\], *Cinese semplificato (GB2312)* \[gb2312, CP936\], *Coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *Coreano (EUC)* \[euc-kr, CP51949\], *Cinese tradizionale (Big5)* \[big5, CP950\] e *Cinese semplificato (GB18030)* \[GB18030, CP54936\].

  - **MidEast**: include le codifiche per il Medio Oriente, ad esempio *Turco (Windows)* \[iso-8859-9, CP1254\], *Ebraico (Windows)* \[windows-1255, CP1255\], *Arabo (Windows)* \[windows-1256, CP1256\], *Arabo (ISO)* \[iso-8859-6, CP28596\], *Ebraico (ISO)* \[iso-8859-8, CP28598\], *Latino 5 (ISO)* \[iso-8859-9, CP28599\] ed *Ebraico (ISO alternativo)* \[iso-8859-8, CP38598\].

  - **Other**: include le altre codifiche, ad esempio *Cirillico (Windows)* \[CP1251\], *Baltico (Windows)* \[iso-8859-4, CP1257\], *Vietnamita (Windows)* \[CP1258\], *Cirillico (KOI8-R)* \[koi8-r, CP1251\], *Ucraino (KOI8-U)* \[koi8-u, CP1251\], *Baltico (ISO)* \[iso-8859-4, CP1257\], *Cirillico (ISO)* \[iso-8859-5, CP1251\], *ISCII Devanagari* \[x-iscii-de, CP57002\], *ISCII Bengali* \[x-iscii-be, CP57003\], *ISCII Tamil* \[x-iscii-ta, CP57004\], *ISCII Telugu* \[x-iscii-te, CP57005\], *ISCII Assamese* \[x-iscii-as, CP57006\], *ISCII Odia* \[x-iscii-or, CP57007\], *ISCII Kannada* \[x-iscii-ka, CP57008\], *ISCII Malayalam* \[x-iscii-ma, CP57009\], *ISCII Gujarati* \[x-iscii-gu, CP57010\], *ISCII Punjabi* \[x-iscii-pa, CP57011\] e *Thai (Windows)* \[CP874\].

  - **Rare**: include le codifiche rare, ad esempio *IBM EBCDIC (Turco)* \[CP1026\], *IBM EBCDIC (Latino 1/Sistema aperto)* \[CP1047\], *IBM EBCDIC (Stati Uniti-Canada-Europa)* \[CP1140\], *IBM EBCDIC (Germania-Europa)* \[CP1141\], *IBM EBCDIC (Danimarca/Norvegia-Europa)* \[CP1142\], *IBM EBCDIC (Finlandia/Svezia-Europa)* \[CP1143\], *IBM EBCDIC (Italia-Europa)* \[CP1144\], *IBM EBCDIC (America Latina/Spagna-Europa)* \[CP1145\], *IBM EBCDIC (Regno Unito-Europa)* \[CP1146\], *IBM EBCDIC (Francia-Europa)* \[CP1147\], *IBM EBCDIC (Internazionale-Europa)* \[CP1148\], *IBM EBCDIC (Islandese-Europa)* \[CP1149\], *IBM EBCDIC (Germania)* \[CP20273\], *IBM EBCDIC (Danimarca/Norvegia)* \[CP20277\], *IBM EBCDIC (Finlandia/Svezia)* \[CP20278\], *IBM EBCDIC (Italia)* \[CP20280\], *IBM EBCDIC (America Latina/Spagna)* \[CP20284\], *IBM EBCDIC (Regno Unito)* \[CP20285\], *IBM EBCDIC (Giapponese Katakana esteso)* \[CP20290\], *IBM EBCDIC (Francia)* \[CP20297\], *IBM EBCDIC (Arabo)* \[CP20420\], *IBM EBCDIC (Ebraico)* \[CP20424\], *IBM EBCDIC (Islandese)* \[CP20871\], *IBM EBCDIC (Cirillico - Serbo, Bulgaro)* \[CP21025\], *IBM EBCDIC (Stati Uniti-Canada)* \[CP37\], *IBM EBCDIC (Internazionale)* \[CP500\], *Arabo (ASMO 708)* \[CP708\], *Europa centrale (DOS)* \[CP852\]*, Cirillico (DOS)* \[CP855\], *Turco (DOS)* \[CP857\], *Europa occidentale (DOS-Europa)* \[CP858\], *Ebraico (DOS)* \[CP862\], *Arabo (DOS)* \[CP864\], *Russo (DOS)* \[CP866\], *Greco (DOS)* \[CP869\], *IBM EBCDIC (Latino 2)* \[CP870\] e *IBM EBCDIC (Greco)* \[CP875\].

  - **West**: include le codifiche per l'area occidentale, ad esempio *Europa occidentale (Mac)* \[macintosh, CP10000\], *Islandese (Mac)* \[x-mac-icelandic, CP10079\], *(Windows)* \[iso-8859-2, CP1250\], *Europa occidentale (Windows)* \[iso-8859-1, CP1252\], *Greco (Windows)* \[iso-8859-7, CP1253\], *Europa centrale (ISO)* \[iso-8859-2, CP28592\], *Latino 3 (ISO)* \[iso-8859-3, CP28593\], *Greco (ISO)* \[iso-8859-7, CP28597\], *Latino 9 (ISO)* \[iso-8859-15, CP28605\], *OEM Stati Uniti* \[CP437\], *Europa occidentale (DOS)* \[CP850\], *Portoghese (DOS)* \[CP860\], *Islandese (DOS)* \[CP861\], *(DOS)* \[CP863\] e *Nordico (DOS)* \[CP865\].

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive** &ndash; `.mSYM` Una proprietà booleana che controlla se `mono-symbolicate`gli &ldquo;elementi vengono creati per un utilizzo successivo con , per estrarre informazioni reali&rdquo; sul nome file e sul numero di riga dalle tracce dello stack di rilascio.

  Per impostazione predefinita, è True per le app &ldquo;Release&rdquo; con i simboli di debug abilitati: `$(EmbedAssembliesIntoApk)` è True, `$(DebugSymbols)` è True e `$(Optimize)` è True.

  Aggiunto in Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Proprietà di compilazione del progetto di binding

Le proprietà MSBuild seguenti vengono usate con i [progetti di binding](~/android/platform/binding-java-library/index.md):

- **AndroidClassParser** &ndash; Una proprietà stringa `.jar` che controlla la modalità di analisi dei file. I valori possibili sono:

  - **class-parse**: usa `class-parse.exe` per analizzare direttamente il bytecode Java senza ricorrere a JVM. Questo valore è sperimentale.

  - **jar2xml**: usa `jar2xml.jar` per estrarre tipi e membri da un file `.jar` con la reflection Java.

  I vantaggi di `class-parse` rispetto a `jar2xml` sono:

  - `class-parse`è in grado di estrarre i *debug* nomi dei parametri dal bytecode `javac -g`Java che contiene simboli di debug (ad esempio bytecode compilato con ).

  - `class-parse` non ignora le classi che ereditano o contengono membri di tipi non risolvibili.

  **Esperimento**. Aggiunto in Xamarin.Android 6.0.

  Il valore predefinito è `jar2xml`.

  Il valore predefinito cambierà in una versione futura.

- **** AndroidCodegenTarget&ndash;: proprietà stringa che controlla l'ABI di destinazione della generazione del codice. I valori possibili sono:

  - **XamarinAndroid**: utilizza l'API di associazione JNI presente da Mono per Android 1.0. Gli assembly di binding compilati con Xamarin.Android 5.0 o versioni successive possono essere eseguiti solo in Xamarin.Android 5.0 o versioni successive (aggiunte API/ABI), ma l'*origine* è compatibile con le versioni precedenti del prodotto.

  - **XAJavaInterop1**: usa Java.Interop per le chiamate JNI. Gli assembly di binding che usano `XAJavaInterop1` possono solo essere compilati ed eseguiti con Xamarin.Android 6.1 o versioni successive. Xamarin.Android 6.1 e versioni successive eseguono il binding di `Mono.Android.dll` con questo valore.

    I vantaggi di `XAJavaInterop1` includono:

    - Assembly di dimensioni ridotte.

    - Memorizzazione nella cache `jmethodID` per le chiamate ai metodi `base`, purché tutti gli altri tipi di binding nella gerarchia di ereditarietà siano compilati con `XAJavaInterop1` o versione successiva.

    - Memorizzazione nella cache `jmethodID` di costruttori di Java Callable Wrapper per sottoclassi gestite.

    Il valore predefinito è `XAJavaInterop1`.

### <a name="resource-properties"></a>Proprietà delle risorse

Le proprietà risorsa controllano la generazione del file `Resource.designer.cs`, che fornisce l'accesso alle risorse Android.

- **AndroidAapt2CompileExtraArgs: ** &ndash; Specifica opzioni aggiuntive della riga di comando da passare al comando **aapt2 compile** quando si elaborano le risorse e gli asset di Android.

  Aggiunta in Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs** &ndash; Specifica opzioni aggiuntive della riga di comando da passare al comando **aapt2 link** quando si elaborano le risorse e gli asset di Android.

  Aggiunta in Xamarin.Android 9.1.

- **** AndroidExplicitCrunch&ndash;: se si compila un'app con un numero molto elevato di risorse drawable locali, il completamento di una compilazione iniziale (o di una ricompilazione) può richiedere alcuni minuti. Per accelerare il processo di compilazione, provare a includere questa proprietà e a impostarla su `True`. Quando questa proprietà è impostata, il processo di compilazione analizza in anticipo i file PNG.

  Nota: questa opzione non è compatibile con l'opzione `$(AndroidUseAapt2)`. Se `$(AndroidUseAapt2)` è abilitata, questa funzionalità verrà disabilitata. Per continuare a usare questa funzionalità, impostare `$(AndroidUseAapt2)` su `False`.

  **Esperimento**. Aggiunto in Xamarin.Android 7.0.

- **AndroidResgenExtraArgs** &ndash; Specifica opzioni della riga di comando aggiuntive da passare al comando aapt durante l'elaborazione di risorse e risorse Android.AndroidResgenExtraArgs Specifies additional command-line options to pass to the **aapt** command when processing Android assets and resources.

- **** AndroidResgenFile&ndash;: specifica il nome del file di risorse da generare. Il modello predefinito lo imposta su `Resource.designer.cs`.

- **AndroidUseAapt2** &ndash; Proprietà booleana che consente allo sviluppatore di controllare l'uso dello strumento `aapt2` per la creazione di pacchetti.
  L'impostazione predefinita è False e si userà `aapt`.
  Se lo sviluppatore vuole usare la nuova funzionalità `aapt2`può impostarla

  ```xml
  <AndroidUseAapt2>True</AndroidUseAapt2>
  ```

  nel proprio file csproj. In alternativa, specificare la proprietà sulla riga di comando usando

  ```
  /p:AndroidUseAapt2=True
  ```

  Aggiunta in Xamarin.Android 8.3.

- **MonoAndroidResourcePrefix** &ndash; Specifica un *prefisso* di percorso che viene rimosso dall'inizio dei nomi di file con un'azione di compilazione pari a `AndroidResource`. per poter modificare la posizione delle risorse.

  Il valore predefinito è `Resources`. Impostarlo su `res` per la struttura del progetto Java.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>Proprietà di firma

Le proprietà di firma controllano come viene firmato il pacchetto dell'applicazione in modo che possa essere installato in un dispositivo Android. Per consentire un'iterazione della compilazione più rapida, le attività di Xamarin.Android non firmano i pacchetti durante il processo di compilazione perché la firma è piuttosto lenta. Vengono invece firmate (se necessario) prima dell'installazione o durante l'esportazione dall'IDE o dalla destinazione di compilazione *Install*. La chiamata alla destinazione *SignAndroidPackage* genererà un pacchetto con il suffisso `-Signed.apk` nella directory di output.

Per impostazione predefinita, la destinazione di firma genera una nuova chiave di firma di debug, se necessario. Per usare una chiave specifica, ad esempio in un server di compilazione, si possono usare le proprietà MSBuild seguenti:

- **AndroidDebugKeyAlgorithm** &ndash; Specifica l'algoritmo predefinito da usare per `debug.keystore`. Il valore predefinito è `RSA`.

- **AndroidDebugKeyValidity** &ndash; Specifica la validità predefinita da usare per `debug.keystore`. Il valore predefinito è `10950` o `30 * 365` o `30 years`.

- **AndroidDebugStoreType** &ndash; Specifica il formato di file `debug.keystore`dell'archivio chiavi da utilizzare per l'oggetto . Il valore predefinito è `pkcs12`.

  Aggiunto in Xamarin.Android 10.2.

- **** AndroidKeyStore&ndash;: valore booleano indicante se devono essere usate informazioni sulla firma personalizzate. Il valore predefinito è `False`, indicante che per la firma dei pacchetti verrà usata la chiave di firma di debug.

- **** AndroidSigningKeyAlias&ndash;: specifica l'alias per la chiave nell'archivio chiavi. Si tratta del valore **keytool -alias** usato durante la creazione dell'archivio chiavi.

- **** AndroidSigningKeyPass&ndash;: specifica la password della chiave nel file dell'archivio chiavi. Si tratta del valore immesso quando `keytool` chiede di **immettere la password della chiave per $(AndroidSigningKeyAlias)**.

  In Xamarin.Android 10.0 e versioni precedenti, questa proprietà supporta solo password in testo normale.

  In Xamarin.Android 10.1 e versioni successive, questa proprietà supporta anche `env:` e `file:` prefissi che possono essere utilizzati per specificare una variabile di ambiente o un file che contiene la password. Queste opzioni consentono di impedire la visualizzazione della password nei log di compilazione.

  Ad esempio, per usare una variabile di ambiente denominata *AndroidSigningPassword:*

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  Per utilizzare un `C:\Users\user1\AndroidSigningPassword.txt`file situato in:

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > Il `env:` prefisso non `$(AndroidPackageFormat)` è `aab`supportato quando è impostato su .

- **AndroidSigningKeyStore** &ndash; Specifica il nome del file `keytool`keystore creato da . Corrisponde al valore fornito all'opzione **keytool -keystore**.

- **AndroidSigningStorePass** &ndash; Specifica la `$(AndroidSigningKeyStore)`password per . Si tratta del valore fornito a `keytool` quando si è creato il file dell'archivio chiavi ed è stato chiesto di **immettere la password dell'archivio chiavi**.

  In Xamarin.Android 10.0 e versioni precedenti, questa proprietà supporta solo password in testo normale.

  In Xamarin.Android 10.1 e versioni successive, questa proprietà supporta anche `env:` e `file:` prefissi che possono essere utilizzati per specificare una variabile di ambiente o un file che contiene la password. Queste opzioni consentono di impedire la visualizzazione della password nei log di compilazione.

  Ad esempio, per usare una variabile di ambiente denominata *AndroidSigningPassword:*

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  Per utilizzare un `C:\Users\user1\AndroidSigningPassword.txt`file situato in:

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > Il `env:` prefisso non `$(AndroidPackageFormat)` è `aab`supportato quando è impostato su .

- **JarsignerTimestampAuthorityCertificateAlias** &ndash; Questa proprietà consente di specificare un alias nell'archivio chiavi per un'autorità timestamp.
  Per ulteriori dettagli, vedere la documentazione relativa al [supporto timestamp per](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) la firma Java.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
  </PropertyGroup>
  ```

- **JarsignerTimestampAuthorityUrl** &ndash; Questa proprietà consente di specificare un URL a un servizio di autorità timestamp. Può essere utilizzato per `.apk` assicurarsi che la firma includa un timestamp.
  Per ulteriori dettagli, vedere la documentazione relativa al [supporto timestamp per](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) la firma Java.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
  </PropertyGroup>
  ```

Si consideri ad esempio la chiamata a `keytool` seguente:

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

Per usare il file dell'archivio chiavi generato sopra, usare il gruppo di proprietà:

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

<a name="Build_Actions" />

## <a name="build-actions"></a>Azioni di compilazione

Le *azioni di compilazione* vengono [applicate ai file](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items) del progetto e controllano l'elaborazione del file.

### <a name="androidaarlibrary"></a>AndroidAarLibrary

L'azione di compilazione `AndroidAarLibrary` deve essere usata per fare riferimento direttamente ai file con estensione aar. Questa azione di compilazione verrà usata più comunemente dai componenti di Xamarin, in particolare per includere riferimenti ai file con estensione aar necessari per il funzionamento di Google Play e altri servizi.

I file con questa azione di compilazione verranno gestiti in modo simile alle risorse incorporate nei progetti di libreria. Il file con estensione aar verrà estratto nella directory intermedia. Quindi qualsiasi asset, risorsa e file con estensione jar verrà incluso nei gruppi di elementi appropriati.

### <a name="androidboundlayout"></a>AndroidBoundLayout

Indica che per il file di layout deve essere generato code-behind quando la proprietà `AndroidGenerateLayoutBindings` è impostata su `false`. In tutti gli altri aspetti è identica all'azione `AndroidResource` descritta in precedenza. Questa azione può essere usata **solo** con i file di layout:

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

I file con un'azione di compilazione `AndroidEnvironment` vengono usati per [inizializzare le variabili di ambiente e le proprietà di sistema durante l'avvio del processo](~/android/deploy-test/environment.md).
L'azione di compilazione `AndroidEnvironment` può essere applicata a più file che verranno valutati in ordine casuale, quindi non specificare la stessa variabile di ambiente o proprietà di sistema in più file.

### <a name="androidfragmenttype"></a>AndroidFragmentType

Specifica il tipo predefinito completo da usare per tutti gli elementi `<fragment>` del layout quando viene generato il codice dei binding di layout. Il valore predefinito della proprietà è il tipo `Android.App.Fragment` standard di Android.

### <a name="androidjavalibrary"></a>AndroidJavaLibrary

I file con un'azione di compilazione `AndroidJavaLibrary` sono archivi Java (file `.jar`) che verranno inclusi nel pacchetto Android finale.

### <a name="androidjavasource"></a>AndroidJavaSource

I file con un'azione di compilazione `AndroidJavaSource` sono codice sorgente Java che verrà incluso nel pacchetto Android finale.

### <a name="androidlintconfig"></a>AndroidLintConfig

L'azione di compilazione "AndroidLintConfig" deve essere usata in combinazione con la proprietà di compilazione `AndroidLintEnabled`. I file con questa azione di compilazione verranno uniti e passati allo strumento `lint` di Android. Devono essere file XML che contengono informazioni sui test da abilitare e disabilitare.

Vedere la [documentazione di Lint](https://developer.android.com/studio/write/lint) per maggiori dettagli.

### <a name="androidnativelibrary"></a>AndroidNativeLibrary

Le [librerie native](~/android/platform/native-libraries.md) vengono aggiunte alla compilazione impostandone l'azione di compilazione su `AndroidNativeLibrary`.

Si noti che, poiché Android supporta più interfacce binarie dell'applicazione (ABI), il sistema di compilazione deve sapere per quale ABI viene compilata per la libreria nativa. Esistono due modi per farlo:

1. Analisi del percorso.
2. Uso dell'attributo dell'elemento `Abi`.

Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Se quindi si aggiunge `lib/armeabi-v7a/libfoo.so` alla compilazione, l'ABI verrà analizzato come `armeabi-v7a`.

#### <a name="item-attribute-name"></a>Nome dell'attributo dell'elemento

**** Abi&ndash;: specifica l'ABI della libreria nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

### <a name="androidresource"></a>AndroidResource

Tutti i file con un'azione di compilazione *AndroidResource* vengono compilati nelle risorse Android durante il processo di compilazione e resi accessibili tramite `$(AndroidResgenFile)`.

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

Gli utenti più avanzati potrebbero aver bisogno di usare risorse diverse a seconda della configurazione, ma con lo stesso percorso effettivo. A questo scopo, è necessario avere più directory di risorse e file con gli stessi percorsi relativi in queste directory diverse e usare condizioni MSBuild per includere in modo condizionale file diversi a seconda della configurazione. Ad esempio:

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**** LogicalName&ndash;: specifica il percorso della risorsa in modo esplicito. Consente l'&ldquo;aliasing&rdquo; dei file in modo che siano disponibili come più nomi di risorse distinti.

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

### <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

L'azione `AndroidResourceAnalysisConfig` di compilazione contrassegna un file come file di configurazione del livello di gravità per lo strumento di diagnostica del layout di Xamarin Android Designer.The Build action marks a file as a severity level configuration file for the Xamarin Android Designer layout diagnostics tool. Attualmente viene utilizzato solo nell'editor di layout e non per i messaggi di compilazione.

Per ulteriori dettagli, vedere la documentazione relativa [all'analisi delle risorse Android.See](https://aka.ms/androidresourceanalysis) the Android Resource Analysis documentation for more details.

Aggiunto in Xamarin.Android 10.2.

### <a name="content"></a>Contenuto

La normale azione di compilazione `Content` non è supportata, perché non è stato determinato come supportarla senza un passaggio per la prima esecuzione probabilmente costoso.

A partire da Xamarin.Android 5.1, se si prova a usare l'azione di compilazione `@(Content)`, verrà visualizzato un avviso `XA0101`.

### <a name="linkdescription"></a>LinkDescription

I file con un'azione di compilazione *LinkDescription* vengono usati per [controllare il comportamento del linker](~/cross-platform/deploy-test/linker.md).

<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

I file con un'azione di compilazione *ProguardConfiguration* contengono opzioni usate per controllare il comportamento di `proguard`. Per altre informazioni su questa azione di compilazione, vedere [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Questi file vengono ignorati a meno che la proprietà MSBuild `$(EnableProguard)` non sia `True`.

## <a name="target-definitions"></a>Definizioni delle destinazioni

Le parti del processo di compilazione specifiche di Xamarin.Android vengono definite in `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`, ma sono necessarie anche le destinazioni specifiche dei normali linguaggi, ad esempio *Microsoft.CSharp.targets*, per compilare l'assembly.

Prima di importare le destinazioni dei linguaggi, è necessario impostare le proprietà di compilazione seguenti:

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

Tutte queste destinazioni e proprietà possono essere incluse per C# importando *Xamarin.Android.CSharp.targets*:

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

Questo file può essere facilmente adattato per altri linguaggi.
