<template>
  <div class="container-fluid py-4">
    <div class="row">
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4" v-for="(data, index) in highlight" :key="index">
        <div class="card">
          <div class="card-body p-3">
            <div class="row">
              <div class="col-8">
                <div class="numbers">
                  <p class="text-sm mb-0 text-capitalize font-weight-bold">{{ data.name }}</p>
                  <h5 class="font-weight-bolder mb-0">
                    {{ data.priceUsd }}
                  </h5>
                  <span
                    :class="[
                      data.increase ? 'text-success' : 'text-danger',
                      'text-sm',
                      'font-weight-bolder'
                    ]"
                    >{{ data.changePercent24Hr }}</span
                  >
                </div>
              </div>
              <div class="col-4 text-end">
                <div
                  class="icon icon-shape bg-gradient-primary shadow text-center border-radius-md"
                >
                  <i class="ni ni-money-coins text-lg opacity-10" aria-hidden="true"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row mt-4">
      <div class="col-12">
        <div class="card mb-4">
          <div class="card-header pb-0 d-flex justify-content-between">
            <h6>Cryptocurrencies</h6>
            <div>
              <button class="btn btn-outline-primary btn-sm mb-0 mx-2" @click="prevData">
                <i class="fas fa-solid fa-chevron-left"></i>
              </button>
              <button class="btn btn-outline-primary btn-sm mb-0" @click="nextData">
                <i class="fas fa-solid fa-chevron-right"></i>
              </button>
            </div>
          </div>
          <div class="card-body px-0 pt-0 pb-2">
            <div class="table-responsive p-0">
              <table class="table align-items-center justify-content-center mb-0">
                <thead>
                  <tr>
                    <th class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7">
                      no
                    </th>
                    <th
                      class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7 ps-2"
                    >
                      name
                    </th>
                    <th
                      class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7 ps-2"
                    >
                      symbol
                    </th>
                    <th
                      class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7 ps-2"
                    >
                      supply/max supply
                    </th>
                    <th
                      class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7 ps-2"
                    >
                      usd
                    </th>
                    <th
                      class="text-uppercase text-secondary text-xs font-weight-bolder opacity-7 ps-2"
                    >
                      24 hr
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(data, index) in dataTable" :key="index">
                    <td>
                      <p class="text-sm font-weight-bold mb-0 px-2">{{ data.rank }}.</p>
                    </td>
                    <td>
                      <span class="text-xs font-weight-bold">{{ data.name }}</span>
                    </td>
                    <td>
                      <span class="text-uppercase text-xs font-weight-bold">{{ data.symbol }}</span>
                    </td>
                    <td>
                      <span class="text-uppercase text-xs font-weight-bold">{{
                        `${data.supply}/${data.maxSupply}`
                      }}</span>
                    </td>
                    <td>
                      <span class="text-xs font-weight-bold">{{ data.priceUsd }}</span>
                    </td>
                    <td>
                      <span
                        :class="[
                          'text-xs',
                          'font-weight-bold',
                          data.increase ? 'text-success' : 'text-danger'
                        ]"
                        >{{ `${data.increase ? '+' : ''}${data.changePercent24Hr}` }}</span
                      >
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import numeral from 'numeral'
import axios from 'axios'
export default {
  data() {
    return {
      limit: 5,
      page: 1,
      totalPage: 0,
      highlight: [],
      dataTable: [],
      dataTableMaster: []
    }
  },
  computed: {
    getFullYear() {
      return new Date().getFullYear()
    }
  },
  mounted() {
    this.getData()
  },
  methods: {
    formatNumber(val = null) {
      if (val) {
        return numeral(val).format('0,0.00')
      }
    },
    renderData() {
      this.dataTable = this.dataTableMaster.filter(
        (data, index) => index >= (this.page - 1) * this.limit && index < this.page * this.limit
      )
    },

    nextData() {
      if (this.page !== this.totalPage) {
        this.page++
        this.renderData()
      }
    },
    prevData() {
      if (this.page > 1) {
        this.page--
        this.renderData()
      }
    },
    async getData() {
      const vm = this
      // await axios.get(`https://komgrip.co.th/coincap/assets?limit=${this.limit}`)
      await axios
        .get(`https://komgrip.co.th/coincap/assets?rank`)
        .then(function (response) {
          if (response.data) {
            vm.dataTableMaster = response.data.data.map((data) => {
              const fsc = data.changePercent24Hr.substring(0, 1)
              return {
                id: data.id,
                rank: data.rank,
                symbol: data.symbol,
                name: data.name,
                supply: data.supply ? numeral(data.supply).format('0,0') : 'no limit',
                maxSupply: data.maxSupply ? numeral(data.maxSupply).format('0,0') : 'no limit',
                marketCapUsd: data.marketCapUsd
                  ? numeral(data.marketCapUsd).format('0,0')
                  : 'no limit',
                volumeUsd24Hr: data.volumeUsd24Hr,
                priceUsd: data.priceUsd ? numeral(data.priceUsd).format('$0,0.00') : 'no limit',
                changePercent24Hr: data.changePercent24Hr
                  ? numeral(data.changePercent24Hr).format('0%')
                  : '',
                vwap24Hr: data.vwap24Hr ? numeral(data.vwap24Hr).format('0%') : '',
                increase: fsc === '-' ? false : true,
                explorer: data.explorer
              }
            })
          }
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
        .finally(function () {
          // always executed
          const highlightMaster = ['bitcoin', 'ethereum', 'solana', 'dogecoin']
          vm.totalPage = Math.ceil(vm.dataTableMaster.length / vm.limit)
          // vm.highlight = vm.dataTableMaster.filter((data) => data.rank <= 4)
          vm.highlight = vm.dataTableMaster.filter((data) => highlightMaster.includes(data.name.toLowerCase())).sort((a, b) => a.rank - b.rank);
          vm.renderData()
        })
    }
  }
}
</script>
