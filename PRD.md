Here is the revised Product Requirements Document, rewritten to use a list for the dosage instructions and with all sections fully written out.

---

### **Product Requirements Document (PRD): Simple Dosage Instruction Writer**

### 1. Introduction & Objective

**1.1. Problem**
Medical practitioners currently write prescription instructions by manually copying and pasting from a style guide (the "Dosage instructions" document). This process is time-consuming and, more importantly, prone to human error. Failure to use the exact, consistent wording can lead to unclear instructions for patients and rejection of the script by the pharmacy team.

**1.2. Solution**
The "Simple Dosage Instruction Writer" is a small, focused utility that automates the creation of these complex dosage instructions. It will guide the practitioner through a series of simple selections, pre-filled with sensible defaults.

**1.3. Objective**
The primary goal is to empower practitioners to generate 100% accurate and compliant dosage instructions in seconds. This will:
* Increase practitioner efficiency.
* Ensure full compliance with the agreed-upon instruction templates.
* Eliminate script rejections caused by incorrect instruction formatting.

---

### 2. Target Audience

* **Primary Users:** Medical practitioners and prescribers who are writing prescriptions.
* **Secondary Beneficiaries:** The pharmacy team (who receive clear, consistent instructions) and patients (who receive unambiguous directions).

---

### 3. Core Features & User Flow

The user's experience will be "very simple," as requested. The flow is as follows:

1.  **Select Dosage Form:** The user is presented with a clear list of all available dosage forms (e.g., "Oral Oil," "THC Flower").
2.  **Select "Things" (Enter Variables):** The user is shown simple input fields *already populated* with the most common default values.
    * *Example: For "Oral Oil," the "Starting Dose" field is pre-filled with "0.1ml".*
3.  **Adjust & Generate:** The user only changes the values that differ from the default, then clicks "Generate."
4.  **Get Instruction:** The tool instantly generates the complete, perfectly formatted dosage instruction string. This string is displayed on-screen with a "Copy to Clipboard" button, allowing the user to paste it directly into their prescription software.

---

### 4. Functional Requirements

#### FR-1: Dosage Form Selection
1.1. The system must display a list of all available dosage forms.
1.2. This list must include all forms specified in FR-2.

#### FR-2: Instruction Templates, Variables, and Defaults
The system must store the following templates. When a form is selected, its input fields must be pre-populated with the specified **[Default Value]**.

* **THC Flower (dried herb)**
    * **Variables:**
        * Monthly max: **[30g]**
        * Usage Amount: **[0.1g]**
        * Max per day: **[1.5g]**
    * **Instruction Template:** Inhale **[Usage Amount]** via approved vaporiser every 4-6 hours as required for symptom control, up to a maximum of **[Max per day]** per day.
    * **Note to Pharmacy:** Dispense in **[Monthly max]** lots.

* **non-THC Flower (dried herb)**
    * **Variables:**
        * Monthly max: **[30g]**
        * Usage Amount: **[0.1g]**
        * Max per day: **[1.5g]**
    * **Instruction Template:** Inhale **[Usage Amount]** via approved vaporiser every 4-6 hours as required for symptom control, up to a maximum of **[Max per day]** per day.
    * **Note to Pharmacy:** None.

* **Oral Oil**
    * **Variables:**
        * Starting Dose: **[0.1ml]**
        * Time of day: **[at night]**
        * Increase Amount: **[0.1ml]**
        * Increase Frequency: **[every 3 days]**
        * Max Dose: **[1ml/day]**
    * **Instruction Template:** Start with **[Starting Dose]** under the tongue **[Time of day]** for a minute then swallow remaining; increasing by **[Increase Amount]** **[Increase Frequency]**, up to max **[Max Dose]** or until symptom controlled or side effects develop via oral route
    * **Note to Pharmacy:** None.

* **Pastilles/Gummies**
    * **Variables:**
        * Quantity: **[one]**
        * Time of day: **[at night]**
        * Increase Amount: **[one]**
        * Increase Frequency: **[every 3 days]**
        * Max Dose: **[four]**
        * Max Dose Unit: **[per day]**
    * **Instruction Template:** Take **[Quantity]** **[Time of day]**; increasing by **[Increase Amount]** **[Increase Frequency]**, up to **[Max Dose]** **[Max Dose Unit]** or until symptom controlled or side effects develop via oral route
    * **Note to Pharmacy:** None.

* **Vape Cartridges**
    * **Variables:**
        * Monthly max: **[2x 1g]**
        * Usage Amount: **[1-2]**
        * Usage Unit: **[puffs]**
        * Usage Frequency: **[every 4-6 hours]**
    * **Instruction Template:** Inhale **[Usage Amount]** **[Usage Unit]** **[Usage Frequency]** as required for symptom control via oral route
    * **Note to Pharmacy:** Dispense in **[Monthly max]** lots.

