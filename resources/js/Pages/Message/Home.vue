<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { ref, onMounted, onUnmounted, nextTick } from 'vue'
import axios from 'axios'
import Message from './Message.vue'
import MessageInput from './MessageInput.vue'

const props = defineProps({
    user: {
        type: Object,
    },
});

// const userData = document.getElementById('main').getAttribute('data-user')
// const user = JSON.parse(userData)
const webSocketChannel = 'channel_for_everyone'

const messages = ref([])
const scroll = ref(null)

const scrollToBottom = () => {
    nextTick(() => {
        scroll.value.scrollIntoView({ behavior: 'smooth' })
    })
}

const connectWebSocket = () => {
    window.Echo.private(webSocketChannel)
        .listen('GotMessage', async () => {
            await getMessages()
        })
        .error((error) => {
            console.error('WebSocket connection error:', error)
            alert('실시간 연결에 실패했습니다. 페이지를 새로고침 해주세요.')
            // 재연결 시도
        })
    }

const getMessages = async () => {
    try {
        const response = await axios.get(`/messages`)
        messages.value = response.data

        scrollToBottom()
    } catch (err) {
        console.log(err.message)
    }
}

onMounted(() => {
    getMessages()
    connectWebSocket()
})

onUnmounted(() => {
    window.Echo.leave(webSocketChannel)
})
</script>

<template>
    <AuthenticatedLayout>
        <template #header>
            <h2
                class="text-xl font-semibold leading-tight text-gray-800"
            >
                Messaging
            </h2>
        </template>

        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Chat Box</div>
                    <div class="card-body" style="height: 500px; overflow-y: auto">
                        <Message
                            v-for="(message, index) in messages"
                            :key="index"
                            :userId="user.id"
                            :message="message"
                        />
                        <span ref="scroll"></span>
                    </div>
                    <div class="card-footer">
                        <MessageInput/>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
