---
layout: post
title: "Moving the Hermes Agent Gateway to Ubuntu"
slug: "hermes-ubuntu-migration-case-study"
summary: "How I adapted an open-source assistant deployment for Ubuntu, checked installed components and service health, and kept deployment evidence separate from feature acceptance."
date: 2026-11-25
categories: portfolio-material
number: 12
tags: [Linux, Ubuntu, Automation, ServiceOperations, Verification]
portfolio: true
---

## Context

I adapted and deployed **Hermes Agent, the open-source project by Nous Research**, on Ubuntu. My contribution was the migration and integration work around the existing agent: deployment support, dependency handling, calendar integration and operational verification. I did not create the Hermes core or its underlying messaging transport.

The engineering problem was making a useful assistant deployment understandable and maintainable. A successful installation command would establish very little by itself. I needed to know which service was running, whether it continued reporting health, and whether selected installed components matched the implementation I intended to deploy.

This case study describes evidence reviewed on **25 November 2026**, rather than an automatically updated service-status page.

## Implementation decisions

I used managed Linux services so that process lifecycle and service results could be inspected independently of an interactive terminal. Runtime health information provides another observation: a process can exist while the application has stopped making useful progress. Service state and a fresh application heartbeat answer different questions, so I kept both.

Dependency handling was part of the migration rather than something to infer from a green service indicator. I retained explicit distinctions between the gateway's own availability and the optional capabilities it can request. Some optional capabilities still depend on workstation services. Moving the gateway to Ubuntu does not establish that every feature is independent of those dependencies.

For selected deployed components, I compared cryptographic file hashes with the intended source. This checks the bytes installed on disk. It is narrower than certifying an entire deployment, and it does not prove which version of every module an existing process has already loaded.

## What the evidence established

The 25 November review found the gateway service running, a fresh application heartbeat and connected states reported by its integrations. Selected installed components matched their source counterparts. Together, these observations support the claim that the Ubuntu deployment was operating when inspected.

They do not establish a new end-to-end delivery result. A connection state is an application report; successful delivery requires evidence from the operation itself. Similarly, a clean service result for a maintenance task is useful operational evidence, but a recovery claim needs a recovery test.

Earlier migration records include deployment and recovery checks. I treat those as dated historical acceptance, rather than continually renewing them whenever the service is still running. The conversation and calendar case study examines a separate recorded transaction and the limits of applying it to later revisions.

## Result and verification boundary

The result is an operating Ubuntu gateway with inspectable service state and selected component checks. The work strengthened my practical experience with Linux service management, release verification and the distinction between component health and a completed user task.

I do not claim complete workstation independence, operation without external services, uninterrupted availability or acceptance of every optional feature. Those conclusions would require broader evidence than this review collected. The useful lesson is to choose a verification method that matches the claim: inspect service health for operation, compare installed bytes for deployment identity, and inspect completed work for feature acceptance.

## My role and use of AI

I used AI coding assistance for implementation, investigation and drafting. My role included choosing requirements, integrating the components, deciding which checks mattered and reviewing their results. Hermes Agent remains Nous Research's upstream project; this case study describes my adaptation and operation of it.

## Versione italiana

### Contesto e contributo

Ho personalizzato e distribuito su Ubuntu **Hermes Agent, progetto open source di Nous Research**. Il mio contributo riguarda migrazione, integrazioni, gestione delle dipendenze e verifica operativa. Il core dell'agente e il trasporto di messaggistica di base appartengono al progetto upstream.

Il problema concreto era rendere la distribuzione comprensibile e gestibile. Il completamento di un comando di installazione non dimostra che l'applicazione continui a funzionare. Servivano osservazioni distinte sul processo, sullo stato applicativo e sui componenti effettivamente installati. Le conclusioni di questo articolo si riferiscono alla verifica del **25 novembre 2026**.

### Scelte implementative

Ho utilizzato servizi Linux gestiti per poter controllare il ciclo di vita dei processi e gli esiti delle esecuzioni. Ho affiancato a questi dati un heartbeat applicativo: un processo presente e un'applicazione che continua a segnalare attività sono evidenze diverse.

Ho mantenuto esplicite anche le dipendenze delle capacità opzionali. Il gateway su Ubuntu non rende automaticamente indipendenti tutte le funzioni: alcune richiedono ancora servizi della workstation. Questo limite influenza il significato di uno stato positivo e il modo in cui descrivo il risultato.

Per componenti selezionati ho confrontato gli hash crittografici dei file installati con quelli del sorgente previsto. Il confronto verifica i byte presenti su disco. Non equivale a certificare l'intera distribuzione né a conoscere la versione di ogni modulo già caricato da un processo.

### Risultati e limiti

La verifica del 25 novembre ha osservato il gateway in esecuzione, un heartbeat fresco e stati di connessione dichiarati dalle integrazioni. I componenti selezionati coincidevano con il sorgente. Queste osservazioni sostengono il funzionamento della distribuzione nel momento del controllo.

Non dimostrano una nuova consegna completa. Per questa serve l'esito dell'operazione specifica; uno stato di connessione, da solo, non basta. Anche un'attività di manutenzione terminata correttamente richiede una prova diversa prima di diventare una garanzia di recuperabilità.

Le verifiche precedenti di migrazione e recupero restano prove storiche datate. Non le considero rinnovate dalla sola presenza del servizio. Il risultato attuale è un gateway Ubuntu operativo e verificabile, senza promesse di disponibilità continua, indipendenza completa dalla workstation o funzionamento senza servizi esterni.

### Metodo di lavoro

Ho usato assistenza AI per codice, indagine e scrittura. Ho definito i requisiti, integrato i componenti e valutato le prove. Il progetto mi ha fatto esercitare nella gestione dei servizi Linux e nella scelta di controlli coerenti con il risultato dichiarato: stato del servizio, identità dei file e completamento di un'attività rispondono a domande differenti.
