<template>
  <div class="search-results">
    <div class="row">
      <div class="col-4"></div>
      <div class="col-8">
        <div class="head">
          <div class="total">
            <span>Total Nights: {{ totalNights }}</span>
          </div>
          <div class="sort">
            <button @click="sortByName" class="btn sort-btn">Sort by Name</button>
            <button @click="sortByPrice" class="btn sort-btn">Sort by price</button>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-4">
        <div class="side-filter">
          <div class="search-field">
            <div class="input-group">
              <div class="input-group-prepend">
                <span class="input-group-text" id="basic-addon1"
                  ><b-icon-search></b-icon-search
                ></span>
              </div>
              <input
                @keyup="searchByName"
                type="text"
                v-model="hotelName"
                class="form-control"
                placeholder="Hotel Name"
                aria-label="Username"
                aria-describedby="basic-addon1"
              />
            </div>
          </div>
          <div class="range-field">
            <label for="price-range">Price Filter</label>
            <b-form-input
                @change="filterByPrice"
              id="price-range"
              v-model="price"
              type="range"
              :min="minPrice"
              :max="maxPrice"
              step="1"
            ></b-form-input>
          </div>
        </div>
      </div>

      <div class="col-8">
        <div class="hotels-results">
          <div class="row" v-if="hotels.length > 0">
            <div class="col-6" v-for="(hotel, index) in hotels" :key="index">
                <app-single-hotel :hotel="hotel"></app-single-hotel>
            </div>
          </div>
            <div v-else class="not-found">
                <p>No results found</p>
            </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import SingleHotel from '@/components/SingleHotel'

export default {
    components: {
        appSingleHotel: SingleHotel
    },
  data() {
    return {
      price: 2,
      hotelsResults: [],
      availableHotels: [],
      hotels: [],
      hotelName: '',

      minPrice: 0,
      maxPrice: 0
    };
  },
  computed: {
      totalNights() {
          let startInMilliSeconds = new Date(this.$route.query.start).getTime();
          let endInMilliSeconds = new Date(this.$route.query.end).getTime();
          let difference = endInMilliSeconds - startInMilliSeconds;
          let total = difference / 1000 / 60 / 60 / 24;
          return total;
      }
    },
    watch: {
        '$route.query'(oldVal, newVal) {
            this.getHotels();
        }
    },
    created() {
        this.getHotels();
    },
    methods: {
        async getHotels() {
            try {

                let hotelsResultsData = await axios.get('https://run.mocky.io/v3/90e1d920-afca-4101-8a97-9097310d7e8a');
                this.hotelsResults = hotelsResultsData.data;
                //get hotels by the specified start and end dates
                this.getHotelsBySpecifiedDates();
                this.getTotalNights();
                this.getMinAndMaxPrice();

            } catch (error) {
                console.log('error occured > ', error);
            }
        },
        getHotelsBySpecifiedDates() {
            let startInMilliSeconds = new Date(this.$route.query.start).getTime();
            let endInMilliSeconds = new Date(this.$route.query.end).getTime();
            this.hotels = this.availableHotels = this.hotelsResults.filter(hotel => {
                //multiply total nights by hotel price
                hotel.price = hotel.price * this.totalNights;
                
                let availableDate = new Date(hotel.available_on).getTime()
                return (availableDate >= startInMilliSeconds) && (availableDate <= endInMilliSeconds);
            });

        },
        getTotalNights() {
            let startInMilliSeconds = new Date(this.$route.query.start).getTime();
            let endInMilliSeconds = new Date(this.$route.query.end).getTime();
            let difference = endInMilliSeconds - startInMilliSeconds;
        },
        searchByName() {
            this.hotels = this.availableHotels.filter(hotel => {
                let reg = new RegExp(this.hotelName, 'gi')
                return hotel.name.match(reg);
            });
        },
        sortByName() {
            if(this.hotels.length == 0)
                return;

            this.hotels.sort((ele1, ele2) => (ele1.name > ele2.name) ? 1 : -1)
        },
        sortByPrice() {
            if(this.hotels.length == 0)
                return;
            this.hotels.sort((ele1, ele2) => ele1.price - ele2.price)
        },
        getMinAndMaxPrice() {
            if(this.availableHotels.length == 0)
                return;
            
            let priceLimits = [...this.availableHotels];
            priceLimits.sort((ele1, ele2) => ele1.price - ele2.price);
            this.minPrice = priceLimits[0].price;
            this.maxPrice = priceLimits[priceLimits.length - 1].price;
        },
        filterByPrice() {
            if(this.availableHotels.length == 0)
                return;
            this.hotels = this.availableHotels.filter(hotel => {
                return hotel.price <= this.price
            })
        }
    }
};
</script>

<style scoped lang="scss">
.search-results {
  margin-top: 2rem;
  border: 1px solid #424242;
  border-radius: 0.25rem;
  padding: 3rem 1rem;

  .head {
    color: #424242;
    display: flex;

    .total {
      font-weight: 600;
      align-self: flex-end;
    }

    .sort {
      margin-left: auto;

      .sort-btn {
        border: 1px solid #424242;
        padding: 0.375rem 1rem;

        &:hover {
            background-color: #424242;
            color: #FFF;
        }

        &:not(:last-of-type) {
          margin-right: 0.5rem;
        }
      }
    }
  }

  .side-filter {
    .range-field {
      margin-top: 1.5rem;
    }
  }

  .hotels-results {

      .not-found {
          min-height: 50vh;
          display: flex;
          justify-content: center;
          align-items: center;
      }
  }
}
</style>
