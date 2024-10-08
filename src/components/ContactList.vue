<template>
  <div>
    <h1>Contact Management</h1>

    <input v-model="search" placeholder="Search contacts..." />
    <form @submit.prevent="addContact">
      <input v-model="newContact.name" placeholder="Name" required />
      <input v-model="newContact.email" placeholder="Email" required />
      <input v-model="newContact.phone" placeholder="Phone" required />
      <button type="submit">Add Contact</button>
    </form>

    <ul>
      <li v-for="contact in filteredContacts" :key="contact._id">
        <strong>{{ contact.name }}</strong><br>
        Email: {{ contact.email }}<br>
        Phone: {{ contact.phone }}<br>
        <button @click="deleteContact(contact._id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      contacts: [],
      search: '',
      newContact: {
        name: '',
        email: '',
        phone: ''
      }
    };
  },
  computed: {
    filteredContacts() {
      return this.contacts.filter(contact =>
        contact.name.toLowerCase().includes(this.search.toLowerCase()) ||
        contact.email.toLowerCase().includes(this.search.toLowerCase()) ||
        contact.phone.includes(this.search)
      );
    }
  },
  methods: {
    async fetchContacts() {
      const response = await axios.get('http://localhost:5000/api/contacts');
      this.contacts = response.data;
    },
    async addContact() {
      const response = await axios.post('http://localhost:5000/api/contacts', this.newContact);
      this.contacts.push(response.data);
      this.newContact = { name: '', email: '', phone: '' };
    },
    async deleteContact(id) {
      await axios.delete(`http://localhost:5000/api/contacts/${id}`);
      this.contacts = this.contacts.filter(contact => contact._id !== id);
    }
  },
  mounted() {
    this.fetchContacts();
  }
};
</script>

<style scoped>
/* 样式略 */
</style>
