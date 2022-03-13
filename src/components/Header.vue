<template>
	<div class="account-users-header">
		<div class="account-users-header__title-part">
			<p>Account users</p>
		</div>
		<div class="account-users-header__search-part">
			<label for="search">
				<input
					v-model="searchedWord"
					id="search"
					name="search"
					type="text"
					placeholder="Search"
				/>
			</label>
			<button
				type="button"
				@click="onUserSearched"
			>
				Connect users
			</button>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Header',
	data() {
		return {
			searchedWord: '',
		}
	},
	methods: {
		onUserSearched() {
			this.$emit("userSerached", this.searchedWord);
		},
	},
}
</script>
