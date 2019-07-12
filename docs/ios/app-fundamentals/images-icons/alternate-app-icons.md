---
title: Icone dell'App alternativo in xamarin. IOS
description: Questo documento descrive come usare le icone dell'app alternativo in xamarin. IOS. Viene descritto come aggiungere queste icone per un progetto xamarin. IOS, come modificare il file Info. plist e come gestire l'icona dell'app a livello di codice.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: fc4ef6eb16893cc1df0d980d27af8589a280e933
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832419"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Icone dell'App alternativo in xamarin. IOS

_Questo articolo illustra l'uso di icone dell'app alternativo in xamarin. IOS._

Apple ha aggiunti numerosi miglioramenti a iOS 10.3 che consentono a un'app gestire la relativa icona:

- `ApplicationIconBadgeNumber` : Ottiene o imposta il badge dell'icona dell'app in Springboard.
- `SupportsAlternateIcons` -Se `true` l'app ha un set alternativo di icone.
- `AlternateIconName` : Restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si usa l'icona principale.
- `SetAlternameIconName` -Usare questo metodo per cambiare l'icona dell'app per l'icona alternativo specificato.

![](alternate-app-icons-images/icons04.png "Un esempio di avviso quando un'app per la relativa icona viene modificato")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Aggiunta di icone alternative a un progetto xamarin. IOS

Per consentire a un'app di passare a un'icona alternativa, una raccolta di immagini icona dovrà essere incluso nel progetto dell'app xamarin. IOS. Queste immagini non è possibile aggiungere al progetto usando il tipico `Assets.xcassets` metodo, devono essere aggiunti per il **risorse** cartella direttamente.

Seguire questa procedura:

1. Selezionare le immagini di icone in una cartella, selezionare tutte e trascinarli il **le risorse** cartella la **Esplora soluzioni**:

    ![](alternate-app-icons-images/icons00.png "Selezionare le immagini di icone da una cartella")

2. Quando richiesto, selezionare **copia**, **Usa la stessa azione per tutti i file selezionati** e fare clic sui **OK** pulsante:

    ![](alternate-app-icons-images/icons02.png "Aggiungi File a cartella finestra di dialogo")

3. Il **risorse** cartella dovrebbe essere simile al seguente quando completata:

    ![](alternate-app-icons-images/icons01.png "Cartella delle risorse dovrebbe essere simile al seguente")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modifica del File Info. plist

Con le immagini necessarie aggiunte per il **risorse** cartella, il [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) chiave dovrà essere aggiunto al progetto **Info. plist** file. Questa chiave definirà il nome della nuova icona e le immagini che lo compongono.

Seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file **Info.plist** per aprirlo e modificarlo.
2. Passare al **origine** visualizzazione.
3. Aggiungere un **icone del Bundle** la chiave e lasciare il **tipo** impostata su **dizionario**.
4. Aggiungere un `CFBundleAlternateIcons` chiave e impostare il **tipo** al **dizionario**.
5. Aggiungere un `AppIcon2` chiave e impostare il **tipo** al **dizionario**. Questo sarà il nome del nuovo set di icone app alternativi.
6. Aggiungere un `CFBundleIconFiles` chiave e impostare il **tipo** a **matrice**
7. Aggiungere una nuova stringa per il `CFBundleIconFiles` matrice per ogni file di icona omettendo l'estensione e il `@2x`, `@3x`, i suffissi e così via (esempio `100_icon`). Ripetere questo passaggio per ogni file che costituiscono il set di icone alternative.
8. Aggiungere un `UIPrerenderedIcon` chiave per il `AppIcon2` dizionario, impostare il **tipo** a **booleano** e il valore da **No**.
9. Salvare le modifiche apportate al file.

L'oggetto risultante **Info. plist** file dovrebbe essere simile al seguente quando completata:

![](alternate-app-icons-images/icons03.png "File Info. plist completato")

In alternativa, come questa se aperto in un editor di testo:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Gestire l'icona dell'App 

Con le immagini icona incluse nel progetto xamarin. IOS e il **Info. plist** file configurato correttamente, lo sviluppatore può utilizzare una delle molte nuove funzionalità aggiunte a iOS 10.3 per controllare l'icona dell'app.

Il `SupportsAlternateIcons` proprietà del `UIApplication` classe consente allo sviluppatore verificare se un'app supporta le icone alternative. Ad esempio:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

Il `ApplicationIconBadgeNumber` proprietà del `UIApplication` classe consente allo sviluppatore di ottenere o impostare il numero corrente di badge dell'icona dell'app in Springboard. Il valore predefinito è zero (0). Ad esempio:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

Il `AlternateIconName` proprietà del `UIApplication` classe consente allo sviluppatore di ottenere il nome dell'icona app alternativo attualmente selezionata o restituisce `null` se l'app Usa l'icona principale. Ad esempio:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

Il `SetAlternameIconName` proprietà del `UIApplication` classe consente allo sviluppatore di modificare l'icona dell'app. Passare il nome dell'icona per selezionare o `null` da restituire per l'icona principale. Ad esempio:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Quando l'app viene eseguita e l'utente seleziona un'icona alternativa, verrà visualizzato un avviso simile al seguente:

![](alternate-app-icons-images/icons04.png "Un esempio di avviso quando un'app per la relativa icona viene modificato")

Se l'utente passa nuovamente all'icona primario, verrà visualizzato un avviso simile al seguente:

![](alternate-app-icons-images/icons05.png "Un esempio di avviso quando un'app viene modificata l'icona principale")

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'aggiunta di icone dell'app alternativo a un progetto xamarin. IOS e il relativo utilizzo all'interno dell'app.



## <a name="related-links"></a>Collegamenti correlati

- [iOSTenThree esempio](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
