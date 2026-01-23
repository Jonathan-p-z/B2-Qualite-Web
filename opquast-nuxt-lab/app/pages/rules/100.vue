<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { getRuleById } from '../../data/rules'

const router = useRouter()

const ruleId = 100
const rule = computed(() => getRuleById(ruleId) ?? getRuleById(String(ruleId)))
const activeTab = ref<'preview' | 'code'>('preview')
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

		<!-- Screenshots -->
		<section class="space-y-4">
			<h2 class="text-lg font-semibold tracking-tight text-zinc-100">Screenshots</h2>

			<div
				v-if="rule.screenshotsSources && rule.screenshotsSources.length"
				class="flex gap-4 overflow-x-auto pb-4 scrollbar-light"
			>
				<div
					v-for="(source, index) in rule.screenshotsSources"
					:key="source + index"
					class="shrink-0 w-[280px] sm:w-[340px]"
				>
					<div
						class="aspect-[16/10] rounded-2xl border border-zinc-800 bg-zinc-900/20 overflow-hidden flex items-center justify-center"
					>
						<a
							:href="`/screenshots/rule-${rule.id}/screenshot-${index + 1}.png`"
							target="_blank"
							rel="noreferrer"
							class="block cursor-zoom-in"
						>
							<img
								:src="`/screenshots/rule-${rule.id}/screenshot-${index + 1}.png`"
								:alt="`Exemple d’application de la règle ${rule.id}`"
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

					<div class="mt-2 text-xs text-zinc-500">
						Source :
						<a
							:href="source"
							target="_blank"
							rel="noreferrer"
							class="underline underline-offset-4 hover:text-zinc-300"
						>
							{{ source }}
						</a>
					</div>
				</div>
			</div>

			<p v-else class="text-sm text-zinc-400">
				Aucune source de screenshot n’est définie pour cette règle.
			</p>
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
							Exemple : contenus potentiellement sensibles, public visé indiqué dès la page d’accueil.
						</p>

						<section class="rounded-xl border border-zinc-800 bg-zinc-950 p-5 space-y-4">
							<header class="space-y-2">
								<p
									class="inline-flex items-center gap-2 rounded-md border border-amber-900/60 bg-amber-900/15 px-3 py-2 text-sm text-amber-200"
									role="note"
									aria-label="Avertissement"
								>
									<span class="font-medium">Avertissement :</span>
									Contenu réservé à un public adulte (18+). Images explicites.
								</p>

								<h3 class="text-lg font-semibold text-zinc-100">
									Galerie artistique — nus (18+)
								</h3>

								<p class="text-sm text-zinc-300">
									Ce site présente des photographies de nus. Si vous cherchez un portfolio “tout public”,
									consultez plutôt la section <a href="#" class="underline" @click.prevent>Portraits</a>.
								</p>
							</header>

							<div class="grid gap-3 sm:grid-cols-2">
								<article class="rounded-lg border border-zinc-800 bg-zinc-900/20 p-4">
									<h4 class="text-sm font-medium text-zinc-100">Ce que vous trouverez</h4>
									<ul class="mt-2 list-disc pl-5 text-sm text-zinc-400 space-y-1">
										<li>Albums classés par séries</li>
										<li>Conditions de consultation (18+)</li>
										<li>Mentions légales et droits d’auteur</li>
									</ul>
								</article>

								<article class="rounded-lg border border-zinc-800 bg-zinc-900/20 p-4">
									<h4 class="text-sm font-medium text-zinc-100">Accès rapides</h4>
									<nav class="mt-2 flex flex-wrap gap-2" aria-label="Accès rapides">
										<a
											href="#"
											@click.prevent
											class="inline-flex items-center rounded-lg border border-zinc-800 bg-zinc-900/60 px-3 py-2 text-sm text-zinc-100 hover:bg-zinc-900/80 transition"
										>
											Entrer (18+)
										</a>
										<a
											href="#"
											@click.prevent
											class="inline-flex items-center rounded-lg border border-zinc-800 bg-zinc-900/20 px-3 py-2 text-sm text-zinc-200 hover:bg-zinc-900/40 transition"
										>
											Accéder à Portraits (tout public)
										</a>
									</nav>
								</article>
							</div>

							<p class="text-xs text-zinc-500">
								Le public visé est indiqué dès l’accueil, pour éviter une exposition involontaire à des contenus sensibles.
							</p>
						</section>
					</div>

					<div v-else id="panel-code" role="tabpanel" aria-labelledby="tab-code">
						<pre class="rounded-xl bg-zinc-950 p-5 overflow-x-auto text-sm text-zinc-100"><code>
&lt;p role="note" aria-label="Avertissement"&gt;
	&lt;strong&gt;Avertissement :&lt;/strong&gt;
	Contenu réservé à un public adulte (18+). Images explicites.
&lt;/p&gt;

&lt;h1&gt;Galerie artistique — nus (18+)&lt;/h1&gt;

&lt;p&gt;
	Ce site présente des photographies de nus.
	Pour un contenu tout public, consulter la section “Portraits”.
&lt;/p&gt;

&lt;nav aria-label="Accès rapides"&gt;
	&lt;a href="/entree"&gt;Entrer (18+)&lt;/a&gt;
	&lt;a href="/portraits"&gt;Accéder à Portraits (tout public)&lt;/a&gt;
&lt;/nav&gt;
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

<style scoped>
.scrollbar-light {
	scrollbar-color: transparent transparent;
}
.scrollbar-light:hover {
	scrollbar-color: #a3a3a3 transparent;
}
</style>
