<template>
<div
	class="account-users-edit-modal"
	:class="!modalCloseStatus ? 'account-users-edit-modal--active' : ''"
>
	<div class="account-users-edit-modal__container">
		<h2>Edit user modal</h2>

		<div>
			<input
				v-model="userModel.name"
				type="text"
				name="name"
				placeholder="Fullname"
				@input="validateNameOnChanged"
			/>
			<p v-if="!isNameValid">Username must be filled!</p>
		</div>
		<div>
			<input
				v-model="userModel.email"
				type="text"
				name="email"
				placeholder="Email"
				@input="validateEmailOnChanged"
			/>
			<p
				v-if="!isEmailValid"
			>
				Email must be filled and must be in correct format!
			</p>
		</div>
		<div>
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
		</div>

		<button
			type="button"
			class="account-users-edit-modal__edit-btn"
			v-wave="vaweStyle"
			@click="onEditUser"
		>
			Edit
		</button>
		<button
			type="button"
			class="account-users-edit-modal__close-btn"
			v-wave="vaweStyle"
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
		},
	},
	inject: ['vaweStyle'],
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
			isNameValid: true,
			isEmailValid: true,
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
			if(!this.isNameValid || !this.isEmailValid) {
				return
			}

			this.modalCloseStatus = true;
			this.$emit("onUserUpdated", this.userModel);
			document.body.style.overflowY = 'auto';
		},
		validateNameOnChanged() {
			if(!this.userModel.name.trim()) {
				this.isNameValid = false;
				return
			}

			this.isNameValid = true;
			return
		},
		validateEmailOnChanged() {
			if(
				!this.userModel.email.trim() ||
				!(/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(this.userModel.email)) //eslint-disable-line
			) {
				this.isEmailValid = false;
				return
			}

			this.isEmailValid = true;
			return
		},
	},
}
</script>
