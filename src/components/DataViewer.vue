<template>
	<div id="dataViewer">
		<span v-if="isText()">{{ nl2br(content) }}</span>
		<ObjectTree v-else :data="content"></ObjectTree>
	</div>
</template>

<script>
import EventBus from '../eventbus.js';
import ObjectTree from '@openeo/vue-components/components/ObjectTree.vue';

export default {
	name: 'DataViewer',
	components:  {
		ObjectTree
	},
	data() {
		return {
			content: null,
		};
	},
	mounted() {
		this.reset();
	},
	methods: {

		isText() {
			return (typeof this.content === 'string');
		},

		reset() {
			this.content = 'Nothing to show.';
		},

		showJson(data) {
			this.content = data;
			EventBus.$emit('showDataViewer');
		},

		showText(data) {
			this.content = data;
			EventBus.$emit('showDataViewer');
		},

		showBlob(blob, mimeType = null) {
			if (mimeType == null) {
				mimeType = blob.type;
			}
			switch(mimeType) {
				case 'application/json':
					this.$utils.blobToText(blob, (event) => {
						var json = JSON.parse(event.target.result);
						this.showJson(json);
					});
					break;
				case 'text/plain':
					this.$utils.blobToText(blob, (event) => {
						this.showText(event.target.result);
					});
					break;
			}
		},

		nl2br (str) {
			if (typeof str === 'undefined' || str === null) {
				return '';
			}
			return (str + '').replace(/([^>\r\n]?)(\r\n|\n\r|\r|\n)/g, '$1<br>$2');
		}

	}
};
</script>

<style scoped>
#dataViewer {
	padding: 5px;
}
</style>