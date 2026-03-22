<template>
  <Teleport to="body" v-if="isMounted">
    <Transition name="fade">
      <div v-if="isOpen" class="fixed inset-0 z-100 flex flex-col bg-obsidian overflow-hidden">
        <!-- Background Glows -->
        <div class="absolute top-0 right-0 w-[600px] h-[600px] bg-emerald-500/5 blur-[120px] rounded-full pointer-events-none -translate-y-1/2 translate-x-1/2"></div>
        <div class="absolute bottom-0 left-0 w-[500px] h-[500px] bg-emerald-500/5 blur-[150px] rounded-full pointer-events-none translate-y-1/2 -translate-x-1/2"></div>

        <!-- Header -->
        <div class="relative z-10 flex justify-between items-center px-6 py-6 sm:px-12 sm:py-8 bg-obsidian/50 backdrop-blur-md border-b border-white/5">
          <div class="flex items-center gap-4">
            <button v-if="currentStep > 0 && !isSuccess" @click="goBack" class="group flex items-center gap-2 text-silver hover:text-emerald-400 transition-all bg-transparent border-none outline-none cursor-pointer">
              <div class="w-8 h-8 rounded-full bg-white/5 flex items-center justify-center group-hover:bg-emerald-500/10 transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
              </div>
              <span class="font-bold text-xs tracking-widest uppercase hidden sm:inline">Späť</span>
            </button>
            <div v-else class="text-xs font-bold tracking-[0.3em] uppercase text-emerald-500/30">Dávid Zliechovec</div>
          </div>

          <!-- Progress bar -->
          <div class="absolute left-1/2 -translate-x-1/2 flex flex-col items-center gap-2">
            <div class="w-32 sm:w-48 h-1 bg-white/5 rounded-full overflow-hidden">
              <div class="h-full bg-emerald-500 transition-all duration-700 ease-out" :style="{ width: `${progress}%` }"></div>
            </div>
            <span class="text-[9px] text-emerald-500/40 uppercase font-bold tracking-widest h-3">
              {{ progressMessage }}
            </span>
          </div>

          <button @click="closeWizard" class="group w-10 h-10 rounded-full bg-white/5 flex items-center justify-center text-silver hover:text-pearl hover:bg-white/10 transition-all border border-white/5 cursor-pointer">
            <svg class="group-hover:rotate-90 transition-transform duration-300" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
          </button>
        </div>

        <!-- Main Content (Centered Column) -->
        <div class="grow relative z-0 flex flex-col items-center overflow-y-auto hide-scrollbar">
          <div class="w-full max-w-xl mx-auto px-6 py-12 flex flex-col min-h-full justify-center">
            <Transition name="slide" mode="out-in">

              <!-- STEP 1: Branching Choice -->
              <div v-if="currentStep === 1" key="step1">
                <h3 class="text-3xl sm:text-4xl font-extrabold text-pearl mb-10 tracking-tight text-center sm:text-left">Ako preferujete <span class="text-emerald-400">komunikovať?</span></h3>
                <div class="grid gap-4">
                  <button v-for="flow in [{id: 'A', text: 'Osobné stretnutie'}, {id: 'B', text: 'Telefonický kontakt'}, {id: 'C', text: 'Len správy'}]" :key="flow.id" @click="setFlow(flow.id)" class="group cursor-pointer p-6 bg-white/5 border border-white/10 rounded-2xl flex items-center justify-between hover:bg-emerald-500/10 hover:border-emerald-500/30 transition-all duration-300">
                    <span class="text-xl font-bold text-pearl group-hover:text-emerald-400">{{ flow.text }}</span>
                    <div class="w-10 h-10 rounded-full bg-white/5 flex items-center justify-center group-hover:bg-emerald-500/20 group-hover:text-emerald-400 transition-all">
                      <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                    </div>
                  </button>
                </div>
              </div>

              <!-- FLOW STEPS (A, B, C combined pattern) -->
              <div v-else-if="currentStep > 1 && !isSuccess" :key="currentFlow + currentStep">
                
                <!-- Step Titles -->
                <h3 class="text-3xl sm:text-4xl font-extrabold text-pearl mb-10 tracking-tight text-center sm:text-left">
                  <template v-if="currentFlow === 'A'">
                    <span v-if="currentStep === 2">Kde sa <span class="text-emerald-400">stretneme?</span></span>
                    <span v-else-if="currentStep === 3">Ktoré dni vám <span class="text-emerald-400">vyhovujú?</span></span>
                    <span v-else-if="currentStep === 4">S čím vám viem <span class="text-emerald-400">pomôcť?</span></span>
                    <span v-else-if="currentStep === 5">{{ contactHeadline.prefix }} <span class="text-emerald-400">{{ contactHeadline.highlight }}</span></span>
                    <span v-else-if="currentStep === 6">Pripravené na <span class="text-emerald-400">odoslanie.</span></span>
                  </template>
                  <template v-else-if="currentFlow === 'B'">
                    <span v-if="currentStep === 2">Kde si <span class="text-emerald-400">zavoláme?</span></span>
                    <span v-else-if="currentStep === 3">Kedy vám to <span class="text-emerald-400">najviac vyhovuje?</span></span>
                    <span v-else-if="currentStep === 4">Stručne opíšte <span class="text-emerald-400">vašu požiadavku</span></span>
                    <span v-else-if="currentStep === 5">{{ contactHeadline.prefix }} <span class="text-emerald-400">{{ contactHeadline.highlight }}</span></span>
                    <span v-else-if="currentStep === 6">Zhrnutie <span class="text-emerald-400">hovoru.</span></span>
                  </template>
                  <template v-else-if="currentFlow === 'C'">
                    <span v-if="currentStep === 2">Platforma pre <span class="text-emerald-400">písanie?</span></span>
                    <span v-else-if="currentStep === 3">{{ contactHeadline.prefix }} <span class="text-emerald-400">{{ contactHeadline.highlight }}</span></span>
                    <span v-else-if="currentStep === 4">S čím vám viem <span class="text-emerald-400">pomôcť?</span></span>
                    <span v-else-if="currentStep === 5">Zhrnutie <span class="text-emerald-400">správy.</span></span>
                  </template>
                </h3>

                <!-- Step Content -->
                <div class="space-y-6">
                  <!-- Options grid for multiple choice -->
                  <div v-if="(currentStep === 2) || (currentStep === 3 && (currentFlow === 'A' || currentFlow === 'B'))">
                    <!-- Many options (Grouped by day for Flow B) -->
                    <div v-if="groupedOptions" class="max-h-[50vh] overflow-y-auto pr-3 custom-scrollbar space-y-12 py-4">
                      <div v-for="(slots, day) in groupedOptions" :key="day" class="space-y-4">
                        <div class="flex items-center gap-4">
                          <h4 class="text-emerald-500/50 text-[10px] font-bold uppercase tracking-[0.3em] whitespace-nowrap">{{ day }}</h4>
                          <div class="h-px grow bg-white/5"></div>
                        </div>
                        <div class="grid grid-cols-2 sm:grid-cols-3 gap-2">
                          <button v-for="slot in slots" :key="slot.full" @click="handleOptionSelect(slot.full)" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-center text-xs transition-all hover:bg-white/10" :class="isOptionSelected(slot.full) ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl/80 font-bold'">
                            {{ slot.label }}
                          </button>
                        </div>
                      </div>
                    </div>

                    <!-- Regular grid -->
                    <div v-else class="grid gap-3" :class="getOptions().length > 4 ? 'grid-cols-2' : 'grid-cols-1'">
                      <button v-for="opt in getOptions()" :key="opt" @click="handleOptionSelect(opt)" class="cursor-pointer p-6 bg-white/5 border rounded-2xl text-left transition-all hover:bg-white/10" :class="isOptionSelected(opt) ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl font-bold'">
                        {{ opt }}
                      </button>
                    </div>

                    <!-- Custom location input for step 2 -->
                    <div v-if="currentStep === 2 && currentFlow === 'A'" class="mt-8 border-t border-white/5 pt-8">
                      <div class="flex gap-2">
                        <input v-model="customLocation" type="text" placeholder="Iné (zadajte miesto)..." class="grow bg-white/5 border border-white/10 rounded-2xl px-6 py-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors" @keyup.enter="if(customLocation) { formData.location = customLocation; nextStep() }" />
                        <button @click="if(customLocation) { formData.location = customLocation; nextStep() }" class="cursor-pointer px-8 py-4 bg-white/10 text-pearl rounded-2xl hover:bg-emerald-500 hover:text-obsidian font-bold transition-all disabled:opacity-30" :disabled="!customLocation">OK</button>
                      </div>
                    </div>

                    <!-- Custom time input for step 3 -->
                    <div v-if="currentStep === 3" class="mt-8 border-t border-white/5 pt-8">
                      <p class="text-emerald-500/60 text-sm italic mb-4">Rád sa Vám prispôsobím na akýkolvek čas</p>
                      <div class="flex gap-2">
                        <input v-model="customTime" type="text" :placeholder="currentFlow === 'A' ? 'Mám presný čas na mysli...' : 'Iný termín...'" class="grow bg-white/5 border border-white/10 rounded-2xl px-6 py-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors" @keyup.enter="if(customTime) { formData.timePreference = customTime; nextStep() }" />
                        <button @click="if(customTime) { formData.timePreference = customTime; nextStep() }" class="cursor-pointer px-8 py-4 bg-white/10 text-pearl rounded-2xl hover:bg-emerald-500 hover:text-obsidian font-bold transition-all disabled:opacity-30" :disabled="!customTime">OK</button>
                      </div>
                    </div>
                  </div>

                  <!-- Textarea for request -->
                  <div v-else-if="currentStep === 4">
                    <textarea v-model="formData.request" rows="5" class="w-full bg-white/5 border border-white/10 rounded-2xl p-6 text-pearl text-lg focus:outline-none focus:border-emerald-500 transition-colors resize-none mb-4" placeholder="Opíšte vašu situáciu alebo projekt..."></textarea>
                    <button @click="nextStep" :disabled="!formData.request" class="w-full py-5 bg-emerald-500 text-obsidian rounded-2xl font-bold text-lg hover:bg-emerald-400 transition-all disabled:opacity-50">Pokračovať</button>
                  </div>

                  <!-- Input for contact -->
                  <div v-else-if="(currentFlow === 'A' && currentStep === 5) || (currentFlow === 'B' && currentStep === 5) || (currentFlow === 'C' && currentStep === 3)">
                    <p class="text-silver/40 mb-4 text-sm font-medium">{{ contactTitle }}</p>
                    <input v-model="formData.contact" type="text" class="w-full bg-white/5 border border-white/10 rounded-2xl px-6 py-5 text-pearl focus:outline-none focus:border-emerald-500 transition-colors mb-4 text-2xl font-bold tracking-tight" :placeholder="contactPlaceholder" />
                    <p v-if="currentFlow === 'A'" class="text-emerald-500/60 text-xs italic mb-6">Poznámka: Preferujem internetovú komunikáciu (WhatsApp, e-mail) pred klasickými SMS.</p>
                    <button @click="nextStep" :disabled="!isContactValid" class="w-full py-5 bg-emerald-500 text-obsidian rounded-2xl font-bold text-lg hover:bg-emerald-400 transition-all disabled:opacity-50">Zhrnutie</button>
                  </div>

                  <!-- Summary steps -->
                  <div v-else-if="(currentStep === totalSteps[currentFlow])">
                    <div class="bg-white/5 border border-white/10 rounded-3xl p-6 sm:p-8 mb-8 space-y-4">
                      
                      <!-- Type of communication -->
                      <div class="flex flex-col">
                        <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] mb-1">Typ komunikácie</span>
                        <p class="text-pearl font-medium">{{ formData.type }}</p>
                      </div>

                      <!-- Location / Platform (Step 2) -->
                      <div class="flex flex-col pt-4 border-t border-white/5" v-if="formData.location">
                        <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] mb-1">{{ currentFlow === 'A' ? 'Miesto stretnutia' : 'Vybraná platforma' }}</span>
                        <p class="text-pearl font-medium">{{ formData.location }}</p>
                      </div>
                      
                      <!-- Time (Step 3 for A, B) -->
                      <div class="flex flex-col pt-4 border-t border-white/5" v-if="formData.timePreference && currentFlow !== 'C'">
                        <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] mb-1">Preferovaný čas</span>
                        <p class="text-pearl font-medium">{{ formData.timePreference }}</p>
                      </div>

                      <!-- Request -->
                      <div class="flex flex-col pt-4 border-t border-white/5">
                        <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] mb-1">Vaša požiadavka</span>
                        <p class="text-pearl font-medium line-clamp-3">{{ formData.request }}</p>
                      </div>
                      
                      <!-- Contact -->
                      <div class="flex flex-col pt-4 border-t border-white/5">
                        <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] mb-1">Váš kontakt</span>
                        <p class="text-2xl font-extrabold text-emerald-400 break-all">{{ formData.contact }}</p>
                      </div>
                    </div>
                    <button @click="submit" :disabled="isSubmitting" class="group cursor-pointer w-full py-5 bg-emerald-500 text-obsidian rounded-2xl font-extrabold text-xl hover:bg-emerald-400 transition-all disabled:opacity-50 flex justify-center items-center gap-3 shadow-lg shadow-emerald-500/20">
                      <span v-if="isSubmitting">Odosielam...</span>
                      <span v-else>Potvrdiť a odoslať</span>
                    </button>
                  </div>
                </div>
              </div>

              <!-- SUCCESS VIEW -->
              <div v-else-if="isSuccess" key="success" class="flex flex-col items-center text-center">
                <div class="w-20 h-20 bg-emerald-500 text-obsidian rounded-full flex items-center justify-center mb-10 shadow-lg shadow-emerald-500/20">
                  <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>
                </div>
                <h3 class="text-4xl font-extrabold text-pearl mb-6">Mám to, ozvem sa!</h3>
                <p class="text-silver/60 text-xl font-medium leading-relaxed mb-10 max-w-md">Vaša požiadavka bola úspešne odoslaná. Budem vás kontaktovať čo najskôr.</p>
                <div class="w-full p-6 bg-white/5 border border-white/10 rounded-2xl mb-12">
                  <span class="text-silver/30 uppercase tracking-widest font-bold text-[10px] block mb-2">Môj priamy kontakt</span>
                  <span class="text-2xl font-extrabold text-emerald-400">+421 903 164 540</span>
                </div>
                <button @click="closeWizard" class="text-silver/40 hover:text-pearl font-bold text-sm tracking-widest uppercase transition-colors border-b border-white/5 hover:border-pearl pb-1">Zavrieť a späť na web</button>
              </div>

            </Transition>
          </div>
        </div>

        <!-- Footer -->
        <div class="relative z-10 px-6 py-6 border-t border-white/5 text-center">
          <p class="text-[10px] text-silver/20 uppercase font-bold tracking-[0.2em]">Bezpečné odoslanie &copy; {{ new Date().getFullYear() }} Dávid Zliechovec</p>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const isMounted = ref(false)
