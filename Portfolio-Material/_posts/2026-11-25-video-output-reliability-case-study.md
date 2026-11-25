---
layout: post
title: "Adding Progress Monitoring and Full-Frame Checks to a Video Pipeline"
slug: "video-output-reliability-case-study"
summary: "How I combined recording progress, bounded recovery and completed full-frame decoding to check whether a video pipeline was producing usable output."
date: 2026-11-25
categories: portfolio-material
number: 14
tags: [Linux, FFmpeg, Reliability, ProcessSupervision, DataIntegrity]
portfolio: true
---

## Context

I built and operated a video-recording pipeline with progress monitoring and output-integrity checks. The engineering objective was to distinguish a running process from a process producing usable output.

That distinction matters in long-running media work. A process can remain present while media time stops advancing. A file can exist and contain a readable header without yielding decodable video. I used different checks for process activity, recording progress and completed-output validity.

The result described here is based on a passive review on **25 November 2026**. It describes software behaviour and verification methods without publishing installation details or media content.

## Progress and recovery

The recorder uses FFmpeg progress information to observe media-time advancement. The supervision logic detects stalled progress and source-clock discontinuities, so it can respond to work that has stopped advancing even if the process has not exited.

Recovery is bounded rather than a permanent wait. The recorder also includes a fallback when audio prevents output, with a path to retry audio availability later. These are implemented recovery behaviours; their presence in source does not mean every failure branch was exercised during the review.

Fragmented output supports the recording workflow, but file production remains a separate observation. I check advancing file metadata as evidence that current work is being written. That observation is useful for liveness, while decoding is needed to establish that completed output is usable.

## Checking completed output

The integrity checker selects completed segments, excluding the current segment that may still be changing. It decodes every video frame and requires a successful process result, an explicit end-of-progress indication and a positive decoded-frame count.

Persisted results distinguish a started check from a completed check. Cached results are tied to file identity, and freshness rules prevent a historical success being treated as indefinitely current. If the segment changes during checking, the result is rejected instead of being assigned to different file contents.

These checks connect an application-level claim to an observable artefact: successful recording needs output that can actually be decoded. They also make it possible to distinguish a previous successful result from work that has not finished.

## What was observed on 25 November

A passive comparison of file metadata showed every observed current writer advancing. The latest observed files were nonempty. Separately, an existing integrity-check receipt showed a completed recent run: its recorded results were successful and contained positive decoded-frame counts. Those results were freshly computed rather than reused from cache.

This combination provides evidence for both current file production and a completed decoder workload. No new recording, decoder workload or failure injection was started for the review, and no media content was inspected or published.

Earlier records include interactive playback acceptance. I keep that historical result separate from the 25 November observations: a previous playback check does not establish today's interface behaviour or every subsequent revision.

## Result and verification boundary

The result is a pipeline whose health assessment includes progress and completed output, with implemented recovery for specific failure conditions. The project gave me practical experience in FFmpeg, process supervision, race-aware file checking and freshness of monitoring data.

The evidence does not establish uninterrupted operation or validate all stored output. It also does not replace current playback acceptance or a controlled recovery test. I use the completed decoder results to support the narrower claim they actually prove: the checked output yielded video frames, and the check itself finished.

## My role and use of AI

I used AI assistance for development, investigation and documentation. My role was choosing the operational requirements, integrating the recorder and checking logic, and reviewing output-based evidence rather than accepting process status alone.

## Versione italiana

### Obiettivo

Ho costruito e gestito una pipeline di registrazione video con controlli sull'avanzamento e sull'integrità dell'output. Il problema era distinguere un processo presente da un processo che produce un risultato utilizzabile.

Un processo multimediale può rimanere in esecuzione mentre il tempo del video smette di avanzare. Anche un file con un'intestazione leggibile può non produrre frame decodificabili. Ho quindi distinto attività del processo, avanzamento della registrazione e validità dell'output completato. Le osservazioni qui riportate si riferiscono al **25 novembre 2026**.

### Monitoraggio e recupero

Il recorder usa le informazioni di avanzamento di FFmpeg. La supervisione riconosce il tempo multimediale bloccato e le discontinuità dell'orologio sorgente, senza affidarsi soltanto all'uscita del processo.

Il recupero prevede attese limitate. Quando l'audio impedisce la produzione dell'output, esiste un comportamento alternativo con successivo tentativo di recupero dell'audio. Sono capacità implementate: la lettura del codice non dimostra che ogni ramo sia stato esercitato nella verifica descritta.

La scrittura di file frammentati sostiene il flusso di registrazione. L'avanzamento dei metadati dei file costituisce una prova di attività corrente; la decodifica risponde alla domanda diversa se un risultato completato sia utilizzabile.

### Controllo dell'output

Il verificatore seleziona segmenti completati ed esclude quello ancora in scrittura. Decodifica tutti i frame e richiede esito positivo, indicazione esplicita di fine elaborazione e un numero positivo di frame decodificati.

I risultati persistenti distinguono avvio e completamento del controllo. La cache è legata all'identità del file; la verifica della freschezza impedisce di trattare un vecchio successo come sempre attuale. Se il segmento cambia durante il controllo, il risultato viene rifiutato.

### Prove e limiti

Il campionamento passivo del 25 novembre mostrava l'avanzamento di tutti i writer osservati e file recenti non vuoti. Una ricevuta già presente attestava anche un ciclo di decodifica recente completato: tutti i risultati registrati erano positivi, con frame effettivi, e non provenivano dalla cache.

Queste prove riguardano produzione corrente dei file e completamento di un controllo dell'output. Non ho avviato nuovi carichi di registrazione o decodifica, provocato guasti o esaminato contenuti multimediali. Una precedente accettazione del playback resta storica e non certifica l'interfaccia attuale.

Il risultato è una valutazione dello stato legata al progresso e all'output. Non dimostra funzionamento ininterrotto, validità di tutto il materiale memorizzato o recupero da ogni guasto. Ho usato assistenza AI per sviluppo, indagine e documentazione, definendo requisiti e valutando prove coerenti con le conclusioni dichiarate.
