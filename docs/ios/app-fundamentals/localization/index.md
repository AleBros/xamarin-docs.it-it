---
title: Localizzazione di iOS
description: Questo documento vengono illustrate le funzionalità di localizzazione di iOS SDK e come accedervi con Xamarin.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 96ee8343bb2b99fdc0be9437aaa7737a70b8c19c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="ios-localization"></a>Localizzazione di iOS

_Questo documento vengono illustrate le funzionalità di localizzazione di iOS SDK e come accedervi con Xamarin._

Consultare la [codifiche di internazionalizzazione](encodings.md) per istruzioni su come includere caratteri set di codici e nelle applicazioni che è necessario elaborare dati non Unicode.

## <a name="ios-platform-features"></a>Funzionalità della piattaforma iOS

In questa sezione vengono descritte alcune delle funzionalità di localizzazione in iOS. Ignorare il [nella sezione successiva](#basics) per visualizzare codice specifico ed esempi.

### <a name="language"></a>Linguaggio

Gli utenti di scegliere la lingua nel **impostazioni** app. Questa impostazione interessa le stringhe relative a lingue e le immagini visualizzate dal sistema operativo e nelle app. 

Per determinare il linguaggio utilizzato in un'app, ottenere il primo elemento di `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Questo valore è un codice di lingua, ad esempio `en` per l'inglese, `es` per lo spagnolo, `ja` per il giapponese e così via. Il valore restituito è limitato a uno dei processi di supportata dall'applicazione (utilizzando le regole di fallback per determinare la migliore corrispondenza).

Il codice dell'applicazione non sempre è necessario controllare il valore-Xamarin e iOS entrambi offre funzionalità che consentono di fornire automaticamente la stringa corretta o la risorsa per la lingua dell'utente. Queste funzionalità sono descritte nel resto di questo documento.

> [!NOTE]
> Utilizzare `NSLocale.PreferredLanguages` per determinare le preferenze di lingua dell'utente, indipendentemente dal fatto di versioni localizzate supportati dall'applicazione. I valori restituiti da questo metodo modificato in iOS 9. vedere [TN2418 Nota tecnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) per informazioni dettagliate.

### <a name="locale"></a>Impostazioni locali

Gli utenti di scegliere le proprie impostazioni internazionali nel **impostazioni** app. Questa impostazione riguarda la modalità di formattazione di date, ore, numeri e valuta.

Consente agli utenti di scegliere se viene visualizzato di formati di ora 12 o 24 ore, se il separatore decimale è una virgola o un punto e l'ordine del giorno, mese e anno nella visualizzazione della data.

Con Xamarin è possibile accedere alle classi di entrambi Apple iOS (`NSNumberFormatter`) nonché le classi .NET in System. Globalization. Gli sviluppatori devono valutare che è più adatto alle loro esigenze, esistono diverse funzionalità disponibili in ogni. In particolare, se si il recupero e visualizzazione dei prezzi di acquisto In-App con StoreKit è necessario usare classi di formattazione di Apple per le informazioni sui prezzi restituiti.

Le impostazioni locali correnti possono eseguire query per uno dei due modi:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Il primo valore può essere memorizzati nella cache dal sistema operativo e pertanto potrebbe non riflettere sempre la lingua dell'utente attualmente selezionato. Utilizzare il secondo valore per ottenere le impostazioni locali di attualmente selezionata.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

Genera l'errore iOS un `NSCurrentLocaleDidChangeNotification` quando l'utente aggiorna le impostazioni locali. Le applicazioni possono restare in ascolto per questa notifica mentre sono in esecuzione e possono apportare modifiche appropriate all'interfaccia utente.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Nozioni di localizzazione in iOS

Le caratteristiche seguenti di iOS sono possibile sfruttare facilmente in Xamarin per fornire risorse localizzate per la visualizzazione per l'utente. Consultare la [TaskyL10n esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) per informazioni su come implementare questi concetti.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Specifica impostazione predefinita e le lingue supportate in Info. plist

In [domande tecniche A QA1828: determinazione di lingua per l'App iOS](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple descrive la modalità di selezione di una lingua da utilizzare in un'app iOS. I seguenti fattori influiscono quale lingua è visualizzata:

- Lingue preferita dell'utente (trovato nel **impostazioni** app)
- Le versioni localizzate in bundle con l'app (.lproj cartelle)
- `CFBundleDevelopmentRegion` (**Info. plist** valore che specifica la lingua predefinita per l'app)
- `CFBundleLocalizations` (**Info. plist** matrice specificando tutte supportate dei processi)

Come indicato nella tecnica di domande e risposte, `CFBundleDevelopmentRegion` rappresenta l'area predefinita di un'applicazione e della lingua. Se l'app in modo esplicito non supporta le lingue preferite dell'utente, utilizza la lingua specificata da questo campo. 

> [!IMPORTANT]
> iOS 11 si applica questo meccanismo di selezione della lingua in modo più restrittivo rispetto a versioni precedenti del sistema operativo. Per questo motivo, qualsiasi app iOS 11 che non dichiara in modo esplicito i relativi processi supportate: incluse le cartelle .lproj o impostando un valore per `CFBundleLocalizations` : potrebbe essere visualizzato una lingua diversa in iOS 11 rispetto iOS 10.

Se `CFBundleDevelopmentRegion` non è stato specificato il **Info. plist** file, gli strumenti di compilazione xamarin utilizzano un valore predefinito di `en_US`. Mentre questi valori possono variare in una versione futura, significa che la lingua predefinita è impostata sull'inglese.

Per garantire che l'app consente di selezionare un linguaggio previsto, eseguire le operazioni seguenti:

- Specificare una lingua predefinita. Aprire **Info. plist** e utilizzare il **origine** visualizzazione per impostare un valore per il `CFBundleDevelopmentRegion` chiave; in XML, dovrebbe essere simile al seguente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Questo esempio viene utilizzato "es" per specificare che, quando nessuno di un utente preferito lingue sono supportate, per impostazione predefinita lo spagnolo.

- Dichiarare tutte supportate dei processi. In **Info. plist**, utilizzare il **origine** Visualizza una matrice per impostare il `CFBundleLocalizations` chiave; in XML, dovrebbe essere simile al seguente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

App xamarin che sono state localizzate utilizzando meccanismi di .NET, ad esempio file con estensione resx devono fornire tali **Info. plist** anche i valori.

Per ulteriori informazioni su questi **Info. plist** chiavi, dare un'occhiata Apple [informazioni di riferimento di chiave proprietà List](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="localizedstring-method"></a>Metodo Stringalocalizzata

Il `NSBundle.MainBundle.LocalizedString` metodo cerca il testo localizzato che è stato archiviato in **.strings** i file del progetto. Questi file sono organizzati per lingua, nella directory speciale denominata con un **.lproj** suffisso.

#### <a name="strings-file-locations"></a>percorsi dei file .strings

- **Base.lproj** è la directory che contiene le risorse per la lingua predefinita.
  Spesso si trova nella radice del progetto (ma può anche essere inserita nel **risorse** cartella).
- **<language>.lproj** directory vengono create per ogni lingua supportata, in genere nel **risorse** cartella.

Può esistere un numero di diversi **.strings** file nella directory ogni lingua:

- **Localizable.Strings** – elenco principale di testo localizzato.
- **InfoPlist.strings** : alcune chiavi specifiche sono consentite in questo file per la conversione, ad esempio, il nome dell'applicazione.
- **< nome dello storyboard > .strings** : file facoltativo che contiene le traduzioni per gli elementi dell'interfaccia utente in uno storyboard.

Il **azione di compilazione** per questi file devono essere **Bundle di risorse**.

#### <a name="strings-file-format"></a>formato di file .strings

La sintassi per i valori stringa localizzata è:

```console
/* comment */
"key"="localized-value";
```

Si devono escape nelle stringhe di caratteri seguenti:

* `\"`  offerta
* `\\`  Barra rovesciata
* `\n`  Carattere di nuova riga

Questo è un esempio **es/Localizable.strings** (ie. File spagnolo) esempio:

```console
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

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome dell'App

Inserimento di un **InfoPlist.strings** file in un **.lproj** directory consente di eseguire l'override di determinati valori da dell'app **Info. plist**, inclusi il nome dell'applicazione:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Altre chiavi che è possibile utilizzare per [localizzare stringhe specifiche dell'applicazione](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) sono:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

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

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Risultati per lo Spagnolo Spagna:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consultare il Apple [data formattatori](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentazione per ulteriori informazioni. Durante la verifica basata sulle impostazioni locali formattazione di data e ora, selezionare entrambe **iPhone Language** e **area** impostazioni.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Layout da destra a sinistra (RTL)

iOS fornisce una serie di funzionalità per facilitare la creazione di applicazioni che supportano da destra a sinistra:

* Layout di utilizzo automatico `leading` e `trailing` gli attributi per aligment controllo (che corrisponde a sinistra e destra per la lingua inglese, ma è invertita per le lingue RTL).
  Il [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) controllo risulta particolarmente utile per disporre i controlli per essere compatibile con da destra a sinistra.
* Utilizzare `TextAlignment = UITextAlignment.Natural` per l'allineamento del testo (che verrà lasciato per la maggior parte delle lingue, ma a destra per RTL).
* `UINavigationController` Consente di capovolgere il pulsante Indietro e inverte la direzione di scorrere automaticamente.

Le schermate seguenti viene illustrato il [localizzata esempio Tasky](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo ed ebraico (anche se nei campi immesso in lingua inglese):

[![](images/rtl-ar-sml.png "Localizzazione in arabo")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "Localizzazione in ebraico")](images/rtl-he.png#lightbox "Hebrew")

iOS invertito automaticamente il `UINavigationController`, e gli altri controlli vengono posizionati all'interno `UIStackView` o allineate con Layout automatico.
Testo da destra a sinistra è localizzato mediante **.strings** file nello stesso modo come testo LTR.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizzazione dell'interfaccia utente nel codice

Il [Tasky (localizzato nel codice)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) esempio viene illustrato come localizzare un'applicazione in cui viene compilata l'interfaccia utente nel codice (anziché XIBs o storyboard).

### <a name="project-structure"></a>Struttura del progetto

![](images/solution-code.png "Struttura ad albero di risorse")

### <a name="localizablestrings-file"></a>File Localizable.Strings

Come descritto in precedenza, il **Localizable.strings** formato di file è costituito da coppie chiave-valore. La chiave viene descritto lo scopo della stringa e il valore è il testo tradotto da usare nell'app.

Lo spagnolo (**es**) versioni localizzate per il codice di esempio sono illustrati di seguito:

```console
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

* In Visual Studio per Mac, è disponibile nel **proprietà riempimento** e viene chiamato **ID di localizzazione**.
* in Xcode, viene chiamato **ID oggetto**.

Questo valore di stringa ha spesso un modulo, ad esempio "NF3 h8 xmR", come illustrato nella schermata seguente:

![](images/xs-designer-localization-id.png "Visualizzazione di Xcode di localizzazione di Storyboard")

Questo valore viene utilizzato il **.strings** file per assegnare il testo tradotto automaticamente a ogni controllo.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

Il formato del file di conversione di uno storyboard è simile al **Localizable.strings** file, ad eccezione del fatto che la chiave (il valore a sinistra) non può essere definita dall'utente, ma invece deve avere un formato molto specifico: `ObjectID.property`.

Nell'esempio **Mainstoryboard.strings** seguito è possibile visualizzare `UITextField`hanno un `placeholder` proprietà di testo che può essere localizzato; `UILabel`hanno un `text` proprietà; e `UIButton`testo predefinito s viene impostato utilizzando `normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> Utilizzo di uno storyboard con classi di dimensioni può comportare traduzioni non presenti nell'applicazione. [Note sulla versione di Apple Xcode](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indicano che un storyboard o XI verranno non localizzare correttamente se tre operazioni sono true: utilizza le classi di dimensioni e la localizzazione di base e la destinazione build vengono impostate su Universal iOS 7.0 è destinata la compilazione. Per risolvere il problema è quello di duplicare il file storyboard, le stringhe in due file identici: **MainStoryboard~iphone.strings** e **MainStoryboard~ipad.strings**, come illustrato nella schermata seguente:
> 
> ![](images/xs-dup-strings.png "File di stringhe")

<a name="appstore" />

## <a name="app-store-listing"></a>Elenco di App Store

Segue domande frequenti di Apple [App Store localizzazione](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) per immettere le traduzioni per ogni paese dell'app è in vendita. Si noti l'avviso indicante che la traduzione verranno visualizzata solo se l'app contiene anche una versione localizzata **.lproj** directory per la lingua.

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
