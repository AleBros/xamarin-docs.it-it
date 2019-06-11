---
title: Icone personalizzate dei documenti in xamarin. IOS
description: Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin. IOS da utilizzare come icona del tipo di documento personalizzato.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 7520d3984a204dbceb67ef5310a10b1571f03127
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827380"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icone personalizzate dei documenti in xamarin. IOS

_Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin. IOS da utilizzare come icona del tipo di documento personalizzato._

Se un'app xamarin. IOS supporta il caricamento di un particolare tipo di documento, lo sviluppatore può specificare le icone che verrà utilizzata dal sistema quando rileva tale tipo di documento, ad esempio quando un utente tiene premuto un allegato nel *applicazione di posta elettronica* come di seguito riportato:

 [![](custom-document-types-images/17.png "Un esempio delle icone del tipo di documento")](custom-document-types-images/17.png#lightbox)

Lo sviluppatore può aggiungere le informazioni sul tipo di documento per un formato di file dell'app è in grado di apertura, includendo le voci di dizionario per la `CFBundleTypeName` stringa e `LSItemContentTypes` matrice dell'app `Info.plist`. Le icone per il tipo di documento andare `CFBundleTypeIconFiles` matrice. Se l'icona del documento non è specificato, esegue la derivazione uno dall'icona dell'app iOS.
Le icone possono essere fornite per diverse dimensioni, ottimizzate per le varie risoluzioni di dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per assegnare questi valori in Visual Studio per Mac, usare il **tipi di documento** sezione sotto il **avanzate** scheda la `Info.plist` editor per aggiungere il tipo di documento e assegnare le icone di immagine a esso. Ad esempio, ecco uno screenshot che illustra la registrazione per supporto per PDF:

 [![](custom-document-types-images/18.png "La sezione tipi di documento sotto la scheda Avanzate sull'editor di 'Info. plist'")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per assegnare questi valori in Visual Studio, usare il **tipi di documento** sezione sotto il **avanzate** scheda la `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Aprire la sezione tipi di documento sotto la scheda Avanzate")

Scegliere il **Aggiungi tipo di documento** pulsante e compilare i campi obbligatori:

![](custom-document-types-images/doc02w.png "Form Aggiungi tipo di documento")

-----


Per altre informazioni sui tipi di documenti, vedere di Apple [riferimento a identificatori di tipo Uniform](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) e [gli argomenti di programmazione del documento interazione per iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