* **Wafer**
    * **Variables:**
        * Quantity: **[one]**
        * Time of day: **[at night]**
        * Increase Amount: **[one]**
        * Increase Frequency: **[every 3 days]**
        * Max Dose: **[four]**
        * Max Dose Unit: **[per day]**
    * **Instruction Template:** Take **[Quantity]** **[Time of day]**; increasing by **[Increase Amount]** **[Increase Frequency]**, up to **[Max Dose]** **[Max Dose Unit]** or until symptom controlled or side effects develop via oral route
    * **Note to Pharmacy:** None.

* **Capsules**
    * **Variables:**
        * Quantity: **[one]**
        * Time of day: **[at night]**
        * Increase Amount: **[one]**
        * Increase Frequency: **[every 3 days]**
        * Max Dose: **[four]**
        * Max Dose Unit: **[per day]**
    * **Instruction Template:** Take **[Quantity]** **[Time of day]**; increasing by **[Increase Amount]** **[Increase Frequency]**, up to **[Max Dose]** **[Max Dose Unit]** or until symptom controlled or side effects develop via oral route
    * **Note to Pharmacy:** None.

* **Extracts/concentrates**
    * **Variables:**
        * Usage Amount: **[0.1g]**
        * Usage Frequency: **[every 4-6 hours]**
        * Max Dose: **[0.5g]**
    * **Instruction Template:** Inhale **[Usage Amount]** **[Usage Frequency]** as required for symptom control, up to a maximum of **[Max Dose]** per day via oral route
    * **Note to Pharmacy:** None.

* **2.5mg THC Metered Dose Inhaler**
    * **Variables:**
        * Usage Amount: **[one]**
        * Usage Unit: **[puff]**
        * Usage Frequency: **[every 4-6 hours]**
        * Max Dose: **[10]**
        * Max Dose Unit: **[puffs per day]**
    * **Instruction Template:** Inhale **[Usage Amount]** **[Usage Unit]** **[Usage Frequency]** as required for symptom control, up to **[Max Dose]** **[Max Dose Unit]** via oral route
    * **Note to Pharmacy:** None.

* **5mg THC Metered Dose Inhaler**
    * **Variables:**
        * Usage Amount: **[one]**
        * Usage Unit: **[puff]**
        * Usage Frequency: **[every 4-6 hours]**
        * Max Dose: **[10]**
        * Max Dose Unit: **[puffs per day]**
    * **Instruction Template:** Inhale **[Usage Amount]** **[Usage Unit]** **[Usage Frequency]** as required for symptom control, up to **[Max Dose]** **[Max Dose Unit]** via oral route
    * **Note to Pharmacy:** None.

* **Topical Creams**
    * **Variables:**
        * Usage Amount: **[a small amount]**
        * Usage Frequency: **[2-3 times daily]**
    * **Instruction Template:** Apply **[Usage Amount]** to the affected area **[Usage Frequency]** as required for symptom control via topical route
    * **Note to Pharmacy:** None.

* **CBD Topical Patch**
    * **Variables:**
        * Usage Amount: **[one]**
        * Usage Unit: **[patch]**
        * Usage Frequency: **[every 24 hours]**
    * **Instruction Template:** Apply **[Usage Amount]** **[Usage Unit]** to the affected area **[Usage Frequency]** via topical route
    * **Note to Pharmacy:** None.

#### FR-3: Dynamic Input Fields
3.1. Upon selection of a dosage form, the UI must dynamically display a set of input fields specific to that form (as defined in FR-2).
3.2. All input fields should be clearly labeled.
3.3. Fields must be pre-populated with the default values specified in FR-2.
3.4. The user must be able to override any default value.

#### FR-4: Instruction & Note Generation
4.1. Clicking "Generate" shall merge the user's final inputs (either the defaults or their overrides) into the stored templates to create the final strings.
4.2. The system must generate both the "Dosage Instruction" and, if applicable, the "Note to Pharmacy."

#### FR-5: Output & Usability
5.1. The generated "Dosage Instruction" must be displayed in a clear, read-only text box.
5.2. A "Copy" button must be present to copy the instruction string to the user's clipboard in one click.
5.3. If a "Note to Pharmacy" is generated, it must be displayed separately (e.g., in a second text box) with its own "Copy" button.

---

### 5. Non-Functional Requirements

* **Simplicity:** The UI must be extremely clean, minimal, and intuitive. No clutter.
* **Speed:** Instruction generation must be instantaneous (client-side).
* **Accuracy:** The output templates must be 100% identical to the source document to ensure pharmacy compliance.
* **Maintainability:** A non-technical admin should (ideally) be able to update and add new dosage form templates and their defaults through a simple interface.

---

### 6. Success Metrics

* **Adoption:** Percentage of practitioners who use the tool vs. the manual copy/paste method.
* **Efficiency:** Time saved per prescription (measured via user surveys).
* **Quality:** Reduction in script rejections related to dosage instructions (feedback from the pharmacy team).