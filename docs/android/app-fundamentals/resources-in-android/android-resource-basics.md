---
title: Nozioni fondamentali sulla risorsa Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: f6be1001e5d3455a94e677f1bb5dc52ca574b873
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-resource-basics"></a>Nozioni fondamentali sulla risorsa Android

Quasi tutte le applicazioni Android avrà una qualche forma di risorse in essi. come minimo hanno spesso il layout dell'interfaccia utente nel formato di file XML. Quando viene creata un'applicazione di xamarin, le risorse predefinite sono il programma di installazione dal modello di progetto xamarin:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![File di risorse](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![File di risorse](android-resource-basics-images/01-resource-files-xs.png)
 
-----

I cinque file che costituiscono le risorse predefinite sono stati creati nella cartella risorse:

-  **Icona Icon.PNG** &ndash; l'icona predefinita per l'applicazione

-  **Main. axml** &ndash; il file di layout predefinito dell'interfaccia utente per un'applicazione. Si noti che, sebbene Android, viene utilizzato il **XML** Usa xamarin estensione di file, il **.axml** estensione di file.

-  **Strings** &ndash; una tabella di stringhe per facilitare la localizzazione dell'applicazione

-  **AboutResources.txt** &ndash; questo non è necessario e può essere eliminato senza problemi. Fornisce solo una panoramica di alto livello di cartella risorse e dei file in essa contenuti.

-  **Cs** &ndash; questo file viene automaticamente generato e gestito da xamarin e univoci contiene gli ID assegnati a ogni risorsa. Questo è molto simile e scopo identico al file R.java che potrebbe avere un'applicazione Android scritta in Java. Viene creata automaticamente dagli strumenti di xamarin e verrà rigenerato nel tempo.


## <a name="creating-and-accessing-resources"></a>Creazione e l'accesso alle risorse

Creazione di risorse è semplice come l'aggiunta di file della directory per il tipo di risorsa in questione. Cattura di schermata seguente mostra le risorse stringa per le impostazioni locali tedesche sono stati aggiunti a un progetto. Quando **Strings** è stato aggiunto al file, il **azione di compilazione** è stata impostata automaticamente su **AndroidResource** dagli strumenti di xamarin:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Azione per Strings impostato su AndroidResource di compilazione](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Azione per Strings impostato su AndroidResource di compilazione](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

In questo modo gli strumenti di xamarin per compilare e incorporare le risorse per il file APK correttamente. Se per qualche motivo il **azione di compilazione** non è impostata su **risorse Android**, quindi i file verranno esclusi dall'APK e qualsiasi tentativo di caricare o accedere alle risorse comporterà un errore di run-time e verificherà un arresto anomalo dell'applicazione.

Inoltre, è importante notare che mentre Android supporta solo nomi file lettere minuscole per gli elementi di risorsa, xamarin tollera un po' meglio; supporta i nomi di file sia lettere maiuscole e minuscole. La convenzione per i nomi delle immagini consiste nell'usare minuscole con caratteri di sottolineatura come separatore (ad esempio, **my\_immagine\_name.png**). Si noti che i nomi delle risorse non possono essere elaborati se trattini o spazi vengono utilizzati come separatori.

Una volta le risorse sono stati aggiunti a un progetto, sono disponibili due modalità di utilizzo in un'applicazione &ndash; a livello di codice (all'interno di codice) o dai file XML.


## <a name="referencing-resources-programmatically"></a>Riferimento alle risorse a livello di codice

Per accedere a questi file a livello di programmazione, vengono assegnati un ID risorsa univoco. Questo ID di risorsa è un numero intero definito in una classe speciale chiamata `Resource`, che si trova nel file **cs**, e sarà simile al seguente:

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

Ogni ID di risorsa è contenuto all'interno di una classe annidata che corrisponde al tipo di risorsa. Ad esempio, quando il file **Icon.png** è stato aggiunto al progetto, xamarin aggiornato il `Resource` (classe), la creazione di una classe annidata denominata `Drawable` con una costante interno denominato `Icon`.
In questo modo il file **Icon.png** a fare riferimento nel codice come `Resource.Drawable.Icon`. La `Resource` classe dovrebbe non essere modificata manualmente, come le modifiche effettuate a esso verranno sovrascritta da xamarin.

Quando si fa riferimento alle risorse a livello di codice (nel codice), è possibile accedere tramite la gerarchia di classi di risorse che utilizza la sintassi seguente:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash; il pacchetto che fornisce la risorsa ed è solo necessario quando vengono utilizzate le risorse da altri pacchetti.

-  **ResourceType** &ndash; si tratta del tipo di risorsa annidata all'interno della classe di risorse descritta in precedenza.

-  **Nome della risorsa** &ndash; questo è il nome del file della risorsa (senza estensione) o il valore dell'attributo android: name per le risorse presenti in un elemento XML.


## <a name="referencing-resources-from-xml"></a>Riferimento alle risorse da XML

Accesso alle risorse in un file XML da una segue una sintassi speciale:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash; il pacchetto che fornisce la risorsa ed è solo necessario quando vengono utilizzate le risorse da altri pacchetti.

-  **ResourceType** &ndash; si tratta del tipo di risorsa annidata all'interno della classe di risorse.

-  **Nome della risorsa** &ndash; si tratta del nome della risorsa (*senza* l'estensione del tipo di file) o il valore del `android:name` attributo per le risorse presenti in un elemento XML.

Ad esempio il contenuto di un file di layout, **axml**, sono i seguenti:

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

L'esempio include un [ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview) che richiede una risorsa drawable denominata **flag**. Il `ImageView` è relativo `src` attributo impostato su **@drawable/flag**. Quando si avvia l'attività, avrà un aspetto Android all'interno della directory **risorse/Drawable** per un file denominato **flag.png** (l'estensione del file potrebbe essere un altro formato di immagine, ad esempio **flag.jpg**) caricare il file e visualizzarli nel `ImageView`.
Quando viene eseguita l'applicazione, dovrebbe essere simile l'immagine seguente:

![ImageView localizzata](android-resource-basics-images/03-localized-screenshot.png)

