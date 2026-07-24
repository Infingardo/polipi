# Tool Diagnostico — Polipi Nasosinusali (CRSwNP) v2.16

Strumento di supporto diagnostico per la classificazione endotipica dei polipi nasosinusali secondo **EPOS 2020**, con valutazione della candidabilità alle terapie biologiche e generazione automatica del testo referto in stile LIS.

---

## Descrizione

File HTML autonomo (`index.html`), eseguibile direttamente nel browser senza dipendenze esterne né connessione internet (eccetto il caricamento dei font Google, con fallback su Georgia/system-sans).

Sviluppato per uso interno in Anatomia Patologica — SC FBF.  
**Non sostituisce il giudizio morfologico del patologo sul vetrino.**

---

## Funzionalità

### Tipo di campione (primo selettore)

Il tool si adatta al tipo di reperto prima ancora di chiedere altro:

| Opzione | Comportamento |
|---|---|
| **Polipo naso-sinusale** | Classificazione endotipica EPOS 2020 completa, biologici, score candidabilità |
| **Frammenti di mucosa respiratoria** | Descrizione morfologica; classificazione EPOS e sezioni cliniche disattivate |
| **REAH** | Apertura narrativa dedicata; classificazione EPOS e sezioni cliniche disattivate |
| **Amartoma siero-mucinoso** | Apertura narrativa dedicata; classificazione EPOS e sezioni cliniche disattivate |

---

### Sezione 1 — Dati Morfologici (obbligatoria per polipi)

**Cellula predominante dell'infiltrato** — domanda iniziale che determina il percorso:

- **Eosinofili / Misto / Neutrofili** → compare il blocco conteggio eos/HPF + metodo
- **Plasmacellule / Linfociti** → blocco eos nascosto; classificazione non-Type 2 con alert diagnostico

**Conteggio eosinofili** (solo se infiltrato eosinofilico/misto):
- Input numerico in eos/HPF (400×)
- Warning dinamico in tempo reale nelle zone critiche: 8–12, 45–55, 68–72 eos/HPF
- Selezione metodo di conteggio con alert per hotspot (sovrastima sistematica)

**Altri parametri morfologici:**
- Stroma: edematoso mixoide / fibroso / misto
- Epitelio: integro / metaplasia squamosa / iperplasia ghiandolare / atipia ⚠️
- Reperti aggiuntivi (checkbox): Charcot-Leyden, degranulazione eosinofila, mucina allergica, funghi, centri germinativi, microascessi, fibrina, remodeling, vasculite ⚠️, pattern patchy

---

### Sezione 2 — Dati Clinici (opzionale, solo polipi)

- Bilateralità clinica ⚠️
- Asma comorbida
- N-ERD / intolleranza a FANS (ASA)
- Anosmia/iposmia
- Eosinofili ematici (cut-off 150 e 250/μL)
- IgE totali
- Recidiva post-FESS
- Steroidoterapia sistemica recente (con nota di potenziale sottostima nel referto)

---

### Sezione 3 — Criteri Candidabilità Biologici EPOS 2020 (opzionale, solo polipi)

Checklist dei 5 criteri ufficiali:

1. SNOT-22 ≥ 20 nonostante terapia massimale
2. ≥ 2 cicli di steroidi sistemici negli ultimi 2 anni (o controindicazione)
3. Recidiva post-FESS entro 12 mesi
4. Anosmia/iposmia grave (VAS ≤ 2/10)
5. Comorbidità Type 2 significativa

Score 0–5 con tre livelli: ≥3 potenzialmente candidabile · 2 borderline · ≤1 insufficiente.

---

## Red Flag Diagnostiche

Il tool interrompe o modifica l'output in presenza di reperti che rendono inappropriata una diagnosi automatica di polipo infiammatorio:

| Reperto | Effetto |
|---|---|
| **Vasculite** | 🔴 Red flag critica — classificazione sospesa, biologici disattivati |
| **Atipia/displasia epiteliale** | 🔴 Red flag critica — classificazione sospesa, biologici disattivati |
| **Unilateralità clinica** | ⚠️ Alert — diagnosi prudente, nota subordinazione biologici |
| **Funghi + mucina allergica** | ⚠️ Alert AFRS — correlare con imaging e colorazioni speciali |
| **Infiltrato plasmacellulare/linfocitario** | ⚠️ Alert — non-Type 2, diagnosi differenziali (IgG4-RD, plasmocitoma, MALT, ecc.) |
| **Profilo neutrofilico** | ⚠️ Nota — cautela nell'interpretazione dell'eosinofilia accessoria |

In presenza di red flag critiche (vasculite, atipia):
- Il badge di classificazione endotipica viene sospeso
- La sezione biologici viene disattivata
- Compare una sezione "Diagnosi Differenziali da Considerare" contestuale al reperto

---

## Logica Classificativa

