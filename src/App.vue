<script setup lang="ts">
import { ref, watch, computed } from 'vue'
import flowerIcon from './assets/icons/flower.svg?raw'
import gummyIcon from './assets/icons/gummy.svg?raw'
import oralLiquidIcon from './assets/icons/oral-liquid.svg?raw'
import vapeIcon from './assets/icons/vape.svg?raw'
import capsuleIcon from './assets/icons/capsule.svg?raw'
import waferIcon from './assets/icons/wafer.svg?raw'
import extractsIcon from './assets/icons/extracts.svg?raw'
import inhalerIcon from './assets/icons/inhaler.svg?raw'
import topicalIcon from './assets/icons/topical.svg?raw'

interface Variable {
  name: string
  label: string
  default: string
  type?: 'text' | 'select'
  options?: string[]
}

interface DosageForm {
  name: string
  icon?: string
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
    name: 'THC Flower (Herb, Dried)',
    icon: flowerIcon,
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
    name: 'non-THC Flower (Herb, Dried)',
    icon: flowerIcon,
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
    name: 'Oral Oil (Oral Liquid)',
    icon: oralLiquidIcon,
    variables: [
      { name: 'startingDose', label: 'Starting Dose', default: '0.1ml' },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night', type: 'select', options: ['in the morning', 'at midday', 'in the afternoon', 'at night', 'before bed'] },
      { name: 'increaseAmount', label: 'Increase Amount', default: '0.1ml' },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days', type: 'select', options: ['every day', 'every 2 days', 'every 3 days', 'every week'] },
      { name: 'maxDose', label: 'Max Dose', default: '1ml/day' }
    ],
    template: 'Start with [startingDose] under the tongue [timeOfDay] for a minute then swallow remaining; increasing by [increaseAmount] [increaseFrequency], up to max [maxDose] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: null
  },
  {
    name: 'Pastilles/Gummies',
      icon: gummyIcon,
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one', type: 'select', options: ['one', 'two', 'three', 'four', 'five'] },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night', type: 'select', options: ['in the morning', 'at midday', 'in the afternoon', 'at night', 'before bed'] },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one', type: 'select', options: ['one', 'two', 'three'] },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days', type: 'select', options: ['every day', 'every 2 days', 'every 3 days', 'every week'] },
      { name: 'maxDose', label: 'Max Dose', default: 'four', type: 'select', options: ['one', 'two', 'three', 'four', 'five', 'six', 'eight', 'ten'] },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day', type: 'select', options: ['per day', 'per dose'] }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Please check pastille strength carefully (esp. THC) before specifying maximum per day'
  },
  {
    name: 'Vape Cartridges (Inhalation)',
    icon: vapeIcon,
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
    icon: waferIcon,
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one', type: 'select', options: ['one', 'two', 'three', 'four', 'five'] },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night', type: 'select', options: ['in the morning', 'at midday', 'in the afternoon', 'at night', 'before bed'] },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one', type: 'select', options: ['one', 'two', 'three'] },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days', type: 'select', options: ['every day', 'every 2 days', 'every 3 days', 'every week'] },
      { name: 'maxDose', label: 'Max Dose', default: 'four', type: 'select', options: ['one', 'two', 'three', 'four', 'five', 'six'] },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day', type: 'select', options: ['per day', 'per dose'] }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Maximum of 4 wafers a day. Place the wafer as far back under the tongue as possible. The wafer should be kept under the tongue until it has dissolved. Repeat up to maximum dose.'
  },
  {
    name: 'Capsules',
    icon: capsuleIcon,
    variables: [
      { name: 'quantity', label: 'Quantity', default: 'one', type: 'select', options: ['one', 'two', 'three', 'four', 'five'] },
      { name: 'timeOfDay', label: 'Time of day', default: 'at night', type: 'select', options: ['in the morning', 'at midday', 'in the afternoon', 'at night', 'before bed'] },
      { name: 'increaseAmount', label: 'Increase Amount', default: 'one', type: 'select', options: ['one', 'two', 'three'] },
      { name: 'increaseFrequency', label: 'Increase Frequency', default: 'every 3 days', type: 'select', options: ['every day', 'every 2 days', 'every 3 days', 'every week'] },
      { name: 'maxDose', label: 'Max Dose', default: 'four', type: 'select', options: ['one', 'two', 'three', 'four', 'five', 'six', 'eight', 'ten'] },
      { name: 'maxDoseUnit', label: 'Max Dose Unit', default: 'per day', type: 'select', options: ['per day', 'per dose'] }
    ],
    template: 'Take [quantity] [timeOfDay]; increasing by [increaseAmount] [increaseFrequency], up to [maxDose] [maxDoseUnit] or until symptom controlled or side effects develop via oral route',
    pharmacyNote: null,
    note: 'Please check capsule strength carefully before specifying maximum no. of capsules per day.'
  },
  {
    name: 'Extracts/Concentrates',
    icon: extractsIcon,
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
    icon: inhalerIcon,
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
    icon: inhalerIcon,
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
    name: 'Topical Creams (Topical)',
    icon: topicalIcon,
    variables: [
      { name: 'usageAmount', label: 'Usage Amount', default: 'a small amount' },
      { name: 'usageFrequency', label: 'Usage Frequency', default: '2-3 times daily' }
    ],
    template: 'Apply [usageAmount] to the affected area [usageFrequency] as required for symptom control via topical route',
    pharmacyNote: null,
    note: 'Remove the last sentence if not required. If in doubt on dosing, please consult product sheet, product team or Team Leader.'
  },
  {
    name: 'CBD Topical Patch (Topical)',
    icon: topicalIcon,
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

const showToast = ref(false)
let toastTimeout: number | undefined

const copyToClipboard = async (text: string) => {
  try {
    await navigator.clipboard.writeText(text)
    
    // Clear any existing timeout
    if (toastTimeout) {
      clearTimeout(toastTimeout)
    }
    
    // Show toast
    showToast.value = true
    
    // Hide toast after 2 seconds
    toastTimeout = setTimeout(() => {
      showToast.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy:', err)
  }
}

// Calculate 30-day totals based on max per day
const monthlyTotals = computed(() => {
  const totals: Record<string, string> = {}
  
  scriptItems.value.forEach(item => {
    const formName = item.formName
    const values = item.formValues
    
    // Priority: Calculate from max per day
    if (values.maxPerDay) {
      // Calculate from max per day (e.g., "1.5g" → "45g")
      const maxPerDay = values.maxPerDay
      const match = maxPerDay.match(/(\d+\.?\d*)/)
      if (match && match[1]) {
        const daily = parseFloat(match[1])
        const monthly = daily * 30
        // Remove unnecessary decimals (45.0 → 45, but 45.5 stays 45.5)
        const monthlyStr = monthly % 1 === 0 ? monthly.toString() : monthly.toFixed(1)
        const unit = maxPerDay.replace(/[\d.]+/, '').trim()
        totals[formName] = `${monthlyStr}${unit}`
      } else {
        totals[formName] = `${maxPerDay}/day`
      }
    } else if (values.maxDose && values.maxDoseUnit === 'per day') {
      // Calculate from max dose per day (e.g., "four per day" → "120")
      const dose = values.maxDose
      // Try to convert text to number
      const textToNum: Record<string, number> = {
        'one': 1, 'two': 2, 'three': 3, 'four': 4, 'five': 5,
        'six': 6, 'eight': 8, 'ten': 10
      }
      const numMatch = dose.match(/\d+/)
      if (numMatch) {
        const daily = parseInt(numMatch[0])
        const monthly = daily * 30
        totals[formName] = `${monthly}`
      } else if (textToNum[dose]) {
        const daily = textToNum[dose]
        const monthly = daily * 30
        totals[formName] = `${monthly}`
      } else {
        totals[formName] = `${dose}/day`
      }
    }
  })
  
  return totals
})

const showClearModal = ref(false)

const clearScript = () => {
  showClearModal.value = true
}

const confirmClear = () => {
  scriptItems.value = []
  showClearModal.value = false
}

const cancelClear = () => {
  showClearModal.value = false
}

// Tab management
const activeTab = ref<'writer' | 'calculators'>('writer')

// THC Calculators
const thcPercent = ref(24)
const monthlyLimit = ref(20)
const flowerThc = ref(45)
const vapeThc = ref(33)

// Dynamic cart list
const carts = ref([
  { mg: 0 }
])

const addCart = () => {
  if (carts.value.length < 6) {
    carts.value.push({ mg: 0 })
  }
}

const removeCart = (index: number) => {
  if (carts.value.length > 1) {
    carts.value.splice(index, 1)
  }
}

// No longer need dropdown options - using number inputs now

// Calculator for strains (dynamic)
const strains = ref([
  { percent: 0 }
])

const addStrain = () => {
  if (strains.value.length < 6) {
    strains.value.push({ percent: 0 })
  }
}

const removeStrain = (index: number) => {
  if (strains.value.length > 1) {
    strains.value.splice(index, 1)
  }
}

const dailyThcFromFlower = computed(() => {
  // Formula: (grams * THC% / 100 * 1000mg) / 30 days
  // Simplified: (grams * THC% * 10) / 30
  return ((monthlyLimit.value * thcPercent.value * 10) / 30).toFixed(0)
})

const isWithinLimit = computed(() => {
  return parseInt(dailyThcFromFlower.value) <= 500
})

const averageThc = computed(() => {
  const validStrains = strains.value.filter(s => s.percent > 0)
  if (validStrains.length === 0) return '0'
  const sum = validStrains.reduce((acc, s) => acc + s.percent, 0)
  const avg = sum / validStrains.length
  
  // Round to 1 decimal place
  const rounded = Math.round(avg * 10) / 10
  
  // Remove unnecessary decimals (38.0 → 38, but 38.5 stays 38.5)
  return rounded % 1 === 0 ? rounded.toString() : rounded.toFixed(1)
})

const dailyThcFromCarts = computed(() => {
  const total = carts.value.reduce((sum, cart) => sum + cart.mg, 0)
  return Math.round(total / 30)
})

const combinedThcPerDay = computed(() => {
  return parseFloat(flowerThc.value.toString()) + parseFloat(vapeThc.value.toString())
})

const combinedIsWithinLimit = computed(() => {
  return combinedThcPerDay.value <= 500
})

const repeatMonthlyLimit = ref(30)
const repeats10g = computed(() => Math.floor(repeatMonthlyLimit.value / 10))
const repeats15g = computed(() => Math.floor(repeatMonthlyLimit.value / 15))

// WA Dispensing Calculator
const waMonthlyLimit = ref(30)
const waThcPercent = ref(24)
const waDispense = computed(() => {
  const grams = waMonthlyLimit.value
  if (grams <= 10) return '1x 10g'
  if (grams <= 15) return '1x 15g'
  if (grams <= 20) return '2x 10g'
  if (grams <= 25) return '1x 10g, 1x 15g'
  if (grams <= 30) return '2x 15g'
  if (grams <= 40) return '2x 10g, 1x 15g'
  if (grams <= 45) return '3x 15g'
  if (grams <= 50) return '2x 10g, 2x 15g'
  return `${Math.floor(grams / 10)}x 10g`
})
const waDailyThc = computed(() => {
  return ((waMonthlyLimit.value * waThcPercent.value * 10) / 30).toFixed(0)
})
const waIsWithinLimit = computed(() => parseInt(waDailyThc.value) <= 500)
</script>

<template>
  <div class="app">
    <div class="container">
      <header class="header">
        <h1>Medical Cannabis Tools</h1>
        <p class="subtitle">To help you support patients with their prescriptions</p>
      </header>

      <!-- Tabs -->
      <div class="tabs">
        <button 
          @click="activeTab = 'writer'" 
          :class="['tab', { active: activeTab === 'writer' }]"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/>
            <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/>
          </svg>
          Dosage Instruction Writer
        </button>
        <button 
          @click="activeTab = 'calculators'" 
          :class="['tab', { active: activeTab === 'calculators' }]"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <rect x="4" y="2" width="16" height="20" rx="2"/>
            <line x1="8" y1="6" x2="16" y2="6"/>
            <line x1="8" y1="10" x2="16" y2="10"/>
            <line x1="8" y1="14" x2="12" y2="14"/>
            <line x1="8" y1="18" x2="12" y2="18"/>
          </svg>
          THC Calculators
        </button>
  </div>

      <!-- Writer Content -->
      <div v-if="activeTab === 'writer'" class="content">
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
                <span v-if="form.icon" class="dosage-icon" v-html="form.icon"></span>
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
                <select v-if="variable.type === 'select'" v-model="formValues[variable.name]">
                  <option v-for="option in variable.options" :key="option" :value="option">
                    {{ option }}
                  </option>
                </select>
                <input v-else v-model="formValues[variable.name]" type="text" />
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

          <!-- Monthly Totals Summary -->
          <div v-if="Object.keys(monthlyTotals).length > 0" class="monthly-summary">
            <div class="summary-header">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
                <line x1="16" y1="2" x2="16" y2="6"></line>
                <line x1="8" y1="2" x2="8" y2="6"></line>
                <line x1="3" y1="10" x2="21" y2="10"></line>
              </svg>
              <div class="summary-title-group">
                <span class="summary-title">30-Day Supply</span>
                <span class="summary-subtitle">Max dose per day × 30</span>
              </div>
            </div>
            <div class="summary-items">
              <div v-for="(total, formName) in monthlyTotals" :key="formName" class="summary-item">
                <div class="summary-form-info">
                  <span v-if="scriptItems.find(item => item.formName === formName)?.dosageForm.icon" 
                        class="summary-icon" 
                        v-html="scriptItems.find(item => item.formName === formName)?.dosageForm.icon">
                  </span>
                  <span class="summary-form">{{ formName }}</span>
                </div>
                <span class="summary-total">{{ total }}</span>
              </div>
            </div>
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
                <div class="script-section" @click="copyToClipboard(item.instruction)" title="Click to copy">
                  <div class="section-header">Dosage Instruction</div>
                  <div class="section-content">
                    {{ item.instruction }}
                    <svg class="copy-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
                      <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
                    </svg>
                  </div>
                </div>
                
                <div v-if="item.pharmacyNote" class="script-section" @click="copyToClipboard(item.pharmacyNote)" title="Click to copy">
                  <div class="section-header">Note to Pharmacy</div>
                  <div class="section-content">
                    {{ item.pharmacyNote }}
                    <svg class="copy-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
                      <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
                    </svg>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Calculators Content -->
      <div v-if="activeTab === 'calculators'" class="calculators-content">
        <div class="calculators-grid">
          <!-- Daily THC Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">Daily THC</h3>
            <p class="calc-description">Daily limit: 500mg (30 day cycle)</p>
            <div class="calc-fields-horizontal">
              <div class="calc-field">
                <label>THC</label>
                <div class="input-with-unit">
                  <input v-model.number="thcPercent" type="number" min="8" max="40" step="0.1" />
                  <span class="input-unit">%</span>
                </div>
              </div>
              <div class="calc-field">
                <label>Monthly</label>
                <div class="input-with-unit">
                  <input v-model.number="monthlyLimit" type="number" min="10" max="100" step="10" />
                  <span class="input-unit">g</span>
                </div>
              </div>
            </div>
            <div class="calc-result compact">
              <div :class="['result-value', { 'within-limit': isWithinLimit, 'over-limit': !isWithinLimit }]">
                {{ dailyThcFromFlower }} mg/day
                <span class="limit-badge">{{ isWithinLimit ? '✓ Within Limit' : '✗ Exceeds Limit' }}</span>
              </div>
            </div>
          </div>

          <!-- Average THC Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">Average THC %</h3>
            <p class="calc-description">Calculate average of multiple strains</p>
            <div class="strain-fields">
              <div v-for="(strain, index) in strains" :key="index" class="strain-field">
                <label>Strain {{ index + 1 }}</label>
                <div class="strain-input-group">
                  <div class="input-with-unit">
                    <input v-model.number="strain.percent" type="number" min="8" max="40" step="0.1" />
                    <span class="input-unit">%</span>
                  </div>
                  <button 
                    v-if="strains.length > 1" 
                    @click="removeStrain(index)" 
                    class="btn-remove-strain"
                    title="Remove strain"
                  >×</button>
                </div>
              </div>
            </div>
            <button 
              v-if="strains.length < 6" 
              @click="addStrain" 
              class="btn-add-strain"
            >+ Add Strain</button>
            <div class="calc-result compact">
              <div class="result-value">Avg: {{ averageThc }}%</div>
            </div>
          </div>

          <!-- Daily Vape Cart THC Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">Vape Cart THC</h3>
            <p class="calc-description">Cart total mg (Daily limit: 500mg)</p>
            <div class="strain-fields">
              <div v-for="(cart, index) in carts" :key="index" class="strain-field">
                <label>Cart {{ index + 1 }} Total Mg</label>
                <div class="strain-input-group">
                  <div class="input-with-unit">
                    <input v-model.number="cart.mg" type="number" min="0" max="5000" step="100" />
                    <span class="input-unit">mg</span>
                  </div>
                  <span class="cart-daily-value">{{ (cart.mg / 30).toFixed(1) }}</span>
                  <button 
                    v-if="carts.length > 1" 
                    @click="removeCart(index)" 
                    class="btn-remove-strain"
                    title="Remove cart"
                  >×</button>
                </div>
              </div>
            </div>
            <button 
              v-if="carts.length < 6" 
              @click="addCart" 
              class="btn-add-strain"
            >+ Add Cart</button>
            <div class="calc-result compact">
              <div class="result-label">THC mg per day</div>
              <div class="result-value">{{ dailyThcFromCarts }}</div>
            </div>
          </div>

          <!-- Combined THC Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">Combined THC</h3>
            <p class="calc-description">Flower + cart totals (mg/day)</p>
            <div class="calc-fields-horizontal">
              <div class="calc-field">
                <label>Flower</label>
                <div class="input-with-unit">
                  <input v-model.number="flowerThc" type="number" step="0.1" min="0" max="500" />
                  <span class="input-unit">mg</span>
                </div>
              </div>
              <div class="calc-field">
                <label>Vape</label>
                <div class="input-with-unit">
                  <input v-model.number="vapeThc" type="number" step="0.1" min="0" max="500" />
                  <span class="input-unit">mg</span>
                </div>
              </div>
            </div>
            <div class="calc-result compact">
              <div :class="['result-value', { 'within-limit': combinedIsWithinLimit, 'over-limit': !combinedIsWithinLimit }]">
                {{ combinedThcPerDay }} mg/day
                <span class="limit-badge">{{ combinedIsWithinLimit ? '✓ Within Limit' : '✗ Exceeds Limit' }}</span>
              </div>
            </div>
          </div>

          <!-- Repeat Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">Repeats</h3>
            <p class="calc-description">Rounds down when tub exceeds limit</p>
            <div class="calc-fields-horizontal">
              <div class="calc-field">
                <label>Monthly</label>
                <div class="input-with-unit">
                  <input v-model.number="repeatMonthlyLimit" type="number" min="10" max="100" step="10" />
                  <span class="input-unit">g</span>
                </div>
              </div>
            </div>
            <div class="calc-result compact">
              <div class="result-row">
                <span class="result-label">10g tub repeats:</span>
                <span class="result-value">{{ repeats10g }}</span>
              </div>
              <div class="result-row">
                <span class="result-label">15g tub repeats:</span>
                <span class="result-value">{{ repeats15g }}</span>
              </div>
            </div>
          </div>

          <!-- WA Dispensing Calculator -->
          <div class="calc-card compact">
            <h3 class="calc-title">WA Dispensing</h3>
            <p class="calc-description">Western Australia dispensing guide</p>
            <div class="calc-fields-horizontal">
              <div class="calc-field">
                <label>Monthly</label>
                <div class="input-with-unit">
                  <input v-model.number="waMonthlyLimit" type="number" min="10" max="100" step="10" />
                  <span class="input-unit">g</span>
                </div>
              </div>
              <div class="calc-field">
                <label>THC</label>
                <div class="input-with-unit">
                  <input v-model.number="waThcPercent" type="number" min="8" max="40" step="0.1" />
                  <span class="input-unit">%</span>
                </div>
              </div>
            </div>
            <div class="calc-result compact">
              <div class="result-label">Dispense</div>
              <div class="result-value">{{ waDispense }}</div>
              <div :class="['result-value', { 'within-limit': waIsWithinLimit, 'over-limit': !waIsWithinLimit }]">
                {{ waDailyThc }} mg/day
                <span class="limit-badge">{{ waIsWithinLimit ? '✓ Within Limit' : '✗ Exceeds Limit' }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Toast Notification -->
    <Transition name="toast">
      <div v-if="showToast" class="toast">
        <svg class="toast-icon" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M20 6L9 17l-5-5"/>
        </svg>
        <span class="toast-text">Copied to clipboard</span>
      </div>
    </Transition>

    <!-- Clear Confirmation Modal -->
    <Transition name="modal">
      <div v-if="showClearModal" class="modal-overlay" @click="cancelClear">
        <div class="modal-content" @click.stop>
          <div class="modal-header">
            <svg class="modal-icon" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
              <path d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/>
            </svg>
            <h3 class="modal-title">Clear All Items?</h3>
            <p class="modal-message">This will remove all dosage instructions from your prescription pad. This action cannot be undone.</p>
          </div>
          <div class="modal-actions">
            <button @click="cancelClear" class="btn btn-secondary">Cancel</button>
            <button @click="confirmClear" class="btn btn-danger">Clear All</button>
          </div>
        </div>
      </div>
    </Transition>
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

.dosage-icon {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
}

.dosage-icon :deep(svg) {
  width: 20px;
  height: 20px;
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
.form-field select,
.form-field textarea {
  width: 100%;
  padding: 0.625rem 0.75rem;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 0.8125rem;
  font-family: inherit;
  transition: all 0.2s;
  background: white;
}

.form-field select {
  cursor: pointer;
}

.form-field input:focus,
.form-field select:focus,
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
  position: relative;
  cursor: pointer;
  transition: all 0.2s;
}

.script-section:hover {
  transform: translateY(-1px);
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
  padding-right: 3rem;
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  line-height: 1.6;
  font-size: 0.8125rem;
  color: #374151;
  position: relative;
  transition: all 0.2s;
}

.script-section:hover .section-content {
  background: #f9fafb;
  border-color: #d1d5db;
}

.copy-icon {
  position: absolute;
  top: 50%;
  right: 1rem;
  transform: translateY(-50%);
  color: #9ca3af;
  opacity: 0;
  transition: opacity 0.2s;
  pointer-events: none;
}

.script-section:hover .copy-icon {
  opacity: 1;
}

/* Monthly Summary */
.monthly-summary {
  margin: 1.5rem;
  padding: 1rem;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  border: 1px solid #bae6fd;
  border-radius: 12px;
}

.summary-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
  color: #0369a1;
}

.summary-title-group {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
}

.summary-title {
  font-size: 0.75rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  line-height: 1;
}

.summary-subtitle {
  font-size: 0.625rem;
  font-weight: 500;
  color: #0284c7;
  opacity: 0.8;
  line-height: 1;
}

.summary-items {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.summary-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.625rem 0.75rem;
  background: white;
  border-radius: 8px;
  font-size: 0.8125rem;
}

.summary-form-info {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex: 1;
}

.summary-icon {
  display: flex;
  align-items: center;
  flex-shrink: 0;
}

.summary-icon :deep(svg) {
  width: 18px;
  height: 18px;
}

.summary-form {
  font-weight: 600;
  color: #1f2937;
}

.summary-total {
  font-weight: 700;
  color: #0369a1;
  background: #f0f9ff;
  padding: 0.25rem 0.625rem;
  border-radius: 6px;
  font-size: 0.75rem;
}

/* Toast Notification */
.toast {
  position: fixed;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 0.625rem;
  padding: 0.875rem 1.5rem;
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  color: white;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(16, 185, 129, 0.3),
              0 4px 12px rgba(0, 0, 0, 0.1);
  font-size: 0.875rem;
  font-weight: 600;
  z-index: 1000;
  pointer-events: none;
}

.toast-icon {
  flex-shrink: 0;
  stroke-width: 2.5;
}

.toast-text {
  white-space: nowrap;
}

/* Toast Transitions */
.toast-enter-active {
  animation: toast-in 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.toast-leave-active {
  animation: toast-out 0.25s ease-in;
}

@keyframes toast-in {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(1rem) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0) scale(1);
  }
}

@keyframes toast-out {
  from {
    opacity: 1;
    transform: translateX(-50%) translateY(0) scale(1);
  }
  to {
    opacity: 0;
    transform: translateX(-50%) translateY(0.5rem) scale(0.95);
  }
}

/* Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  padding: 1rem;
}

.modal-content {
  background: white;
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  max-width: 400px;
  width: 100%;
  overflow: hidden;
}

.modal-header {
  padding: 2rem;
  text-align: center;
}

.modal-icon {
  color: #dc2626;
  margin-bottom: 1rem;
}

.modal-title {
  margin: 0 0 0.75rem 0;
  font-size: 1.25rem;
  font-weight: 700;
  color: #1f2937;
}

.modal-message {
  margin: 0;
  font-size: 0.875rem;
  line-height: 1.5;
  color: #6b7280;
}

.modal-actions {
  display: flex;
  gap: 0.75rem;
  padding: 1.5rem;
  background: #f9fafb;
  border-top: 1px solid #e5e7eb;
}

.modal-actions .btn {
  flex: 1;
  padding: 0.75rem;
  border: none;
  border-radius: 10px;
  font-size: 0.875rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-danger {
  background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
  color: white;
}

.btn-danger:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(220, 38, 38, 0.4);
}

/* Modal Transitions */
.modal-enter-active {
  animation: modal-overlay-in 0.3s ease-out;
}

.modal-enter-active .modal-content {
  animation: modal-content-in 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.modal-leave-active {
  animation: modal-overlay-out 0.25s ease-in;
}

.modal-leave-active .modal-content {
  animation: modal-content-out 0.25s ease-in;
}

@keyframes modal-overlay-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes modal-overlay-out {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}

@keyframes modal-content-in {
  from {
    opacity: 0;
    transform: scale(0.9) translateY(1rem);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

@keyframes modal-content-out {
  from {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
  to {
    opacity: 0;
    transform: scale(0.95) translateY(0.5rem);
  }
}

/* Tabs */
.tabs {
  display: flex;
  gap: 0.5rem;
  margin: 0 3rem 1.5rem 3rem;
  border-bottom: 2px solid rgba(255, 255, 255, 0.2);
}

.tab {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.875rem 1.5rem;
  background: rgba(255, 255, 255, 0.15);
  border: none;
  border-bottom: 3px solid transparent;
  color: rgba(255, 255, 255, 0.7);
  font-size: 0.875rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  margin-bottom: -2px;
  backdrop-filter: blur(8px);
}

.tab svg {
  flex-shrink: 0;
  stroke: currentColor;
}

.tab:hover {
  color: rgba(255, 255, 255, 0.95);
  background: rgba(255, 255, 255, 0.25);
}

.tab.active {
  color: white;
  border-bottom-color: white;
  background: rgba(255, 255, 255, 0.3);
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

/* Calculators */
.calculators-content {
  padding: 0 3rem 3rem 3rem;
  overflow-y: auto;
  flex: 1;
  min-height: 0;
}

.calculators-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  max-width: 1600px;
  margin: 0 auto;
}

@media (min-width: 1200px) {
  .calculators-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

.calc-card {
  background: white;
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  border: 1px solid #e5e7eb;
}

.calc-title {
  margin: 0 0 0.5rem 0;
  font-size: 1.125rem;
  font-weight: 700;
  color: #1f2937;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.calc-description {
  margin: 0 0 1.25rem 0;
  font-size: 0.8125rem;
  color: #6b7280;
  line-height: 1.5;
}

.calc-fields {
  display: flex;
  flex-direction: column;
  gap: 0.875rem;
  margin-bottom: 1.25rem;
}

.calc-fields-horizontal {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 1rem;
  flex-wrap: wrap;
}

.calc-fields-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.625rem;
  margin-bottom: 1rem;
}

.strain-fields {
  display: flex;
  flex-direction: column;
  gap: 0.625rem;
  margin-bottom: 0.75rem;
}

.strain-field {
  display: flex;
  flex-direction: column;
  gap: 0.375rem;
}

.strain-field label {
  font-size: 0.8125rem;
  font-weight: 600;
  color: #374151;
  margin: 0;
}

.strain-field input {
  height: 2.5rem;
  padding: 0.625rem 2.5rem 0.625rem 0.75rem;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  font-size: 0.9375rem;
  transition: all 0.2s;
}

.strain-field input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.strain-input-group {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.strain-input-group .input-with-unit {
  flex: 1;
}

.btn-remove-strain {
  width: 28px;
  height: 28px;
  min-width: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #fee2e2;
  color: #dc2626;
  border: none;
  border-radius: 6px;
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1;
  cursor: pointer;
  transition: all 0.2s;
  padding: 0;
}

.btn-remove-strain:hover {
  background: #fecaca;
  transform: scale(1.05);
}

.btn-add-strain {
  width: 100%;
  padding: 0.625rem;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  color: #0369a1;
  border: 1px dashed #bae6fd;
  border-radius: 8px;
  font-size: 0.8125rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  margin-bottom: 0.75rem;
}

.btn-add-strain:hover {
  background: linear-gradient(135deg, #e0f2fe 0%, #bae6fd 100%);
  border-color: #7dd3fc;
}

.cart-daily-value {
  min-width: 60px;
  padding: 0.375rem 0.75rem;
  background: linear-gradient(135deg, #dbeafe 0%, #bfdbfe 100%);
  color: #1e40af;
  border-radius: 6px;
  font-size: 0.875rem;
  font-weight: 700;
  text-align: center;
  white-space: nowrap;
}

.calc-field {
  display: flex;
  flex-direction: column;
  gap: 0.375rem;
  flex: 1;
  min-width: 0;
}

.calc-field label {
  font-size: 0.75rem;
  font-weight: 600;
  color: #374151;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.calc-field input,
.calc-field select {
  padding: 0.5rem 0.625rem;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  font-size: 0.875rem;
  color: #1f2937;
  transition: all 0.2s;
  background: white;
}

.calc-field input:focus,
.calc-field select:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.input-with-unit {
  position: relative;
  display: flex;
  align-items: center;
}

.input-with-unit input {
  flex: 1;
  padding-right: 2.5rem;
}

.input-unit {
  position: absolute;
  right: 0.75rem;
  color: #9ca3af;
  font-size: 0.875rem;
  font-weight: 500;
  pointer-events: none;
  user-select: none;
}

.calc-card.compact {
  padding: 1.25rem;
}

.calc-card.compact .calc-title {
  font-size: 1rem;
  margin-bottom: 0.375rem;
}

.calc-card.compact .calc-description {
  font-size: 0.75rem;
  margin-bottom: 1rem;
}

.calc-result.compact {
  padding: 0.75rem;
}

.calc-result.compact .result-label {
  font-size: 0.6875rem;
  margin-bottom: 0.375rem;
}

.calc-result.compact .result-value {
  font-size: 1.125rem;
}

.calc-result {
  padding: 1rem;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  border-radius: 12px;
  border: 1px solid #bae6fd;
}

.result-label {
  font-size: 0.75rem;
  font-weight: 700;
  color: #0369a1;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.5rem;
}

.result-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: #0369a1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.75rem;
  flex-wrap: nowrap;
}

.result-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0;
}

.result-row:not(:last-child) {
  border-bottom: 1px solid rgba(59, 130, 246, 0.15);
}

.result-row .result-label {
  margin: 0;
  color: #64748b;
  font-weight: 500;
  font-size: 0.875rem;
}

.result-row .result-value {
  font-size: 1.25rem;
  color: #1e40af;
  font-weight: 700;
}

.limit-badge {
  display: inline-flex;
  align-items: center;
  padding: 0.375rem 0.875rem;
  border-radius: 9999px;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.025em;
  white-space: nowrap;
  flex-shrink: 0;
  margin-left: auto;
}

.within-limit .limit-badge {
  background: #10b981;
  color: white;
  box-shadow: 0 1px 3px rgba(16, 185, 129, 0.3);
}

.over-limit {
  color: #dc2626 !important;
}

.over-limit .limit-badge {
  background: #ef4444;
  color: white;
  box-shadow: 0 1px 3px rgba(239, 68, 68, 0.3);
}

/* Responsive */
@media (max-width: 1200px) {
  .content {
    grid-template-columns: 1fr;
    grid-template-rows: auto 1fr;
  }
  
  .calculators-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .app {
    padding: 1rem 0.5rem;
  }
  
  .tabs {
    margin: 0 1rem 1rem 1rem;
    border-bottom: 2px solid rgba(255, 255, 255, 0.15);
  }
  
  .tab {
    padding: 0.75rem 1rem;
    font-size: 0.8125rem;
    background: rgba(255, 255, 255, 0.2);
  }
  
  .tab.active {
    background: rgba(255, 255, 255, 0.35);
  }
  
  .calculators-content {
    padding: 0 1rem 2rem 1rem;
  }
  
  .calculators-grid {
    grid-template-columns: 1fr;
  }
  
  .calc-fields-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .calc-fields-horizontal {
    flex-direction: column;
  }
  
  .calc-field {
    min-width: 100%;
  }
  
  .limit-badge {
    font-size: 0.6875rem;
    padding: 0.3125rem 0.625rem;
  }
  
  .result-value {
    font-size: 1rem !important;
  }
  
  .calc-result.compact .result-value {
    font-size: 1rem !important;
  }
  
  .btn-add-strain {
    font-size: 0.75rem;
    padding: 0.5rem;
  }
  
  .strain-field label {
    font-size: 0.6875rem;
  }
  
  .result-row .result-label {
    font-size: 0.8125rem;
  }
  
  .result-row .result-value {
    font-size: 1.125rem;
  }
  
  .btn-remove-strain {
    width: 26px;
    height: 26px;
    min-width: 26px;
    font-size: 1.125rem;
  }
  
  .cart-daily-value {
    min-width: 50px;
    font-size: 0.75rem;
    padding: 0.3125rem 0.625rem;
  }

  .header h1 {
    font-size: 1.25rem;
  }

  .content {
    gap: 1rem;
  }
}
</style>
