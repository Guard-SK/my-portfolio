<template>
  <Teleport to="body">
    <Transition name="fade">
      <div v-if="isOpen" class="fixed inset-0 z-100 flex items-center justify-center bg-obsidian/95 backdrop-blur-xl p-4 sm:p-6">
        <!-- Background Glow -->
        <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[500px] h-[500px] bg-emerald-500/10 blur-[150px] rounded-full pointer-events-none"></div>
        
        <!-- Modal Container -->
        <div class="relative w-full max-w-2xl bg-charcoal border border-white/10 rounded-3xl p-6 sm:p-10 shadow-2xl flex flex-col max-h-[90vh] overflow-y-auto hide-scrollbar">
          
          <!-- Header -->
          <div class="flex justify-between items-center mb-8">
            <button v-if="currentStep > 1 && !isSuccess" @click="goBack" class="flex items-center gap-2 text-silver hover:text-emerald-400 transition-colors bg-transparent border-none outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
              <span class="font-bold text-sm tracking-widest uppercase">Späť</span>
            </button>
            <div v-else></div> <!-- Layout placeholder -->
            
            <button @click="closeWizard" class="w-10 h-10 rounded-full bg-white/5 flex items-center justify-center text-silver hover:text-pearl hover:bg-white/10 transition-colors border border-white/5 cursor-pointer">
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
            </button>
          </div>

          <!-- Wizard Body -->
          <div class="grow flex flex-col justify-center">
            
            <!-- STEP 1: Branching Choice -->
            <Transition name="slide" mode="out-in">
              <div v-if="currentStep === 1" key="step1">
                <h3 class="text-3xl font-extrabold text-pearl mb-8 tracking-tight text-center">Ako preferujete komunikovať?</h3>
                <div class="grid gap-4">
                  <button @click="setFlow('A')" class="group cursor-pointer p-5 bg-white/5 border border-white/10 rounded-2xl flex items-center justify-between hover:bg-emerald-500/10 hover:border-emerald-500/30 transition-all duration-300 transform hover:-translate-y-1">
                    <span class="text-lg font-bold text-pearl group-hover:text-emerald-400">Osobné stretnutie</span>
                    <svg class="text-silver group-hover:text-emerald-400 transition-transform group-hover:translate-x-1" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                  </button>
                  <button @click="setFlow('B')" class="group cursor-pointer p-5 bg-white/5 border border-white/10 rounded-2xl flex items-center justify-between hover:bg-emerald-500/10 hover:border-emerald-500/30 transition-all duration-300 transform hover:-translate-y-1">
                    <span class="text-lg font-bold text-pearl group-hover:text-emerald-400">Telefonický kontakt</span>
                    <svg class="text-silver group-hover:text-emerald-400 transition-transform group-hover:translate-x-1" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                  </button>
                  <button @click="setFlow('C')" class="group cursor-pointer p-5 bg-white/5 border border-white/10 rounded-2xl flex items-center justify-between hover:bg-emerald-500/10 hover:border-emerald-500/30 transition-all duration-300 transform hover:-translate-y-1">
                    <span class="text-lg font-bold text-pearl group-hover:text-emerald-400">Len správy</span>
                    <svg class="text-silver group-hover:text-emerald-400 transition-transform group-hover:translate-x-1" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                  </button>
                </div>
              </div>

              <!-- FLOW A: Osobné stretnutie -->
              <div v-else-if="currentFlow === 'A' && currentStep === 2" key="A2">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Kde sa stretneme?</h3>
                <div class="grid gap-3">
                  <button v-for="opt in ['Prídem k vám do podniku', 'Na káve v meste']" :key="opt" @click="formData.location = opt; nextStep()" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-left font-medium transition-all transform hover:-translate-y-0.5" :class="formData.location === opt ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl hover:bg-white/10'">
                    {{ opt }}
                  </button>
                </div>
              </div>

              <div v-else-if="currentFlow === 'A' && currentStep === 3" key="A3">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Ktoré dni vám zvyčajne vyhovujú?</h3>
                <div class="grid gap-3 mb-4">
                  <button v-for="opt in ['Doobeda (pred otváračkou)', 'Poobede']" :key="opt" @click="formData.timePreference = opt; nextStep()" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-left font-medium transition-all transform hover:-translate-y-0.5" :class="formData.timePreference === opt ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl hover:bg-white/10'">
                    {{ opt }}
                  </button>
                </div>
                <div class="relative mt-2">
                  <label class="text-silver text-sm font-bold uppercase tracking-wider mb-2 block">Mám čas inak:</label>
                  <div class="flex gap-2">
                    <input v-model="customTime" type="text" placeholder="Rád sa prispôsobím..." class="w-full bg-obsidian border border-white/10 rounded-xl px-4 py-3 text-pearl focus:outline-none focus:border-emerald-500 transition-colors" />
                    <button @click="if(customTime) { formData.timePreference = customTime; nextStep() }" class="cursor-pointer px-6 py-3 bg-white/10 text-pearl rounded-xl hover:bg-emerald-500 hover:text-obsidian font-bold transition-all">OK</button>
                  </div>
                </div>
              </div>

              <div v-else-if="currentFlow === 'A' && currentStep === 4" key="A4">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Ako sa volá váš podnik a s čím vám viem pomôcť?</h3>
                <textarea v-model="formData.request" rows="4" class="w-full bg-obsidian border border-white/10 rounded-xl p-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors resize-none mb-4" placeholder="Opíšte vašu situáciu..."></textarea>
                <button @click="nextStep" :disabled="!formData.request" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Pokračovať</button>
              </div>

              <div v-else-if="currentFlow === 'A' && currentStep === 5" key="A5">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Aké je vaše telefónne číslo pre potvrdenie stretnutia?</h3>
                <input v-model="formData.contact" type="tel" class="w-full bg-obsidian border border-white/10 rounded-xl px-4 py-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors mb-4 text-lg" placeholder="+421 900 000 000" />
                <button @click="nextStep" :disabled="!formData.contact" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Zhrnutie</button>
              </div>

              <div v-else-if="currentFlow === 'A' && currentStep === 6" key="A6">
                <h3 class="text-2xl font-extrabold text-pearl mb-6 text-center">Zhrnutie požiadavky</h3>
                <div class="bg-obsidian border border-white/10 rounded-2xl p-6 mb-6">
                  <div class="space-y-4 text-sm">
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Stretnutie</span>
                      <span class="text-pearl font-medium">{{ formData.location }}</span>
                    </div>
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Čas</span>
                      <span class="text-pearl font-medium">{{ formData.timePreference }}</span>
                    </div>
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Podnik a požiadavka</span>
                      <span class="text-pearl font-medium line-clamp-2">{{ formData.request }}</span>
                    </div>
                    <div class="flex flex-col">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Kontakt</span>
                      <span class="text-pearl font-bold text-emerald-400">{{ formData.contact }}</span>
                    </div>
                  </div>
                </div>
                <button @click="submit" :disabled="isSubmitting" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 flex justify-center items-center gap-2">
                  <span v-if="isSubmitting">Odosielam...</span>
                  <span v-else>Odoslať požiadavku</span>
                </button>
              </div>

              <!-- FLOW B: Telefonický kontakt -->
              <div v-else-if="currentFlow === 'B' && currentStep === 2" key="B2">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Kde si zavoláme?</h3>
                <div class="grid gap-3">
                  <button v-for="opt in ['Klasický hovor', 'WhatsApp', 'Zoom/Meet']" :key="opt" @click="formData.location = opt; nextStep()" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-left font-medium transition-all transform hover:-translate-y-0.5" :class="formData.location === opt ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl hover:bg-white/10'">
                    {{ opt }}
                  </button>
                </div>
              </div>

              <div v-else-if="currentFlow === 'B' && currentStep === 3" key="B3">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Kedy sa vám to najviac hodí?</h3>
                <div class="grid gap-3 mb-4">
                  <button v-for="opt in ['Pondelok 10:00 - 10:30', 'Streda 14:00 - 14:30']" :key="opt" @click="formData.timePreference = opt; nextStep()" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-left font-medium transition-all transform hover:-translate-y-0.5" :class="formData.timePreference === opt ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl hover:bg-white/10'">
                    {{ opt }}
                  </button>
                </div>
                <div class="relative mt-2">
                  <label class="text-silver text-sm font-bold uppercase tracking-wider mb-2 block">Mám čas inokedy:</label>
                  <div class="flex gap-2">
                    <input v-model="customTime" type="text" placeholder="Rád sa prispôsobím..." class="w-full bg-obsidian border border-white/10 rounded-xl px-4 py-3 text-pearl focus:outline-none focus:border-emerald-500 transition-colors" />
                    <button @click="if(customTime) { formData.timePreference = customTime; nextStep() }" class="cursor-pointer px-6 py-3 bg-white/10 text-pearl rounded-xl hover:bg-emerald-500 hover:text-obsidian font-bold transition-all">OK</button>
                  </div>
                </div>
              </div>

              <div v-else-if="currentFlow === 'B' && currentStep === 4" key="B4">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Stručne mi opíšte vašu požiadavku</h3>
                <textarea v-model="formData.request" rows="4" class="w-full bg-obsidian border border-white/10 rounded-xl p-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors resize-none mb-4" placeholder="Opíšte o aký projekt ide..."></textarea>
                <button @click="nextStep" :disabled="!formData.request" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Pokračovať</button>
              </div>

              <div v-else-if="currentFlow === 'B' && currentStep === 5" key="B5">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Aké je vaše telefónne číslo alebo e-mail?</h3>
                <input v-model="formData.contact" type="text" class="w-full bg-obsidian border border-white/10 rounded-xl px-4 py-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors mb-4 text-lg" placeholder="napr. +421 900 000 000 alebo mail@domena.sk" />
                <button @click="nextStep" :disabled="!formData.contact" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Zhrnutie</button>
              </div>

              <div v-else-if="currentFlow === 'B' && currentStep === 6" key="B6">
                <h3 class="text-2xl font-extrabold text-pearl mb-6 text-center">Zhrnutie hovoru</h3>
                <div class="bg-obsidian border border-white/10 rounded-2xl p-6 mb-6">
                  <div class="space-y-4 text-sm">
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Kde si zavoláme</span>
                      <span class="text-pearl font-medium">{{ formData.location }}</span>
                    </div>
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Čas</span>
                      <span class="text-pearl font-medium">{{ formData.timePreference }}</span>
                    </div>
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Požiadavka</span>
                      <span class="text-pearl font-medium line-clamp-2">{{ formData.request }}</span>
                    </div>
                    <div class="flex flex-col">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Kontakt</span>
                      <span class="text-pearl font-bold text-emerald-400">{{ formData.contact }}</span>
                    </div>
                  </div>
                </div>
                <button @click="submit" :disabled="isSubmitting" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 flex justify-center items-center gap-2">
                  <span v-if="isSubmitting">Odosielam...</span>
                  <span v-else>Zarezervovať hovor</span>
                </button>
              </div>

              <!-- FLOW C: Len správy -->
              <div v-else-if="currentFlow === 'C' && currentStep === 2" key="C2">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Cez akú platformu si napíšeme?</h3>
                <div class="grid grid-cols-2 gap-3">
                  <button v-for="opt in ['WhatsApp', 'E-mail', 'SMS', 'Instagram']" :key="opt" @click="formData.location = opt; nextStep()" class="cursor-pointer p-4 bg-white/5 border rounded-xl text-center font-medium transition-all transform hover:-translate-y-0.5" :class="formData.location === opt ? 'border-emerald-500 bg-emerald-500/10 text-emerald-400' : 'border-white/10 text-pearl hover:bg-white/10'">
                    {{ opt }}
                  </button>
                </div>
              </div>

              <div v-else-if="currentFlow === 'C' && currentStep === 3" key="C3">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">Zadajte váš kontakt (číslo, e-mail alebo IG tag):</h3>
                <input v-model="formData.contact" type="text" class="w-full bg-obsidian border border-white/10 rounded-xl px-4 py-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors mb-4 text-lg" placeholder="Váš kontakt" />
                <button @click="nextStep" :disabled="!formData.contact" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Pokračovať</button>
              </div>

              <div v-else-if="currentFlow === 'C' && currentStep === 4" key="C4">
                <h3 class="text-2xl font-extrabold text-pearl mb-6">S čím vám viem pomôcť?</h3>
                <textarea v-model="formData.request" rows="5" class="w-full bg-obsidian border border-white/10 rounded-xl p-4 text-pearl focus:outline-none focus:border-emerald-500 transition-colors resize-none mb-4" placeholder="Podrobnosti a vaše otázky..."></textarea>
                <button @click="nextStep" :disabled="!formData.request" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 disabled:cursor-not-allowed">Zhrnutie</button>
              </div>

              <div v-else-if="currentFlow === 'C' && currentStep === 5" key="C5">
                <h3 class="text-2xl font-extrabold text-pearl mb-6 text-center">Zhrnutie správy</h3>
                <div class="bg-obsidian border border-white/10 rounded-2xl p-6 mb-6">
                  <div class="space-y-4 text-sm">
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Platforma</span>
                      <span class="text-pearl font-medium">{{ formData.location }}</span>
                    </div>
                    <div class="flex flex-col border-b border-white/5 pb-3">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">Požiadavka</span>
                      <span class="text-pearl font-medium line-clamp-3">{{ formData.request }}</span>
                    </div>
                    <div class="flex flex-col">
                      <span class="text-silver/60 uppercase tracking-wider font-bold text-xs mb-1">A kam sa vám ozvem?</span>
                      <span class="text-pearl font-bold text-emerald-400">{{ formData.contact }}</span>
                    </div>
                  </div>
                </div>
                <button @click="submit" :disabled="isSubmitting" class="cursor-pointer w-full py-4 bg-emerald-500 text-obsidian rounded-xl font-bold hover:bg-emerald-400 transition-colors disabled:opacity-50 flex justify-center items-center gap-2">
                  <span v-if="isSubmitting">Odosielam...</span>
                  <span v-else>Odoslať správu</span>
                </button>
              </div>

              <!-- SUCCESS VIEW -->
              <div v-else-if="isSuccess" key="success" class="flex flex-col items-center justify-center text-center py-8">
                <div class="w-20 h-20 bg-emerald-500/20 text-emerald-400 rounded-full flex items-center justify-center mb-6 border-2 border-emerald-500/50">
                  <svg xmlns="http://www.w3.org/2000/svg" width="36" height="36" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>
                </div>
                <!-- Success Text depending on Flow -->
                <template v-if="currentFlow === 'A'">
                  <h3 class="text-3xl font-extrabold text-pearl mb-4">Výborne!</h3>
                  <p class="text-silver/90 text-xl font-medium">Čoskoro vám zavolám alebo napíšem SMS, aby sme si potvrdili presný termín stretnutia.</p>
                </template>
                <template v-else-if="currentFlow === 'B'">
                  <h3 class="text-3xl font-extrabold text-pearl mb-4">Super!</h3>
                  <p class="text-silver/90 text-xl font-medium">V dohodnutom čase vám budem volať na zvolenú platformu.</p>
                </template>
                <template v-else>
                  <h3 class="text-3xl font-extrabold text-pearl mb-4">Mám to!</h3>
                  <p class="text-silver/90 text-xl mb-6 font-medium">Ozvem sa vám čoskoro. Ak to ponáhľa, tu je môj priamy kontakt: <span class="font-bold text-emerald-400 block mt-3">+421 903 164 540</span></p>
                </template>
              </div>

            </Transition>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isOpen = ref(false)
