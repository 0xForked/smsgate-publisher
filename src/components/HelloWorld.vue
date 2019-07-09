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

          <b-form class="mt-4">
            <label for="phone">Provider</label>
            <b-input
              v-model="provider"
              id="provider"
              disabled
            ></b-input>
          </b-form>

          <b-form class="mt-4">
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
      telkomsel: [11, 12, 13, 21, 22, 23, 51, 52],
      indosat: [14, 15, 16, 55, 56, 57, 58],
      xl: [17, 18, 19, 59, 77, 78],
      tri: [96, 97, 98, 99],
      phone: '',
      message: '',
      provider: '',
      isLoading: false,
      isProvider: null
    }
  },
  methods: {
    addSMS () {
      this.isLoading = true
      const issued = new Date()
      const status = 'PROCESS'
      const provider = this.provider
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
  },
  watch: {
    phone: {
      handler: function (val) {
        var isZero = val.slice(0, 1)
        if (isZero != 0) {
          this.isProvider = val.slice(1, 3)
        } else {
          this.isProvider = val.slice(2, 4)
        }

        for(var i = 0; i < this.telkomsel.length; i++) {
          if (this.telkomsel[i] == this.isProvider) {
            this.provider = 'TELKOMSEL'
          }
        }

        for (var i = 0; i < this.indosat.length; i++) {
          if (this.indosat[i] == this.isProvider) {
            this.provider = 'INDOSAT'
          }
        }

        for (var i = 0; i < this.xl.length; i++) {
          if (this.xl[i] == this.isProvider) {
            this.provider = 'XL'
          }
        }

        for (var i = 0; i < this.tri.length; i++) {
          if (this.tri[i] == this.isProvider) {
            this.provider = 'TRI'
          }
        }

        // if (this.telkomsel.includes(this.isProvider)) this.provider = 'TELKOMSEL'
        // if (this.indosat.includes(this.isProvider)) this.provider = 'INDOSAT'
        // if (this.xl.includes(this.isProvider)) this.provider = 'XL'
        // if (this.tri.includes(this.isProvider)) this.provider = 'TRI'

        console.log(this.isProvider)
        console.log(this.provider)
      }
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
