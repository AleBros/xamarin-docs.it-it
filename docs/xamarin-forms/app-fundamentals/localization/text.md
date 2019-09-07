---
title: Localizzazione di stringhe e immagini
description: Le app Xamarin.Forms possono essere localizzate mediante i file di risorse di .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 8e347d5d0c80f7aaac121ca1e68eec618ff2ff88
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760989"
---
# <a name="localization"></a>Localizzazione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

_Le app Xamarin.Forms possono essere localizzate mediante i file di risorse di .NET._

## <a name="overview"></a>Panoramica

Il meccanismo incorporato per la localizzazione delle applicazioni .NET usa i [file con estensione resx](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e le classi negli spazi dei nomi `System.Resources` e `System.Globalization`. I file con estensione resx che contengono le stringhe tradotte sono incorporati nell'assembly Xamarin.Forms, insieme a una classe generata dal compilatore che fornisce l'accesso fortemente tipizzato alle traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

### <a name="sample-code"></a>Codice di esempio

Sono disponibili due esempi associati a questo documento:

- [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) costituisce una chiara dimostrazione dei concetti spiegati. I frammenti di codice illustrati di seguito provengono tutti da questo esempio.
- [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) è un'app di base che usa queste tecniche di localizzazione.

#### <a name="shared-projects-are-not-recommended"></a>I progetti condivisi non sono consigliati

L'esempio TodoLocalized include una [demo di progetto condiviso](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) ma, a causa delle limitazioni del sistema di compilazione, i file di risorse non generano un file **.designer.cs** e ciò non consente di accedere a stringhe tradotte fortemente tipizzate nel codice.

Il resto del documento riguarda i progetti che usano il modello di libreria Xamarin.Forms .NET Standard.

## <a name="globalizing-xamarinforms-code"></a>Globalizzazione del codice Xamarin.Forms

La **globalizzazione** di un'applicazione è il processo che consiste nel renderla più "internazionale". Ciò significa che il codice deve essere scritto in modo da essere visualizzato in più lingue.

Uno degli aspetti principali della globalizzazione di un'applicazione consiste nel creare l'interfaccia utente in modo che non sia presente testo *hardcoded*. Al contrario, tutto ciò che l'utente può visualizzare deve essere recuperato da un set di stringhe che sono state tradotte nella lingua scelta.

In questo documento verrà illustrato come usare i file con estensione resx per archiviare le stringhe e recuperarle per la visualizzazione in base alle preferenze dell'utente.

Negli esempi sono incluse come lingue di destinazione inglese, francese, spagnolo, tedesco, cinese, giapponese, russo e portoghese brasiliano. Le applicazioni possono essere tradotte in un numero qualsiasi di lingue, a seconda delle esigenze.

> [!NOTE]
> Nella piattaforma UWP per la localizzazione delle notifiche push devono essere usati i file con estensione resw, invece che resx. Per altre informazioni, vedere [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Aggiunta di risorse

Il primo passaggio per la globalizzazione di un'applicazione della libreria Xamarin.Forms .NET Standard consiste nell'aggiungere i file di risorse RESX che verranno usati per archiviare tutto il testo usato nell'app. È necessario aggiungere un file RESX che contiene il testo predefinito e quindi aggiungere i file RESX restanti per ogni lingua da supportare.

#### <a name="base-language-resource"></a>Risorse della lingua di base

Il file di risorse di base (con estensione resx) contiene le stringhe della lingua predefinita (negli esempi è la lingua inglese). Aggiungere il file al progetto di codice comune Xamarin.Forms facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Aggiungi > Nuovo File**.

Scegliere un nome significativo, ad esempio **AppResources**, e premere **OK**.

[![Aggiungere un file di risorse](text-images/resx-new-file-sml.png "Finestra di dialogo Nuovo File")](text-images/resx-new-file.png#lightbox "Finestra di dialogo Nuovo File")

Vengono aggiunti due file al progetto:

- Il file **AppResources.resx** in cui sono archiviate le stringhe traducibili in formato XML.
- Il file **AppResources.designer.cs** che dichiara una classe parziale per contenere riferimenti a tutti gli elementi creati nel file RESX in formato XML.

Nell'albero della soluzione vengono visualizzati i file correlati. Il file RESX *deve* essere modificato per aggiungere nuove stringhe traducibili; il file **. designer.cs** *non* deve essere modificato.

![](text-images/appresources-tree.png "File AppResources.resx")

##### <a name="string-visibility"></a>Visibilità delle stringhe

Per impostazione predefinita, quando vengono generati i riferimenti alle stringhe fortemente tipizzate, saranno `internal` all'assembly. Questo accade perché lo strumento di compilazione predefinito per i file RESX genera il file **.designer.cs** con le proprietà `internal`.

Selezionare il file **AppResources.resx** e visualizzare il riquadro **Proprietà** per visualizzare dove viene configurato lo strumento di compilazione. Lo screenshot seguente mostra **Strumento personalizzato: ResXFileCodeGenerator**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Finestra Proprietà per AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Riquadro Proprietà per AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Per rendere le proprietà delle stringhe fortemente tipizzate `public`, è necessario modificare manualmente la configurazione in **Strumento personalizzato: PublicResXFileCodeGenerator**, come illustrato nello screenshot seguente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Finestra Proprietà per AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Riquadro Proprietà per AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

[![](text-images/xs-resx-public-sml.png "Riquadro Proprietà per AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Questa modifica è facoltativa ed è necessaria solo se si vuole fare riferimento a stringhe localizzate in assembly diversi, ad esempio, se si inseriscono i file RESX in un assembly diverso dal codice che si sta scrivendo. Nell'esempio di questo argomento vengono lasciate le stringhe `internal` perché sono definite nello stesso assembly della libreria Xamarin.Forms .NET Standard in cui vengono usate.

È sufficiente impostare lo strumento personalizzato nel file RESX di base, come illustrato in precedenza; non è necessario impostare uno degli *strumenti di compilazione* nei file RESX specifici della lingua descritti nelle sezioni seguenti.

##### <a name="editing-the-resx-file"></a>Modifica del file RESX

Sfortunatamente non esiste alcun editor di RESX incorporato in Visual Studio per Mac. Per aggiungere nuove stringhe traducibili è necessario aggiungere un nuovo elemento XML `data` per ogni stringa. Ogni elemento `data` può contenere quanto segue:

- Un attributo `name` (obbligatorio) che è la chiave per questa stringa traducibile. Deve essere un nome della proprietà C# valido, quindi non sono consentiti spazi o caratteri speciali.
- Un elemento `value` (obbligatorio), ovvero la stringa effettiva che viene visualizzata nell'applicazione.
- Un elemento `comment` (facoltativo) che può contenere istruzioni per il traduttore che spiegano come viene usata la stringa.
- Un attributo `xml:space` (facoltativo) per controllare come viene mantenuta la spaziatura nella stringa.

Di seguito vengono illustrati alcuni elementi `data` di esempio:

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

A mano a mano che l'applicazione viene scritta, tutte le parti di testo visualizzabili per l'utente devono essere aggiunte al file di risorse RESX di base in un nuovo elemento `data`. È consigliabile includere quanti più elementi `comment` possibile per garantire una traduzione di alta qualità.

> [!NOTE]
> Visual Studio (inclusa l'edizione Community gratuita) include un editor di RESX di base. Se si ha accesso a un computer Windows, questo potrebbe essere un modo pratico per aggiungere e modificare stringhe nei file RESX.

#### <a name="language-specific-resources"></a>Risorse specifiche della lingua

In genere, l'effettiva traduzione delle stringhe di testo predefinite non viene eseguita fino a quando non sono stati scritti grandi blocchi dell'applicazione, nel qual caso il file RESX predefinito contiene un numero elevato di stringhe. È comunque consigliabile aggiungere le risorse specifiche della lingua nelle prime fasi del ciclo di sviluppo, includendo testo tradotto automaticamente per semplificare il test del codice di localizzazione.

Viene aggiunto un file RESX per ogni lingua da supportare.
I file specifici della lingua devono seguire una convenzione di denominazione specifica: usare lo stesso nome file come file di risorse di base, ad esempio **AppResources** seguito da un punto (.) e quindi il codice della lingua. Di seguito sono riportati alcuni esempi:

- **AppResources.fr.resx** per le traduzioni in francese.
- **AppResources.es.resx** per le traduzioni in spagnolo.
- **AppResources.de.resx** per le traduzioni in tedesco.
- **AppResources.ja.resx** per le traduzioni in giapponese.
- **AppResources.zh-Hans.resx** per le traduzioni in cinese (semplificato).
- **AppResources.zh-Hant.resx** per le traduzioni in cinese (tradizionale).
- **AppResources.pt.resx** per le traduzioni in portoghese.
- **AppResources.pt-BR.resx** per le traduzioni in portoghese brasiliano.

Il modello generale consiste nell'usare i codici della lingua a due lettere, ma vi sono alcuni esempi, come il cinese, in cui viene usato un formato diverso e altri, come il portoghese brasiliano, in cui è richiesto un identificatore a quattro caratteri.

Questi file di risorse specifici della lingua *non* richiedono una classe parziale **.designer.cs** per essere aggiunti come normali file XML, se è stata impostata **Azione compilazione: EmbeddedResource**. Lo screenshot seguente illustra una soluzione che include file di risorse specifici della lingua:

![](text-images/appresources-langs.png "File di risorse specifici della lingua")

Quando si sviluppa un'applicazione e si aggiunge testo al file RESX di base, è necessario inviare il file ai traduttori che tradurranno ogni elemento `data` e restituiranno un file di risorse specifico della lingua, con la convenzione di denominazione illustrata in precedenza, da includere nell'app. Di seguito sono riportati alcuni esempi di traduzione automatica:

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

**AppResources.pt-BR.resx (portoghese brasiliano)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Il traduttore deve aggiornare solo l'elemento `value`; l'elemento `comment` non deve essere tradotto. Quando si modificano i file XML, ricordare di sostituire i caratteri riservati come ad esempio `<`, `>`, `&` con `&lt;`, `&gt;`, e `&amp;` se sono inclusi negli elementi `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso delle risorse nel codice

Perché le stringhe dei file di risorse RESX siano disponibili per l'uso nel codice dell'interfaccia utente è necessario usare la classe `AppResources`. L'elemento `name` assegnato a ogni stringa del file RESX diventa una proprietà nella classe a cui può essere fatto riferimento nel codice Xamarin.Forms come illustrato di seguito:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

L'interfaccia utente in iOS, Android e nella piattaforma UWP viene visualizzata come previsto, con la differenza che ora è possibile tradurre l'app in diverse lingue perché il testo non è hardcoded ma viene caricato da una risorsa. Di seguito viene riportato uno screenshot che illustra l'interfaccia utente in ogni piattaforma prima della traduzione:

![](text-images/simple-example-english.png "Interfacce utente multipiattaforma prima della traduzione")

### <a name="troubleshooting"></a>Risoluzione dei problemi

#### <a name="testing-a-specific-language"></a>Test in una lingua specifica

Convertire un simulatore o un dispositivo in lingue diverse può risultare complicato, soprattutto durante lo sviluppo, quando si vogliono testare rapidamente diverse impostazioni cultura.

È possibile forzare il caricamento di una lingua specifica impostando l'elemento `Culture` come illustrato nel frammento di codice seguente:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Questo approccio, che consiste nel configurare le impostazioni cultura direttamente nella classe `AppResources`, può essere usato anche per implementare un selettore di lingua nell'app, invece di usare le impostazioni locali del dispositivo.

#### <a name="loading-embedded-resources"></a>Caricamento di risorse incorporate

Il frammento di codice seguente è utile quando si tenta di eseguire il debug di problemi con risorse incorporate, come ad esempio i file RESX. Aggiungere questo codice all'app, all'inizio del ciclo di vita dell'app. Verranno elencate tutte le risorse incorporate nell'assembly, con l'identificatore della risorsa completo:

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

Nel file **AppResources.Designer.cs** (all'incirca alla *riga 33*), viene indicato il *nome completo della gestione risorse* specificato (ad esempio, `"UsingResxLocalization.Resx.AppResources"`) simile al codice riportato di seguito:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

In **Output applicazione** verificare i risultati del codice di debug sopra riportato, per confermare che le risorse elencate siano corrette, (ad esempio, `"UsingResxLocalization.Resx.AppResources"`).

In caso contrario, la classe `AppResources` non potrà caricare le risorse.
Per risolvere eventuali problemi nel caso in cui le risorse non vengano trovate, verificare quanto segue:

- Lo spazio dei nomi predefinito per il progetto deve corrispondere allo spazio dei nomi radice nel file **AppResources.Designer.cs**.
- Se il file **AppResources.resx** si trova in una sottodirectory, il nome della sottodirectory deve far parte dello spazio dei nomi *e* dell'identificatore di risorsa.
- Nel file **AppResources.resx** deve essere impostata **Azione compilazione: EmbeddedResource**.
- L'opzione **Opzioni progetto > Codice sorgente > Criteri di denominazione .NET > Use Visual Studio-style resources names** (Usa i nomi di risorsa di tipo Visual Studio) deve essere selezionata. È possibile deselezionare questa opzione, tuttavia gli spazi dei nomi usati quando si fa riferimento alle risorse RESX dovranno essere aggiornati in tutta l'app.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Non funziona in modalità di DEBUG (solo Android)

Se le stringhe tradotte vengono usate nelle compilazioni VERSIONE di Android, ma non durante il debug, fare clic con il pulsante destro del mouse sul **progetto Android** e selezionare **Opzioni > Compilazione > Compilazione Android** e assicurarsi che l'opzione **Distribuzione rapida di assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere usata se si stanno testando app localizzate.

### <a name="displaying-the-correct-language"></a>Visualizzazione della lingua corretta

Finora abbiamo esaminato come scrivere codice in modo da includere le traduzioni, ma non come fare in modo che vengano effettivamente visualizzate. Il codice Xamarin.Forms può sfruttare le risorse di .NET per caricare le traduzioni della lingua corretta, ma è necessario eseguire query sul sistema operativo in ogni piattaforma per determinare la lingua selezionata dall'utente.

Poiché per ottenere le preferenze dell'utente è necessario codice specifico della piattaforma, usare un [servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md) per esporre queste informazioni nell'app Xamarin.Forms e implementarle per ogni piattaforma.

Per prima cosa, definire un'interfaccia per esporre le impostazioni cultura dell'utente, come illustrato in seguito:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

In secondo luogo, usare [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) nella classe `App` di Xamarin.Forms per chiamare l'interfaccia e configurare le impostazioni cultura delle risorse RESX con il valore corretto. Non è necessario impostare questo valore manualmente per la piattaforma UWP in quanto il framework delle risorse riconosce automaticamente la lingua selezionata nella piattaforma.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

È necessario impostare la risorsa `Culture` quando l'applicazione viene caricata per la prima volta in modo che vengano usate le stringhe della lingua corretta. Facoltativamente è possibile aggiornare questo valore in base a eventi specifici della piattaforma che possono verificarsi in iOS o Android se l'utente aggiorna le preferenze della lingua mentre l'app è in esecuzione.

Le implementazioni per l'interfaccia `ILocalize` vengono visualizzate nella sezione [Codice specifico della piattaforma](#Platform-specific_Code) più avanti nell'articolo. Queste implementazioni sfruttano questa classe dell'helper `PlatformCulture`:

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

Il codice che rileva la lingua da visualizzare deve essere specifico della piattaforma perché iOS, Android e UWP espongono questa informazione in modi leggermente diversi. Il codice per il servizio di dipendenza `ILocalize` viene specificato in seguito per ogni piattaforma, insieme con requisiti specifici della piattaforma aggiuntivi per garantire che il testo localizzato venga visualizzato correttamente.

Il codice specifico della piattaforma deve anche gestire casi in cui il sistema operativo consente all'utente di configurare un identificatore delle impostazioni locali non supportato dalla classe `CultureInfo` di .NET. In questi casi è necessario scrivere codice personalizzato per rilevare le impostazioni locali non supportate e sostituirle con le migliori impostazioni locali disponibili compatibili con .NET.

#### <a name="ios-application-project"></a>Progetto di applicazione iOS

Gli utenti iOS selezionano la lingua preferita in un percorso diverso dalle impostazioni cultura di formattazione di data e ora. Per caricare le risorse corrette per localizzare un'app Xamarin.Forms è sufficiente eseguire una query sulla matrice `NSLocale.PreferredLanguages` per il primo elemento.

L'implementazione seguente del servizio di dipendenza `ILocalize` deve essere inserita nel progetto dell'applicazione iOS. Poiché iOS usa caratteri di sottolineatura invece dei trattini, che costituiscono la rappresentazione di .NET standard, il codice sostituisce il carattere di sottolineatura prima della creazione di un'istanza della classe `CultureInfo`:

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
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

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
> I blocchi `try/catch` presenti nel metodo `GetCurrentCultureInfo` simulano il comportamento di fallback in genere usato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare la corrispondenza più vicina basata semplicemente sulla lingua (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di Xamarin.Forms, alcune impostazioni locali sono valide in iOS, ma non corrispondono a un valore `CultureInfo` valido in .NET pertanto il codice precedente tenta di gestire il problema.
>
> Ad esempio, la schermata di iOS **Settings > General Language &amp; Region** (Impostazioni > Generali per lingua e area geografica) consente di impostare il valore di **Language** (Lingua) del telefono su **English** (Inglese) ma di impostare anche  **Region** (Area geografica) su **Spain** (Spagna), generando così una stringa di impostazioni locali `"en-ES"`. Quando la creazione di `CultureInfo` non riesce, il codice torna a usare solo le prime due lettere per selezionare la lingua di visualizzazione.
>
> Gli sviluppatori devono modificare i metodi `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` per gestire i casi specifici per le lingue supportate.

Esistono alcuni elementi dell'interfaccia utente definiti dal sistema che vengono automaticamente tradotti da iOS, ad esempio il pulsante **Done** (Fatto) nel controllo `Picker`. Per forzare iOS perché traduca questi elementi è necessario indicare quali lingue sono supportate nel file **Info.plist**. È possibile aggiungere questi valori tramite **Info.plist > Source** (Origine) come illustrato di seguito:

![Chiavi di localizzazione in Info.plist](text-images/info-plist.png "Chiavi di localizzazione in Info.plist")

In alternativa, aprire il file **Info.plist** in un editor XML e modificare i valori direttamente:

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

Dopo aver implementato il servizio di dipendenza e aggiornato **Info.plist**, l'app iOS potrà visualizzare il testo localizzato.

> [!NOTE]
> Si noti che Apple considera il portoghese in modo leggermente diverso da quello che si potrebbe pensare.
> Dalla [documentazione Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"use pt as the language ID for Portuguese as it is used in Brazil and pt-PT as the language ID for Portuguese as it is used in Portugal"_ (usare pt come ID del portoghese brasiliano e pt-PT come ID del portoghese parlato in Portogallo).
> Ciò significa che quando la lingua portoghese viene scelta come impostazione locale non standard, in iOS la lingua di fallback è il portoghese brasiliano, a meno che sia stato scritto del codice per modificare questo comportamento, come nell'elemento `ToDotnetFallbackLanguage` sopra riportato.

Per altre informazioni sulla localizzazione in iOS, vedere [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Progetto di applicazione Android

Android espone le impostazioni locali attualmente selezionate tramite `Java.Util.Locale.Default` e usa anche un carattere di sottolineatura anziché un trattino come separatore (che verrà sostituito dal codice seguente). Aggiungere l'implementazione del servizio di dipendenza al progetto dell'applicazione Android:

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
> I blocchi `try/catch` presenti nel metodo `GetCurrentCultureInfo` simulano il comportamento di fallback in genere usato con gli identificatori delle impostazioni locali: se la corrispondenza esatta non viene trovata, cercare la corrispondenza più vicina basata semplicemente sulla lingua (primo blocco di caratteri nelle impostazioni locali).
>
> Nel caso di Xamarin.Forms, alcune impostazioni locali sono valide in Android, ma non corrispondono a un valore `CultureInfo` valido in .NET pertanto il codice precedente tenta di gestire il problema.
>
> Gli sviluppatori devono modificare i metodi `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` per gestire i casi specifici per le lingue supportate.

Dopo aver aggiunto questo codice al progetto dell'applicazione Android, sarà possibile visualizzare automaticamente le stringhe tradotte.

> [!WARNING]
> Se le stringhe tradotte vengono usate nelle compilazioni VERSIONE di Android, ma non durante il debug, fare clic con il pulsante destro del mouse sul **progetto Android** e selezionare **Opzioni > Compilazione > Compilazione Android** e assicurarsi che l'opzione **Distribuzione rapida di assembly** non sia selezionata. Questa opzione può causare problemi con il caricamento delle risorse e non deve essere usata se si stanno testando app localizzate.

Per altre informazioni sulla localizzazione in Android, vedere [Localizzazione in Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

I progetti Universal Windows Platform (UWP) non richiedono il servizio di dipendenza. Al contrario, questa piattaforma imposta automaticamente le impostazioni cultura della risorsa in modo corretto.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Espandere il nodo Proprietà nel progetto della libreria .NET Standard e fare doppio clic sul file **AssemblyInfo.cs**. Aggiungere la riga seguente al file per impostare la lingua dell'assembly delle risorse neutre sull'inglese:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Ciò indica alla gestione risorse le impostazioni cultura predefinite dell'app, assicurando che le stringhe definite nel file RESX della lingua neutra (**AppResources.resx**) verranno visualizzate quando l'app è in esecuzione in una delle impostazioni locali in inglese.

### <a name="example"></a>Esempio

Dopo aver aggiornato i progetti specifici della piattaforma come indicato sopra e aver ricompilato l'app con i file RESX tradotti, le traduzioni aggiornate saranno disponibili in ogni app. Di seguito è riportato uno screenshot del codice di esempio tradotto in cinese semplificato:

![](text-images/simple-example-hans.png "Interfacce utente multipiattaforma tradotte in cinese semplificato")

Per altre informazioni sulla localizzazione in UWP, vedere [Localizzazione in UWP](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localizzazione di XAML

Quando si crea un'interfaccia utente di Xamarin.Forms in XAML, il markup ha un aspetto simile al seguente, con le stringhe incorporate direttamente nel codice XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

In teoria, sarebbe possibile tradurre i controlli dell'interfaccia utente direttamente in XAML creando un'*estensione di markup*. Di seguito è riportato il codice per un'estensione di markup che espone le risorse RESX in XAML. Questa classe deve essere aggiunta al codice comune di Xamarin.Forms, insieme con le pagine XAML:

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

L'elenco riportato di seguito descrive gli elementi importanti del codice precedente:

- La classe è denominata `TranslateExtension`, ma per convenzione vi si può fare riferimento come **Translate** nel markup.
- La classe implementa `IMarkupExtension`, che è necessario perché Xamarin.Forms funzioni.
- `"UsingResxLocalization.Resx.AppResources"` è l'identificatore di risorsa per le risorse RESX. È costituito dallo spazio dei nomi predefinito, dalla cartella in cui si trovano i file di risorse e dal nome file RESX predefinito.
- La classe `ResourceManager` viene creata usando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` per determinare l'assembly corrente da cui caricare le risorse e viene memorizzata nella cache nel campo statico `ResMgr`. Viene creata con un tipo `Lazy` in modo che la sua creazione venga posticipata fino al primo utilizzo nel metodo `ProvideValue`.
- `ci` usa il servizio di dipendenza per ottenere la lingua utente scelta dal sistema operativo nativo.
- `GetString` è il metodo che recupera la stringa effettiva tradotta dal file di risorse. Nella piattaforma UWP `ci` sarà Null perché l'interfaccia `ILocalize` non è implementata in tali piattaforme. Ciò equivale a chiamare il metodo `GetString` con solo il primo parametro. Al contrario, il framework di risorse riconoscerà automaticamente le impostazioni locali e recupererà la stringa tradotta dal file RESX appropriato.
- La gestione degli errori è stata inclusa per facilitare il debug delle risorse mancanti generando un'eccezione (solo in modalità `DEBUG`).

Il frammento di codice XAML seguente illustra come usare l'estensione di markup. I passaggi per fare ciò sono due:

1. Dichiarare lo spazio dei nomi `xmlns:i18n` personalizzato nel nodo radice. Gli elementi `namespace` e `assembly` devono corrispondere esattamente alle impostazioni di progetto. In questo esempio è così ma nel progetto potrebbero essere diversi.
2. Usare la sintassi `{Binding}` per gli attributi che solitamente includono testo per chiamare l'estensione di markup `Translate`. La chiave di risorsa è l'unico parametro obbligatorio.

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

Anche la sintassi seguente, più dettagliata, è valida per l'estensione di markup:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localizzazione di elementi specifici della piattaforma

Anche se è possibile gestire la traduzione dell'interfaccia utente nel codice Xamarin.Forms, vi sono alcuni elementi che devono essere localizzati in ogni singolo progetto specifico della piattaforma. In questa sezione viene illustrato come localizzare gli elementi seguenti:

- Nome applicazione
- Immagini

Il progetto di esempio include un'immagine localizzata denominata **flag.png**, cui viene fatto riferimento in C# come indicato di seguito:

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

Anche il file XAML fa riferimento all'immagine di flag, come illustrato in seguito:

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

Tutte le piattaforme risolveranno automaticamente i riferimenti a immagini come questo nelle versioni localizzate delle immagini, purché siano state implementate le strutture dei progetti illustrate in seguito.

### <a name="ios-application-project"></a>Progetto di applicazione iOS

iOS usa uno standard di denominazione chiamato progetti di localizzazione o directory **.lproj** per contenere risorse di tipo immagine e stringa. Queste directory possono contenere le versioni localizzate delle immagini usate nell'app nonché il file **InfoPlist.strings** che può essere usato per localizzare il nome dell'app. Per altre informazioni sulla localizzazione in iOS, vedere [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Immagini

Questa schermata illustra l'app di esempio iOS con directory **.lproj** specifiche della lingua. La directory per lo spagnolo denominata **es.lproj** contiene le versioni localizzate dell'immagine predefinita, nonché l'immagine **flag.png**:

![](text-images/ios-resources.png "Directory del progetto di localizzazione di iOS")

Ogni directory di lingua contiene una copia di **flag.png**, localizzata per tale lingua. Se non viene specificata alcuna immagine, il sistema operativo sceglierà per impostazione predefinita l'immagine nella directory della lingua predefinita. Per il supporto completo di Retina, è necessario specificare **@2x** e **@3x** copie di ogni immagine.

#### <a name="app-name"></a>Nome app

**InfoPlist.strings** contiene solo un valore della chiave per configurare il nome dell'app:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando l'applicazione viene eseguita, vengono localizzati sia il nome dell'app che l'immagine:

![](text-images/ios-imageicon.png "Localizzazione di immagine e testo dell'app iOS di esempio")

### <a name="android-application-project"></a>Progetto di applicazione Android

Android segue uno schema diverso per archiviare le immagini localizzate tramite directory di **risorse drawable** e **stringhe** con un suffisso di codice della lingua. Quando è necessario un codice delle impostazioni locali a quattro caratteri, come ad esempio zh-TW o pt-BR, Android richiede una **r** aggiuntiva dopo il trattino/prima del codice dell'impostazione locale. Ad esempio: zh-rTW o pt rBR. Per altre informazioni sulla localizzazione in Android, vedere [Localizzazione in Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Immagini

In questo screenshot viene visualizzato l'esempio Android di esempio con alcune risorse drawable e stringhe localizzate:

![](text-images/android-resources.png "Directory di risorse drawable e stringhe localizzate in Android")

Si noti che Android non usa i codici zh-Hans e zh-Hant per il cinese semplificato e il cinese tradizionale. Supporta solo i codici paese zh-CN e zh-TW.

Per supportare immagini con una risoluzione diversa per schermi ad alta densità, creare cartelle di lingue aggiuntive con il suffisso `-*dpi`, ad esempio **drawable-es-mdpi**, **drawable-es-xdpi**, **drawable-es-xxdpi** e così via. Per altre informazioni, vedere [Providing Alternative Android Resources](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) (Specificare risorse Android alternative).

#### <a name="app-name"></a>Nome app

**strings.xml** contiene solo un valore della chiave per configurare il nome dell'app:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Aggiornare **MainActivity.cs** nel progetto dell'app per Android in modo che `Label` faccia riferimento alle stringhe XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Il nome e l'immagine dell'app sono ora localizzati. Di seguito viene riportato lo screenshot del risultato (in spagnolo):

![](text-images/android-imageicon.png "Localizzazione di immagine e testo dell'app Android di esempio")

### <a name="universal-windows-platform-application-projects"></a>Progetti dell'app per la piattaforma UWP (Universal Windows Platform)

La piattaforma UWP include un'infrastruttura di risorse che semplifica la localizzazione delle immagini e del nome dell'app. Per altre informazioni sulla localizzazione in UWP, vedere [Localizzazione in UWP](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Immagini

Le immagini possono essere localizzate inserendole in una cartella specifica delle risorse, come illustrato nello screenshot seguente:

![](text-images/uwp-image-folder-structure.png "Struttura delle cartelle di localizzazione immagine piattaforma UWP")

In fase di esecuzione l'infrastruttura di risorse di Windows seleziona l'immagine appropriata in base alle impostazioni locali.

## <a name="summary"></a>Riepilogo

Le applicazioni Xamarin.Forms possono essere localizzate mediante i file RESX e le classi di globalizzazione .NET. Fatto salvo per una piccola quantità di codice specifico della piattaforma che consente di rilevare la lingua preferita dall'utente, la maggior parte della localizzazione viene scritta nel codice comune.

Le immagini vengono generalmente gestite in modo specifico per la piattaforma per sfruttare il supporto per diverse risoluzioni offerto sia da iOS che da Android.

## <a name="related-links"></a>Collegamenti correlati

- [RESX Localization Sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization) (Esempio di localizzazione RESX)
- [TodoLocalized Sample App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalized) (App di esempio TodoLocalized)
- [Localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md)
- [Localizzazione in Android](~/android/app-fundamentals/localization.md)
- [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Uso della classe CultureInfo](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Individuazione e uso di risorse per impostazioni cultura specifiche](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
