<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const props = defineProps(['messages'])

const message = ref('')

const sendMessage = () => {
    if (message.value.trim() === '') {
        alert('Please enter a message!')
        return
    }

    messageRequest(message.value)
    message.value = ''
}

const messageRequest = async (text) => {
    try {
        var response = await axios.post(`/message`, { text })
    } catch (err) {
        console.log(err.message)
    }
}

</script>

<template>
    <div class="input-group">
        <input
            v-model="message"
            @keyup.enter="sendMessage"
            autocomplete="off"
            type="text"
            class="form-control"
            placeholder="Message..."
        />
        <div class="input-group-append">
            <button
                @click="sendMessage"
                class="btn btn-primary"
                type="button"
            >
                Send
            </button>
        </div>
    </div>
</template>
