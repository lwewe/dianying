<template>
  <div class="indexwrap">
    <!-- <NProgress v-if="loadingflag"/> -->
    <EnterLoging v-if="loadingflag"></EnterLoging>
    <!-- <div>
      <transition name="fade">
        <loading v-if="isLoading"></loading>
      </transition>
    </div> -->
    <div class="indexCenter">
      <div class="header">
        <div class="headerleft">
          <City></City>
        </div>
        <div class="headercenter">
          <van-search class="search" shape="round" background="#fff0" v-model="searchtext" @input="inpchange"
            @clear="searchtext = ''" placeholder="输入影片搜索" style="padding: 0;" />
        </div>
        <div class="headerright" style="" @click="gokefu">
          <img class="img" src="../assets/kfb.png" alt="">
        </div>
      </div>
      <!-- 轮播图 -->
      <div class="banner">
        <!--        :autoplay="3000"-->
        <van-swipe class="my-swipe" indicator-color="red">
          <van-swipe-item v-for="(item, index) in swiperimg" :key="index" @click="toFDetail(item.url)">
            <img class="img" :src="item.img" alt="">
          </van-swipe-item>
        </van-swipe>
      </div>

      <div class="welfare  mt0"
        v-if="searchtext == '' && !change && (kaquan_list1.length > 0 || kaquan_list2.length > 0)">
        <div class="hotsize">
          <div class="hotsizeTitle">文娱会员</div>
          <div class="gomemberindex" @click="gomemberindex">
            <div>全部</div>
            <div class="icons">
              <van-icon class="hoticon" size="12px" name="arrow" color="#A2988C" style="" />
            </div>
          </div>
        </div>
        <div class="welfarebox bgfff mt2">
          <div class="left" style="position: relative;">
            <div class="leftimgbox" v-for="(item, index) in kaquan_list1" :key="item.id" :id="index + 1"
              @click="godetail(item)">
              <img :src="item.img" :id="index + 1" alt="">
            </div>
          </div>
          <div class="right" style="">
            <div class="wrap1" v-for="(item) in kaquan_list2" :key="item.id">
              <div class="circle" :id="item.id">
                <!-- @click="gomusicmember(item.id)" -->
                <a :href="item.url">
                  <img :src="item.img" :id="item.id" alt="">
                </a>

              </div>
              <span style="white-space: nowrap">{{ item.title }}</span>
            </div>
          </div>
        </div>
      </div>
      <!-- yanchu -->


      <div class="welfare bikan" v-if="mustSeeList.length > 1">
        <div class="hotsize">
          <div class="hotsizeTitle">必看演出</div>
          <div class="gomemberindex" @click="toOther('/performanceCalendar')">
            <div>全部</div>
            <div class="icons">
              <van-icon class="hoticon" size="12px" name="arrow" color="#A2988C" style="" />
            </div>
          </div>
        </div>
        <div
          style="padding: 0px 10px;background-color: #ffffff;border-bottom-left-radius: 10px;border-bottom-right-radius: 10px;">
          <div class="birthdayBox bgfff">
            <div class="birthday" :style="'width:' + mustSeeList.slice(1, 11).length * 86 + 'px;'">
              <div class="listItem" v-for="item in mustSeeList.slice(1, 11)" :key="item.id">
                <ShopList :listItem="item" :flag="2"></ShopList>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- yanchu -->



    </div>

    <!-- 热映榜单 -->


    <div v-if="searchtext == ''" style="padding: 0 15px;">
      <div class="welfare reying">
        <div class="hotsize hotsize2">
          <div class="hotsizeTitle hotsizeTitle2">热映榜单</div>
          <div class="gomemberindex" @click="hotlist">
            <div>全部</div>
            <div class="icons">
              <van-icon class="hoticon" size="12px" name="arrow" color="#A2988C" style="" />
            </div>
          </div>
        </div>
        <div class="hot bgfff">
          <div>
            <div class="banshi">
              <div class="swiper-container1">
                <div class="swiper-wrapper1">
                  <div v-for="(item, index) in swiperList" :key="index" class="swiper-slide1">
                    <div class="imgbox" style="position: relative;">
                      <img @click="filmdetail(item.movieId)" class="hotimg" :src="item.posterUrl" alt="" style="">
                      <div class="score">评分 {{ item.score }}</div>
                      <img class="logonum" :src="require('../assets/number/' + (index + 1) + '.png')" alt="">
                    </div>
                    <p class="hotname elips1" style="">{{ item.name }}</p>
                    <!-- <button @click="buyticket(item)">购&nbsp;票</button> -->
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>


    </div>

    <!-- 正在热映 -->
    <van-tabs class="seehotlist" v-model="active" @change="changeActive" title-active-color="#240003" :line-width="22"
      background="#fff0">
      <van-tab title="正在热映"></van-tab>
      <van-tab title="即将上映"></van-tab>
    </van-tabs>
    <div class="filmboxMax">
      <div class="filmbox" v-for="(item, index) in hotfilmList" :key="index"
        @click="filmdetail(item.movieId, item.showSt)">
        <div class="filmleft">
          <div class="posterUrl">
            <img class="img" :src="item.posterUrl" alt="" style="">
          </div>
          <div class="nameBox">
            <div class="elips11 itemName">{{ item.name }}</div>
            <div class="starBox" v-if="item.star">
              <div class="font1">主演:</div>
              <div class="font2 elips11">{{ item.star.split(" ").join("/") }}</div>
            </div>
            <div class="starBox" v-if="item.director">
              <div class="font1">导演:</div>
              <div class="font2 elips11">{{ item.director }}</div>
            </div>
            <div class="starBox categoryBox ">
              <div v-if="item.category" class="category elips11">{{ item.category.split("|").join("/") }}</div>
              <div v-if="item.version" class="category elips11">{{ item.version }}</div>
            </div>
          </div>
        </div>
        <div class="filmright">
          <div class="itemscore" v-if="active == 0">评分
            <span>{{ item.score }}</span>
          </div>
          <div class="itemscore" v-if="active == 1 && item.wish > 0">
            <span>{{
              item.wish > 1000 ? (item.wish / 10000).toFixed(1) : item.wish
            }}</span>{{ item.wish > 1000 ? "万" : '' }}人想看
          </div>
          <!-- <div class="pap" v-if="item.isPreShow || active == 0" :class="{ pap2: active == 1 }"
            @click.stop="buyticket(item)">{{
              active == 0 ? "购 票" : "预 售"
            }}
          </div> -->
        </div>
      </div>
    </div>
    <!--    <NavigationTab :active="0"></NavigationTab>-->
  </div>
