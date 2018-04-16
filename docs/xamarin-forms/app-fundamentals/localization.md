---
title: Localizzazione
description: App xamarin. Forms possono essere localizzate mediante i file di risorse .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 7cae53187c9bc35d55f34dca664e28280cdab062
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="localization"></a>Localizzazione

_App xamarin. Forms possono essere localizzate mediante i file di risorse .NET._

## <a name="overview"></a>Panoramica

Il meccanismo predefinito per la localizzazione di applicazioni .NET viene utilizzato [file RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e le classi di `System.Resources` e `System.Globalization` spazi dei nomi. I file RESX che contiene le stringhe tradotte sono incorporati nell'assembly xamarin. Forms, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato per le traduzioni. Il testo di conversione può essere recuperato nel codice.

### <a name="sample-code"></a>Codice di esempio

Sono disponibili due esempi relativi a questo documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) è dimostrare i concetti spiegati molto semplice. I frammenti di codice illustrati di seguito sono tutti da questo esempio.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) è un'app di lavoro di base che Usa queste tecniche di localizzazione.

#### <a name="shared-projects-are-not-recommended"></a>Non sono consigliati progetti condivisi

Nell'esempio TodoLocalized è incluso un [demo progetto condiviso](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) tuttavia a causa di limitazioni del sistema di compilazione i file di risorse non si ottengono un **. designer.cs** file generato che interrompe la possibilità di accedere convertire le stringhe [fortemente tipizzate nel codice](~/xamarin-forms/app-fundamentals/localization.md).

Il resto di questo documento si riferisce ai progetti utilizzando il modello libreria di classi Portabile xamarin. Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalizing Xamarin.Forms Code

**Globalizzazione** un'applicazione è il processo di renderla "world ready". Ciò significa che la scrittura di codice che è in grado di visualizzare diverse lingue.

Una delle parti principali di globalizzazione di un'applicazione crea l'interfaccia utente in modo che non esiste alcun *hardcoded* testo. Al contrario, qualsiasi elemento visualizzato all'utente deve essere recuperata da un set di stringhe che sono stati tradotti nella lingua scelta.

In questo documento verrà esaminato come utilizzare i file RESX per archiviare le stringhe e recuperarli per la visualizzazione in base alle preferenze dell'utente.

Gli esempi di destinazione le lingue inglese, francese, spagnolo, tedesco, cinese, giapponese, russo e portoghese (Brasile). Le applicazioni possono essere tradotte in come numero di lingue come richiesto.

### <a name="adding-resources"></a>Aggiunta di risorse

Il primo passaggio per la globalizzazione di un'applicazione di libreria di classi Portabile xamarin. Forms è aggiungendo i file di risorse RESX che verranno utilizzati per archiviare tutto il testo utilizzato nell'app. È necessario aggiungere un file RESX che contiene il testo predefinito e quindi aggiungere ulteriori file RESX per ogni lingua che si desidera supportare.

#### <a name="base-language-resource"></a>Risorsa di lingua di base

Il file di risorse (RESX) di base con le stringhe di linguaggio predefinito (gli esempi si presuppongono il che inglese è la lingua predefinita). Aggiungere il file al progetto di codice comune xamarin. Forms facendo clic sul progetto e scegliendo **Aggiungi > Nuovo File...** .

Scegliere un nome significativo, ad esempio **AppResources** e premere **OK**.

