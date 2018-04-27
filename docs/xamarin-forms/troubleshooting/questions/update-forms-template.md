---
title: È possibile aggiornare il modello predefinito di xamarin. Forms per un pacchetto NuGet più recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 6aea0faa65944f33783940178a1d2ce3ef65df1a
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>È possibile aggiornare il modello predefinito di xamarin. Forms per un pacchetto NuGet più recente?

Questa Guida Usa il modello libreria di classi Portabile xamarin. Forms ad esempio, ma lo stesso metodo generale funziona anche per il modello di progetto condiviso xamarin. Forms. Questa guida è stata scritta con l'esempio di aggiornamento da xamarin. Forms 1.5.1.6471 a 2.1.0.6529, ma gli stessi passaggi sono possibili impostare le altre versioni invece come impostazione predefinita.

1.  Copiare il modello originale `.zip` da:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Decomprimere il `.zip` in un percorso temporaneo.

3.  Modificare tutte le occorrenze della versione precedente del pacchetto di form per la nuova versione che si desidera utilizzare.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Esempio: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Modificare l'elemento "name" di principale [file modello multiprogetto](http://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`) per renderlo univoco. Ad esempio:
    > <Name>Applicazione vuota (xamarin. Forms portabile) - 2.1.0.6529</Name>

5.  Comprimere nuovamente la cartella di modello intero. Verificare che corrispondere alla struttura di file originale del `.zip` file. Il `Xamarin.Forms.PCL.vstemplate` file deve trovarsi nella parte superiore del `.zip` file, non all'interno di tutte le cartelle.

6.  Creare una sottodirectory "App per dispositivi mobili" nella cartella modelli di Visual Studio per utente:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Copiare la nuova cartella del modello di backup compresso nella nuova directory "App per dispositivi mobili".

8.  Scaricare il pacchetto NuGet che corrisponde alla versione del passaggio 3. Ad esempio [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (vedere anche [ http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) e copiarlo nella sottocartella appropriata della cartella di estensioni di Visual Studio a Xamarin:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
