<template>
	<div id="decisionDrivers" class="inputGroup">
		<TemplateHeader
			:infoText="'Decision Drivers are competing forces or facing concerns that influence the decision.'"
		>
			<h2>Decision Drivers</h2>
		</TemplateHeader>
		<draggable class="dragArea" :list="decisionDrivers" :sort="true" handle="#driversGrabber" @update="checkMove">
			<div v-for="(driver, index) in decisionDriversWithBlank" :key="index" class="multiInput">
				<i id="driversGrabber" class="codicon codicon-grabber" v-if="decisionDrivers[index] !== ''"></i>
				<input v-model="decisionDrivers[index]" @input="updateArray($event.target.value, index)" />
				<i
					id="multiInputDeleteIcon"
					class="codicon codicon-close"
					v-if="decisionDrivers[index] !== ''"
					@click="updateArray('', index)"
				></i>
			</div>
		</draggable>
	</div>
</template>

<script lang="ts">
	import { defineComponent, PropType } from "vue";
	import { VueDraggableNext } from "vue-draggable-next";
	import TemplateHeader from "./TemplateHeader.vue";

	export default defineComponent({
		name: "TemplateDecisionDriversSection",
		components: {
			TemplateHeader,
			draggable: VueDraggableNext,
		},
		props: {
			decisionDriversProp: {
				type: Array as PropType<string[]>,
				default: [],
			},
		},
		data() {
			return {
				decisionDrivers: this.decisionDriversProp,
			};
		},
		computed: {
			/**
			 * Computes a new decision drivers array with a blank entry at the end of the array such that
			 * a blank input field is rendered for the user to enter a new decision driver in.
			 */
			decisionDriversWithBlank() {
				const decisionDriversWithBlank = this.decisionDrivers;
				decisionDriversWithBlank.push("");
				return decisionDriversWithBlank;
			},
		},
		methods: {
			/**
			 * Prevents the user to drag an item below an empty input field that is reserved for new inputs.
			 * @param evt The event fired upon causing an update with a drag
			 */
			checkMove(evt: any) {
				if (this.decisionDrivers[evt.newIndex - 1] === "") {
					this.decisionDrivers[evt.newIndex - 1] = this.decisionDrivers[evt.newIndex];
					this.decisionDrivers.splice(evt.newIndex, 1);
					this.decisionDrivers = this.decisionDrivers.filter((driver) => driver !== "");
				}
			},
			/**
			 * Updates the list of decision drivers/links.
			 */
			updateArray(text: string, index: number) {
				this.decisionDrivers.splice(index, 1, text);
				this.decisionDrivers = this.decisionDrivers.filter((driver) => driver !== "");
				this.$emit("update:decisionDrivers", this.decisionDrivers);
			},
		},
	});
</script>

<style lang="scss" scoped>
	@use "../static/mixins.scss" as *;

	.inputGroup {
		margin-bottom: 1.5rem;

		& input {
			height: 3rem;
		}
	}

	.multiInput {
		@include centered-flex(row);
		justify-content: left;
		margin: 0.5rem 0;
	}

	.dragArea {
		display: flex;
		flex-direction: column;
		flex-wrap: wrap;
		width: 100%;
	}

	#multiInputDeleteIcon {
		transform: scale(1.5);
		margin-left: 0.5rem;

		&:hover {
			cursor: pointer;
		}
	}

	#driversGrabber {
		position: initial;
		margin-right: 0.5rem;
		transform: scale(1.2);

		&:hover {
			cursor: grab;
		}

		&:active {
			cursor: grabbing;
		}
	}
</style>