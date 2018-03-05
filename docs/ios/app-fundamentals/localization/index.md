---
title: Localizzazione di iOS
description: "Questo documento vengono illustrate le funzionalità di localizzazione di iOS SDK e come accedervi con Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: ea91dbcf7148651cb5d10acae4ada8bb6758c39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="ios-localization"></a>Localizzazione di iOS

_Questo documento vengono illustrate le funzionalità di localizzazione di iOS SDK e come accedervi con Xamarin._

Consultare la [codifiche di internazionalizzazione](encodings.md) per istruzioni su come includere caratteri set di codici e nelle applicazioni che è necessario elaborare dati non Unicode.

## <a name="ios-platform-features"></a>Funzionalità della piattaforma iOS

In questa sezione vengono descritte alcune delle funzionalità di localizzazione in iOS. Ignorare il [nella sezione successiva](#basics) per visualizzare codice specifico ed esempi.

### <a name="language"></a>Linguaggio

Gli utenti di scegliere la lingua nel **impostazioni** app. Questa impostazione interessa le stringhe relative a lingue e le immagini visualizzate dal sistema operativo, nonché le applicazioni che rileva le impostazioni di lingua.

Si tratta in cui gli utenti verranno decidere se desiderano visualizzare inglese, spagnolo, giapponese, francese o altra lingua visualizzata nelle proprie app.

L'elenco delle lingue supportate in iOS 7 effettivo è: inglese (Stati Uniti), inglese (Regno Unito), cinese semplificato, cinese (tradizionale), francese, tedesco, italiano, giapponese, coreano, spagnolo, arabo, catalano, croato, ceco, danese, olandese, finlandese, greco, ebraico, Inglese (Australia), ungherese, indonesiano, malese, norvegese, polacco, portoghese, portoghese (Brasile), rumeno, russo, slovacco, svedese, tailandese, turco, ucraino e vietnamita, spagnolo (Messico).

La lingua corrente può eseguire query per l'accesso sul primo elemento del `PreferredLanguages` matrice:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Questo valore è un codice di lingua, ad esempio `en` per l'inglese, `es` per lo spagnolo, `ja` per il giapponese e così via. _Il valore restituito è limitato a uno dei processi di supportata dall'applicazione (utilizzando le regole di fallback per determinare la migliore corrispondenza)._

Il codice dell'applicazione non sempre è necessario controllare il valore - Xamarin e iOS entrambi offre funzionalità che consentono di fornire automaticamente la stringa corretta o la risorsa per la lingua dell'utente. Queste funzionalità sono descritte nel resto di questo documento.

> [!NOTE]
> **Nota:** prima di iOS 9, il codice consigliato è `var lang = NSLocale.PreferredLanguages [0];`.
>
> Vedere i risultati restituiti da questo codice modificato in iOS 9 - [TN2418 Nota tecnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) per ulteriori informazioni.
>
> È possibile continuare a utilizzare `NSLocale.PreferredLanguages [0]` per determinare i valori effettivi selezionati dall'utente (indipendentemente dal fatto di versioni localizzate supportate dall'app).

### <a name="locale"></a>Impostazioni locali

Gli utenti di scegliere le proprie impostazioni internazionali nel **impostazioni** app. Questa impostazione riguarda la modalità di formattazione di date, ore, numeri e valuta.

Consente agli utenti di scegliere se visualizzano i formati di ora 12 o 24 ore, se il separatore decimale è una virgola o un punto e l'ordine del giorno, mese e anno nella visualizzazione della data.

Con Xamarin è possibile accedere alle classi di entrambi Apple iOS (`NSNumberFormatter`) nonché le classi .NET in System. Globalization. Gli sviluppatori devono valutare che è più adatto alle loro esigenze, esistono diverse funzionalità disponibili in ogni. In particolare, se si sono recuperare e visualizzare i prezzi di acquisto In App utilizzando StoreKit sicuramente utilizzare le classi di formattazione di Apple per le informazioni sui prezzi restituiti.

Le impostazioni locali correnti è possibile eseguire query tramite:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Il primo valore può essere memorizzati nella cache dal sistema operativo e pertanto potrebbe non riflettere sempre la lingua dell'utente attualmente selezionato. Utilizzare il secondo valore per ottenere le impostazioni locali di attualmente selezionata.


### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

Genera l'errore iOS un `NSCurrentLocaleDidChangeNotification` quando l'utente aggiorna le impostazioni locali. Le applicazioni possono restare in ascolto per questa notifica mentre sono in esecuzione e apportare le modifiche appropriate all'interfaccia utente.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Nozioni di localizzazione in iOS

