---
layout: post
title: "Integrating Climate Approval with Durable Usage Reporting"
slug: "climate-approval-reporting-case-study"
summary: "How I integrated Home Assistant approval requests and persistent usage reports while separating operating-mode observations, delivery acknowledgements and physical actuation."
date: 2026-11-25
categories: portfolio-material
number: 16
tags: [HomeAssistant, Automation, Authorization, DataQuality, Reporting]
portfolio: true
---

## Context

I integrated climate automation with an owner-approval workflow and durable usage reporting around Home Assistant. The engineering challenge was to represent permission, command execution and measured behaviour as separate states, rather than treating an accepted request as proof that the physical action happened.

Reporting introduced a related distinction: time recorded in an operating mode is not energy consumption. A useful report must explain what its inputs support and retain uncertainty when observations are incomplete.

This case study describes implementation and evidence reviewed on **25 November 2026**. It includes no operating programme or installation details.

## Approval-state design

The approval bridge persists pending requests and resolves them into explicit outcomes. Approval, denial and expiry are distinct. The resolution operation checks that the request is still pending, so concurrent or repeated callbacks cannot simply overwrite a completed decision.

Callback handling checks the authorising identity before accepting a decision. The command path is separate from the stored approval result. That distinction matters when interpreting state: an approval record describes authorisation, while successful physical actuation requires additional evidence.

Expiry handling prevents a pending decision from remaining actionable indefinitely. Persistent state also makes request resolution inspectable after the process that created the request is no longer the current execution.

## Reporting design

The reporting workflow turns recorded mode changes into aggregates, applying calendar and time-zone boundaries explicitly. It carries incomplete coverage and unavailable time into the report instead of silently treating them as normal operation.

The measure is **time in recorded operating modes**. It is not a measurement of electrical consumption, compressor runtime, comfort or savings. Keeping that definition visible makes the output easier to interpret and harder to overstate.

Report delivery uses persistent state. A durable reservation helps prevent repeated generation from sending the same report again, while a state for ambiguous delivery avoids blindly treating an uncertain send as a clean retry. These controls manage duplicate risk; they do not guarantee that duplication is impossible under every failure.

## What was observed on 25 November

The review found Home Assistant and the approval bridge running. Persisted request metadata demonstrated expiry handling, and the reporting archive contained aggregates through the preceding day. Existing report records included sent outcomes with delivery identifiers. A recorded report execution and archive execution had completed successfully.

These observations support current operation of the inspected components, completed aggregation work and API-acknowledged report sending. A delivery identifier is not evidence that a report was read. Recorded climate states are also observations supplied by the software, rather than an independent physical measurement.

I distinguish source implementation and observed component operation from acceptance of the complete automation behaviour. The latter needs evidence from the action itself.

## Result and verification boundary

The implemented result combines explicit approval-state handling with persistent, clearly defined usage reports. It gave me practical experience in authorisation checks, atomic state changes, calendar-based aggregation and recovery from uncertain external results.

**Physical actuation following owner approval remains unverified.** The evidence also does not establish temperature accuracy, energy savings or report reading. I do not infer these outcomes from a running service, an approval state or a report delivery identifier.

The useful engineering result is a workflow that records decisions and preserves data limitations, together with completed aggregation and send evidence. Its verification boundary remains visible alongside that result.

## My role and use of AI

I used AI coding assistance for implementation, investigation and documentation. My role was defining the approval and reporting requirements, integrating the components and checking that public conclusions matched the evidence. Home Assistant is the existing automation platform; the case study describes my integration work around it.

## Versione italiana

### Obiettivo

Ho integrato automazioni del clima, approvazione dell'owner e report persistenti attorno a Home Assistant. Ho distinto autorizzazione, invio del comando e comportamento fisico misurato: una richiesta approvata non dimostra da sola che l'azione sia avvenuta.

Anche il reporting richiedeva una definizione precisa. Il tempo registrato in una modalità operativa non misura il consumo energetico. Le conclusioni di questo articolo si riferiscono al riesame del **25 novembre 2026**, senza descrivere programmi operativi o dettagli dell'installazione.

### Stato delle approvazioni

Il bridge salva le richieste in attesa e distingue approvazione, rifiuto e scadenza. La risoluzione controlla che la richiesta sia ancora pendente: callback ripetute o concorrenti non devono sovrascrivere liberamente una decisione già conclusa.

La gestione delle callback verifica l'identità autorizzata. Il percorso del comando resta distinto dall'esito di approvazione memorizzato. Questo permette di leggere correttamente lo stato: autorizzazione e attuazione fisica richiedono prove diverse.

La scadenza evita che una richiesta rimanga utilizzabile indefinitamente. La persistenza permette inoltre di esaminare le decisioni oltre la singola esecuzione che le ha generate.

### Aggregazione e invio dei report

Il reporting ricostruisce aggregati dai cambi di modalità registrati, trattando esplicitamente calendario e fuso orario. Copertura incompleta e tempo non disponibile restano visibili, senza diventare silenziosamente funzionamento ordinario.

La grandezza descritta è il **tempo nelle modalità operative registrate**. Non è una misura di consumi elettrici, attività del compressore, comfort o risparmio. Anche l'invio usa uno stato persistente: una prenotazione duratura aiuta a evitare ripetizioni, mentre un esito ambiguo impedisce di considerare automaticamente sicuro un nuovo tentativo. Sono controlli sul rischio di duplicazione, non una garanzia assoluta.

### Prove e limiti

Il 25 novembre Home Assistant e bridge risultavano in esecuzione. I metadati persistenti mostravano richieste scadute; l'archivio conteneva aggregati fino al giorno precedente e report inviati con identificatori di consegna. Le esecuzioni registrate di archivio e report erano terminate correttamente.

Queste prove sostengono funzionamento dei componenti osservati, aggregazione completata e accettazione API degli invii. Non dimostrano lettura del report o una misura fisica indipendente. Distinguo implementazione e funzionamento dei componenti dall'accettazione dell'intero comportamento automatico, che richiede prove sull'azione stessa.

**L'attuazione fisica dopo approvazione resta non verificata.** Non dichiaro accuratezza della temperatura, risparmi energetici o lettura dei report. Ho usato assistenza AI per sviluppo, indagine e documentazione, definendo requisiti e controllando la corrispondenza tra prove e conclusioni. Home Assistant rimane la piattaforma esistente su cui ho realizzato l'integrazione.
