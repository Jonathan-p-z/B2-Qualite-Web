<template>
  <section v-if="rule" class="space-y-6">
    <!-- Header -->
    <header class="space-y-3">
      <button
        @click="router.back()"
        class="inline-flex items-center gap-2 text-sm text-zinc-400 hover:text-zinc-200 transition"
      >
        ← Retour
      </button>

      <div class="text-sm text-zinc-400">Règle n° {{ rule.id }}</div>

      <h1 class="text-2xl sm:text-3xl font-semibold tracking-tight text-zinc-100">
        {{ rule.title }}
      </h1>

      <div class="text-base sm:text-sm tracking-tight text-zinc-300">
        {{ rule.description }}
      </div>

      <div class="flex flex-wrap gap-2">
        <span
          v-for="tag in rule.tags"
          :key="tag"
          class="text-xs rounded-full border border-zinc-800 bg-zinc-900/30 px-2.5 py-1 text-zinc-300"
        >
          {{ tag }}
        </span>
      </div>

      <div v-if="rule.authors && rule.authors.length" class="text-sm text-zinc-400">
        Écrit par <span class="text-zinc-300">{{ rule.authors.join(', ') }}</span>
      </div>
    </header>

    <!-- Objectif -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Objectif</h2>
      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="o in rule.objectives" :key="o">{{ o }}</li>
      </ul>
    </section>

    <!-- Mise en œuvre -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Mise en œuvre</h2>

      <p v-if="rule.implementationIntro" class="mt-3 text-sm text-zinc-400">
        {{ rule.implementationIntro }}
      </p>

      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="x in rule.implementation" :key="x">{{ x }}</li>
      </ul>
    </section>

    <!-- Contrôle -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Contrôle</h2>
      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="c in rule.control" :key="c">{{ c }}</li>
      </ul>
    </section>

    <!-- Exemples -->
    <section class="space-y-4">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Exemples</h2>

      <div class="rounded-2xl border border-zinc-800 bg-zinc-900/30 overflow-hidden">
        <!-- Tabs -->
        <div class="flex border-b border-zinc-800" role="tablist" aria-label="Exemples">
          <button
            @click="activeTab = 'preview'"
            role="tab"
            id="tab-preview"
            :aria-selected="activeTab === 'preview'"
            aria-controls="panel-preview"
            :tabindex="activeTab === 'preview' ? 0 : -1"
            :class=" [
              'px-5 py-3 text-sm transition',
              activeTab === 'preview'
                ? 'text-zinc-100 border-b-2 border-zinc-100'
                : 'text-zinc-400 hover:text-zinc-200',
            ]"
          >
            Rendu
          </button>

          <button
            @click="activeTab = 'code'"
            role="tab"
            id="tab-code"
            :aria-selected="activeTab === 'code'"
            aria-controls="panel-code"
            :tabindex="activeTab === 'code' ? 0 : -1"
            :class=" [
              'px-5 py-3 text-sm transition',
              activeTab === 'code'
                ? 'text-zinc-100 border-b-2 border-zinc-100'
                : 'text-zinc-400 hover:text-zinc-200',
            ]"
          >
            Code
          </button>
        </div>

        <div class="p-6">
          <!-- RENDU -->
          <div
            v-if="activeTab === 'preview'"
            id="panel-preview"
            role="tabpanel"
            aria-labelledby="tab-preview"
            class="space-y-4"
          >
            <p class="text-sm text-zinc-400">
              Exemple : l’utilisateur peut relancer l’envoi du code 2FA (avec un délai pour limiter l’abus).
            </p>

            <div class="rounded-xl border border-zinc-800 bg-zinc-950 p-5 space-y-4">
              <div>
                <label for="otp" class="block text-sm font-medium text-zinc-200">Code de vérification</label>
                <input
                  id="otp"
                  v-model="code"
                  inputmode="numeric"
                  autocomplete="one-time-code"
                  class="mt-1 w-full rounded-lg border border-zinc-800 bg-zinc-950 px-3 py-2 text-sm text-zinc-100 focus:outline-none focus:ring-2 focus:ring-zinc-600"
                  placeholder="Ex. 123456"
                />
              </div>

              <div class="flex flex-wrap items-center gap-3">
                <button
                  type="button"
                  @click="resendCode"
                  :disabled="!canResend"
                  class="rounded-lg border border-zinc-800 bg-zinc-900/40 px-3 py-2 text-sm text-zinc-100 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-zinc-900/60 transition"
                >
                  <span v-if="canResend">Renvoyer le code</span>
                  <span v-else>Renvoyer le code ({{ cooldown }}s)</span>
                </button>

                <p v-if="status" class="text-sm text-zinc-400" aria-live="polite">
                  {{ status }}
                </p>
              </div>

              <p class="text-xs text-zinc-500">
                Important : en production, la relance doit être limitée (rate limiting) et proposer une alternative (changer de canal, support).
              </p>
            </div>
          </div>

          <!-- CODE -->
          <div v-else id="panel-code" role="tabpanel" aria-labelledby="tab-code">
            <pre class="rounded-xl bg-zinc-950 p-5 overflow-x-auto text-sm text-zinc-100"><code>
&lt;label for="otp"&gt;Code de vérification&lt;/label&gt;
&lt;input id="otp" autocomplete="one-time-code" inputmode="numeric" /&gt;

&lt;button type="button" disabled&gt;
  Renvoyer le code (20s)
&lt;/button&gt;

&lt;!-- Après le délai : bouton actif, on peut relancer la procédure --&gt;
&lt;button type="button"&gt;Renvoyer le code&lt;/button&gt;
</code></pre>
          </div>
        </div>
      </div>
    </section>
  </section>

  <!-- Fallback -->
  <section v-else class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
    <h1 class="text-lg font-semibold text-zinc-100">Règle introuvable</h1>
    <p class="mt-2 text-sm text-zinc-400">
      Impossible de trouver la règle <code class="text-zinc-300">{{ ruleId }}</code>.
      Vérifie que l’ID existe dans <code class="text-zinc-300">rules.json</code>.
    </p>
  </section>
</template>

<script setup lang="ts">
import { ref, computed, onBeforeUnmount } from 'vue'
import { useRouter } from 'vue-router'
import { getRuleById } from '@/app/data/rules'

const router = useRouter()

const ruleId = 96
const rule = computed(() => getRuleById(ruleId) ?? getRuleById(String(ruleId)))
const activeTab = ref<'preview' | 'code'>('preview')

// Exemple "relance 2FA"
const code = ref('')
const status = ref<string | null>(null)
const cooldown = ref(0)
let timer: ReturnType<typeof setInterval> | null = null

const canResend = computed(() => cooldown.value === 0)

function startCooldown(seconds = 20) {
  cooldown.value = seconds
  if (timer) clearInterval(timer)
  timer = setInterval(() => {
    cooldown.value = Math.max(0, cooldown.value - 1)
    if (cooldown.value === 0 && timer) {
      clearInterval(timer)
      timer = null
    }
  }, 1000)
}

function resendCode() {
  status.value = 'Un nouveau code vient d’être envoyé.'
  startCooldown(20)
}

onBeforeUnmount(() => {
  if (timer) clearInterval(timer)
})
</script>