<template>
	<div class="pilot-modal">
		<div class="pilot-header-container">
			<div class="section-header clipped-medium-backward-bio">
				<img src="/icons/pilot.svg" />
				<h1>{{ pilot.name }} [{{ pilot.callsign }}]</h1>
			</div>
			<div class="rhombus-back">&nbsp;</div>
		</div>
		<div class="pilot">
			<div class="pilot-gear">
				<div class="gear-grid">
					<div v-for="item in gearItems" class="pilot-gear-item">
						<h1>{{ item.label }}</h1>
						<h2>{{ item.name }}</h2>
					</div>
				</div>
			</div>
			<div class="pilot-notes markdown">
				<div v-html="getHistory()"/>
			</div>
		</div>
	</div>
	<div class="pilot-modal portrait">
		<div class="pilot-header-container">
			<div class="section-header clipped-medium-backward-pilot">
				<img src="/icons/portrait.svg" />
				<h1>Pilot Artwork</h1>
			</div>
			<div class="rhombus-back">&nbsp;</div>
		</div>
		<div class="pilot">
			<img :src="pilotPortrait" class="portrait" />
		</div>
	</div>
</template>

<script>
import { VueMarkdownIt } from '@f3ve/vue-markdown-it';

export default {
	components: {
		VueMarkdownIt,
	},
	inheritAttrs: false,
	props: {
		pilot: {
			type: Object,
			required: true,
		},
		pilotGear: {
			type: Array,
			default: () => [],
		},
	},
	data() {
		return {
			markdownHtml: true,
		};
	},
	computed: {
		gearItems() {
			const loadout = this.pilot.loadout || {}
			const groups = [
				['Armor', loadout.armor],
				['Weapon', loadout.weapons],
				['Weapon', loadout.extendedWeapons],
				['Gear', loadout.gear],
				['Gear', loadout.extendedGear],
			]
			return groups.flatMap(([label, items]) =>
				(items || []).filter(Boolean).map((item) => {
					const gear = this.pilotGear.find((obj) => obj.id === item.id)
					return { label, name: gear?.name || 'ERR: DATA NOT FOUND' }
				})
			)
		},
    pilotPortrait() {
      if (this.pilot.portrait) return this.pilot.portrait
      return `/pilots/${this.pilot.callsign.toUpperCase()}.webp`
    },
    mechPortrait() {
      return `/mechs/${this.pilot.callsign}.webp`
    },
	},
	methods: {
		getHistory(){
			if (this.pilot.history === ""){
				return `<p> <h2> [ERR: REDACTED] </h2> </p>`
			}

			let response = "<p>"
			
			if (this.pilot.text_appearance !== ""){
				response += `<h2>APPEARANCE</h2> ${this.pilot.text_appearance} </hr>`;
			}

			if (this.pilot.history !== ""){
				response += `<h2>HISTORY</h2> ${this.pilot.history} </hr>`;
			}

			response += "</p>"

			return response;
		}
	},
};
</script>