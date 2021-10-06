<template class="container">
<div class="center">
  <div class="input-group mb-0 mt-5 .input-sm">

    <input type="text" class="form-control" aria-label="Text input with dropdown button" v-on:input="currencyValue = $event.target.value" placeholder="0,00">

    <div class="input-group-append ">
        <button id="dropdown" class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          <img :src="images[currencyTypeIn]" style="width: 45px;">
        </button>
        <ul class="dropdown-menu">
          <li class="dropdown-item" href="#" :value="currency.value" v-for="currency in currencies" :key="currency.nome" @click="currencyTypeIn=currency.value">{{currency.nome}}</li>
      </ul>
    </div>
  </div>
  <div class="timeline mb-0 mt-0">
    <div class="dashed"></div>
    <div class="circle">
      <span>{{showValue}}</span>
    </div>
    <div class="dashed"></div>
    <div class="circle">
      <span>{{showIof}}</span>
    </div>
    <div class="dashed"></div>
    <div class="circle">
      <span>{{showFx}}</span>
    </div>
    <div class="dashed"></div>
    </div>
  <div>
  </div>
  <div class="input-group mb-3 mt-0">

    <input disable :value="converted" type="text" class="form-control" aria-label="Text input with dropdown button" v-on:input="currencyValue = $event.target.value" placeholder="0,00">

    <div class="input-group-append">
        <button id="dropdown" class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          <img :src="images[currencyTypeFor]" style="width: 45px;">
        </button>
        <ul class="dropdown-menu">
          <li class="dropdown-item" href="#" :value="currency.value" v-for="currency in currencies" :key="currency.nome" @click="currencyTypeFor=currency.value">{{currency.nome}}</li>
      </ul>
    </div>
  </div>
  <div class="flex-row position-absolute fixed-top d-flex justify-content-center mt-5">
    <span :hidden="erro" class="alert alert-danger" role="alert">Não pode converter a mesma moeda!</span>
  </div>
</div>
</template>

<script>
import brl from '../assets/brl.png'
import eur from '../assets/eur.png'
import usd from '../assets/usd.png'
export default {

    data() {

        return {
            images: {
              BRL: brl,
              EUR: eur,
              USD: usd,
            },
            currencyValue: '',
            currencyTypeIn: 'BRL',
            currencyTypeFor: 'USD',
            iofValue: 1.1,
            fxValue: 1.0,
            erro: false,
            currencies: [
                {nome: 'Real (BRL)', value:'BRL', flag: 'brl', locale: 'pt-BR'},
                {nome: 'Dolar (USD)', value:'USD', flag: 'usd.png', locale: 'en_US'},
                {nome: 'Euro (EUR)', value:'EUR', flag: 'eur.png', locale: 'de-DE'}
            ],
            fxRate:{
                    USDBRL: 5.2164,
                    EURBRL: 6.3970, 
                    EURUSD: 1.2206
            }
        }
    },

    methods:{
        calculate(iofValue, fxValue){
            let convertedValue = 0
            let fxRate = this.fxRate[this.currencyTypeFor + this.currencyTypeIn]
            let fxRateB = this.currencyTypeIn + this.currencyTypeFor
            if(fxRate){
                convertedValue = (this.currencyValue - iofValue - fxValue) / fxRate
            }else{
                fxRate = this.fxRate[fxRateB]
                convertedValue = (this.currencyValue - iofValue - fxValue) * fxRate
            }
            return [convertedValue, fxRate]
        },
        validate(){
            if(this.currencyTypeIn === this.currencyTypeFor){
                this.erro = false
                return false
            }
            this.erro = true
            return true
        },
    },

    computed:{
        // converted_value = (value - iof_value - fx_value) * fx_rate
        converted(){
            if(!this.validate()) return 0
            let iofValue = (1.1/100)*this.currencyValue
            let fxValue = (1.0/100)*this.currencyValue
            let result = new Intl.NumberFormat(this.currencies.locale, {style: 'currency', currency: this.currencyTypeFor}).format(this.calculate(iofValue, fxValue)[0])
            return result
        },
        showValue(){
          let currencyCalc = ''
          if(this.fxRate[`${this.currencyTypeIn}${this.currencyTypeFor}`]){
            currencyCalc = `${this.currencyTypeIn} 1 = ${this.currencyTypeFor} ${this.calculate(this.iofValue, this.fxValue)[1]}`
          }else{
            let value = parseFloat((1/this.calculate(this.iofValue, this.fxValue)[1]).toFixed(4))
            currencyCalc = `${this.currencyTypeIn} 1 = ${this.currencyTypeFor} ${value}`
          }

          return currencyCalc
        },
        showIof(){
          let iofCalc = `IOF (${this.iofValue}%) = ${this.currencyTypeIn} ${parseFloat((this.currencyValue/100)*this.iofValue).toFixed(1)}`

          return iofCalc
        },
        showFx(){
          let showfx = `Taxa administrativa = ${this.currencyTypeFor} ${(this.currencyValue/100)*this.fxValue}`
          
          return showfx
        }
    },
    filter: {
      currency() {
        return 'teste'
      }
    }
}
</script>


<style>
  #dropdown{
    background-color: #2e436a;
    color: white;
  }
  .center {
    margin: auto;
    width: 50%;
    padding: 10px;
}
.circle {
  display: flex;
  width: 13px;
  align-items: center;
  justify-content: end;
  height: 13px;
  background: #23344d;
  border-radius: 50%;
}

.circle span {
  position: absolute;
  color: #23344d;
  padding-left: 15px;
}

.dashed {
  height: 100px;
  border-left: 2px solid black;
}
.timeline {
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  justify-content: start;
}

.circle {
  display: flex;
  align-items: center;
  justify-content: start;
  width: 31px;
  height: 31px;
  background: #23344d;
  border-radius: 50%;
}

.circle span {
  color: white;
  font-weight: 500px;
  padding-left: 38px;
}

.dashed {
  height: 40px;
  border-left: 5px solid #23344d;
}
</style>