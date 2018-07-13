---
title: Processo di compilazione
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2018
ms.openlocfilehash: bf8dfb43115806f28935c6dec0ebd2d6d7bd2cdc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998256"
---
# <a name="build-process"></a>Processo di compilazione


## <a name="overview"></a>Panoramica

Il processo di compilazione di Xamarin.Android è responsabile della gestione delle diverse operazioni: [generazione di `Resource.designer.cs`](~/android/internals/api-design.md), supporto di `AndroidAsset`, `AndroidResource` e altre [azioni di compilazione](#Build_Actions), generazione di [wrapper disponibili per la chiamata tramite Android](~/android/platform/java-integration/android-callable-wrappers.md) e generazione di un file `.apk` da eseguire sui dispositivi Android.


## <a name="application-packages"></a>Pacchetti dell'applicazione

In senso lato, esistono due tipi di pacchetti dell'applicazione Android (file `.apk`) che possono essere generati dal sistema di compilazione di Xamarin.Android: 

-   Build di **rilascio**, che sono completamente autonome e non richiedono l'esecuzione di pacchetti aggiuntivi. Questi sono i pacchetti che verrebbero forniti a un archivio app. 

-   Build di **debug**, che sono l'esatto contrario. 

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

Gli utenti avanzati potrebbero voler eseguire operazioni non supportate dall'interfaccia utente grafica dell'IDE, quindi il processo di compilazione si può personalizzare modificando direttamente il file di progetto. Questa pagina documenta solo le funzionalità e le personalizzazioni specifiche di Xamarin.Android, ma è possibile eseguire molte altre operazioni con i normali elementi, proprietà e destinazioni MSBuild. 

<a name="Build_Targets" />

## <a name="build-targets"></a>Destinazioni di compilazione

Per i progetti Xamarin.Android vengono definite le destinazioni di compilazione seguenti:

-   **Build**: compila il pacchetto.

-   **Clean**: rimuove tutti i file generati dal processo di compilazione.

-   **Install**: installa il pacchetto nel dispositivo predefinito o nel dispositivo virtuale.

-   **Uninstall**: disinstalla il pacchetto dal dispositivo predefinito o dal dispositivo virtuale.

-   **SignAndroidPackage**: crea e firma il pacchetto (`.apk`). Da usare con `/p:Configuration=Release` per generare i pacchetti "Release" (Rilascio) autonomi.

-   **UpdateAndroidResources**: aggiorna il file `Resource.designer.cs`. Questa destinazione viene in genere chiamata dall'IDE quando vengono aggiunte nuove risorse al progetto.


## <a name="build-properties"></a>Proprietà di compilazione

Le proprietà MSBuild controllano il comportamento delle destinazioni. Sono specificate nel file di progetto, ad esempio **MyApp.csproj**, in un [elemento PropertyGroup MSBuild](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

-   **Configuration**: specifica la configurazione della build da usare, ad esempio "Debug" o "Release" (Rilascio). La proprietà Configuration viene usata per determinare i valori predefiniti per le altre proprietà che determinano il comportamento delle destinazioni. Configurazioni aggiuntive possono essere create nell'IDE.

    *Per impostazione predefinita*, con la configurazione `Debug` le destinazioni `Install` e `SignAndroidPackage` creeranno un pacchetto Android più piccolo per il cui funzionamento è necessaria la presenza di altri file e pacchetti.

    Con la configurazione `Release` predefinita, le destinazioni `Install` e `SignAndroidPackage` creeranno un pacchetto Android *autonomo* e possono essere usate senza installare altri pacchetti o file.

-   **DebugSymbols**: valore booleano che determina se il pacchetto Android *può essere sottoposto a debug*, in combinazione con la proprietà `$(DebugType)`. Un pacchetto sottoponibile a debug contiene simboli di debug, imposta l'attributo `//application/@android:debuggable` su `true` e aggiunge automaticamente l'autorizzazione `INTERNET` in modo che un debugger possa connettersi al processo. Un'applicazione può essere sottoposta a debug se `DebugSymbols` è `True` *e* `DebugType` è una stringa vuota o `Full`.

-   **DebugType**: specifica il [tipo di simboli di debug](https://docs.microsoft.com/visualstudio/msbuild/csc-task) da generare durante la compilazione, da cui dipende anche la possibilità di sottoporre l'applicazione a debug. I valori possibili includono:

    - **Full**: vengono generati simboli completi. Se anche la proprietà MSBuild `DebugSymbols` è `True`, il pacchetto dell'applicazione può essere sottoposto a debug.

    - **PdbOnly**: vengono generati simboli "PDB". Il pacchetto dell'applicazione *non* potrà essere sottoposto a debug.

    Se `DebugType` non è impostato o è una stringa vuota, la proprietà `DebugSymbols` controlla se l'applicazione può essere sottoposta o meno a debug.


### <a name="install-properties"></a>Proprietà di installazione

Le proprietà di installazione controllano il comportamento delle destinazioni `Install` e `Uninstall`.

-   **AdbTarget**: specifica il dispositivo di destinazione Android in cui il pacchetto Android può essere installato o rimosso. Il valore di questa proprietà è lo stesso dell'[opzione del dispositivo di destinazione `adb`](http://developer.android.com/tools/help/adb.html#issuingcommands):

    ```bash
    # Install package onto emulator via -e
    # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
    MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
    ```


### <a name="packaging-properties"></a>Proprietà per la creazione di pacchetti

Queste proprietà controllano la creazione del pacchetto Android e vengono usate dalle destinazioni `Install` e `SignAndroidPackage`.
Anche le [proprietà di firma](#Signing_Properties) sono rilevanti quando si creano pacchetti delle applicazioni di rilascio.


-   **AndroidApkSigningAlgorithm** &ndash; Valore stringa che specifica l'algoritmo di firma da usare con `jarsigner -sigalg`.

    Il valore predefinito è `md5withRSA`.

    Aggiunta in Xamarin.Android 8.2.

-   **AndroidApplication**: valore booleano indicante se il progetto è destinato a un'applicazione Android (`True`) o a un progetto di libreria Android (`False` o non presente).

    In un pacchetto Android può essere presente un solo progetto con `<AndroidApplication>True</AndroidApplication>`. Questo purtroppo non è ancora stato verificato e possono presentarsi errori insoliti e difficili da rilevare relativi alle risorse Android.

-   **AndroidBuildApplicationPackage**: valore booleano indicante se creare e firmare il pacchetto (APK). Impostare questo valore su `True` equivale a usare la destinazione di compilazione [SignAndroidPackage](#Build_Targets).

    Il supporto per questa proprietà stato aggiunto dopo Xamarin.Android 7.1.

    Per impostazione predefinita, il valore della proprietà è `False`.

-   **AndroidEnableMultiDex**: proprietà booleana che determina se nel file `.apk` finale verrà usato o meno il supporto multidex.

    Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

    Per impostazione predefinita, il valore della proprietà è `False`.

-   **AndroidEnableSGenConcurrent**: proprietà booleana che determina se verrà usato o meno l'[agente di raccolta GC simultaneo](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) di Mono.

    Il supporto per questa proprietà stato aggiunto in Xamarin.Android 7.2.

    Per impostazione predefinita, il valore della proprietà è `False`.

-   **AndroidErrorOnCustomJavaObject** &ndash; Proprietà booleana che determina se i tipi possono implementare `Android.Runtime.IJavaObject`
    *senza* anche ereditare da `Java.Lang.Object` o `Java.Lang.Throwable`:

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

-   **AndroidFastDeploymentType**: elenco di valori separati da `:` (due punti) che consente di controllare quali tipi possono essere distribuiti nella [directory Fast Deployment](#Fast_Deployment) sul dispositivo di destinazione quando la proprietà MSBuild `$(EmbedAssembliesIntoApk)` è `False`. Se una risorsa viene distribuita con Fast Deployment, *non* viene incorporata nel file `.apk` generato, con la possibilità di accelerare i tempi di distribuzione. Maggiore è la velocità di distribuzione, meno spesso sarà necessario ricompilare il file `.apk` e il processo di installazione potrà essere più rapido. I valori validi includono:

    - `Assemblies`: distribuisce gli assembly dell'applicazione.

    - `Dexes`: distribuisce i file `.dex`, le risorse Android e gli asset Android. **Questo valore può essere usato *solo* nei dispositivi che eseguono Android 4.4 o versione successiva (API 19).**

    Il valore predefinito è `Assemblies`.

    **Sperimentale**. Aggiunto in Xamarin.Android 6.1.

-   **AndroidApplicationJavaClass**: nome della classe Java completo da usare invece di `android.app.Application` quando una classe eredita da [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/).

    Questa proprietà viene in genere impostata da *altre* proprietà, ad esempio la proprietà MSBuild `$(AndroidEnableMultiDex)`.

    Aggiunto in Xamarin.Android 6.1.

-   **AndroidHttpClientHandlerType** &ndash; Controlla l'implementazione predefinita di `System.Net.Http.HttpMessageHandler` che verrà usata dal costruttore predefinito `System.Net.Http.HttpClient`. Il valore è un nome di tipo qualificato dall'assembly di una sottoclasse `HttpMessageHandler`, adatta per l'uso con [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).

    Il valore predefinito è `System.Net.Http.HttpClientHandler, System.Net.Http`.

    Questo valore può essere sostituito per contenere invece `Xamarin.Android.Net.AndroidClientHandler`, che usa le API Java Android per eseguire le richieste di rete. Ciò consente l'accesso agli URL TLS 1.2 quando la versione di Android sottostante supporta TLS 1.2.  
    Solo Android 5.0 e versioni successive offrono un supporto affidabile di TLS 1.2 tramite Java.

    *Nota*: se è richiesto il supporto di TLS 1.2 nelle versioni di Android precedenti alla 5.0 *oppure* se è richiesto il supporto di TLS 1.2 con `System.Net.WebClient` e le API correlati, è necessario usare `$(AndroidTlsProvider)`.

    *Nota*: il supporto per questa proprietà funziona impostando la [variabile di ambiente `XA_HTTP_CLIENT_HANDLER_TYPE`](~/android/deploy-test/environment.md).
    Un eventuale valore `$XA_HTTP_CLIENT_HANDLER_TYPE` trovato in un file con un'azione di compilazione `@(AndroidEnvironment)` avrà la precedenza.

    Aggiunto in Xamarin.Android 6.1.

-   **AndroidTlsProvider**: valore di stringa che specifica il provider TLS da usare in un'applicazione. I possibili valori sono:

    - `btls`: usa [Boring SSL](https://boringssl.googlesource.com/boringssl) per la comunicazione TLS con [HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest.aspx).
      Ciò consente l'uso di TLS 1.2 in tutte le versioni di Android.

    - `legacy`: usa l'implementazione SSL cronologica gestita per l'interazione in rete. *Non* supporta TLS 1.2.

    - `default`: consente a *Mono* di scegliere il provider TLS predefinito.
      Equivale a `legacy`, anche in Xamarin.Android 7.3.  
      *Nota*: è improbabile che questo valore venga visualizzato nei valori `.csproj`, perché il valore "Default" dell'IDE comporta la *rimozione* della proprietà `$(AndroidTlsProvider)`.

    - Non impostato/stringa vuota: In Xamarin.Android 7.1 equivale a `legacy`.  
      In Xamarin.Android 7.3, equivale a `btls`.

    Il valore predefinito è la stringa vuota.

    Aggiunto in Xamarin.Android 7.1.

-   **AndroidLinkMode**: specifica il tipo di [collegamento](~/android/deploy-test/linker.md) da eseguire negli assembly contenuti nel pacchetto Android. Usato solo nei progetti Applicazione Android. Il valore predefinito è *SdkOnly*. I valori validi sono:

    - **None**: non verranno tentati collegamenti.

    - **SdkOnly**: il collegamento verrà eseguito solo nelle librerie di classi base e non negli assembly dell'utente.

    - **Full**: il collegamento verrà eseguito nelle librerie di classi base e negli assembly dell'utente. **Nota:** se si usa *Full* come valore di `AndroidLinkMode`, spesso le app vengono interrotte, soprattutto quando viene usata la reflection. Evitarlo a meno che non se ne sia *davvero* certi.

    ```xml
    <AndroidLinkMode>SdkOnly</AndroidLinkMode>
    ```

-   **AndroidLinkSkip**: specifica un elenco di nomi di assembly delimitati da punto e virgola (`;`), senza estensioni di file. Tali assembly non devono essere collegati. Usato solo nei progetti Applicazione Android.

    ```xml
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
    ```

-   **AndroidManagedSymbols**: proprietà booleana che controlla se vengono generati punti di sequenza in modo che le informazioni sul nome file e sul numero di riga possano essere estratte dalle analisi dello stack `Release`.

    Aggiunto in Xamarin.Android 6.1.

-   **AndroidManifest**: specifica un nome file da usare come modello per il file [`AndroidManifest.xml`](~/android/platform/android-manifest.md) dell'app.
    Durante la compilazione, verranno uniti gli altri valori necessari per generare il file `AndroidManifest.xml` effettivo.
    `$(AndroidManifest)` deve contenere il nome del pacchetto nell'attributo `/manifest/@package`.

-   **AndroidSdkBuildToolsVersion**: il pacchetto di strumenti di compilazione Android SDK fornisce gli strumenti **aapt**, **zipalign** e altri ancora. Più versioni diverse del pacchetto di strumenti di compilazione possono essere installate contemporaneamente. Il pacchetto di strumenti di compilazione scelto per la creazione di pacchetti viene creato cercando e usando una versione "preferita" degli strumenti di compilazione, se presente. Se la versione "preferita" *non* è presente, viene usato il pacchetto di strumenti di compilazione installato con la versione superiore.

    La proprietà MSBuild `$(AndroidSdkBuildToolsVersion)` contiene la versione preferita degli strumenti di compilazione. Il sistema di compilazione Xamarin.Android specifica un valore predefinito in `Xamarin.Android.Common.targets` ed è possibile eseguire l'override del valore predefinito nel file di progetto per scegliere una versione alternativa degli strumenti di compilazione, se, ad esempio, lo strumento aapt più recente si arresta in modo anomalo mentre una versione di aapt precedente è sicuramente funzionante.

-   **AndroidSupportedAbis**: proprietà stringa contenente un elenco di ABI delimitati da punto e virgola (`;`), che devono essere inclusi nel file `.apk`.

    I valori supportati includono:

    -   `armeabi`
    -   `armeabi-v7a`
    -   `x86`
    -   `arm64-v8a`: richiede Xamarin.Android 5.1 e versioni successive.
    -   `x86_64`: richiede Xamarin.Android 5.1 e versioni successive.

-   **AndroidUseSharedRuntime**: proprietà booleana che determina se sono necessari i *pacchetti di runtime condiviso* per eseguire l'applicazione sul dispositivo di destinazione. Basandosi sui pacchetti di runtime condiviso, è possibile ridurre le dimensioni del pacchetto dell'applicazione, velocizzando il processo di creazione e distribuzione del pacchetto e di conseguenza anche il ciclo di compilazione/distribuzione/debug.

    Questa proprietà deve essere `True` per le build di debug e `False` per i progetti di rilascio.

-   **AotAssemblies**: proprietà booleana che determina se gli assembly verranno compilati o meno in anticipo nel codice nativo e inclusi nel file `.apk`.

    Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

    Per impostazione predefinita, il valore della proprietà è `False`.

-   **EmbedAssembliesIntoApk**: proprietà booleana che determina se gli assembly dell'app devono essere incorporati o meno nel pacchetto dell'applicazione.

    Questa proprietà deve essere `True` per le build di rilascio e `False` per le build di debug. *Potrebbe* dover essere `True` nelle build di debug se Fast Deployment non supporta il dispositivo di destinazione.

    Quando questa proprietà è `False`, la proprietà MSBuild `$(AndroidFastDeploymentType)` controlla anche che cosa verrà incorporato in `.apk` e ciò può influire sui tempi di distribuzione e ricompilazione.

-   **EnableLLVM** &ndash;Proprietà booleana che determina se LLVM verrà usato o meno durante la compilazione Ahead Of Time degli assembly nel codice nativo.

    Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

    Per impostazione predefinita, il valore della proprietà è `False`.

    Questa proprietà viene ignorata a meno che la proprietà MSBuild `$(AotAssemblies)` non sia `True`.

-   **EnableProguard**: proprietà booleana che determina se [proguard](http://developer.android.com/tools/help/proguard.html) viene eseguito o meno durante il processo di creazione del pacchetto per collegare il codice Java.

    Il supporto per questa proprietà stato aggiunto in Xamarin.Android 5.1.

    Per impostazione predefinita, il valore della proprietà è `False`.

    Se `True`, i file di [ProguardConfiguration](#ProguardConfiguration) verranno usati per controllare l'esecuzione di `proguard`.

-   **JavaMaximumHeapSize**: specifica il valore del parametro **java**
    `-Xmx` da usare durante la compilazione del file `.dex` nell'ambito del processo di creazione del pacchetto. Se non specificato, l'opzione `-Xmx` non viene fornita a **java**.

    Specificare questa proprietà è necessario se la [destinazione `_CompileDex` genera `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

    ```xml
    <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
    ```

-   **JavaOptions**: specifica opzioni aggiuntive della riga di comando da passare a **java** quando si compila il file `.dex`.

-   **MandroidI18n**: specifica il supporto di internazionalizzazione incluso con l'applicazione, ad esempio le regole di confronto e le tabelle di ordinamento. Il valore è un elenco separato da virgole o punti e virgola di uno o più dei valori seguenti senza distinzione tra maiuscole e minuscole:

    -   **None**: non include codifiche aggiuntive.

    -   **All**: include tutte le codifiche disponibili.

    -   **CJK**: include le codifiche per cinese, giapponese e coreano, ad esempio *Giapponese (EUC)* \[enc-jp, CP51932\], *Giapponese (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *Giapponese (JIS)* \[CP50220\], *Cinese semplificato (GB2312)* \[gb2312, CP936\], *Coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *Coreano (EUC)* \[euc-kr, CP51949\], *Cinese tradizionale (Big5)* \[big5, CP950\] e *Cinese semplificato (GB18030)* \[GB18030, CP54936\].

    -   **MidEast**: include le codifiche per il Medio Oriente, ad esempio *Turco (Windows)* \[iso-8859-9, CP1254\], *Ebraico (Windows)* \[windows-1255, CP1255\], *Arabo (Windows)* \[windows-1256, CP1256\], *Arabo (ISO)* \[iso-8859-6, CP28596\], *Ebraico (ISO)* \[iso-8859-8, CP28598\], *Latino 5 (ISO)* \[iso-8859-9, CP28599\] ed *Ebraico (ISO alternativo)* \[iso-8859-8, CP38598\].

    -   **Other**: include le altre codifiche, ad esempio *Cirillico (Windows)* \[CP1251\], *Baltico (Windows)* \[iso-8859-4, CP1257\], *Vietnamita (Windows)* \[CP1258\], *Cirillico (KOI8-R)* \[koi8-r, CP1251\], *Ucraino (KOI8-U)* \[koi8-u, CP1251\], *Baltico (ISO)* \[iso-8859-4, CP1257\], *Cirillico (ISO)* \[iso-8859-5, CP1251\], *ISCII Devanagari* \[x-iscii-de, CP57002\], *ISCII Bengali* \[x-iscii-be, CP57003\], *ISCII Tamil* \[x-iscii-ta, CP57004\], *ISCII Telugu* \[x-iscii-te, CP57005\], *ISCII Assamese* \[x-iscii-as, CP57006\], *ISCII Odia* \[x-iscii-or, CP57007\], *ISCII Kannada* \[x-iscii-ka, CP57008\], *ISCII Malayalam* \[x-iscii-ma, CP57009\], *ISCII Gujarati* \[x-iscii-gu, CP57010\], *ISCII Punjabi* \[x-iscii-pa, CP57011\] e *Thai (Windows)* \[CP874\].

    -   **Rare**: include le codifiche rare, ad esempio *IBM EBCDIC (Turco)* \[CP1026\], *IBM EBCDIC (Latino 1/Sistema aperto)* \[CP1047\], *IBM EBCDIC (Stati Uniti-Canada-Europa)* \[CP1140\], *IBM EBCDIC (Germania-Europa)* \[CP1141\], *IBM EBCDIC (Danimarca/Norvegia-Europa)* \[CP1142\], *IBM EBCDIC (Finlandia/Svezia-Europa)* \[CP1143\], *IBM EBCDIC (Italia-Europa)* \[CP1144\], *IBM EBCDIC (America Latina/Spagna-Europa)* \[CP1145\], *IBM EBCDIC (Regno Unito-Europa)* \[CP1146\], *IBM EBCDIC (Francia-Europa)* \[CP1147\], *IBM EBCDIC (Internazionale-Europa)* \[CP1148\], *IBM EBCDIC (Islandese-Europa)* \[CP1149\], *IBM EBCDIC (Germania)* \[CP20273\], *IBM EBCDIC (Danimarca/Norvegia)* \[CP20277\], *IBM EBCDIC (Finlandia/Svezia)* \[CP20278\], *IBM EBCDIC (Italia)* \[CP20280\], *IBM EBCDIC (America Latina/Spagna)* \[CP20284\], *IBM EBCDIC (Regno Unito)* \[CP20285\], *IBM EBCDIC (Giapponese Katakana esteso)* \[CP20290\], *IBM EBCDIC (Francia)* \[CP20297\], *IBM EBCDIC (Arabo)* \[CP20420\], *IBM EBCDIC (Ebraico)* \[CP20424\], *IBM EBCDIC (Islandese)* \[CP20871\], *IBM EBCDIC (Cirillico - Serbo, Bulgaro)* \[CP21025\], *IBM EBCDIC (Stati Uniti-Canada)* \[CP37\], *IBM EBCDIC (Internazionale)* \[CP500\], *Arabo (ASMO 708)* \[CP708\], *Europa centrale (DOS)* \[CP852\]*, Cirillico (DOS)* \[CP855\], *Turco (DOS)* \[CP857\], *Europa occidentale (DOS-Europa)* \[CP858\], *Ebraico (DOS)* \[CP862\], *Arabo (DOS)* \[CP864\], *Russo (DOS)* \[CP866\], *Greco (DOS)* \[CP869\], *IBM EBCDIC (Latino 2)* \[CP870\] e *IBM EBCDIC (Greco)* \[CP875\].

    -   **West**: include le codifiche per l'area occidentale, ad esempio *Europa occidentale (Mac)* \[macintosh, CP10000\], *Islandese (Mac)* \[x-mac-icelandic, CP10079\], *(Windows)* \[iso-8859-2, CP1250\], *Europa occidentale (Windows)* \[iso-8859-1, CP1252\], *Greco (Windows)* \[iso-8859-7, CP1253\], *Europa centrale (ISO)* \[iso-8859-2, CP28592\], *Latino 3 (ISO)* \[iso-8859-3, CP28593\], *Greco (ISO)* \[iso-8859-7, CP28597\], *Latino 9 (ISO)* \[iso-8859-15, CP28605\], *OEM Stati Uniti* \[CP437\], *Europa occidentale (DOS)* \[CP850\], *Portoghese (DOS)* \[CP860\], *Islandese (DOS)* \[CP861\], *(DOS)* \[CP863\] e *Nordico (DOS)* \[CP865\].


    ```xml
    <MandroidI18n>West</MandroidI18n>
    ```

-   **MonoSymbolArchive**: proprietà booleana che controlla se vengono creati elementi `.mSYM` da usare in seguito con `mono-symbolicate`, per estrarre le informazioni &ldquo;effettive&rdquo; su nome file e numero di riga dalle analisi dello stack Release.

    Per impostazione predefinita, è True per le app &ldquo;Release&rdquo; con i simboli di debug abilitati: `$(EmbedAssembliesIntoApk)` è True, `$(DebugSymbols)` è True e `$(Optimize)` è True.

    Aggiunto in Xamarin.Android 7.1.

-   **AndroidVersionCodePattern**: proprietà stringa che consente allo sviluppatore di personalizzare `versionCode` nel manifesto.
    Per informazioni utili per decidere riguardo a `versionCode`, vedere [Creazione del codice della versione per l'APK](~/android/deploy-test/building-apps/abi-specific-apks.md).
    
    Alcuni esempi: se `abi` è `armeabi` e `versionCode` nel manifesto è `123`, `{abi}{versionCode}` genererà un elemento versionCode `1123` quando `$(AndroidCreatePackagePerAbi)` è True. In caso contrario genererà il valore 123.
    Se `abi` è `x86_64` e `versionCode` nel manifesto è `44`, verrà generato `544` quando `$(AndroidCreatePackagePerAbi)` è True. In caso contrario, genererà il valore `44`.

    Se viene inclusa una stringa di formato con riempimento a sinistra `{abi}{versionCode:0000}`, genererà `50044` perché si riempie `versionCode` a sinistra con `0`. In alternativa, è possibile usare il riempimento decimale, ad esempio `{abi}{versionCode:D4}`, che produce lo stesso risultato dell'esempio precedente.

    Sono supportate solo le stringhe in formato con riempimento "0" e "Dx" perché il valore DEVE essere un intero.
    
    Elementi chiave predefiniti

    -   **abi** &ndash; Inserisce l'ABI di destinazione per l'app
        -   1 &ndash; `armeabi`
        -   2 &ndash; `armeabi-v7a`
        -   3 &ndash; `x86`
        -   4 &ndash; `arm64-v8a`
        -   5 &ndash; `x86_64`

    -   **minSDK**: inserisce il valore minimo supportato per l'SDK da `AndroidManifest.xml` o `11` se non è definito nessuno.

    -   **versionCode** &ndash; Usa il codice della versione direttamente da `Properties\AndroidManifest.xml`. 

    È possibile definire gli elementi personalizzati usando la proprietà `$(AndroidVersionCodeProperties)` (definita successivamente).

    Per impostazione predefinita, il valore verrà impostato su `{abi}{versionCode:D6}`. Se uno sviluppatore vuole mantenere il comportamento precedente può ignorare l'impostazione predefinita impostando la proprietà `$(AndroidUseLegacyVersionCode)` su `true`

    Aggiunto in Xamarin.Android 7.2.

-   **AndroidVersionCodeProperties**: proprietà stringa che consente allo sviluppatore di definire elementi personalizzati con `AndroidVersionCodePattern`, sotto forma di coppia `key=value`. Tutti gli elementi in `value` devono essere valori interi. Ad esempio: `screen=23;target=$(_SupportedApiLevel)`. Come si può osservare, è possibile usare nella stringa proprietà MSBuild esistenti o personalizzate.

    Aggiunto in Xamarin.Android 7.2.

-   **AndroidUseLegacyVersionCode** &ndash; Proprietà booleana che consente allo sviluppatore di ripristinare il comportamento precedente a Xamarin.Android 8.2 per i calcoli versionCode. Deve essere usata SOLO dagli sviluppatori con applicazioni esistenti in Google Play Store. È consigliabile usare la nuova proprietà `$(AndroidVersionCodePattern)`.

    Aggiunta in Xamarin.Android 8.2.

-  **AndroidUseManagedDesignTimeResourceGenerator** &ndash; Proprietà booleana che consente di passare all'uso del parser delle risorse gestito per le compilazioni in fase di progettazione invece di `aapt`.

    Aggiunta in Xamarin.Android 8.1.

-  **AndroidUseApkSigner** &ndash; Proprietà booleana che consente allo sviluppatore di usare lo strumento `apksigner` invece di `jarsigner`.

    Aggiunta in Xamarin.Android 8.2.

-  **AndroidApkSignerAdditionalArguments** &ndash; Proprietà stringa che consente allo sviluppatore di specificare argomenti aggiuntivi per lo strumento `apksigner`.

    Aggiunta in Xamarin.Android 8.2.

### <a name="binding-project-build-properties"></a>Proprietà di compilazione del progetto di binding

Le proprietà MSBuild seguenti vengono usate con i [progetti di binding](~/android/platform/binding-java-library/index.md):

-   **AndroidClassParser**: proprietà stringa che controlla come vengono analizzati i file `.jar`. I valori possibili includono:

    - **class-parse**: usa `class-parse.exe` per analizzare direttamente il bytecode Java senza ricorrere a JVM. Questo valore è sperimentale. 


    - **jar2xml**: usa `jar2xml.jar` per estrarre tipi e membri da un file `.jar` con la reflection Java.

    I vantaggi di `class-parse` rispetto a `jar2xml` sono:

    - `class-parse` può estrarre i nomi dei parametri dal bytecode Java che contiene i simboli di *debug* (ad esempio, bytecode compilato con `javac -g`).

    - `class-parse` non ignora le classi che ereditano o contengono membri di tipi non risolvibili.

    **Sperimentale**. Aggiunto in Xamarin.Android 6.0.

    Il valore predefinito è `jar2xml`.

    Il valore predefinito cambierà in una versione futura.

-   **AndroidCodegenTarget**: proprietà stringa che controlla l'ABI di destinazione della generazione del codice. I valori possibili includono:

    - **XamarinAndroid**: usa l'API di binding JNI presente a partire da Mono for Android 1.0. Gli assembly di binding compilati con Xamarin.Android 5.0 o versioni successive possono essere eseguiti solo in Xamarin.Android 5.0 o versioni successive (aggiunte API/ABI), ma l'*origine* è compatibile con le versioni precedenti del prodotto.

    - **XAJavaInterop1**: usa Java.Interop per le chiamate JNI. Gli assembly di binding che usano `XAJavaInterop1` possono solo essere compilati ed eseguiti con Xamarin.Android 6.1 o versioni successive. Xamarin.Android 6.1 e versioni successive eseguono il binding di `Mono.Android.dll` con questo valore.

      I vantaggi di `XAJavaInterop1` includono:

      - Assembly di dimensioni ridotte.

      - Memorizzazione nella cache `jmethodID` per le chiamate ai metodi `base`, purché tutti gli altri tipi di binding nella gerarchia di ereditarietà siano compilati con `XAJavaInterop1` o versione successiva.

      - Memorizzazione nella cache `jmethodID` di costruttori di Java Callable Wrapper per sottoclassi gestite.

    Il valore predefinito è `XamarinAndroid`.

    Il valore predefinito cambierà in una versione futura.


### <a name="resource-properties"></a>Proprietà risorsa

Le proprietà risorsa controllano la generazione del file `Resource.designer.cs`, che fornisce l'accesso alle risorse Android. 

-   **AndroidResgenExtraArgs**: specifica opzioni aggiuntive della riga di comando da passare al comando **aapt** quando si elaborano le risorse e gli asset di Android.

-   **AndroidResgenFile**: specifica il nome del file di risorse da generare. Il modello predefinito lo imposta su `Resource.designer.cs`.

-   **MonoAndroidResourcePrefix**: specifica un *prefisso percorso* che viene rimosso dall'inizio dei nomi file con un'azione di compilazione `AndroidResource`, per poter modificare la posizione delle risorse.

    Il valore predefinito è `Resources`. Impostarlo su `res` per la struttura del progetto Java.

-   **AndroidExplicitCrunch**: se si compila un'app con un numero molto elevato di risorse drawable locali, il completamento di una compilazione iniziale (o di una ricompilazione) può richiedere alcuni minuti. Per accelerare il processo di compilazione, provare a includere questa proprietà e a impostarla su `True`. Quando questa proprietà è impostata, il processo di compilazione analizza in anticipo i file PNG.

    **Sperimentale**. Aggiunto in Xamarin.Android 7.0.


<a name="Signing_Properties" />

### <a name="signing-properties"></a>Proprietà di firma

Le proprietà di firma controllano come viene firmato il pacchetto dell'applicazione in modo che possa essere installato in un dispositivo Android. Per consentire un'iterazione della compilazione più rapida, le attività di Xamarin.Android non firmano i pacchetti durante il processo di compilazione perché la firma è piuttosto lenta. Vengono invece firmate (se necessario) prima dell'installazione o durante l'esportazione dall'IDE o dalla destinazione di compilazione *Install*. La chiamata alla destinazione *SignAndroidPackage* genererà un pacchetto con il suffisso `-Signed.apk` nella directory di output.

Per impostazione predefinita, la destinazione di firma genera una nuova chiave di firma di debug, se necessario. Per usare una chiave specifica, ad esempio in un server di compilazione, si possono usare le proprietà MSBuild seguenti:

-   **AndroidKeyStore**: valore booleano indicante se devono essere usate informazioni sulla firma personalizzate. Il valore predefinito è `False`, indicante che per la firma dei pacchetti verrà usata la chiave di firma di debug.

-   **AndroidSigningKeyAlias**: specifica l'alias per la chiave nell'archivio chiavi. Si tratta del valore **keytool -alias** usato durante la creazione dell'archivio chiavi. 

-   **AndroidSigningKeyPass**: specifica la password della chiave nel file dell'archivio chiavi. Si tratta del valore immesso quando `keytool` chiede di **immettere la password della chiave per $(AndroidSigningKeyAlias)**.

-   **AndroidSigningKeyStore**: specifica il nome del file dell'archivio chiavi creato da `keytool`. Corrisponde al valore fornito all'opzione **keytool -keystore**.

-   **AndroidSigningStorePass**: specifica la password per `$(AndroidSigningKeyStore)`. Si tratta del valore fornito a `keytool` quando si è creato il file dell'archivio chiavi ed è stato chiesto di **immettere la password dell'archivio chiavi**. 

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

-   **AndroidDebugKeyAlgorithm** &ndash; Specifica l'algoritmo predefinito da usare per `debug.keystore`. Il valore predefinito è `RSA`.

-   **AndroidDebugKeyValidity** &ndash; Specifica la validità predefinita da usare per `debug.keystore`. Il valore predefinito è `10950` o `30 * 365` o `30 years`.

<a name="Build_Actions" />

## <a name="build-actions"></a>Azioni di compilazione

Le *azioni di compilazione* vengono [applicate ai file](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items) del progetto e controllano l'elaborazione del file. 

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

I file con un'azione di compilazione `AndroidEnvironment` vengono usati per [inizializzare le variabili di ambiente e le proprietà di sistema durante l'avvio del processo](~/android/deploy-test/environment.md).
L'azione di compilazione `AndroidEnvironment` può essere applicata a più file che verranno valutati in ordine casuale, quindi non specificare la stessa variabile di ambiente o proprietà di sistema in più file.


### <a name="androidjavasource"></a>AndroidJavaSource

I file con un'azione di compilazione `AndroidJavaSource` sono codice sorgente Java che verrà incluso nel pacchetto Android finale.


### <a name="androidjavalibrary"></a>AndroidJavaLibrary

I file con un'azione di compilazione `AndroidJavaLibrary` sono archivi Java (file `.jar`) che verranno inclusi nel pacchetto Android finale.


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

**LogicalName**: specifica il percorso della risorsa in modo esplicito. Consente l'&ldquo;aliasing&rdquo; dei file in modo che siano disponibili come più nomi di risorse distinti.

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


### <a name="androidnativelibrary"></a>AndroidNativeLibrary

Le [librerie native](~/android/platform/native-libraries.md) vengono aggiunte alla compilazione impostandone l'azione di compilazione su `AndroidNativeLibrary`.

Si noti che, poiché Android supporta più interfacce binarie dell'applicazione (ABI), il sistema di compilazione deve sapere per quale ABI viene compilata per la libreria nativa. Esistono due modi per farlo:

1.  Analisi del percorso.
2.  Uso dell'attributo dell'elemento `Abi`.

Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Se quindi si aggiunge `lib/armeabi/libfoo.so` alla compilazione, l'ABI verrà analizzato come `armeabi`. 


#### <a name="item-attribute-name"></a>Nome dell'attributo dell'elemento

**Abi**: specifica l'ABI della libreria nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```


### <a name="androidaarlibrary"></a>AndroidAarLibrary

L'azione di compilazione `AndroidAarLibrary` deve essere usata per fare riferimento direttamente ai file con estensione aar. Questa azione di compilazione verrà usata più comunemente dai componenti di Xamarin, in particolare per includere riferimenti ai file con estensione aar necessari per il funzionamento di Google Play e altri servizi.

I file con questa azione di compilazione verranno gestiti in modo simile alle risorse incorporate nei progetti di libreria. Il file con estensione aar verrà estratto nella directory intermedia. Quindi qualsiasi asset, risorsa e file con estensione jar verrà incluso nei gruppi di elementi appropriati.  

### <a name="content"></a>Content

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
