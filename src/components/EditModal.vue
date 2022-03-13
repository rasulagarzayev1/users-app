<template>
<div
	class="account-users-edit-modal"
	:class="!modalCloseStatus ? 'account-users-edit-modal--active' : ''"
>
	<div>
		<p>Edit user modal</p>
		<input
			v-model="userModel.name"
			type="text"
			name="name"
			placeholder="Fullname"
		/>
		<input
			v-model="userModel.email"
			type="text"
			name="email"
			placeholder="Email"
		/>
		<select
			v-model="userModel.role"
			name="permission"
			id="permission"
		>
			<option
				v-for="permission in permissions"
				:key="permission.id"
				:value="permission.id"
			>
				{{ permission.value }}
			</option>
		</select>

		<button
			type="button"
			class="account-users-edit-modal__edit-btn"
			@click="onEditUser"
		>
			Edit
		</button>
		<button
			type="button"
			class="account-users-edit-modal__close-btn"
			@click="onCloseModal"
		>
			Close
		</button>
	</div>
</div>  
</template>

<script>

export default {
	name: 'EditModal',
	props: {
		editedUser: {
			default: {},
		},
		isModalOpen: {
			default: false,
		}
	},
	data() {
		return {
			userModel: {},
			modalCloseStatus: true,
			permissions: [
				{
					id: 'ACCOUNT_MANAGER',
					value: 'Account manager',
				},
				{
					id: 'ADMIN',
					value: 'Admin',
				},
				{
					id: 'AGENT',
					value: 'Agent',
				},
				{
					id: 'EXTERNAL_REVIEWER',
					value: 'External reviewer',
				},
			],
		}
	},
	watch: {
		'editedUser': function() {
			this.userModel = this.editedUser;
		},
		'isModalOpen': function() {
			this.modalCloseStatus = this.isModalOpen;
		},
	},
	methods: {
		onCloseModal() {
			this.modalCloseStatus = true;
			document.body.style.overflowY = 'auto';
		},
		onEditUser() {
			this.modalCloseStatus = true;
			this.$emit("onUserUpdated", this.userModel);
			document.body.style.overflowY = 'auto';
		},
	},
}
</script>
