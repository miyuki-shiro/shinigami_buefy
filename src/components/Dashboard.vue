<style>
    .btn { margin: 0.1em 0.8em; }
</style>

<template>
    <div class="container">
        <section class="section section-padding">
            <div class="has-text-centered">
                <b-icon pack="fas" icon="skull" size="is-large" type="is-primary"></b-icon>
                <h1 class="title">Get your appointment with Death now</h1>
            </div>
        </section>
            
        <section class="section">
            <div class="columns is-variable is-8">

                <div class="column is-one-third">
                    <b-field label="1. Select appointment date">
                        <b-datepicker v-model="date" inline :unselectable-days-of-week="[0, 6]" @input="setDate"></b-datepicker>
                    </b-field>
                </div>

                <div class="column is-one-third" v-if="day">
                    <b-field label="2. Choose a hour"></b-field>
                    <div class="buttons is-inline-block" v-for="ah in avhours" :key="ah.value">
                        <b-button type="is-primary btn" v-model="ah.value" @click="setHour(ah.value)">{{ah.name}}</b-button>
                    </div>
                    <div v-if="loading" style="margin-top: 2em" class="has-text-centered">
                         <b-icon pack="fas" icon="spinner" size="is-large" custom-class="fa-pulse"></b-icon>
                         <br><p>Loading</p>
                    </div>
                    <div v-if="postresponse" style="margin-top: 1em">
                        <div v-if="postresponse.data">
                            <b-message title="Success" type="is-success" aria-close-label="Close message" @close="reload">
                                {{postresponse.message.success}}
                            </b-message>
                        </div>
                        <div v-else>
                            <b-message title="Warning" type="is-danger" aria-close-label="Close message" @close="reload">
                                {{postresponse.warning}}
                            </b-message>
                        </div>
                    </div>
                    
                </div>

                <div class="column is-one-third" v-if="hour">
                    <b-field label="3. Complete the required information"></b-field>
                    <form @submit.prevent="onSubmit">
                        <b-field label="Date">
                            <b-input v-model="day" placeholder="Date selected..." readonly></b-input>
                        </b-field>
                        <b-field label="Hour">
                            <b-input v-model="hour" placeholder="Hour selected..." readonly></b-input>
                        </b-field>
                        <b-field label="Email">
                            <b-input v-model="email" type="email" maxlength="30" placeholder="Your email..."></b-input>
                        </b-field>
                        <div class="buttons is-centered">
                            <b-button type="is-primary" @click="sendForm">Submit</b-button>
                            <b-button type="is-dark" @click="reload">Cancel</b-button>
                        </div>
                    </form>
                </div>
            </div>
        </section>

        <!--<section v-if="appointments">
            <table class="table">
                <thead>
                    <tr><th>ID</th><th>Date</th><th>Start</th><th>End</th><th>Email</th></tr>
                </thead>
                <tbody>
                    <template v-for="apm in appointments">
                        <tr v-bind:key="apm.id">
                            <td>{{ apm.id }}</td>
                            <td>{{ apm.date }}</td>
                            <td>{{ apm.start }}</td>
                            <td>{{ apm.end }}</td>
                            <td>{{ apm.email }}</td>
                        </tr>
                    </template>
                </tbody>
            </table>
        </section>-->           
    </div>
</template>



<script>
import axios from 'axios'
import { API_BASE_URL } from '../config'

export default {
    data() {
        return {
            //appointments: {},
            loading: false,
            date: new Date(),
            day: '', hour: '', email: '', 
            avhours: {},
            postresponse: '',
            hours: [
                { name:'09:00', value: '09:00:00' }, { name:'10:00', value: '10:00:00' }, { name:'11:00', value: '11:00:00' },
                { name:'12:00', value: '12:00:00' }, { name:'13:00', value: '13:00:00' }, { name:'14:00', value: '14:00:00' },
                { name:'15:00', value: '15:00:00' }, { name:'16:00', value: '16:00:00' }, { name:'17:00', value: '17:00:00' }
            ]
        }
    },
    /*async created() {
        try {
            const response = await axios.get(API_BASE_URL + '/appointments')
            this.appointments = response.data.data
        } catch (e) {
            // handle the authentication error here
        }
    },*/
    methods: { 
        setDate: function() { 
            //this.day = `${this.date.getFullYear()}-${this.date.getMonth()+1}-${this.date.getDate()}`
            this.day = this.date.toISOString().substring(0, 10)
            this.hour = ''
            this.showHours()
        },
        showHours: function() {
            this.avhours = this.hours
            /*this.appointments.forEach(element => {
                if (element.date.toString() == this.day) {
                    this.avhours = this.hours.filter(h => h.value != element.start.toString());
                } else {
                    this.avhours = this.hours
                }
            });*/
        },
        setHour: function(d) {
            this.hour = d
        },
        sendForm: function() {
            this.loading = true
            axios.post(API_BASE_URL + '/appointments', {date: this.day, hour: this.hour, email: this.email})
                .then(response => { 
                    this.postresponse = response.data 
                    this.loading = false    
                })
        }, 
        reload: function(){
            window.location.reload()
        }
    }
    
}
</script>
