<template>
    <v-dialog
        v-if="modal"
        v-model="activator"
        :max-width="maxWidth"
        :width="width"
        :persistent="persistent"
    >
        <v-container class="pa-0 white schedule-wrapper">
            <v-layout wrap row justify-left align-center>
                <v-flex xs12>
                    <v-container fluid class="schedule-header">
                        <h3>Schedule an Overview Call</h3>
                    </v-container>
                </v-flex>
                <v-flex xs12 v-if="stepper === 1">
                    <v-container fluid class="pt-4 pr-4 pl-4 pb-4 white schedule-content">
                        <div
                            class="input-wrapper"
                            v-for="(textField, index) in schedulerTextFields"
                            :key="index"
                        >
                            <label class="label">{{textField.label}}</label>
                            <!-- Handles Name and Email -->
                            <input v-if="textField.id !== 'phone'"
                                class="text-field"
                                v-model="textField.value"
                                :placeholder="textField.placeholder"
                            />
                            <!-- Handles Phone number -->
                            <the-mask
                                v-else
                                mask="(+1) ###-###-####"
                                type="tel"
                                :masked="false"
                                class="text-field"
                                v-model="textField.value"
                                :placeholder="textField.placeholder"
                            ></the-mask>
                            <span
                                v-if="textField.errorState"
                                class="caption red--text"
                            >{{ textField.errorMessage }}</span>
                        </div>
                    </v-container>
                </v-flex>
                <v-flex xs12 v-else-if="stepper === 2">
                    <v-container fluid class="pa-0 schedule-content">
                        <date-slot
                            v-for="(slot, index) in availableSlots" :key="index"
                            v-on:updateSelectedSlot="onUpdateSelectedSlot"
                            :date="new Date(slot.date)"
                            :availableSlots="slot.timeSlots"
                            :selectedSlot="selectedSlot"
                        ></date-slot>
                    </v-container>
                    <!-- <v-container v-else fluid fill-height class="pa-0 white schedule-content">
                        <v-progress-circular
                            class="schedule-progress-circular"
                            indeterminate
                            color="purple"
                        ></v-progress-circular>
                    </v-container> -->
                </v-flex>
                <v-flex xs12>
                    <v-container fluid class="pa-2 schedule-footer">
                        <v-btn
                            large
                            flat
                            dark
                            class="action-btn"
                            @click="cancelScheduler"
                        >
                            <span class="header font-weight-bold black--text">Cancel</span>
                        </v-btn>
                        <v-btn
                            large
                            flat
                            :loading="nextButtonLoading"
                            :disabled="!(isFormValid) || nextButtonLoading"
                            :class="nextButtonClass + ' action-btn'"
                            v-if="stepper === 1"
                            @click="nextButtonAction"
                        >
                            <span class="header font-weight-bold">Next</span>
                        </v-btn>
                        <v-btn
                            large
                            flat
                            :loading="scheduleButtonloading"
                            :disabled="selectedSlot == null || scheduleButtonloading"
                            :class="scheduleButtonClass + ' action-btn'"
                            v-else-if="stepper === 2"
                            @click="scheduleButtonAction"
                        >
                            <span class="header font-weight-bold">Schedule Call</span>
                        </v-btn>
                    </v-container>
                </v-flex>
            </v-layout>
        </v-container>
    </v-dialog>
    <v-container v-else class="pa-0 white schedule-wrapper">
        <v-layout wrap row justify-left align-center>
            <v-flex xs12>
                <v-container fluid class="schedule-header">
                    <h3>Schedule an Overview Call</h3>
                </v-container>
            </v-flex>
            <v-flex xs12 v-if="stepper === 1">
                <v-container fluid class="pt-4 pr-5 pl-5 pb-4 white schedule-content">
                    <div
                        class="input-wrapper"
                        v-for="(textField, index) in schedulerTextFields"
                        :key="index"
                    >
                        <label class="label">{{textField.label}}</label>
                        <!-- Handles Name and Email -->
                        <input v-if="textField.id !== 'phone'"
                            class="text-field"
                            v-model="textField.value"
                            :placeholder="textField.placeholder"
                        />
                        <!-- Handles Phone number -->
                        <the-mask
                            v-else
                            mask="(+1) ###-###-####"
                            type="tel"
                            :masked="false"
                            class="text-field"
                            v-model="textField.value"
                            :placeholder="textField.placeholder"
                        ></the-mask>
                    </div>
                </v-container>
            </v-flex>
            <v-flex xs12 v-else-if="stepper === 2">
                <v-container v-if="availableSlots.length > 0" fluid class="pa-0 schedule-content">
                    <date-slot
                        v-for="(slot, index) in availableSlots" :key="index"
                        v-on:updateSelectedSlot="onUpdateSelectedSlot"
                        :date="new Date(slot.date)"
                        :availableSlots="slot.timeSlots"
                        :selectedSlot="selectedSlot"
                    ></date-slot>
                </v-container>
                <v-container v-else fluid fill-height class="pa-0 white schedule-content">
                    <v-progress-circular
                        class="schedule-progress-circular"
                        indeterminate
                        color="purple"
                    ></v-progress-circular>
                </v-container>
            </v-flex>
            <v-flex xs12>
                <v-container fluid class="pa-2 schedule-footer">
                    <v-btn
                        large
                        flat
                        dark
                        class="action-btn"
                        @click="cancelScheduler"
                    >
                        <span class="header font-weight-bold black--text">Cancel</span>
                    </v-btn>
                    <v-btn
                        large
                        flat
                        :loading="nextButtonLoading"
                        :disabled="!(isFormValid) || nextButtonLoading"
                        :class="nextButtonClass + ' action-btn'"
                        v-if="stepper === 1"
                        @click="nextButtonAction"
                    >
                        <span class="header font-weight-bold">Next</span>
                    </v-btn>
                    <v-btn
                        large
                        flat
                        :loading="scheduleButtonloading"
                        :disabled="selectedSlot == null || scheduleButtonloading"
                        :class="scheduleButtonClass + ' action-btn'"
                        v-else-if="stepper === 2"
                        @click="scheduleButtonAction"
                    >
                        <span class="header font-weight-bold">Schedule Call</span>
                    </v-btn>
                </v-container>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
    import DateSlot from './date-slot.vue'
    import { TheMask } from 'vue-the-mask'

    export default {
        name: 'ScheduleCard',
        props: {
            modal: {
                default: false,
                type: Boolean
            },
            persistent: {
                default: true,
                type: Boolean
            },
            maxWidth: {
                default: 'auto',
                type: String
            },
            width: {
                default: '38rem',
                type: String
            },
            activator: {
                default: false,
                type: Boolean
            }
        },
        components: {
            DateSlot,
            TheMask,
        },
        data() {
            return {
                selectedSlot: null,
                scheduleButtonClass: 'disabled-btn',
                scheduleButtonloading: false,
                nextButtonClass: 'disabled-btn',
                nextButtonLoading: false,
                stepper: 1,
                schedulerTextFields: [{
                    id: 'name',
                    label: 'Full Name',
                    type: 'text',
                    value: '',
                    placeholder: 'Full Name',
                    errorState: false,
                    errorMessage: null
                }, {
                    id: 'email',
                    label: 'Email',
                    type: 'text',
                    value: '',
                    placeholder: 'Email Address',
                    errorState: false,
                    errorMessage: null
                }, {
                    id: 'phone',
                    label: 'Phone',
                    type: 'tel',
                    value: '',
                    placeholder: 'Phone Number',
                    errorState: false,
                    errorMessage: null
                }],
                isFormValid: false,
                availableSlots: []
            }
        },
        methods: {
            /* API Requests START */
            async getAvailableSlotsAPI () {
                const response = await this.$axios.$get('schedules/get-available-slots')
                if (response.success) {
                    this.availableSlots = response.data.availableSlots
                } else {
                    // alert('error requesting available slots')
                    this.$emit('errorAction')
                }
            },
            
            async scheduleSlotAPI (payload) {
                const response = await this.$axios.$post('/schedules/create-scheduled-slot', payload)
                if (response.success) {
                    this.$emit('successAction')
                } else {
                    this.$emit('errorAction')
                }
            },
            /* API Requests End */

            onUpdateSelectedSlot(value) {
                this.selectedSlot = value
            },

            cancelScheduler () {
                this.stepper = 1
                this.selectedSlot = null
                this.clearForm()
                this.$emit('cancelScheduler')
            },

            nextButtonAction () {
                this.nextButtonLoading = true
                this.getAvailableSlotsAPI().then(() => {
                    this.nextButtonLoading = false
                    this.stepper = 2
                })
            },

            scheduleButtonAction () {
                if (this.selectedSlot !== null) {
                    this.scheduleButtonloading = true
                    var postBody = {
                        slot: new Date(this.selectedSlot).valueOf(),
                        name: this.schedulerTextFields[0].value,
                        email: this.schedulerTextFields[1].value,
                        contactNumber: this.schedulerTextFields[2].value
                    }

                    this.scheduleSlotAPI(postBody).then(() => {
                        this.scheduleButtonloading = false
                        this.stepper = 1
                        this.selectedSlot = null
                        this.clearForm()
                    })
                }
            },

            validateEmail(email) {
                var re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
                return re.test(email);
            },
          
            checkForm () {
                let hasError = false

                for (let i = 0; i < this.schedulerTextFields.length; i++) {
                    if (!this.schedulerTextFields[i].value) {
                        hasError = true
                    }

                    // Email check 
                    if (this.schedulerTextFields[i].id === 'email') {
                        if (!this.validateEmail(this.schedulerTextFields[i].value)) {
                            hasError = true
                        }
                    }

                    if (this.schedulerTextFields[i].id === 'phone') {
                        if (this.schedulerTextFields[i].value.length !== 10) {
                            hasError = true
                        }
                    }
                }
            
                if (hasError) {
                    this.isFormValid = false
                } else {
                    this.isFormValid = true
                }
            },

            clearForm () {
                for (let i = 0; i < this.schedulerTextFields.length; i++) {
                    this.schedulerTextFields[i].value = ''
                }
            }
        },
        watch: {
            selectedSlot () {
                if (this.selectedSlot === null) {
                    this.scheduleButtonClass = 'disabled-btn'
                } else {
                    this.scheduleButtonClass = ''
                }
            },

            schedulerTextFields: {
                handler: function (newValue) {
                    this.checkForm()
                }, 
                deep: true
            },

            isFormValid () {
                if (!this.isFormValid) {
                    this.nextButtonClass = 'disabled-btn'
                } else {
                    this.nextButtonClass = ''
                }
            }
        }
    }
