<template>
    <div>
        <div class="currectCity">
            <h1>{{CurrentData.cityName}}</h1>
            <h2>{{CurrentData.temperature}}<span>º C</span>
            </h2>
            <p>{{CurrentData.mainType}}</p>
            <ul type="none" class="moreDesc">
                <li>
                    <b>Feels Like</b> - {{CurrentData.temperature}}º</li>
                <li>
                    <b>Wind speed</b> - {{CurrentData.windSpeed}} kmph</li>
                <li>
                    <b>Barometer </b> - {{CurrentData.barometer}} mb</li>
                <li>
                    <b>See Level </b> - {{CurrentData.sea_level}} m</li>
                <li>
                    <b>Humidity</b> - {{CurrentData.humidity}}%</li>
                <li>
                    <b>Precipitation volume for last 3 hours</b> - {{CurrentData.rainH}}mm</li>
            </ul>
        </div>
        <div class="dropDownSelect" ref="posLeftParent">
          <span class="borderBottom" :style="{width:PosWidth+'px', left:PosLeft+'px', opacity:opacity}"></span>
          <ul type="none">
            <li ref="active" @click="filterDays('today', $event.target)">Today</li>
            <li @click="filterDays('5', $event.target)">5 Day</li>
          </ul>
        </div>
        <div class="swiperSlide" ref="widthMainSlider">
            
            <ul class="chevronsSlide">
                <li>
                    <span class="button-prev lnr lnr-chevron-left" @click="sliderLeft()"></span>
                </li>
                <li>
                    <span class="button-next lnr lnr-chevron-right" @click="sliderRight()"></span>
                </li>
            </ul>
            <div class="main_Slides" ref="MainSlider" :style="{height:heightBadge+'px', width:commonBadgesCount*SliderItemsWidth+'px', transform: 'translate('+(SliderTranslate)+'px)'}">
                <template v-for="(item, index) of changesData">
                  <div :class="{'activeItem':addClassSelectedItem}" class="slideWrap" ref="HeightBadge" v-if="index < commonBadgesCount" :style="{width:SliderItemsWidth+'px'}" @click="showMoreWeatherBottom(index)">
                    <div class="borderWrap">
                      <div class="dateRow">
                        <p v-if="item.multipleDay" class="multiDay">{{item.dt_txt.split(" ")[0]}}</p>
                        <p v-else>{{item.dt_txt.split(" ")[0]}}</p>
                        <h3 v-if="!item.multipleDay">{{item.dt_txt.split(" ")[1].split(":").splice(0, item.dt_txt.split(" ")[1].split(":").length-1).join(":")}}</h3>
                        <p v-if="!item.multipleDay">
                          <img v-bind:src="'//openweathermap.org/img/w/' + item.weather[0].icon+'.png'"  alt="">
                        </p>
                        <p v-else class="multiDayIcon">
                          <img v-bind:src="'//openweathermap.org/img/w/' + item.dayIcon+'.png'"  alt="">
                          <img v-if="item.nightIcon" v-bind:src="'//openweathermap.org/img/w/' + item.nightIcon+'.png'"  alt="">
                        </p>
                      </div>
                      <div class="bottomCelsius" v-if="item.multipleDay">
                        <p class="multiDayTemp">
                          <span v-if="item.dayTemp" class="day">{{Math.round(item.dayTemp-273.15)}}º</span>
                          <span  v-if="item.nightTemp" class="night">{{Math.round(item.nightTemp-273.15)-2}}º</span>
                        </p>
                        <h4 class="multiDayWeatherDesc">
                          <span v-if="item.dayWeather">{{item.dayWeather}}</span>
                          <span v-if="item.nightWeather">{{item.nightWeather}}</span>
                        </h4>
                      </div>
                      <div v-else class="bottomCelsius" >
                        <p>
                          <span class="day">{{Math.round(item.main.temp-273.15)}}º</span>
                        </p>
                        <h4>{{item.weather[0].description}}</h4>
                      </div>
                      
                    </div>
                  </div>
                </template>
            </div>
        </div>
        <a v-if="hideSecondParts"  class="clickMore">Click each Item for view more</a>
        <div v-if="BooleanMultipleItemWeatherClick" class="moreDescMultipleDay">
          <h4>{{headerMoreWeather}}</h4>
          <ul type="none">
            <li>
              <b>Clock</b>
              <span>Icon</span>
              <span>Tremperature</span>
              <span>Humidity</span>
              <span>Weather Desc.</span>
              <span>Wind Speed</span>
            </li>
            <li v-for="(item, index) of bottomArrayData">
              <b>{{item.dt_txt.split(" ")[1].split(":").splice(0, item.dt_txt.split(" ")[1].split(":").length-1).join(":")}}</b>
              <span> <img v-bind:src="'//openweathermap.org/img/w/' + item.weather[0].icon+'.png'"  alt=""></span>
              <span>{{Math.round(item.main.temp-273.15)}}º</span>
              <span>{{item.main.humidity}}%</span>
              <span>{{item.weather[0].description}}</span>
              <span>{{item.wind.speed}}kmph</span>
            </li>
          </ul>
        </div>
    </div>
