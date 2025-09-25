# HE_trigger_warning
 Projekat za nadzorno-upravljacku aplikaciju trigger-warning sistema hidroelektrane

# README – CODESYS projekat (SCADA + MIL simulacija + alarmi) 

## 0) Preduslovi
- **CODESYS 3.5 SPxx** (Development System).
- Target za testiranje bez PLC-a: **CODESYS Control Win V3** (ili bilo koji *SoftPLC*).
- Instalirana biblioteka **Standard** (dodaje se u Library Manager).

---

## 1) Otvaranje postojećeg projekta
Ako imaš `.project` ili `.project.archive`:
1. Pokreni CODESYS → **File → Open…** → izaberi fajl.
2. Ako traži update uređaja ili biblioteka → **Yes** ili **Install missing**.

Ako imaš samo PLCopenXML/ST fajlove → kreni od **2. Kreiranje prazne aplikacije**.

---

## 2) Kreiranje prazne aplikacije
1. **File → New Project… → Standard Project**.
2. Izaberi target: **CODESYS Control Win V3 (x64)** ili drugi runtime.
3. Nazovi projekat → **Finish**.
4. Ako nije već tu, dodaj aplikaciju: **Add Device… → Application**.

---

## 3) Import koda

### 3.1 PLCopenXML
1. **Project → Import → PLCopenXML…**.
2. Uvezi:
   - `Signals.gvl.xml` ili `Signals_and_Sim.gvl.txt` (vidi 3.2),
   - `TW_AlarmHi.xml`,
   - `AlarmMgr.xml`,
   - `MAIN.xml` ili `MAIN_with_Sim.program.txt`,
   - `TW_SimAnalog.xml`, `TW_SimDigital.xml`, `SimMgr.xml` (ako postoje).
3. CODESYS će rasporediti Declaration/Implementation.

### 3.2 ST fajlovi (copy-paste)
1. **Add Object… → Global Variable List** → npr. `Signals`.
   - U **Declaration** paste sadržaj `Signals_and_Sim.gvl.txt`.
2. **Add Object… → Function Block (ST)** → `TW_AlarmHi`.
   - **Declaration**: paste deklaraciju.
   - **Implementation**: paste implementaciju.
3. Isto za:
   - `AlarmMgr`, `TW_SimAnalog`, `TW_SimDigital`, `SimMgr`.
4. Za `MAIN` (Program):
   - **Declaration**: ubaci instance `Sim`, `Alms`.
   - **Implementation**: pozovi `Sim(); Alms();`.

> **Napomena**: IF/CASE i pozivi FB-ova idu **samo** u *Implementation* deo.

---

## 4) Library Manager
1. **Add Object… → Library Manager**.
2. **Add Library… → Standard** (zbog `TON`, `TP`, itd.).

---

## 5) Task Configuration
1. **Application → Task Configuration → MainTask**.
2. Period: npr. **10 ms**.
3. Desni klik na **MainTask → Add Call… → MAIN**.

---

## 6) Vizualizacija (opciono)
1. **Add Object… → Visualization**.
2. Dodaj elemente:
   - **Numeric Input**: `AI_*`, pragovi `*_WARN_HI`, `*_CRIT_HI`.
   - **Lamp**: `LAMP_*_YEL` (žuta), `LAMP_*_RED` (crvena).
   - **Button/Toggle**: `BTN_START`, `BTN_STOP`, `SIM_ENABLE`, `ALM_ACK`, `ALM_MUTE`.
   - **Numeric Display**: `SIM_SPEED`, `SIM_DT_S`, `SIM_TIME_S`.

---

## 7) Build & Run
1. **Build → Rebuild** (nema Errors).
2. **Online → Login** (CODESYS Control Win V3 mora raditi).
3. **Online → Run** (F5).
4. U vizu:
   - `SIM_ENABLE := TRUE`,
   - klikni `BTN_START`,
   - analogni `AI_*` “dišu”, `DI_*` trepću,
   - `LAMP_*` reaguju na alarme.

---

## 8) Export projekta

### 8.1 Kompletan projekat
- **File → Export → Archive…** → `.project.archive`.

### 8.2 Pojedinačni objekti
- Označi Application ili POU/GVL → **Project → Export → PLCopenXML…**.

### 8.3 ST fajlovi
- Otvori POU/GVL → **File → Save As…** ili desni klik → **Export**.

---

## 9) Import projekta

### 9.1 `.project.archive`
- **File → Open…** → izaberi `.project.archive`.

### 9.2 PLCopenXML
- **Project → Import → PLCopenXML…** → izaberi `.xml`.

---

## 10) Najčešće greške
- **“This code is not supported in declaration part”**  
  → IF/CASE/pozivi FB-ova su u *Declaration* → prebaci u *Implementation*.
- **`TON not defined`**  
  → Dodaj biblioteku **Standard**.
- **`AI_*_MIN` not found**  
  → U *lite* postavci koristi **literale** u `SimMgr_Literals` ili dodaj konstante.
- **Duplicate symbol**  
  → U GVL ostavi **jednu** verziju (AI kao REAL, DI/DO kao BOOL).
- **MAIN se ne izvršava**  
  → Proveri Task Configuration → Add Call → MAIN.

---

## 11) Test
1. Login & Run.
2. U vizu:
   - `SIM_ENABLE := TRUE`,
   - klik `BTN_START`,
   - gledaj promene `AI_*` i lampice.
3. Klikni `ALM_ACK := TRUE` da resetuješ latched alarme.

---

## 12) Sadržaj projekta
- **TW_AlarmHi (FB)** – logika WARN/CRIT.
- **AlarmMgr (FB)** – više alarma, lampice, master signali.
- **SimMgr / SimMgr_Literals (FB)** – generisanje AI/DI.
- **GVL (Signals)** – globalne promenljive i pragovi.
- **MAIN (Program)** – poziva simulaciju i alarme.
