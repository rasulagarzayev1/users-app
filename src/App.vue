<template>
	<page-header
		@user-serached="onUserSearched"
	/>
	<user-list
		:serached-word="serachedWord"
	/>
</template>

<script>
import PageHeader from './components/PageHeader.vue';
import UserList from './components/UserList.vue';

export default {
	name: 'App',
	components: {
		PageHeader,
		UserList,
	},
	data() {
		return {
			serachedWord: '',
		};
	},
	provide() {
		return {
			vaweStyle: {
				color: 'black',
				startingOpacity: 0.3,
				easing: 'ease-in',
			},
		}
	},
	methods: {
		onUserSearched(word) {
			this.serachedWord = word;
		},
	},
}
</script>
