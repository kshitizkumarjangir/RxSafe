# RxSafe

## Prescription Safety Companion

Powered by **MedGemma (google/medgemma-1.5-4b-it)** | Runs fully offline | No data leaves the system

[Watch the demo on YouTube](https://youtu.be/Djx7iYyfH8U?si=lh8fiRtJYOZ8H40l)

---

## Problem Statement

Every day, millions of patients leave clinics holding prescriptions they don't fully understand—unaware of how new medicines interact with what they are already taking, what side effects to watch for, or what to do if a medicine is unavailable.  
This gap between prescription and patient understanding is not a minor inconvenience. It is a **patient safety crisis**.

| 125M+ | 50% | 1.5 Billion |
|-------|-----|-------------|
| adverse drug events globally per year | of patients do not take medications as prescribed | prescriptions written annually in India alone |

The problem is acute in low-resource settings where patients may have limited health literacy, doctors have limited consultation time, and the pharmacist is the only point of contact after discharge.  
**RxSafe** is designed for this context: a tool that works offline on clinic hardware and puts an intelligent safety companion in every patient's hands.

---

## Overall Solution

### What RxSafe Does

RxSafe is a two-phase intelligent prescription companion.  
It first **educates the patient** in plain language about every medicine on their prescription, then performs a **personalized safety check** against their health profile.

---

### Phase 1 - Patient Education (Primary Objective)

For each prescribed medicine, MedGemma generates:

- What it is – plain-language explanation of the drug class and mechanism  
- Why it was prescribed – contextualized to the diagnosis or doctor's notes  
- How to take it – timing, food interactions, what to do if a dose is missed  
- Common side effects – described without alarming medical jargon  
- What to avoid – foods, activities, and other substances to steer clear of  
- Pharmacist guidance – what to tell the pharmacist if the medicine is out of stock, including the generic name and drug class so an equivalent can be sourced  

---

### Phase 2 - Personalized Safety Check (Secondary Objective)

The patient inputs their existing medications, known conditions, and allergies. MedGemma flags:

- **Drug-allergy conflicts** (e.g., Amoxicillin prescribed to a Penicillin-allergic patient → CRITICAL)  
- **Drug-drug interactions** between new and existing medications  
- **Drug-condition contraindications** (e.g., NSAIDs with kidney disease)  
- **Dosage concerns** relative to standard published guidelines  

---

### Why MedGemma - Not a Generic LLM or Database

| Feature | Advantage |
|---------|-----------|
| Multimodal capability | Reads prescription images directly—extracting medicines from handwritten and printed prescriptions where OCR alone fails |
| Open-weight advantage | Runs locally with zero internet dependency—prescription data never leaves the premises |
| Customizability | Fine-tuned on regional formularies (e.g., CDSCO-approved Indian brand names, regional language support) |
| Edge deployment | 4-bit quantized model runs on a single T4 GPU at 3–5 sec latency |

---

### Out-of-the-Box MedGemma Performance

- Accurate extraction of medicines, dosages, frequency, route, and diagnosis from prescription images  
- Patient-friendly per-medicine explanations: purpose, usage, side effects, food interactions, and tips  
- Drug-interaction and allergy conflict detection against patient-reported profiles  
- Pharmacist-ready guidance for unavailable medicines (generic name, drug class, escalation criteria)  

---

### No Internet & No Data Leaves the System

MedGemma’s weights are downloaded once during setup and stored locally.  
At runtime, the model runs entirely offline—prescriptions never leave the building.

---

## Technical Details

### Architecture and Pipeline

| Step | Description |
|------|-------------|
| **INPUT** | Patient uploads prescription image + fills profile (conditions, allergies, current medications) |
| **VISION CALL** | MedGemma 4B multimodal extracts medicines, dosages, frequency, route, diagnosis as structured JSON |
| **VALIDATE** | Python pre-validation: empty patient profile → CLEAR |
| **TEXT CALL** | MedGemma 4B text generates patient education cards + safety check |
| **OUTPUT** | UI renders: extracted medicines table, per-medicine education cards, color-coded safety flags |

---

### Two Model Calls

1. **Vision Call** – Prescription image → structured JSON  
2. **Text Call** – JSON + patient profile → education cards + safety check  

Merging education and safety into one text call saves **30–40 seconds per analysis**.

---

### Application Stack

| Component | Details |
|-----------|---------|
| Model | google/medgemma-1.5-4b-it (4-bit quantised via BitsAndBytesConfig) |
| Framework | HuggingFace Transformers + PyTorch (bfloat16) |
| UI | Gradio Blocks with custom CSS |
| Environment | Kaggle Notebook, NVIDIA T4 GPU, Python 3.12 |
| External APIs | None – fully self-contained |
| Code | Available as reproducible Kaggle Notebook |

---

## Future Opportunities

| Opportunity | Description |
|-------------|-------------|
| QLoRA Fine-tuning | Fine-tune MedGemma on CDSCO registry, local dosing conventions, and drug-interaction data |
| Lab Report Integration | Upload lab reports alongside prescriptions for joint reasoning |
| Multilingual Output | Patient education in Hindi, Tamil, Bengali, etc. |
| EHR / FHIR Integration | Connect to clinic EHR systems via FHIR API |
| On-Device Inference | Run MedGemma 2B INT4 fully on-device with mobile NPUs |

---

## Disclaimer

RxSafe is a **decision-support tool only**.  
All outputs must be confirmed with a qualified doctor or pharmacist before any change to medication.  
RxSafe does not diagnose, prescribe, or replace clinical judgment.