Le caratteristiche seguenti di iOS sono possibile sfruttare facilmente in Xamarin per fornire risorse localizzate per la visualizzazione per l'utente. Consultare la [TaskyL10n esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) per informazioni su come implementare questi concetti.

### <a name="infoplist"></a>Info. plist

Prima di iniziare, configurare il **Info. plist** file con le chiavi seguenti:

- `CFBundleDevelopmentRegion` -la lingua predefinita per l'applicazione (in genere la lingua parlata dagli sviluppatori e utilizzati nei storyboard e le risorse stringa e immagine). Nell'esempio seguente, **en** (per la lingua inglese) è specificato.
- `CFBundleLocalizations` -una matrice di altri processi supportata dall'applicazione, anche con i codici di lingua come **es** (spagnolo) e **pt-PT** (portoghese parlata in Portogallo).

```xml
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
<key>CFBundleLocalizations</key>
<array>
  <string>de</string>
  <string>es</string>
  <string>ja</string>
  ...
</array>
```

### <a name="localizedstring-method"></a>Metodo Stringalocalizzata

Il `NSBundle.MainBundle.LocalizedString` metodo cerca il testo localizzato che è stato archiviato in **.strings** i file del progetto. Questi file sono organizzati per lingua, nella directory speciale denominata con un **.lproj** suffisso.

#### <a name="strings-file-locations"></a>percorsi dei file .strings

- **Base.lproj** è la directory che contiene le risorse per la lingua predefinita.
  Spesso si trova nella radice del progetto (ma può anche essere inserita nel **risorse** cartella).
- **<language>.lproj** directory vengono create per ogni lingua supportata, in genere nel **risorse** cartella.

Può esistere un numero di diversi **.strings** file nella directory ogni lingua:

- **Localizable.Strings** -elenco principale di testo localizzato.
- **InfoPlist.strings** - alcune chiavi specifiche sono consentite in questo file per la conversione di operazioni come il nome dell'applicazione.
- **< nome dello storyboard > .strings** -file facoltativo che contiene le traduzioni per gli elementi dell'interfaccia utente in uno storyboard.

Il **azione di compilazione** per questi file devono essere **Bundle di risorse**.

#### <a name="strings-file-format"></a>formato di file .strings

La sintassi per i valori stringa localizzata è:

```csharp
/* comment */
"key"="localized-value";
```

Si devono escape nelle stringhe di caratteri seguenti:

* `\"`  offerta
* `\\`  Barra rovesciata
* `\n`  Carattere di nuova riga

Questo è un esempio **es/Localizable.strings** (ie. File spagnolo) esempio:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Immagini

Per localizzare un'immagine in iOS:

1. Fare riferimento all'immagine nel codice, ad esempio:

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. Inserire il file di immagine predefinita **flag.png** in **Base.lproj** (la directory di linguaggio di sviluppo nativo).

3. Se lo si desidera posizionare le versioni localizzate dell'immagine nel **.lproj** cartelle per ogni lingua (ad es. **es.lproj**, **ja.lproj**). Utilizzare lo stesso nome file **flag.png** in ogni directory di linguaggio.

Se un'immagine non è presente per una determinata lingua, iOS verrà fallback nella cartella della lingua predefinita e caricare l'immagine da tale posizione.


#### <a name="launch-images"></a>Le immagini di avvio

Utilizzare le convenzioni di denominazione standard per le immagini di avvio (e XI o di uno Storyboard per i modelli di iPhone 6) durante il posizionamento nel **.lproj** directory per ogni lingua.