const isOpen = ref(false)
const currentStep = ref(1) // Start with 0 (Intro)
const currentFlow = ref(null) // 'A', 'B', 'C'
const customTime = ref('')
const customLocation = ref('')
const isSubmitting = ref(false)
const isSuccess = ref(false)

const formData = ref({
  type: '', 
  location: '',
  timePreference: '',
  request: '',
  contact: ''
})

// Grouping logic for many options (e.g. time slots)
const groupedOptions = computed(() => {
  if (currentStep.value === 3 && (currentFlow.value === 'B' || currentFlow.value === 'A')) {
    const options = getOptions()
    const groups = {}
    options.forEach(opt => {
      const parts = opt.split(' ')
      const day = parts[0]
      const time = parts.slice(1).join(' ')
      if (!groups[day]) groups[day] = []
      groups[day].push({ label: time, full: opt })
    })
    return groups
  }
  return null
})

// Progress calculation
const totalSteps = { 'A': 6, 'B': 6, 'C': 5 }
const progress = computed(() => {
  if (isSuccess.value) return 100
  if (currentStep.value === 0) return 5
  if (!currentFlow.value) return 15
  const flowTotal = totalSteps[currentFlow.value] || 6
  return Math.min(Math.round((currentStep.value / flowTotal) * 100), 100)
})

