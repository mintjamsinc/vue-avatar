<!-- Copyright (c) 2021 MintJams Inc. Licensed under MIT License. -->

<template>
	<div class="avatar">
		<div v-if="hasImage" class="content content-image" v-lazy:background-image="imageURL"></div>
		<div v-if="!hasImage" class="content content-icon" :class="colorClasses"><i :class="iconClasses"></i></div>
		<div style="position: absolute; top: 0; bottom: 0; left: 0; right: 0; z-index: 1;">
			<slot></slot>
		</div>
	</div>
</template>

<script>
import Identicon from 'identicon.js';
import sha256 from 'crypto-js/sha256';
import {lazy} from '@mintjamsinc/vue-lazy';
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
		'memo': {
			'type': Object,
		},
		'icon': {
			'type': String,
		},
		'identifier': {
			'type': String,
		},
	},
	directives: {
		lazy,
	},
	data() {
		return {
			'ui': null
		};
	},
	computed: {
		authorizableInstance() {
			let vm = this;
			if (vm.authorizable) {
				if (vm.authorizable.$data) {
					return vm.authorizable;
				}
				return window.Webtop.userClient.newAuthorizable(vm.authorizable);
			}
			return undefined;
		},
		itemInstance() {
			let vm = this;
			if (vm.item) {
				if (vm.item.$data) {
					return vm.item;
				}
				return window.Webtop.cmsClient.newItem(vm.item);
			}
			return undefined;
		},
		hasImage() {
			let vm = this;

			if (vm.authorizable) {
				if (!vm.authorizableInstance.isGroup) {
					return true;
				}
			}

			if (vm.item) {
				if (!vm.itemInstance.isCollection && vm.itemInstance.hasThumbnail) {
					return true;
				}
			}

			if (vm.identifier) {
				return true;
			}

			return false;
		},
		imageURL() {
			let vm = this;

			if (vm.authorizable) {
				let photoURL = vm.authorizableInstance.photoURL;
				if (photoURL) {
					return photoURL;
				}

				let hash = sha256(vm.authorizableInstance.id).toString();
				let icon = new Identicon(hash, {
					background: [222, 226, 230, 255],
					margin: 0.2,
					format: 'svg',
				}).toString();
				return 'data:image/svg+xml;base64,' + icon;
			}

			if (vm.item) {
				return vm.itemInstance.thumbnailURL;
			}

			if (vm.identifier) {
				let hash = sha256(vm.identifier).toString();
				let icon = new Identicon(hash, {
					background: [222, 226, 230, 255],
					margin: 0.2,
					format: 'svg',
				}).toString();
				return 'data:image/svg+xml;base64,' + icon;
			}

			return '';
		},
		iconClasses() {
			let vm = this;

			if (vm.authorizable) {
				return 'fas fa-user-friends';
			}

			if (vm.item) {
				return Files.iconClasses(vm.itemInstance);
			}

			if (vm.task) {
				return 'far fa-sticky-note';
			}

			if (vm.memo) {
				return 'far fa-sticky-note';
			}

			if (vm.icon) {
				return vm.icon;
			}

			return '';
		},
		colorClasses() {
			let vm = this;

			if (vm.authorizable) {
				return '';
			}

			if (vm.item) {
				return Files.colorClasses(vm.itemInstance);
			}

			return '';
		},
	},
}
</script>
