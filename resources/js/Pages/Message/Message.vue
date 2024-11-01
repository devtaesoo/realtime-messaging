<script setup>
import { computed } from 'vue'
const props = defineProps({
    userId: {
        type: Number,
        required: true
    },
    message: {
        type: Object,
        required: true
    }
});

const alertClass = computed(() =>
    props.userId === props.message.user_id ? 'alert-primary' : 'alert-secondary'
)

</script>

<template>
    <div :class="['row', { 'justify-content-end': userId === message.user_id }]">
        <div class="col-md-6">
            <small class="text-muted">
                <strong>{{ message.user.name }} | </strong>
            </small>
            <small class="text-muted float-right">
                {{ message.time }}
            </small>
            <div
                :class="['alert', alertClass]"
                role="alert"
            >
                {{ message.text }}
            </div>
        </div>
    </div>
</template>