const progressMessage = computed(() => {
  if (isSuccess.value) return 'Úspešne odoslané'
  if (currentStep.value === 1) return 'Vyberte si cestu'
  
  if (!currentFlow.value) return ''
  
  const flowTotal = totalSteps[currentFlow.value]
  if (currentStep.value === flowTotal) return 'Už stačí len potvrdiť'
  if (currentStep.value === flowTotal - 1) return 'Posledné údaje'
  
  const p = progress.value
  if (p < 30) return 'Už to skoro bude'
  if (p < 60) return 'Ešte pár krokov'
  return 'Už to skoro bude'
})

// Dynamic contact messaging
const contactHeadline = computed(() => {
  if (currentFlow.value === 'A') return { prefix: 'Kam sa vám', highlight: 'môžem ozvať?' }
  const loc = formData.value.location
  if (loc === 'WhatsApp' || loc === 'Klasicky cez hovor' || loc === 'SMS') return { prefix: 'Aké je vaše', highlight: 'číslo?' }
  if (loc === 'Zoom/Meet' || loc === 'E-mail') return { prefix: 'Aký je váš', highlight: 'e-mail?' }
  if (loc === 'Instagram') return { prefix: 'Váš Instagram', highlight: 'profil?' }
  return { prefix: 'Kam sa vám', highlight: 'môžem ozvať?' }
})

