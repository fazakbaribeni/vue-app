<template>
  <div class="booking">
    <h1>Bookings</h1>
    <input v-model="search" placeholder="Search" @input="searchBookings" />
    <table>
      <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Car Make</th>
        <th>Car Model</th>
        <th>Phone Number</th>
        <th>Booking Date</th>
        <th>Edit</th> <!-- Changed the header -->
        <!-- Add more table headers for other properties here -->
      </tr>
      </thead>
      <tbody>
      <tr v-for="booking in filteredBookings" :key="booking.id">
        <td>{{ booking.name }}</td>
        <td>{{ booking.email }}</td>
        <td>{{ booking.vehicle_make }}</td>
        <td>{{ booking.vehicle_model }}</td>
        <td>{{ booking.phone_number }}</td>
        <td>{{ booking.booking_date }}</td>
        <td>
          <!-- Use router-link to navigate to edit page -->
          <router-link :to="'booking/edit/' + booking.id">Edit</router-link>
        </td>
        <!-- Add more table cells for other properties here -->
      </tr>
      </tbody>
    </table>
  </div>
</template>

<!-- The rest of your component remains the same -->

<style scoped>
/* Your custom CSS styles */
.booking {
  font-family: Arial, sans-serif;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

table, th, td {
  border: 1px solid #ddd;
}

th, td {
  padding: 8px;
  text-align: left;
}

input {
  padding: 8px;
  width: 100%;
  margin-bottom: 20px;
}
</style>

<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';

const headers = [
  { text: 'Name' },
  // Add more headers for other properties here
];

const bookings = ref([]);
const search = ref('');

// Create a computed property for filtered bookings
const filteredBookings = computed(() => {
  return bookings.value.filter((booking) => {
    const searchLower = search.value.toLowerCase();
    return (
        booking.name.toLowerCase().includes(searchLower) ||
        booking.email.toLowerCase().includes(searchLower) ||
        booking.vehicle_make.toLowerCase().includes(searchLower) ||
        booking.vehicle_model.toLowerCase().includes(searchLower) ||
        booking.phone_number.toLowerCase().includes(searchLower) ||
        booking.booking_date.toLowerCase().includes(searchLower)
    );
  });
});

const searchBookings = () => {
  // The search logic is handled by the computed property
};

const fetchBookings = async () => {
  try {
    const response = await axios.get('http://booking-app.test/api/bookings'); // Replace with your API URL
    bookings.value = response.data;
  } catch (error) {
    console.error('Error fetching bookings:', error);
  }
};

onMounted(() => {
  fetchBookings();
});
</script>