```
Tipo campione?
├── Polipo
│     Cellula predominante?
│     ├── Plasmacellule / Linfociti → non-Type 2 + alert ddx
│     └── Eosinofili / Misto / Neutrofili
│           Red flag critica? (vasculite / atipia)
│           ├── SÌ → classificazione sospesa, biologici off
│           └── NO
│                 Eosinofili ≥ 10/HPF?
│                 ├── SÌ → Endotipo Type 2
│                 │         Asma + N-ERD? → alert triade AERD
│                 │         Criteri biologici ≥ 3/5? → potenzialmente candidabile
│                 │         Stratificazione: 10–49 / 50–69 / ≥70 eos/HPF
│                 └── NO  → Endotipo non-Type 2
│                             Biologici: razionale debole
├── Mucosa respiratoria → descrizione morfologica, EPOS off
├── REAH → apertura narrativa REAH, EPOS off
└── Amartoma siero-mucinoso → apertura narrativa amartoma, EPOS off
```

---

## Output

### Badge classificazione
Visualizzazione immediata dell'endotipo con colore dedicato (blu Type 2, grigio non-Type 2, neutro se sospeso).

### Stratificazione eosinofilia (solo polipi Type 2)

| Range | Classificazione | Nota |
|---|---|---|
| < 10 eos/HPF | Non-Type 2 | — |
| 10–49 eos/HPF | Type 2 moderato | Soglia EPOS 2020 |
| 50–69 eos/HPF | Type 2 elevato | Soglia osservazionale (Toro et al. 2021) |
| ≥ 70 eos/HPF | Type 2 molto elevato | Soglia osservazionale |

> Le soglie ≥50 e ≥70 derivano da studi osservazionali e non vanno interpretate come parametri oncologici né soglie obbliganti.

### Testo referto (stile LIS)
Prosa narrativa strutturata, pronta per copia nel LIS:

- Apertura con etichetta campione (es. `A, B)`) e riferimento plurale adattivo (*Nel campione / In entrambi i campioni / Nei campioni esaminati*)
- Descrizione morfologica in forma narrativa con negazioni esplicite
- Correlazione clinico-patologica (se dati clinici compilati)
- Riga finale: `Inquadramento EPOS 2020: ...` (solo per polipi)

### Azioni disponibili
- 📋 Copia testo negli appunti
- 🖨️ Stampa / Salva PDF (ottimizzata A4, nasconde form e pulsanti)
- 🔄 Nuovo Caso (reset completo)

---

## Note Metodologiche

- **Hotspot**: se selezionato come metodo, il referto include avvertenza di potenziale sovrastima.
- **Steroidoterapia recente**: segnalata nel referto come causa di potenziale sottostima del conteggio eosinofili.
- **Pattern patchy**: il referto specifica che il valore si riferisce alle aree a maggiore infiltrazione.
- **Cut-off ≥10 eos/HPF**: unico con consenso internazionale (EPOS 2020). Le soglie superiori sono orientative.
- **Eosinofilia ematica**: orientante ma non sufficiente da sola per la scelta del biologico; non sostituisce la valutazione clinica.

---

## Riferimenti

1. Fokkens WJ et al. *European Position Paper on Rhinosinusitis and Nasal Polyps 2020.* Rhinology. [doi:10.4193/Rhin20.600](https://doi.org/10.4193/Rhin20.600)
2. Toro MDC et al. *Achieving the best method to classify Eosinophilic Chronic Rhinosinusitis.* Rhinology 2021. [doi:10.4193/Rhin20.644](https://doi.org/10.4193/Rhin20.644)
3. McHugh T et al. *High tissue eosinophilia as a marker for CRS subtype.* Int Forum Allergy Rhinol 2020. [doi:10.1002/alr.22517](https://doi.org/10.1002/alr.22517)
4. Laidlaw TM, Mullol J et al. *N-ERD and aspirin-exacerbated respiratory disease.* J Allergy Clin Immunol Pract 2021. [doi:10.1016/j.jaip.2021.01.013](https://doi.org/10.1016/j.jaip.2021.01.013)
5. Bachert C et al. *Dupilumab efficacy in severe CRSwNP.* J Allergy Clin Immunol 2019. [doi:10.1016/j.jaci.2019.08.016](https://doi.org/10.1016/j.jaci.2019.08.016)

---

## Disclaimer

Questo tool è un ausilio diagnostico. La diagnosi finale deve essere formulata dal patologo sulla base dell'esame morfologico completo, della correlazione clinico-patologica e del giudizio professionale. L'istologia fornisce supporto morfologico alla classificazione endotipica, da integrare con dati clinici, endoscopici e laboratoristici. La candidabilità al trattamento biologico richiede valutazione clinica multidisciplinare.

---

*SC Anatomia Patologica — Fatebenefratelli, Melloni e Territorio*  
*Versione 2.14 — Maggio 2026*