const contactTitle = computed(() => {
  if (currentFlow.value === 'A') return 'Kam sa vám môžem ozvať?'
  const loc = formData.value.location
  if (loc === 'WhatsApp') return 'Aké telefónne číslo používate na WhatsAppe?'
  if (loc === 'Klasicky cez hovor') return 'Na aké číslo vám môžem zavolať?'
  if (loc === 'SMS') return 'Na aké číslo vám môžem napísať SMS?'
  if (loc === 'Zoom/Meet') return 'Na aký e-mail vám pošlem pozvánku?'
  if (loc === 'E-mail') return 'Na aký e-mail vám môžem napísať?'
  if (loc === 'Instagram') return 'Aký je váš Instagramový profil (začína @)?'
  return 'Kam sa vám môžem ozvať?'
})

const contactPlaceholder = computed(() => {
  if (currentFlow.value === 'A') return 'e-mail alebo telefón'
  const loc = formData.value.location
  if (loc === 'WhatsApp' || loc === 'Klasicky cez hovor' || loc === 'SMS') return '+421 9XX XXX XXX'
  if (loc === 'Zoom/Meet' || loc === 'E-mail') return 'váš@email.sk'
  if (loc === 'Instagram') return '@vas_profil'
  return 'e-mail alebo telefón'
})

