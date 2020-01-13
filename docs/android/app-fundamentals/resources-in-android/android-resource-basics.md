---
title: Nozioni di base sulle risorse Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: ac228e6f0c251ae6f0fcabe1be855c6ed4a85d35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025355"
---
# <a name="android-resource-basics"></a>Nozioni di base sulle risorse Android

Quasi tutte le applicazioni Android avranno un certo tipo di risorse. come minimo, spesso i layout dell'interfaccia utente sono disponibili sotto forma di file XML. Quando viene creata per la prima volta un'applicazione Xamarin.Android, le risorse predefinite vengono impostate dal modello di progetto Xamarin.Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File di risorse](android-resource-basics-images/01-resource-files-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![File di risorse](android-resource-basics-images/01-resource-files-xs.png)

-----

I cinque file che costituiscono le risorse predefinite sono stati creati nella cartella Resources:

- **Icon. png** &ndash; icona predefinita per l'applicazione

- **Main. axml** &ndash; il file di layout dell'interfaccia utente predefinito per un'applicazione. Si noti che, mentre Android usa l'estensione di file **XML** , Xamarin.Android usa l'estensione di file **aXML** .

- Strings **. xml** &ndash; una tabella di stringhe per facilitare la localizzazione dell'applicazione

- **AboutResources. txt** &ndash; questa operazione non è necessaria e può essere eliminata in modo sicuro. Fornisce solo una panoramica di alto livello della cartella delle risorse e dei file al suo interno.

- **Resource.designer.cs** &ndash; questo file viene generato e gestito automaticamente da Xamarin.Android e mantiene l'ID univoco assegnato a ogni risorsa. Questa operazione è molto simile e identica allo scopo del file R. Java che un'applicazione Android è stata scritta in Java. Viene creata automaticamente dagli strumenti Xamarin.Android e verrà rigenerata di tanto in tanto.

## <a name="creating-and-accessing-resources"></a>Creazione e accesso alle risorse

La creazione di risorse è semplice quanto l'aggiunta di file alla directory per il tipo di risorsa in questione. Lo screenshot seguente mostra le risorse di stringa per le impostazioni locali tedesche aggiunte a un progetto. Quando **Strings. XML** è stato aggiunto al file, l' **azione di compilazione** è stata impostata automaticamente su **AndroidResource** dagli strumenti di Xamarin.Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Azione di compilazione per Strings. XML impostata su AndroidResource](android-resource-basics-images/02-build-action-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Azione di compilazione per Strings. XML impostata su AndroidResource](android-resource-basics-images/02-build-action-xs.png)

-----

Ciò consente agli strumenti Xamarin.Android di compilare e incorporare correttamente le risorse nel file APK. Se per qualche motivo l' **azione di compilazione** non è impostata su **risorsa Android**, i file verranno esclusi dall'apk e qualsiasi tentativo di caricare o accedere alle risorse comporterà un errore di run-time e l'applicazione si arresterà in modo anomalo.

Inoltre, è importante notare che, mentre Android supporta solo nomi file minuscoli per gli elementi di risorsa, Xamarin.Android è un po' più indulgente; supporta i nomi file sia maiuscoli che minuscoli. La convenzione per i nomi delle immagini prevede l'uso di caratteri minuscoli con caratteri di sottolineatura come separatori (ad esempio, **my\_image\_Name. png**). Si noti che i nomi di risorse non possono essere elaborati se vengono usati trattini o spazi come separatori.

Una volta aggiunte le risorse a un progetto, esistono due modi per usarle in un'applicazione &ndash; a livello di codice (all'interno del codice) o da file XML.

## <a name="referencing-resources-programmatically"></a>Riferimento a risorse a livello di codice

Per accedere a questi file a livello di codice, viene assegnato un ID di risorsa univoco. Questo ID risorsa è un numero intero definito in una classe speciale denominata `Resource`, disponibile nel file **Resource.designer.cs**, e ha un aspetto simile al seguente:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Ogni ID di risorsa è contenuto all'interno di una classe annidata che corrisponde al tipo di risorsa. Ad esempio, quando il file **Icon. png** è stato aggiunto al progetto, Xamarin.Android ha aggiornato la classe `Resource`, creando una classe annidata denominata `Drawable` con una costante all'interno del `Icon`denominato.
Ciò consente di fare riferimento al file **Icon. png** nel codice come `Resource.Drawable.Icon`. La classe `Resource` non deve essere modificata manualmente, perché tutte le modifiche apportate verranno sovrascritte da Xamarin.Android.

Quando si fa riferimento alle risorse a livello di codice (nel codice), è possibile accedervi tramite la gerarchia della classe Resources, che usa la sintassi seguente:

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

- **Packagename** &ndash; il pacchetto che fornisce la risorsa ed è necessario solo quando vengono usate le risorse di altri pacchetti.

- **ResourceType** &ndash; questo è il tipo di risorsa annidato che si trova all'interno della classe di risorse descritta in precedenza.

- **Nome della risorsa** &ndash; questo è il nome file della risorsa (senza estensione) o il valore dell'attributo Android: Name per le risorse presenti in un elemento XML.

## <a name="referencing-resources-from-xml"></a>Riferimento a risorse da XML

Per accedere alle risorse di un file XML, è necessario seguire una sintassi speciale:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

- **Packagename** &ndash; il pacchetto che fornisce la risorsa ed è necessario solo quando vengono usate le risorse di altri pacchetti.

- **ResourceType** &ndash; questo è il tipo di risorsa annidato che si trova all'interno della classe di risorse.

- **Nome della risorsa** &ndash; questo è il nome file della risorsa (*senza* l'estensione del tipo di file) o il valore dell'attributo `android:name` per le risorse presenti in un elemento XML.

Ad esempio, il contenuto di un file di layout, **Main. aXML**, è il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

In questo esempio è presente un [`ImageView`](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) che richiede una risorsa di cui è stato disegnato un **flag**denominato. L'attributo `src` dell'`ImageView` è impostato su `@drawable/flag`. Quando l'attività viene avviata, Android cercherà all'interno della risorsa della directory **/disegnatore** per un file denominato **flag. png** (l'estensione del file potrebbe essere un altro formato di immagine, ad esempio **flag. jpg**) e caricare il file e visualizzarlo nella `ImageView`.
Quando l'applicazione viene eseguita, avrà un aspetto simile all'immagine seguente:

![ImageView localizzato](android-resource-basics-images/03-localized-screenshot.png)
