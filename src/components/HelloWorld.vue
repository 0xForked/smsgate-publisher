<template>
  <div class="container">
    <div class="row justify-content-center">
      <b-card
        style="width: 35rem;"
      >
        <div class="card-head">
          <h5>SMSGATE PUBLISHER</h5>
        </div>
        <div class="card-body">

          <b-form>
            <label for="phone">Number</label>
            <b-input
              v-model="phone"
              :state="phoneValidation"
              id="phone"
            ></b-input>
            <b-form-invalid-feedback :state="phoneValidation">
              Please look at your phone.
            </b-form-invalid-feedback>
            <b-form-valid-feedback :state="phoneValidation">
              Looks Good.
            </b-form-valid-feedback>
          </b-form>

          <b-form style="margin-top:20px">
            <label for="message">Message</label>
            <b-form-textarea
              id="message"
              v-model="message"
              :state="messageValidation"
              placeholder="Enter at least 10 characters"
              rows="3"
            ></b-form-textarea>
            <b-form-invalid-feedback :state="messageValidation">
              Text lenght min 10.
            </b-form-invalid-feedback>
            <b-form-valid-feedback :state="messageValidation">
              Looks Good.
            </b-form-valid-feedback>
          </b-form>

        </div>
        <b-button
          variant="primary float-right mr-3"
          :disabled="!phoneValidation"
          v-on:click="addSMS"
        >
          <b-spinner small type="grow" v-show="isLoading"></b-spinner>
          {{ (!isLoading) ? 'save to queue' : 'push to queue...' }}
        </b-button>

      </b-card>
    </div>
  </div>
</template>

<script>
import { db } from '../main'

export default {
  name: 'HelloWorld',
  data () {
    return {
      phone: '',
      message: '',
      isLoading: false
    }
  },
  methods: {
    addSMS () {
      this.isLoading = true
      const issued = new Date()
      const status = 'PROCESS'
      const provider = null
      const sent = null
      const number = this.phone
      const message = this.message
      let sms = db.collection('sms')
      sms.add({
        issued, status, provider, number, message, sent
      }).then(ref => {
        sms.doc(ref.id).update({id: ref.id})
        this.isLoading = false
        alert('Added new document')
        location.reload()
      })
    }
  },
  computed: {
    phoneValidation  () {
      return this.phone.length > 4 && this.phone.length < 13
    },
    messageValidation () {
      return this.message.length >= 10
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .card-head {
    text-align: center;
    margin-bottom: 2em;
  }
  .card-body {
    text-align: left;
  }
</style>
