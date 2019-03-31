---
title: Localizzazione in xamarin. IOS
description: 'Questo documento descrive le funzionalità di localizzazione di iOS e su come usare queste funzionalità nell''App xamarin. IOS. Viene descritto language, delle impostazioni locali, i file di stringhe, immagini di avvio e altro ancora.'
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
---

# <a name="localization-in-xamarinios"></a>Localizzazione in xamarin. IOS

_Questo documento illustra le funzionalità di localizzazione di iOS SDK e come accedervi con Xamarin._

Vedere le [codifiche di internazionalizzazione](encodings.md) per istruzioni sull'inclusione di pagine di codice/set di caratteri nelle applicazioni che devono elaborare dati non Unicode.

## <a name="ios-platform-features"></a>funzionalità della piattaforma iOS

Questa sezione descrive alcune delle funzionalità di localizzazione in iOS. Ignorare per il [nella sezione successiva](#localization-basics-in-ios) per visualizzare codice specifico ed esempi.

### <a name="language"></a>Linguaggio

Gli utenti di scegliere la lingua nel **impostazioni** app. Questa impostazione influisce sulla stringhe lingua e le immagini visualizzate dal sistema operativo e nelle app.

Per determinare la lingua in uso in un'app, ottenere il primo elemento di `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Questo valore sarà un codice di lingua, ad esempio `en` per inglese `es` per lo spagnolo, `ja` per il giapponese e così via. Il valore restituito è limitato a uno delle versioni localizzate supportate dall'applicazione (utilizzando le regole di fallback per determinare la migliore corrispondenza).

Il codice dell'applicazione non sempre è necessario verificare la presenza di questo valore, Xamarin e iOS entrambi offre funzionalità che consentono di fornire automaticamente la stringa corretta o la risorsa per la lingua dell'utente. Queste funzionalità sono descritte nel resto di questo documento.

> [!NOTE]
> Usare `NSLocale.PreferredLanguages` per determinare le preferenze di lingua dell'utente, indipendentemente dal fatto di localizzazioni supportati dall'app. I valori restituiti da questo metodo modificato in iOS 9. visualizzare [TN2418 Nota tecnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) per informazioni dettagliate.

### <a name="locale"></a>Impostazioni locali

Gli utenti scelgono le impostazioni locali nel **impostazioni** app. Questa impostazione riguarda la modalità di formattazione di date, ore, numeri e valuta.

Ciò consente agli utenti di scegliere se viene visualizzato di formati di ora 12 o 24 ore, se il separatore decimale è una virgola o un punto e l'ordine del giorno, mese e anno nella visualizzazione della data.

Con Xamarin è possibile accedere alle classi di iOS di Apple entrambi (`NSNumberFormatter`), nonché le classi di .NET in System. Globalization. Gli sviluppatori devono valutare che è più adatto alle loro esigenze, poiché sono presenti varie funzionalità disponibili in ognuno. In particolare, se si ha il recupero e visualizzazione dei prezzi di acquisto In-App con StoreKit è necessario utilizzare classi di formattazione di Apple per le informazioni sui prezzi restituiti.

Le impostazioni locali correnti è possibile eseguire query con uno dei due modi:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Il primo valore può essere memorizzati nella cache dal sistema operativo e pertanto potrebbe non riflettere sempre la lingua dell'utente attualmente selezionato. Utilizzare il secondo valore per ottenere le impostazioni locali attualmente selezionate.

> [!NOTE]
> Mono (runtime di .NET su cui si basa xamarin. IOS) e API di iOS di Apple non supportano i set identico di combinazioni lingua/area geografica.
> Per questo motivo, è possibile selezionare una combinazione di lingua/area geografica in iOS **impostazioni** app che non esegue il mapping a un valore valido in Mono. Ad esempio, impostazione della lingua di un iPhone su inglese e relativa area per la Spagna determinerà le API seguenti restituire valori diversi:
>
> - `CurrentThead.CurrentCulture`: en-US (API di Mono)
> - `CurrentThread.CurrentUICulture`: en-US (API di Mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API Apple)
>
> Poiché Usa Mono `CurrentThread.CurrentUICulture` per selezionare le risorse e `CurrentThread.CurrentCulture` per formattare date e valute, localizzazione basato su Mono (ad esempio, con estensione resx) potrebbe non produrre i risultati previsti per tali combinazioni lingua/area geografica. In questi casi, si basano su API di Apple per localizzare base alle esigenze.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera una `NSCurrentLocaleDidChangeNotification` quando l'utente non aggiorna le impostazioni locali. Le applicazioni possono restare in ascolto su questa notifica mentre è in esecuzione e può apportare le modifiche appropriate all'interfaccia utente.

## <a name="localization-basics-in-ios"></a>Nozioni fondamentali di localizzazione in iOS

Facilmente offerte le caratteristiche seguenti di iOS in Xamarin per fornire le risorse localizzate per la visualizzazione all'utente. Vedere le [TaskyL10n esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) per informazioni su come implementare queste idee.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Specifica impostazione predefinita e lingue supportate in Info. plist

In [risposte tecniche un QA1828: Modalità di determinazione di lingua per l'App iOS](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple descrive la modalità di selezione di un linguaggio da usare in un'app iOS. I fattori seguenti influiscono sulla quale lingua viene visualizzata:

- Lingue preferite dell'utente (trovato nel **impostazioni** app)
- Le versioni localizzate in bundle con l'app (.lproj cartelle)
- `CFBundleDevelopmentRegion` (**Info. plist** valore che specifica la lingua predefinita per l'app)
- `CFBundleLocalizations` (**Info. plist** matrice specificando localizzazioni tutte supportate)

Come indicato nelle domande tecniche e risposte, `CFBundleDevelopmentRegion` rappresenta l'area predefinita e linguaggio dell'app. Se l'app non supporta in modo esplicito a uno qualsiasi dei linguaggi Preferiti dell'utente, userà la lingua specificata da questo campo.

> [!IMPORTANT]
> iOS 11 si applica questo meccanismo di selezione del linguaggio in modo più restrittivo rispetto a versioni precedenti del sistema operativo. Per questo motivo, tutte le app che non dichiara in modo esplicito le localizzazioni supportati: incluse le cartelle .lproj o impostando un valore per iOS 11 `CFBundleLocalizations` : può visualizzare una lingua diversa in iOS 11 rispetto a quanto accadeva in iOS 10.

Se `CFBundleDevelopmentRegion` non è stata specificata la **Info. plist** file, gli strumenti di compilazione xamarin. IOS usano attualmente un valore predefinito di `en_US`. Anche se ciò potrebbe cambiare nelle versioni future, significa che la lingua predefinita è in lingua inglese.

Per assicurarsi che l'app consente di selezionare un linguaggio previsto, procedere come segue:

- Specificare una lingua predefinita. Aprire **Info. plist** e utilizzare il **origine** vista per impostare un valore per il `CFBundleDevelopmentRegion` chiave; in XML, dovrebbe essere simile al seguente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Questo esempio Usa "es" per specificare che, quando nessuno di un utente preferito sono supportati linguaggi, per impostazione predefinita spagnolo.

- Dichiarare localizzazioni tutte supportate. Nella **Info. plist**, utilizzare il **origine** visualizzazione di una matrice per impostare il `CFBundleLocalizations` chiave; in XML, dovrebbe essere simile al seguente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Le app xamarin. IOS che sono state localizzate usando i meccanismi di .NET, ad esempio file con estensione resx devono fornire questi **Info. plist** anche i valori.

Per altre informazioni su questi **Info. plist** le chiavi, ottenere un quadro di Apple [informazioni di riferimento sulle chiavi elenco proprietà](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="getlocalizedstring-method"></a>Metodo GetLocalizedString

Il `NSBundle.MainBundle.GetLocalizedString` metodo cerca il testo localizzato che è stato archiviato nel **.strings** file nel progetto. Questi file sono organizzati per linguaggio, nelle directory appositamente denominata con un **.lproj** suffisso (si noti la prima lettera dell'estensione è una "L" minuscola).

#### <a name="strings-file-locations"></a>percorsi dei file .strings

- **Base.lproj** è la directory che contiene le risorse per la lingua predefinita.
  Spesso si trova nella radice del progetto (ma può anche essere inserito nel **risorse** cartella).
- **&lt;linguaggio&gt;.lproj** directory vengono create per ogni lingua supportata, in genere il **risorse** cartella.

Può esistere una serie di diversi **.strings** file nella directory ogni linguaggio:

- **Localizable.Strings** – elenco principale di testo localizzato.
- **InfoPlist.strings** : determinate chiavi specifiche sono consentite in questo file per tradurre le cose, ad esempio il nome dell'applicazione.
- **< storyboard-name > .strings** : file facoltativo che contiene le traduzioni per gli elementi dell'interfaccia utente in uno storyboard.

Il **Build Action** per questi file devono essere **Bundle di risorse**.

#### <a name="strings-file-format"></a>formato di file .strings

La sintassi per i valori stringa localizzata è:

```console
/* comment */
"key"="localized-value";
```

È consigliabile eseguire l'escape i seguenti caratteri nelle stringhe:

* `\"` offerta
* `\\` barra rovesciata
* `\n` carattere di nuova riga

In questo esempio viene illustrato **es/Localizable.strings** (ad esempio, File spagnolo) dall'esempio:

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

2. Inserire il file di immagine predefinito **flag.png** nelle **Base.lproj** (la directory di linguaggio di sviluppo nativo).

3. Se lo si desidera posizionare le versioni localizzate dell'immagine nel **.lproj** cartelle per ogni lingua (ad es. **es.lproj**, **ja.lproj**). Usare lo stesso nome file **flag.png** in ogni directory di linguaggio.

Se un'immagine non è presente per una determinata lingua, iOS verrà fallback nella cartella della lingua predefinita e caricare l'immagine da tale posizione.

#### <a name="launch-images"></a>Le immagini di avvio

Utilizzano convenzioni di denominazione standard per le immagini di avvio (e il file XIB e Storyboard per i modelli di iPhone 6) durante il posizionamento nel **.lproj** directory per ciascuna lingua.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome dell'App

Inserimento di un **InfoPlist.strings** del file in un **.lproj** directory consente di eseguire l'override di alcuni valori dell'app **Info. plist**, inclusi il nome dell'applicazione:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Altre chiavi che è possibile usare per [localizzare le stringhe specifiche dell'applicazione](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) sono:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Date e ore

Sebbene sia possibile usare le funzioni predefinite di data e ora .NET (insieme a corrente `CultureInfo`) per formattare date e ore per impostazioni locali, questo ignora le impostazioni locali specifiche impostazioni utente (che possono essere impostate separatamente dal linguaggio).

Usare iOS `NSDateFormatter` per produrre l'output corrispondente alle preferenze delle impostazioni locali dell'utente. Esempio di codice seguente viene illustrata la data di base e l'ora le opzioni di formattazione:

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

Risultati per la lingua inglese negli Stati Uniti:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Risultati per spagnolo della Spagna:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Fare riferimento a Apple [formattatori data](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) per altre informazioni. Durante il test locali formattazione di data e ora, controllare entrambi **iPhone Language** e **area** impostazioni.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Layout di destra a sinistra (RTL)

iOS offre una serie di funzionalità per semplificare la creazione di App con riconoscimento da destra a sinistra:

- Layout di uso automatico `leading` e `trailing` attributi per l'allineamento del controllo (che corrisponde a sinistra e destra per la lingua inglese, ma viene invertito per le lingue RTL).
  Il [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) controllo risulta particolarmente utile per disporre i controlli da tenere presenti da destra a sinistra.
- Usare `TextAlignment = UITextAlignment.Natural` per l'allineamento del testo (che verrà lasciato per la maggior parte dei linguaggi, ma a destra per da destra a sinistra).
- `UINavigationController` Capovolge il pulsante Indietro e scorrere rapidamente direzione viene invertita automaticamente.

Gli screenshot seguenti viene illustrato il [localizzata esempio Tasky](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo ed ebraico (sebbene inglese inserito nei campi):

[![](images/rtl-ar-sml.png "Localizzazione in arabo")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localizzazione in ebraico")](images/rtl-he.png#lightbox "Hebrew")

iOS viene invertita automaticamente il `UINavigationController`, e gli altri controlli vengono posizionati all'interno di `UIStackView` o allineate con Layout automatico.
Testo da destra a sinistra è localizzato usando **.strings** file nello stesso modo come testo di conservazione a lungo termine.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizzazione dell'interfaccia utente nel codice

Il [Tasky (localizzato in codice)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) esempio mostra come localizzare un'applicazione in cui l'interfaccia utente viene compilato nel codice (anziché i file Xib o gli storyboard).

### <a name="project-structure"></a>Struttura del progetto

![](images/solution-code.png "Albero delle risorse")

### <a name="localizablestrings-file"></a>File Localizable.Strings

Come descritto in precedenza, il **Localizable.strings** formato di file è costituito da coppie chiave-valore. La chiave viene descritto lo scopo della stringa e il valore è il testo tradotto da usare nell'app.

Il gruppo spagnolo (**es**) localizzazioni per il codice di esempio sono illustrate di seguito:

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

### <a name="performing-the-localization"></a>Esegue la localizzazione

Nel codice dell'applicazione, ogni volta che il testo di visualizzazione dell'interfaccia utente è impostato (si tratti di testo dell'etichetta, o segnaposto dell'input e così via) il codice Usa iOS `GetLocalizedString` funzione per recuperare la conversione corretta da visualizzare:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizzazione di interfacce utente di storyboard

L'esempio [Tasky (storyboard localizzato)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) Mostra come localizzare il testo nei controlli in uno storyboard.

### <a name="project-structure"></a>Struttura del progetto

Il **Base.lproj** directory contiene lo storyboard e deve contenere anche eventuali immagini usate nell'applicazione.

Contengono le altre directory lingua una **Localizable.strings** file per tutte le risorse stringa cui viene fatto riferimento nel codice, nonché una **MainStoryboard.strings** file che contiene le traduzioni per il testo di storyboard.

![](images/solution-storyboard.png "Albero delle risorse")

Le directory di linguaggio devono contenere una copia di tutte le immagini che sono state localizzate, per eseguire l'override di quello presente nella **Base.lproj**.

### <a name="object-id--localization-id"></a>ID di oggetto / ID di localizzazione

Durante la creazione e modifica dei controlli in uno storyboard, selezionare ogni controllo e controllare l'ID da usare per la localizzazione:

- In Visual Studio per Mac, si trova nel **riquadro delle proprietà** e viene chiamato **ID di localizzazione**.
- In Xcode, bensì **ID di oggetto**.

Questo valore di stringa ha spesso un form, ad esempio "NF3 h8 xmR", come illustrato nello screenshot seguente:

![](images/xs-designer-localization-id.png "Visualizzazione di Xcode della localizzazione di uno Storyboard")

Questo valore viene utilizzato il **.strings** file per assegnare testo tradotto automaticamente per ogni controllo.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

Il formato del file di traduzione storyboard è simile al **Localizable.strings** del file, ad eccezione del fatto che la chiave (il valore a sinistra) non può essere definito dall'utente, ma invece deve avere un formato molto specifico: `ObjectID.property`.

Nell'esempio **Mainstoryboard.strings** di seguito sono illustrate `UITextField`hanno una `placeholder` proprietà text che possono essere localizzati. `UILabel`hanno un `text` proprietà; e `UIButton`testo predefinito s viene impostato utilizzando `normalTitle`:

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
> Utilizzare uno storyboard con classi di dimensioni può comportare traduzioni che non vengono visualizzati nell'applicazione. [Note sulla versione di Apple Xcode](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indicano che dello storyboard o del file XIB non localizzare in modo corretto se tre operazioni sono vere: Usa le classi di dimensioni, la localizzazione di base e la destinazione build vengono impostati su Universal e la compilazione destinata a iOS è 7.0. La correzione consiste nel duplicare il file storyboard, le stringhe in due file identici: **MainStoryboard~iphone.strings** e **MainStoryboard~ipad.strings**, come illustrato nello screenshot seguente:
>
> ![](images/xs-dup-strings.png "File di stringhe")

<a name="appstore" />

## <a name="app-store-listing"></a>Elenco di App Store

Segue domande frequenti di Apple [localizzazione dell'App Store](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) per immettere le traduzioni per ogni paese l'app è in vendita. Tenere presente gli avviso indicante che le traduzioni verranno visualizzati solo se l'app contiene anche un localizzata **.lproj** directory per la lingua.

## <a name="summary"></a>Riepilogo

Questo articolo tratta i fondamenti della localizzazione di applicazioni iOS usando le funzionalità di gestione e storyboard di risorse predefiniti.

Altre informazioni su i18n e L10n per le app iOS, Android e lo sviluppo multipiattaforma (xamarin. Forms inclusi) in [questa guida per lo sviluppo multipiattaforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato in codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (storyboard localizzato) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guida di localizzazione di Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Panoramica sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localizzazione in Android](~/android/app-fundamentals/localization.md)
