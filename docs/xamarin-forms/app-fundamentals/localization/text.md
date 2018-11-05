---
title: Stringa e la localizzazione di immagine
description: App xamarin. Forms possono essere localizzate mediante i file di risorse di .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108457"
---
# <a name="localization"></a>Localizzazione

_App xamarin. Forms possono essere localizzate mediante i file di risorse di .NET._

## <a name="overview"></a>Panoramica

Il meccanismo incorporato per la localizzazione .NET applications utilizzato [i file RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e le classi nel `System.Resources` e `System.Globalization` gli spazi dei nomi. I file RESX che contiene le stringhe tradotte vengono incorporati nell'assembly xamarin. Forms, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato per le traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

### <a name="sample-code"></a>Codice di esempio

Sono disponibili due esempi relativi a questo documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) è dimostrare i concetti spiegati molto semplice. I frammenti di codice illustrati di seguito sono tutti da questo esempio.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) è un'app funzionante di base che Usa queste tecniche di localizzazione.

#### <a name="shared-projects-are-not-recommended"></a>Non sono consigliati progetti condivisi

L'esempio TodoLocalized include un' [demo di progetto condiviso](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) tuttavia a causa delle limitazioni del sistema di compilazione i file di risorse non si ottengono un **. designer.cs** file generati che non sarà più possibile accedere a stringhe tradotte fortemente tipizzate nel codice.

Il resto di questo documento è correlato ai progetti che usano il modello di libreria xamarin. Forms .NET Standard.

## <a name="globalizing-xamarinforms-code"></a>Globalizzazione di codice xamarin. Forms

**Globalizzazione** un'applicazione è il processo di renderlo "world ready". Ciò significa che la scrittura di codice che è in grado di visualizzare diverse lingue.

Una delle parti principali di globalizzazione di un'applicazione sta creando l'interfaccia utente in modo che sia presente alcun *hardcoded* testo. Al contrario, qualsiasi elemento visualizzato all'utente deve essere recuperata da un set di stringhe che sono stati tradotti nella lingua scelta.

In questo documento verrà esaminato come utilizzare i file RESX per archiviare le stringhe e recuperarli per la visualizzazione in base alle preferenze dell'utente.

Gli esempi di come destinazione le lingue inglese, francese, spagnolo, tedesco, cinese, giapponese, russo e portoghese (Brasile). Le applicazioni possono essere tradotti nelle lingue minor o il numero in base alle esigenze.