[![Aggiungere File di risorse](localization-images/resx-new-file-sml.png "nuova finestra di dialogo File")](localization-images/resx-new-file.png#lightbox "nuova finestra di dialogo File")

Verranno aggiunti due file al progetto:

* **AppResources.resx** file in cui traducibili sono archiviate in formato XML.
* **AppResources.designer.cs** file che dichiara una classe parziale per contenere riferimenti a tutti gli elementi creati nel file RESX XML.

Struttura della soluzione visualizzerà i file correlati. Il file RESX *deve* modificato per aggiungere nuove stringhe traducibili; il **. designer.cs** file dovrebbe *non* essere modificato.

![](localization-images/appresources-tree.png "File AppResources.resx")

##### <a name="string-visibility"></a>Visibilità di stringa

Per impostazione predefinita quando vengono generati i riferimenti alle stringhe fortemente tipizzate, saranno `internal` all'assembly. In questo modo lo strumento di compilazione predefinito per i file RESX genera il **. designer.cs** file con `internal` proprietà.

Selezionare il **AppResources.resx** file e visualizzare il **proprietà** pad per vedere in questo strumento di compilazione è configurare. Nella schermata seguente viene illustrato il **lo strumento personalizzato: ResXFileCodeGenerator**.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-internal-sml.png "Finestra proprietà per AppResources.Resx")](localization-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "Proprietà di riempimento per AppResources.Resx")](localization-images/xs-resx-internal.png#lightbox)

-----

Per rendere le proprietà della stringa fortemente tipizzato `public`, è necessario modificare manualmente la configurazione per **lo strumento personalizzato: PublicResXFileCodeGenerator**, come illustrato nella schermata seguente:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-public-sml.png "Finestra proprietà per AppResources.Resx")](localization-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "Proprietà di riempimento per AppResources.Resx")](localization-images/xs-resx-internal.png#lightbox)


[![](localization-images/xs-resx-public-sml.png "Proprietà di riempimento per AppResources.Resx")](localization-images/xs-resx-public.png#lightbox)

-----

Questa modifica è facoltativa ed è solo necessario se si desidera fare riferimento a stringhe localizzate in assembly diversi (ad esempio, se si inserisce i file RESX in un assembly diverso per il codice). L'esempio di questo argomento lascia le stringhe `internal` perché sono definiti nello stesso assembly xamarin. Forms PCL in cui vengono utilizzati.

È necessario impostare lo strumento personalizzato nel file RESX base, come illustrato sopra. non è necessario impostare *qualsiasi* strumento di compilazione nei file specifici della lingua RESX illustrati nelle sezioni seguenti.

##### <a name="editing-the-resx-file"></a>Modifica del file RESX

In Visual Studio per Mac. non è Sfortunatamente disponibile alcun editor RESX incorporato Aggiunta di nuove stringhe traducibili richiede l'aggiunta di un nuovo codice XML `data` elemento per ogni stringa. Ogni `data` elemento può contenere i seguenti elementi:

* `name` attributo (obbligatorio) è la chiave di questa stringa convertibile. Deve essere un valido nome - proprietà c# quindi spazi o caratteri speciali non sono consentiti.
* `value` elemento (obbligatorio), che è la stringa effettiva che viene visualizzata nell'applicazione.
* `comment` elemento (facoltativo) può contenere istruzioni per la funzione di conversione che viene illustrato come utilizzare questa stringa.
* `xml:space` (facoltativo) per controllare la modalità di mantenimento spaziatura nella stringa di attributo.

Alcuni esempi `data` gli elementi riportati di seguito:

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

Come l'applicazione viene scritta, tutte le parti del testo visualizzato all'utente devono essere aggiunte al file di risorse RESX di base in un nuovo `data` elemento. Si consiglia di includere `comment`s quanto possibile, per garantire una traduzione di alta qualità.

> [!NOTE]
> Visual Studio (incluso l'edizione gratuita Community) contiene un editor di base RESX. Se si ha accesso a un computer Windows, che può essere un modo pratico per aggiungere e modificare le stringhe nei file RESX.

#### <a name="language-specific-resources"></a>Risorse specifiche della lingua

In genere, la traduzione effettiva delle stringhe di testo predefinito non verrà eseguito fino a quando non sono stati scritti grandi blocchi dell'applicazione (nel qual caso il file RESX predefinito contiene un numero elevato di stringhe). È comunque consigliabile aggiungere le risorse specifiche della lingua nelle prime fasi del ciclo di sviluppo, popolando facoltativamente con il testo tradotto per testare il codice di localizzazione.

Un file RESX aggiuntivo viene aggiunto per ogni lingua che si desidera supportare.
File di risorse specifiche della lingua devono seguire una convenzione di denominazione specifica: utilizzare lo stesso nome file come file di risorse di base (ad es. **AppResources**) seguito da un punto (.) e quindi il codice della lingua. Esempi semplici includono:

* **AppResources.fr.resx** -traduzioni in lingua francese.
* **AppResources.es.resx** -traduzioni di lingua spagnola.
* **AppResources.de.resx** -traduzioni in lingua tedesca.
* **AppResources.ja.resx** -traduzioni in lingua giapponese.
* **AppResources.zh Hans.resx** - cinese (semplificato) traduzioni delle lingue.
* **AppResources.zh Hant.resx** - cinese (tradizionale) traduzioni delle lingue.
* **AppResources.pt.resx** -traduzioni in lingua portoghese.
* **AppResources.pt BR.resx** -traduzioni in lingua portoghese (Brasile).

Il modello generale consiste nell'utilizzare i codici di lingua di due lettere, ma vi sono alcuni esempi (ad esempio il cinese) in cui viene utilizzato un formato diverso e altri esempi (ad esempio il portoghese brasiliano) in cui è richiesto un identificatore di impostazioni locali di quattro caratteri.

Questi file di risorse specifiche della lingua *non* richiedono un **. designer.cs** parziale classe pertanto possono essere aggiunti come file XML standard, con la **azione di compilazione: EmbeddedResource**impostato. Questa schermata mostra una soluzione contenente i file di risorse specifiche della lingua:

![](localization-images/appresources-langs.png "File di risorse specifiche della lingua")

Come un'applicazione viene sviluppata e il file RESX base è il testo aggiunto, è necessario inviarlo al traduttori che convertirà ogni `data` elemento e restituire un file di risorse specifiche della lingua (utilizzando la convenzione di denominazione mostrata) da includere nell'app. Di seguito sono riportati alcuni esempi di 'tradotti':

**AppResources.es.resx (spagnolo)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (giapponese)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (portoghese (Brasile))**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Solo il `value` elemento deve essere aggiornato dal convertitore - il `comment` non può essere convertito. Ricorda: quando si modifica il file XML di escape per i caratteri riservati come `<`, `>`, `&` con `&lt;`, `&gt;`, e `&amp;` se sono visualizzate nel `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Utilizzo delle risorse nel codice

Le stringhe nei file di risorse RESX saranno disponibili per l'utilizzo nel codice dell'interfaccia utente utilizzando il `AppResources` classe. Il `name` assegnato a ogni stringa di RESX file diventa una proprietà su tale classe a cui può fare riferimento nel codice di xamarin. Forms, come illustrato di seguito:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

L'interfaccia utente in iOS, Android e i renderer di piattaforme di Windows come è prevedibile, tranne che ora è possibile tradurre l'app in più lingue, perché il testo viene caricato da una risorsa anziché a livello di codice. Di seguito è riportata una schermata che illustra l'interfaccia utente in ogni piattaforma prima di conversione:

![](localization-images/simple-example-english.png "Interfacce utente multipiattaforma prima della conversione")

### <a name="troubleshooting"></a>Risoluzione dei problemi

#### <a name="testing-a-specific-language"></a>Test di una lingua specifica

Può essere difficile attivare un dispositivo o il simulatore in lingue diverse, particolarmente durante lo sviluppo quando si desidera testare rapidamente diverse impostazioni cultura.

È possibile forzare un linguaggio specifico di essere caricati tramite l'impostazione di `Culture` come illustrato nel frammento di codice seguente:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Questo approccio, impostare le impostazioni cultura direttamente sulla `AppResources` classe: può essere usata anche per implementare un selettore di lingua all'interno dell'app (invece di usare impostazioni locali del dispositivo).

#### <a name="loading-embedded-resources"></a>Caricamento risorse incorporate

Il seguente frammento di codice è utile quando si tenta di eseguire il debug di problemi con le risorse incorporate (ad esempio i file RESX). Aggiungere il codice per l'app (nelle prime fasi del ciclo di vita app) e verranno visualizzate tutte le risorse incorporate nell'assembly, che mostra l'identificatore completo della risorsa:

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

Nel **AppResources.Designer.cs** file (intorno *riga 33*), la versione completa *nome di gestione risorse* specificato (ad es. `"UsingResxLocalization.Resx.AppResources"`) simile al codice riportato di seguito:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Controllare il **Output dell'applicazione** per i risultati di debug nel codice sopra riportato, per verificare il corretto le risorse sono elencate (ie. `"UsingResxLocalization.Resx.AppResources"`).

In caso contrario, la `AppResources` classe sarà in grado di caricare le proprie risorse.
Selezionare la casella seguente per risolvere i problemi in cui non è possibile trovare le risorse:

* Spazio dei nomi predefinito per il progetto corrispondente lo spazio dei nomi radice nel **AppResources.Designer.cs** file.
* Se il **AppResources.resx** file si trova in una sottodirectory, il nome della sottodirectory deve far parte dello spazio dei nomi *e* fa parte dell'identificatore di risorsa.
* Il **AppResources.resx** file ha **azione di compilazione: EmbeddedResource**.
* Il **opzioni progetto > codice sorgente > .NET Naming Policies > i nomi di risorse di tipo utilizzare Visual Studio** sia selezionata. È possibile untick questa se si preferisce, tuttavia gli spazi dei nomi utilizzato per fare riferimento alle risorse RESX dovrà aggiornato in tutta l'app.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Non funziona in modalità di DEBUG (solo Android)

Se le stringhe tradotte sta nelle build di rilascio Android, ma non durante il debug, fare clic su di **progetto Android** e selezionare **Opzioni > compilare > compilare Android** e assicurarsi che il **Rapida distribuzione di assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere utilizzata se si siano testando applicazioni localizzate.

### <a name="displaying-the-correct-language"></a>La visualizzazione nella lingua corretta

Finora abbiamo esaminato come scrivere codice in modo che possono essere fornite le traduzioni, ma non di effettivamente. Codice di xamarin. Forms può sfruttare. Risorse di rete per caricare le traduzioni della lingua corretta, ma è necessario eseguire il sistema operativo in ogni piattaforma per determinare il linguaggio in cui l'utente ha selezionato una query.

Poiché alcuni codice specifico della piattaforma è necessario per ottenere preferenze della lingua dell'utente, utilizzare un [servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md) per esporre le informazioni nell'app xamarin. Forms e implementarla per ogni piattaforma.

Prima di definire un'interfaccia per esporre lingua preferita dell'utente, simile al codice riportato di seguito:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

In secondo luogo, utilizzare il [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) in di xamarin. Forms `App` classe per chiamare l'interfaccia e impostare la lingua risorse RESX sul valore corretto. Si noti che non è necessario impostare manualmente questo valore per Windows Phone e la piattaforma Windows universale, poiché il framework di risorse automaticamente riconosce la lingua selezionata per tali piattaforme.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

La risorsa `Culture` deve essere impostata quando si carica l'applicazione in modo che vengono utilizzate le stringhe lingua corretta. È possibile aggiornare questo valore in base agli eventi specifici della piattaforma che possono essere generati su iOS e Android, se l'utente aggiorna le preferenze di lingua, mentre l'app è in esecuzione.

Le implementazioni per le `ILocalize` interfaccia vengono visualizzati di [codice specifico della piattaforma](#Platform-specific_Code) sezione riportata di seguito. Queste implementazioni di sfruttano i vantaggi di questo `PlatformCulture` classe helper:

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Codice specifico della piattaforma

Il codice per rilevare la lingua da visualizzare deve essere specifico della piattaforma poiché iOS, Android e le piattaforme Windows espongono queste informazioni in modi leggermente differenti. Il codice per il `ILocalize` servizio di dipendenza è fornito di seguito per ogni piattaforma, insieme a ulteriori requisiti specifici della piattaforma per verificare il testo localizzato viene eseguito il rendering correttamente.

Il codice specifico della piattaforma deve gestire anche i casi in cui il sistema operativo consente all'utente di un identificatore delle impostazioni locali che non è supportato da configurare. Del NET `CultureInfo` classe. In questi casi è necessario scrivere codice personalizzato per rilevare le impostazioni locali non supportate e sostituire la migliore. Impostazioni locali di NET compatibile.

#### <a name="ios-application-project"></a>Progetto di applicazione iOS

gli utenti di iOS selezionare la lingua preferita separatamente dalla data e ora di formattazione delle impostazioni cultura. Per caricare le risorse corrette per un'app xamarin. Forms, è sufficiente eseguire una query di localizzare il `NSLocale.PreferredLanguages` matrice per il primo elemento.

L'implementazione seguente del `ILocalize` servizio di dipendenza deve essere inserito nel progetto di applicazione iOS. Poiché iOS utilizza caratteri di sottolineatura anziché trattini (che è la rappresentazione standard .NET) il codice sostituisce il carattere di sottolineatura prima di creare un'istanza di `CultureInfo` classe:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Il `try/catch` in blocco il `GetCurrentCultureInfo` metodo simulare il comportamento di fallback in genere utilizzato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare una corrispondenza Chiudi dipende solo la lingua (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di xamarin. Forms, alcune impostazioni locali sono validi in iOS ma non corrispondono a un oggetto valido `CultureInfo` in .NET e il codice tenta di gestire questa situazione.
>
> Ad esempio, iOS **Impostazioni > generale Language &amp; area** schermata consente di impostare il telefono **Language** a **inglese** ma la  **Area** a **Spagna**, che comporta una stringa di impostazioni locali di `"en-ES"`. Quando il `CultureInfo` ha esito negativo di creazione, il codice tenta di utilizzare solo le prime due lettere per selezionare la lingua di visualizzazione.
>
> Gli sviluppatori devono modificare il `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` metodi per gestire i casi specifici necessari per le lingue supportate.


Esistono alcuni elementi dell'interfaccia utente definiti dal sistema che vengono automaticamente convertite da iOS, ad esempio il **eseguita** pulsante il `Picker` controllo. Per forzare iOS per convertire questi elementi è necessario indicare quali lingue è supportato nel **Info. plist** file. È possibile aggiungere questi valori tramite **Info. plist > origine** come illustrato di seguito:

![Le chiavi di localizzazione in Info. plist](localization-images/info-plist.png "le chiavi di localizzazione in Info. plist")

In alternativa, aprire il **Info. plist** file in un editor XML e di modificare direttamente i valori:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

Dopo avere implementato il servizio di dipendenza e aggiornato **Info. plist**, l'app iOS sarà in grado di visualizzare il testo localizzato.

> [!NOTE]
> Si noti che considera Apple portoghese in modo leggermente diverso da quello previsto.
> Da [i documenti](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"Usa pt come ID di lingua per il portoghese è utilizzato in Brasile e pt-PT come ID di lingua per il portoghese è utilizzato in Portogallo"_.
> Ciò significa che, quando portoghese viene scelto delle impostazioni locali non standard, la lingua di fallback sarà portoghese (Brasile) in iOS, a meno che non viene scritto codice per modificare questo comportamento (ad esempio il `ToDotnetFallbackLanguage` sopra).

#### <a name="android-application-project"></a>Progetto di applicazione Android

Android espone le impostazioni locali attualmente selezionata tramite `Java.Util.Locale.Default`e utilizza anche un separatore a carattere di sottolineatura anziché un trattino (che viene sostituito con il codice seguente). Aggiungere l'implementazione del servizio di dipendenza per il progetto di applicazione Android:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Il `try/catch` in blocco il `GetCurrentCultureInfo` metodo simulare il comportamento di fallback in genere utilizzato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare una corrispondenza Chiudi dipende solo la lingua (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di xamarin. Forms, alcune impostazioni locali sono validi in Android ma non corrispondono a un oggetto valido `CultureInfo` in .NET e il codice tenta di gestire questa situazione.
>
> Gli sviluppatori devono modificare il `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` metodi per gestire i casi specifici necessari per le lingue supportate.


Dopo aver aggiunto questo codice al progetto di applicazione Android, sarà in grado di visualizzare automaticamente le stringhe tradotte.

> [!NOTE]
>️ **avviso:** se le stringhe tradotte sta nelle build di rilascio Android, ma non durante il debug, fare clic su di **progetto Android** e selezionare **Opzioni > compilare > Android Compilare** e assicurarsi che il **rapida distribuzione di assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere utilizzata se si siano testando applicazioni localizzate.

#### <a name="windows-application-projects"></a>Progetti di applicazioni Windows

Progetti Windows 8.1 e Windows piattaforma UWP (Universal) non richiede il servizio di dipendenza: queste piattaforme impostate automaticamente le impostazioni cultura della risorsa correttamente.

Implementazione dell'estensione di markup XAML descritto più avanti in questo documento potrebbe richiedere il `ILocalize` implementazione illustrato di seguito per Windows Phone.

##### <a name="windows-phone-80"></a>Windows Phone 8.0

Anche se non utilizzato nel `App` classe, di seguito è l'implementazione di Windows Phone per la `ILocalize` servizio di dipendenza. Aggiungere questa classe al progetto di app di Windows Phone. sarà necessaria se l'implementazione dell'estensione di markup XAML descritto di seguito:

```csharp
[assembly: Dependency(typeof(UsingResxLocalization.WinPhone.Localize))]

namespace UsingResxLocalization.WinPhone
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci) { }
        public System.Globalization.CultureInfo GetCurrentCultureInfo ()
        {
            return System.Threading.Thread.CurrentThread.CurrentUICulture;
        }
    }
}

