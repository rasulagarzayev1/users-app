<template>
	<Header
		@user-serached="onUserSearched"
	/>
	<UserList
		:serached-word="serachedWord"
	/>
</template>

<script>
import Header from './components/Header.vue';
import UserList from './components/UserList.vue';

export default {
	name: 'App',
	components: {
		Header,
		UserList,
	},
	data() {
		return {
			serachedWord: '',
		};
	},
	methods: {
		onUserSearched(word) {
			this.serachedWord = word;
		},
	},
}
</script>
