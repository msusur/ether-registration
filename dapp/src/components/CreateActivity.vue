<template>
  <div>
    <div class="container">
      <form @submit.prevent="createActivityButton()">
        <div class="form-group">
          <label for="exampleFormControlInput1">Activity Name</label>
          <input type="text" class="form-control" id="exampleFormControlInput1" v-model="activity_name" placeholder="Meeting">
        </div>
        <div class="form-check">
          <input class="form-check-input" type="checkbox" v-on:change="free($event)" value="" id="isPayActiveCheck">
          <label class="form-check-label" for="isPayActiveCheck">
            Free
          </label>
        </div>
        <div class="form-check">
          <input class="form-check-input" type="checkbox" v-model="is_active"  value="" id="isActiveCheck">
          <label class="form-check-label" for="isActiveCheck">
            Is Active
          </label>
        </div>
        <br>
        <div class="form-group col-md-2" v-show="isActivePrice">
          <label for="price">Price</label>
          <input type="number" class="form-control" v-model="price" id="price">
        </div>
        <br>
        <div class="form-group col-md-2">
          <label for="participantLimit">Participant Limit</label>
          <input type="number" class="form-control" v-model="participant_limit" id="participantLimit">
        </div>
        <br>
        <button type="submit" class="btn btn-primary mb-2">Create Activity</button>
      </form>
      <div>
        <ul>
          <li>{{this.activity_name}}</li>
          <li>{{this.price}}</li>
          <li>{{this.participant_limit}}</li>
        </ul>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'CreateActivity',
  data () {
    return {
      c_instance: null,
      coinbase: null,
      activity_name: '',
      price: null,
      is_active: null,
      participant_limit: null,
      // validation_number: null,
      activity_list: null,
      isActivePrice: true

    }
  },
  beforeCreate () {
  },
  created () {
    this.c_instance = this.$store.getters.contractInstance()
    this.coinbase = this.$store.getters.currentAddress
    const temp = this.c_instance.methods.getTotalActivity().call()
    temp.then(function (val) {
      console.log('Total activity', val)
    })
  },
  methods: {
    free (event) {
      if (event.target.checked) {
        this.isActivePrice = false
        this.price = 0
      } else {
        this.isActivePrice = true
      }
    },
    createActivityButton () {
      this.pending = true
      console.log('Try to create')
      const temp = this.c_instance.methods.createActivity(
        this.activity_name,
        this.participant_limit,
        this.price
      ).send(
        {value: this.$options.filters.toWei('0.1'), from: this.coinbase, gas: 4700000})
      temp.then(function (error, value) {
        console.log(error)
        console.log(value)
      })
    }
  }
}
</script>

<style scoped>

</style>
