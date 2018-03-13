---
title: Android Wear
description: Creazione di applicazioni per dispositivi Android di indossabile.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ac83b74f39497333de7aa80079784adf61bf2e65
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="android-wear"></a>Android Wear

Usura Android è una versione di Android è progettato per i dispositivi indossabile, ad esempio le espressioni di controllo smart. In questa sezione include le istruzioni su come installare e configurare gli strumenti necessari per lo sviluppo di scadenza, una procedura dettagliata per la creazione del primo dispositivo usura e un elenco di esempi che è possibile fare riferimento per l'App, usare per la creazione di propri.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introduzione](~/android/wear/get-started/index.md)

Introduce accenti Android, viene descritto come installare e configurare il computer per lo sviluppo di usura e vengono descritti i passaggi che consentono di creare ed eseguire la prima app Android con accenti su un emulatore o dispositivo usura.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaccia utente](~/android/wear/user-interface/index.md)

Spiega Android usura controlla e fornisce collegamenti a esempi che illustrano come utilizzare questi controlli.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Funzionalità della piattaforma](~/android/wear/platform/index.md)

Documenti in questa sezione riguardano le funzionalità specifiche per Android con accenti. Di seguito è disponibile un argomento che descrive come creare un WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Dimensioni dello schermo](~/android/wear/screen-sizes.md)

Visualizzare in anteprima e ottimizzare l'interfaccia utente per le dimensioni dello schermo disponibili.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Distribuzione e test](~/android/wear/deploy-test/index.md)

Viene illustrato come distribuire l'app, usare Android a un dispositivo Android con accenti o all'emulatore Android configurato per la scadenza. Include inoltre il debug di suggerimenti e informazioni su come configurare una connessione Bluetooth tra il computer di sviluppo e un dispositivo Android.



## <a name="samples"></a>Esempi

È possibile trovare un numero di [esempi](https://developer.xamarin.com/samples/android/Android%20Wear/) utilizzando accenti Android (o passare direttamente alla [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
          <strong>Esempio</strong>
      </th>
      <th>
          <strong>Descrizione</strong>
      </th>
      <th>
          <strong>schermata di</strong>
      </th>
  </thead>
  <tbody>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/SkeletonWear/">SkeletonWear</a>
      </td>
      <td valign="top">
Un esempio semplice delle nozioni di base dei progetti indossabile, incluse GridViewPager e le notifiche interattive.
      </td>
      <td>
          <img src="Images/skeleton.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/WatchViewStub/">WatchViewStub</a>
      </td>
      <td valign="top">
Una semplice demo del controllo WatchViewStub che rileva forma dello schermo e carica automaticamente il layout corretto.
Verificare il funzionamento del WatchViewStub <b>Resources/layout/main_actvity.xml</b> layout.
      </td>
      <td>
          <img src="Images/watchview.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/RecipeAssistant/">RecipeAssistant</a>
      </td>
      <td valign="top">
Dimostrazione di pagine di notifica usura, sotto forma di passaggi recipe. Le notifiche vengono create in <b>RecipeService.cs</b>.
      </td>
      <td>
          <img src="Images/recipeassist.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/ElizaChat/">ElizaChat</a>
      </td>
      <td valign="top">
Esempio divertente dell'interazione con un Assistente"personale" chiamato Eliza, utilizzando le notifiche degli interattive usura per creare una conversazione che utilizza le risposte precompilate.
      </td>
      <td>
          <img src="Images/eliza.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/GridViewPager/">GridViewPager</a>
      </td>
      <td valign="top">
GridViewPager implementa il modello di navigazione 2D, in cui l'utente passa verticalmente e orizzontalmente per spostarsi tra le opzioni e il contenuto.
      </td>
      <td>
          <img src="Images/gridviewpager.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/monodroid/wear/WatchFace">WatchFace</a>
      </td>
      <td valign="top">
          <b>WatchFace</b> è un tipo di carattere personalizzato espressioni di controllo con stile analogico ora, minuto e secondo mani. Questo esempio viene illustrato come creare un servizio faccia espressioni di controllo che disegna l'ora corrente e la visibilità e la modalità ambiente gestisce gli eventi di modifica. Include un ricevitore di trasmissione che ascolta i cambiamenti del fuso orario e aggiorna automaticamente il tempo di conseguenza.
      </td>
      <td>
          <img src="Images/watchface.png" class="tableimg">
      </td>
  </tr>
  </tbody>
</table>

##  <a name="videos"></a>Video

Controllare i collegamenti che trattano xamarin con usura supportano i video.

<table align="center" border="0" cellpadding="1" cellspacing="1">
    <tr>
        <td>
        <a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/"><img src="Images/video-android-l.png" border="0" /></td>
        <td><a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/">L Android e molto altro ancora</a>
        <br />
L'anteprima per sviluppatori di L Android introdotto moltissime nuove API per gli sviluppatori di sfruttare, tra cui progettazione materiale, notifiche e le nuove animazioni, per citarne alcune.</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=80H8tXByZQc"><img src="Images/video-eyes-ears.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=80H8tXByZQc">C# è in auguri e il punto di vista: Google Glass e accenti Android</a>
        <br />
Calcolo indossabile potrebbe sembrare un elemento da futuro (o un episodio di controllo Gadget), ma molti utenti sono già adottando futuro oggi. Gli sviluppatori c# conoscono e dispongono già di strumenti e le competenze di sfruttare le potenzialità di dispositivi indossabile (da Evolve 2014).</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU"><img src="Images/video-whats-new.png" border="0" /></td>
        <td><a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU">Novità di xamarin</a>
        <br />
        <i>L Android, usura Android, Android TV, Auto Android, progettazione materiali e arte; cosa significa all'utente gli sviluppatori Xamarin? </i> da evolvere 2014.</td>
    </tr>
</table>


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