// Validation
const isContactValid = computed(() => {
  const val = formData.value.contact.trim()
  if (!val) return false
  
  const loc = formData.value.location
  if (currentFlow.value !== 'A' && loc) {
    if (loc === 'WhatsApp' || loc === 'Klasicky cez hovor' || loc === 'SMS') {
      return val.replace(/[\s\+]/g, '').length >= 9 && /^[0-9\s\+]+$/.test(val)
    }
    if (loc === 'Zoom/Meet' || loc === 'E-mail') {
      return val.includes('@') && val.includes('.') && val.length > 5
    }
    if (loc === 'Instagram') {
      return val.length >= 2
    }
  }
  
  // Basic fallback
  const isEmail = val.includes('@') && val.includes('.') && val.length > 5
  const isPhone = val.replace(/[\s\+]/g, '').length >= 9 && /^[0-9\s\+]+$/.test(val)
  return isEmail || isPhone
})

// Analytics placeholder (PostHog / Custom)
const trackStep = (step, data = {}) => {
  if (typeof posthog !== 'undefined') {
    posthog.capture('wizard_step', { step, ...data })
  }
  console.log('Analytics captured:', step, data)
}

const openWizard = () => {
  isOpen.value = true
  document.body.style.overflow = 'hidden'
  trackStep('wizard_opened')
  // Reset scroll on open
  window.scrollTo(0, 0)
}

const closeWizard = () => {
  isOpen.value = false
  setTimeout(() => resetWizard(), 300)
  document.body.style.overflow = ''
  trackStep('wizard_closed')
}

const resetWizard = () => {
  currentStep.value = 1 // Back to selection step
  currentFlow.value = null
  customTime.value = ''
  customLocation.value = ''
  isSuccess.value = false
  formData.value = {
    type: '',
    location: '',
    timePreference: '',
    request: '',
    contact: ''
  }
}

