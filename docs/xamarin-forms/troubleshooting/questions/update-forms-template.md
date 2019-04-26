---
title: È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: e439d39dd8591cad14485e64aabab2d6016a8e27
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345909"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?

Questa Guida Usa il modello di libreria xamarin. Forms .NET Standard come esempio, ma lo stesso metodo generale funzioneranno anche per il modello di progetto condiviso xamarin. Forms. Questa guida è stata scritta con l'esempio di aggiornamento da xamarin. Forms 1.5.1.6471 a 2.1.0.6529, ma gli stessi passaggi sono possibili impostare invece altre versioni come impostazione predefinita.

1.  Copiare il modello originale `.zip` da:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Decomprimere il `.zip` in un percorso temporaneo.

3.  Modificare tutte le occorrenze della versione precedente del pacchetto form alla nuova versione da usare.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Esempio: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Modificare l'elemento "name" di principale [file modello multiprogetto](https://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`) per renderlo univoco. Ad esempio:
    > <Name>App vuota (xamarin. Forms portabile) - 2.1.0.6529</Name>

5.  Comprimere nuovamente la cartella del modello intero. Assicurarsi di corrisponde alla struttura di file originale del `.zip` file. Il `Xamarin.Forms.PCL.vstemplate` file deve essere in cima il `.zip` file, non all'interno di tutte le cartelle.

6.  Creare una sottodirectory "App per dispositivi mobili" nella cartella modelli di Visual Studio per ogni utente:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Copiare la nuova cartella del modello di backup compressi nella nuova directory "App per dispositivi mobili".

8.  Scaricare il pacchetto NuGet corrispondente alla versione del passaggio 3. Ad esempio, [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (vedere anche [ https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) e copiarlo nella sottocartella appropriata della cartella delle estensioni Xamarin Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