```

Progetti Windows Phone 8.0 devono essere configurati correttamente per il testo localizzato da visualizzare.
È necessario selezionare le lingue supportate nelle opzioni di progetto *e* il **WMAppManifest.xml** file.
Se queste impostazioni non vengono aggiornate le risorse localizzate di RESX non verranno caricate.

##### <a name="project-options"></a>Opzioni progetto

Pulsante destro del mouse sul progetto Windows Phone e selezionare **proprietà**. Nel **applicazione** scheda segni di graduazione di **impostazioni cultura supportate** che supporta l'applicazione:

[![](localization-images/winphone-projectproperties-sml.png "Proprietà - impostazioni cultura supportate del progetto")](localization-images/winphone-projectproperties.png#lightbox "proprietà - impostazioni cultura supportate del progetto")

##### <a name="wmappmanifestxml"></a>WMAppManifest.xml

Espandere il nodo di proprietà del progetto Windows Phone e fare doppio clic su di **WMAppManifest.xml** file. Fare clic su di **imballaggio** scheda e selezionare tutte le lingue supportate dall'applicazione.

[![](localization-images/winphone-wmappmanifest-sml.png "Lingue supportate WMAppManifest.xml -")](localization-images/winphone-wmappmanifest.png#lightbox "WMAppManifest.xml - le lingue supportate.")

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Espandere il nodo di proprietà del progetto libreria di classe portabile (PCL) e fare doppio clic su di **AssemblyInfo.cs** file. Aggiungere la riga seguente al file per impostare il linguaggio assembly di risorse neutre su inglese:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Informa il gestore delle risorse delle impostazioni cultura predefinite dell'app, assicurando pertanto che le stringhe definite nel file RESX della lingua neutro (**AppResources.resx**) verrà visualizzato quando l'app è in esecuzione in uno il impostazioni locali inglesi.

### <a name="example"></a>Esempio

Dopo l'aggiornamento di progetti specifici della piattaforma come illustrato sopra e ricompilare l'app con file RESX tradotti, traduzioni aggiornate saranno disponibili in ciascuna applicazione. Di seguito è riportata una schermata dal codice di esempio convertita in cinese semplificato:

![](localization-images/simple-example-hans.png "Interfacce utente multipiattaforma convertite in cinese semplificato")

## <a name="localizing-xaml"></a>Localizzazione XAML

Quando la creazione di un'interfaccia utente xamarin. Forms in XAML il markup sarebbe simile al seguente, con le stringhe incorporati direttamente nel codice XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Idealmente, è stato possibile tradurre controlli dell'interfaccia utente direttamente nel codice XAML, possiamo creando un *estensione di markup*. Il codice per un'estensione di markup che espone le risorse RESX in XAML è illustrato di seguito. Questa classe deve essere aggiunto al codice comune di xamarin. Forms (con le pagine XAML):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

L'elenco riportato di seguito illustrano gli elementi importanti nel codice precedente:

* La classe è denominata `TranslateExtension`, ma per convenzione, è possibile fare riferimento a è come **Traduci** nel nostro codice.
* La classe implementa `IMarkupExtension`, richiesto dal xamarin. Forms per il funzionamento.
* `"UsingResxLocalization.Resx.AppResources"` è l'identificatore di risorsa per le risorse RESX. È costituito il nostro spazio dei nomi predefinito, la cartella in cui si trovano i file di risorse e il nome del file RESX predefinito.
* Il `ResourceManager` classe viene creata utilizzando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` per determinare l'assembly corrente per caricare le risorse e memorizzati nella cache in statica `ResMgr` campo. Viene creata come un `Lazy` tipo in modo che la sua creazione viene posticipata finché non si utilizza innanzitutto nel `ProvideValue` metodo.
* `ci` Usa il servizio di dipendenza per ottenere la lingua dell'utente scelto dal sistema operativo nativo.
* `GetString` il metodo che recupera la stringa tradotta effettiva dal file delle risorse. In Windows Phone 8.1 e la piattaforma Windows universale, `ci` sarà null perché il `ILocalize` interfaccia non è implementata in tali piattaforme. Questo è equivalente alla chiamata di `GetString` metodo solo con il primo parametro. Al contrario, il framework di risorse riconoscerà automaticamente le impostazioni locali e recupererà la stringa tradotta dal file RESX appropriato.
* Gestione degli errori è stato incluso per facilitare il debug delle risorse mancanti generando un'eccezione (in `DEBUG` solo in modalità).

