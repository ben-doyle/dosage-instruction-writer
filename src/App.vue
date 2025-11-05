<script setup lang="ts">
import { ref, watch } from 'vue'

interface Variable {
  name: string
  label: string
  default: string
}

interface DosageForm {
  name: string
  variables: Variable[]
  template: string
  pharmacyNote: string | null
  note: string | null
}

interface ScriptItem {
  id: number
  formName: string
  instruction: string
  pharmacyNote: string
  formValues: Record<string, string>
  dosageForm: DosageForm
}

const dosageForms: DosageForm[] = [
  {
    name: 'THC Flower (dried herb)',
    variables: [
      { name: 'monthlyMax', label: 'Monthly max', default: '30g' },
      { name: 'usageAmount', label: 'Usage Amount', default: '0.1g' },
      { name: 'maxPerDay', label: 'Max per day', default: '1.5g' }
    ],
    template: 'Inhale [usageAmount] via approved vaporiser every 4-6 hours as required for symptom control, up to a maximum of [maxPerDay] per day.',
    pharmacyNote: 'Dispense in [monthlyMax] lots.',
    note: null
  },
  {
    name: 'non-THC Flower (dried herb)',
    variables: [
      { name: 'monthlyMax', label: 'Monthly max', default: '30g' },
      { name: 'usageAmount', label: 'Usage Amount', default: '0.1g' },
      { name: 'maxPerDay', label: 'Max per day', default: '1.5g' }
    ],
    template: 'Inhale [usageAmount] via approved vaporiser every 4-6 hours as required for symptom control, up to a maximum of [maxPerDay] per day.',
    pharmacyNote: null,
    note: null
  },
  {
    name: 'Oral Oil',
    variables: [
      { name: 'startingDose', label: 'Starting Dose', default: '0.1ml' },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night' },
      { name: 'increaseAmount', label: 'Increase Amount', default: '0.1ml' },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days' },
      { name: 'maxDose', label: 'Max Dose', default: '1ml/day' }
    ],
    template: 'Start with [startingDose] under the tongue [timeOfDay] for a minute then swallow remaining; increasing by [increaseAmount] [increaseFrequency], up to max [maxDose] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: null
  },
  {
    name: 'Pastilles/Gummies',
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one' },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night' },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one' },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days' },
      { name: 'maxDose', label: 'Max Dose', default: 'four' },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day' }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Please check pastille strength carefully (esp. THC) before specifying maximum per day'
  },
  {
    name: 'Vape Cartridges',
    variables: [
      { name: 'monthlyMax', label: 'Monthly max', default: '2x 1g' },
      { name: 'usageAmount', label: 'Usage Amount', default: '1-2' },
      { name: 'usageUnit', label: 'Usage Unit', default: 'puffs' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: 'every 4-6 hours' }
    ],
    template: 'Inhale [usageAmount] [usageUnit] [usageFrequency] as required for symptom control via oral route',
    pharmacyNote: 'Dispense in [monthlyMax] lots. Please dispense in tandem with a vape battery if required.',
    note: 'Remember: The pharmacy note includes vape battery dispensing instruction.'
  },
  {
    name: 'Wafer',
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one' },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night' },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one' },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days' },
      { name: 'maxDose', label: 'Max Dose', default: 'four' },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day' }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Maximum of 4 wafers a day. Place the wafer as far back under the tongue as possible. The wafer should be kept under the tongue until it has dissolved. Repeat up to maximum dose.'
  },
  {
    name: 'Capsules',
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one' },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night' },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one' },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days' },
      { name: 'maxDose', label: 'Max Dose', default: 'four' },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day' }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Please check capsule strength carefully before specifying maximum no. of capsules per day.'
  },
  {
    name: 'Extracts/concentrates',
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: '0.1g' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: 'every 4-6 hours' },
      { name: 'maxDose', label: 'Max Dose', default: '0.5g' }
    ],
    template: 'Inhale [usageAmount] [usageFrequency] as required for symptom control, up to a maximum of [maxDose] per day via oral route',
    pharmacyNote: null,
    note: 'Can be very product specific. If unsure, ask. For resins or similar, patients can dab it with a quartz banger (glass piece that is heated with a torch) or, via a vaporiser like Xmax Riggo.'
  },
  {
    name: '2.5mg THC Metered Dose Inhaler',
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: 'one' },
      { name: 'usageUnit', label: 'Usage Unit', default: 'puff' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: 'every 4-6 hours' },
      { name: 'maxDose', label: 'Max Dose', default: '10' },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'puffs per day' }
    ],
    template: 'Inhale [usageAmount] [usageUnit] [usageFrequency] as required for symptom control, up to [maxDose] [maxDoseUnit] via oral route',
    pharmacyNote: null,
    note: 'Aim for 1-2 inhalers a month until we get more data on recommended maximums of this product.'
  },
  {
    name: '5mg THC Metered Dose Inhaler',
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: 'one' },
      { name: 'usageUnit', label: 'Usage Unit', default: 'puff' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: 'every 4-6 hours' },
      { name: 'maxDose', label: 'Max Dose', default: '10' },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'puffs per day' }
    ],
    template: 'Inhale [usageAmount] [usageUnit] [usageFrequency] as required for symptom control, up to [maxDose] [maxDoseUnit] via oral route',
    pharmacyNote: null,
    note: 'Aim for 1 inhaler a month until we get more data on recommended maximums of this product.'
  },
  {
    name: 'Topical Creams',
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: 'a small amount' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: '2-3 times daily' }
    ],
    template: 'Apply [usageAmount] to the affected area [usageFrequency] as required for symptom control via topical route',
    pharmacyNote: null,
    note: 'Remove the last sentence if not required. If in doubt on dosing, please consult product sheet, product team or Team Leader.'
  },
  {
    name: 'CBD Topical Patch',
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: 'one' },
      { name: 'usageUnit', label: 'Usage Unit', default: 'patch' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: 'every 24 hours' }
    ],
    template: 'Apply [usageAmount] [usageUnit] to the affected area [usageFrequency] via topical route',
    pharmacyNote: null,
    note: 'X equals the patch CBD strength times the daily quantity'
  }
]

