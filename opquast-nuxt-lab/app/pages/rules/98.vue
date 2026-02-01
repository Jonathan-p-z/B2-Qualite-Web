<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { getRuleById } from '../../data/rules'

const router = useRouter()

const ruleId = 98
const rule = computed(() => getRuleById(ruleId) ?? getRuleById(String(ruleId)))
const activeTab = ref<'preview' | 'code'>('preview')

// Exemple
const email = ref('')
const accepted = ref(false)

const isValid = computed(() => {
  const okEmail = /^\S+@\S+\.\S+$/.test(email.value)
  return okEmail && accepted.value
})

function onSubmit(e: Event) {
  if (!isValid.value) {
    e.preventDefault()
    e.stopPropagation()
  }
}
</script>

<template>
  <section v-if="rule" class="space-y-6">
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

    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Objectif</h2>
      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="o in rule.objectives" :key="o">{{ o }}</li>
      </ul>
    </section>

    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Mise en œuvre</h2>

      <p v-if="rule.implementationIntro" class="mt-3 text-sm text-zinc-400">
        {{ rule.implementationIntro }}
      </p>

      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="x in rule.implementation" :key="x">{{ x }}</li>
      </ul>
    </section>

    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Contrôle</h2>
      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="c in rule.control" :key="c">{{ c }}</li>
      </ul>
    </section>

    <section class="space-y-4">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">Exemples</h2>

      <div class="rounded-2xl border border-zinc-800 bg-zinc-900/30 overflow-hidden">
        <div class="flex border-b border-zinc-800" role="tablist" aria-label="Exemples">
          <button
            @click="activeTab = 'preview'"
            role="tab"
            id="tab-preview"
            :aria-selected="activeTab === 'preview'"
            aria-controls="panel-preview"
            :tabindex="activeTab === 'preview' ? 0 : -1"
            :class="[
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
            :class="[
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
          <div
            v-if="activeTab === 'preview'"
            id="panel-preview"
            role="tabpanel"
            aria-labelledby="tab-preview"
            class="space-y-4"
          >
            <p class="text-sm text-zinc-400">
              Exemple : bouton “désactivé” visuellement, mais annoncé (via <code class="text-zinc-300">aria-disabled</code>) et expliqué.
            </p>

            <form class="rounded-xl border border-zinc-800 bg-zinc-950 p-5 space-y-4" @submit="onSubmit">
              <div>
                <label for="email" class="block text-sm font-medium text-zinc-200">Email</label>
                <input
                  id="email"
                  v-model="email"
                  type="email"
                  autocomplete="email"
                  inputmode="email"
                  class="mt-1 w-full rounded-lg border border-zinc-800 bg-zinc-950 px-3 py-2 text-sm text-zinc-100 focus:outline-none focus:ring-2 focus:ring-zinc-600"
                  placeholder="nom@domaine.fr"
                />
              </div>

              <div class="flex items-start gap-2">
                <input
                  id="cgu"
                  v-model="accepted"
                  type="checkbox"
                  class="mt-1"
                />
                <label for="cgu" class="text-sm text-zinc-200">
                  J’accepte les conditions
                </label>
              </div>

              <p id="submitHelp" class="text-xs text-zinc-500">
                Pour activer “Valider”, renseigne un email valide et coche la case.
              </p>

              <button
                type="submit"
                :aria-disabled="!isValid"
                aria-describedby="submitHelp"
                :class="[
                  'rounded-lg border border-zinc-800 px-3 py-2 text-sm transition',
                  isValid
                    ? 'bg-zinc-900/60 text-zinc-100 hover:bg-zinc-900/80'
                    : 'bg-zinc-900/20 text-zinc-400 cursor-not-allowed',
                ]"
                @click="(e) => { if (!isValid) { e.preventDefault(); e.stopPropagation() } }"
              >
                Valider
              </button>

              <p class="text-xs text-zinc-500">
                Note : ici on évite l’attribut <code class="text-zinc-300">disabled</code> pour que le bouton reste atteignable/annoncé, et on bloque l’action côté JS.
              </p>
            </form>

            <!-- Screenshot rendu comme dans la page 100 -->
            <div class="mt-6">
              <a
                href="/screenshots/rule-98/screenshot-1.png"
                target="_blank"
                rel="noreferrer"
                class="block cursor-zoom-in"
              >
                <img
                  src="/screenshots/rule-98/screenshot-1.png"
                  alt="Exemple d’application de la règle 98"
                  class="h-full w-full object-cover"
                  onerror="
                    this.style.display = 'none'
                    this.nextElementSibling.style.display = 'block'
                  "
                />
              </a>
              <div class="hidden text-center px-4">
                <div class="text-sm text-zinc-300 font-medium">Screenshot à ajouter</div>
                <div class="mt-1 text-xs text-zinc-500">Exemple réel attendu</div>
              </div>
            </div>
          </div>

          <div v-else id="panel-code" role="tabpanel" aria-labelledby="tab-code">
            <pre class="rounded-xl bg-zinc-950 p-5 overflow-x-auto text-sm text-zinc-100"><code>
&lt;p id="submitHelp"&gt;
  Pour activer “Valider”, renseigne un email valide et coche la case.
&lt;/p&gt;

&lt;button
  type="submit"
  aria-disabled="true"
  aria-describedby="submitHelp"
&gt;
  Valider
&lt;/button&gt;

&lt;!-- Le bouton reste présent et annoncé ; l’action est bloquée tant que ce n’est pas valide. --&gt;
</code></pre>
          </div>
        </div>
      </div>
    </section>
  </section>

  <section v-else class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
    <h1 class="text-lg font-semibold text-zinc-100">Règle introuvable</h1>
    <p class="mt-2 text-sm text-zinc-400">
      Impossible de trouver la règle <code class="text-zinc-300">{{ ruleId }}</code>.
      Vérifie que l’ID existe dans <code class="text-zinc-300">rules.json</code>.
    </p>
  </section>
</template>
