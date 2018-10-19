---
title: Tema Material
description: Come tema dell'app xamarin. Android con tema Material
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 4b9c39a0ced9a264f501d78142c3bdfd556593ed
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "30771320"
---
# <a name="material-theme"></a>Tema Material

*Tema Material* è uno stile di interfaccia utente che determina l'aspetto delle visualizzazioni e le attività a partire da Android 5.0 (Lollipop). Tema Material viene compilata in Android 5.0, quindi viene usato dal sistema dell'interfaccia utente e con le applicazioni. Tema Material non è un "tema" nel senso di un'opzione di aspetto a livello di sistema che un utente può scegliere in modo dinamico da un menu di impostazioni. Piuttosto, materiale tema può essere considerato come un set di stili di base incorporati correlati che è possibile usare per personalizzare l'aspetto dell'app.

Android offre tre tipi di materiale tema:

-  `Theme.Material` &ndash; Versione scuro del tema Material; Questa è la versione predefinita in Android 5.0.

-  `Theme.Material.Light` &ndash; Versione ridotta del tema Material.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versione ridotta del tema di materiale, ma con una barra delle azioni scuro.

Esempi di questi tipi di materiale tema vengono visualizzati di seguito:

[![Screenshot di esempio del tema scuro, tema chiaro e il tema scuro della barra delle azioni](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

È possibile derivare dal tema Material per creare il proprio tema, si esegue l'override di alcuni o tutti gli attributi di colore. Ad esempio, è possibile creare un tema che deriva da `Theme.Material.Light`, ma esegue l'override di colore della barra di app in modo che corrisponda il colore del tuo marchio. È anche possibile applicare uno stile singole viste; ad esempio, è possibile creare uno stile per [widget CardView](~/android/user-interface/controls/card-view.md) che ha gli angoli arrotondati più e utilizza un colore di sfondo più scuro.

È possibile utilizzare un tema singolo per un'intera app oppure è possibile utilizzare diversi temi disponibili per le diverse schermate (attività) in un'app. Negli screenshot precedente, ad esempio, una singola app Usa un tema diverso per ogni attività per illustrare le combinazioni di colori predefiniti. Pulsanti di opzione passa all'app per le varie attività e, di conseguenza, visualizzano diversi temi disponibili.

Tema Material è supportato solo in Android 5.0 e versioni successive, è possibile usare (o un tema personalizzato derivato dal tema Material) al tema di app per l'esecuzione in versioni precedenti di Android. Tuttavia, è possibile configurare l'app per usare tema Material nei dispositivi Android 5.0 e normalmente il fallback a un tema precedente quando è in esecuzione in versioni precedenti di Android (vedere la [compatibilità](#compatibility) sezione di questo articolo per informazioni dettagliate).


## <a name="requirements"></a>Requisiti

Di seguito è necessario usare le nuove funzionalità di Android 5.0 materiale tema nelle App basate su Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac. 

-  **Android SDK** &ndash; Android 5.0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

-  **Java JDK 1.8** &ndash; JDK 1.7 può essere usato se si è specificamente a livello di destinazione è API 23 e versioni precedente. È disponibile dal JDK 1.8 [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Per informazioni su come configurare un progetto di app Android 5.0, vedere [impostazione di un progetto Android 5.0](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Tramite i temi predefiniti

Il modo più semplice per utilizzare il tema Material consiste nel configurare l'app per usare un tema predefinito senza personalizzazione. Se non si desidera configurare in modo esplicito un tema, l'app predefinita sarà `Theme.Material` (il tema scuro). Se l'app ha una sola attività, è possibile configurare un tema a livello di applicazione. Se l'app ha più attività, è possibile configurare un tema a livello di applicazione in modo che usa lo stesso tema per tutte le attività, oppure è possibile assegnare diversi temi disponibili per diverse attività. Le sezioni seguenti illustrano come configurare i temi a livello di app e a livello di attività.


### <a name="theming-an-application"></a>Un'applicazione di temi

Per configurare un'intera applicazione per usare una versione del tema di materiale, impostare il `android:theme` attributo del nodo dell'applicazione nel **androidmanifest. XML** a uno dei seguenti:

-  `@android:style/Theme.Material` &ndash; Tema scuro.

-  `@android:style/Theme.Material.Light` &ndash; Tema chiaro.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Tema chiaro con barra delle azioni scuro.

L'esempio seguente configura l'applicazione *MyApp* per utilizzare il tema chiaro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

In alternativa, è possibile impostare l'applicazione `Theme` dell'attributo **AssemblyInfo.cs** (oppure **Properties.cs**). Ad esempio:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando il tema dell'applicazione è impostato su `@android:style/Theme.Material.Light`, ogni attività nel *MyApp* verrà visualizzata utilizzando `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Temi un'attività

A tema un'attività, aggiungere un `Theme` impostazione il `[Activity]` attributo sopra la dichiarazione di attività e assegnare `Theme` per la versione del tema di materiale che si desidera utilizzare. I temi di esempio seguente un'attività con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Altre attività in questa app utilizzerà il valore predefinito `Theme.Material` combinazione di colori scura (oppure, se configurato, l'impostazione del tema dell'applicazione).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Utilizzo dei temi personalizzati

È possibile migliorare il tuo marchio creando un tema personalizzato che consente di disegnare l'app con il proprio marchio&rsquo;colori s. Per creare un tema personalizzato, è definire un nuovo stile che deriva da una versione del tema Material incorporata, sostituendo gli attributi di colore che si desidera modificare. Ad esempio, è possibile definire un tema personalizzato che deriva da `Theme.Material.Light.DarkActionBar` e cambia il colore di sfondo dello schermo in beige invece vuoto.

Tema Material espone i seguenti attributi di layout per la personalizzazione:

-  `colorPrimary` &ndash; Colore della barra dell'app.

-  `colorPrimaryDark` &ndash; Il colore della barra di stato e le barre dell'app contestuali; in genere si tratta di una versione scura di `colorPrimary`.

-  `colorAccent` &ndash; Colore dei controlli dell'interfaccia utente, ad esempio caselle di controllo, pulsanti di opzione e caselle di testo di modifica.

-  `windowBackground` &ndash; Colore dello sfondo dello schermo.

-  `textColorPrimary` &ndash; Colore del testo dell'interfaccia utente nella barra dell'app.

-  `statusBarColor` &ndash; Colore della barra di stato.

-  `navigationBarColor` &ndash; Colore della barra di spostamento.

Queste aree dello schermo sono contrassegnate nel diagramma seguente:

[![Diagramma degli attributi e delle rispettive aree dello schermo associato](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Per impostazione predefinita `statusBarColor` è impostata sul valore di `colorPrimaryDark`. È possibile impostare `statusBarColor` a un colore a tinta unita, oppure è possibile impostarlo `@android:color/transparent` per rendere trasparente la barra di stato. La barra di spostamento può inoltre essere creata trasparente impostando `navigationBarColor` a `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Creazione di un tema dell'App personalizzata

È possibile creare un tema personalizzato app creando e modificando i file nei **risorse** cartella del progetto dell'app. Per applicare uno stile all'app con un tema personalizzato, usare la procedura seguente:

-   Creare un **Colors** del file in **risorse/valori** &mdash; è utilizzare questo file per definire i colori del tema personalizzato. Ad esempio, è possibile incollare il codice seguente nel **Colors** che consentono di iniziare:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modificare il file di esempio per definire i nomi e codici colore per le risorse di colore che si userà il tema personalizzato.

-   Creare un **risorse/valori-v21** cartella. In questa cartella, creare un **Styles** file:

    [![Posizione del Styles. XML nella cartella di risorse/valori-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Si noti che **risorse/valori-v21** è specifico di Android 5.0 &ndash; le versioni precedenti di Android non leggerà i file in questa cartella.

-   Aggiungere un `resources` nodo a **Styles** e definire un `style` nodo con il nome del tema personalizzato. Ad esempio, ecco una **Styles** file che definisce *MyCustomTheme* (derivato da predefiniti `Theme.Material.Light` stile del tema):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   A questo punto, un'app che usa *MyCustomTheme* visualizzerà le scorte `Theme.Material.Light` tema senza personalizzazioni:

    [![Aspetto di tema personalizzato prima le personalizzazioni](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Aggiungere le personalizzazioni dei colori **Styles** definendo i colori degli attributi di layout che si desidera modificare. Ad esempio, per modificare il colore della barra di app per `my_blue` e modificare il colore dei controlli dell'interfaccia utente da `my_purple`, aggiungere le sostituzioni di colore per **Styles. XML** che fanno riferimento a risorse di colore configurate **Colors**:

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

Con queste modifiche posto, un'app che usa *MyCustomTheme* visualizzerà un colore della barra di app in `my_blue` e i controlli dell'interfaccia utente `my_purple`, ma usare il `Theme.Material.Light` in qualsiasi altro schema di colori:

[![Aspetto di tema personalizzato dopo le personalizzazioni](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

In questo esempio *MyCustomTheme* sfrutta i colori da `Theme.Material.Light` per lo sfondo colori, barra di stato e dei colori del testo, ma si modifica il colore della barra dell'app per `my_blue` e imposta il colore del pulsante di opzione `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Creazione di uno stile di visualizzazione personalizzata

Android 5.0 rende inoltre possibile applicare uno stile a una singola visualizzazione. Dopo aver creato **Colors** e **Styles. XML** (come descritto nella sezione precedente), è possibile aggiungere uno stile di visualizzazione da **Styles**.
Per applicare uno stile a una singola visualizzazione, procedere come segue:

-   Modificare **Resources/values-v21/styles.xml** e aggiungere un `style` nodo con il nome dello stile di visualizzazione personalizzata. Impostare gli attributi di colore personalizzato per la visualizzazione all'interno di questo `style` nodo. Ad esempio, per creare una classe personalizzata [widget CardView](~/android/user-interface/controls/card-view.md) stile di visualizzazione che ha più angoli arrotondati e utilizza `my_blue` come il colore di sfondo di smart card, aggiungere un `style` nodo **Styles. XML** (all'interno di `resources`nodo) e configurare il raggio di colore e nell'angolo in background:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   Nel layout, impostare il `style` attributo per la visualizzazione affinché corrisponda al nome di uno stile personalizzato scelto nel passaggio precedente. Ad esempio:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Nella schermata seguente viene fornito un esempio del valore predefinito `CardView` (indicato a sinistra) rispetto a un `CardView` che è stato applicato lo stile con l'oggetto personalizzato `CardView.MyBlue` tema (illustrata a destra):

[![Esempi di predefinito widget CardView e widget CardView personalizzato](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

In questo esempio, l'oggetto personalizzato `CardView` viene visualizzato con il colore di sfondo `my_blue` e un raggio dell'angolo 18dp.


## <a name="compatibility"></a>Compatibilità

Per applicare uno stile all'app in modo che lo Usa tema di materiale in Android 5.0 ma verrà automaticamente ripristinato uno stile verso il basso compatibile in versioni precedenti di Android, usare la procedura seguente:

-   Definire un tema personalizzato nella **Resources/values-v21/styles.xml** che deriva da uno stile del tema Material. Ad esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definire un tema personalizzato nella **Resources/values/styles.xml** che deriva da un tema meno recente, ma usa lo stesso nome di tema come illustrato in precedenza. Ad esempio:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Nelle **androidmanifest. XML**, configurare l'app con il nome del tema personalizzato. 
    Ad esempio:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   In alternativa, è possibile applicare uno stile di un'attività specifica usando il tema personalizzato:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se il tema usa i colori definiti in un **Colors** del file, assicurarsi di inserire questo file nella **risorse/valori** (anziché **risorse/valori-v21**) in modo che entrambe le versioni di il tema personalizzato può accedere alle definizioni dei colori.

Quando l'app viene eseguita in un dispositivo Android 5.0, userà la definizione del tema specificata nella **Resources/values-v21/styles.xml**. Quando si esegue questa app nei dispositivi Android meno recenti, eseguirà automaticamente il fallback alla definizione del tema specificata nella **Resources/values/styles.xml**.

Per altre informazioni sulla compatibilità di tema con versioni precedenti di Android, vedere [risorse alternative](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Riepilogo

Questo articolo è stato introdotto il nuovo stile interfaccia utente tema materiale incluso in Android 5.0 (Lollipop). Descrive le tre versioni tema Material predefinite che è possibile usare per l'app in stile, spiega come creare un tema personalizzato per la personalizzazione di app e fornito un esempio relativo al tema una singola visualizzazione. Infine, in questo articolo spiega come usare tema Material nell'app e mantiene la compatibilità con le versioni precedenti di Android verso il basso.



## <a name="related-links"></a>Collegamenti correlati

- [ThemeSwitcher (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introduzione al simbolo](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Risorse alternative](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android Lollipop](https://developer.android.com/about/versions/lollipop)
- [Per gli sviluppatori Android a torta](https://developer.android.com/about/versions/pie/)
- [Material Design](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Principi di progettazione dei materiali](https://material.io/design/)
- [Mantiene la compatibilità](https://developer.android.com/training/backward-compatible-ui/)