// State management
const configuring = ref<boolean>(false)
const selectedForm = ref<DosageForm | null>(null)
const formValues = ref<Record<string, string>>({})
const pharmacyNoteValue = ref<string>('')
const scriptItems = ref<ScriptItem[]>([])
const editingItemId = ref<number | null>(null)
const nextId = ref(1)

// Watch for form selection
watch(selectedForm, (newForm) => {
  if (newForm) {
    formValues.value = {}
    newForm.variables.forEach(v => {
      formValues.value[v.name] = v.default
    })
    pharmacyNoteValue.value = newForm.pharmacyNote ? generateInstruction(newForm.pharmacyNote, formValues.value) : ''
  }
})

const selectForm = (form: DosageForm) => {
  selectedForm.value = form
  configuring.value = true
}

const generateInstruction = (template: string, values: Record<string, string>): string => {
  let result = template
  Object.keys(values).forEach(key => {
    const value = values[key] || ''
    const placeholder = `[${key}]`
    const regex = new RegExp(`\\${placeholder}`, 'g')
    result = result.replace(regex, value)
  })
  return result
}

const addToScript = () => {
  if (!selectedForm.value) return
  
  const instruction = generateInstruction(selectedForm.value.template, formValues.value)
  const pharmacyNote = pharmacyNoteValue.value.trim()
  
  if (editingItemId.value !== null) {
    const index = scriptItems.value.findIndex(item => item.id === editingItemId.value)
    if (index !== -1) {
      scriptItems.value[index] = {
        id: editingItemId.value,
        formName: selectedForm.value.name,
        instruction,
        pharmacyNote,
        formValues: { ...formValues.value },
        dosageForm: selectedForm.value
      }
    }
    editingItemId.value = null
  } else {
    scriptItems.value.push({
      id: nextId.value++,
      formName: selectedForm.value.name,
      instruction,
      pharmacyNote,
      formValues: { ...formValues.value },
      dosageForm: selectedForm.value
    })
  }
  
  selectedForm.value = null
  pharmacyNoteValue.value = ''
  configuring.value = false
}

