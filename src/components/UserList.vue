<template>
	<div
		class="account-users-list"
	>
		<div
			class="account-users-list__controls"
		>
			<p>{{ selectedUsersCount }} users selected</p>
			<button
				v-if="selectedUsersCount===1"
				v-wave="vaweStyle"
				@click="onUserEditClicked"
			>
				<img :src="require('../assets/img/edit.svg')" />
				Edit
			</button>
			<button
				v-if="selectedUsersCount!==0"
				v-wave="vaweStyle"
				@click="onUserDeleteClicked"
			>
				<img :src="require('../assets/img/delete.svg')" />
				Delete
			</button>
		</div>
		<div 
			class="account-users-list__list-headings"
		>
			<div
				class="account-users-list-headings__checkbox-part"
			>
				<input
					v-model="isAllUsersSelected"
					type="checkbox"
					name="allUsers"
					@click="onAllUsersSelected"
				/>
			</div>
			<div
				class="account-users-list-headings__text-part"
			>
				<p>
					Permissions
					<img
						:src="require('../assets/img/ordering.svg')"
						@click="onOrderingClicked"
					/>
				</p>
			</div>
		</div>
		<div
			v-for="user in loadedUsers"
			:key="user.id"
		>
			<list-item
				:user="user"
				:is-all-users-selected="isAllUsersSelected"
				@handle-user-selection="handleUserSelection"
				@on-user-deleted="onUserDeleteClicked"
				@on-edit-modal-opened="handleUserEdit"
			/>
		</div>
		<p
			v-if="users.length === 0"
			class="account-users-list__user-not-found"
		>
			No user found
		</p>
	</div>
	<edit-modal
		:edited-user="editedUser"
		:is-modal-open="isModalOpen"
		@on-user-updated="onUserUpdated"
	/>
</template>

<script>
import axios from 'axios';

import EditModal from './EditModal.vue';
import ListItem from './ListItem.vue';

export default {
  components: {
		ListItem,
		EditModal,
	},
	name: 'UserList',
	props: {
		serachedWord: {
			type: String,
			default: '',
		},
	},
	inject: ['vaweStyle'],
	data () {
		return {
			loadedUsers:[],
			users: [],			
			permissionsOrders: {
				ACCOUNT_MANAGER: 1,
				ADMIN: 2,
				AGENT: 3,
				EXTERNAL_REVIEWER: 4,
			},
			postPerLoad: 20,
			startPoint: 20,
			scrollCount: 1,
			isDescending: true,
			isAllUsersSelected: false,
			selectedUsersIds: [],
			selectedUsersCount: 0,
			editedUser: {},
			isModalOpen: false,
		};
	},
	async mounted () {
		await this.getAllUsers();
		this.orderUsersByPermissions();
		this.getNextUsers();
	},
	watch: {
		'serachedWord': function() {
			const originalList = [...this.users];		
			this.users = this.users.filter((user) => {
				return user.name.includes(this.serachedWord) || user.email.includes(this.serachedWord)
			});
			this.orderUsersByPermissions();
			this.getNextUsers();
			this.users = [...originalList];
		},
	},
	methods: {
		async getAllUsers() {
			try {
				const response = await axios.get('https://raw.githubusercontent.com/klausapp/frontend-engineer-test-task/master/users.json');
				this.users = response.data.users;
			} catch (err) {
				// Handle Error Here
				console.error(err);
			}
		},
		orderUsersByPermissions() {
			this.users.sort( (a, b) => {
				return this.permissionsOrders[a.role] - this.permissionsOrders[b.role];
			});
			this.getInitialUsersForUI();
		},
		getInitialUsersForUI() {
			this.loadedUsers = this.users.slice(0, this.startPoint);
		},
		getNextUsers() {
			window.onscroll = () => {
				let bottomOfWindow = document.documentElement.scrollTop + window.innerHeight === document.documentElement.offsetHeight;
				if (bottomOfWindow) {
					this.scrollCount++;
					this.loadedUsers = [...this.loadedUsers, ...this.users.slice(this.startPoint, this.postPerLoad*this.scrollCount)];
					this.startPoint = this.startPoint + this.postPerLoad;
				}
			}
		},
		onOrderingClicked(e) {
			this.users = this.users.reverse();
			this.getInitialUsersForUI();

			this.isDescending = !this.isDescending;

			if(this.isDescending) {
				e.target.style.transform = 'rotate(0deg)';
				return
			}

			e.target.style.transform = 'rotate(180deg)';
		},
		onAllUsersSelected(e) {
			if(e.target.checked) {
				this.isAllUsersSelected = true;
				this.selectedUsersCount = this.users.length;
				return
			}

			this.selectedUsersCount = 0;
			this.isAllUsersSelected = false;
		},
		onUserDeleteClicked() {
			if (this.isAllUsersSelected) {
				this.users = [];
				this.isAllUsersSelected = false;
				this.selectedUsersCount = 0;
			} else {
				this.users = this.users.filter((user) => {
					return !this.selectedUsersIds.includes(user.id)
				});

				this.selectedUsersIds = [];
				this.selectedUsersCount = 0;
			}

			this.orderUsersByPermissions();
			this.getNextUsers();
		},
		handleUserSelection(isSelected, userId) {
			if(isSelected) {
				this.selectedUsersCount++;
				this.selectedUsersIds.push(userId);
				return
			}

			if(this.selectedUsersCount > 0) {
				this.selectedUsersCount--;
				this.selectedUsersIds = this.selectedUsersIds.filter(id => id !== userId);
			}
		},
		handleUserEdit(user, isOpen) {
			this.editedUser = user;
			this.isModalOpen = isOpen;
		},
		onUserUpdated(userModel) {
			this.loadedUsers = this.loadedUsers.map((user) => {
				if (user.id === userModel.id) {
					return Object.assign({}, userModel)
				}
				return user;
			})
		},
		onUserEditClicked() {
			this.editedUser = this.loadedUsers.find(user => user.id === this.selectedUsersIds[0]);
			this.isModalOpen = !this.isModalOpen;
		},
	},
}
</script>
