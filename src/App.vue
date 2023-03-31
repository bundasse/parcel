<template>
  <div class="p-5 text-white text-lg md:text-xl xl:text-2xl flex justify-between flex-wrap bgcolor" :class="themeColor[theme] && themeColor[theme].back">
    <h3 class="basis-full md:basis-[45%] font-extrabold mb-2 md:mb-0">국내·외 택배조회 시스템</h3>
    <div class="basis-full md:basis-[45%] text-sm xl:text-base text-left md:text-right">
      <span class="mr-2">테마 : {{ theme }}</span>
      <button class="mx-1 md:mx-2 xl:mx-3 p-1 border border-white rounded-md" @click="ResetColor(), theme='default'">기본</button>
      <button class="mx-1 md:mx-2 xl:mx-3 p-1 border border-white rounded-md" @click="ResetColor(), theme='orange'">오렌지</button>
      <button class="mx-1 md:mx-2 xl:mx-3 p-1 border border-white rounded-md" @click="ResetColor(), theme='blue'">파랑</button>
      <button class="mx-1 md:mx-2 xl:mx-3 p-1 border border-white rounded-md" @click="[RandomColor(),btnRandomColor()]">랜덤</button>
    </div>
  </div>
  <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto mt-20 mb-5 flex bg-white rounded items-center pt-2 flex-wrap">
    <div class="border-b basis-full py-2 px-3 flex justify-center items-center text-sm flex-wrap">
      <span class="basis-[50%] md:basis-[20%] text-center mr-5">국내/국외 선택</span>
      <button @click="[isBtn=1,btnRandomColor()]; t_code='04';" id="dom" class="text-sm border p-1 px-2 md:px-5 rounded bgcolor mr-4" :class="[isBtn==1 && themeColor[theme] && themeColor[theme].active , themeColor[theme] && themeColor[theme].hover , isBtn==1 ? 'text-white':'text-black']">국내</button>
      <!-- isBtn이 1이고 theme가 있다면 hover로 설정한 색상을 넣으십쇼 -->
      <button @click="[isBtn=2,btnRandomColor()]; t_code='12';" id="int" class="text-sm border p-1 px-2 md:px-5 rounded bgcolor" :class="[isBtn==2 && themeColor[theme] && themeColor[theme].active , themeColor[theme] && themeColor[theme].hover , isBtn==2 ? 'text-white':'text-black']">국외</button>
      <div class="basis-[80%] md:basis-[40%] text-center mt-2 md:mt-0">
        <select v-model="t_code" class="w-[90%] border border-gray-500 px-3 xl:px-5 py-1">
          <option v-for="e in Company" :key="e" :value="e.Code">{{ e.Name }}</option>
        </select>
      </div>
    </div>
    <div class="basis-full py-4 text-center border-b">
      <input type="text" placeholder="운송장 번호 입력. 숫자만 입력해주세요." class="border w-[80%] px-5 py-2 rounded-md outline-indigo-300" v-model="t_invoice" @input="bindNumber" id="t_invoice">
    </div>
    <div class="w-full text-center">
      <button id="call" class="w-full text-white py-3 bgcolor" :class="[Trackings != ''&& themeColor[theme] && themeColor[theme].active , themeColor[theme] && themeColor[theme].back]"  @click="PostList()">조회하기</button>
    </div>
  </div>
  <p class="text-center text-2xl text-red-500 font-bold">{{ errorMsg }}</p>
  <div v-if="Trackings != ''" class="w-full">
    <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto bg-gray-200 py-5">
      <div class="flex justify-center py-3 px-5 flex-wrap items-center text-center mx-auto">
        <span class="text-md basis-[30%] font-bold mr-3 mb-5">운송장 번호</span>
        <h3 class="text-2xl basis-[45%] font-bold mb-5">{{ Trackings.invoiceNo }}</h3>
        <span class="text-md basis-[30%] font-bold mr-3">택배사</span>
        <h3 class="text-xl basis-[45%] font-bold">{{ TrackingCode[0] }}</h3>
      </div>
      <div class="w-[250px] h-[250px] pt-6 rounded-full bg-white mx-auto text-center">
        <img :src="require(`@/assets/images/ic_ani_step${Trackings.level-1}.gif`)" class="w-[150px] mx-auto mb-2" :alt="Trackings.lastDetail.kind">
        <p class="font-bold text-xl" :class="themeColor[theme] && themeColor[theme].text">{{ Trackings.lastDetail.kind }}</p>
      </div>
    </div>
    <div class="my-10 flex justify-around w-4/5 md:w-3/5 xl:w-4/12 mx-auto">
      <div class="basis-20 flex justify-center flex-wrap" v-for="level in 5" :key="level">
        <img :src="Trackings.level === level+1 ? require(`@/assets/images/ic_tropical_delivery_step${level}_on.png`) : require(`@/assets/images/ic_tropical_delivery_step${level}_off.png`)" class="w-[70%] md:w-auto">
        <p class="text-xs md:text-sm mt-2">{{ PostListName[(level-1)] }}</p>
      </div>
    </div>
    <div class="w-4/5 md:w-3/5 xl:w-4/12 mx-auto">
      <div class="pl-20 py-3 border-b-2 border-gray-100" :class="Trackings.level === e.level ? 'bg-gray-100': 'bg-transparent'" v-for="(e, index) in Trackings.trackingDetails.slice().reverse()" :key="index"> <!-- slice().reverse() 는 번호를 복사해서 거꾸로 하고 다시 넣으라는 것 -->
        <p :class="[Trackings.level === e.level && themeColor[theme] && themeColor[theme].text ,Trackings.level === e.level ? 'font-bold tcolor' : 'font-normal']">
          {{ e.where }}│{{ e.kind }}
        </p>
        <p v-if="e.telno !== ''" class="text-sm text-gray-400">
          담당자 : {{ e.telno }}
        </p>
        <p class=" text-sm text-gray-400">
          {{ e.timeString }}
        </p>
      </div>
    </div>
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
      // 테스트코드 363804378896
      Carriers: CarrierList,
      isShow: false,
      Trackings: [],
      errorMsg: '',
      PostListName: ["상품인수","상품이동중", "배송지도착","배송출발","배송완료"],
      theme: 'default',
      themeColor: {
        'default':{
          "back" : "bg-teal-700", "hover": "hover:bg-teal-100", "active": "bg-teal-500", "text": "text-teal-700"
        },
        'orange':{
          "back" : "bg-orange-600", "hover": "hover:bg-orange-200", "active": "bg-orange-400", "text": "text-orange-600"
        },
        'blue':{
          "back" : "bg-indigo-700", "hover": "hover:bg-indigo-200", "active": "bg-indigo-400", "text": "text-indigo-700"
        },
      },
      colorCode:''
    }
  },
  methods: {
    bindNumber(){
      return this.t_invoice = this.t_invoice.replace(/[^0-9]/g, '')  
    },
    PostList(){
      
      if(this.t_invoice ===''){
        this.errorMsg = '운송장 번호를 입력해주세요';
        document.querySelector("#t_invoice").focus();
        return
      }
      this.errorMsg = '';
      this.Trackings = Tracking;
      // axios.get("https://info.sweettracker.co.kr/api/v1/trackingInfo",{
      //   params:{
      //     t_code: this.t_code,
      //     t_invoice: this.t_invoice,
      //     t_key: this.t_key
      //   }
      // }).then((res)=>{
      //   console.log(res);
      //   if(res.data.code === '104'){
      //     this.errorMsg = res.data.msg
      //     }else{
      //     this.Trackings = res.data
      //     this.errorMsg = ''
      //   }
      // }).catch((error)=>{
      //   console.log(error)
      // })
    },
    ResetColor(){
      document.querySelector('.bgcolor').style.backgroundColor = ''
      document.getElementById('dom').style.backgroundColor = '' 
      document.getElementById('call').style.backgroundColor =  ''
      document.querySelector('.tcolor').style.color = ''
    },
    RandomColor(){
      this.theme = 'random';
      this.isBtn===1;
      this.colorCode  = "#" + Math.round(Math.random() * 0xffffff).toString(16)
      document.querySelector('.bgcolor').style.backgroundColor = this.colorCode
      document.getElementById('dom').style.backgroundColor = this.colorCode;
      document.getElementById('call').style.backgroundColor = this.colorCode;
      document.querySelector('.tcolor').style.color = this.colorCode
    },
    btnRandomColor(){
      if (this.isBtn=== 1 && this.theme===''){
        document.getElementById('dom').style.backgroundColor = this.colorCode;
        document.getElementById('int').style.backgroundColor = 'white';
      }else if(this.isBtn === 2 && this.theme ===''){
        document.getElementById('dom').style.backgroundColor = 'white';
        document.getElementById('int').style.backgroundColor = this.colorCode;
      }
    }
},
  created() {
    // axios.get("https://info.sweettracker.co.kr/api/v1/companylist?t_key=WjnqPOL69jaWL8Ah5aeTlg").then((res)=>{
    //   this.Carriers = res.data.Company;
    //   console.log(this.Carriers)
    // }).catch((error)=>{
    //   console.log(error)
    // })
    // 보통 이렇게 불러오지만 오늘은 local로 돌릴거다!
  },
  mounted() {
    console.log(this.Trackings)
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
    },
    TrackingCode(){
      return this.Carriers.filter((e) => {
        return e.Code === this.t_code;
      }).map(e => e.Name)
    },
  },
  watch:{
  },
  components: {
  }
}
</script>

<style>
</style>
