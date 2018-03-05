---
title: Icone personalizzate del documento
description: Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine da utilizzare come icona del tipo di documento personalizzato in un'app xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/23/2017
ms.openlocfilehash: 582fcbacbf1959e05773babb1219817ba319a937
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="custom-document-icons"></a>Icone personalizzate del documento

_Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine da utilizzare come icona del tipo di documento personalizzato in un'app xamarin. IOS._

Se un'app xamarin supporta il caricamento di un particolare tipo di documento, lo sviluppatore può specificare le icone che verrà utilizzata dal sistema quando rileva tale tipo di documento, ad esempio quando un utente tiene premuto un allegato nel *applicazione di posta elettronica* come illustrato di seguito:

 [ ![](custom-document-types-images/17.png "Un esempio delle icone del tipo di documento")](custom-document-types-images/17.png)

Lo sviluppatore può aggiungere informazioni sul tipo di documento per un formato di file dell'app è in grado di apertura con l'inclusione di voci del dizionario per la `CFBundleTypeName` stringa e `LSItemContentTypes` matrice dell'app `Info.plist`. Le icone per il tipo di documento passare il `CFBundleTypeIconFiles` matrice. Se non viene fornita un'icona di documento, iOS verrà derivare dall'icona dell'app.
È possibile fornire le icone per diverse dimensioni, ottimizzate per le varie risoluzioni di dispositivo. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per assegnare i valori sono i seguenti in Visual Studio per Mac, usare il **tipi di documento** sezione sotto il **avanzate** scheda il `Info.plist` editor per aggiungere il tipo di documento e assegnare le icone di immagine. Ad esempio, ecco una schermata che illustra la registrazione per il supporto PDF:

 [ ![](custom-document-types-images/18.png "La sezione tipi di documento nella scheda avanzata l'editor 'Info. plist'")](custom-document-types-images/18.png)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per assegnare questi valori in Visual Studio, usare il **tipi di documento** sezione sotto il **avanzate** scheda il `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Aprire la sezione tipi di documento nella scheda Avanzate")

Fare clic su di **Aggiungi tipo di documento** pulsante e compilare i campi obbligatori:

![](custom-document-types-images/doc02w.png "Il modulo Aggiungi tipo di documento")

-----


Per ulteriori informazioni sui tipi di documenti, vedere Apple [Uniform riferimento al tipo di identificatori](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) e [gli argomenti di programmazione di documento interazione per iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
