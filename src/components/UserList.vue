<template>
	<div
		class="account-users-list"
	>
		<div
			class="account-users-list__controls"
		>
			<p>2 users selected</p>
			<button>
				<img src="../assets/img/edit.svg" />
				Edit
			</button>
			<button>
				<img src="../assets/img/delete.svg" />
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
					type="checkbox"
					name="all"
				/>
			</div>
			<div
				class="account-users-list-headings__text-part"
			>
				<p>
					Permissions
					<img src="../assets/img/ordering.svg" />
				</p>
			</div>
		</div>
		<div v-for="user in loadedUsers" :key="user.id">
			<ListItem
				:user="user"
			/>
		</div>
	</div>
</template>

<script>
import ListItem from './ListItem.vue';
import axios from 'axios';
export default {
  components: {
		ListItem,
	},
	name: 'UserList',
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
			postPerLoad: 40,
			startPoint: 40,
			counter: 1,
		};
	},
	async mounted () {
		await this.getAllUsers();
		this.orderUsersByPermissions();
		this.getNextUsers();
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
			this.loadedUsers = this.users.slice(0, this.startPoint);
		},
		getNextUsers() {
			window.onscroll = () => {
				let bottomOfWindow = document.documentElement.scrollTop + window.innerHeight === document.documentElement.offsetHeight;
				if (bottomOfWindow) {
					this.counter++;
					this.loadedUsers = [...this.loadedUsers, ...this.users.slice(this.startPoint, this.postPerLoad*this.counter)];
					this.startPoint = this.startPoint + 40;
				}
			}
		},
	}

}
</script>
