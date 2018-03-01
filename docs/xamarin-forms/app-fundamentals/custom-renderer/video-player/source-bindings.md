---
title: Associazione origini video al lettore
ms.topic: article
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: d0842a54f725e5a9504668f977ba06648a96ee6d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-video-sources-to-the-player"></a>Associazione origini video al lettore

Quando il `Source` proprietà del `VideoPlayer` visualizzazione è impostata su un nuovo file video, il video esistente viene interrotta la riproduzione e inizia il video di nuovo. Ciò viene dimostrata la **selezionare Video Web** pagina del [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) esempio. La pagina include un `ListView` con i titoli dei tre video a cui fa riferimento il **app** file:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        
        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Quando si seleziona un video, il `ItemSelected` esecuzione del gestore eventi nel file code-behind. Il gestore eventuali spazi vuoti e apostrofi rimuove il titolo che verrà usata come una chiave per ottenere una delle risorse definite nel **app** file. Che `UriVideoSource` oggetto viene impostato il `Source` proprietà del `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Quando il primo caricamento della pagina, in è stato selezionato alcun elemento di `ListView`, pertanto è necessario selezionarne una per il video iniziare la riproduzione:

[![Selezionare Video Web](source-bindings-images/selectwebvideo-small.png "selezionare Web Video")](source-bindings-images/selectwebvideo-large.png "selezionare Web Video")

Il `Source` proprietà `VideoPlayer` è supportato da una proprietà associabile, il che significa che può essere la destinazione di un'associazione dati. Ciò viene dimostrata la **associare VideoPlayer** pagina. Il markup di **BindToVideoPlayer.xaml** file è supportata dalla classe seguente che incapsula un titolo di un video e un oggetto corrispondente `VideoSource` oggetto:

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

Il `ListView` nel **BindToVideoPlayer.xaml** file contiene una matrice di questi `VideoInfo` oggetti, ognuno inizializzata con un titolo del video e `UriVideoSource` oggetto dal dizionario risorse in  **App**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Il `Source` proprietà del `VideoPlayer` è associato il `ListView`. Il `Path` dell'associazione è specificato come `SelectedItem.VideoSource`, ovvero un tracciato composto costituita da due proprietà: `SelectedItem` è una proprietà di `ListView`. L'elemento selezionato è di tipo `VideoInfo`, che presenta un `VideoSource` proprietà.

Come con il primo **selezionare Video Web** pagina, è selezionato alcun elemento inizialmente dal `ListView`, pertanto è necessario selezionare uno dei video prima di iniziare la riproduzione.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