Nel frammento XAML seguente viene illustrato come utilizzare l'estensione di markup. Sono disponibili due passaggi per consentire il funzionamento:

1. Dichiarare l'oggetto personalizzato `xmlns:i18n` dello spazio dei nomi nel nodo radice. Il `namespace` e `assembly` devono corrispondere alle impostazioni di progetto - esattamente in questo esempio sono identiche ma potrebbe essere diversi nel progetto.
2. Utilizzare `{Binding}` sintassi per gli attributi che in genere contiene il testo di chiamare il `Translate` estensione di markup. La chiave di risorsa è l'unico parametro obbligatorio.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

La seguente sintassi più dettagliata è inoltre valida per l'estensione di markup:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localizzazione di elementi specifici della piattaforma

Anche se è possibile gestire la traduzione dell'interfaccia utente nel codice di xamarin. Forms, esistono alcuni elementi che devono essere eseguite in ogni progetto specifico della piattaforma. In questa sezione illustra come localizzare:

* Nome applicazione
* Immagini

Il progetto di esempio include un'immagine localizzata denominata **flag.png**, cui viene fatto riferimento in c# come indicato di seguito:

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

L'immagine di flag viene anche fatto riferimento nel codice XAML simile al seguente:

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

Tutte le piattaforme risolverà automaticamente i riferimenti all'immagine simili alle versioni localizzate di immagini, come vengono implementate le strutture di progetto, come illustrate di seguito.