</script>

<style scoped lang="scss">
    .schedule-wrapper {
        border: 1px solid lightgrey;
        border-radius: 3px;
        overflow: hidden;
        width:auto;
        max-width: 100%;

    
        .schedule-header {
            border-bottom: 1px solid lightgrey;
            position: relative;
            background-color: #f9f9fa;
            text-align: center;

            h3 {
                font-size: 1.4375rem;
                font-weight: 600;
            }
        }

        .schedule-content {
            position: relative;
            overflow-y: scroll;
            max-height: 65vh;

            .input-wrapper {
                &:not(:last-child) {
                    margin-bottom: .75rem;
                }

                .label {
                    &:not(:last-child) {
                        margin-bottom: 0;
                    }

                    color: #363636;
                    width: 100%;
                    margin-bottom: 0;
                    z-index: 20;
                    -webkit-box-flex: 0;
                    -ms-flex: 0 0;
                    flex: 0 0;
                    padding: .5rem;
                    white-space: nowrap;
                    font-size: .875rem;
                    font-weight: 500;
                    line-height: 1;
                    letter-spacing: .5px;
                }
            }

            .text-field {
                width: 100%;
                height: auto;
                line-height: 2.625rem;
                transition: all .2s cubic-bezier(.445,.05,.55,.95);
                padding: 0 1rem;
                border: 1px solid transparent;
                border-radius: 3px;
                box-shadow: none;
                height: 2.75em;
                position: relative;
                background-color: #fff;
                border-color: #dbdbdb;
                color: #363636;
                box-shadow: inset 0 1px 2px rgba(64,62,65,.1);
                max-width: 100%;
                width: 100%;

                &:focus  {
                    border: 1px solid #69488e;
                    box-shadow: none!important;
                    outline: none!important;
                }
            }

            &::-webkit-scrollbar { 
                display: none; 
            }

            .schedule-progress-circular {
                margin: 1rem auto;
            }
        }
        .schedule-footer {
            border-top: 1px solid lightgrey;
            background-color: #f9f9fa;
            text-align: right;
            font-weight: 600;

            .action-btn {
                text-transform: capitalize;
                border-radius: 3px;

                &:first-child {
                    border: 1px solid lightgrey;
                    background-color:#fff !important;
                    &:hover {
                        border-color: #b6b6b6;
                        background-color:#fff !important;
                    }
                }
                &:last-child {
                    background-color: #69488e;
                    color: #fff !important;
                }
            }
            .disabled-btn {
                opacity: 0.6 !important;
            }
        }
    }
</style>