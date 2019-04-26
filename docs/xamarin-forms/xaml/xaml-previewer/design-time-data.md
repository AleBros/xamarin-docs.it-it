---
title: Usare i dati della fase di progettazione con il Visualizzatore anteprima XAML
description: Questo articolo illustra come usare i dati della fase di progettazione per mostrare i layout con intensa attività di dati nel Visualizzatore anteprima XAML senza eseguire l'app.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 0ff9f8b5ee6f9468650b6535745706bee8f96536
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876355"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usare i dati della fase di progettazione con il Visualizzatore anteprima XAML

_Alcuni layout sono difficili da visualizzare senza dati. Usare questi suggerimenti per sfruttare al meglio la visualizzazione in anteprima le pagine di dati con intensa attività nel Visualizzatore anteprima XAML._

## <a name="design-time-data-basics"></a>Nozioni di base di tempo dei dati di progettazione

Fase i dati sono dati fittizi che è impostato per rendere più semplice visualizzare i controlli nel Visualizzatore anteprima XAML di progettazione. Per iniziare, aggiungere le seguenti righe di codice per l'intestazione della pagina XAML:

```csharp
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Dopo aver aggiunto gli spazi dei nomi, è possibile inserire `d:` davanti a qualsiasi attributo o un controllo per visualizzarlo nel Visualizzatore anteprima XAML. Gli elementi con `d:` non vengono visualizzati in fase di esecuzione.

Ad esempio, è possibile aggiungere testo a un'etichetta che in genere è associata a dati.

```csharp
<Label Text={Binding Name} d:Text="Name" />
```

[![Progettazione di dati della fase con il testo in un'etichetta](xaml-previewer-images/designtimedata-label-sm.png "Design-time di dati con il testo di un'etichetta")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

 In questo esempio, senza `d:Text`, il Visualizzatore anteprima XAML mostrerebbe nothing per l'etichetta. Al contrario, verrà visualizzato "Nome" in cui l'etichetta avranno i dati reali in fase di esecuzione.

È possibile usare `d:` con qualsiasi attributo per un controllo di xamarin. Forms, come i colori, le dimensioni dei caratteri e la spaziatura. È anche possibile aggiungerlo al controllo stesso:

```csharp
<d:Button Text="Design Time Button" />
```

[![Progettare i dati temporali con un controllo Button](xaml-previewer-images/designtimedata-controls-sm.png "dati temporali con un controllo pulsante di progettazione")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

In questo esempio, il pulsante viene visualizzata solo in fase di progettazione. Usare questo metodo per inserire un segnaposto in per un [controllo personalizzato non è supportata per il Visualizzatore anteprima XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Immagini di anteprima in fase di progettazione

È possibile impostare una fase di progettazione origine per le immagini che sono associati alla pagina o caricati in modo dinamico. Nel progetto Android, aggiungere l'immagine da visualizzare nel Visualizzatore anteprima XAML per il **risorse > Drawable** cartella. Nel progetto iOS, aggiungere l'immagine per il **risorse** cartella. È quindi possibile visualizzare tale immagine nel Visualizzatore anteprima XAML in fase di progettazione:

```csharp
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```
[![I dati ora con le immagini di progettazione](xaml-previewer-images/designtimedata-image-sm.png "progettare dati temporali con iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Dati della fase di progettazione per controlli ListView

ListView che rappresentano una modalità comune per visualizzare i dati in un'app per dispositivi mobili. Tuttavia, sono difficili da visualizzare senza dati reali. Per usare i dati della fase di progettazione con essi, è necessario creare una matrice di fase di progettazione da usare come una proprietà ItemsSource. Il Visualizzatore anteprima XAML viene visualizzato ciò che è in tale array nel ListView in fase di progettazione.

```csharp
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Progettare i dati di tempo con un ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "progettare dati temporali con un ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

Questo esempio viene illustrato un ListView di tre TextCells nel Visualizzatore anteprima XAML. È possibile modificare `x:String` a un modello di dati esistente nel progetto.

Fare riferimento a [app Hanselman.Forms di James Montemagno](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L36-L57) per un esempio più complesso.


## <a name="alternative-hardcode-a-static-viewmodel"></a>In alternativa: Impostare come hardcoded un ViewModel statico

Se non si desidera aggiungere i dati della fase di progettazione a singoli controlli, è possibile impostare un archivio dati fittizi per l'associazione a una pagina. Fare riferimento a di James Montemagno [post di blog sull'aggiunta di dati design-time](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) per informazioni su come associare a un elemento ViewModel statiche in XAML.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="requirements"></a>Requisiti

Dati della fase di progettazione richiedono almeno la versione 3.6 di xamarin. Forms.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>In IntelliSense vengono mostrate le righe ondulate sotto i dati di fase di progettazione

Questo è un problema noto e verrà risolto in una versione futura di Visual Studio. Il progetto verrà comunque compilato senza errori.

### <a name="the-xaml-previewer-stopped-working"></a>Il Visualizzatore anteprima XAML ha smesso di funzionare

Provare a chiudere e riaprire il file XAML e la pulizia e ricompilazione del progetto.
