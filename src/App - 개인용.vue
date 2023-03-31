<template>
  <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto my-40 flex bg-white rounded items-center pt-2 flex-wrap">
    <div class="border-b basis-full py-2 px-5 flex justify-center items-center text-sm">
      <span class="basis-[20%] text-center mr-3">국내/국외 선택</span>
      <button @click="isBtn=1; t_code='04';" :class="isBtn==1? 'bg-teal-600 text-white': 'text-black'" class="text-sm border p-1 px-3 rounded hover:text-white hover:bg-teal-400 mr-3">국내</button>
      <button @click="isBtn=2; t_code='12';" :class="isBtn==2? 'bg-teal-600 text-white': 'text-black'" class="text-sm border p-1 px-3 rounded hover:text-white hover:bg-teal-400">국외</button>
      <div class="basis-[50%] text-center">
        <select v-model="t_code" class="w-[50%] border border-teal-700 px-5 py-1">
          <option v-for="e in Company" :key="e" :value="e.Code">{{ e.Name }}</option>
        </select>
      </div>
    </div>
    <div class="basis-full py-4 text-center border-b">
      <input type="text" placeholder="운송장 번호 입력. 숫자만 입력해주세요." class="border w-[80%] px-5 py-2 rounded-md outline-indigo-300" v-model="t_invoice" @input="bindNumber">
    </div>
    <div class="w-full text-center">
      <button class="w-full bg-teal-700 text-white py-3" @click="PostList()">조회하기</button>
    </div>
    <div v-if="Trackings != ''" class="w-full">
      <div class="flex flex-wrap py-10 items-center mb-5 border-b-2">
        <div class="basis-[30%] px-1">
          <span class="text-xs border rounded-xl p-1 mr-1 text-slate-500 border-slate-300">운송장번호</span>
          <p class="text-xl">{{ Trackings.invoiceNo }}</p>
        </div>
        <div class="basis-[70%] pl-5">
          <p class="text-2xl font-bold text-rose-600">
            {{ Trackings.lastDetail.kind }}
          </p>
          <p>
            {{ Trackings.lastDetail.timeString }}
            {{ Trackings.lastDetail.where }}
          </p>
          <p class="text-sm">
            <span class="mr-2">배달원</span>
            <span>{{ Trackings.lastDetail.manName }}</span>
            <span v-if="Trackings.lastDetail.manPic !== ''">( {{ Trackings.lastDetail.manPic }} )</span>
          </p>
        </div>
      </div>
      <ul>
        <li v-for="e in Trackings.trackingDetails" :key="e" class="bg-slate-50 mb-3 p-5 rounded-xl flex items-center">
          <p class="mr-5 text-lg font-bold text-center text-cyan-700 basis-[20%]">{{ e.kind }}</p>
          <div>
            <p class="text-sm font-semibold">{{ e.where }}</p>
            <p class="text-sm">{{ e.timeString }}</p>
          </div>
        </li>
      </ul>
    </div>
    <p class="text-xs text-slate-400">배송 정보 제공 : 스윗트래커 스마트택배API</p>
  </div>
</template>

<script>
// import axios from "axios";
import CarrierList from './assets/Company.json'
import Tracking from './assets/Tracking.json'
export default {
  name: 'App',
  data() {
    return {
      isBtn: 1,
      t_key: "WjnqPOL69jaWL8Ah5aeTlg",
      t_code: "04",
      t_invoice: "",
      Carriers: CarrierList,
      Trackings: []
    }
  },
  methods: {
    bindNumber(){
      return this.t_invoice = this.t_invoice.replace(/[^0-9]/g, '')  
    },
    PostList(){
      this.Trackings = Tracking;
    }
  },
  created() {
    // axios.get("https://info.sweettracker.co.kr/api/v1/companylist?t_key=WjnqPOL69jaWL8Ah5aeTlg").then((res)=>{
    //   this.Carriers = res.data.Company;
    //   console.log(this.Carriers)
    // }).catch((error)=>{
    //   console.log(error)
    // }) 보통 이렇게 불러오지만 오늘은 local로 돌릴거다!
  },
  computed:{
    Company(){
      return this.Carriers.filter((data)=>{
        if(this.isBtn == 1){
          return data.International == 'false'
        }else{
          return data.International == 'true'
        }
      })
    }
  },
  watch:{
  },
  components: {
  }
}
</script>

<style>
</style>
