---
title: Localizzazione in Novell. iOS
description: Questo documento descrive le funzionalità di localizzazione di iOS e come usare queste funzionalità nelle app Novell. iOS. Viene illustrato il linguaggio, le impostazioni locali, i file di stringhe, le immagini di avvio e altro ancora.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: fc67c7f683b6c55d3b4f552c2f8c113ee721be61
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009799"
---
# <a name="localization-in-xamarinios"></a>Localizzazione in Novell. iOS

_Questo documento illustra le funzionalità di localizzazione di iOS SDK e come accedervi con Novell._

Per istruzioni su come includere set di caratteri o tabelle codici nelle applicazioni che devono elaborare dati non Unicode, vedere le [codifiche di internazionalizzazione](encodings.md) .

## <a name="ios-platform-features"></a>funzionalità della piattaforma iOS

Questa sezione descrive alcune delle funzionalità di localizzazione di iOS. Passare alla [sezione successiva](#localization-basics-in-ios) per visualizzare codice ed esempi specifici.

### <a name="language"></a>Language

Gli utenti scelgono la lingua nell'app **Impostazioni** . Questa impostazione influiscono sulle stringhe e sulle immagini della lingua visualizzate dal sistema operativo e dalle app.

Per determinare la lingua da usare in un'app, ottenere il primo elemento di `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Questo valore sarà un codice di lingua, ad esempio `en` per l'inglese, `es` per lo spagnolo, `ja` per il giapponese e così via. Il valore restituito è limitato a una delle localizzazioni supportate dall'applicazione, usando le regole di fallback per determinare la migliore corrispondenza.

Il codice dell'applicazione non è sempre in grado di verificare la presenza di questo valore: Novell e iOS forniscono funzionalità che consentono di fornire automaticamente la stringa o la risorsa corretta per la lingua dell'utente. Queste funzionalità sono descritte nella parte restante di questo documento.

> [!NOTE]
> Usare `NSLocale.PreferredLanguages` per determinare le preferenze di lingua dell'utente, indipendentemente dalle localizzazioni supportate dall'app. I valori restituiti da questo metodo sono stati modificati in iOS 9; per informazioni dettagliate, vedere la [Nota tecnica TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) .

### <a name="locale"></a>Impostazioni locali

Gli utenti scelgono le impostazioni locali nell'app **Impostazioni** . Questa impostazione influiscono sulla modalità di formattazione di date, ore, numeri e valuta.

In questo modo gli utenti possono scegliere se visualizzare i formati di 12 ore o 24 ore, se il separatore decimale è una virgola o un punto e l'ordine del giorno, del mese e dell'anno nella visualizzazione della data.

Con Novell è possibile accedere alle classi iOS di Apple (`NSNumberFormatter`) e alle classi .NET in System. Globalization. Gli sviluppatori dovrebbero valutare quale sia la soluzione più adatta alle proprie esigenze, in quanto sono disponibili diverse funzionalità. In particolare, se si stanno recuperando e visualizzando i prezzi di acquisto in-app usando StoreKit, è consigliabile usare le classi di formattazione di Apple per le informazioni sui prezzi restituite.

Le impostazioni locali correnti possono essere sottoposte a query in uno dei due modi seguenti:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

Il primo valore può essere memorizzato nella cache dal sistema operativo e pertanto potrebbe non rispecchiare sempre le impostazioni locali attualmente selezionate dall'utente. Utilizzare il secondo valore per ottenere le impostazioni locali attualmente selezionate.

> [!NOTE]
> Mono (il Runtime .NET su cui si basa Novell. iOS) e le API iOS di Apple non supportano set identici di combinazioni di lingua/area geografica.
> Per questo motivo, è possibile selezionare una combinazione lingua/area nell'app **Impostazioni** iOS che non esegue il mapping a un valore valido in mono. Se ad esempio si imposta la lingua di un iPhone sull'inglese e la relativa area in Spagna, le API seguenti restituiranno valori diversi:
>
> - `CurrentThead.CurrentCulture`: en-US (API mono)
> - `CurrentThread.CurrentUICulture`: en-US (API mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API Apple)
>
> Poiché mono USA `CurrentThread.CurrentUICulture` per selezionare le risorse e `CurrentThread.CurrentCulture` per formattare date e valute, la localizzazione basata su mono (ad esempio, con i file resx) potrebbe non restituire i risultati previsti per queste combinazioni di lingua/area geografica. In queste situazioni, utilizzare le API di Apple per localizzare in base alle esigenze.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera un `NSCurrentLocaleDidChangeNotification` quando l'utente aggiorna le impostazioni locali. Le applicazioni possono restare in ascolto di questa notifica mentre sono in esecuzione e apportare le modifiche appropriate all'interfaccia utente.

## <a name="localization-basics-in-ios"></a>Nozioni fondamentali sulla localizzazione in iOS

Le funzionalità seguenti di iOS sono facilmente sfruttabili in Novell per fornire le risorse localizzate da visualizzare all'utente. Vedere l' [esempio TaskyL10n](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) per informazioni su come implementare queste idee.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Specifica di linguaggi predefiniti e supportati in info. plist

In [Technical Q & a QA1828: in che modo iOS determina la lingua per l'app](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple descrive in che modo iOS seleziona una lingua da usare in un'app. I fattori seguenti influiscono sulla lingua visualizzata:

- Lingue preferite dell'utente (disponibili nell'app **Impostazioni** )
- Le localizzazioni in bundle con l'app (cartelle con estensione LPROJ)
- `CFBundleDevelopmentRegion` (valore**info. plist** che specifica la lingua predefinita per l'app)
- `CFBundleLocalizations` (array**info. plist** che specifica tutte le localizzazioni supportate)

Come indicato in Technical Q & A, `CFBundleDevelopmentRegion` rappresenta l'area e la lingua predefinite di un'app. Se l'app non supporta in modo esplicito le lingue preferite di un utente, userà la lingua specificata da questo campo.

> [!IMPORTANT]
> iOS 11 applica questo meccanismo di selezione della lingua in modo più rigoroso rispetto alle versioni precedenti del sistema operativo. Per questo motivo, qualsiasi app iOS 11 che non dichiara in modo esplicito le proprie localizzazioni supportate, incluse le cartelle. lproj o l'impostazione di un valore per `CFBundleLocalizations`, può visualizzare una lingua diversa in iOS 11 rispetto a quella eseguita in iOS 10.

Se `CFBundleDevelopmentRegion` non è stato specificato nel file **info. plist** , gli strumenti di compilazione Novell. iOS utilizzano attualmente il valore predefinito `en_US`. Sebbene ciò possa cambiare in una versione futura, significa che la lingua predefinita è l'inglese.

Per assicurarsi che l'app selezioni una lingua prevista, seguire questa procedura:

- Specificare una lingua predefinita. Aprire **info. plist** e usare la visualizzazione **origine** per impostare un valore per la chiave di `CFBundleDevelopmentRegion`; in XML dovrebbe essere simile al seguente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Questo esempio USA "es" per specificare che, quando nessuna delle lingue preferite di un utente è supportata, il valore predefinito è spagnolo.

- Dichiarare tutte le localizzazioni supportate. In **info. plist**usare la visualizzazione **origine** per impostare una matrice per la chiave di `CFBundleLocalizations`; in XML dovrebbe essere simile al seguente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Le app Novell. iOS che sono state localizzate con i meccanismi .NET, ad esempio i file con estensione resx, devono fornire anche questi valori **info. plist** .

Per altre informazioni su queste chiavi **info. plist** , vedere la Guida di riferimento alla [chiave dell'elenco di proprietà delle informazioni](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)di Apple.

### <a name="getlocalizedstring-method"></a>Metodo GetLocalizedString

Il metodo `NSBundle.MainBundle.GetLocalizedString` Cerca il testo localizzato archiviato nei file con **estensione Strings** del progetto. Questi file sono organizzati in base al linguaggio, in directory denominate con un suffisso **. lproj** (si noti che la prima lettera dell'estensione è un minuscolo "L").

#### <a name="strings-file-locations"></a>percorsi file. Strings

- **Base. lproj** è la directory che contiene le risorse per la lingua predefinita.
  Si trova spesso nella radice del progetto (ma può anche essere inserito nella cartella **Resources** ).
- vengono create le directory **&lt;language&gt;. lproj** per ogni lingua supportata, in genere nella cartella **Resources** .

In ogni directory della lingua può essere presente un numero di file con **estensione stringhe** diversi:

- **Localizzable. Strings** : elenco principale del testo localizzato.
- **InfoPlist. Strings** : alcune chiavi specifiche sono consentite in questo file per tradurre elementi come il nome dell'applicazione.
- **\<storyboard-name >. Strings** : file facoltativo che contiene le traduzioni per gli elementi dell'interfaccia utente in uno storyboard.

L' **azione di compilazione** per questi file deve essere una **risorsa di bundle**.

#### <a name="strings-file-format"></a>formato file. Strings

La sintassi per i valori stringa localizzati è:

```console
/* comment */
"key"="localized-value";
```

È consigliabile evitare l'escape dei caratteri seguenti nelle stringhe:

- `\"` virgolette
- barra rovesciata `\\`
- `\n` nuova riga

Si tratta di un esempio **es/Localize. Strings** (IE. Spagnolo) file dall'esempio:

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

1. Vedere l'immagine nel codice, ad esempio:

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. Inserire il file di immagine predefinito **flag. png** in **base. lproj** (directory nativa del linguaggio di sviluppo).

3. Facoltativamente, inserire le versioni localizzate dell'immagine in cartelle **. lproj** per ogni lingua, ad esempio **es. lproj**, **ja. lproj**). Usare lo stesso nome file **flag. png** in ogni directory della lingua.

Se un'immagine non è presente per una lingua specifica, iOS eseguirà il fallback alla cartella predefinita della lingua nativa e caricherà l'immagine da questa posizione.

#### <a name="launch-images"></a>Immagini di avvio

Usare le convenzioni di denominazione standard per le immagini di avvio (e il XIB o lo storyboard per i modelli iPhone 6) quando vengono posizionate nelle directory **. lproj** per ogni lingua.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome dell'app

L'inserimento di un file **InfoPlist. Strings** in una directory **. lproj** consente di eseguire l'override di alcuni valori del file **info. plist**dell'app, incluso il nome dell'applicazione:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Altre chiavi che è possibile usare per [localizzare stringhe specifiche dell'applicazione](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) sono:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Date e ore

Sebbene sia possibile utilizzare le funzioni di data e ora .NET predefinite (insieme alla `CultureInfo`corrente) per formattare date e ore per le impostazioni locali, questo ignora le impostazioni utente specifiche delle impostazioni locali (che possono essere impostate separatamente dalla lingua).

Usare il `NSDateFormatter` iOS per produrre un output corrispondente alle preferenze locali dell'utente. Il codice di esempio seguente illustra le opzioni di formattazione di data e ora di base:

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

Risultati per la lingua inglese nel Stati Uniti:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Risultati per lo spagnolo in Spagna:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Per ulteriori informazioni, fare riferimento alla documentazione dei [formattatori data](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) di Apple. Quando si esegue il test della formattazione di data e ora dipendente dalle impostazioni locali, controllare le impostazioni relative alla lingua e all' **area** di **iPhone** .

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Layout da destra a sinistra (RTL)

iOS offre diverse funzionalità per semplificare la creazione di app compatibili con RTL:

- Usare gli attributi di `leading` e `trailing` del layout automatico per l'allineamento del controllo (che corrisponde a Left e Right per la lingua inglese, ma è invertito per le lingue RTL).
  Il controllo [`UIStackView`](~/ios/user-interface/controls/uistackview.md) è particolarmente utile per il layout dei controlli in modo che siano compatibili con RTL.
- Usare `TextAlignment = UITextAlignment.Natural` per l'allineamento del testo, che verrà lasciato per la maggior parte delle lingue ma per la destra per RTL.
- `UINavigationController` capovolge automaticamente il pulsante indietro e inverte la direzione di scorrimento.

Gli screenshot seguenti mostrano l' [esempio di attività localizzato](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo ed ebraico (sebbene sia stata immessa la lingua inglese nei campi):

[![](images/rtl-ar-sml.png "Localization in Arabic")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localization in Hebrew")](images/rtl-he.png#lightbox "Hebrew")

iOS inverte automaticamente il `UINavigationController`e gli altri controlli vengono inseriti all'interno `UIStackView` o allineati con il layout automatico.
Il testo RTL viene localizzato usando i file con **estensione Strings** nello stesso modo in cui è il testo ltr.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizzazione dell'interfaccia utente nel codice

L'esempio [Tasky (localizzato nel codice)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) Mostra come localizzare un'applicazione in cui l'interfaccia utente è compilata in codice (anziché XIB o Storyboard).

### <a name="project-structure"></a>Struttura del progetto

![](images/solution-code.png "Resources tree")

### <a name="localizablestrings-file"></a>File. Strings localizzabile

Come descritto in precedenza, il formato di file con **estensione Strings localizzabile** è costituito da coppie chiave-valore. La chiave descrive lo scopo della stringa e il valore è il testo tradotto da usare nell'app.

Di seguito sono elencate le localizzazioni in spagnolo (**es**) per l'esempio:

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

### <a name="performing-the-localization"></a>Esecuzione della localizzazione

Nel codice dell'applicazione, ogni volta che viene impostato il testo visualizzato di un'interfaccia utente, che si tratti di un testo di un'etichetta o di un segnaposto di input e così via, il codice usa la funzione `GetLocalizedString` iOS per recuperare la traduzione corretta da visualizzare:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizzazione di interfacce utente storyboard

L'attività di esempio [(Storyboard localizzato)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) Mostra come localizzare il testo nei controlli in uno storyboard.

### <a name="project-structure"></a>Struttura del progetto

La directory **base. lproj** contiene lo storyboard e deve contenere anche le immagini usate nell'applicazione.

Le altre directory della lingua contengono un file **. Strings localizzabile** per tutte le risorse di stringa a cui viene fatto riferimento nel codice, nonché un file **file mainstoryboard. Strings** che contiene le traduzioni per il testo nello storyboard.

![](images/solution-storyboard.png "Resources tree")

Le directory della lingua devono contenere una copia di tutte le immagini che sono state localizzate, per eseguire l'override di quella presente in **base. lproj**.

### <a name="object-id--localization-id"></a>ID oggetto/ID localizzazione

Quando si creano e si modificano i controlli in uno storyboard, selezionare ogni controllo e verificare l'ID da usare per la localizzazione:

- In Visual Studio per Mac, si trova nella **riquadro delle proprietà** ed è denominato ID di **localizzazione**.
- In Xcode è denominato **ID oggetto**.

Questo valore di stringa spesso ha un formato, ad esempio "NF3-h8-xmR", come illustrato nello screenshot seguente:

![](images/xs-designer-localization-id.png "Xcode view of Storyboard localization")

Questo valore viene utilizzato nel file con **estensione Strings** per assegnare automaticamente il testo tradotto a ogni controllo.

### <a name="mainstoryboardstrings"></a>File mainstoryboard. Strings

Il formato del file di traduzione storyboard è simile al file **. Strings localizzabile** , ad eccezione del fatto che la chiave (il valore a sinistra) non può essere definita dall'utente, ma deve avere un formato molto specifico: `ObjectID.property`.

Nell'esempio **file mainstoryboard. Strings** riportato di seguito è possibile vedere `UITextField`s hanno una proprietà `placeholder` text che può essere localizzata. `UILabel`s hanno una proprietà `text`; viene impostato il testo predefinito `UIButton`s utilizzando `normalTitle`:

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
> L'uso di uno storyboard con le classi di dimensioni può comportare traduzioni che non vengono visualizzate nell'applicazione. Le [Note sulla versione di Apple Xcode](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indicano che uno storyboard o XIB non verrà localizzato correttamente se si verificano tre situazioni: usa classi di dimensioni, la localizzazione di base e la destinazione di compilazione sono impostate su universale e la build è destinata a iOS 7,0. La correzione consiste nel duplicare il file delle stringhe storyboard in due file identici: **file mainstoryboard ~ iPhone. Strings** e **file mainstoryboard ~ iPad. Strings**, come illustrato nello screenshot seguente:
>
> ![](images/xs-dup-strings.png "Strings files")

<a name="appstore" />

## <a name="app-store-listing"></a>Elenco di App Store

Segue le domande frequenti di Apple sulla [localizzazione dell'app Store](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) per immettere le traduzioni per ogni paese in cui è in vendita l'app. Si noti che l'avviso indica che le traduzioni verranno visualizzate solo se l'app contiene anche una directory **. lproj** localizzata per la lingua.

## <a name="summary"></a>Riepilogo

Questo articolo illustra le nozioni di base della localizzazione di applicazioni iOS usando le funzionalità predefinite di gestione delle risorse e storyboard.

È possibile ottenere altre informazioni su i18n e l10n per app iOS, Android e multipiattaforma (incluso Novell. Forms) in [questa guida multipiattaforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato nel codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (Storyboard localizzato) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guida alla localizzazione di Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Panoramica della localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di Novell. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localizzazione in Android](~/android/app-fundamentals/localization.md)
