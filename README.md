# Tool Diagnostico — Polipi Nasosinusali (CRSwNP) v2.0

Strumento di supporto diagnostico per la classificazione endotipica dei polipi nasosinusali secondo **EPOS 2020**, con valutazione della candidabilità alle terapie biologiche e identificazione della triade AERD/N-ERD.

---

## Descrizione

File HTML autonomo (`index_v2.html`), eseguibile direttamente nel browser senza dipendenze esterne né connessione internet (eccetto il caricamento dei font Google, con fallback su Georgia/system-sans).

Sviluppato per uso interno in Anatomia Patologica. Non sostituisce il giudizio morfologico del patologo sul vetrino.

---

## Funzionalità

### Sezione 0 — Dati Caso
- Numero accession / numero di caso
- Data referto
- Sede di prelievo

Questi campi vengono inclusi nell'intestazione del referto generato.

### Sezione 1 — Morfologia EE (obbligatoria)
- **Conteggio eosinofili**: input numerico libero (eos/HPF, 400×), con warning dinamico in tempo reale nelle zone critiche:
  - 8–12 eos/HPF → zona grigia del cut-off EPOS
  - 45–55 eos/HPF → area intorno alla soglia prognostica osservazionale
  - 68–72 eos/HPF → area intorno alla soglia "molto severo"
- **Metodo di conteggio**: con alert visivo se selezionato "hotspot singolo" (sovrastima sistematica)
- **Stroma**: edematoso mixoide / fibroso / misto
- **Infiltrato predominante**: eosinofili / neutrofili / plasmacellule / linfociti / misto
- **Epitelio**: respiratorio integro / metaplasia squamosa / iperplasia ghiandolare / atipia
- **Reperti aggiuntivi** (checkbox):
  - Cristalli di Charcot-Leyden
  - Degranulazione eosinofila extracellulare massiva
  - Mucina allergica
  - Funghi
  - Centri germinativi prominenti
  - Microascessi neutrofili
  - Depositi di fibrina
  - Remodeling stromale fibroblastico marcato
  - Vasculite *(con alert esplicito: escludere GPA/EGPA)*
  - Distribuzione disomogenea / pattern patchy *(nota nel referto sull'interpretabilità del conteggio)*

### Sezione 2 — Dati Clinici (opzionale)
- Bilateralità clinica
- Asma comorbida
- N-ERD / intolleranza a FANS
- Anosmia/iposmia
- Eosinofili ematici (con cut-off 150 e 250/μL)
- IgE totali
- Recidiva post-FESS
- Steroidoterapia sistemica recente *(nota di sottostima nel referto)*

### Sezione 3 — Criteri Candidabilità Biologici EPOS 2020 (opzionale)
Checklist dei 5 criteri ufficiali:
1. SNOT-22 ≥ 20 nonostante terapia massimale
2. ≥ 2 cicli di steroidi sistemici negli ultimi 2 anni (o controindicazione)
3. Recidiva post-FESS entro 12 mesi
4. Anosmia/iposmia grave (VAS ≤ 2/10)
5. Comorbidità Type 2 significativa

Score calcolato (0–5) con tre livelli di output:
- **≥ 3/5** → potenzialmente candidabile
- **2/5** → borderline, valutazione multidisciplinare
- **≤ 1/5** → criteri insufficienti

Suggerimento biologico (dupilumab vs mepolizumab) basato su profilo eosinofilia ematica e asma.

---

## Output Generato

### Classificazione endotipica
- **Type 2** (≥ 10 eos/HPF) vs **non-Type 2** (< 10 eos/HPF) secondo EPOS 2020
- Alert triade AERD/N-ERD se CRSwNP eosinofilica + asma + N-ERD (triade di Samter)

### Stratificazione eosinofilia
| Range | Classificazione | Note |
|---|---|---|
| < 10 eos/HPF | Non-Type 2 | Prognosi favorevole |
| 10–49 eos/HPF | Type 2 moderato | Rischio moderato recidiva |
| 50–69 eos/HPF | Type 2 severo | Soglia osservazionale (non standardizzata) |
| ≥ 70 eos/HPF | Type 2 molto severo | Soglia osservazionale (non standardizzata) |

> **Nota metodologica**: il cut-off ≥ 10 eos/HPF è l'unico con consenso internazionale (EPOS 2020). Le soglie 50 e 70 derivano da studi osservazionali con variabilità metodologica e non vanno interpretate come parametri oncologici.

### Referto testuale
Testo strutturato pronto per copia nel LIS, con:
- Intestazione caso (se compilata)
- Diagnosi
- Valutazione istologica dettagliata
- Correlazione clinico-patologica
- Classificazione finale
- Nota patologica medico-legale standard

### Azioni disponibili nell'output
- **📋 Copia testo** — copia il referto negli appunti
- **🖨️ Stampa / Salva PDF** — stampa ottimizzata A4 (nasconde form e pulsanti)
- **🔄 Nuovo Caso** — reset completo con scroll alla cima

---

## Logica Diagnostica

```
Eosinofili ≥ 10/HPF?
    ├── SÌ → Endotipo Type 2
    │         ├── Asma + N-ERD? → Alert triade AERD
    │         ├── Criteri biologici ≥ 3/5? → Potenzialmente candidabile
    │         └── Stratificazione severità (10/50/70)
    └── NO → Endotipo non-Type 2
              └── Non indicato biologico anti-IL-4/13 / anti-IL-5
```

---

## Avvertenze Tecniche

- **Hotspot**: se selezionato come metodo, il tool mostra un warning visivo e include una nota nel referto. Il valore inserito potrebbe sovrastimare l'infiltrato medio.
- **Steroidoterapia recente**: segnalata nel referto come causa di potenziale sottostima del conteggio eosinofili.
- **Pattern patchy**: se spuntato, il referto include nota esplicita sull'interpretabilità del valore di conta.
- **Vasculite**: checkbox con alert immediato per esclusione di GPA / EGPA / patologia ANCA-associata.

---

## Riferimenti

1. Fokkens WJ et al. *European Position Paper on Rhinosinusitis and Nasal Polyps 2020.* Rhinology. doi:[10.4193/Rhin20.600](https://doi.org/10.4193/Rhin20.600)
2. Toro MDC et al. *Achieving the best method to classify Eosinophilic Chronic Rhinosinusitis.* Rhinology 2021. doi:[10.4193/Rhin20.644](https://doi.org/10.4193/Rhin20.644)
3. McHugh T et al. *High tissue eosinophilia as a marker for CRS subtype.* Int Forum Allergy Rhinol 2020. doi:[10.1002/alr.22517](https://doi.org/10.1002/alr.22517)
4. Laidlaw TM, Mullol J et al. *N-ERD and aspirin-exacerbated respiratory disease.* J Allergy Clin Immunol Pract 2021. doi:[10.1016/j.jaip.2021.01.013](https://doi.org/10.1016/j.jaip.2021.01.013)
5. Bachert C et al. *Dupilumab efficacy in severe CRSwNP.* J Allergy Clin Immunol 2019. doi:[10.1016/j.jaci.2019.08.016](https://doi.org/10.1016/j.jaci.2019.08.016)

---

## Disclaimer

Questo tool è un ausilio diagnostico. La diagnosi finale deve essere formulata dal patologo sulla base dell'esame morfologico completo, della correlazione clinico-patologica e del giudizio professionale. **L'istologia certifica l'endotipo; la candidabilità al trattamento biologico richiede valutazione clinica multidisciplinare.**

---

*Sviluppato per uso interno — SC Anatomia Patologica*
*Versione 2.0 — Febbraio 2026*