const cancelConfigure = () => {
  selectedForm.value = null
  editingItemId.value = null
  pharmacyNoteValue.value = ''
  configuring.value = false
}

const editItem = (item: ScriptItem) => {
  selectedForm.value = item.dosageForm
  formValues.value = { ...item.formValues }
  pharmacyNoteValue.value = item.pharmacyNote
  editingItemId.value = item.id
  configuring.value = true
}

const deleteItem = (id: number) => {
  scriptItems.value = scriptItems.value.filter(item => item.id !== id)
}

const copyToClipboard = async (text: string) => {
  try {
    await navigator.clipboard.writeText(text)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
}

const clearScript = () => {
  if (confirm('Clear all items from script?')) {
    scriptItems.value = []
  }
}
</script>

<template>
  <div class="app">
    <div class="container">
      <header class="header">
        <h1>Dosage Instruction Writer</h1>
        <p class="subtitle">Build dosage instructions</p>
      </header>

      <div class="content">
        <!-- Left Card: Add/Configure -->
        <div class="card left-card">
          <div v-if="!configuring" class="card-content">
            <h2 class="card-title">Select Dosage Form</h2>
            <div class="dosage-list">
              <button
                v-for="form in dosageForms"
                :key="form.name"
                @click="selectForm(form)"
                class="dosage-item"
              >
                <span class="dosage-name">{{ form.name }}</span>
                <span class="dosage-arrow">→</span>
              </button>
            </div>
          </div>

          <div v-else class="card-content">
            <div class="config-header">
              <h2 class="card-title">{{ selectedForm?.name }}</h2>
            </div>
            
            <div v-if="selectedForm?.note" class="alert">
              <span class="alert-icon">ℹ️</span>
              <span class="alert-text">{{ selectedForm.note }}</span>
            </div>

            <div class="form-fields">
              <div v-for="variable in selectedForm?.variables" :key="variable.name" class="form-field">
                <label>{{ variable.label }}</label>
                <input v-model="formValues[variable.name]" type="text" />
              </div>

              <div class="form-field pharmacy-field">
                <label>Note to Pharmacy <span class="optional">(optional)</span></label>
                <textarea v-model="pharmacyNoteValue" rows="2"></textarea>
              </div>
            </div>

            <div class="card-actions">
              <button @click="cancelConfigure" class="btn btn-secondary">Cancel</button>
              <button @click="addToScript" class="btn btn-primary">
                {{ editingItemId ? 'Update' : 'Add to Script' }}
              </button>
            </div>
          </div>
        </div>

        <!-- Right Card: Script -->
        <div class="card right-card">
          <div class="card-header">
            <h2 class="card-title">Prescription Pad</h2>
            <button v-if="scriptItems.length > 0" @click="clearScript" class="btn-clear">
              Clear All
            </button>
          </div>

          <div v-if="scriptItems.length === 0" class="empty-state">
            <svg class="empty-icon" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
              <path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"/>
            </svg>
            <p class="empty-text">No items added yet</p>
            <p class="empty-hint">Select a dosage form to begin</p>
          </div>

          <div v-else class="script-items">
            <div v-for="(item, index) in scriptItems" :key="item.id" class="script-card">
              <div class="script-header">
                <div class="script-title">
                  <span class="script-num">{{ index + 1 }}</span>
                  <span class="script-name">{{ item.formName }}</span>
                </div>
                <div class="script-actions">
                  <button @click="editItem(item)" class="action-btn" title="Edit">
                    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                      <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/>
                      <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/>
                    </svg>
                  </button>
                  <button @click="deleteItem(item.id)" class="action-btn action-btn-danger" title="Delete">
                    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                      <path d="M3 6h18M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/>
                    </svg>
                  </button>
                </div>
              </div>
              
              <div class="script-body">
                <div class="script-section">
                  <div class="section-header">Dosage Instruction</div>
                  <div class="section-content">{{ item.instruction }}</div>
                  <button @click="copyToClipboard(item.instruction)" class="btn-copy">Copy</button>
                </div>
                
                <div v-if="item.pharmacyNote" class="script-section">
                  <div class="section-header">Note to Pharmacy</div>
                  <div class="section-content">{{ item.pharmacyNote }}</div>
                  <button @click="copyToClipboard(item.pharmacyNote)" class="btn-copy">Copy</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
/* Global styles */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html, body {
  width: 100%;
  height: 100%;
  overflow: hidden;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  width: 100%;
  height: 100%;
}
</style>

<style scoped>
.app {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 1.5rem 1rem;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.container {
  max-width: 1400px;
  width: 100%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  flex: 1;
  min-height: 0;
  overflow: hidden;
}

.header {
  text-align: center;
  margin-bottom: 1.5rem;
  flex-shrink: 0;
}

.header h1 {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 700;
  color: white;
  letter-spacing: -0.5px;
}

.subtitle {
  margin: 0.375rem 0 0 0;
  font-size: 0.8125rem;
  color: rgba(255, 255, 255, 0.85);
}

.content {
  display: grid;
  grid-template-columns: 400px 1fr;
  gap: 1.25rem;
  flex: 1;
  min-height: 0;
  overflow: hidden;
}

/* Cards */
.card {
  background: white;
  border-radius: 16px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.15);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  min-height: 0;
  height: 100%;
}

.card-content {
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  flex: 1;
  min-height: 0;
  overflow: hidden;
}

.card-header {
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid #f0f1f3;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-shrink: 0;
}

.card-title {
  margin: 0;
  font-size: 0.9375rem;
  font-weight: 700;
  color: #1f2937;
  letter-spacing: -0.2px;
}

.config-header {
  margin-bottom: 1rem;
  flex-shrink: 0;
}

/* Dosage List */
.dosage-list {
  flex: 1;
  min-height: 0;
  overflow-y: auto;
  overflow-x: hidden;
  margin-top: 0.75rem;
  padding-right: 0.25rem;
}

.dosage-list::-webkit-scrollbar {
  width: 5px;
}

.dosage-list::-webkit-scrollbar-track {
  background: transparent;
}

.dosage-list::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 3px;
}

