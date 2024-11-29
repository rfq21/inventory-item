<template>
  <div class="user-list">
    <div class="header">
      <h2>Daftar Pengguna</h2>
      <button class="add-btn" @click="$emit('add-user')">
        Tambah Pengguna
      </button>
    </div>
    <div class="table-responsive">
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Username</th>
            <th>Email</th>
            <th>Role</th>
            <th class="action-column">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id">
            <td>{{ user.id }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.email }}</td>
            <td>{{ user.role }}</td>
            <td>
              <button class="edit-btn" @click="$emit('edit-user', user)">
                Edit
              </button>

              <button class="edit-btn" @click="deleteUser(user.id)">
                Hapus
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { computed, onMounted } from "vue";

import { useUserStore } from "../../../stores/userStore ";
import { useAuthStore } from "@/stores/authStore";

import UserCard from "@/components/admin/user/UserCard.vue";

import Modal from "@/components/Modal.vue";

import UserForm from "@/components/admin/user/UserForm.vue";

import eventBus from "@/utils/EventBus";

export default {
  name: "users",

  components: {
    UserCard,

    Modal,

    UserForm,
  },

  setup() {
    const userStore = useUserStore();

    const authStore = useAuthStore();

    const users = computed(() => userStore.users);

    onMounted(() => {
      if (authStore.token) {
        userStore.fetchUsers();
      } else {
        console.error("User is not authenticated");
      }
    });

    return {
      users,

      userStore,

      addUser: userStore.addUser,

      updateUser: userStore.updateUser,

      deleteUser: userStore.deleteUser,
    };
  },

  data() {
    return {
      showForm: false,

      selectedUser: null,

      isEdit: false,

      searchQuery: "",
    };
  },

  computed: {
    filteredUsers() {
      return this.users.filter((user) =>
        user.username.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },

  methods: {
    showAddForm() {
      this.selectedUser = { id: "", username: "", email: "", role: "USER" };

      this.isEdit = false;

      this.showForm = true;
    },

    editUser(user) {
      this.selectedUser = { ...user };

      this.isEdit = true;

      this.showForm = true;
    },

    async handleSubmit(user) {
      if (this.isEdit) {
        await this.updateUser(user);
      } else {
        await this.addUser(user);
      }

      await this.userStore.fetchUsers(); // Fetch latest users

      this.showForm = false;
    },

    cancelEditForm() {
      this.showForm = false;
    },

    async handleDeleteUser(id) {
      await this.deleteUser(id);

      await this.userStore.fetchUsers(); // Fetch latest users
    },

    handleSearch(query) {
      this.searchQuery = query;
    },
  },

  mounted() {
    eventBus.on("search", this.handleSearch);
  },

  beforeUnmount() {
    eventBus.off("search", this.handleSearch);
  },
};
</script>

<style scoped>
.user-list {
  padding: 24px;

  background-color: #fff;

  border-radius: 8px;

  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);

  margin: 20px 0;
}

.header {
  display: flex;

  justify-content: space-between;

  align-items: center;

  margin-bottom: 12px;
}

h2 {
  color: #4b3f6b;

  font-size: 24px;
}

.add-btn {
  background-color: #754bc5;

  color: white;

  padding: 6px 12px;

  border: none;

  cursor: pointer;

  border-radius: 4px;

  font-size: 14px;
}

.add-btn:hover {
  background-color: #5a37a0;
}

.table-responsive {
  width: 100%;

  overflow-x: auto;
}

table {
  width: 100%;

  border-collapse: collapse;
}

th,
td {
  border: 1px solid #ddd;

  padding: 12px 15px;

  text-align: center;

  vertical-align: middle;
}

th {
  background-color: #4b3f6b;

  color: white;

  text-transform: uppercase;

  letter-spacing: 0.1em;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

tr:hover {
  background-color: #ddd;
}

button {
  padding: 6px 12px;

  border: none;

  cursor: pointer;

  border-radius: 4px;

  font-size: 14px;
}

.edit-btn {
  background-color: #4caf50;

  color: white;

  margin-right: 5px;
}

.edit-btn:hover {
  background-color: #45a049;
}

.delete-btn {
  background-color: #f44336;

  color: white;
}

.delete-btn:hover {
  background-color: #d32f2f;
}

@media (max-width: 600px) {
  th,
  td {
    padding: 8px 10px;
  }

  .action-buttons {
    display: flex;

    flex-direction: column;

    align-items: stretch;
  }

  .action-buttons button {
    margin: 5px 0;
  }
}
</style>
