<template>
	<div id="container" :class="getStatusClass(adr.adr.status)">
		<div id="adr-box" :class="[adr.adr.conforming ? 'conforming' : 'not-conforming']">
			<div class="adr-info">
				<h3>{{ adr.adr.title ? getAdrNumber + ": " + adr.adr.title : "(No title)" }}</h3>
				<h5>
					<TT>{{ adr.relativePath }}</TT>
				</h5>
			</div>
			<div class="button-group">
				<button id="view" v-if="adr.adr.conforming" @click="$emit('requestView')">View</button>
				<button id="edit" v-else @click="$emit('requestEdit')">Edit</button>
				<button id="delete" @click="$emit('requestDelete')">Delete</button>
			</div>
		</div>
		<h4 v-if="!adr.adr.conforming" class="not-conforming-message" v-for="error in adr.adr.parseErrors">
			{{ "Parsing error at line " + error.line + ", position " + error.charPosition + ": " + error.message }}
		</h4>
	</div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
	name: "ADRContainer",
	props: {
		adr: {
			type: Object,
			required: true,
		},
	},
	computed: {
		/**
		 * Returns the number of the ADR.
		 */
		getAdrNumber() {
			return `#${this.adr.fileName.substring(0, 4)}`;
		}
	},
	methods: {
		getStatusClass(status: string) {
			let lowerCaseStatus = status.toLowerCase();
			switch (lowerCaseStatus) {
				case 'accepted':
					return 'status-accepted';
				case 'rejected':
					return 'status-rejected';
				case 'proposed':
					return 'status-proposed';
				case 'superseded':
					return 'status-superseded';
				default:
					return ''; // Default class if status is undefined or unknown
			}
		}
	}
});
</script>

<style lang="scss" scoped>
@use "../static/mixins.scss" as *;

#adr-box {
	@include centered-flex(row);
	width: 100%;
	justify-content: space-between;
	margin: 1rem 0;
	padding: 0 1rem;
}

.adr-info {
	& h3 {
		margin-top: 10px;
	}

	& h5 {
		margin-bottom: 10px;
	}
}

.conforming {
	border: 1.5px solid var(--vscode-descriptionForeground);
}

.not-conforming {
	border: 1.5px solid var(--vscode-editorError-foreground);
}

.not-conforming-message {
	color: var(--vscode-editorError-foreground);
	margin: -0.75rem 0 1rem 0;
}

.button-group {
	@include centered-flex(row);
}


.status-accepted {
	background-color: #4CAF50;
	color: white;
}

.status-rejected {
	background-color: #d32f2f;
	color: white;
}

.status-proposed {
	background-color: #FF8C00;
	color: white;
}

.status-superseded {
	background-color: #607D8B;
	color: white;
}

button {
	margin: 1rem;
	padding: 0.5rem 1rem;
	width: fit-content;
	min-width: 73px;
	@include button-styling;

	&#delete {
		background: var(--vscode-editorError-foreground);
	}

	&#view {
		background: var(--vscode-button-background);
	}

	&:disabled {
		filter: brightness(50%);
		cursor: default;
	}
}
</style>
