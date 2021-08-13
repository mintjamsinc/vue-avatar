<!-- Copyright (c) 2021 MintJams Inc. Licensed under MIT License. -->

<template>
	<div class="avatar" :class="{'border border-black-50 text-black-50': (!!task && !hasImage)}">
		<div v-if="hasImage" class="content content-image" v-lazy:background-image="imageURL"></div>
		<div v-if="!hasImage" class="content content-icon" :class="colorClasses"><i :class="iconClasses"></i></div>
		<div style="position: absolute; top: 0; bottom: 0; left: 0; right: 0; z-index: 1;">
			<slot></slot>
		</div>
	</div>
</template>

<script>
import Identicon from 'identicon.js';
import XXH from 'xxhashjs';
import commons from '@mintjamsinc/webtop-app-commons';
const {Files} = commons;

export default {
	props: {
		'authorizable': {
			'type': Object,
		},
		'item': {
			'type': Object,
		},
		'task': {
			'type': Object,
		},
	},
	data() {
		return {
			'ui': null
		};
	},
	created() {
		let vm = this;
		class UI {
		}
		vm.ui = new UI();
	},
	computed: {
		hasImage() {
			let vm = this;

			if (vm.authorizable) {
				if (!vm.authorizable.isGroup) {
					return true;
				}
			}

			if (vm.item) {
				if (!vm.item.isCollection && vm.item.hasThumbnail) {
					return true;
				}
			}

			return false;
		},
		imageURL() {
			let vm = this;

			if (vm.authorizable) {
				let photoURL = vm.authorizable.photoURL;
				if (photoURL) {
					return photoURL;
				}

				let hash = XXH.h64(vm.authorizable.id, 1).toString(16);
				let icon = new Identicon(hash, {
					background: [222, 226, 230, 255],
					margin: 0.2,
					format: 'svg',
				}).toString();
				return 'data:image/svg+xml;base64,' + icon;
			}

			if (vm.item) {
				return vm.item.thumbnailURL;
			}

			return '';
		},
		iconClasses() {
			let vm = this;

			if (vm.authorizable) {
				return 'fas fa-user-friends';
			}

			if (vm.item) {
				return Files.iconClasses(vm.item);
			}

			if (vm.task) {
				return 'far fa-sticky-note';
			}

			return '';
		},
		colorClasses() {
			let vm = this;

			if (vm.authorizable) {
				return 'fas fa-user-friends';
			}

			if (vm.item) {
				return Files.colorClasses(vm.item);
			}

			if (vm.task) {
				return 'far fa-sticky-note';
			}

			return '';
		},
	},
}
</script>
