---
title: Localizzazione di stringhe e immagini inXamarin.Forms
description: Xamarin.Formsle app possono essere localizzate usando i file di risorse .NET.
zone_pivot_groups: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af15dc5a23404a11be6207bef7b4fc3e4bf9fad7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137605"
---
# <a name="xamarinforms-string-and-image-localization"></a>Xamarin.FormsLocalizzazione di stringhe e immagini

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

La localizzazione è il processo di adattamento di un'applicazione per soddisfare la lingua o i requisiti culturali specifici di un mercato di destinazione. Per eseguire la localizzazione, potrebbe essere necessario tradurre il testo e le immagini in un'applicazione in più lingue. Un'applicazione localizzata Visualizza automaticamente il testo tradotto in base alle impostazioni cultura del dispositivo mobile:

![Schermate dell'applicazione di localizzazione in iOS e Android](text-images/localizationdemo-screenshots.png)

In .NET Framework è incluso un meccanismo incorporato per la localizzazione di applicazioni tramite [file di risorse RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Un file di risorse archivia testo e altro contenuto come coppie nome/valore che consentono all'applicazione di recuperare il contenuto per una chiave fornita. I file di risorse consentono il contenuto localizzato separato dal codice dell'applicazione.

Per usare i file di risorse per localizzare Xamarin.Forms le applicazioni, è necessario eseguire i passaggi seguenti:

1. [Creare file resx](#create-resx-files) contenenti testo tradotto.
1. Consente di [specificare le impostazioni cultura predefinite](#specify-the-default-culture) nel progetto condiviso.
1. [Localizzare il testo Xamarin.Forms in ](#localize-text-in-xamarinforms).
1. [Localizzare le immagini](#localize-images) in base alle impostazioni cultura per ogni piattaforma.
1. [Localizzare il nome dell'applicazione](#localize-the-application-name) in ogni piattaforma.
1. [Verifica della localizzazione](#test-localization) in ogni piattaforma.

## <a name="create-resx-files"></a>Crea file resx

I file di risorse sono file XML con estensione **resx** compilati in file di risorse binari (con estensione resources) durante il processo di compilazione. Visual Studio 2019 genera una classe che fornisce un'API usata per recuperare le risorse. Un'applicazione localizzata contiene in genere un file di risorse predefinito con tutte le stringhe utilizzate nell'applicazione, nonché i file di risorse per ogni lingua supportata. Nell'applicazione di esempio è presente una cartella **resx** nel progetto condiviso che contiene i file di risorse e il file di risorse predefinito denominato **AppResources. resx**.

I file di risorse contengono le informazioni seguenti per ogni elemento:

- **Nome** specifica la chiave usata per accedere al testo nel codice.
- **Value** specifica il testo tradotto.
- Il **Commento** è un campo facoltativo che contiene informazioni aggiuntive.

::: zone pivot="windows"

Un file di risorse viene aggiunto con la finestra di dialogo **Aggiungi nuovo elemento** in Visual Studio 2019:

![Aggiungere una nuova risorsa in Visual Studio 2019](text-images/pc-add-resource-file.png)

Una volta aggiunto il file, è possibile aggiungere le righe per ogni risorsa di testo:

![Specificare le risorse di testo predefinite in un file con estensione resx](text-images/pc-default-strings.png)

L'impostazione elenco a discesa **modificatore di accesso** determina il modo in cui Visual Studio genera la classe usata per accedere alle risorse. L'impostazione del modificatore di accesso su **public** o **Internal** restituisce una classe generata con il livello di accessibilità specificato. L'impostazione del modificatore di accesso su **Nessuna generazione del codice** non genera un file di classe. Il file di risorse predefinito deve essere configurato per generare un file di classe, il che comporta l'aggiunta di un file con estensione **Designer.cs** al progetto.

Una volta creato il file di risorse predefinito, è possibile creare file aggiuntivi per ogni lingua supportata dall'applicazione. Ogni file di risorse aggiuntivo deve includere le impostazioni cultura di traduzione nel nome file e deve avere il **modificatore di accesso** impostato su **Nessuna generazione di codice**. 

In fase di esecuzione, l'applicazione tenta di risolvere una richiesta di risorse in ordine di specificità. Ad esempio, se le impostazioni cultura del dispositivo sono **en-US** , l'applicazione cerca i file di risorse nell'ordine seguente:

1. AppResources. en-US. resx
1. AppResources. en. resx
1. AppResources. resx (impostazione predefinita)

Lo screenshot seguente mostra un file di traduzione spagnolo denominato **AppResources.es.cs**:

![Specificare le risorse di testo predefinite in un file con estensione resx](text-images/pc-spanish-strings.png)

Il file di traduzione usa gli stessi valori di **nome** specificati nel file predefinito, ma contiene le stringhe della lingua spagnola nella colonna **valore** . Inoltre, il **modificatore di accesso** è impostato su **Nessuna generazione di codice**.

::: zone-end
::: zone pivot="macos"

Un file di risorse viene aggiunto con la finestra di dialogo **Aggiungi nuovo file** in Visual Studio 2019 per Mac:

![Aggiungere una nuova risorsa in Visual Studio 2019 per Mac](text-images/mac-add-resource-file.png)

Una volta creato un file di risorse predefinito, è possibile aggiungere testo creando `data` elementi all'interno dell' `root` elemento nel file di risorse:

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

È possibile creare un file di classe **. designer.cs** impostando una proprietà **dello strumento personalizzata** nelle opzioni del file di risorse:

![Strumento personalizzato specificato nelle proprietà di un file di risorse](text-images/mac-resx-properties.png)

L'impostazione dello **strumento personalizzato** su **PublicResXFileCodeGenerator** comporterà la generazione della classe con `public` accesso. L'impostazione dello **strumento personalizzato** su **InternalResXFileCodeGenerator** comporterà la generazione di una classe generata con `internal` accesso. Un valore di **strumento personalizzato** vuoto non genererà una classe. Il nome della classe generata corrisponderà al nome del file di risorse. Il file **AppResources. resx** , ad esempio, comporterà la creazione di una `AppResources` classe in un file denominato **AppResources.designer.cs**.

È possibile creare file di risorse aggiuntivi per ogni lingua supportata. Ogni file di lingua deve includere le impostazioni cultura di traduzione nel nome file in modo che un file destinato a **es-MX** sia denominato **AppResources.es-MX. resx**.

In fase di esecuzione, l'applicazione tenta di risolvere una richiesta di risorse in ordine di specificità. Ad esempio, se le impostazioni cultura del dispositivo sono **en-US** , l'applicazione cerca i file di risorse nell'ordine seguente:

1. AppResources. en-US. resx
1. AppResources. en. resx
1. AppResources. resx (impostazione predefinita)

I file di traduzione della lingua devono avere gli stessi valori di **nome** specificati come file predefiniti. Nel codice XML seguente viene illustrato il file di traduzione spagnolo denominato **AppResources. es. resx**:

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

Per il corretto funzionamento dei file di risorse, è necessario che l'applicazione disponga di un `NeutralResourcesLanguage` specificato. Nel progetto condiviso, il file **AssemblyInfo.cs** deve essere personalizzato per specificare le impostazioni cultura predefinite. Il codice seguente illustra come impostare `NeutralResourcesLanguage` su **en-US** nel file **AssemblyInfo.cs** :

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Se non si specifica l' `NeutralResourcesLanguage` attributo, la `ResourceManager` classe restituisce `null` i valori per tutte le impostazioni cultura senza un file di risorse specifico. Quando si specificano le impostazioni cultura predefinite, `ResourceManager` restituisce i risultati dal file resx predefinito per le impostazioni cultura non supportate. È pertanto consigliabile specificare sempre `NeutralResourcesLanguage` in modo tale che il testo venga visualizzato per le impostazioni cultura non supportate.

Una volta creato un file di risorse predefinito e le impostazioni cultura predefinite specificate nel file **AssemblyInfo.cs** , l'applicazione può recuperare le stringhe localizzate in fase di esecuzione.

Per altre informazioni sui file di risorse, vedere [creare file di risorse per le app .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="localize-text-in-xamarinforms"></a>Localizzare il testo inXamarin.Forms

Il testo è localizzato in Xamarin.Forms utilizzando la `AppResources` classe generata. Questa classe è denominata in base al nome del file di risorse predefinito. Poiché il file di risorse del progetto di esempio è denominato **AppResources.cs**, Visual Studio genera una classe corrispondente denominata `AppResources` . Le proprietà statiche vengono generate nella `AppResources` classe per ogni riga del file di risorse. Nella classe dell'applicazione di esempio vengono generate le proprietà statiche seguenti `AppResources` :

- AddButton
- NotesLabel
- NotesPlaceholder

L'accesso a questi valori come proprietà [x:static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) consente di visualizzare il testo localizzato in XAML:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

Il testo localizzato può essere recuperato anche nel codice:

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

Le proprietà della `AppResources` classe usano il valore corrente dell'oggetto `System.Globalization.CultureInfo.CurrentUICulture` per determinare il file di risorse delle impostazioni cultura da cui recuperare i valori.

## <a name="localize-images"></a>Localizzare le immagini

Oltre a archiviare il testo, i file resx sono in grado di archiviare più di un solo testo, ma anche di archiviare immagini e dati binari. Tuttavia, i dispositivi mobili hanno una gamma di dimensioni e densità dello schermo e ogni piattaforma mobile dispone di funzionalità per la visualizzazione di immagini dipendenti dalla densità. Pertanto, è consigliabile usare la funzionalità di localizzazione delle immagini della piattaforma anziché archiviare le immagini nei file di risorse.

### <a name="localize-images-on-android"></a>Localizzare immagini in Android

In Android, le drawables localizzate (immagini) vengono archiviate usando una convenzione di denominazione per le cartelle nella directory **Resources** . Le cartelle sono **denominate** con un suffisso per la lingua di destinazione. Ad esempio, la cartella in lingua spagnola è denominata **disegnatore-es**.

Quando è richiesto un codice delle impostazioni locali di quattro lettere, Android richiede un altro **r** dopo il trattino. Ad esempio, la cartella locale del Messico (es-MX) dovrebbe essere denominata **disegnatore-es-rMX**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto Android](text-images/pc-android-images.png)

Per ulteriori informazioni, vedere la pagina relativa alla [localizzazione Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localizzare immagini in iOS

In iOS le immagini localizzate vengono archiviate utilizzando una convenzione di denominazione per le cartelle nella directory **Resources** . La cartella predefinita è denominata **base. lproj**. Le cartelle specifiche della lingua sono denominate con la lingua o il nome delle impostazioni locali, seguito da **. lproj**. Ad esempio, la cartella in lingua spagnola è denominata **es. lproj**.

I codici locali di quattro lettere funzionano esattamente come i codici di lingua di due lettere. Ad esempio, la cartella Mexico locale (es-MX) deve essere denominata **es-MX. lproj**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto iOS](text-images/pc-ios-images.png)

> [!NOTE]
> iOS supporta la creazione di un catalogo asset localizzato anziché usare la struttura di cartelle. lproj. Tuttavia, questi devono essere creati e gestiti in Xcode.

Per ulteriori informazioni, vedere la pagina relativa alla [localizzazione iOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localizzare le immagini in UWP

In UWP le immagini localizzate vengono archiviate utilizzando una convenzione di denominazione per le cartelle nella directory **assets/images** . Le cartelle sono denominate con la lingua o le impostazioni locali. Ad esempio, la cartella in lingua spagnola è denominata **es** e la cartella delle impostazioni locali del Messico deve essere denominata **es-MX**. I nomi dei file di immagine in ogni cartella delle impostazioni locali devono essere identici:

![Immagini localizzate nel progetto UWP](text-images/pc-uwp-images.png)

Per altre informazioni, vedere [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Utilizzare immagini localizzate

Poiché ogni piattaforma archivia immagini con una struttura di file univoca, XAML usa la `OnPlatform` classe per impostare la `ImageSource` Proprietà in base alla piattaforma corrente:

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
> L' `OnPlatform` estensione di markup offre un modo più conciso per specificare valori specifici della piattaforma. Per altre informazioni, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

L'origine dell'immagine può essere impostata in base alla `Device.RuntimePlatform` proprietà nel codice:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localizzare il nome dell'applicazione

Il nome dell'applicazione è specificato per piattaforma e non usa i file di risorse RESX. Per localizzare il nome dell'applicazione in Android, vedere [localizzare il nome dell'app in Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Per localizzare il nome dell'applicazione in iOS, vedere [localizzare il nome dell'app in iOS](~/ios/app-fundamentals/localization/index.md#app-name). Per localizzare il nome dell'applicazione in UWP, vedere [localizzare stringhe nel manifesto del pacchetto UWP](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Localizzazione di test

La localizzazione dei test viene eseguita in modo ottimale cambiando la lingua del dispositivo. È possibile impostare il valore di `System.Globalization.CultureInfo.CurrentUICulture` nel codice, ma il comportamento è incoerente tra le piattaforme, quindi questa operazione non è consigliata per i test.

In iOS nell'app impostazioni è possibile impostare la lingua di ogni app in modo specifico senza modificare la lingua del dispositivo.

In Android le impostazioni della lingua vengono rilevate e memorizzate nella cache all'avvio dell'applicazione. Se si modificano le lingue, potrebbe essere necessario chiudere e riavviare l'applicazione per visualizzare le modifiche applicate.

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di esempio di localizzazione](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Creare i file di risorse per le app .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Uso della classe CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Localizzazione in Android](~/android/app-fundamentals/localization.md)
- [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md)
- [Localizzazione nella piattaforma UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Individuazione e uso di risorse per impostazioni cultura specifiche](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