</template>
<script>

export default {
  name: "SelectedCity",
  data() {
    return {
      CurrentData: {},
      currTime:
        Math.round(new Date().getTime() / 1000.0) +
        -new Date().getTimezoneOffset() / 60 * 3600,
      allListHour:[],
      changesData:[],
      allTimes:[],
      SliderItemsWidth:"auto",
      SliderTranslate:0,
      MainSliderWrapWidth:0,
      badgesCountShows:5,
      commonBadgesCount:0,
      heightBadge:0,
      slideIndex:0,
      PosWidth:0,
      PosLeft:0,
      opacity:0,
      commonData:[],
      hideMoreWeatherBottom:false,
      hideSecondParts:false,
      BooleanMultipleItemWeatherClick:false,
      headerMoreWeather:"",
      filteredAllData:[],
      bottomArrayData:[],
      addClassSelectedItem:false
    };
  },
  props: {},

  methods: {
    setData: function(data) {
      // Border Bottom Style and position animating
      this.PosWidth = this.$refs.active.getBoundingClientRect().width;
      this.PosLeft = this.$refs.active.getBoundingClientRect().left -this.$refs.posLeftParent.getBoundingClientRect().left;
      // _________________________________________

      this.commonData = data;
      this.MainSliderWrapWidth = this.$refs.widthMainSlider.getBoundingClientRect().width;
      
      this.CurrentData = {};
      this.allListHour = [];
      Array.prototype.map.call(data.list, (obj, indexList)=>{
          if(this.currTime < data.list[indexList].dt + (3*3600)){
            this.allListHour.push(data.list[indexList])
          }
      })
      this.changesData = this.allListHour;
      this.commonBadgesCount = Math.round(this.changesData.length/5) 
      this.CurrentData = {
          cityName: data.city.name,
          timeAll: {},
          set currentTime(time) {
            Array.prototype.map.call(data.list, (elem, ind) => {
              if (time > data.list[ind].dt) {
                this.timeAll = {
                  origDT: time,
                  objDT: data.list[ind].dt,
                  objDTTxt: data.list[ind].dt_txt
                };
                this.wind = data.list[ind].wind;
                this.weather = data.list[ind].weather[0];
                this.main = data.list[ind].main;

                this.rain = data.list[ind].rain;
                this.sys = data.list[ind].sys;
                this.temperature = Math.round(this.main.temp-273.15);
                this.humidity = this.main.humidity;
                this.barometer = this.main.pressure;
                this.sea_level = this.main.sea_level;
                this.rainH = (this.rain != undefined && this.rain.hasOwnProperty("3h"))?this.rain["3h"].toFixed(2):"--";
                this.mainType = data.list[ind].weather[0].main;
                this.windSpeed = data.list[ind].wind.speed;
                return;
              }
            });
          }
        };
      this.CurrentData.currentTime = this.currTime;
      setTimeout(() => {
        this.heightBadge = this.$refs.HeightBadge[0].getBoundingClientRect().height
      }, 100);
      
      this.hideMoreWeatherBottom = false;
      this.hideSecondParts = this.hideMoreWeatherBottom;
      this.BooleanMultipleItemWeatherClick = false;
      this.addClassSelectedItem = false;
      setTimeout(()=>{
          this.badgeCount ()
          this.slideIndex = this.MainSliderWrapWidth/this.SliderItemsWidth;
      }, 10)
    },

    sliderLeft(){
   
      if(this.slideIndex > this.MainSliderWrapWidth/this.SliderItemsWidth){
        this.slideIndex--;
        this.SliderTranslate += this.SliderItemsWidth;
      }
      else{
        this.SliderTranslate = 0;
        this.$refs.MainSlider.style.marginLeft= "20px";
        setTimeout(()=>{
          this.$refs.MainSlider.style.marginLeft= "0px";
        }, 400);
      }
    },
    
    sliderRight(){
      this.MainSliderWrapWidth = this.$refs.widthMainSlider.getBoundingClientRect().width;
       console.log(this.slideIndex, this.commonBadgesCount)
      if(this.slideIndex < this.commonBadgesCount){
        this.slideIndex++;
        this.SliderTranslate-=this.SliderItemsWidth;
      }
      else{
        this.$refs.MainSlider.style.marginLeft= "-20px";
        setTimeout(()=>{
          this.$refs.MainSlider.style.marginLeft= "0px";
        }, 400);
      }
    },

    badgeCount (){
       this.MainSliderWrapWidth = this.$refs.widthMainSlider.getBoundingClientRect().width;
        if (window.matchMedia("(min-width: 991px)").matches) {
          this.badgesCountShows = this.MainSliderWrapWidth/5
          this.SliderItemsWidth = this.badgesCountShows;
        }
        if (window.matchMedia("(min-width: 767px) and (max-width:991px)").matches) {
          this.badgesCountShows = this.MainSliderWrapWidth/4
          this.SliderItemsWidth = this.badgesCountShows;
        }
        if (window.matchMedia("(min-width: 576px) and (max-width:767px)").matches) {
          this.badgesCountShows = this.MainSliderWrapWidth/3
          this.SliderItemsWidth = this.badgesCountShows;
        }
        if (window.matchMedia("(min-width: 480px) and (max-width:576px)").matches) {
          this.badgesCountShows = this.MainSliderWrapWidth/2
          this.SliderItemsWidth = this.badgesCountShows;
        }
        if (window.matchMedia("(max-width: 480px)").matches) {
          this.badgesCountShows = this.MainSliderWrapWidth/1
          this.SliderItemsWidth = this.badgesCountShows;
        }
        
    },

    filterDays(dayCount, th){
      if(dayCount == "today"){
        this.changesData = this.allListHour
        this.commonBadgesCount = Math.round(this.changesData.length/5);
        this.hideMoreWeatherBottom = false;
        this.hideSecondParts = false;
        this.BooleanMultipleItemWeatherClick = false;
        this.addClassSelectedItem = false;
      }
      else{
          
        let arrayDay = [], 
            firstDateArr = [], 
            Dayresult = [],
            badgeResult = [];

        function createObj(n, nightIndex, dayIndex){
          badgeResult.push({
              multipleDay:true,
              dt_txt:arrayDay[n][dayIndex].dt_txt.split(" ")[0],
              dayTemp: arrayDay[n][dayIndex].main.temp,
              dayIcon: arrayDay[n][dayIndex].weather[0].icon,
              dayWeather: arrayDay[n][dayIndex].weather[0].main,
              
              nightTemp: arrayDay[n][nightIndex].main.temp,
              nightIcon: arrayDay[n][nightIndex].weather[0].icon,
              nightWeather: arrayDay[n][nightIndex].weather[0].main,
          })
        }

        for(let i = 0; i<this.commonData.list.length; i++){
            firstDateArr.push(this.commonData.list[i].dt_txt.split(" ")[0])
        }

        firstDateArr.forEach(function(item) {
            if(Dayresult.indexOf(item) < 0) {
                Dayresult.push(item);
            }
        });

        for(let i = 0; i < Dayresult.length; i++){
          arrayDay.push([])
          for(let j = 0; j<this.commonData.list.length; j++){
            if(Dayresult[i].split(" ")[0] == this.commonData.list[j].dt_txt.split(" ")[0]){
              arrayDay[i].push(this.commonData.list[j])
            } 
          }
        }

        for(let l = 0; l< arrayDay.length; l++){
            if(arrayDay[l].length == 8){
              createObj(l, 0, 3)
            }
            else if(arrayDay[l].length >= 4){
              createObj(l, (arrayDay[0].length - 1), 3)
            }
            else{
              let sysN = 0, sysD = 0, sysRes;
              for(let nd = 0; nd < arrayDay[l].length; nd++){
                if(arrayDay[l][nd].sys.pod == "n"){
                  sysN++
                }
                else if(arrayDay[l][nd].sys.pod == "d"){
                  sysD++
                }
              }
              sysRes = (sysN > sysD)?sysN:sysD;
              badgeResult.push({
                multipleDay:true,
                dt_txt:arrayDay[l][sysRes-1].dt_txt.split(" ")[0],
                dayTemp: arrayDay[l][sysRes-1].main.temp,
                dayIcon: arrayDay[l][sysRes-1].weather[0].icon,
                dayWeather: arrayDay[l][sysRes-1].weather[0].main,
              })
            }
        }

        this.filteredAllData = arrayDay; 
        this.changesData = badgeResult;
        this.commonBadgesCount = this.changesData.length
        this.hideMoreWeatherBottom = true;
        this.hideSecondParts = this.hideMoreWeatherBottom;
        this.addClassSelectedItem = true;
      }
      this.PosWidth = th.getBoundingClientRect().width;
      this.PosLeft = th.getBoundingClientRect().left -this.$refs.posLeftParent.getBoundingClientRect().left;
      this.SliderTranslate = 0;
      
      setTimeout(()=>{
        this.badgeCount ()
         this.slideIndex = this.MainSliderWrapWidth/this.SliderItemsWidth;
      }, 100)
    },
    showMoreWeatherBottom(indexItem){
      if(this.hideMoreWeatherBottom){
        Array.prototype.map.call(this.$refs.HeightBadge, (elem)=>{
          elem.classList.remove("activeShow")
        })
        this.$refs.HeightBadge[indexItem].classList.add("activeShow");
        this.headerMoreWeather = this.changesData[indexItem].dt_txt
        this.hideSecondParts = false;
        this.BooleanMultipleItemWeatherClick = true
        this.bottomArrayData = this.filteredAllData[indexItem]
        setTimeout(()=>{
          this.badgeCount ()
        }, 10)
      }
    }

  },
  mounted() {
    this.PosWidth = this.$refs.active.getBoundingClientRect().width;
    this.PosLeft = this.$refs.active.getBoundingClientRect().left;
    this.opacity = 1;
		this.$nextTick(function() {
				window.addEventListener('resize', ()=>{
          this.SliderTranslate = 0;
          this.MainSliderWrapWidth = this.$refs.widthMainSlider.getBoundingClientRect().width;
          this.badgeCount();
           this.slideIndex = this.MainSliderWrapWidth/this.SliderItemsWidth;
        });
		})
	},
  created: function() {
    this.$parent.$on("updateResponse", this.setData);
  }
};