```csharp
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome dell'App

Inserimento di un **InfoPlist.strings** file in un **.lproj** directory consente di eseguire l'override di determinati valori da dell'app **Info. plist**, inclusi il nome dell'applicazione:

```csharp
"CFBundleDisplayName" = "LeónTodo";
```

Altre chiavi che è possibile utilizzare per [localizzare stringhe specifiche dell'applicazione](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) sono:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright


<!--
## App icon

Does not seem to be possible (although it definitely used to be!).
-->

### <a name="localization-native-development-region"></a>Localizzazione lo sviluppo nativo area

Le stringhe predefinite (nella **Base.lproj** cartella) verrà presupposto che la lingua 'fallback'. Ciò significa che se una traduzione è richiesto nel codice e non è disponibile per una lingua corrente, il **Base.lproj** cartella verrà effettuata la ricerca per la stringa predefinita da utilizzare (se viene trovata alcuna corrispondenza, la stringa dell'identificatore di conversione stesso è visualizzato).

Gli sviluppatori possono scegliere una lingua diversa per il fallback impostando la chiave plist `CFBundleDevelopmentRegionKey`. Il valore deve essere impostato per il codice di lingua per il linguaggio di sviluppo nativo. Questa schermata è riportato che un plist Xamarin Studio con l'area di sviluppo nativo impostata sullo spagnolo (es):

![](images/cfbundledevelopmentregion.png "Proprietà InfoPList.strings")

Se la lingua predefinita utilizzata in tutto il codice e gli storyboard non è in inglese, impostare questo valore in modo da riflettere il linguaggio nativo utilizzato in tutto il codice dell'applicazione.

### <a name="dates-and-times"></a>Date e ore

Sebbene sia possibile utilizzare le funzioni predefinite di data e ora .NET (insieme corrente `CultureInfo`) per formattare date e ore per impostazioni locali, questo potrebbe ignorare delle impostazioni locali specifiche impostazioni utente (che possono essere impostate separatamente da linguaggio).

Utilizzare iOS `NSDateFormatter` per produrre l'output corrispondente alle preferenze dell'utente delle impostazioni locali. Esempio di codice seguente illustra la data di base e l'ora le opzioni di formattazione:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Risultati per l'inglese degli Stati Uniti:

```csharp
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Risultati per lo Spagnolo Spagna:

```csharp
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consultare il Apple [data formattatori](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentazione per ulteriori informazioni. Durante la verifica basata sulle impostazioni locali formattazione di data e ora, selezionare entrambe **iPhone Language** e **area** impostazioni.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Layout da destra a sinistra (RTL)

iOS fornisce una serie di funzionalità per facilitare la creazione di applicazioni che supportano da destra a sinistra:

* Utilizzare **Layout automatico** `leading` e `trailing` attributi per controllo aligment (che corrisponde a *sinistro* e *destra* per l'inglese, ad esempio, ma è invertita per le lingue RTL).
  Il [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) controllo risulta particolarmente utile per disporre i controlli per essere compatibile con da destra a sinistra.
* Utilizzare `TextAlignment = UITextAlignment.Natural` per l'allineamento del testo (che sarà *sinistro* per la maggior parte delle lingue ma *destra* per RTL).
* `UINavigationController` Consente di capovolgere il pulsante Indietro e inverte la direzione di scorrere automaticamente.

Le schermate seguenti viene illustrato il [localizzata **Tasky** esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo ed ebraico (anche se nei campi immesso in lingua inglese):

[ ![](images/rtl-ar-sml.png "Localizzazione in arabo")](images/rtl-ar.png "Arabic") 

[ ![](images/rtl-he-sml.png "Localizzazione in ebraico")](images/rtl-he.png "Hebrew")

iOS invertito automaticamente il `UINavigationController`, e gli altri controlli vengono posizionati all'interno `UIStackView` o allineate con Layout automatico.
Testo da destra a sinistra è localizzato mediante **.strings** file nello stesso modo come testo LTR.


<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizzazione dell'interfaccia utente nel codice

Il [Tasky (localizzato nel codice)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) esempio viene illustrato come localizzare un'applicazione in cui viene compilata l'interfaccia utente nel codice (anziché XIBs o storyboard).

### <a name="project-structure"></a>Struttura del progetto



![](images/solution-code.png "Struttura ad albero di risorse")

### <a name="localizablestrings-file"></a>File Localizable.Strings

Come descritto in precedenza, il **Localizable.strings** formato di file è costituito da coppie chiave-valore, dove la chiave è una stringa selezionate dall'utente che indica

Lo spagnolo (**es**) versioni localizzate per il codice di esempio sono illustrati di seguito:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Eseguendo la localizzazione

Nel codice dell'applicazione, ogni volta che visualizza il testo dell'interfaccia utente è impostato (se si tratta di testo dell'etichetta, o il segnaposto dell'input e così via) il codice Usa iOS `LocalizedString` per recuperare la traduzione corretta da visualizzare:

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizzazione di interfacce utente di Storyboard

L'esempio [Tasky (storyboard localizzate)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) viene illustrato come localizzare il testo nei controlli in uno storyboard.

### <a name="project-structure"></a>Struttura del progetto

Il **Base.lproj** directory contiene lo storyboard e deve inoltre contenere tutte le immagini utilizzate nell'applicazione.

Contenere altre directory language un **Localizable.strings** file per tutte le risorse stringa a cui fa riferimento nel codice, nonché una **MainStoryboard.strings** file che contiene le traduzioni per il testo nel storyboard.

![](images/solution-storyboard.png "Struttura ad albero di risorse")

Directory language deve contenere una copia di tutte le immagini che sono stati localizzati, per eseguire l'override di quello presente nel **Base.lproj**.

### <a name="object-id--localization-id"></a>ID di oggetto / ID di localizzazione

Durante la creazione e modifica dei controlli in uno storyboard, selezionare ogni controllo e controllare l'ID da utilizzare per la localizzazione:

* in Visual Studio per Mac, è si trova nel riquadro proprietà e ha chiamato **ID di localizzazione**.
* in Xcode, viene chiamato **ID oggetto**.

È un valore stringa che è spesso un modulo come **"NF3-h8-xmR"**:

![](images/xs-designer-localization-id.png "Visualizzazione di Xcode di localizzazione di Storyboard")

Questo valore viene utilizzato il **.strings** file per assegnare il testo tradotto automaticamente a ogni controllo.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

Il formato del file di conversione di uno storyboard è simile al **Localizable.strings** file, ad eccezione del fatto che la chiave (il valore a sinistra) non può essere definita dall'utente, ma invece deve avere un formato molto specifico: `ObjectID.property`.

Nell'esempio **Mainstoryboard.strings** seguito è possibile visualizzare `UITextField`hanno un `placeholder` proprietà di testo che può essere localizzato; `UILabel`hanno un `text` proprietà; e `UIButton`testo predefinito s viene impostato utilizzando `normalTitle`:

```csharp
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> **Utilizzo di uno storyboard con classi di dimensione** può comportare le traduzioni non visualizzati. Ciò è probabilmente correlato [questo problema](http://stackoverflow.com/questions/24989208/xcode-6-does-not-localize-interface-builder) in cui è indicato la documentazione di Apple: localizzazione di storyboard XI verranno non localizzare correttamente se tutte le tre condizioni sono vere: storyboard o XI utilizza classi di dimensione. La localizzazione di base e la destinazione build vengono impostati su Universal. La compilazione è destinato a iOS 7.0.
Per risolvere il problema è quello di duplicare il file storyboard, le stringhe in due file identici: **MainStoryboard~iphone.strings** e **MainStoryboard~ipad.strings**:

> ![](images/xs-dup-strings.png "File di stringhe")


<!--
# Native Formatting

Xamarin.iOS applications can take advantage of the .NET framework's formatting options for localizing
numbers and dates.

### Date string formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html#//apple_ref/doc/uid/TP40002369-SW1


NSDateFormatter formatter = new NSDateFormatter ();
formatter.DateFormat = "MMMM/dd/yyyy";
NSString dateString = new NSString (formatter.ToString (d));


### Number formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfNumberFormatting10_4.html#//apple_ref/doc/uid/TP40002368-SW1

-->

<a name="appstore" />

## <a name="app-store-listing"></a>Elenco di App Store

Segue domande frequenti di Apple [App Store localizzazione](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) per immettere le traduzioni per ogni paese dell'app è in vendita. Si noti l'avviso indicante che la traduzione verranno visualizzata solo se l'app contiene anche una versione localizzata **.lproj** directory per la lingua.

<!--

Once you’ve entered your application into iTunes Connect the default language
metadata and screenshots will appear as shown:

![]( "itunes connect 1")

Use the language list on the right to select other languages to provide
translated application name, description, search keywords, URLs and screenshots.
The complete list of languages is shown in this screenshot:

![]( "itunes connect 2")
-->

## <a name="summary"></a>Riepilogo

Questo articolo vengono illustrate le nozioni di base di localizzazione di applicazioni iOS utilizzando le funzionalità di gestione e di storyboard di risorse predefiniti.

È possibile ottenere ulteriori informazioni su i18n e L10n per le app iOS, Android e multipiattaforma (incluso xamarin. Forms) in [questa guida multipiattaforma](~/cross-platform/app-fundamentals/localization.md).


## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato nel codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (storyboard localizzate) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guida di Apple localizzazione](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Cenni preliminari sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di xamarin. Forms](~/xamarin-forms/app-fundamentals/localization.md)
- [Localizzazione di Android](~/android/app-fundamentals/localization.md)
