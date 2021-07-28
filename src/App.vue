<template>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Audiowide">
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia">
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia&effect=neon|outline|emboss|shadow-multiple">

  <div class="image">
    <img class="anh" alt="" src="./assets/image.d7265326.png" />
  </div>

  <div>
    <div class="global">
      <h1> Toàn Cầu</h1>
      <div v-if="global !== null" class="container">
        <div class="globaltotal">
          <h3>Tổng số ca:</h3>
          <p>{{new Intl.NumberFormat().format(global.TotalConfirmed)  }}</p>
        </div>

        <div class="globalrecovery">
          <h3>Số ca đã hồi phục:</h3>
          <p>{{ new Intl.NumberFormat().format(global.TotalRecovered) }}</p>
        </div>

        <div class="globaldeath">
          <h3>Số ca đã tử vong:</h3>
          <p>{{ new Intl.NumberFormat().format(global.TotalDeaths) }}</p>
        </div>
      </div>
    </div>
  </div>

  <div>
    <div class="region-selection">
      <label for="region">Chọn Châu Lục:</label>
      <select v-model="regionselected" id="region" @change="regionChange">
        <option disabled value="DEFAULT">Vui lòng chọn Châu Lục</option>
        <option value="asia">Châu Á</option>
        <option value="europe">Châu Âu</option>
        <option value="americas">Châu Mĩ</option>
        <option value="oceania">Châu Đại Dương</option>
        <option value="africa">Châu Phi</option>
      </select>
      <p>Châu Lục:{{ regionselected }}</p>
    </div>
 
    <div v-if="isregionselected" class="region-selection">
      <label for="cars">Chọn quốc gia:</label>
      <!-- Tìm Kiếm -->
      <!-- <input type="text" v-model="selected" name="cars" id="country" @change="handleChange($event)"> -->
      <select v-model="selected" name="cars" id="country" @change="handleChange($event)">
        <option disabled value="default">Vui lòng chọn Quốc Gia</option>
        <option v-for="(country,index) in countries" :value="country.alpha2Code">
          {{ country.name }} ({{ index }})
        </option>
      </select>
    </div>
  </div>

  <div v-if="selectedCountry !== null" class="global">
    <h1>{{ selectedName }}</h1>
    <div class="container">
      <div class="globaltotal">
        <h3>Tổng số ca:</h3>
        <p>{{ new Intl.NumberFormat().format(selectedCountry.TotalConfirmed) }}</p>
      </div>

      <div class="globalrecovery">
        <h3>Số ca đã hồi phục:</h3>
        <p>{{ new Intl.NumberFormat().format(selectedCountry.TotalRecovered) }}</p>
      </div>

      <div class="globaldeath">
        <h3>Số ca đã tử vong:</h3>
        <p>{{ new Intl.NumberFormat().format(selectedCountry.TotalDeaths) }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: "Covid 19",
      countries: [],
      regionselected:'DEFAULT',
      selectedRegion:null,
      isregionselected: false,
      selected:'default',
      selectedCountry: null,
      selectedID: null,
      selectedName: "",
      lastUpdated: null,
      global: null,
      byCountries: [],
    };
  },

  mounted() {
    fetch("https://api.covid19api.com/summary")
      .then((res) => {
        return res.json();
      })
      .then((res) => {
        this.lastUpdated = res.Date;
        this.global = res.Global;
        this.byCountries = res.Countries;
      });
  },

  methods: {
    handleChange(e) {
      console.log(e)
      this.selectedCountry = this.byCountries.find(
        (country) => country.CountryCode === this.selected
      );
      this.selectedID = this.byCountries.findIndex(
        (country) => country.CountryCode === this.selected
      );
      this.selectedName = this.byCountries[this.selectedID].Country

      //Tìm Kiếm
      // console.log(e)
      // this.selectedCountry = this.byCountries.find(
      //   (country) => country.Country === this.selected
      // );
      // this.selectedID = this.byCountries.findIndex(
      //   (country) => country.Country === this.selected
      // );
      // this.selectedName = this.byCountries[this.selectedID].Country
    },
    async regionChange(){
      this.isregionselected = true
      const apilink = `https://restcountries.eu/rest/v2/region/${this.regionselected}`
      console.log(apilink)
      try{
        const data = await fetch(apilink)
        const jsondata = await data.json();
        this.countries = jsondata;
        console.log(this.countries)
      }catch(e){
        console.log(e)
      }
    },
  },
};
</script>

<style scoped>
@import './assets/style/ThucTap.css';
</style>