</script>
<style lang="scss">
.currectCity {
  text-align: center;
  padding: 60px 20px 20px;
  & > p {
    font-size: 25px;
  }
  h2 {
    font-size: 60px;
    & span {
      font-size: 26px;
      position: relative;
      top: -25px;
      left: 3px;
    }
  }
  .moreDesc {
    max-width: 650px;
    margin: auto;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    padding: 10px 0;
    li {
      padding: 2px 5px;
    }
  }
}
 .dropDownSelect {
    max-width: 1600px;
    margin: auto;
    padding: 0 10px;
    position: relative;
    .borderBottom{
      position: absolute;
      opacity: 0;
      width: 20px;
      height: 2px;
      background-color: #ffcc01;
      bottom: -3px;
      transition: .4s;
      left: 0;
    }
    ul{
      display: flex;
      > li{
        padding: 5px;
        cursor: pointer;
      }
    }
}
.swiperSlide {
  margin: 5px auto;
  position: relative;
  overflow: hidden;
  max-width: 1600px;

  .chevronsSlide {
      list-style: none;
      padding: 0;
      position: absolute;
      top: 0;
      bottom: 0;
      margin: auto;
      height: fit-content;
      width: 100%;
      left: 0;
      height: 0;
      padding: 0 15px;
      display: flex;
      justify-content: space-between;
      z-index: 22;
      > li span{
        font-size: 25px;
        cursor: pointer;
        width: 25px;
        height: 25px;
        background-color: rgba(255, 204, 1, 0.37);
        padding: 6px;
        transition: .4s;
        border-radius: 3px;
        opacity: 0;
        top: -12px;
        position: relative;
        &:hover{
          background-color: rgba(255, 204, 1, 1);
        }
      }
      .button-next{
        right: -20px;
      }
      .button-prev{
        left: -20px;
      }
  }
  &:hover .chevronsSlide .button-next{
    right: 0;
    opacity: 1;
  }
  &:hover .chevronsSlide .button-prev{
    opacity: 1;
    left: 0;
  }
  .main_Slides {
    transition: .4s;
    overflow: hidden;
    // display: flex;
    // justify-content: space-around;
    .dateRow{
      p{
          font-size: 12px;
      }
      h3{
        font-size: 25px
      }
      .multiDayIcon{
        img{
          margin: 0 10px;
        }
      }
    }
    .bottomCelsius{
      p{
        text-align: center;
      }
      .multiDayTemp{
        span{
          padding: 0 15px;
        }
      }
       h4 {
          height: 20px;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          width: 100%;
      }
      .multiDayWeatherDesc{
        span{
          padding: 0 15px;
        }
      }
      .day {
          font-size: 25px;
          font-weight: 600;
      }
    }
    .slideWrap {
        display: inline-block;
        padding: 10px;
        height: 220px;
        .borderWrap{
          height: 100%;
          justify-content: space-around;
          display: flex;
          flex-direction: column;
          align-items: center;
          padding: 25px 0;
          border: 1px solid #ccc;
          background-color: #fff;
          .dateRow {
              text-align: center;
              p.multiDay{
                font-weight: 600;
                font-size: 20px;
              }
          }
        }
      }
      .activeItem{
       .borderWrap{
          position: relative;
          &:after{
            content:"";
            position: absolute;
            left: 0;
            right: 0;
            width: 100%;
            height: 100%;
            opacity: 0;
            background-color: #ffcc014f;
            cursor: pointer;
            transition: .4s;
          }
          &:hover:after{
            opacity: 1;
          }
       }
      }
      .activeShow{
        .borderWrap{
          position: relative;
          &:after{
            content:"";
            position: absolute;
            left: 0;
            right: 0;
            width: 100%;
            height: 100%;
            background-color: #ffcc014f;
            cursor: pointer;
            opacity: 1;
            transition: .4s;
          }
          
       }
      }
    }
  }
  .clickMore{
    display:block;
    text-align: center;
    text-decoration: none;
    color: #000;
  }
  .moreDescMultipleDay {
    max-width: 1600px;
    margin: auto;
    padding: 10px;
    
    display: flex;
    flex-direction: column;
    align-items: center;
    h4{
      position: relative;
      &:after{
          content: "";
          position: absolute;
          left: 0;
          right: 0;
          height: 2px;
          width: 60px;
          background-color: #ffcc01;
          margin: auto;
          bottom: -6px;
      }
    }
    ul{
      margin-top: 15px;
      li{
        display: flex;
        align-items: center;
        b, span{
          background-color: #fff;
          border: 1px solid #ccc;
          display: block;
          width: 125px;
          text-align: center;
          height: 45px;
          line-height: 45px;
          text-overflow: ellipsis;
          white-space: nowrap;
          overflow: hidden;
          img {
              max-width: 45px;
          }
        }
      }
    }
  }
  @media screen and (max-width:767px){
    .moreDescMultipleDay{
      align-items: flex-start;
          width: 100%;
          overflow-x: scroll;
          overflow-y: hidden;
    }
    .button-next{
      right: 0px !important;
      opacity: 1 !important;
    }
    .button-prev{
      left: 0px !important;
      opacity: 1 !important;
    }
  }
</style>