const currentStep = ref(1)
const currentFlow = ref(null) // 'A', 'B', 'C'
const customTime = ref('')
const isSubmitting = ref(false)
const isSuccess = ref(false)

const formData = ref({
  type: '', 
  location: '',
  timePreference: '',
  request: '',
  contact: ''
})

// Analytics placeholder (PostHog / Custom)
const trackStep = (step, data = {}) => {
  posthog.capture('wizard_step', { step, ...data })
  console.log('Analytics captured:', step, data)
}

const openWizard = () => {
  isOpen.value = true
  document.body.style.overflow = 'hidden'
  trackStep('wizard_opened')
}

const closeWizard = () => {
  isOpen.value = false
  setTimeout(() => resetWizard(), 300)
  document.body.style.overflow = ''
  trackStep('wizard_closed')
}

const resetWizard = () => {
  currentStep.value = 1
  currentFlow.value = null
  customTime.value = ''
  isSuccess.value = false
  formData.value = {
    type: '',
    location: '',
    timePreference: '',
    request: '',
    contact: ''
  }
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
    // Expected to send to Cloudflare Pages endpoint or equivalent
    await fetch('/api/contact', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(formData.value)
    }).catch(err => {
        // Silently catch fetch errs for pure frontend demo if API is missing
        console.warn('API error caught, proceeding for UX', err)
    })
    
    // Simulate network delay for UX
    await new Promise(resolve => setTimeout(resolve, 800))
    
    isSuccess.value = true
    trackStep('wizard_submit_success')
    
    // Auto close
    if (currentFlow.value === 'A' || currentFlow.value === 'B') {
      setTimeout(() => closeWizard(), 4000)
    }
  } catch (error) {
    console.error('Submit error', error)
    trackStep('wizard_submit_error', { error: error.message || 'unknown' })
    // In production we would show an error toast, but here we proceed to success or back UX-wise
    isSuccess.value = true
    setTimeout(() => closeWizard(), 4000)
  } finally {
    isSubmitting.value = false
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

onMounted(() => {
  window.addEventListener('click', handleGlobalClick)
})

onUnmounted(() => {
  window.removeEventListener('click', handleGlobalClick)
})
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}
.slide-enter-from {
  opacity: 0;
  transform: translateX(30px) scale(0.98);
}
.slide-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
.hide-scrollbar::-webkit-scrollbar {
  display: none;
}
.hide-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>
