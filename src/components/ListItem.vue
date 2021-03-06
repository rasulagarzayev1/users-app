<template>
    <div
		class="account-users-list__list-item"
		:id="user.id"
		:class="isUserSelected || isAllUsersSelected ? 'account-users-list__list-item--selected' : ''"
		v-wave="vaweStyle"
		@click="onUserSlected"
	>
			<div class="user-info-part">
				<input
					type="checkbox"
					name="username"
					:checked="isAllUsersSelected || isUserSelected"
				>
				<img
					:src="user.avatar"
					alt="avatar"
					@error="onImageLoadError"
				/>
				<div>
					<p>{{ user.name}}</p>
					<a
						:href="`mailto: ${user.email}`"
					>
						{{ user.email }}
					</a>
				</div>
			</div>
			<div
				class="user-permission-part"
			>
				<div
					class="user-permission-part__permission-name"
					:class="`user-permission-part__permission-name--${user.role}`"
				>
					{{ developPermissionsForUI }}
				</div>
				<div
					class="user-permission-part__user-controls"
				>
					<button
						type="button"
						v-wave="vaweStyle"
						@click="onEditUserClicked"
					>
						<img :src="require('../assets/img/edit.svg')" />
						Edit
					</button>
					<button
						type="button"
						v-wave="vaweStyle"
						@click="onDeleteUserClicked"
					>
						<img :src="require('../assets/img/delete.svg')" />
					</button>
				</div>
			</div>
		</div>
</template>

<script>
export default {
	name: 'ListItem',
	props: {
		user: {
			default: {},
		},
		isAllUsersSelected: {
			type: Boolean,
			default: false,
		},
	},
	inject: ['vaweStyle'],
	data () {
		return {
			isUserSelected: false,
			selectedItemClassName: 'account-users-list__list-item--selected',
			isEditModalOpen: false,
			isImageLoaded: false,
		};
	},
	computed: {
		developPermissionsForUI: function() {
			switch (this.user.role) {
				case 'ACCOUNT_MANAGER':
					return 'Account manager';
				case 'ADMIN':
					return 'Admin';
				case 'AGENT':
					return 'Agent';
				case 'EXTERNAL_REVIEWER':
					return 'External reviewer';
				default:
					return this.user.role;
			}
		},
	},
	watch: {
		'isAllUsersSelected': function() {
			if(this.isAllUsersSelected) {
				this.isUserSelected = false;
			}
		},
	},
	methods: {
		onUserSlected (e) {
			const selectedItem = e.currentTarget;
			const checkBox = selectedItem.querySelector('input');

			if(e.target.type === 'button' || e.target.parentNode.type === 'button') {
				return
			}

			if(
				(!checkBox.checked && !(e.target.type === 'checkbox')) ||
				(e.target.type === 'checkbox' && e.target.checked)
			) {
				this.isUserSelected = true;
				this.$emit("handleUserSelection", this.isUserSelected, this.user.id);
				return
			}

			this.isUserSelected = false;
			this.$emit("handleUserSelection", this.isUserSelected, this.user.id);
		},
		onEditUserClicked() {
			document.body.style.overflowY = 'hidden';
			this.isEditModalOpen = !this.isEditModalOpen;
			this.$emit("onEditModalOpened", this.user, this.isEditModalOpen);
		},
		onDeleteUserClicked() {
			this.$emit("handleUserSelection", true, this.user.id);
			this.$emit("onUserDeleted");

			// Another just removing directly from HTML
			//const element = document.getElementById(`${this.user.id}`)
			//element.parentNode.remove(element)
		},
		onImageLoadError(e) {
			e.target.src = require('../assets/img/defaultAvatar.jpg');
		},
	},
}
</script>