</template>

<script>
import Loading from "@/components/loding.vue";
import NavigationTab from "@/components/NavigationTab.vue";
import Swiper from 'swiper'; // 引入的Swiper
import 'swiper/css/swiper.min.css'
import axios from "axios"
//引入
import moment from 'moment';
import 'moment/locale/zh-cn';

moment.locale('zh-cn');
// 获取客服
import { getLogo } from "@/api/locality/login"
//调用热映电影列表
import { getFilmList, getFutureFilmList } from "@/api/film";
// 调用首页的轮播图
// import {getIndexBannerList} from "@/api/locality/banner"
// 调用城市接口
// import {getCityList, getDramaList} from "@/api/city";
import { getDramaList } from "@/api/performance";
// 调用地图
import BMap from "BMap"
import City from "@/components/City.vue";
// 调用加载组件
import EnterLoging from "@/components/EnterLoging";
import ShopList from "@/components/ShopList.vue";
import { getAccessToken, getComingMoviesList, getHitFilmList } from "@/api/newFilm";
import { getCityList, getIndexBannerList } from "@/api/city";
import wx from "weixin-js-sdk";
import { getLocations } from "@/api/locality/login";
export default {
  components: { ShopList, NavigationTab, Loading, City, EnterLoging },
  data() {
    return {
      mustSeeList: [],
      info: "66666",
      isLoading: false,
      token: '',
      // baseUrl: require("../utils/baseurl.js").baseUrl,
      swiperimg: [],
      timestamp: null,
      searchtext: "",
      // swiper
      swiper: null,
      // 热映榜单
      swiperList: [],
      //正在热映列表
      hotfilmList: [],
      //即将上映列表
      futurefilmList: [],
      // 地图位置
      center: { lng: "", lat: "" },
      province: "",
      // 调用接口查询的所有城市
      cityList: [],
      city: "正在定位",
      cityid: null,
      kaquan_list1: [],
      kaquan_list2: [],
      loadingflag: true,
      kefu: "",
      count: 1,
      change: null,
      shopList: [],
      active: 0,
      pageno: 1,
      timer: null
    }
  },
  methods: {
    getLocation1() {
      let purl = /(Android)/i.test(navigator.userAgent) ? location.href.split('#')[0] : window.localStorage.getItem('scanUrl');
      getLocations({
        url: purl
      }).then(res => {

        if (res.code == 200) {
          this.info = res.data

           setTimeout(() => {this.getSign()},1000)
          
        }
      })
    },
    getSign() {

      
      //请求后端，获取微信签名信息
      let configData = {
        debug: false,
        appId: this.info.appid, // 必填，公众号的唯一标识
        timestamp: "" + this.info.time, // 必填，生成签名的时间戳
        nonceStr: this.info.nonceStr, // 必填，生成签名的随机串
        signature: this.info.signature,// 必填，签名
        jsApiList: ['getLocation']
      }

      wx.config(configData);

      wx.ready((res1) => {
        wx.getLocation({
          type: 'wgs84',
          success: (res) => {
            this.getCityName(res.longitude, res.latitude)

          }, fail: () => {
            // this.createMap2()
          }
        });
      });
      wx.error(function (res) {
      });
    },
    isWeiXin() {
      var ua = window.navigator.userAgent.toLowerCase();
      if (ua.match(/MicroMessenger/i) == "micromessenger") {
        return true;
      } else {
        return false;
      }
    },
    getCityName(lng, lat) {
      // 创建一个坐标点
      let point = new BMap.Point(lng, lat);

      // 创建一个地理编码实例
      let geoc = new BMap.Geocoder();

      // 根据坐标点进行逆地理编码
      geoc.getLocation(point, (rs) => {
        let addComp = rs.addressComponents;
        // console.log(addComp,"addComp")
        this.city = addComp.city;

        sessionStorage.setItem("cityName", this.city)
        // if (sessionStorage.getItem("promptcity") != 2 && sessionStorage.getItem("change") != 1) {
        //   this.messageinfo()
        // }

        // sessionStorage.getItem("cityName", this.city)
        this.getCity1(this.city)
      });
    },
    getCity1(cityName) {

      getCityList({
        keyword: cityName ? cityName.replace(/市$/, '') : '',
      }).then(res => {

        this.cityList = res.data || []

        if (this.cityList.length > 0) {
          this.getDramaProjectList(this.cityList[0].cityid1)


        }
      })
    },
    //addend

    // 获取演出列表
    getDramaProjectList(ids) {

      let data2 = {
        method: "xuankua.drama.dramaProjectList",
        cityCode: ids || sessionStorage.getItem("targetId"),
        pageNo: 1,
        pageSize: 100,
        ...this.$store.state.parameter,
      }

      let sign2 = this.$utils.getASCII(data2)
      getDramaList({
        sign: sign2,
        ...data2
      }).then(res => {


        this.loadingflag = false
        if (res.code == 200 && res.data.records.length > 0) {
          res.data.records.forEach(item => {
            if (this.calculateDaysDiff(item.showStartTime) <= 30 && this.calculateDaysDiff(item.showStartTime) > 1) {

            } else if (this.calculateDaysDiff(item.showStartTime) <= 1) {
              this.mustSeeList.push(item)

            }

          })

        }
      })
    },
    toOther(path) {

      window.location.href = this.$store.state.perkUrl;
    },
    // 距离现在有多少天
    calculateDaysDiff(date) {
      const targetDate = new Date(date);
      const currentDate = new Date();
      const timeDiff = targetDate.getTime() - currentDate.getTime();
      const daysDiff = Math.ceil(timeDiff / (1000 * 3600 * 24));
      return daysDiff
    },
    // 搜索
    inpchange(e) {
      if (this.timer) {
        clearTimeout(this.timer);
      }
      this.timer = setTimeout(() => {
        if (this.active == 0) {
          this.getFilm()
        } else {
          // 获取即将上映电影列表
          this.getFutureFilm()
        }
        this.timer = undefined;
      }, 1000)
    },
    // tab卡换
    changeActive(e) {
      if (e == 0) {
        this.getFilm()
      } else {
        // 获取即将上映电影列表
        this.getFutureFilm()
      }
    },
    godetail(item) {
      this.$router.push(item.url)
    },
    toFDetail(url) {
      if (url) {
        // window.location.href = url+"&cityid="+ this.cityid
        this.$router.push({ path: url + "&cityid=" + this.cityid })
      }
    },
    toPer() {
      window.location.href = this.$store.state.perkUrl
    },
    // 跳转到福利专区
    gomemberindex() {
      this.$router.push({ path: "/memberindex" })
    },
    //热映榜单跳转
    hotlist() {
      this.$router.push({ path: "/hotlist", query: { cityid: '' + this.cityid } })
    },
    // 点击进入电影详情
    filmdetail(id, showSt) {
      this.$router.push({ path: "/filmdetail", query: { id, showSt } })
    },
    // 搜索框搜索
    searchfilm() {
      this.isSearch = !this.isSearch
    },
    // 获取客服信息
    getLoginInfo() {
      getLogo().then(res => {
        if (res.code == 200) {
          this.kefu = res.data.zxkf
        }
      })
    },
    // 获取首页轮播图
    getallSwiper() {
      getIndexBannerList({ token: this.token }).then(res => {
        if (res.code == 200) {
          this.swiperimg = res.data.list
          this.kaquan_list1 = res.data.kaquan_list1
          this.kaquan_list2 = res.data.kaquan_list2


        } else {
        }
      })
    },
    gokefu() {
      window.location.href = this.kefu
      // window.open(this.kefu,"_blank")
    },
    // 地图获取当前定位
    createMap() {
      const geolocation = new BMap.Geolocation();
      var _this = this
      geolocation.getCurrentPosition(function getinfo(position) {
        let latitude = position.latitude
        let longitude = position.longitude
        let city = position.address.city.substring(0, position.address.city.length - 1); //获取城市信息
        let province = position.address.province;  //获取省份信息
        _this.center.lng = longitude;
        _this.center.lat = latitude;
        _this.province = province
        _this.city = city
        // seehotlist
        // 获取当前的城市


        if (_this.isWeiXin()) {
          if (sessionStorage.getItem("targetId2")) {
           // alert(1)
            //   _this.center.lng = sessionStorage.getItem("longitude");
            //  _this.center.lat = sessionStorage.getItem("latitude")
            _this.getCity(_this.city)
          } else {
          //  alert(2)
            _this.getLocation1()
               _this.getCity(_this.city)
          }
        } else {
       //   alert(3)
          _this.getCity(_this.city)
        }

      }, { enableHighAccuracy: true }, function (e) {
      }, {
        provider: 'baidu'
      });
    },
    // 买票  暂无数据
    buyticket(item) {
      // var id = option.target.getAttribute("name")
      // var data = option.target.getAttribute("data")
      this.$router.push({
        path: "/multiplex",
        query: { movieId: item.movieId, movieCode: item.movieCode, moveName: item.name }
      })
    },
    // 获取所有城市
    getCity(cityName) {

      getCityList({
        keyword: cityName
      }).then(res => {
        this.cityList = res.data || []
        if (this.cityList.length > 0) {
          if (!sessionStorage.getItem("cityName")) {
            // console.log(this.cityList[0].cityid1 + 'this.cityList[0].cityid1')
           // this.getDramaProjectList(this.cityList[0].cityid1)
            sessionStorage.setItem("targetId", this.cityList[0].cityid1)
            sessionStorage.setItem("targetId2", this.cityList[0].cityid2)
            sessionStorage.setItem("cityName", this.cityList[0].name)
            sessionStorage.setItem("cityId2", this.cityList[0].id)
            sessionStorage.setItem("cityItem", JSON.stringify(this.cityList))
            //  alert('d' + this.cityList[0].cityid1)
          }
          // this.$toast(this.cityList[0].cityid2)
          this.city = sessionStorage.getItem("cityName")
          if (this.city != cityName) {
            console.log(sessionStorage.getItem("targetId") + '--targetId--')
            //  this.getDramaProjectList(sessionStorage.getItem("targetId"))
            this.cityid2 = Number(sessionStorage.getItem("targetId2"))
            //  alert('er' + sessionStorage.getItem("targetId"))

          } else {
            console.log(this.cityList[0].cityid1 + '+++')
            this.cityid2 = this.cityList[0].cityid2
            //  this.getDramaProjectList(sessionStorage.getItem("targetId"))
            //  alert('san' + sessionStorage.getItem("targetId"))
          }

          this.getFilm()

        }
      })
    },
    //获取热映列表
    getFilm() {
      getHitFilmList({
        cityId: this.cityid2,
        pageSize: 99999999
      }).then(res => {
        // console.log(res)
        this.loadingflag = false
        if (res.code == 0) {
          this.hotfilmList = res.data.movies
          this.swiperList = res.data.movies.slice(0, 10)
          if (this.searchtext == "") {
            setTimeout(() => {
              this.getSwiper()
            }, 500)
          } else {
            this.hotfilmList = res.data.movies.filter(item => item.name.includes(this.searchtext));
          }
        } else if (res.code == 401) {
          // this.$toast(res.msg)
          // setTimeout(()=>{
          //   this.getFilm()
          // },1000)
        }
      })
    },
    //获取即将上映电影列表
    getFutureFilm() {
      getComingMoviesList({
        cityId: this.cityid2,
        // pageSize: 99999999
      }).then(res => {
        // console.log(res)
        this.loadingflag = false
        if (res.code == 0) {
          this.hotfilmList = res.data.movies
          if (this.searchtext == "") {
            setTimeout(() => {
              this.getSwiper()
            }, 500)
          } else {
            this.hotfilmList = res.data.movies.filter(item => item.name.includes(this.searchtext));
          }
        }
      })
    },
    // banner轮播图
    getSwiper() {
      this.swiper = new Swiper(".swiper-container", {
        loop: false, // 无缝
        // autoplay: { //自动开始
        //   delay: 3000, //时间间隔
        //   disableOnInteraction: false, //*手动操作轮播图后不会暂停*
        // },
        grabCursor: true,
        paginationClickable: true,
        slidesPerView: 3.7, // 一组三个
        spaceBetween: 2, // 间隔
        // 窗口变化,重新init,针对F11全屏和放大缩小,必须加
        observer: true,
        observeParents: true,
        // 如果需要分页器
      })
    },
    // 专享福利右侧跳转
    gomusicmember(num) {
      if (num == 5 || num == 8 || num == 9) {
        this.$router.push({ path: "/musicmember" })
      } else if (num == 6) {
        this.$router.push({ path: "/memberindex", query: { id: '' + num - 1 } })
      } else {
        this.$router.push({ path: "/readmember" })
      }
    },

    getTokenKey() {
      getAccessToken({
        entId: "101344",
        appSecret: "IALrbqLuSVtlMW3k9WlnxnCwuCA556jr"
      }).then(res => {
        // console.log(res, "1111")
        if (res.code == 0) {
          sessionStorage.setItem('token_type', res.data.token_type);
          sessionStorage.setItem('access_token', res.data.access_token);
          this.createMap()
        }
      })
    },
  },
  mounted() {
    // this.messageinfo()
    window.addEventListener('scroll', this.handleScroll);
    this.getLoginInfo()
    //获取正在热播
    // setTimeout(() => {
    //   this.getFilm()
    // }, 800)
    // 获取轮播图
    this.getallSwiper()
    // if (this.$route.query.change == 1) {
    //   this.getCity(sessionStorage.getItem("cityName"))
    // } else {
    //   this.getDramaProjectList()
    // }
    // alert('sessionStorage.getItem("targetId")' +sessionStorage.getItem("targetId"))
    if (sessionStorage.getItem("targetId")) {

      this.getDramaProjectList(sessionStorage.getItem("targetId"));
    }


  },
  destroyed() {
    window.removeEventListener('scroll', this.handleScroll, false);
    window.onresize = null;

  },
  created() {
    this.token = localStorage.getItem("token");
    console.log(this.token)
    setTimeout(() => {
      this.loadingflag = false
    }, 4000)
    // 是微信端浏览器——公众号
    // let APPID = "wx05cc5223511e93c3";
    this.timestamp = moment(new Date()).format('yyyy-MM-DD HH:mm:ss');
    //判断url是否传过来值，若没传过来则证明为刚进网站，若有值则证明去地址页面选择了地址
    this.city = sessionStorage.getItem("cityName")
    this.cityid = Number(sessionStorage.getItem("targetId"))




    //  if (sessionStorage.getItem("targetId")) {
    // this.getDramaProjectList()
    // }



    this.cityid2 = Number(sessionStorage.getItem("targetId2"))
    this.change = sessionStorage.getItem("change")
    if (!sessionStorage.getItem('access_token')) {
      this.getTokenKey()
    } else {
      this.createMap()
    }


    //  if ( this.cityid && this.city) {

    //   this.getLocation1()



    // }

  },
}
</script>
<style scoped lang="less">
.elips1 {
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  font-weight: 500;
  //width: 100% !important;
}

