<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Contact Management</h1>
    
    <!-- 联系人列表 -->
    <div v-if="contacts.length" class="contact-list mb-5">
      <h3>Contacts</h3>
      <ul class="list-group">
        <li class="list-group-item d-flex justify-content-between align-items-center" v-for="contact in contacts" :key="contact._id">
          <div>
            <strong>{{ contact.name }}</strong> <br>
            <span>{{ contact.email }}</span> <br>
            <span>{{ contact.phone }}</span>
          </div>
          <div>
            <button class="btn btn-primary btn-sm mr-2" @click="editContact(contact)">Edit</button>
            <button class="btn btn-danger btn-sm" @click="deleteContact(contact._id)">Delete</button>
          </div>
        </li>
      </ul>
    </div>

    <!-- 编辑表单 -->
    <div v-if="editingContact" class="edit-form mb-5">
      <h3>Edit Contact</h3>
      <form @submit.prevent="updateContact">
        <div class="form-group">
          <label for="name">Name:</label>
          <input type="text" v-model="editingContact.name" id="name" class="form-control" required>
        </div>
        <div class="form-group">
          <label for="email">Email:</label>
          <input type="email" v-model="editingContact.email" id="email" class="form-control" required>
        </div>
        <div class="form-group">
          <label for="phone">Phone:</label>
          <input type="text" v-model="editingContact.phone" id="phone" class="form-control" required>
        </div>
        <button type="submit" class="btn btn-success">Save</button>
        <button type="button" class="btn btn-secondary ml-2" @click="cancelEdit">Cancel</button>
      </form>
    </div>

    <!-- 新增联系人 -->
    <div class="new-contact">
      <h3>Add New Contact</h3>
      <form @submit.prevent="addContact">
        <div class="form-group">
          <label for="newName">Name:</label>
          <input type="text" v-model="newContact.name" id="newName" class="form-control" required>
        </div>
        <div class="form-group">
          <label for="newEmail">Email:</label>
          <input type="email" v-model="newContact.email" id="newEmail" class="form-control" required>
        </div>
        <div class="form-group">
          <label for="newPhone">Phone:</label>
          <input type="text" v-model="newContact.phone" id="newPhone" class="form-control" required>
        </div>
        <button type="submit" class="btn btn-primary">Add Contact</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      contacts: [],           // 联系人列表
      newContact: {           // 新增联系人的数据
        name: '',
        email: '',
        phone: ''
      },
      editingContact: null    // 当前正在编辑的联系人
    };
  },
  mounted() {
    this.fetchContacts();    // 初始化时获取联系人列表
  },
  methods: {
    // 获取联系人列表
    async fetchContacts() {
      const response = await axios.get('http://localhost:5000/api/contacts');
      this.contacts = response.data;
    },
    // 添加新联系人
    async addContact() {
      const response = await axios.post('http://localhost:5000/api/contacts', this.newContact);
      this.contacts.push(response.data);    // 将新联系人加入列表
      this.newContact = { name: '', email: '', phone: '' };  // 清空表单
    },
    // 编辑联系人
    editContact(contact) {
      this.editingContact = { ...contact };  // 克隆联系人信息
    },
    // 更新联系人
    async updateContact() {
      const response = await axios.put(`http://localhost:5000/api/contacts/${this.editingContact._id}`, this.editingContact);
      const updatedContact = response.data;

      // 更新列表中的联系人信息
      const index = this.contacts.findIndex(c => c._id === updatedContact._id);
      this.$set(this.contacts, index, updatedContact);

      this.editingContact = null;  // 退出编辑模式
    },
    // 删除联系人
    async deleteContact(contactId) {
      await axios.delete(`http://localhost:5000/api/contacts/${contactId}`);
      this.contacts = this.contacts.filter(contact => contact._id !== contactId);
    },
    // 取消编辑
    cancelEdit() {
      this.editingContact = null;  // 退出编辑模式
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
}

.contact-list h3 {
  margin-bottom: 20px;
}

.list-group-item {
  margin-bottom: 10px;
}

.edit-form, .new-contact {
  margin-top: 40px;
}

.edit-form h3, .new-contact h3 {
  margin-bottom: 20px;
}

button {
  cursor: pointer;
}
</style>