.dosage-item {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 0.625rem;
  padding: 0.75rem 0.875rem;
  margin-bottom: 0.375rem;
  background: #f8f9fb;
  border: 2px solid transparent;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s;
  text-align: left;
}

.dosage-item:hover {
  background: #f0f4ff;
  border-color: #667eea;
}

.dosage-name {
  flex: 1;
  font-size: 0.8125rem;
  font-weight: 600;
  color: #374151;
}

.dosage-arrow {
  color: #667eea;
  font-size: 1rem;
  opacity: 0;
  transition: opacity 0.2s;
  flex-shrink: 0;
}

.dosage-item:hover .dosage-arrow {
  opacity: 1;
}

/* Alert */
.alert {
  display: flex;
  gap: 0.625rem;
  padding: 0.75rem;
  background: #fef3c7;
  border-left: 3px solid #f59e0b;
  border-radius: 8px;
  margin-bottom: 1rem;
  flex-shrink: 0;
}

.alert-icon {
  flex-shrink: 0;
  font-size: 0.875rem;
}

.alert-text {
  font-size: 0.75rem;
  line-height: 1.5;
  color: #92400e;
}

/* Form Fields */
.form-fields {
  flex: 1;
  min-height: 0;
  overflow-y: auto;
  overflow-x: hidden;
  margin-bottom: 1rem;
  padding-right: 0.25rem;
}

.form-fields::-webkit-scrollbar {
  width: 5px;
}

