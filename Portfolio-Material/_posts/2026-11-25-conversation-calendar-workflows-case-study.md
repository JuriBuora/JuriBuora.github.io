---
layout: post
title: "Building Conversation-to-Calendar Workflows with Readback Checks"
slug: "conversation-calendar-workflows-case-study"
summary: "How I integrated bounded conversation context, date validation, checked calendar writes and persistent notification retries around Hermes Agent."
date: 2026-11-25
categories: portfolio-material
number: 13
tags: [Automation, CalendarIntegration, DataValidation, APIs, Verification]
portfolio: true
---

## Context

I built conversation and calendar integrations around **Nous Research's open-source Hermes Agent**. The aim was to turn relevant conversational context into carefully checked calendar actions, with a record of what happened when an operation needed to be retried.

The difficult part is the transition from language to a durable action. A plausible interpretation can still contain the wrong date, duration or participant. A successful API call can also leave uncertainty about the resulting record. I treated extraction, validation, writing, readback and notification as separate stages.

This account distinguishes implementation reviewed on **25 November 2026** from a completed transaction recorded on **15 November 2026**. No conversation content or calendar-entry details are reproduced.

## Implementation decisions

Conversation capture uses bounded context rather than an unlimited history. Recent context, speaker information and existing calendar context help reconstruct a proposal, while validation checks whether the proposed participants and date or time expressions are supported by the input. The implementation includes conditions for postponing processing while a conversation is still developing.

The calendar adapter validates event duration and handles time-zone rules explicitly. Ambiguous or nonexistent local times around daylight-saving transitions are rejected instead of silently converted into an unintended appointment.

For calendar writes, deterministic identifiers support duplicate detection and recovery. A readback then compares the stored record with expected fields, including timing and the application's marker. An API success response and a verified stored result are separate pieces of evidence.

Notification work is persisted with application state so that a later execution can retry it. This avoids relying only on transient process memory. Persisted retries improve recovery, but do not establish that every notification will appear only once or that an API acknowledgement means someone read it.

## Evidence and acceptance

The 25 November review found the relevant workflows registered as enabled, recent executions recorded as successful, and selected installed files matching the inspected source. Aggregate state showed that new speaker-related fields were being written. These observations support deployment and execution of parts of the implementation; they do not measure conversational interpretation quality.

A separate acceptance record dated **15 November** documents a genuine owner-requested calendar write, a single-result readback and notification API acceptance. It verifies that particular transaction on its then-current implementation. It does not establish a complete conversation-to-action exchange or certify the revisions reviewed on 25 November.

**The newer conversational features remain under normal-use validation.** Historical acceptance and focused synthetic checks are useful evidence, but neither substitutes for observing the revised workflow complete an appropriate genuine request.

## Result and verification boundary

The implemented workflow has explicit checks between a model's proposed interpretation and a calendar operation, plus durable notification state for recovery. This was practical work in API integration, data validation, time handling and retry behaviour.

I do not claim that every interpretation is correct, that every notification is read or that retries cannot duplicate an effect. I also do not describe the extraction path as entirely local or available without external services. The defensible result is a checked integration with a dated successful transaction, while acceptance of the newer conversational behaviour continues through normal use.

## My role and use of AI

I used AI assistance for implementation, investigation and writing. My contribution was adapting and integrating the workflows, defining validation requirements and reviewing evidence. Nous Research provides Hermes Agent and its upstream messaging foundations; I did not build those foundations from scratch.

## Versione italiana

### Problema e obiettivo

Ho sviluppato integrazioni conversazionali e calendario attorno a **Hermes Agent, progetto open source di Nous Research**. L'obiettivo era trasformare contesto pertinente in azioni sul calendario sottoposte a controlli, mantenendo uno stato persistente per le operazioni da ritentare.

Il passaggio dal linguaggio a una modifica duratura richiede più di una risposta plausibile. Data, durata e partecipanti possono essere interpretati male; anche una chiamata API riuscita va distinta dalla verifica del dato memorizzato. Per questo ho separato estrazione, validazione, scrittura, rilettura e notifica.

### Implementazione

La raccolta conversazionale usa un contesto limitato. Informazioni sul parlante, contesto recente e dati calendario già disponibili aiutano a ricostruire una proposta. I controlli verificano se partecipanti e riferimenti temporali trovano sostegno nell'input. Sono previste condizioni per rimandare l'elaborazione quando il dialogo sta ancora evolvendo.

L'adapter calendario controlla la durata e tratta esplicitamente le regole del fuso orario. Gli orari locali ambigui o inesistenti durante il cambio dell'ora vengono rifiutati. Identificatori deterministici aiutano a riconoscere un'operazione già esistente; una rilettura confronta il risultato memorizzato con i campi attesi, inclusi tempi e marcatura applicativa.

Lo stato delle notifiche viene salvato insieme allo stato applicativo, così un'esecuzione successiva può riprendere il lavoro. Questa persistenza migliora il recupero, ma non garantisce unicità assoluta degli effetti o lettura della notifica.

### Prove datate e risultato

Il controllo del **25 novembre 2026** ha osservato flussi abilitati, esecuzioni recenti registrate come riuscite e file installati selezionati coincidenti con il sorgente. Lo stato aggregato mostrava anche la scrittura di nuovi campi relativi al parlante. Sono prove di distribuzione ed esecuzione di parti del sistema, non una misura della qualità di comprensione.

Una ricevuta del **15 novembre 2026** documenta una richiesta autentica dell'owner, una scrittura sul calendario, una rilettura con risultato singolo e l'accettazione API della notifica. Vale per quella transazione e per l'implementazione di allora. Non certifica uno scambio conversazionale completo né le revisioni successive.

**Le nuove capacità conversazionali restano in validazione nell'uso normale.** Il risultato dimostrabile è un'integrazione con controlli e una transazione storica riuscita. Non prometto correttezza universale, assenza assoluta di duplicati, lettura delle notifiche o elaborazione interamente locale senza servizi esterni.

### Contributo personale

Ho usato assistenza AI per codice, indagine e testo. Il mio lavoro comprende integrazione, definizione dei controlli e valutazione dei risultati. Attribuisco a Nous Research il core Hermes e le basi di messaggistica upstream. Il valore tecnico del progetto è avere reso espliciti i passaggi e le prove necessari tra interpretazione e modifica di un dato.