.elips11 {
  color: #474747;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;

  //width: 100% !important;
}

.van-tabs__nav {
  background-color: transparent !important;
}

.indexwrap {
  //width: 100vw;
  //overflow-y: scroll;
  min-height: calc(100vh - 50px);
  background-color: #FFF8E2;
  //overflow: hidden;
}


.indexCenter {
  background: linear-gradient(#FF6E9E, #FFF8E2);
  padding: 0 15px;
}

.indexwrap .header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0px 11px;
  gap: 10px;
}

.indexwrap .header .cross,
.service,
.ellipsis,
.arrow-down {
  color: #fff;

}

.headercenter {
  width: 100%;
  padding-left: 10px;

  /deep/ .van-search__content {
    background-color: #ffffff;
  }

  /deep/ .van-cell {
    font-size: 13px;
  }
}

.headerright {
  position: relative;
  top: 3px;
  width: 43px;
}

.indexwrap .header .headerleft .address {
  margin-left: 10px;
  height: 100%;
  display: flex;
  align-items: center;
}

.indexwrap .header .headerleft .address span {
  font-size: 15px;
  color: #fff;
  margin-right: 3px;
}

/* banner部分 */
.indexwrap .banner {
  //border-radius: 10px;
  //height: 190px;
}

.indexwrap .banner .my-swipe {
  //height: 135px;
  //height: 21vh;\\
  border-radius: 8px;
}

