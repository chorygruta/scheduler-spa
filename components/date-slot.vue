<template>
    <v-container fluid class="pa-0 date-slot-card">
        <v-layout wrap row justify-left align-center>
            <v-flex xs12 text-xs-left>
                <div class="header pl-3 pr-3 pt-1 pb-1">
                    <span class="font-weight-medium"> {{ header }} </span>
                </div>
            </v-flex>
            <v-flex xs12>
                <v-container fluid grid-list-lg>
                    <v-layout wrap>
                        <v-flex xs4 v-for ="(slot, index) in availableSlots" :key="index">
                            <time-slot
                                @click.native="selectSlot(slot)"
                                :time="new Date(slot)"
                                :selectedSlot="selectedSlot === slot"
                            ></time-slot>
                        </v-flex>
                    </v-layout>
                </v-container>
            </v-flex>
            <v-flex>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
    import TimeSlot from './../components/time-slot.vue'

    export default {
        name: 'DateSlot',
        props: {
            date: Date,
            availableSlots: Array,
            selectedSlot: String
        },
        components: {
            TimeSlot
        },
        methods: {
            selectSlot (slot) {
                if (this.selectedSlot === slot) {
                    this.$emit('updateSelectedSlot', null)
                } else {
                    this.$emit('updateSelectedSlot', slot)
                }
            }
        },
        computed: {
            header () {
                const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
                const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'Novemer', 'December']
                const date = this.date.getDate()

                let dayName = days[this.date.getDay()]
                let monthName = months[this.date.getMonth()]
                let dateString
                const yearString = this.date.getFullYear()

                if (date === 1) {
                    dateString = '1st'
                } else if (date === 2) {
                    dateString = '2nd'
                } else if (date === 3) {
                    dateString = '3rd'
                } else {
                    dateString = date + 'th'
                }

                return dayName + ', ' + monthName + ' ' + dateString + ', ' + yearString
            }
        }
    }
</script>

<style scoped>
    .date-slot-card {
        background-color: #fff;
        height: auto;
        border-bottom: 1px solid lightgrey;
    }
    .header {
        border: 1px solid lightgrey;
        border-right: none;
        border-left: none;
        border-top: none;
    }
</style>