.form-fields::-webkit-scrollbar-track {
  background: transparent;
}

.form-fields::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 3px;
}

.form-field {
  margin-bottom: 1rem;
}

.form-field label {
  display: block;
  font-size: 0.75rem;
  font-weight: 600;
  color: #374151;
  margin-bottom: 0.375rem;
}

.optional {
  font-weight: 400;
  color: #9ca3af;
}

.form-field input,
.form-field textarea {
  width: 100%;
  padding: 0.625rem 0.75rem;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 0.8125rem;
  font-family: inherit;
  transition: all 0.2s;
}

.form-field input:focus,
.form-field textarea:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.form-field textarea {
  resize: vertical;
  line-height: 1.5;
}

.pharmacy-field {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid #f0f1f3;
}

/* Buttons */
.card-actions {
  display: flex;
  gap: 0.75rem;
  flex-shrink: 0;
}

.btn {
  flex: 1;
  padding: 0.75rem;
  border: none;
  border-radius: 10px;
  font-size: 0.8125rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.btn-primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.btn-secondary {
  background: #f3f4f6;
  color: #6b7280;
}

.btn-secondary:hover {
  background: #e5e7eb;
}

.btn-clear {
  padding: 0.5rem 0.875rem;
  background: #fee2e2;
  color: #dc2626;
  border: none;
  border-radius: 8px;
  font-size: 0.75rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-clear:hover {
  background: #fecaca;
}

/* Empty State */
.empty-state {
  padding: 4rem 2rem;
  text-align: center;
}

.empty-icon {
  margin-bottom: 1rem;
  color: #d1d5db;
}

.empty-text {
  margin: 0;
  font-size: 0.9375rem;
  font-weight: 600;
  color: #6b7280;
}

.empty-hint {
  margin: 0.5rem 0 0 0;
  font-size: 0.8125rem;
  color: #9ca3af;
}

/* Script Items */
.script-items {
  padding: 1.5rem;
  overflow-y: auto;
  overflow-x: hidden;
  flex: 1;
  min-height: 0;
}

.script-items::-webkit-scrollbar {
  width: 6px;
}

.script-items::-webkit-scrollbar-track {
  background: transparent;
}

.script-items::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 3px;
}

.script-card {
  background: #f8f9fb;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  margin-bottom: 1rem;
  overflow: hidden;
  transition: all 0.2s;
}

.script-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.script-header {
  padding: 1rem 1.25rem;
  background: white;
  border-bottom: 1px solid #e5e7eb;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.script-title {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.script-num {
  width: 24px;
  height: 24px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 0.75rem;
}

.script-name {
  font-weight: 600;
  font-size: 0.875rem;
  color: #1f2937;
}

.script-actions {
  display: flex;
  gap: 0.5rem;
}

.action-btn {
  padding: 0.375rem;
  background: transparent;
  border: none;
  border-radius: 6px;
  color: #6b7280;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
}

.action-btn:hover {
  background: #f3f4f6;
  color: #374151;
}

.action-btn-danger:hover {
  background: #fee2e2;
  color: #dc2626;
}

.script-body {
  padding: 1.25rem;
}

.script-section {
  margin-bottom: 1.25rem;
}

.script-section:last-child {
  margin-bottom: 0;
}

.section-header {
  font-size: 0.6875rem;
  font-weight: 700;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.5rem;
}

.section-content {
  padding: 0.875rem 1rem;
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  line-height: 1.6;
  font-size: 0.8125rem;
  color: #374151;
  margin-bottom: 0.625rem;
}

.btn-copy {
  padding: 0.5rem 1rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 0.75rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-copy:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.3);
}

/* Responsive */
@media (max-width: 1200px) {
  .content {
    grid-template-columns: 1fr;
    grid-template-rows: auto 1fr;
  }
}

@media (max-width: 768px) {
  .app {
    padding: 1rem 0.5rem;
  }

  .header h1 {
    font-size: 1.25rem;
  }

  .content {
    gap: 1rem;
  }
}
</style>