### <a name="ios-application-project"></a>Progetto di applicazione iOS

iOS utilizza uno standard di denominazione chiamato progetti di localizzazione o **.lproj** Directory contenga risorse stringa e di immagine. Queste directory possono contenere le versioni localizzate di immagini usate nell'app, nonché il **InfoPlist.strings** file che può essere utilizzato per localizzare il nome dell'applicazione.

#### <a name="images"></a>Immagini

Questa schermata mostra l'app di esempio di iOS con specifiche della lingua **.lproj** directory. La directory spagnola chiama **es.lproj**, contiene le versioni localizzate dell'immagine predefinita, così come **flag.png**:

![](localization-images/ios-resources.png "Directory di progetto di localizzazione iOS")

Ciascuna directory lingua contiene una copia di **flag.png**localizzato per tale lingua. Se non ci sono immagini viene fornito, il sistema operativo utilizzerà l'immagine nella directory di lingua predefinita. Per il supporto della Retina completo, è necessario fornire **@2x** e **@3x** copie di ogni immagine.

#### <a name="app-name"></a>Nome app

Il contenuto del **InfoPlist.strings** è solo un singolo chiave-valore per configurare il nome dell'app:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando viene eseguita l'applicazione, il nome dell'applicazione e l'immagine sono entrambi localizzati:

![](localization-images/ios-imageicon.png "iOS testo App di esempio e la localizzazione di immagine")

### <a name="android-application-project"></a>Progetto di applicazione Android

Android segue uno schema diverso per l'archiviazione di immagini localizzate usando diversi **drawable** e **stringhe** directory con un suffisso di codice della lingua. Quando un codice di quattro lettere delle impostazioni locali è necessario (ad esempio zh-TW o pt-BR), si noti che richiede un'ulteriore Android **r** seguenti dash/precedente di codice le impostazioni locali (ad es. zh-rTW o pt rBR).

#### <a name="images"></a>Immagini

Questa schermata è riportato il Android esempio con un alcune versioni localizzate drawables e stringhe:

![](localization-images/android-resources.png "Android localizzato Drawables e le directory di stringa")

Si noti che Android non utilizza zh-Hans e zh-Hant codici per semplificato e cinese tradizionale; In alternativa, supporta solo un codice paese specifico zh-CN e zh-TW.

Per supportare le immagini di diverse risoluzioni per gli schermi ad alta densità, creare cartelle aggiuntive con `-*dpi` suffissi, ad esempio **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi**e così via. Vedere [risorse Android alternativa fornendo](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) per ulteriori informazioni.

#### <a name="app-name"></a>Nome app