const getOptions = () => {
  if (currentStep.value === 2) {
    if (currentFlow.value === 'A') return ['Prídem k vám do podniku', 'Na káve v meste']
    if (currentFlow.value === 'B') return ['Klasicky cez hovor', 'WhatsApp', 'Zoom/Meet']
    if (currentFlow.value === 'C') return ['WhatsApp', 'E-mail', 'SMS', 'Instagram']
  }
  if (currentStep.value === 3) {
    if (currentFlow.value === 'A') return [
      'Pondelok Doobeda', 'Pondelok Poobede',
      'Utorok Doobeda', 'Utorok Poobede',
      'Streda Doobeda', 'Streda Poobede',
      'Štvrtok Doobeda', 'Štvrtok Poobede',
      'Piatok Doobeda', 'Piatok Poobede',
      'Sobota Doobeda', 'Sobota Poobede',
      'Nedeľa Doobeda', 'Nedeľa Poobede'
    ]
    if (currentFlow.value === 'B') return ['Pondelok 15:00 - 15:30', 'Pondelok 16:00 - 16:30', 'Pondelok 16:30 - 17:00', 'Pondelok 19:00 - 19:30', 'Pondelok 20:00 - 20:30', 'Utorok 16:00 - 16:30', 'Utorok 16:30 - 17:00', 'Utorok 17:00 - 17:30', 'Utorok 17:30 - 18:00', 'Utorok 18:00 - 18:30', 'Utorok 18:30 - 19:00', 'Utorok 19:00 - 19:30', 'Utorok 19:30 - 20:00', 'Streda 14:30 - 15:00', 'Streda 15:00 - 15:30', 'Streda 15:30 - 16:00', 'Streda 16:00 - 16:30', 'Streda 16:30 - 17:00', 'Streda 17:00 - 17:30', 'Streda 17:30 - 18:00', 'Streda 18:00 - 18:30', 'Streda 18:30 - 19:00', 'Streda 19:00 - 19:30', 'Streda 19:30 - 20:00', 'Štvrtok 16:00 - 16:30', 'Štvrtok 16:30 - 17:00', 'Štvrtok 17:00 - 17:30', 'Štvrtok 17:30 - 18:00', 'Štvrtok 18:00 - 18:30', 'Štvrtok 18:30 - 19:00', 'Štvrtok 19:00 - 19:30', 'Štvrtok 19:30 - 20:00', 'Piatok 13:30 - 14:00', 'Piatok 14:00 - 14:30', 'Piatok 14:30 - 15:00', 'Piatok 15:00 - 15:30', 'Piatok 15:30 - 16:00', 'Piatok 16:00 - 16:30', 'Piatok 16:30 - 17:00', 'Piatok 17:00 - 17:30', 'Piatok 17:30 - 18:00', 'Piatok 18:00 - 18:30', 'Piatok 18:30 - 19:00', 'Piatok 19:00 - 19:30', 'Piatok 19:30 - 20:00', 'Sobota Navrhnite čas', 'Nedeľa Navrhnite čas']
  }
  return []
}

const handleOptionSelect = (opt) => {
  if (currentStep.value === 2) {
    formData.value.location = opt
    nextStep()
  } else if (currentStep.value === 3) {
    formData.value.timePreference = opt
    nextStep()
  }
}

const isOptionSelected = (opt) => {
  if (currentStep.value === 2) return formData.value.location === opt
  if (currentStep.value === 3) return formData.value.timePreference === opt
  return false
}

const setFlow = (flow) => {
  currentFlow.value = flow
  formData.value.type = flow === 'A' ? 'Osobné stretnutie' : (flow === 'B' ? 'Telefonický kontakt' : 'Len správy')
  currentStep.value = 2
  trackStep(`wizard_flow_${flow}`)
}

const nextStep = () => {
  if (currentFlow.value === 'A' && currentStep.value < 6) currentStep.value++
  else if (currentFlow.value === 'B' && currentStep.value < 6) currentStep.value++
  else if (currentFlow.value === 'C' && currentStep.value < 5) currentStep.value++
  customTime.value = ''
  
  trackStep('wizard_step_forward', { flow: currentFlow.value, step: currentStep.value })
}

const goBack = () => {
  if (currentStep.value > 1) {
    if (currentStep.value === 2) {
      currentFlow.value = null
    }
    currentStep.value--
    trackStep('wizard_step_back', { newStep: currentStep.value })
  }
}

