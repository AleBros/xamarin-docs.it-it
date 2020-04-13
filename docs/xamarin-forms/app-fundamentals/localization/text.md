---
title: Localizzazione di stringhe e immagini in Xamarin.FormsString and Image Localization in Xamarin.Forms
description: Le app Xamarin.Forms possono essere localizzate usando i file di risorse .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
ms.openlocfilehash: bf99873d88a69a715cdf7969ad94afd66372b5e3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135389"
---
# <a name="xamarinforms-string-and-image-localization"></a>Xamarin.Forms Stringa e Image Localization

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

La localizzazione è il processo di adattamento di un'applicazione per soddisfare le esigenze linguistiche o culturali specifiche di un mercato di destinazione. Per eseguire la localizzazione, potrebbe essere necessario convertire il testo e le immagini in un'applicazione in più lingue. Un'applicazione localizzata visualizza automaticamente il testo tradotto in base alle impostazioni cultura del dispositivo mobile:

![Screenshot dell'applicazione di localizzazione su iOS e Android](text-images/localizationdemo-screenshots.png)

.NET framework include un meccanismo incorporato per la localizzazione di applicazioni utilizzando i file di [risorse Resx.](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps) Un file di risorse archivia testo e altro contenuto come coppie nome/valore che consentono all'applicazione di recuperare il contenuto per una chiave fornita. I file di risorse consentono di separare il contenuto localizzato dal codice dell'applicazione.

L'utilizzo dei file di risorse per localizzare le applicazioni Xamarin.Forms richiede la procedura seguente:

1. [Creare file Resx contenenti](#create-resx-files) testo tradotto.
1. [Specificare le impostazioni cultura predefinite](#specify-the-default-culture) nel progetto condiviso.
1. [Localizzare il testo in Xamarin.Forms](#localize-text-in-xamarinforms).
1. [Localizzare le immagini](#localize-images) in base alle impostazioni cultura per ogni piattaforma.
1. [Localizzare il nome dell'applicazione](#localize-the-application-name) in ogni piattaforma.
1. [Testare la localizzazione](#test-localization) su ogni piattaforma.

## <a name="create-resx-files"></a>Creazione di file Resx

I file di risorse sono file XML con estensione **resx** compilati in file di risorse binari (con estensione resources) durante il processo di compilazione. Visual Studio 2019 genera una classe che fornisce un'API utilizzata per recuperare le risorse. Un'applicazione localizzata contiene in genere un file di risorse predefinito con tutte le stringhe utilizzate nell'applicazione, nonché file di risorse per ogni lingua supportata. L'applicazione di esempio include una cartella **Resx** nel progetto condiviso che contiene i file di risorse e il relativo file di risorse predefinito denominato **AppResources.resx**.

I file di risorse contengono le informazioni seguenti per ogni elemento:Resource files contain the following information for each item:

- **Name** specifica la chiave utilizzata per accedere al testo nel codice.
- **Valore** specifica il testo tradotto.
- **Commento** è un campo facoltativo contenente informazioni aggiuntive.

::: zone pivot="windows"

Viene aggiunto un file di risorse con la finestra di dialogo Aggiungi nuovo elemento in Visual Studio 2019:A resource file is added with the **Add New Item** dialog in Visual Studio 2019:

![Aggiungere una nuova risorsa in Visual Studio 2019Add a new Resource in Visual Studio 2019](text-images/pc-add-resource-file.png)

Una volta aggiunto il file, è possibile aggiungere righe per ogni risorsa di testo:Once the file is added, rows can be added for each text resource:

![Specificare le risorse di testo predefinite in un file RESXSpecify default text resources in a .resx file](text-images/pc-default-strings.png)

L'impostazione dell'elenco a discesa **Modificatore** di accesso determina il modo in cui Visual Studio genera la classe utilizzata per accedere alle risorse. L'impostazione del modificatore di accesso su **Public** o **Internal** determina una classe generata con il livello di accessibilità specificato. L'impostazione del modificatore di accesso su **Nessuna generazione** di codice non genera un file di classe. Il file di risorse predefinito deve essere configurato per generare un file di classe, che comporta un file con l'estensione **.designer.cs** viene aggiunto al progetto.

Dopo aver creato il file di risorse predefinito, è possibile creare file aggiuntivi per ogni lingua supportata dall'applicazione. Ogni file di risorse aggiuntivo deve includere le impostazioni cultura di conversione nel nome del file e il **modificatore** di accesso deve essere impostato su **Nessuna generazione**di codice . 

In fase di esecuzione, l'applicazione tenta di risolvere una richiesta di risorsa in ordine di specificità. Ad esempio, se le impostazioni cultura del dispositivo sono **en-US,** l'applicazione cerca i file di risorse nell'ordine seguente:For example, if the device culture is en-US the application looks for resource files in this order:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (impostazione predefinita)

La schermata seguente mostra un file di traduzione spagnolo denominato **AppResources.es.cs**:

![Specificare le risorse di testo predefinite in un file RESXSpecify default text resources in a .resx file](text-images/pc-spanish-strings.png)

Il file di traduzione utilizza gli stessi valori **Name** specificati nel file predefinito ma contiene stringhe in lingua spagnola nella colonna **Valore.** Inoltre, il **modificatore** di accesso è impostato su **Nessuna generazione**di codice .

::: zone-end
::: zone pivot="macos"

Viene aggiunto un file di risorse con la finestra di dialogo Aggiungi nuovo file in Visual Studio 2019 per Mac:A resource file is added with the **Add New File** dialog in Visual Studio 2019 for Mac:

![Aggiungere una nuova risorsa in Visual Studio 2019 per MacAdd a new Resource in Visual Studio 2019 for Mac](text-images/mac-add-resource-file.png)

Dopo aver creato un file di risorse predefinito, è possibile aggiungere testo creando elementi all'interno dell'elemento nel file di risorse:Once a default resource file has been created, text can be added by creating `data` elements within the `root` element in the resource file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

È possibile creare un file di classe .designer.cs impostando una proprietà Strumento personalizzato nelle opzioni del file di risorse:A **.designer.cs** class file can be created by setting a **Custom Tool** property in the resource file options:

![Strumento personalizzato specificato nelle proprietà di un file di risorseCustom Tool specified in a resource file's properties](text-images/mac-resx-properties.png)

L'impostazione **dello strumento personalizzato su** **PublicResXFileCodeGenerator** comporterà la generazione della classe con `public` accesso. L'impostazione **dello strumento personalizzato** su **InternalResXFileCodeGenerator** genererà una classe generata con `internal` accesso. Un valore **di strumento personalizzato** vuoto non genererà una classe. Il nome della classe generata corrisponderà al nome del file di risorse. Ad esempio, il file **AppResources.resx** comporterà `AppResources` la creazione di una classe in un file denominato **AppResources.designer.cs**.

È possibile creare file di risorse aggiuntivi per ogni lingua supportata. Ogni file di lingua deve includere le impostazioni cultura di traduzione nel nome del file, pertanto un file destinato a **es-MX** deve essere denominato **AppResources.es-MX.resx**.

In fase di esecuzione, l'applicazione tenta di risolvere una richiesta di risorsa in ordine di specificità. Ad esempio, se le impostazioni cultura del dispositivo sono **en-US,** l'applicazione cerca i file di risorse nell'ordine seguente:For example, if the device culture is en-US the application looks for resource files in this order:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (impostazione predefinita)

I file di traduzione della lingua devono avere gli stessi valori **Name** specificati come file predefinito. Nel codice XML seguente viene illustrato il file di traduzione spagnolo denominato **AppResources.es.resx**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>Specificare le impostazioni cultura predefinite

Affinché i file di risorse funzionino correttamente, è necessario che all'applicazione sia specificato un `NeutralResourcesLanguage` oggetto . Nel progetto condiviso, il **file di AssemblyInfo.cs** deve essere personalizzato per specificare le impostazioni cultura predefinite. Il codice seguente mostra `NeutralResourcesLanguage` come impostare l'oggetto su **en-US** nel file **di AssemblyInfo.cs:**

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Se non si `NeutralResourcesLanguage` specifica l'attributo, la `ResourceManager` classe restituisce `null` valori per le impostazioni cultura senza un file di risorse specifico. Quando vengono specificate le `ResourceManager` impostazioni cultura predefinite, vengono restituiti i risultati dal file Resx predefinito per le impostazioni cultura non supportate. Pertanto, si consiglia di `NeutralResourcesLanguage` specificare sempre il testo in modo che il testo viene visualizzato per le impostazioni cultura non supportate.

Dopo aver creato un file di risorse predefinito e le impostazioni cultura predefinite specificate nel file **AssemblyInfo.cs,** l'applicazione può recuperare le stringhe localizzate in fase di esecuzione.

Per altre informazioni sui file di risorse, vedere Creare file di [risorse per le app .NET.](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)

## <a name="localize-text-in-xamarinforms"></a>Localizzare il testo in Xamarin.Forms

Il testo viene localizzato in Xamarin.Forms utilizzando la classe generata. `AppResources` Questa classe viene denominata in base al nome del file di risorse predefinito. Poiché il file di **AppResources.cs**risorse del progetto di `AppResources`esempio è denominato AppResources.cs , Visual Studio genera una classe corrispondente denominata . Le proprietà statiche `AppResources` vengono generate nella classe per ogni riga nel file di risorse. Le seguenti proprietà statiche vengono generate `AppResources` nella classe dell'applicazione di esempio:The following static properties are generated in the sample application's class:

- AddButton
- NoteEtichetta
- NoteSegnaposto

L'accesso a questi valori come proprietà x:Static consente la visualizzazione di testo localizzato in XAML:Accessing these values as [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) properties allows localized text to be displayed in XAML:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

Il testo localizzato può anche essere recuperato nel codice:Localized text can also be retrieved in code:

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };
    
    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };
    
    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };
    
    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

Le proprietà `AppResources` nella classe utilizzano `System.Globalization.CultureInfo.CurrentUICulture` il valore corrente di per determinare il file di risorse delle impostazioni cultura da cui recuperare i valori.

## <a name="localize-images"></a>Localizzare le immagini

Oltre a memorizzare il testo, i file Resx sono in grado di memorizzare più di un semplice testo, possono anche memorizzare immagini e dati binari. Tuttavia, i dispositivi mobili hanno una gamma di dimensioni e densità dello schermo e ogni piattaforma mobile dispone di funzionalità per la visualizzazione di immagini dipendenti dalla densità. Pertanto, la funzionalità di localizzazione dell'immagine della piattaforma deve essere utilizzata anziché archiviare le immagini nei file di risorse.

### <a name="localize-images-on-android"></a>Localizzare le immagini su Android

In Android, i drawable localizzati (immagini) vengono archiviati usando una convenzione di denominazione per le cartelle nella directory **Resources.** Le cartelle sono **denominate drawable** con un suffisso per la lingua di destinazione. Ad esempio, la cartella in lingua spagnola è denominata **drawable-es**.

Quando è necessario un codice delle impostazioni locali di quattro lettere, Android richiede un **r** aggiuntivo dopo il trattino. Ad esempio, la cartella delle impostazioni locali del Messico (es-MX) deve essere denominata **drawable-es-rMX**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto AndroidLocalized images in the Android project](text-images/pc-android-images.png)

Per ulteriori informazioni, consultate [Localizzazione Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localizzare le immagini su iOSLocalize images on iOS

In iOS, le immagini localizzate vengono archiviate usando una convenzione di denominazione per le cartelle nella directory **Resources.On** iOS, localized images are stored using a naming convention for folders in the Resources directory. La cartella predefinita è denominata **Base.lproj**. Le cartelle specifiche della lingua sono denominate con il nome della lingua o delle impostazioni locali, seguito da **lproj**. Ad esempio, la cartella in lingua spagnola è denominata **es.lproj**.

I codici locali di quattro lettere funzionano proprio come i codici di lingua a due lettere. Ad esempio, la cartella delle impostazioni locali del Messico (es-MX) deve essere denominata **es-MX.lproj**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto iOSLocalized images in the iOS project](text-images/pc-ios-images.png)

> [!NOTE]
> iOS supporta la creazione di un catalogo di asset localizzato anziché utilizzare la struttura di cartelle con estensione lproj.iOS supports creating a localized Asset Catalog instead of using the .lproj folder structure. Tuttavia, questi devono essere creati e gestiti in Xcode.

Per ulteriori informazioni, vedere [localizzazione iOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localizzare le immagini su UWP

Nella piattaforma UWP, le immagini localizzate vengono archiviate utilizzando una convenzione di denominazione per le cartelle nella directory **Assets/Images.** Le cartelle sono denominate con la lingua o le impostazioni locali. Ad esempio, la cartella in lingua spagnola è denominata **es** e la cartella delle impostazioni locali del Messico deve essere denominata **es-MX**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto UWPLocalized images in the UWP project](text-images/pc-uwp-images.png)

Per altre informazioni, vedere [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Usare immagini localizzate

Poiché ogni piattaforma archivia immagini con una `OnPlatform` struttura di `ImageSource` file univoca, il codice XAML usa la classe per impostare la proprietà in base alla piattaforma corrente:Since each platform stores images with a unique file structure, the XAML uses the class to set the property based on the current platform:

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

> [!NOTE]
> L'estensione `OnPlatform` di markup offre un modo più conciso di specificare i valori specifici della piattaforma. Per ulteriori informazioni, vedere [Estensione di markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

L'origine dell'immagine può `Device.RuntimePlatform` essere impostata in base alla proprietà nel codice:The image source can be set based on the property in code:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localizzare il nome dell'applicazioneLocalize the application name

Il nome dell'applicazione viene specificato per piattaforma e non utilizza i file di risorse Resx. Per localizzare il nome dell'applicazione in Android, consultate Localizzare il [nome dell'app in Android.](~/android/app-fundamentals/localization.md#stringsxml-file-format) Per localizzare il nome dell'applicazione in iOS, vedere Localizzare il [nome dell'applicazione in iOS](~/ios/app-fundamentals/localization/index.md#app-name). Per localizzare il nome dell'applicazione in UWP, vedere [Localizzare stringhe nel manifesto del pacchetto UWP](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Verificare la localizzazione

Il test della localizzazione è più adatto alla modifica della lingua del dispositivo. È possibile impostare il `System.Globalization.CultureInfo.CurrentUICulture` valore di nel codice, ma il comportamento è incoerente tra le piattaforme, pertanto questa operazione non è consigliata per il test.

In iOS, nell'app Impostazioni, puoi impostare la lingua per ogni app in modo specifico senza modificare la lingua del dispositivo.

In Android, le impostazioni della lingua vengono rilevate e memorizzate nella cache all'avvio dell'applicazione. Se si cambia noto linguaggio, potrebbe essere necessario chiudere e riavviare l'applicazione per visualizzare le modifiche applicate.

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di esempio di localizzazioneLocalization Sample Project](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Creare i file di risorse per le app .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Uso della classe CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Localizzazione in Android](~/android/app-fundamentals/localization.md)
- [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md)
- [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Individuazione e uso di risorse per impostazioni cultura specifiche](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