Il contenuto del **Strings** è solo un singolo chiave-valore per configurare il nome dell'app:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Aggiornamento di **Mainactivity** nel progetto dell'app Android in modo che il `Label` fa riferimento a stringhe XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

L'app localizza ora il nome dell'applicazione e l'immagine. Di seguito è riportata una schermata del risultato (in spagnolo):

![](localization-images/android-imageicon.png "Esempio di App Android e la localizzazione di immagine")

### <a name="windows-phone-80-application-project"></a>Progetto di applicazione Windows Phone 8.0

Incorporato in modo semplice di selezione di un'immagine della localizzata specifica né per localizzare il nome dell'applicazione non dispone di Windows Phone.

#### <a name="images"></a>Immagini

Per aggirare questa limitazione l'esempio fornisce un suggerimento per come è possibile implementare localizzate immagine durante il caricamento tramite un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per il `Image` controllo.

Il codice di renderer personalizzato è illustrato di seguito, se l'origine è un `FileImageSource` quindi estrae il nome del file e crea un percorso di un'immagine localizzata tramite il `CurrentUICulture`. Alcuni linguaggi richiedono una gestione speciale in modo che i fallback funzionano come previsto. Nell'esempio il valore predefinito consiste nell'utilizzare solo il codice di lingua di due lettere, ad eccezione in alcuni casi speciali:

