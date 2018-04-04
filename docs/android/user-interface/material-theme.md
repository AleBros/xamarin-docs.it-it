---
title: Tema materiale
description: Come tema dell'app xamarin con tema materiale
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a3b5f908330833a38aad9e329835a4a437fc29f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="material-theme"></a>Tema materiale

*Tema materiale* è uno stile dell'interfaccia utente che determina l'aspetto delle viste e le attività a partire da Android 5.0 (simbolo). Tema materiale viene compilata in Android 5.0, verrà utilizzato dal sistema dell'interfaccia utente e con le applicazioni. Tema materiale non è un "tema" nel senso di un'opzione a livello di sistema aspetto che un utente può scegliere in modo dinamico da un menu di impostazioni. Piuttosto, materiale tema può essere considerato come un set di stili di base incorporati correlati che è possibile utilizzare per personalizzare l'aspetto dell'app.

Android sono disponibili tre tipi di materiale tema:

-  `Theme.Material` &ndash; Versione scuro del tema di materiale. Questa è la versione predefinita in Android 5.0.

-  `Theme.Material.Light` &ndash; Versione Light di materiale tema.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versione Light di materiale tema, ma con una barra di azione scuro.

Esempi di queste versioni di tema materiale vengono visualizzati qui:

[![Schermate di esempio del tema scuro, tema chiaro e il tema scuro barra delle azioni](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

È possibile derivare dal materiale tema per creare un tema, si esegue l'override di alcuni o tutti gli attributi di colore. Ad esempio, è possibile creare un tema che deriva da `Theme.Material.Light`, ma esegue l'override di colore della barra di app in modo che corrisponda a quello del produttore. È inoltre possibile applicare uno stile singole visualizzazioni; ad esempio, è possibile creare uno stile per [CardView](~/android/user-interface/controls/card-view.md) che ha gli angoli arrotondati più e si utilizza un colore di sfondo scuro.

È possibile usare un singolo tema per un'intera app oppure è possibile utilizzare diversi temi per schermi (attività) in un'applicazione. Nelle schermate precedenti, ad esempio, una singola app Usa un tema diverso per ogni attività per illustrare le combinazioni di colori predefiniti. Pulsanti di opzione passare l'app per diverse attività e, di conseguenza, visualizzano diversi temi.

Poiché il tema di materiale è supportato solo in Android 5.0 e versioni successive, è possibile utilizzare è (o un tema personalizzato derivato dal materiale tema) al tema dell'app per l'esecuzione in versioni precedenti di Android. Tuttavia, è possibile configurare l'applicazione per utilizzare il tema di materiale in dispositivi Android 5.0 e normalmente tentare di un tema precedenti durante l'esecuzione in versioni precedenti di Android (vedere il [compatibilità](#compatibility) sezione di questo articolo per informazioni dettagliate).


## <a name="requirements"></a>Requisiti

Di seguito è necessario utilizzare le nuove funzionalità di Android 5.0 materiale tema nelle App basate su Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

-  **Android SDK** &ndash; Android 5.0 (API 21) o versioni successive deve essere installato mediante Android SDK Manager.

-  **Java JDK 1.8** &ndash; 1.7 JDK può essere utilizzata se sono specificamente con API livello 23 e versioni precedente. 1.8 JDK disponibile da [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Per informazioni su come configurare un progetto di app Android 5.0, vedere [impostazione di un progetto Android 5.0](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Tramite i temi predefiniti

Il modo più semplice per utilizzare il tema di materiale consiste nel configurare l'app per usare un tema predefinito senza personalizzazione. Se non si desidera configurare in modo esplicito un tema, l'applicazione utilizzerà `Theme.Material` (tema scuro). Se l'applicazione ha una sola attività, è possibile configurare un tema a livello di applicazione. Se l'applicazione ha più attività, è possibile configurare un tema a livello di applicazione in modo che usa lo stesso tema per tutte le attività, oppure è possibile assegnare diversi temi alle diverse attività. Le sezioni seguenti illustrano come configurare i temi a livello di app e a livello di attività.


### <a name="theming-an-application"></a>Un'applicazione di temi

Per configurare un'intera applicazione di utilizzare una versione del tema di materiale, impostare il `android:theme` attributo del nodo dell'applicazione in **AndroidManifest.xml** a uno dei seguenti:

-  `@android:style/Theme.Material` &ndash; Tema scuro.

-  `@android:style/Theme.Material.Light` &ndash; Tema chiaro.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Tema chiaro con barra delle azioni scuro.

Nell'esempio seguente consente di configurare l'applicazione *MyApp* per utilizzare il tema chiaro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

In alternativa, è possibile impostare l'applicazione `Theme` attributo **AssemblyInfo.cs** (o **Properties.cs**). Ad esempio:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando il tema dell'applicazione è impostato su `@android:style/Theme.Material.Light`, ogni attività in *MyApp* verrà visualizzata utilizzando `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Temi un'attività

Tema un'attività, si aggiunge un `Theme` impostazione il `[Activity]` attributo sopra la dichiarazione di attività e assegnare `Theme` per la versione del tema di materiale che si desidera utilizzare. I temi di esempio seguente un'attività con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Il valore predefinito verrà utilizzato da altre attività in questa app `Theme.Material` combinazione di colori scuro (oppure, se configurato, l'impostazione del tema dell'applicazione).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Utilizzo di temi personalizzati

È possibile migliorare il marchio creando un tema personalizzato che gli stili dell'app con il proprio marchio&rsquo;colori s. Per creare un tema personalizzato, definire un nuovo stile che deriva da una versione di materiale tema predefinita, si esegue l'override gli attributi di colore che si desidera modificare. Ad esempio, è possibile definire un tema personalizzato che deriva da `Theme.Material.Light.DarkActionBar` e modifica il colore di sfondo schermata di colore anziché vuoti.

Tema materiale espone i seguenti attributi di layout per la personalizzazione:

-  `colorPrimary` &ndash; Il colore della barra delle applicazioni.

-  `colorPrimaryDark` &ndash; Il colore della barra di stato e le barre di app contestuali. in genere si tratta di una versione di scuro `colorPrimary`.

-  `colorAccent` &ndash; Colore dei controlli dell'interfaccia utente, ad esempio caselle di controllo, pulsanti di opzione e caselle di testo.

-  `windowBackground` &ndash; Il colore di sfondo dello schermo.

-  `textColorPrimary` &ndash; Colore del testo dell'interfaccia utente nella barra dell'app.

-  `statusBarColor` &ndash; Il colore della barra di stato.

-  `navigationBarColor` &ndash; Il colore della barra di spostamento.

Queste aree dello schermo sono contrassegnate nel diagramma seguente:

[![Diagramma degli attributi e le aree della schermata associata](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Per impostazione predefinita, `statusBarColor` è impostata sul valore di `colorPrimaryDark`. È possibile impostare `statusBarColor` un colore a tinta unita, oppure è possibile impostare `@android:color/transparent` per rendere trasparente la barra di stato. Barra di navigazione può inoltre essere rese trasparente impostando `navigationBarColor` a `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Creazione di un tema personalizzato App

È possibile creare un tema personalizzato app mediante la creazione e modifica di file di **risorse** cartella del progetto dell'app. Per definire lo stile dell'app con un tema personalizzato, utilizzare la procedura seguente:

-   Creare un **Colors. XML** file **risorse/valori** &mdash; utilizzare questo file per definire i colori del tema personalizzato. Ad esempio, è possibile incollare il codice seguente nel **Colors. XML** consentono di iniziare:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modificare il file di esempio per definire i nomi e i codici colore per le risorse di colore da utilizzare nel tema personalizzato.

-   Creare un **risorse/valori-v21** cartella. In questa cartella, creare un **Styles** file:

    [![Percorso di Styles nella cartella risorse/valori-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Si noti che **risorse/valori-v21** è specifico di Android 5.0 &ndash; le versioni precedenti di Android non leggerà i file in questa cartella.

-   Aggiungere un `resources` nodo **Styles** e definire un `style` nodo con il nome del tema personalizzato. Ad esempio, ecco un **Styles** file che definisce *MyCustomTheme* (derivato da predefinito `Theme.Material.Light` dello stile del tema):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   A questo punto, un'applicazione che utilizza *MyCustomTheme* visualizzerà le scorte `Theme.Material.Light` tema senza personalizzazioni:

    [![Aspetto di un tema personalizzato prima di personalizzazioni](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Aggiungere le personalizzazioni di colori per **Styles** specificando i colori di attributi di layout che si desidera modificare. Ad esempio, per modificare il colore della barra di app per `my_blue` e modificare il colore di controlli dell'interfaccia utente `my_purple`, aggiungere gli override per colore **Styles** che fanno riferimento a risorse colore configurate **Colors.XML**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Con queste modifiche sul posto, un'app che usa *MyCustomTheme* visualizzerà un colore della barra di app in `my_blue` e i controlli dell'interfaccia utente `my_purple`, ma utilizzare il `Theme.Material.Light` combinazione di colori in qualsiasi altro:

[![Aspetto tema personalizzato dopo le personalizzazioni](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

In questo esempio, *MyCustomTheme* utilizza i colori di `Theme.Material.Light` per lo sfondo del colore, la barra di stato e i colori di testo, ma modifica il colore della barra dell'app per `my_blue` e imposta il colore del pulsante di opzione per `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Creazione di uno stile di visualizzazione personalizzata

Android 5.0 rende inoltre possibile definire lo stile di una singola visualizzazione. Dopo aver creato **Colors. XML** e **Styles** (come descritto nella sezione precedente), è possibile aggiungere uno stile di visualizzazione per **Styles**.
Per applicare uno stile di visualizzazione singoli, utilizzare la procedura seguente:

-   Modifica **Resources/values-v21/styles.xml** e aggiungere un `style` nodo con il nome del proprio stile di visualizzazione personalizzata. Impostare gli attributi personalizzati per la visualizzazione all'interno di questa `style` nodo. Ad esempio, per creare un oggetto personalizzato [CardView](~/android/user-interface/controls/card-view.md) stile che ha più angoli arrotondati e utilizza `my_blue` come il colore di sfondo delle smart card, aggiungere un `style` nodo **Styles** (all'interno di `resources`nodo) e configurare il raggio di colore e l'angolo di sfondo:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   Nel layout, impostare il `style` attributo per la visualizzazione affinché corrisponda al nome di stile personalizzato che si è scelto nel passaggio precedente. Ad esempio:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Nella schermata seguente viene fornito un esempio del valore predefinito `CardView` (indicato a sinistra) rispetto ad un `CardView` che abbia uno stile con personalizzata `CardView.MyBlue` tema (illustrato a destra):

[![Esempi di CardView predefiniti e personalizzati CardView](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

In questo esempio, l'oggetto personalizzato `CardView` viene visualizzato con il colore di sfondo `my_blue` e un raggio dell'angolo 18dp.


## <a name="compatibility"></a>Compatibilità

Per definire lo stile dell'app in modo che usa il tema di materiale in Android 5.0, ma verrà automaticamente ripristinata a uno stile verso il basso compatibile in versioni precedenti di Android, attenersi alla procedura seguente:

-   Definire un tema personalizzato in **Resources/values-v21/styles.xml** che deriva da uno stile del tema di materiale. Ad esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definire un tema personalizzato in **Resources/values/styles.xml** che deriva da un tema precedente, ma viene usato lo stesso nome di tema precedente. Ad esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   In **AndroidManifest.xml**, configurare l'app con il nome del tema personalizzato. 
    Ad esempio:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   In alternativa, puoi applicare stili a un'attività specifica utilizzando il tema personalizzato:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se il tema Usa colori definiti in un **Colors. XML** file, assicurarsi di inserire il file in **risorse/valori** (anziché **risorse/valori-v21**) in modo che entrambe le versioni di il tema personalizzato è possibile accedere alle definizioni dei colori.

Quando l'app viene eseguita in un dispositivo Android 5.0, viene utilizzata la definizione del tema specificata in **Resources/values-v21/styles.xml**. Quando si esegue l'app nei dispositivi Android precedenti, eseguirà automaticamente il fallback alla definizione del tema specificata in **Resources/values/styles.xml**.

Per ulteriori informazioni sulla compatibilità di tema con le versioni precedenti di Android, vedere [risorse alternativo](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto il nuovo stile interfaccia utente di materiale tema incluso in Android 5.0 (simbolo). Descritto i tre varianti materiale tema predefinite che è possibile utilizzare per definire lo stile dell'app, illustrato come creare un tema personalizzato per la personalizzazione dell'applicazione e fornito un esempio di come un tema in una singola visualizzazione. Infine, in questo articolo viene illustrato come utilizzare il tema di materiale nell'app e mantiene la compatibilità con le versioni precedenti di Android verso il basso.



## <a name="related-links"></a>Collegamenti correlati

- [ThemeSwitcher (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introduzione al simbolo](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [Risorse alternative](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [L Android Developer Preview](http://developer.android.com/preview/index.html)
- [Progettazione materiale](http://developer.android.com/preview/material/index.html)
- [Principi di progettazione materiale](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [Mantenendo la compatibilità](http://developer.android.com/preview/material/compatibility.html)
