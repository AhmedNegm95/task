<template>
    <section class="task">
        <!-- <div class="-box"> -->
            <div class="container">
                <div class="search-box d-flex justify-content-center">
                    <form @submit.prevent="onSearch" class="form-inline">
                        <!-- <div class="form-row"> -->
                            <div class="form-group">
                                <label for="start-date">From:</label>
                                <b-input-group>
                                  <b-form-input
                                    @input="$v.startDate.$touch()"
                                    :class="{ invalid: $v.startDate.$error }"
                                    id="start-date"
                                    v-model="startDate"
                                    disabled
                                    type="text"
                                    placeholder="Select Start Date"
                                    autocomplete="off"
                                  ></b-form-input>
                                  <b-input-group-append>
                                    <b-form-datepicker
                                      v-model="startDate"
                                      button-only
                                      right
                                      locale="en-US"
                                      aria-controls="start-date"
                                    ></b-form-datepicker>
                                  </b-input-group-append>
                                </b-input-group>
                                <template v-if="$v.startDate.$error">
                                  <small
                                    v-if="!$v.startDate.required"
                                    class="form-text text-muted"
                                  >
                                    <span>This Field is required</span>
                                  </small>
                                </template>

                            </div>
                            <div class="form-group">
                                <label for="end-date">To:</label>
                                <b-input-group>
                                  <b-form-input
                                    @input="$v.endDate.$touch()"
                                    :class="{ invalid: $v.endDate.$error }"
                                    id="end-date"
                                    v-model="endDate"
                                    disabled
                                    type="text"
                                    placeholder="Select End Date"
                                    autocomplete="off"
                                  ></b-form-input>
                                  <b-input-group-append>
                                    <b-form-datepicker
                                      v-model="endDate"
                                      button-only
                                      right
                                      locale="en-US"
                                      aria-controls="end-date"
                                    ></b-form-datepicker>
                                  </b-input-group-append>
                                </b-input-group>

                            </div>
                            <button type="submit" :disabled="$v.$invalid" class="btn submit-btn">Search</button>
                        <!-- </div> -->
                    </form>
                </div>

                <router-view></router-view>

                </div>

            <!-- </div> -->
        <!-- </div> -->
    </section>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import { BIconSearch } from 'bootstrap-vue'
import {required} from 'vuelidate/lib/validators';

export default {
  name: 'Home',
  components: {
    // HelloWorld
    BIconSearch
  },
  data() {
    return {
      value: '',
      startDate: '',
      endDate: '',
    }
  },
  validations: {
    startDate: {
      required
    },
    endDate: {
      required,
      lessThan: function(val) {
        if (val === "") return true;
        let start = new Date(this.startDate).getTime();
        let end = new Date(this.endDate).getTime();
        return (end > start) ? true : false
      }
    }
  },
  methods: {
    onSearch() {
      console.log('search !');
      console.log('start > ', this.startDate, ' end > ', this.endDate);

      this.$router.push('/search?start=' + this.startDate + '&end=' + this.endDate)

    }
  }
}
</script>

<style lang="scss">
.task {
  .search-box {
    margin-top: 2rem;
    border: 1px solid #424242;
    border-radius: .25rem;
    padding: 3rem 0;

    .form-group {
      margin: 0 1rem;

      .input-group {
        margin: 0 1rem;
      }
    }

    .submit-btn {
      margin: 0 1rem;
      border: 1px solid #424242;
      padding: 0.375rem 2rem;
      transition: all .2s;
      cursor: pointer;

      &:hover {
        background-color: #424242;
        color: #FFF;
      }
    }

  }



  /* Global Styles */

  button {
    
    &:focus {
      box-shadow: none;
    }
  }

  input {

    &:focus {
      box-shadow: none;
      border: 1px solid #ced4da;
    }
  }

  .invalid {
    border: 1px solid red;
  }

}
</style>
