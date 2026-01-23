<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { getRuleById } from '../../data/rules'

const router = useRouter()

const ruleId = 94
const rule = computed(() => getRuleById(ruleId) ?? getRuleById(String(ruleId)))
const activeTab = ref('preview')
</script>

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

      <div
        v-if="rule.authors && rule.authors.length"
        class="text-sm text-zinc-400"
      >
        Écrit par <span class="text-zinc-300">{{ rule.authors.join(', ') }}</span>
      </div>
    </header>

    <!-- Objectif -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">
        Objectif
      </h2>

      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="o in rule.objectives" :key="o">{{ o }}</li>
      </ul>
    </section>

    <!-- Mise en œuvre -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">
        Mise en œuvre
      </h2>

      <p v-if="rule.implementationIntro" class="mt-3 text-sm text-zinc-400">
        {{ rule.implementationIntro }}
      </p>

      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="x in rule.implementation" :key="x">{{ x }}</li>
      </ul>
    </section>

    <!-- Contrôle -->
    <section class="rounded-2xl border border-zinc-800 bg-zinc-900/30 p-6">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">
        Contrôle
      </h2>

      <ul class="mt-3 list-disc pl-5 space-y-2 text-sm text-zinc-300">
        <li v-for="c in rule.control" :key="c">{{ c }}</li>
      </ul>
    </section>

    <!-- Exemples -->
    <section class="space-y-4">
      <h2 class="text-lg font-semibold tracking-tight text-zinc-100">
        Exemples
      </h2>

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

        <!-- Content -->
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
              Exemple de liste d’options classées par ordre alphabétique
            </p>

            <div class="rounded-xl border border-zinc-800 bg-zinc-950 p-5 space-y-2">
              <label
                for="country"
                class="block text-sm font-medium text-zinc-200"
              >
                Pays
              </label>

              <select
                id="country"
                class="w-full rounded-lg border border-zinc-800 bg-zinc-950 px-3 py-2 text-sm text-zinc-100 focus:outline-none focus:ring-2 focus:ring-zinc-600"
              >
                <option value="">Sélectionnez un pays</option>
                <option value="de">Allemagne</option>
                <option value="be">Belgique</option>
                <option value="es">Espagne</option>
                <option value="fr">France</option>
                <option value="it">Italie</option>
                <option value="pt">Portugal</option>
              </select>
            </div>
          </div>

          <!-- CODE -->
          <div
            v-else
            id="panel-code"
            role="tabpanel"
            aria-labelledby="tab-code"
          >
            <pre class="rounded-xl bg-zinc-950 p-5 overflow-x-auto text-sm text-zinc-100">
<code>
&lt;label for="country"&gt;Pays&lt;/label&gt;

&lt;select id="country"&gt;
  &lt;option value=""&gt;Sélectionnez un pays&lt;/option&gt;
  &lt;option value="de"&gt;Allemagne&lt;/option&gt;
  &lt;option value="be"&gt;Belgique&lt;/option&gt;
  &lt;option value="es"&gt;Espagne&lt;/option&gt;
  &lt;option value="fr"&gt;France&lt;/option&gt;
  &lt;option value="it"&gt;Italie&lt;/option&gt;
  &lt;option value="pt"&gt;Portugal&lt;/option&gt;
&lt;/select&gt;
</code>
</pre>

            <p class="mt-3 text-xs text-zinc-500">
              Les options sont classées selon un ordre identifiable (ici alphabétique),
              facilitant la recherche et réduisant l’effort cognitif.
            </p>
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
      Vérifie que l’ID existe dans <code class="text-zinc-300">rules.json</code> et qu’il a le bon type (string vs number).
    </p>
  </section>
</template>

<style scoped>
.scrollbar-light {
  scrollbar-color: transparent transparent;
}
.scrollbar-light:hover {
  scrollbar-color: #a3a3a3 transparent;
}
</style>