const submit = async () => {
  isSubmitting.value = true
  trackStep('wizard_submit_started', formData.value)
  
  try {
    // 1. Pripravíme dáta do formátu, ktorý Web3Forms očakáva (a ktorý bude v maily pekne vyzerať)
    const payload = {
      access_key: "4f619a44-8441-40a3-9538-b59aa077455f", // Tvoj Web3Forms kľúč
      subject: `Nová požiadavka z webu: ${formData.value.type}`, // Predmet e-mailu
      from_name: "Web Portfólio - Kontakt",
      
      // Samotné dáta z formulára (Pomenuj si kľúče tak, ako ich chceš vidieť v e-maile)
      Typ_komunikácie: formData.value.type,
      Preferované_miesto_alebo_platforma: formData.value.location || 'Nezadané',
      Preferovaný_čas: formData.value.timePreference || 'Nezadané',
      Text_požiadavky: formData.value.request || 'Nezadané',
      Kontakt_na_klienta: formData.value.contact || 'Nezadané'
    };

    // 2. Odošleme to cez fetch na ich API
    const response = await fetch('https://api.web3forms.com/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
      body: JSON.stringify(payload)
    });

    const result = await response.json();

    // 3. Skontrolujeme, či to prešlo
    if (response.ok) {
      isSuccess.value = true;
      trackStep('wizard_submit_success');
      
      // Auto close po 6 sekundách pre plynulý zážitok
      if (currentFlow.value === 'A' || currentFlow.value === 'B') {
        setTimeout(() => closeWizard(), 6000);
      }
    } else {
      // Ak API vráti chybu (napr. zlý kľúč)
      console.error('Web3Forms Error:', result);
      throw new Error(result.message || 'Chyba zo strany Web3Forms');
    }

  } catch (error) {
    console.error('Submit error', error)
    trackStep('wizard_submit_error', { error: error.message || 'unknown' })
    
    // Fallback pre užívateľa, ak by zlyhalo pripojenie
    alert("Ospravedlňujem sa, formulár sa nepodarilo odoslať. Skúste mi prosím napísať priamo na e-mail alebo Instagram.");
  } finally {
    isSubmitting.value = false;
  }
}

// Global click event interception to easily connect the component without prop drilling
const handleGlobalClick = (e) => {
  const trigger = e.target.closest('[data-trigger-wizard]')
  if (trigger) {
    e.preventDefault()
    openWizard()
  }
}

// Keyboard support
const handleKeyDown = (e) => {
  if (!isOpen.value) return
  
  if (e.key === 'Escape') {
    closeWizard()
  }
  
  if (e.key === 'Enter') {
    // Prevent default to avoid form submission if any
    e.preventDefault()
    
    // Logic for "Continue" button
    if (currentStep.value === 0) startWizard()
    else if (currentStep.value === 1 && currentFlow.value) setFlow(currentFlow.value) // Should be handled by click
    else if (currentStep.value === 4 && currentFlow.value === 'A' && formData.value.request) nextStep()
    else if (currentStep.value === 5 && currentFlow.value === 'A' && isContactValid.value) nextStep()
    else if (currentStep.value === 6 && currentFlow.value === 'A' && !isSubmitting.value) submit()
    // Add for other flows...
    else if (currentStep.value === 4 && currentFlow.value === 'B' && formData.value.request) nextStep()
    else if (currentStep.value === 5 && currentFlow.value === 'B' && isContactValid.value) nextStep()
    else if (currentStep.value === 3 && currentFlow.value === 'C' && isContactValid.value) nextStep()
    else if (currentStep.value === 4 && currentFlow.value === 'C' && formData.value.request) nextStep()
  }
}

const startWizard = () => {
  currentStep.value = 1
  trackStep('wizard_started')
}

onMounted(() => {
  isMounted.value = true
  window.addEventListener('click', handleGlobalClick)
  window.addEventListener('keydown', handleKeyDown)
})

onUnmounted(() => {
  window.removeEventListener('click', handleGlobalClick)
  window.removeEventListener('keydown', handleKeyDown)
})
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Vertical Slide for steps */
.slide-enter-active,
.slide-leave-active {
  transition: all 0.5s cubic-bezier(0.16, 1, 0.3, 1);
}

.slide-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.slide-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

.hide-scrollbar::-webkit-scrollbar {
  display: none;
}
.hide-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

input:focus, textarea:focus {
  outline: none;
  border-color: rgba(16, 185, 129, 0.5) !important;
}

.custom-scrollbar::-webkit-scrollbar {
  width: 4px;
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: rgba(16, 185, 129, 0.2);
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background: rgba(16, 185, 129, 0.4);
}

/* Simplified button styles using standard CSS to avoid @apply warnings in some environments */
.btn-primary {
  background-color: rgb(16, 185, 129);
  color: rgb(10, 10, 10);
  transition: all 0.2s ease-in-out;
  cursor: pointer;
}

.btn-primary:hover {
  background-color: rgb(52, 211, 153);
}

.btn-primary:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}
</style>
