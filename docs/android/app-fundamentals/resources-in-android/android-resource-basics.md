---
title: Concetti fondamentali sulle risorse Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: 2673021fae2f0a0b45761bf4ed619c92fb826b13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110134"
---
# <a name="android-resource-basics"></a>Concetti fondamentali sulle risorse Android

Quasi tutte le Android applicazioni avranno una sorta di risorse. come minimo hanno spesso il layout di interfaccia utente sotto forma di file XML. Quando viene creata un'applicazione xamarin. Android, le risorse predefinite sono configurati dal modello di progetto xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File di risorse](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![File di risorse](android-resource-basics-images/01-resource-files-xs.png)
 
-----

I cinque file che costituiscono le risorse predefinite sono stati creati nella cartella delle risorse:

-  **Icon. PNG** &ndash; l'icona predefinita per l'applicazione

-  **Main. axml** &ndash; il file di layout dell'interfaccia utente predefinite per un'applicazione. Si noti che mentre Android usano la **. XML** estensione di file, xamarin. Android Usa le **axml** estensione di file.

-  **Strings. XML** &ndash; una tabella di stringhe per facilitare la localizzazione dell'applicazione

-  **Aboutresources** &ndash; questo non è necessario e può essere eliminato senza problemi. Fornisce solo una panoramica generale della cartella delle risorse e file in essa.

-  **Resource.Designer.cs** &ndash; questo file viene automaticamente generato e gestito da xamarin. Android e univoco contiene gli ID assegnati a ogni risorsa. Questo è molto simile e scopo identico al file R.java che potrebbe avere un'applicazione Android scritta in Java. Viene automaticamente creato dagli strumenti di xamarin. Android e verrà rigenerato di tanto in tanto.


## <a name="creating-and-accessing-resources"></a>Creazione e l'accesso alle risorse

Creazione di risorse è semplice come aggiungere file alla directory per il tipo di risorsa in questione. Cattura di schermata seguente mostra le risorse stringa per le impostazioni locali tedesche sono stati aggiunti a un progetto. Quando **Strings. XML** è stato aggiunto al file, il **azione di compilazione** automaticamente è stata impostata su **AndroidResource** dagli strumenti di xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Operazione per impostare su AndroidResource Strings. XML di compilazione](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Operazione per impostare su AndroidResource Strings. XML di compilazione](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

In questo modo gli strumenti di xamarin. Android compilare e incorporare le risorse in per il file APK in modo corretto. Se per qualche motivo il **Build Action** non è impostata su **risorse Android**, quindi i file verranno esclusi dall'APK e qualsiasi tentativo di caricare o accedere alle risorse comporterà un errore di run-time e il applicazioni si arresteranno.

Inoltre, è importante notare che, mentre Android supporta solo nomi file lettere minuscole per gli elementi di risorsa, xamarin. Android è un po' più tollerante; supporterà i nomi di file sia lettere maiuscole e minuscole. La convenzione per i nomi delle immagini consiste nell'usare lettere minuscole con caratteri di sottolineatura come separatore (ad esempio, **my\_immagine\_name.png**). Si noti che i nomi delle risorse non possono essere elaborati se trattini né spazi vengono utilizzati come separatori.

Dopo aver aggiunto le risorse a un progetto, esistono due modi per usarli in un'applicazione &ndash; a livello di codice (all'interno di codice) o dai file XML.


## <a name="referencing-resources-programmatically"></a>Riferimento alle risorse a livello di codice

Per accedere ai file a livello di programmazione, vengono assegnati un ID di risorsa univoco. Questo ID risorsa è un numero intero definito in una classe speciale chiamata `Resource`, che si trova nel file **Resource.designer.cs**, e ha un aspetto simile al seguente:

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

Ogni ID di risorsa è contenuta all'interno di una classe annidata che corrisponde al tipo di risorsa. Ad esempio, quando il file **Icon. PNG** è stato aggiunto al progetto xamarin. Android aggiornato il `Resource` (classe), la creazione di una classe annidata denominata `Drawable` all'interno di una costante denominata `Icon`.
In questo modo, il file **Icon. PNG** a fare riferimento nel codice come `Resource.Drawable.Icon`. Il `Resource` classe non deve essere modificata manualmente, come tutte le modifiche vengono apportate a essa verranno sovrascritto da xamarin. Android.

Quando si fa riferimento alle risorse a livello di codice (nel codice), è possibile accedervi tramite la gerarchia di classi di risorse che usa la sintassi seguente:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash; il pacchetto che fornisce la risorsa ed è solo necessario quando vengono utilizzate le risorse da altri pacchetti.

-  **ResourceType** &ndash; questo è il tipo di risorsa annidata che si trova all'interno della classe di risorse descritta in precedenza.

-  **Nome della risorsa** &ndash; questo è il nome del file della risorsa (senza estensione) o il valore dell'attributo android: name per le risorse in un elemento XML.


## <a name="referencing-resources-from-xml"></a>Riferimento alle risorse da XML

Accesso alle risorse in un file XML da un segue una sintassi speciale:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash; il pacchetto che fornisce la risorsa ed è solo necessario quando vengono utilizzate le risorse da altri pacchetti.

-  **ResourceType** &ndash; questo è il tipo di risorsa annidata che si trova all'interno della classe di risorse.

-  **Nome della risorsa** &ndash; si tratta del nome della risorsa (*senza* l'estensione del tipo di file) o il valore della `android:name` attributo per le risorse in un elemento XML.

Ad esempio il contenuto di un file di layout **Main. axml**, sono i seguenti:

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

Questo esempio è costituito da un [ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) che richiede una risorsa drawable denominata **flag**. Il `ImageView` ha relativi `src` attributo impostato su **@drawable/flag**. All'avvio, l'attività Android avrà un aspetto all'interno della directory **risorsa/Drawable** per un file denominato **flag.png** (l'estensione di file potrebbe essere un altro formato di immagine, ad esempio **flag.jpg**) e caricare tale file e lo visualizza nel `ImageView`.
Quando si esegue questa applicazione, che sarebbe simile all'immagine seguente:

![ImageView localizzato](android-resource-basics-images/03-localized-screenshot.png)

