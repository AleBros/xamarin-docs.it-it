---
title: Preparazione di un'applicazione per il rilascio
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2018
ms.openlocfilehash: 238e224a1dfbc17089c8b6d03e78043f77f3f383
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="preparing-an-application-for-release"></a>Preparazione di un'applicazione per il rilascio


Dopo aver scritto il codice di un'applicazione e averla testata, è necessario preparare il pacchetto per la distribuzione. La prima attività di preparazione del pacchetto è la compilazione dell'applicazione per il rilascio, che essenzialmente comporta l'impostazione di alcuni attributi dell'applicazione.

Usare la procedura seguente per compilare l'app per il rilascio:

-   **[Specificare l'icona dell'applicazione](#Specify_the_Application_Icon)** &ndash; Per ogni applicazione Xamarin.Android deve essere specificata un'icona di applicazione. Anche se tecnicamente non è necessaria, alcuni mercati, ad esempio Google Play, la richiedono.

-   **[Specificare la versione dell'applicazione](#Versioning)** &ndash; Questo passaggio prevede l'inizializzazione o l'aggiornamento delle informazioni di controllo della versione. Questo è importante per gli aggiornamenti futuri dell'applicazione e per verificare che gli utenti siano informati riguardo alla versione dell'applicazione installata.

-   **[Compattare il file APK](#shrink_apk)** &ndash; La dimensione dell'APK finale può essere notevolmente ridotta usando il linker di Xamarin.Android per il codice gestito e ProGuard per il bytecode Java.

-   **[Proteggere l'applicazione](#protect_app)** &ndash; Impedire agli utenti malintenzionati di eseguire operazioni di debug, reverse engineering o manomissione dell'applicazione disabilitando il debug, nascondendo il codice gestito, aggiungendo funzionalità anti-debug e anti-manomissione e usando la compilazione nativa.

-   **[Impostare proprietà per la creazione di pacchetti](#Set_Packaging_Properties)** &ndash; Le proprietà per la creazione di pacchetti controllano la creazione del pacchetto dell'applicazione Android (APK). Questo passaggio consente di ottimizzare l'APK, di proteggerne gli asset e personalizzare il pacchetto in base alle esigenze.

-   **[Compilare](#Compile)** &ndash; Questo passaggio consente di compilare il codice e gli asset per verificare che la compilazione avvenga in modalità di rilascio.

-   **[Archiviare per la pubblicazione](#archive)** &ndash; In questo passaggio si compila l'app e la si inserisce in un archivio per la firma e la pubblicazione.

Ognuno di questi passaggi è descritto in dettaglio di seguito.

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>Specificare l'icona dell'applicazione

È consigliabile che ogni applicazione Xamarin.Android specifichi l'icona dell'applicazione. Alcuni marketplace di applicazioni non consentono che un'applicazione Android venga pubblicata senza la relativa icona. La proprietà `Icon` dell'attributo `Application` viene usata per specificare l'icona dell'applicazione per un progetto Xamarin.Android.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio 2015 e versioni successive specificare l'icona dell'applicazione usando la sezione **Manifesto Android** delle **proprietà** del progetto, come illustrato nello screenshot seguente:

[![Impostare l'icona dell'applicazione](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In Visual Studio per Mac è anche possibile specificare l'icona dell'applicazione usando la sezione **Applicazione Android** delle **opzioni del progetto**, come illustrato nello screenshot seguente:

[![Impostare l'icona dell'applicazione](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png#lightbox)

-----

In questi esempi, `@drawable/icon` fa riferimento a un file di icona disponibile in **Resources/drawable/icon.png** (si noti che l'estensione **png** non è inclusa nel nome di risorsa). L'attributo può anche essere dichiarato nel file **Properties\AssemblyInfo.cs**, come illustrato nel frammento di esempio seguente:

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

In genere, `using Android.App` viene dichiarato all'inizio di **AssemblyInfo.cs** (lo spazio dei nomi dell'attributo `Application` è `Android.App`). Tuttavia, potrebbe essere necessario aggiungere l'istruzione `using` se non è già presente.


<a name="Versioning" />

## <a name="version-the-application"></a>Specificare la versione dell'applicazione

Il controllo delle versioni è importante per la distribuzione e la manutenzione delle applicazioni Android. Senza una qualche forma di controllo delle versioni, è difficile determinare se e come un'applicazione debba essere aggiornata. Per facilitare il controllo delle versioni, Android riconosce due tipi diversi di informazioni: 

-   **Numero di versione**: un valore intero, usato internamente da Android e dall'applicazione, che rappresenta la versione dell'applicazione. La maggior parte delle applicazioni all'inizio hanno questo valore impostato su 1, che si incrementa a ogni compilazione. Tale valore non ha alcuna relazione o affinità con l'attributo del nome di versione (vedere sotto). Applicazioni e servizi di pubblicazione non devono rendere visualizzabile il valore agli utenti. Questo valore viene archiviato nel file **AndroidManifest.xml** come `android:versionCode`. 

-   **Nome della versione**: una stringa che viene usata solo per comunicare all'utente informazioni sulla versione dell'applicazione installata in un dispositivo specifico. Il nome della versione deve essere visualizzato agli utenti o in Google Play. Questa stringa non viene usata internamente da Android. Il nome della versione può essere qualsiasi valore stringa che consenta a un utente di identificare la build installata nel dispositivo. Questo valore viene archiviato nel file **AndroidManifest.xml** come `android:versionName`. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio questi valori possono essere impostati nella sezione **Manifesto Android** delle **proprietà** del progetto, come illustrato nello screenshot seguente:

[![Impostare il numero di versione](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Questi valori possono essere impostati usando la sezione **Compila > Applicazione Android** di **Opzioni progetto**, come illustra lo screenshot seguente:

[![Impostare il numero di versione](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png#lightbox)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>Compattare il file APK

Le dimensioni dei file APK di Xamarin.Android possono essere ridotte usando una combinazione del linker di Xamarin.Android, che rimuove il codice *gestito* non necessario, con lo strumento *ProGuard* dell'SDK di Android, che rimuove il *bytecode Java* non in uso. Il processo di compilazione usa in primo luogo il linker di Xamarin.Android per ottimizzare l'app a livello di codice gestito (C#) e in un secondo momento usa ProGuard, se è abilitato, per ottimizzare l'APK a livello di bytecode Java.


### <a name="configure-the-linker"></a>Configurare il linker

La modalità di rilascio disattiva il runtime condiviso e attiva il collegamento in modo che l'applicazione usi solo le parti di Xamarin.Android necessarie in fase di runtime. Il *linker* in Xamarin.Android usa l'analisi statica per determinare quali assembly, tipi e membri di tipo vengono usati o a cui viene fatto riferimento da un'applicazione Xamarin.Android. Il linker elimina quindi tutti gli assembly, i tipi e i membri inutilizzati o a cui non si fa riferimento. Ciò può comportare una riduzione significativa delle dimensioni del pacchetto. Si consideri l'esempio [HelloWorld](~/android/deploy-test/linker.md) per cui si verifica una riduzione dell'83% delle dimensioni finali dell'APK: 

-   Configurazione: nessuna &ndash; Xamarin.Android 4.2.5 Dimensioni = 17,4 MB.

-   Configurazione: solo assembly SDK &ndash; Xamarin.Android 4.2.5 Dimensioni = 3,0 MB.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Impostare le opzioni del linker nella sezione **Opzioni Android** delle **proprietà** del progetto:

[![Opzioni linker](images/vs/03-linking-sml.png)](images/vs/03-linking.png#lightbox)

Il menu a discesa **Collegamento** include le seguenti opzioni per il controllo del linker:

-   **Nessuno** &ndash; Disattiva il linker e non viene eseguito alcun collegamento.

-   **Solo assembly SDK** &ndash; Verranno collegati solo gli assembly [richiesti da Xamarin.Android](~/cross-platform/internals/available-assemblies.md). 
    Gli altri assembly non verranno collegati.

-   **Assembly SDK e utente** &ndash; Consente di collegare tutti gli assembly richiesti dall'applicazione, non solo quelli richiesti da Xamarin.Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Impostare le opzioni del linker nella scheda **Linker** della sezione **Compilazione Android** delle **opzioni del progetto**, come indicato nello screenshot seguente:

[![Opzioni linker](images/xs/03-linking-sml.png)](images/xs/03-linking.png#lightbox)

Di seguito sono riportate le opzioni di controllo del linker:

-   **Non collegare** &ndash; Disattiva il linker e non viene eseguito alcun collegamento.

-   **Collega solo assembly SDK** &ndash; Verranno collegati solo gli assembly [richiesti da Xamarin.Android](~/cross-platform/internals/available-assemblies.md). Gli altri assembly non verranno collegati.

-   **Collega tutti gli assembly** &ndash; Consente di collegare tutti gli assembly richiesti dall'applicazione, non solo quelli richiesti da Xamarin.Android.

-----

Il collegamento può produrre alcuni effetti collaterali imprevisti, quindi è importante che un'applicazione venga di nuovo testata in modalità di rilascio in un dispositivo fisico.


### <a name="proguard"></a>ProGuard

*ProGuard* è uno strumento Android SDK che si collega al codice Java e consente di offuscarlo. ProGuard viene in genere usato per creare applicazioni più piccole, riducendo la superficie delle raccolte di grandi dimensioni incluse, come ad esempio Google Play Services, nell'APK. ProGuard rimuove i bytecode Java inutilizzati, riducendo le dimensioni dell'app risultante. Ad esempio, se si usa ProGuard su app Xamarin.Android di piccole dimensioni, in genere si ottiene una riduzione delle dimensioni pari al 24%. Se si usa ProGuard in app di grandi dimensioni con diverse dipendenze di raccolte, la riduzione delle dimensioni ottenuta è nettamente superiore. 

ProGuard non è un'alternativa al linker di Xamarin.Android. Il linker di Xamarin.Android collega il codice *gestito*, mentre ProGuard collega il bytecode Java. Il processo di compilazione usa in primo luogo il linker di Xamarin.Android per ottimizzare il codice (C#) gestito nell'app e in un secondo momento usa ProGuard, se è abilitato, per ottimizzare l'APK a livello di bytecode Java. 

Quando l'opzione **Abilita ProGuard** è selezionata, Xamarin.Android esegue lo strumento ProGuard sull'APK risultante. Viene generato un file di configurazione ProGuard che ProGuard usa in fase di compilazione. Xamarin.Android supporta anche azioni di compilazione *ProguardConfiguration* personalizzate. È possibile aggiungere un file di configurazione ProGuard personalizzato al progetto, fare clic con il pulsante destro del mouse sul file e selezionarlo come un'azione di compilazione come illustrato nell'esempio seguente: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Azione di compilazione di ProGuard](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Azione di compilazione di ProGuard](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png#lightbox)

-----

ProGuard è disabilitato per impostazione predefinita. L'opzione **Abilita ProGuard** è disponibile solo quando il progetto è impostato sulla modalità **Release** (Rilascio). Tutte le azioni di compilazione ProGuard vengono ignorate a meno che l'opzione **Abilita ProGuard** non sia selezionata. La configurazione ProGuard di Xamarin.Android non offusca l'APK e non è possibile abilitare l'offuscamento, neanche con i file di configurazione personalizzati. Se si vuole usare l'offuscamento, vedere [Protezione dell'applicazione con Dotfuscator](~/android/deploy-test/release-prep/index.md#dotfuscator). 

Per informazioni più dettagliate sull'uso dello strumento ProGuard, vedere [ProGuard](~/android/deploy-test/release-prep/proguard.md).

<a name="protect_app" />

## <a name="protect-the-application"></a>Proteggere l'applicazione

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>Disabilitare il debug

Durante lo sviluppo di un'applicazione Android, il debug viene eseguito con *Java Debug Wire Protocol* (JDWP). Si tratta di una tecnologia che consente agli strumenti come **adb** di comunicare con JVM a fini di debug. JDWP è attivato per impostazione predefinita per le compilazioni di debug delle applicazioni Xamarin.Android. JDWP è importante durante lo sviluppo, ma può rappresentare un problema per la sicurezza per le applicazioni già rilasciate. 

> [!IMPORTANT]
> Disabilitare sempre lo stato di debug in un'applicazione già rilasciata perché, tramite JDWP, è possibile accedere al processo Java ed eseguire codice arbitrario nel contesto dell'applicazione se lo stato di debug non è disabilitato.

Il Manifesto Android contiene l'attributo `android:debuggable`, che controlla se l'applicazione può essere sottoposta a debug. È opportuno impostare l'attributo `android:debuggable` su `false`. Il modo più semplice per eseguire questa operazione è aggiungere un'istruzione di compilazione condizionale in **AssemblyInfo.cs**: 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

Si noti che le compilazioni di debug impostano automaticamente alcune autorizzazioni per semplificare l'esecuzione del debug, ad esempio **Internet** e **ReadExternalStorage**. Le compilazioni di rilascio, tuttavia, usano solo le autorizzazioni configurate in modo esplicito. Se si ritiene che il passaggio a una compilazione di rilascio comporti la perdita nell'app di un'autorizzazione che era disponibile nella compilazione di debug, verificare che tale autorizzazione sia stata attivata in modo esplicito nell'elenco **Autorizzazioni necessarie** come descritto in [Permissions](~/android/app-fundamentals/permissions.md) (Autorizzazioni). 
 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>Protezione dell'applicazione con Dotfuscator

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Anche con il [debug disabilitato](#Disable_Debugging), è comunque possibile che gli utenti malintenzionati riescano a creare un nuovo pacchetto di un'applicazione, aggiungendo o rimuovendo opzioni di configurazione o autorizzazioni. Ciò consente di decodificare o manomettere l'applicazione o di eseguirne il debug.
[Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) consente di nascondere il codice gestito e inserire il codice di rilevamento dello stato di sicurezza del runtime in un'app Xamarin.Android in fase di compilazione.

Dotfuscator CE è incluso in Visual Studio, tuttavia solo in Visual Studio 2015 Update 3 (e versioni successive) è disponibile la versione corretta per Xamarin.Android. Per usare Dotfuscator, fare clic su **Strumenti > PreEmptive Protection - Dotfuscator**.

Per configurare Dotfuscator CE, vedere le istruzioni relative all'[uso di Dotfuscator Community Edition con Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator).
Dopo la configurazione, Dotfuscator CE proteggerà automaticamente ogni build creata.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Anche con il [debug disabilitato](#Disable_Debugging), è comunque possibile che gli utenti malintenzionati riescano a creare un nuovo pacchetto di un'applicazione, aggiungendo o rimuovendo opzioni di configurazione o autorizzazioni. Ciò consente di decodificare o manomettere l'applicazione o di eseguirne il debug.
Benché non supporti Visual Studio for Mac, è possibile usare [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) con Visual Studio per nascondere il codice gestito e inserire il codice di rilevamento dello stato di sicurezza del runtime in un'app Xamarin.Android in fase di compilazione.

Per configurare Dotfuscator CE, vedere le istruzioni relative all'[uso di Dotfuscator Community Edition con Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator).
Dopo la configurazione, Dotfuscator CE proteggerà automaticamente ogni build creata.

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>Crea un bundle di assembly nel codice nativo

Quando questa opzione è abilitata, gli assembly vengono aggregati in una raccolta condivisa nativa. Questa opzione mantiene il codice sicuro in quanto protegge gli assembly incorporandoli in file binari nativi.

Questa opzione richiede una licenza Enterprise ed è disponibile solo quando l'opzione **Usa Fast Deployment** è disabilitata. L'opzione **Crea un bundle di assembly nel codice nativo** è disabilitata per impostazione predefinita.

Si noti che l'opzione **Crea un bundle di assembly nel codice nativo** *non* significa che gli assembly vengono compilati in codice nativo. Non è possibile usare [**Compilazione AOT**](#aot) per compilare gli assembly in codice nativo in quanto attualmente si tratta solo di una funzionalità sperimentale che non viene usata in produzione.

<a name="aot" />

### <a name="aot-compilation"></a>Compilazione AOT

L'opzione **Compilazione AOT** nella pagina [Proprietà per creazione pacchetto](#Set_Packaging_Properties) consente la compilazione di assembly AOT (Ahead Of Time). Quando questa opzione è abilitata, viene ridotto il sovraccarico all'avvio di JIT mediante la precompilazione degli assembly prima del runtime. Il codice nativo risultante e gli assembly non compilati vengono inclusi nell'APK. Questo consente tempi di avvio dell'applicazione ridotti ma a scapito di dimensioni dell'APK leggermente superiori.

L'opzione **Compilazione AOT** richiede una licenza Enterprise o superiore. L'opzione **Compilazione AOT** è disponibile solo quando il progetto è configurato per la modalità di rilascio, ed è disabilitata per impostazione predefinita. Per altre informazioni sulla compilazione AOT, vedere [AOT](http://www.mono-project.com/docs/advanced/aot/).


#### <a name="llvm-optimizing-compiler"></a>Compilatore ottimizzatore LLVM

Il _Compilatore ottimizzatore LLVM_  crea codice compilato più rapido e di dimensioni minori e converte gli assembly compilati con AOT in codice nativo, ma a scapito di tempi di compilazione più lunghi. Il compilatore LLVM è disabilitato per impostazione predefinita. Per usare il compilatore LLVM, è necessario per prima cosa abilitare l'opzione **Compilazione AOT** nella pagina [Proprietà per creazione pacchetto](#Set_Packaging_Properties).


> [!NOTE]
> L'opzione **Compilatore ottimizzatore LLVM** richiede una licenza Business.  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>Impostare le proprietà per la creazione di pacchetti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Le proprietà per la creazione di pacchetti possono essere impostate nella sezione **Opzioni Android** delle **proprietà** del progetto, come illustrato nello screenshot seguente:

[![Proprietà per la creazione di pacchetti](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Le proprietà per la creazione di pacchetti possono essere impostate in **Opzioni Android**, come illustrato nello screenshot seguente:

[![Proprietà per la creazione di pacchetti](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png#lightbox)

-----

Molte di queste proprietà, ad esempio **Usa runtime condiviso** e **Usa Fast Deployment** sono destinate alla modalità di debug. Tuttavia, quando l'applicazione è configurata per la modalità di rilascio, esistono altre impostazioni che determinano il modo in cui l'app [viene ottimizzata in termini di dimensioni e velocità di esecuzione](#shrink_apk), [è protetta da eventuali manomissioni](#protect_app) e può essere inserita in un pacchetto per supportare diverse architetture e limitazioni di dimensione.


### <a name="specify-supported-architectures"></a>Specificare le architetture supportate

Quando si prepara un'app Xamarin.Android per il rilascio, è necessario specificare le architetture della CPU supportate. Un singolo APK può contenere codice macchina per supportare più architetture diverse. Vedere [Architetture CPU](~/android/app-fundamentals/cpu-architectures.md) per informazioni dettagliate sul supporto di più architetture della CPU.


### <a name="generate-one-package-apk-per-selected-abi"></a>Generare un pacchetto (file APK) per ogni ABI selezionato

Quando questa opzione è abilitata, viene creato un APK per ogni ABI supportato, selezionato nella scheda **Avanzate**, come descritto in [Architetture CPU](~/android/app-fundamentals/cpu-architectures.md), anziché un singolo APK di grandi dimensioni per tutti gli ABI supportati. L'opzione è disponibile solo quando il progetto è configurato per la modalità di rilascio, ed è disabilitata per impostazione predefinita.


### <a name="multi-dex"></a>Multidex

Quando l'opzione **Abilita multidex** è abilitata, vengono usati gli strumenti Android SDK per ignorare il limite dei 65.000 metodi presente nel formato di file **DEX**. Il limite a 65.000 metodi si basa sul numero di metodi di Java cui un'app _fa riferimento_, compresi quelli nelle eventuali raccolte da cui l'applicazione dipende. Non si basa sul numero di metodi _scritti nel codice sorgente_. Se un'applicazione definisce solo pochi metodi ma ne usa molti, o usa raccolte di grandi dimensioni, è possibile che il limite di 65.000 venga superato.

È possibile che un'app non usi tutti i metodi in tutte le raccolte cui fa riferimento, quindi è possibile che uno strumento come ProGuard (vedere sopra) possa rimuovere i metodi non usati dal codice. La procedura consigliata consiste nell'abilitare **Abilita multidex** solo se assolutamente necessario, ovvero se l'app fa riferimento a più di 65.000 metodi Java anche dopo l'uso di ProGuard.

Per altre informazioni su multidex, vedere [Configurare le app con più di 64.000 metodi](http://developer.android.com/tools/building/multidex.html).

<a name="Compile" />

## <a name="compile"></a>Compile

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Al termine di tutti i passaggi precedenti, l'app è pronta per la compilazione. Selezionare **Compila > Ricompila soluzione** per verificare che la compilazione avvenga in modalità di rilascio. Si noti che questo passaggio non genera ancora un file APK.

L'articolo sulla [firma del pacchetto dell'app](~/android/deploy-test/signing/index.md) descrive le procedure di creazione del pacchetto e di firma più in dettaglio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Al termine di tutti i passaggi precedenti, compilare l'applicazione, selezionando **Compila > Compila tutto**, per verificare che la compilazione avvenga in modalità di rilascio. Si noti che questo passaggio non genera ancora un file APK.

-----


<a name="archive" />

## <a name="archive-for-publishing"></a>Archiviare per la pubblicazione

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per iniziare il processo di pubblicazione, fare clic con il pulsante destro del mouse sul progetto nel **riquadro della soluzione** e selezionare **Archivio...**  dal menu di scelta rapida:

[![Archiviare l'app](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png#lightbox)

**Archivio...** avvia **Gestione archivi** e inizia il processo di archiviazione del bundle dell'app, come illustrato in questo screenshot:

[![Gestione archivi](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png#lightbox)

Un altro modo per creare un archivio è fare clic con il pulsante destro del mouse sulla soluzione nel **riquadro della soluzione** e selezionare **Archivia tutti...**  per compilare la soluzione e archiviare tutti i progetti Xamarin in grado di generare un archivio:

[![Archivia tutti](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png#lightbox)


Sia **Archivio** che **Archivia tutti** avviano automaticamente **Gestione archivi**. Per avviare direttamente **Gestione archivi**, fare clic sulla voce di menu **Strumenti > Gestione archivi...** :

[![Avviare Gestione archivi](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png#lightbox)

Per visualizzare gli archivi della soluzione, fare clic con il pulsante destro del mouse sul nodo **Soluzione** e selezionare **Visualizza archivi**:

[![Visualizza archivi](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png#lightbox)

### <a name="the-archive-manager"></a>Gestione archivi

La funzionalità **Gestione archivi** è costituita da un **elenco di soluzioni**, un **elenco di archivi** e un **riquadro dei dettagli**:

[![Riquadri di Gestione archivi](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png#lightbox)

L'**elenco di soluzioni** visualizza tutte le soluzioni con almeno un progetto archiviato. L'**elenco di soluzioni** comprende le seguenti sezioni:

* **Soluzione corrente** &ndash; Visualizza la soluzione corrente. Si noti che quest'area può essere vuota se per la soluzione corrente non esiste un archivio.
* **Tutti gli archivi** &ndash; Visualizza tutte le soluzioni con un archivio.
* Casella di testo **Ricerca** (in alto) &ndash; Filtra le soluzioni elencate nell'elenco **Tutti gli archivi** in base alla stringa di ricerca immessa nella casella di testo.

L'**elenco di archivi** visualizza l'elenco di tutti gli archivi per la soluzione selezionata. L'**elenco di archivi** comprende le seguenti sezioni:

* **Nome della soluzione selezionata** &ndash; Nome della soluzione selezionata nell'**elenco di soluzioni**. Tutte le informazioni visualizzate nell'**elenco di archivi** fa riferimento a questa soluzione selezionata.
* **Filtro Piattaforme** &ndash; Campo che consente di filtrare gli archivi in base al tipo di piattaforma (ad esempio iOS o Android).
* **Archivi** &ndash; Elenco degli archivi per la soluzione selezionata. Ogni voce di questo elenco include il nome del progetto, la data di creazione e la piattaforma. Può anche visualizzare informazioni aggiuntive, ad esempio lo stato di avanzamento quando una voce viene archiviata o pubblicata.

Il **riquadro dei dettagli** visualizza informazioni aggiuntive su ogni archivio. Consente inoltre all'utente di avviare il flusso di lavoro di distribuzione o aprire la cartella in cui è stata creata la distribuzione. La sezione **Commenti per la build** consente di includere commenti sulla compilazione nell'archivio.

### <a name="distribution"></a>Distribuzione

Quando una versione archiviata dell'applicazione è pronta per la pubblicazione, selezionare l'archivio in **Gestione archivi** e fare clic sul pulsante **Distribuisci...**:

[![Pulsante Distribuisci](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png#lightbox)

La finestra di dialogo **Canale di distribuzione** visualizza informazioni sull'app, un'indicazione dell'avanzamento del flusso di lavoro di distribuzione e una selezione di canali di distribuzione. Alla prima esecuzione vengono visualizzate due opzioni:

[![Selezionare il canale di distribuzione](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png#lightbox)

È possibile scegliere uno dei seguenti canali di distribuzione:

* **Ad hoc** &ndash; Salva su disco un APK firmato che può essere trasferito localmente ai dispositivi Android. Passare alla sezione relativa alla [firma del pacchetto dell'app](~/android/deploy-test/signing/index.md) per sapere come creare un'identità di firma di Android, creare un nuovo certificato di firma per le applicazioni Android e pubblicare una versione _ad hoc_ nel disco. Questo è un buon metodo per creare un file APK per il test.

* **Google Play** &ndash; Pubblica un file APK firmato in Google Play. Vedere la sezione relativa alla [pubblicazione in Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) per informazioni su come firmare e pubblicare un file APK in Google Play Store.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per avviare il processo di pubblicazione, selezionare **Compila > Archivia per la pubblicazione**:

[![Archiviare per la pubblicazione](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png#lightbox)

**Archivia per la pubblicazione** consente di compilare il progetto e lo raggruppa in un file di archivio. L'opzione di menu **Archivia tutti** consente di archiviare tutti i progetti archiviabili nella soluzione. Entrambe le opzioni aprono automaticamente **Gestione archivi** al termine delle operazioni di compilazione e creazione di bundle:

[![Visualizzazione degli archivi](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png#lightbox)

In questo esempio **Gestione archivi** elenca solo un'applicazione archiviata, **MyApp**. Si noti che il campo del commento consente di salvare un breve commento con l'archivio. Per pubblicare una versione archiviata di un'applicazione Xamarin.Android, selezionare l'app in **Gestione archivi** e fare clic su **Firma e distribuisci...**  come illustrato in precedenza. Viene aperta la finestra di dialogo **Firma e distribuisci** che presenta due opzioni:

[![Firma e distribuisci](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png#lightbox)


Da qui è possibile selezionare il canale di distribuzione:

-   **Ad hoc**: salva su disco un pacchetto dell'applicazione Android firmato in modo che possa essere trasferito localmente ai dispositivi Android. Passare alla sezione relativa alla [firma del pacchetto dell'app](~/android/deploy-test/signing/index.md) per sapere come creare un'identità di firma di Android, creare un nuovo certificato di firma per le applicazioni Android e pubblicare una versione &ldquo;ad hoc&rdquo; nel disco. Questo è un buon metodo per creare un file APK per il test.


-   **Google Play** &ndash; Pubblica un file APK firmato in Google Play.
    Vedere la sezione relativa alla [pubblicazione in Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) per informazioni su come firmare e pubblicare un file APK in Google Play Store.

-----


## <a name="related-links"></a>Collegamenti correlati

- [Dispositivi multi-core e Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [Architetture CPU](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](http://www.mono-project.com/docs/advanced/aot/)
- [Compattare il codice e le risorse](http://developer.android.com/tools/help/proguard.html)
- [Configurare le app con più di 64.000 metodi](http://developer.android.com/tools/building/multidex.html)
