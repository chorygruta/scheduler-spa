<template>
    <v-container fluid :class="containerColorClass + ' time-slot-card'">
        <v-layout wrap row justify-left align-center>
            <v-flex xs12 text-xs-left>
                <span>{{ time.toLocaleTimeString() | formatTime}}</span>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
  export default {
    name: 'TimeSlot',
    props: {
        time: Date,
        selectedSlot: Boolean
    },
    filters: {
        formatTime(value) {
            let time = value.slice(0, value.length - 6)
                + ' '
                + value.substring(value.length - 2, value.length)
            return time.toLowerCase()
        }
    },
    data() {
        return {
            containerColorClass: ''
        }
    },
    watch: {
        selectedSlot () {
            this.changeButtonColor()
        }
    },
    methods: {
        changeButtonColor () {
            if (this.selectedSlot) {
                this.containerColorClass = 'selectedSlot'
            } else {
                this.containerColorClass = ''
            }
        }
    }
  }
</script>

<style lang="scss" scoped>
    .time-slot-card {
        background-color: #fff;
        max-width: 30vw;
        border-radius: 3px;
        border: 1px solid lightgrey;
        transition: background-color 0.4s ease;
        padding: 16px 8px;
        transition: transform 0.2s ease;

        &:hover {
           transform: scale(1.05);
        }
    }
    .selectedSlot {
        background-color: #69488e;
        border-color: #69488e;
        color: white;
        transform: scale(1) !important;
    }
</style>