```csharp
using System.IO;
using Xamarin.Forms;
using Xamarin.Forms.Platform.WinPhone;

[assembly: ExportRenderer(typeof(Image), typeof(UsingResxLocalization.WinPhone.LocalizedImageRenderer))]
namespace UsingResxLocalization.WinPhone
{
    public class LocalizedImageRenderer : ImageRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Image> e)
        {
            base.OnElementChanged(e);

            if (e.NewElement != null)
            {
                var s = e.NewElement.Source as FileImageSource;
                if (s != null)
                {
                    var fileName = s.File;
                    string ci = System.Threading.Thread.CurrentThread.CurrentUICulture.ToString();
                    // you might need some custom logic here to support particular cultures and fallbacks
                    if (ci == "pt-BR") {
                        // use the complete string 'as is'
                    } else if (ci == "zh-CN") {
                         // we could have named the image directories differently,
                         // but this keeps them consisent with RESX file naming
                        ci = "zh-Hans";
                    } else if (ci == "zh-TW" || ci == "zh-HK") {
                        ci = "zh-Hant";
                    } else {
                        // for all others, just use the two-character language code
                        ci = System.Threading.Thread.CurrentThread.CurrentUICulture.TwoLetterISOLanguageName;
                    }
                    e.NewElement.Source = Path.Combine("Assets/" + ci + "/" + fileName);
                }
            }
        }
    }
}
```

Questo codice funziona con le immagini localizzate nella struttura di directory illustrato di seguito. Si consiglia di modificare il codice per soddisfare i requisiti di localizzazione specifica (ad esempio la gestione delle impostazioni locali più specifiche e fallback quando non sono disponibili immagini):

![](localization-images/winphone-resources.png "Struttura di Directory immagini localizzata in Windows Phone")

Windows Phone ora localizza l'immagine. Di seguito è riportata una schermata del risultato (in spagnolo e cinese semplificato):

![](localization-images/winphone-image-sml.png "Testo App di esempio WinPhone e localizzazione di immagine")

#### <a name="app-name"></a>Nome app

Fare riferimento alla documentazione di Microsoft per [localizzazione il titolo dell'applicazione Windows Phone 8.0](http://msdn.microsoft.com/library/windows/apps/ff967550(v=vs.105).aspx).

### <a name="windows-phone-81-and-universal-windows-platform-application-projects"></a>Windows Phone 8.1 e Universal Windows Platform progetti di applicazione

Windows Phone 8.1 e la piattaforma Windows universale entrambi disporre di un'infrastruttura di risorsa che semplifica la localizzazione di immagini e il nome dell'applicazione.

#### <a name="images"></a>Immagini

Le immagini possono essere localizzate inserendoli in una cartella specifica della risorsa, come illustrato nella schermata seguente:

![](localization-images/uwp-image-folder-structure.png "Windows Phone 8.1 e UWP immagine localizzazione cartella struttura")

In fase di esecuzione, l'infrastruttura di risorse Windows selezionerà l'immagine appropriata in base alle impostazioni locali dell'utente.

#### <a name="app-name"></a>Nome app

Fare riferimento alla documentazione di Microsoft per [app di Windows 8.1 Store: localizzare le informazioni che descrivono l'app agli utenti](https://msdn.microsoft.com/library/windows/apps/hh454044.aspx) e [durante il caricamento delle stringhe dal manifesto dell'applicazione](https://msdn.microsoft.com/library/windows/apps/xaml/hh965323.aspx#loading_strings_from_the_app_manifest.).

## <a name="summary"></a>Riepilogo

Xamarin. Forms applicazioni possono essere localizzate mediante i file RESX e le classi di globalizzazione .NET. Oltre a una piccola quantità di codice specifico della piattaforma per rilevare la lingua preferita dell'utente, la maggior parte del lavoro localizzazione è centralizzata nel codice comune.

Le immagini vengono in genere gestite in modo specifico della piattaforma per sfruttare il supporto multi-risoluzione fornito in iOS e Android. Windows Phone richiede codice personalizzato per la localizzazione di immagini in modo facile integrazione piattaforma incrociata; Per aggiungere questa funzionalità è stato fornito codice di esempio.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di localizzazione RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [App di esempio TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di iOS](~/ios/app-fundamentals/localization/index.md)
- [Localizzazione di Android](~/android/app-fundamentals/localization.md)
- [Utilizzo della classe CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Individuazione e l'utilizzo di risorse per una lingua specifica (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
