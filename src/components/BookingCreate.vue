<template>


  <div class="booking-create">
    <h1>Create Booking</h1>
    <form @submit.prevent="createBooking">
      <div class="form-group">
        <label for="name">Name:</label>
        <input
            type="text"
            id="name"
            v-model="booking.name"
            class="form-control"
            required
        />
      </div>

      <div class="form-group">
        <label for="email">Email:</label>
        <input
            type="email"
            id="email"
            v-model="booking.email"
            class="form-control"
            required
        />
      </div>

      <div class="form-group">
        <label for="phone">Phone:</label>
        <input
            type="tel"
            id="phone_number"
            v-model="booking.phone_number"
            class="form-control"
            required
        />
      </div>

      <div class="form-group">
        <label for="vehicle_make">Vechile Make:</label>
        <input
            type="text"
            id="vehicle_make"
            v-model="booking.vehicle_make"
            class="form-control"
            required
        />
      </div>

      <div class="form-group">
        <label for="vehicle_model">Vechile Model:</label>
        <input
            type="text"
            id="vehicle_model"
            v-model="booking.vehicle_model"
            class="form-control"
            required
        />
      </div>

      <!-- Date and Time Picker -->
      <div class="form-group">
        <label for="bookingDateTime">Booking Date and Time:</label><br>
        <flat-pickr
            id="bookingDateTime"
            v-model="booking.booking_date"
            :config="datePickerConfig"
            @change="onDateSelection"
        ></flat-pickr>
      </div>

      <!-- Add more input fields for other booking properties -->

      <button type="submit" class="btn btn-primary btn-lg btn-block mt-4">
        <i class="fas fa-check-circle mr-2"></i>Create Booking
      </button>

      <div class="success-message" v-if="successMessage">{{ successMessage }}</div>
    </form>
  </div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<script setup>
import { ref, defineProps } from 'vue';
import axios from 'axios';
import flatPickr from 'vue-flatpickr-component';
import 'flatpickr/dist/flatpickr.css';

const props = defineProps(['bookingDateConfig']);

const successMessage = ref('');

// API ENDPOINT base
const url = "http://booking-app.test/api/";


const booking = ref({
  name: '',
  email: '',
  phone_number: '',
  vehicle_make: '',
  vehicle_model: '',
  booking_date: '',
  // Add more properties here
});

const bookedTimeSlots = ref([]);

const datePickerConfig = {
  enable: [
    function (date) {
      return date.getDay() >= 1 && date.getDay() <= 5; // Monday to Friday
    },
    // Add other configuration options as needed
  ],
  noCalendar: false,
  minuteIncrement: 30,
  enableTime: true,
  dateFormat: 'Y-m-d H:i',
  minTime: '09:00',
  maxTime: '17:30',
  disable: [],
};

const fetchBookedTimeSlots = async (selectedDate) => {
  try {
    const response = await axios.get(url + 'booked-slots', {
      params: {
        date: selectedDate,
      },
    });
    bookedTimeSlots.value = response.data.dates;

    // If there is a date from API response
    if (Array.isArray(bookedTimeSlots.value) && bookedTimeSlots.value.length > 0) {
      console.log('Booked Time Slots:', bookedTimeSlots.value); // Debugging line

      datePickerConfig.disable = bookedTimeSlots.value.map((slot) => {
        // Format the 'from' and 'to' slots as "yyyy-mm-dd h:i"
        const from = new Date(slot);
        const to = new Date(slot);
        // Format the time part with leading zero for minutes but without leading zero for hours
        const formattedTime = (date) => {
          const hours = date.getHours();
          const minutes = date.getMinutes();
          return `${hours < 10 ? '0' : ''}${hours}:${minutes < 10 ? '0' : ''}${minutes}`;
        };

        const formattedSlot = {
          from: `${from.getFullYear()}-${from.getMonth() + 1}-${from.getDate()} ${formattedTime(from)}`,
          to: `${to.getFullYear()}-${to.getMonth() + 1}-${to.getDate()} ${formattedTime(to)}`,
        };

        console.log('Disable Slot:', formattedSlot); // Debugging line

        return formattedSlot;
      });

      console.log('config:' ,datePickerConfig)
    } else {
      datePickerConfig.disable = [];
    }

  } catch (error) {
    console.error('Error fetching booked time slots:', error);
  }


};

const onDateSelection = () => {

  if (booking.value.booking_date) {
    fetchBookedTimeSlots(booking.value.booking_date);
  }
};


const createBooking = async () => {
  try {
    const response = await axios.post(url + 'bookings', booking.value);
    successMessage.value = 'Booking successful!'; // Set the success message

    // Handle success, e.g., show a success message and clear the form.
  } catch (error) {
    successMessage.value = 'Something Went Wrong, contact us to re-try again!'; // Set the success message
    console.error('Error creating booking:', error);
    // Handle errors, e.g., show an error message.
  }
};


</script>
<style scoped>
/* Your custom CSS styles */
.booking-create {
  font-family: Arial, sans-serif;
  max-width: 400px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 20px;
}

.form-control {
  width: 100%;
  padding: 8px;
}
</style>