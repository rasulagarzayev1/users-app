<template>
    <div
		class="account-users-list__list-item"
		@click="itemSelectionHandler"
	>
			<div class="user-info-part">
				<input
					type="checkbox"
					name="username"
				>
				<img :src="user.avatar" />
				<div>
					<p>{{ user.name}}</p>
					<a :href="`mailto: ${user.email}`">
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
					{{ developPermissionsForUI(user.role) }}
				</div>
				<div
					class="user-permission-part__user-controls"
				>
					<button>
						<img src="../assets/img/edit.svg" />
						Edit
					</button>
					<button>
						<img src="../assets/img/delete.svg" />
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
		}
	},
	methods: {
		itemSelectionHandler (e) {
			const selectedItem = e.target.parentNode;
			const checkBox = selectedItem.querySelector('input');
			if (!checkBox.checked) {
				selectedItem.classList.add('account-users-list__list-item--selected');
				selectedItem.querySelector('input').checked = true;
				return
			}
			selectedItem.classList.remove('account-users-list__list-item--selected');
			checkBox.checked = false;
		},
		developPermissionsForUI(permission) {
			switch (permission) {
				case 'ACCOUNT_MANAGER':
					return 'Account manager';
				case 'ADMIN':
					return 'Admin';
				case 'AGENT':
					return 'Agent';
				case 'EXTERNAL_REVIEWER':
					return 'External reviewer';
				default:
					return permission;
			}
		},
	}
}
</script>