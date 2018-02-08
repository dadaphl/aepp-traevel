<template>
  <div class='index-screen screen'>
    <header>
      <div class="grid">
        <div>
          <h2>You are in <strong>{{myLocation}}</strong>,</h2>
          <p class='popular'>
            Change to:
            <a @click='myLocation = l' v-for='l in startLocations'>
              {{l}}
            </a>
          </p>
          <h1>where do you want to travel?</h1>
          <p>Popular destinations:
          <span class='popular'>
            <a @click='search(l)' v-for='l in locations'>
              {{l}}
            </a>
          </span>
          </p>
        </div>
        <div class='search'>
          <input class='search-input' placeholder='I want to go to, eg. Berlin' v-model='myDestinaton' type="text">
          <button class='search-button' @click='search(searchString)'>
            <img src="@/assets/search.svg" alt=""/>
          </button>
        </div>
      </div>

    </header>
    <h1>
      <span v-if='myDestinaton ===""'>All</span>
      Offers
      from <strong>{{myLocation}}</strong>
      <span v-if='myDestinaton !==""'>
        <template v-if="desiredDestination">
          to <strong>{{myDestinaton}}</strong>
        </template>
      </span>
    </h1>
    <div v-if='desiredDestination && desiredDestination.length' class="offers">
      <ae-panel v-for='m in desiredDestination'>
      <div class='offer grid'>
        <ae-identity-avatar :address='m.data.from_acc'/>
        <div class='offer-from'>
          {{ m.data.from_acc | startAndEnd(20,20)}}
        </div>
      </div>
      <div class='offer grid'>
        <div class='data'>
          <div class='data-field'>from</div>
          <div class='data-value'>{{ m.data.from }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>to</div>
          <div class='data-value'>{{ m.data.to }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>date</div>
          <div class='data-value'>{{ m.data.date | humanDate }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>travel time</div>
          <div class='data-value'>{{ m.data.travel_time }} hours</div>
        </div>
        <div class='data'>
          <div class='data-field'>free seats</div>
          <div class='data-value'>{{ m.data.capacity }}</div>
        </div>
      </div>
      <div class='offer grid bottom'>
        <div class='data'>
          <div class='data-field'>asking price</div>
          <div class='price data-value'>{{ m.data.price }} AE</div>
        </div>
        <div class='data demand-price'>
          <div class='data-field'>you pay</div>
          <div class='data-value'>
            <input type="number" :value='m.data.price' name="" id=""/>
            AE</div>
        </div>
        <ae-button type='dramatic' @click='book(m)'>Order</ae-button>
      </div>
      </ae-panel>
    </div>
    <div v-else>
      <h2>Sorry, no offers for <strong>{{myDestinaton}}</strong>. Try offers to:</h2>
      <div class='popular'>
        <a @click='search(l)' v-for='l in locations'>
          {{l}}
        </a>
      </div>
    </div>
    <h1>My Demand</h1>
    <div v-if='myDemand && myDemand.length' class="offers">
      <ae-panel v-for='m in myDemand'>
      <div class='offer grid'>
        <div class='data'>
          <div class='data-field'>from</div>
          <div class='data-value'>{{ m.data.from }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>to</div>
          <div class='data-value'>{{ m.data.to }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>date</div>
          <div class='data-value'>{{ m.data.date | humanDate }}</div>
        </div>
        <div class='data'>
          <div class='data-field'>travel time</div>
          <div class='data-value'>{{ m.data.travel_time }} hours</div>
        </div>
        <div class='data'>
          <div class='data-field'>price</div>
          <div class='data-value'>{{ m.data.price }} AE</div>
        </div>
      </div>
      </ae-panel>
    </div>
    <div v-else>
      <h2>No Demand yet</h2>
    </div>

    <h1>
      My Trips
    </h1>
    <div v-if='myMatches && myMatches.length' class='matches'>
      <ae-panel v-for='m in myMatches'>
      <div>
        <div class='offer grid'>
          <h3>Seller</h3>
          <ae-identity-avatar :address='m.data.from_acc'/>
          <div class='offer-from'>
            {{ m.data.from_acc | startAndEnd(20,20)}}
          </div>
        </div>
        <div class="offer grid">
          <div class='data'>
            <div class='data-field'>from</div>
            <div class='data-value'>{{ m.offer[0].data.from }}</div>
          </div>
          <div class='data'>
            <div class='data-field'>to</div>
            <div class='data-value'>{{ m.offer[0].data.to }}</div>
          </div>
          <div class='data'>
            <div class='data-field'>date</div>
            <div class='data-value'>{{ m.offer[0].data.date | humanDate }}</div>
          </div>
          <div class='data'>
            <div class='data-field'>travel time</div>
            <div class='data-value'>{{ m.offer[0].data.travel_time }} hours</div>
          </div>
          <div class='data'>
            <div class='data-field'>price</div>
            <div class='data-value'>{{ m.offer[0].data.price }} AE</div>
          </div>
        </div>
        <div class='offer grid'>
          <h3>Buyer</h3>
          <ae-identity-avatar :address='m.data.to_acc'/>
            <div class='offer-from'>
              {{ m.data.to_acc | startAndEnd(20,20)}}
            </div>
        </div>
      </div>
      </ae-panel>
    </div>
    <h2 v-else>
      No trips yet
    </h2>
    <div class="mykey">
      <ae-identity-avatar :address='myKey'/>
      <span>
      {{myKey | startAndEnd}}
      </span>
      <strong>
        {{myBalance}}
        AE
      </strong>
    </div>
  </div>
</template>

<script>
import {
  AeAddressInput,
  AeBanner,
  AeAmount,
  AeInput,
  AeDivider,
  AeAmountInput,
  AeButton,
  AeLabel,
  AeFilterItem,
  AeNotification,
  AeFilterList,
  AeFilterSeparator,
  AeHeader,
  AeHeaderButton,
  AeIdentity,
  AeIdentityAvatar,
  AeMain,
  AeModal,
  AeOverlay,
  AePanel,
  AeSwitch,
  AeAppIcon,
  AeAddress,
  AeIcon,
  AeBadge,
  AeLoader
} from '@aeternity/aepp-components'
export default {
  data () {
    return {
      searchString: '',
      myLocation: 'Munich',
      myDestinaton: '',
      apiMarket: null,
      apiInfo: null,
      apiBalance: null
    }
  },
  components: {
    AeBanner,
    AeAmount,
    AeInput,
    AeDivider,
    AeAmountInput,
    AeButton,
    AeLabel,
    AeFilterItem,
    AeFilterList,
    AeFilterSeparator,
    AeHeader,
    AeNotification,
    AeHeaderButton,
    AeIdentity,
    AeIdentityAvatar,
    AeMain,
    AeModal,
    AeOverlay,
    AePanel,
    AeSwitch,
    AeAppIcon,
    AeAddress,
    AeAddressInput,
    AeIcon,
    AeBadge,
    AeLoader
  },
  computed: {
    myKey () {
      if (!this.apiInfo) return null
      return this.apiInfo.public_key
    },
    myBalance () {
      if (!this.apiInfo) return null
      return this.apiBalance.balance
    },
    matches () {
      if (!this.apiMarket) return null
      return this.apiMarket
        .filter(m => m.data.offer_hash && m.data.demand_hash)
        .map(m => Object.assign({}, m, {offer: this.offers.filter(o => o.hash === m.data.offer_hash)}))
    },
    myMatches () {
      if (!this.matches) return null
      // to_acc correct?
      return this.matches.filter(m => m.data.to_acc === this.myKey)
    },
    validMarket () {
      if (!this.apiMarket) return null
      return this.apiMarket
        .filter(m =>
          m.data.type &&
          m.data.from &&
          m.data.to &&
          m.data.price &&
          m.data.travel_time
        )
        .sort((a, b) => a.data.date - b.data.date)
    },
    offers () {
      if (!this.validMarket) return null
      return this.validMarket.filter(m => m.data.type === 'offer')
    },
    demand () {
      if (!this.validMarket) return null
      return this.validMarket.filter(m => m.data.type === 'demand')
    },
    myDemand () {
      if (!this.demand) return null
      return this.demand
        .filter(d => d.data.from_acc === this.myKey)
        .map(d => Object.assign({}, d, {match: this.matches.filter(m => m.data.demand_hash === d.hash)}))
        .filter(d => d.match.length === 0)
    },
    future () {
      if (!this.offers) return null
      return this.offers.filter(m => m.data.date * 1000 > new Date())
    },
    fromMyLocations () {
      if (!this.future) return null
      return this.future.filter(m => m.data.from === this.myLocation)
    },
    desiredDestination () {
      if (!this.fromMyLocations) return null
      return this.fromMyLocations.filter(m => m.data.to.match(`.*${this.myDestinaton}.*`))
    },
    startLocations () {
      if (!this.future) return null
      return this.future.map(m => m.data.from).filter((value, index, self) => self.indexOf(value) === index)
    },
    locations () {
      if (!this.fromMyLocations) return null
      return this.fromMyLocations.map(m => m.data.to).filter((value, index, self) => self.indexOf(value) === index)
    }
  },
  methods: {
    search (searchStr) {
      this.myDestinaton = searchStr
    },
    book (m) {
      this.$http.post('market/demand', {
        price: m.data.price,
        date: m.data.date,
        capacity: 1,
        travel_time: m.data.travel_time,
        from: m.data.from,
        to: m.data.to
      }).then(resp => {
        if (resp.body.ok) {
          alert(resp.body.ok)
        }
      }, resp => {
      })
    },
    getData () {
      this.$http.get('market').then(resp => {
        this.apiMarket = resp.body
      }, resp => {
      })
      this.$http.get('info').then(resp => {
        this.apiInfo = resp.body
        this.$http.get(`balance/${this.myKey}`).then(resp => {
          this.apiBalance = resp.body
        }, resp => {
        })
      }, resp => {
      })
    }
  },
  created () {
    this.getData()
    setInterval(this.getData, 1000)
  }
}
</script>
<style src='./index.scss' lang='scss' />