.indexwrap .banner .my-swipe /deep/ .van-swipe-item {
  color: #fff;
  font-size: 20px;
  text-align: center;
  border-radius: 8px;
}

.indexwrap .banner .my-swipe /deep/ .van-swipe-item img {
  background-size: cover;
  border-radius: 8px;
}

.indexwrap .banner /deep/ .van-swipe__indicator {
  background-color: #fff;
}

.indexwrap .welfare {
  //height: 210px;
  background-image: linear-gradient(to bottom, #f7ecee 10%, #ffffff 90%);
  margin: 5px 0px 10px 0px;
  border-radius: 8px;
}

.indexwrap .welfare.reying {

  background-image: linear-gradient(to bottom, #FDECCC 1%, #ffffff 99%);
}

.indexwrap .welfare.bikan {
  background-image: linear-gradient(to bottom, #FFDFD5 1%, #ffffff 99%);
}

.indexwrap .welfare.mt0 {
  margin-top: 2px;

}

.indexwrap .welfare .welfarebox {
  width: 100%;
  display: flex;
  border-radius: 10px;
  overflow: hidden;
  padding: 0px 10px 8px;
  box-sizing: border-box;
  justify-content: space-between;
}

.indexwrap .welfare .bgfff {
  border-top-left-radius: 0;
  border-top-right-radius: 0;
  background-color: #ffffff;
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;
}

.welfarebox .mt2 {
  padding-top: 2px;
}

.indexwrap .welfare .welfarebox .left {
  width: 53%;
  /* background-color: red; */
  //margin-left: 10px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  // padding-top: 5px;
}

.indexwrap .welfare .welfarebox .left .leftimgbox {
  width: 48%;
  overflow: hidden;
}

.indexwrap .welfare .welfarebox .left .leftimgbox img {
  width: 100%;
  /* height: 200px; */
}

.indexwrap .welfare .welfarebox .right {
  width: 44%;
  display: flex;
  flex-wrap: wrap;
  /* margin: 5 0px; */
  //padding: 5px 10px;
  justify-content: space-between;
  gap: 5px;
}

.indexwrap .welfare .welfarebox .right .wrap1 {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 27%;
}

.indexwrap .welfare .welfarebox .right .circle {
  border-radius: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex: none;
  width: 100%;
  text-align: end;
  overflow: hidden;
}

.indexwrap .welfare .welfarebox .right .circle a {
  display: initial;
}

.indexwrap .welfare .welfarebox .right .circle img {
  flex: none;
  width: 90%;
}

.indexwrap .welfare .welfarebox .right span {
  color: #342C2C;
  font-size: 12px;
  line-height: 1;
}


/* 热映榜单标题 */
.hotsize {
  padding: 8px 10px 3px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
  background-image: linear-gradient(to bottom, #f7ecee 50%, #ffffff);

  .hotsizeTitle {
    font-size: 16px;
    color: #FF2F70;
    letter-spacing: 1px;
    font-weight: 500;
  }

  .hotsizeTitle2 {
    color: #FF2F70;
  }

  .gomemberindex {
    display: flex;
    align-items: center;
    gap: 2px;
    color: #9B9186;
    font-size: 12px;

    .icons {
      height: 12px;
    }
  }
}

.hotsize2 {
  margin-top: 3px;
}

/deep/ .van-tab {
  flex: none;
  font-size: 16px;
}

/deep/ .van-tabs--line .van-tabs__wrap {
  height: 38px;
  padding: 0px 12px;
}

.indexwrap /deep/ .van-hairline--top-bottom::after {
  border: none !important;
}

/* 热映榜单 */

.hot {
  overflow: hidden;
  padding: 3px 10px 9px 10px;

  .swiper-wrapper1 {
    display: flex;
    overflow-y: hidden;
    overflow-x: auto;
    gap: 8px;

    .swiper-slide1 {
      min-width: 92px;
    }
  }

  .swiper-wrapper1::-webkit-scrollbar {
    display: none;
    /* 对于 Chrome, Safari 和 Opera */
  }

  .imgbox {
    width: 100%;
    height: 121px;
    border-radius: 5px;
    overflow: hidden;
  }

  .score {
    position: absolute;
    left: 0;
    bottom: 0px;
    z-index: 100;
    color: #fff;
    background-image: linear-gradient(to bottom, #00000012, #000000);
    font-size: 12px;
    width: 100%;
    padding: 5px 9px 5px;
    box-sizing: border-box;
  }

  .logonum {
    position: absolute;
    top: 0px;
    left: 5px;
    width: 18px;
  }

  button {
    font-size: 13px;
    color: #fff;
    border: none;
    background-image: linear-gradient(to bottom, #FF6E9E, #FF2F70);
    border-radius: 30px;
    margin: auto;
    display: block;
    width: 63px;
    height: 26px;
    line-height: 27px;
    font-weight: 500;
    padding: 0px;
    margin-top: 5px;
  }

  .hotimg {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .hotname {
    width: 100%;
    text-align: center;
    font-size: 14px;
    margin-top: 5px;
    margin-bottom: 0;
  }
}

.filmboxMax {
  padding: 0px 16px 65px;
  margin-top: -3px;
}

.filmbox {
  background-color: #ffffff;
  border-radius: 10px;
  padding: 10px;
  display: flex;
  //align-items: center;
  justify-content: space-between;
  margin-top: 13px;

  .filmleft {
    display: flex;
    //align-items: center;
    gap: 8px;
    width: 71%;

    .posterUrl {
      min-width: 76px;
      width: 76px;
      overflow: hidden;
      border-radius: 7px;

      img {
        display: flex;
      }
    }

    .nameBox {
      width: calc(100% - 84px);
      font-size: 13px;

      .itemName {
        font-size: 18px;
        color: #222222;
      }

      .starBox {
        display: flex;
        align-items: center;
        white-space: nowrap;
        gap: 2px;
        margin-top: 5px;
      }

      .font1 {
        color: #474747;
      }

      .categoryBox {
        gap: 6px;
        margin-top: 11px;

        .category {
          color: #c2c2c2;
          border-radius: 2px;
          border: 1px solid #e4e2e2;
          font-size: 10px;
          height: 18px;
          line-height: 18px;
          padding: 0px 6px;
          max-width: 112px;

        }
      }

    }
  }

  .filmright {
    padding-top: 3px;

    .itemscore {
      font-size: 12px;
      color: #756B59;
      text-align: end;

      span {
        color: #D7000F;
        font-size: 15px;
      }
    }

    .pap {
      background-color: #FF2F70;
      font-size: 12px;
      color: #fff;
      font-weight: 500;
      border-radius: 30px;
      width: 63px;
      height: 26px;
      line-height: 27px;
      margin-top: 17px;
      text-align: center;
      // line-height: 27px;
    }

    .pap2 {
      background-color: #40A6FB;
    }
  }
}

//
.birthdayBox {
  overflow-x: auto;
  overflow-y: hidden;
  // margin-top: 8px;
  //background-color: white;
  border-radius: 10px;

  .birthday {
    display: flex;
    gap: 3px;
    padding: 3px 0px 5px;

    .listItem {
      width: 100%;
      padding-bottom: 5px;
      padding: 0px 1px 3px;
      border-radius: 5px;

      .textInfo {
        font-size: 10px;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        overflow: hidden;
        -webkit-line-clamp: 2;
        /* 显示两行 */
        margin-top: 5px;
      }

      .hotText {
        color: #F0585E;
        border-radius: 30px;
        //text-align: center;
        font-size: 12px;
        //width: 70%;
        margin: auto;
        position: relative;
        z-index: 2;
        padding: 2px 0px;
        margin-top: 6px;
      }

      .title {
        font-size: 12px;
        font-weight: bold;
        color: #313131;
        padding-top: 3px;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        overflow: hidden;
        -webkit-line-clamp: 2;
        /* 显示两行 */
      }

      .shopInfo {
        padding: 0px 6px;
      }


      .oprice {
        font-size: 10px;
        color: #A1A1A1;
        text-decoration: line-through;
      }
    }
  }
}


.birthdayBox::-webkit-scrollbar {
  display: none
}

.pap {
  white-space: nowrap;
}
</style>