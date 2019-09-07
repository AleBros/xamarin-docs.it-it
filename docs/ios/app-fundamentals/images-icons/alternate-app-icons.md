---
title: Icone alternative per le app in Novell. iOS
description: Questo documento descrive come usare icone alternative dell'app in Novell. iOS. Viene illustrato come aggiungere queste icone a un progetto Novell. iOS, come modificare il file INFO. plist e come gestire l'icona dell'app a livello di codice.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d391c57c2c63cd4e371bd97ba455962aa053f9ed
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767342"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Icone alternative per le app in Novell. iOS

_Questo articolo illustra l'uso di icone alternative per le app in Novell. iOS._

Apple ha aggiunto diversi miglioramenti a iOS 10,3 che consentono a un'app di gestire la relativa icona:

- `ApplicationIconBadgeNumber`: Ottiene o imposta la notifica dell'icona dell'app nel Springboard.
- `SupportsAlternateIcons`-Se `true` l'app dispone di un set alternativo di icone.
- `AlternateIconName`: Restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si utilizza l'icona primaria.
- `SetAlternameIconName`-Usare questo metodo per impostare l'icona dell'app sull'icona alternativa specificata.

![](alternate-app-icons-images/icons04.png "Un avviso di esempio quando un'app modifica la relativa icona")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Aggiunta di icone alternative a un progetto Novell. iOS

Per consentire a un'app di passare a un'icona alternativa, è necessario includere una raccolta di immagini icona nel progetto app Novell. iOS. Queste immagini non possono essere aggiunte al progetto usando il metodo `Assets.xcassets` tipico, ma devono essere aggiunte direttamente alla cartella **Resources** .

Seguire questa procedura:

1. Selezionare le immagini icona obbligatorie in una cartella, selezionare tutti e trascinarli nella cartella **risorse** nel **Esplora soluzioni**:

    ![](alternate-app-icons-images/icons00.png "Selezionare le immagini delle icone da una cartella")

2. Quando richiesto, selezionare **copia**, **usare la stessa azione per tutti i file selezionati** e fare clic sul pulsante **OK** :

    ![](alternate-app-icons-images/icons02.png "Finestra di dialogo Aggiungi file a cartella")

3. Al termine, la cartella **Resources** avrà un aspetto simile al seguente:

    ![](alternate-app-icons-images/icons01.png "La cartella risorse dovrebbe essere simile alla seguente")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modifica del file INFO. plist

Con le immagini obbligatorie aggiunte alla cartella **Resources** , è necessario aggiungere la chiave [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) al file **info. plist** del progetto. Questa chiave definirà il nome della nuova icona e le immagini che lo compongono.

Seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file **Info.plist** per aprirlo e modificarlo.
2. Passare alla visualizzazione di **origine** .
3. Aggiungere una chiave delle **Icone del bundle** e lasciare il **tipo** impostato su **Dictionary**.
4. Aggiungere una `CFBundleAlternateIcons` chiave e impostare il **tipo** su **Dictionary**.
5. Aggiungere una `AppIcon2` chiave e impostare il **tipo** su **Dictionary**. Questo sarà il nome del nuovo set di icone dell'app alternativa.
6. Aggiungere una `CFBundleIconFiles` chiave e impostare il **tipo** su **Array**
7. Aggiungere una `CFBundleIconFiles` nuova stringa alla matrice per ogni file di icona lasciando l'estensione e i `@2x`suffissi `@3x`, e così via (esempio `100_icon`). Ripetere questo passaggio per ogni file che compone il set di icone alternative.
8. Aggiungere una `UIPrerenderedIcon` `AppIcon2` chiave al dizionario, impostare il **tipo** su **booleano** e il valore su **No**.
9. Salvare le modifiche apportate al file.

Al termine, il file **info. plist** risultante sarà simile al seguente:

![](alternate-app-icons-images/icons03.png "File INFO. plist completato")

O come questo se aperto in un editor di testo:

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

## <a name="managing-the-apps-icon"></a>Gestione dell'icona dell'app 

Con le immagini icona incluse nel progetto Novell. iOS e il file **info. plist** configurato correttamente, lo sviluppatore può usare una delle numerose nuove funzionalità aggiunte a iOS 10,3 per controllare l'icona dell'app.

La `SupportsAlternateIcons` proprietà`UIApplication` della classe consente allo sviluppatore di verificare se un'app supporta icone alternative. Ad esempio:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

La `ApplicationIconBadgeNumber` proprietà`UIApplication` della classe consente allo sviluppatore di ottenere o impostare il numero di notifica corrente dell'icona dell'app nel Springboard. Il valore predefinito è zero (0). Ad esempio:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

La `AlternateIconName` proprietà `null` della classe consente allo sviluppatore di ottenere il nome dell'icona dell'app alternativa attualmente selezionata oppure restituisce se l'app usa l'icona primaria. `UIApplication` Ad esempio:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

La `SetAlternameIconName` proprietà`UIApplication` della classe consente allo sviluppatore di modificare l'icona dell'app. Passare il nome dell'icona per selezionare o `null` tornare all'icona primaria. Ad esempio:

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

![](alternate-app-icons-images/icons04.png "Un avviso di esempio quando un'app modifica la relativa icona")

Se l'utente torna all'icona principale, verrà visualizzato un avviso simile al seguente:

![](alternate-app-icons-images/icons05.png "Un avviso di esempio quando un'app viene modificata nell'icona primaria")

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato l'aggiunta di icone di app alternative a un progetto Novell. iOS e l'uso all'interno dell'app.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