> [!NOTE]
> Nella piattaforma Windows universale, si Indicizzano file devono essere utilizzati per la localizzazione delle notifiche push, anziché i file RESX. Per altre informazioni, vedere [UWP localizzazione](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Aggiunta di risorse

Il primo passaggio nella globalizzazione di un'applicazione xamarin. Forms .NET Standard library include i file di risorse RESX che verranno usati per archiviare tutto il testo usato nell'app. È necessario aggiungere un file RESX che contiene il testo predefinito e quindi aggiungere i file RESX aggiuntivi per ogni lingua che si desidera supportare.

#### <a name="base-language-resource"></a>Risorse di lingua di base

Il file di base delle risorse (RESX) conterrà le stringhe della lingua predefinita (gli esempi presumono che l'inglese è la lingua predefinita). Aggiungere il file al progetto di codice comune xamarin. Forms facendo clic sul progetto e scegliendo **Aggiungi > Nuovo File...** .

Scegliere un nome significativo, ad esempio **AppResources** , quindi premere **OK**.

[![Aggiungere File di risorse](text-images/resx-new-file-sml.png "finestra di dialogo Nuovo File")](text-images/resx-new-file.png#lightbox "nuova finestra di dialogo File")

Verranno aggiunti due file al progetto:

* **AppResources.resx** file in cui sono archiviate le stringhe traducibili in formato XML.
* **AppResources.designer.cs** file che dichiara una classe parziale per contenere riferimenti a tutti gli elementi creati nel file RESX XML.

Struttura della soluzione visualizzerà i file correlati. File RESX *dovrebbero* modificarla per aggiungere nuove stringhe traducibili; il **. designer.cs** file dovrebbe *non* essere modificato.

![](text-images/appresources-tree.png "File AppResources.resx")

##### <a name="string-visibility"></a>Visibilità di stringa

Per impostazione predefinita quando vengono generati i riferimenti alle stringhe fortemente tipizzate, saranno `internal` all'assembly. Questo accade perché lo strumento di compilazione predefinito per i file RESX genera il **. designer.cs** file con `internal` proprietà.

Selezionare il **AppResources.resx** file e visualizzare i **proprietà** riempimento in cui questo strumento di compilazione sia configurare. Lo screenshot seguente mostra le **lo strumento personalizzato: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Finestra delle proprietà per AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Riquadro delle proprietà per AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Per rendere le proprietà della stringa fortemente tipizzate `public`, è necessario modificare manualmente la configurazione **lo strumento personalizzato: PublicResXFileCodeGenerator**, come illustrato nello screenshot seguente:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Finestra delle proprietà per AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Riquadro delle proprietà per AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Riquadro delle proprietà per AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Questa modifica è facoltativa ed è solo necessario se si desidera fare riferimento a stringhe localizzate in assembly diversi (ad esempio, se si inserisce i file RESX in un assembly diverso al codice). L'esempio di questo argomento lascia le stringhe `internal` perché sono definiti nello stesso assembly della libreria .NET Standard di xamarin. Forms in cui vengono usati.

È sufficiente impostare lo strumento personalizzato nel file RESX base, come illustrato in precedenza; non è necessario impostare *qualsiasi* dello strumento di compilazione nei file RESX specifici del linguaggio descritto nelle sezioni seguenti.

##### <a name="editing-the-resx-file"></a>Modifica del file RESX

Sfortunatamente non è alcun editor di RESX incorporati in Visual Studio per Mac. Aggiunta di nuove stringhe traducibili richiede l'aggiunta di un nuovo codice XML `data` (elemento) per ogni stringa. Ogni `data` elemento può contenere quanto segue:

* `name` attributo (obbligatoria) è la chiave per questa stringa traducibile. Deve essere un valido C# nome della proprietà, quindi non vengono consentiti spazi o caratteri speciali.
* `value` elemento (obbligatorio), ovvero la stringa effettiva che viene visualizzata nell'applicazione.
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

Come l'applicazione viene scritta, tutte le parti del testo visualizzato all'utente devono essere aggiunti al file di risorse RESX base in un nuovo `data` elemento. Si consiglia di includere `comment`quanto più possibile garantire una traduzione di alta qualità.

> [!NOTE]
> Visual Studio (inclusa l'edizione Community gratuita) include un editor di RESX base. Se si ha accesso a un computer Windows, che può essere un modo pratico per aggiungere e modificare le stringhe nei file RESX.

#### <a name="language-specific-resources"></a>Risorse specifiche della lingua

In genere, l'effettiva traduzione delle stringhe di testo predefinito non verrà eseguita fino a quando non sono state scritte grandi blocchi dell'applicazione (nel qual caso il file RESX predefinito contiene un numero elevato di stringhe). È comunque consigliabile aggiungere le risorse specifiche del linguaggio nelle prime fasi del ciclo di sviluppo popolando facoltativamente con il testo tradotto per testare il codice di localizzazione.

Viene aggiunto un file RESX aggiuntivo per ogni lingua che si desidera supportare.
File di risorse specifiche della lingua devono seguire una convenzione di denominazione specifica: usare lo stesso nome file, come le risorse di base del file (ad es. **AppResources**) seguito da un punto (.) e quindi il codice della lingua. Semplici esempi includono:

* **AppResources.fr.resx** -traduzione in francese.
* **AppResources.es.resx** -traduzioni lingua spagnola.
* **AppResources.de.resx** -le traduzioni in lingua tedesca.
* **AppResources.ja.resx** -le traduzioni per la lingua giapponese.
* **AppResources.zh Hans.resx** - cinese (semplificato) linguaggio traduzioni.
* **AppResources.zh Hant.resx** - cinese (tradizionale) le conversioni di linguaggio.
* **AppResources.pt.resx** -traduzioni delle lingue portoghese.
* **AppResources.pt BR.resx** -traduzioni in lingua portoghese (Brasile).

Il modello generale consiste nell'usare i codici di lingua di due lettere, ma sono disponibili alcuni esempi (ad esempio il cinese) in cui viene usato un formato diverso e altri esempi (ad esempio, portoghese (Brasile)) in cui è necessario un identificatore delle impostazioni locali di quattro caratteri.

Questi file di risorse specifiche della lingua *non li* richiedono una **. designer.cs** parziale classe in modo che possono essere aggiunti come normali file XML, con la **azione di compilazione: EmbeddedResource**impostato. Questa schermata mostra una soluzione che contiene i file di risorse specifiche della lingua:

![](text-images/appresources-langs.png "File di risorse specifiche della lingua")

Quando viene sviluppata un'applicazione e il file RESX base ha aggiunto testo, è necessario lo invia al traduttori che converte ogni `data` elemento e restituire un file di risorse specifico del linguaggio (usando la convenzione di denominazione mostrata) da includere nell'app. Di seguito sono riportati alcuni esempi 'traduzione automatica':

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

Solo le `value` elemento deve essere aggiornato dal convertitore - il `comment` non può essere convertito. Tenere presente: quando si modificano i file XML di escape per caratteri riservati, ad esempio `<`, `>`, `&` con `&lt;`, `&gt;`, e `&amp;` se appaiono nella `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Utilizzo delle risorse nel codice

Le stringhe nei file di risorse RESX saranno disponibili per l'uso nel codice dell'interfaccia utente usando il `AppResources` classe. Il `name` assegnato a ogni stringa di RESX file diventa una proprietà in quella classe a cui può essere fatto riferimento nel codice xamarin. Forms come illustrato di seguito:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

L'interfaccia utente in iOS, Android e i renderer di Universal Windows Platform (UWP) come si può immaginare, tranne che a questo punto è possibile convertire l'app in più lingue in quanto il testo viene caricato da una risorsa invece che hardcoded. Di seguito è riportata una schermata che mostra l'interfaccia utente in ciascuna piattaforma prima della conversione:

![](text-images/simple-example-english.png "Interfacce utente multipiattaforma prima della traduzione")

### <a name="troubleshooting"></a>Risoluzione dei problemi

#### <a name="testing-a-specific-language"></a>Un linguaggio specifico di test

Può essere difficile passare il simulatore o in un dispositivo per diversi linguaggi, in particolare durante lo sviluppo quando si desidera testare rapidamente le impostazioni cultura diverse.

È possibile forzare un linguaggio specifico di essere caricati impostando la `Culture` come illustrato nel frammento di codice seguente:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Questo approccio, impostando la lingua direttamente sul `AppResources` classe: può essere usata anche per implementare un selettore di lingua all'interno di app (anziché di uso delle impostazioni locali del dispositivo).

#### <a name="loading-embedded-resources"></a>Caricamento risorse incorporate

Il seguente frammento di codice è utile quando si tenta di eseguire il debug di problemi con le risorse incorporate (ad esempio i file RESX). Aggiungere questo codice all'App (all'inizio del ciclo di vita app) e verranno visualizzate tutte le risorse incorporate nell'assembly, che mostra l'identificatore di risorsa completo:

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

Verificare i **Output applicazione** per i risultati di debug nel codice sopra riportato, per confermare il corretto le risorse sono elencate (ad esempio, `"UsingResxLocalization.Resx.AppResources"`).

In caso contrario, il `AppResources` classe sarà in grado di caricare le proprie risorse.
Verificare il comando seguente per risolvere i problemi in cui le risorse non sono state trovate:

* Spazio dei nomi predefinito per il progetto corrisponde a spazio dei nomi radice nel **AppResources.Designer.cs** file.
* Se il **AppResources.resx** file si trova in una sottodirectory, il nome della sottodirectory deve far parte dello spazio dei nomi *e* fa parte dell'identificatore di risorsa.
* Il **AppResources.resx** file ha **azione di compilazione: EmbeddedResource**.
* Il **opzioni progetto > codice sorgente > Criteri di denominazione .NET > i nomi delle risorse di tipo usare Visual Studio** sia selezionata. È possibile untick questa se si preferisce, tuttavia gli spazi dei nomi usato quando si fa riferimento a risorse RESX dovranno aggiornato in tutta l'app.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Non funziona in modalità di DEBUG (solo Android)

Se le stringhe tradotte lavora nelle build di versione Android, ma non durante il debug, fare clic sui **progetto Android** e selezionare **Opzioni > compilazione > compilazione Android** e assicurarsi che la **Fast deployment assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere utilizzata se si sta testando l'App localizzate.

### <a name="displaying-the-correct-language"></a>Visualizzare la lingua corretta

Finora abbiamo esaminato come scrivere codice in modo che le traduzioni possono essere fornite, ma non come effettivamente renderli vengono visualizzati. Codice xamarin. Forms possa sfruttare. Le risorse di rete vengano caricate le traduzioni della lingua corretta, ma è necessario eseguire query sul sistema operativo in ogni piattaforma per determinare il linguaggio selezionato dall'utente.

Poiché il codice specifico della piattaforma deve ottenere preferenze della lingua dell'utente, usare una [servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md) esporre queste informazioni nell'app xamarin. Forms e implementarlo per ogni piattaforma.

In primo luogo, definire un'interfaccia per esporre lingua preferita dell'utente, simile al codice riportato di seguito:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

In secondo luogo, usare il [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) in xamarin. Forms `App` classe per chiamare l'interfaccia e impostato sul valore corretto nostra cultura di risorse RESX. Si noti che non occorre impostare manualmente questo valore per la piattaforma Windows universale, poiché il framework di risorse automaticamente riconosce la lingua selezionata in tali piattaforme.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

La risorsa `Culture` deve essere impostato quando si carica l'applicazione in modo che vengano usate le stringhe lingua corretta. È facoltativamente può aggiornare questo valore in base agli eventi specifici della piattaforma che possono essere generati su iOS o Android, se l'utente non aggiorna le preferenze di lingua durante l'esecuzione dell'app.

Le implementazioni per le `ILocalize` interfaccia vengono visualizzati nei [codice specifico della piattaforma](#Platform-specific_Code) sezione riportata di seguito. Queste implementazioni sfruttano questo `PlatformCulture` classe helper:

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

Il codice per rilevare la lingua da visualizzare deve essere specifico della piattaforma poiché iOS, Android e UWP esporre queste informazioni in modi leggermente diversi. Il codice per il `ILocalize` servizio di dipendenza viene fornito sotto per ogni piattaforma, insieme ai requisiti specifici della piattaforma aggiuntivi per assicurarsi che il testo localizzato viene eseguito il rendering correttamente.

Il codice specifico della piattaforma deve anche gestire casi in cui il sistema operativo consente all'utente di configurare un identificatore delle impostazioni locali che non è supportato da. Di NET `CultureInfo` classe. In questi casi codice personalizzato deve essere scritto per rilevare le impostazioni locali non supportate e sostituire il meglio. Impostazioni locali compatibili con NET.

#### <a name="ios-application-project"></a>Progetto di applicazione iOS

gli utenti iOS selezionare la lingua desiderata separatamente dalla data e ora la formattazione delle impostazioni cultura. Per caricare le risorse corrette per localizzare un'app xamarin. Forms è sufficiente eseguire una query di `NSLocale.PreferredLanguages` matrice per il primo elemento.

L'implementazione seguente del `ILocalize` servizio di dipendenza deve essere inserito nel progetto applicazione iOS. Poiché iOS Usa caratteri di sottolineatura invece di trattini, ovvero la rappresentazione di .NET standard, il codice sostituisce il carattere di sottolineatura prima della creazione di un'istanza di `CultureInfo` classe:

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
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
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
> Il `try/catch` blocchi presenti il `GetCurrentCultureInfo` metodo simulare il comportamento di fallback in genere utilizzato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare per corrispondenza più vicina basata semplicemente sul linguaggio (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di xamarin. Forms, alcune impostazioni locali sono validi in iOS, ma non corrispondono a un valore valido `CultureInfo` in .NET e il codice precedente tentativi per gestire questo problema.
>
> Ad esempio, iOS **Impostazioni > generale Language &amp; area** schermata consente di impostare il telefono **lingua** per **inglese** ma il  **Area geografica** al **Spagna**, che si ottiene una stringa di impostazioni locali di `"en-ES"`. Quando il `CultureInfo` creazione si verifica un errore, il codice tenta di utilizzare solo le prime due lettere per selezionare la lingua di visualizzazione.
>
> Gli sviluppatori devono modificare il `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` metodi per gestire i casi specifici necessari per le lingue supportate.


Esistono alcuni elementi dell'interfaccia utente definiti dal sistema che vengono automaticamente tradotti da iOS, ad esempio la **Done** pulsante la `Picker` controllo. Per forzare per convertire questi elementi è necessario indicare in quali lingue sono supportate in iOS il **Info. plist** file. È possibile aggiungere questi valori tramite **Info. plist > origine** come illustrato di seguito:

![Le chiavi di localizzazione in Info. plist](text-images/info-plist.png "le chiavi di localizzazione in Info. plist")

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

Dopo aver implementato il servizio di dipendenza e aggiornato **Info. plist**, l'app per iOS saranno in grado di visualizzare il testo localizzato.

> [!NOTE]
> Si noti che considera Apple portoghese in modo leggermente diverso da quello previsto.
> Dal [loro docs](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"usare pt come l'ID della lingua per il portoghese perché è usato in Brasile e pt-PT come ID di lingua per il portoghese perché è usato in Portogallo"_.
> Ciò significa che, quando portoghese viene scelto delle impostazioni locali non standard, la lingua di fallback sarà portoghese (Brasile) in iOS, a meno che non viene scritto codice per modificare questo comportamento (ad esempio il `ToDotnetFallbackLanguage` sopra).

Per altre informazioni sulla localizzazione iOS, vedere [iOS localizzazione](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Progetto di applicazione Android

Android espone le impostazioni locali attualmente selezionata tramite `Java.Util.Locale.Default`e Usa anche un separatore di caratteri di sottolineatura anziché un trattino (che verrà sostituito dal codice seguente). Aggiungere questa implementazione del servizio di dipendenza al progetto di applicazione Android:

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
> Il `try/catch` blocchi presenti il `GetCurrentCultureInfo` metodo simulare il comportamento di fallback in genere utilizzato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare per corrispondenza più vicina basata semplicemente sul linguaggio (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di xamarin. Forms, alcune impostazioni locali sono validi in Android, ma non corrispondono a un valore valido `CultureInfo` in .NET e il codice precedente tentativi per gestire questo problema.
>
> Gli sviluppatori devono modificare il `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` metodi per gestire i casi specifici necessari per le lingue supportate.

Dopo aver aggiunto questo codice al progetto di applicazione Android, sarà in grado di visualizzare automaticamente stringhe tradotte.

> [!NOTE]
>️ **avviso:** se le stringhe tradotte lavora nelle build di versione Android, ma non durante il debug, fare clic sui **progetto Android** e selezionare **Opzioni > compilazione > Android Compilare** e assicurarsi che il **Fast deployment assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere utilizzata se si sta testando l'App localizzate.

Per altre informazioni sulla localizzazione Android, vedere [localizzazione Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Progetti di Universal Windows Platform (UWP) non richiedono il servizio di dipendenza. Al contrario, questa piattaforma imposta automaticamente le impostazioni cultura della risorsa in modo corretto.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Espandere il nodo delle proprietà nel progetto della libreria .NET Standard e fare doppio clic sulla **AssemblyInfo.cs** file. Aggiungere la riga seguente al file per impostare il linguaggio assembly di risorse neutre all'inglese:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Ciò indica a Gestione risorse di impostazioni cultura predefinite dell'app, assicurando pertanto che le stringhe definite nel file RESX della lingua neutro (**AppResources.resx**) verrà visualizzato quando l'app è in esecuzione in uno il impostazioni locali inglesi.

### <a name="example"></a>Esempio

Dopo l'aggiornamento di progetti specifici delle piattaforme come indicato sopra e ricompilare l'app con file RESX tradotti, traduzioni aggiornate saranno disponibili in ogni app. Di seguito è riportato uno screenshot il codice di esempio convertito in cinese semplificato:

![](text-images/simple-example-hans.png "Interfacce utente multipiattaforma convertite in cinese semplificato")

Per altre informazioni sulla localizzazione UWP, vedere [UWP localizzazione](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localizzazione XAML

Quando la creazione di un'interfaccia utente di xamarin. Forms in XAML markup avrebbe un aspetto simile al seguente, con le stringhe incorporati direttamente nel codice XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

In teoria, è stato possibile tradurre controlli dell'interfaccia utente direttamente in XAML, il cui possiamo mediante la creazione di un *estensione di markup*. Seguito è riportato il codice per un'estensione di markup che espone le risorse RESX da XAML. Questa classe deve essere aggiunto al codice comune di xamarin. Forms (insieme di pagine XAML):

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

L'elenco riportato di seguito vengono descritti gli elementi importanti nel codice precedente:

* La classe è denominata `TranslateExtension`, ma per convenzione si può fare riferimento sia come **Translate** nel nostro markup.
* La classe implementa `IMarkupExtension`, necessaria per xamarin. Forms per il funzionamento.
* `"UsingResxLocalization.Resx.AppResources"` è l'identificatore di risorsa per le nostre risorse RESX. Si è costituito da spazio dei nomi predefinito, la cartella in cui si trovano i file di risorse e il nome del file RESX predefinito.
* Il `ResourceManager` classe viene creata utilizzando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` per determinare l'assembly corrente per caricare le risorse e memorizzati nella cache in statico `ResMgr` campo. Viene creata come una `Lazy` digitate in modo che la sua creazione viene posticipata fino a quando non viene usato prima di tutto nel `ProvideValue` (metodo).
* `ci` Usa il servizio di dipendenza per ottenere la lingua dell'utente scelto dal sistema operativo nativo.
* `GetString` è il metodo che recupera la stringa tradotta effettiva dal file delle risorse. Nella piattaforma Windows Universal `ci` sarà null perché il `ILocalize` interfaccia non è implementata in tali piattaforme. Ciò equivale a chiamare il `GetString` metodo con solo il primo parametro. Al contrario, il framework di risorse riconoscerà automaticamente le impostazioni locali e recupererà la stringa tradotta dal file RESX appropriato.
* Gestione degli errori è stata inclusa per facilitare il debug delle risorse mancanti generando un'eccezione (in `DEBUG` solo in modalità).

Il frammento di codice XAML seguente viene illustrato come usare l'estensione di markup. Esistono due passaggi per consentire il funzionamento:

1. Dichiarare l'oggetto personalizzato `xmlns:i18n` dello spazio dei nomi nel nodo radice. Il `namespace` e `assembly` deve corrispondere alle impostazioni di progetto esattamente - in questo esempio sono identiche ma potrebbe essere diversi nel progetto.
2. Uso `{Binding}` sintassi per gli attributi che normalmente conterrebbe il testo per chiamare il `Translate` estensione di markup. La chiave di risorsa è l'unico parametro obbligatorio.

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

Anche se è possibile gestire la traduzione dell'interfaccia utente nel codice xamarin. Forms, esistono alcuni elementi che devono essere eseguite in ogni progetto specifico della piattaforma. In questa sezione illustra le procedure per la localizzazione:

* Nome applicazione
* Immagini

Il progetto di esempio include un'immagine localizzata chiamata **flag.png**, cui viene fatto riferimento C# come indicato di seguito:

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

L'immagine di flag fa anche riferimento il XAML simile al seguente:

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

Tutte le piattaforme risolverà automaticamente riferimenti a immagini simili ai seguenti alle versioni localizzate delle immagini, purché le strutture di progetto illustrate di seguito vengono implementate.

### <a name="ios-application-project"></a>Progetto di applicazione iOS

iOS usa uno standard di denominazione denominato progetti di localizzazione o **.lproj** Directory contenga risorse stringa e immagini. Queste directory possono contenere le versioni localizzate delle immagini usate nell'app e anche il **InfoPlist.strings** file che può essere usato per localizzare il nome dell'app. Per altre informazioni sulla localizzazione iOS, vedere [iOS localizzazione](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Immagini

Questa schermata mostra l'app di esempio iOS con specifiche della lingua **.lproj** le directory. La directory spagnolo denominato **es.lproj**, contiene le versioni localizzate dell'immagine predefinita, nonché **flag.png**:

![](text-images/ios-resources.png "Directory di progetto di localizzazione di iOS")

Ogni directory lingua contiene una copia dello **flag.png**, localizzato per tale lingua. Se non viene fornita alcuna immagine, il sistema operativo per impostazione predefinita l'immagine nella directory lingua predefinita. Per il supporto completo della Retina, è necessario fornire **@2x** e **@3x** copie di ogni immagine.

#### <a name="app-name"></a>Nome app

Il contenuto del **InfoPlist.strings** è solo un singolo valore di chiave per configurare il nome dell'app:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando viene eseguita l'applicazione, il nome dell'app e l'immagine sono entrambi localizzati:

![](text-images/ios-imageicon.png "iOS localizzazione immagine e testo delle App di esempio")

### <a name="android-application-project"></a>Progetto di applicazione Android

Android segue uno schema diverso per archiviare le immagini localizzate tramite numerosi **drawable** e **stringhe** le directory con un suffisso di codice della lingua. Quando un codice di quattro lettere delle impostazioni locali è necessario (ad esempio, zh-TW o pt-BR), si noti che Android richiede un'ulteriore **r** dash/precedente in seguito le impostazioni locali del codice (ad es. zh-rTW o pt rBR). Per altre informazioni sulla localizzazione Android, vedere [localizzazione Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Immagini

In questo screenshot appare il Android di esempio con un alcune drawable e le stringhe localizzate:

![](text-images/android-resources.png "Android Drawable e le directory di stringhe localizzate")

Si noti che Android non usa zh-Hans e zh-Hant codici semplificato e cinese tradizionale; In alternativa, supporta solo i codici paese specifico. zh-CN e zh-TW.

Per supportare immagini diverse risoluzioni per gli schermi ad alta densità, creare cartelle di lingue aggiuntive con `-*dpi` i suffissi, ad esempio **drawable-es-mdpi**, **drawable-es-xdpi**, **drawable-es-xxdpi**e così via. Visualizzare [fornendo risorse Android alternativa](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) per altre informazioni.

#### <a name="app-name"></a>Nome app

Il contenuto del **Strings. XML** è solo un singolo valore di chiave per configurare il nome dell'app:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Aggiorna il **MainActivity.cs** nel progetto di app per Android in modo che il `Label` fa riferimento a stringhe XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

L'app ora localizza il nome dell'app e l'immagine. Di seguito è riportata una schermata del risultato (in spagnolo):

![](text-images/android-imageicon.png "Localizzazione di immagine e testo di App di esempio di Android")

### <a name="universal-windows-platform-application-projects"></a>Progetti di applicazione di Universal Windows Platform

La piattaforma Windows universale possiedono un'infrastruttura di risorsa che semplifica la localizzazione delle immagini e il nome dell'app. Per altre informazioni sulla localizzazione UWP, vedere [UWP localizzazione](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Immagini

Le immagini possono essere localizzate inserendoli in una cartella di risorse specifiche, come illustrato nello screenshot seguente:

![](text-images/uwp-image-folder-structure.png "Struttura di cartelle di localizzazione immagine piattaforma UWP")

In fase di esecuzione l'infrastruttura di risorse di Windows selezionerà l'immagine appropriata in base alle impostazioni locali.

## <a name="summary"></a>Riepilogo

Le applicazioni xamarin. Forms possono essere localizzate mediante i file RESX e le classi di globalizzazione .NET. Oltre a una piccola quantità di codice specifico della piattaforma per rilevare la lingua preferenziale per l'utente, la maggior parte dello sforzo di localizzazione è centralizzata nel codice comune.

Le immagini vengono in genere gestite in modo specifico della piattaforma per poter sfruttare il supporto di più risoluzioni fornito in iOS e Android.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di localizzazione RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [App di esempio TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localizzazione di cross-Platform](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di iOS](~/ios/app-fundamentals/localization/index.md)
- [Localizzazione di Android](~/android/app-fundamentals/localization.md)
- [Localizzazione di UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Utilizzo della classe CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Individuazione e l'utilizzo delle risorse per impostazioni cultura specifiche (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
