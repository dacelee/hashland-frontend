<template>
  <div class="basic_card_page">
    <!-- 卡牌 -->
    <div class="myCard">
      <div class="card_left">
        <span class="span1 fontsize22">{{ $t("message.nftMining.txt3") }}</span>
        <div class="card_btnbox">
          <div class="synthesis_btn btn_margin_left fontsize16" @click="transferClick">
            {{$t("message.transfer.txt1")}}
          </div>
          <div class="synthesis_btn fontsize16" @click="synthesisClick">
            {{ $t("message.nftMining.txt5") }}
          </div>
        </div>
      </div>
      <span class="span2 fontsize12_400">{{$t("message.nftMining.txt4")}}</span>
    </div>
    <!-- 我的新卡牌轮播 -->
    <div class="swiper-container swiper-container1">
      <div class="swiper-wrapper">
        <div class="swiper-slide" v-for="(item, index) in newCardArr" :key="index">
          <div class="content_box" @click="jumpDetails(item,2)">
            <img :src="item.src" class="swiper_img" />
            <div class="grade_box">
              <div class="five_pointed_star">
                <img
                  :src="`${$store.state.imgUrl}cardlevel${item.level}.png`"
                  class="start_img"
                />
                <span class="level_class fontsize16">LV{{ item.level }}</span>
              </div>
              <div class="num_details">
                <div class="card_grade_loading fontsize16" v-if="item.status"><NewLoading></NewLoading></div>
                <span class="card_grade fontsize16" v-else>{{ item.num }}</span>
                <span class="details fontsize12">{{
                  $t("message.nftMining.txt6")
                }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- 如果需要导航按钮 -->
      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </div>
    <!-- 我的卡牌轮播 -->
    <div class="swiper-container add_mynewCard swiper-container3">
      <div class="swiper-wrapper">
        <div class="swiper-slide" v-for="(item, index) in slotArr" :key="index">
          <div class="content_box" @click="jumpDetails(item,1)">
            <img :src="item.src" class="swiper_img" />
            <div class="grade_box">
              <div class="five_pointed_star">
                <img
                  :src="`${$store.state.imgUrl}cardlevel${item.level}.png`"
                  class="start_img"
                />
                <span class="level_class fontsize16">LV{{ item.level }}</span>
              </div>
              <div class="num_details">
                <div class="card_grade_loading fontsize16" v-if="item.status"><NewLoading></NewLoading></div>
                <span class="card_grade fontsize16" v-else>{{ item.num }}</span>
                <span class="details fontsize12">{{
                  $t("message.nftMining.txt6")
                }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- 如果需要导航按钮 -->
      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </div>
    <!-- 卡槽 -->
    <div class="myCard margin_top_card">
      <div class="card_left">
        <span class="span1 fontsize22">{{ $t("message.nftMining.txt7") }}</span>
      </div>
      <span class="span2 fontsize12_400">{{
        $t("message.nftMining.txt8")
      }}</span>
    </div>
    <!-- 卡槽轮播 -->
    <div class="swiper-container swiper-container2 self_swiper">
      <div class="swiper-wrapper">
        <div
          class="swiper-slide"
          v-for="(item, index) in cardsoltArr"
          :key="index"
        >
          <div class="outbox">
            <div class="second-content_box">
              <img
                :src="item.src"
                class="swiper_img"
                :class="{serise2Img:item.series == 1}"
                v-if="item.btnstatus !== 3"
              />
              <Lottie :left="getIsMobile?'48%':'50%'" :transform="true" :top="getIsMobile?'6%':'-3%'" :options="anmationArr.filter(ele => {return ele.level == item.level && ele.type == item.type})[0].dataJson" :width="getIsMobile?210:''" v-if="item.ultra && anmationArr.length > 0"></Lottie>
              <img
                :src="item.src"
                class="lock_swiper_img"
                v-if="item.btnstatus == 3"
              />
              <img
                :src="`${$store.state.imgUrl}pledgebg.png`"
                class="base_img"
              />
            </div>
            <div
              class="btnbox remove_btnbox fontsize16"
              v-if="item.btnstatus == 1"
              @click="insertClick"
            >
              {{ $t("message.nftMining.txt15") }}
            </div>
            <div
              class="btnbox insert_btnbox fontsize16"
              v-if="item.btnstatus == 2"
              @click="removeClick(item)"
            >
              {{ $t("message.nftMining.txt9") }}
              <BtnLoading :isloading="item.isloading"></BtnLoading>
            </div>
            <div
              class="btnbox lock_btnbox fontsize16"
              v-if="item.btnstatus == 3"
            >
              <Btn
                :isapprove="isapprove"
                :approveloading="item.isloading"
                :isloading="item.isloading"
                :word="$t('message.nftMining.txt10')"
                ref="mychild"
                @sonapprove="sonapprove(item)"
                @dosomething="Unlock(item)"
              />
            </div>
          </div>
        </div>
      </div>
      <!-- 如果需要导航按钮 -->
      <div class="swiper-button-prev"></div>
      <div class="swiper-button-next"></div>
    </div>
    <Proup
      :btntxt="btntxt"
      :word="word"
      :proupDis="proupDis"
      @besurefun="sureBtnFun"
      @closedis="closeFun"
    ></Proup>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Swiper from "swiper";
import { hnPool, hn, getSigner, hc, util, contract, getHnImg } from "hashland-sdk";
export default {
  data () {
    return {
      anmationArr:[],//动画数组的json
      newCardArr:[
        {
          level: 1,
          src: `${this.$store.state.imgUrl}newCardLevel1.png`,
          num: 0,
          status:true
        },
        {
          level: 2,
          src: `${this.$store.state.imgUrl}newCardLevel2.png`,
          num: 0,
          status:true
        },
        {
          level: 3,
          src: `${this.$store.state.imgUrl}newCardLevel3.png`,
          num: 0,
          status:true
        },
        {
          level: 4,
          src: `${this.$store.state.imgUrl}newCardLevel4.png`,
          num: 0,
          status:true
        },
        {
          level: 5,
          src: `${this.$store.state.imgUrl}newCardLevel5.png`,
          num: 0,
          status:true
        },
      ],
      changeAddress:true,//切换账号时此变量变换
      isapprove: false,// 是否授权busd
      btntxt: "", // 弹窗页面的确认按钮
      word: "", //弹窗提示文字
      proupDis: false, // 弹窗展示消失变量
      cardSlot: 0, //用户的卡槽数量
      emptyCardSlot: 0, //用户的空卡槽数量
      maxCardSlot: 0, //最大卡槽数量
      buyHCMoney: 0, //解锁所需hc金额
      proupBtnstatus: false, // 弹窗的确认按钮 是否可以关闭弹窗
      datainfo: {}, // 点击解锁按钮 存储的信息
      slotArr:[
        {
          level: 1,
          src: `${this.$store.state.imgUrl}level1.png`,
          num: 0,
          status:true
        },
        {
          level: 2,
          src: `${this.$store.state.imgUrl}level2.png`,
          num: 0,
          status:true
        },
        {
          level: 3,
          src: `${this.$store.state.imgUrl}level3.png`,
          num: 0,
          status:true
        },
        {
          level: 4,
          src: `${this.$store.state.imgUrl}level4.png`,
          num: 0,
          status:true
        },
        {
          level: 5,
          src: `${this.$store.state.imgUrl}level5.png`,
          num: 0,
          status:true
        },
      ],
      cardsoltArr: [], // 卡牌组2
      resetCardsoltArr:[
        {
          src: "",
          btnstatus: 1, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        },
        {
          src: "",
          btnstatus: 1, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        },
        {
          src: `${this.$store.state.imgUrl}cardlock.png`,
          btnstatus: 3, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        },
        {
          src: `${this.$store.state.imgUrl}cardlock.png`,
          btnstatus: 3, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        },
        {
          src: `${this.$store.state.imgUrl}cardlock.png`,
          btnstatus: 3, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        },
        {
          src: `${this.$store.state.imgUrl}cardlock.png`,
          btnstatus: 3, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        }
      ],// 质押卡槽数组初始数据
      swiper1: null,
      swiper2: null,
      timerll:null, // 定时器对象  获取用户总卡牌数
      time_btn:null
    };
  },
  mounted () {
    let timerObject = setInterval(() => {
      if(localStorage.getItem('Animation')){
        this.anmationArr = JSON.parse(localStorage.getItem('Animation'))
        clearInterval(timerObject)
      }
    },1000)
    this.$nextTick(() => {
      this.swiper1 = new Swiper(".swiper-container1", {
        nextButton: ".swiper-button-next",
        prevButton: ".swiper-button-prev",
        slidesPerView: document.body.clientWidth > 980 ? 4 : 2,
        observer: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        observeParents: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        initialSlide: 0,
      });
      this.swiper2 = new Swiper(".swiper-container2", {
        nextButton: ".swiper-button-next",
        prevButton: ".swiper-button-prev",
        slidesPerView: document.body.clientWidth > 980 ? 4 : 2,
        observer: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        observeParents: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        initialSlide: 0,
      });
      this.swiper3 = new Swiper(".swiper-container3", {
        nextButton: ".swiper-button-next",
        prevButton: ".swiper-button-prev",
        slidesPerView: document.body.clientWidth > 980 ? 4 : 2,
        observer: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        observeParents: true, // 将observe应用于Swiper的祖先元素。当Swiper的祖先元素变化时，例如window.resize，Swiper更新。
        initialSlide: 0,
      });
    });
  },
  watch: {
    getIstrue: {
      handler (newValue) {
        if (newValue) {
          this.slotArr.forEach(item => {
            item.status = true
          })
          this.newCardArr.forEach(item => {
            item.status = true
          })
          this.getUserAllCard()
          this.getCardSlotInfo();
          clearInterval(this.time_btn)
          this.time_btn = setInterval(() => {
            if (this.cardsoltArr.length > 0 && this.changeAddress) {
              let istrue = this.cardsoltArr.some(item => {
                return item.btnstatus == 3
              })
              if(istrue){
                for (let index = 0; index < this.$refs.mychild.length; index++) {
                  this.$refs.mychild[index].isApproveFun('hc', contract().HNPool).then(res => {
                    if (res) {
                      this.isapprove = true
                    } else {
                      this.isapprove = false
                    }
                  })
                }
                clearInterval(this.time_btn)
              }else{
                clearInterval(this.time_btn)
              }
            }
          }, 1000)
        } else {
          this.slotArr.forEach(item => {
            item.num = 0
            item.status = false
          })
          this.newCardArr.forEach(item => {
            item.num = 0
            item.status = false
          })
          this.cardsoltArr = this.resetCardsoltArr
          this.initSwiper(1);
          this.initSwiper(2);
          clearInterval(this.time_btn)
        }
      },
      deep: true,
      immediate: true,
    },
  },
  computed: {
    ...mapGetters(["getIstrue", "getAccount", "getUserCardInfo","getIsMobile"]),
  },
  methods: {
    sonapprove (item) {
      if (item.isloading) return
      item.isloading = true
      //console.log('父组件页面调用子组件的授权方法,授权busd', item)
      for (let index = 0; index < this.cardsoltArr.length; index++) {
        const element = this.cardsoltArr[index];
        if(element.btnstatus == 3){
          element.isloading = true
        }
      }
      this.$refs.mychild[0].goApproveFun('hc', contract().HNPool).then(res => {
        if (res) {
          for (let index = 0; index < this.cardsoltArr.length; index++) {
            const element = this.cardsoltArr[index];
            element.isloading = false
          }
          this.isapprove = true
        } else {
          for (let index = 0; index < this.cardsoltArr.length; index++) {
            const element = this.cardsoltArr[index];
            element.isloading = false
          }
          this.isapprove = false
        }
      }).catch(err => {
        for (let index = 0; index < this.cardsoltArr.length; index++) {
          const element = this.cardsoltArr[index];
          element.isloading = false
        }
        this.isapprove = false
      })
      // this.$refs.mychild[0].goApproveFun('busd',contract().HNMarket);
    },
    // 用户总卡牌数据获取
    getUserAllCard(){
      clearInterval(this.timerll)
      this.timerll = setInterval(() => {
        if(sessionStorage.getItem('count')){
          clearInterval(this.timerll)
          this.slotArr = [
            {
              level: 1,
              src: `${this.$store.state.imgUrl}level1.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 1 && data.series == 1;
              }).length,
              status:false
            },
            {
              level: 2,
              src: `${this.$store.state.imgUrl}level2.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 2 && data.series == 1;
              }).length,
              status:false
            },
            {
              level: 3,
              src: `${this.$store.state.imgUrl}level3.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 3 && data.series == 1;
              }).length,
              status:false
            },
            {
              level: 4,
              src: `${this.$store.state.imgUrl}level4.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 4 && data.series == 1;
              }).length,
              status:false
            },
            {
              level: 5,
              src: `${this.$store.state.imgUrl}level5.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 5 && data.series == 1;
              }).length,
              status:false
            },
          ]
          this.newCardArr = [
            {
              level: 1,
              src: `${this.$store.state.imgUrl}newCardLevel1.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 1 && data.series == 2;
              }).length,
              status:false
            },
            {
              level: 2,
              src: `${this.$store.state.imgUrl}newCardLevel2.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 2 && data.series == 2;
              }).length,
              status:false
            },
            {
              level: 3,
              src: `${this.$store.state.imgUrl}newCardLevel3.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 3 && data.series == 2;
              }).length,
              status:false
            },
            {
              level: 4,
              src: `${this.$store.state.imgUrl}newCardLevel4.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 4 && data.series == 2;
              }).length,
              status:false
            },
            {
              level: 5,
              src: `${this.$store.state.imgUrl}newCardLevel5.png`,
              num: JSON.parse(this.getUserCardInfo).filter((data) => {
                return data.level == 5 && data.series == 2;
              }).length,
              status:false
            },
          ]
          this.initSwiper(1);
        }
        // console.log("获取用户信息")
      }, 1000);
    },
    /**初始化swiper */
    initSwiper (index) {
      setTimeout(() => {
        // console.log("移动", index)
        if (index == 1) {
          this.swiper1.slideTo(0, 100, false);
        } else if (index == 2) {
          this.swiper2.slideTo(0, 100, false);
        }
      }, 1000);
    },
    // 取消按钮(关闭弹窗)
    closeFun () {
      this.proupDis = false;
    },
    // 确认按钮(弹窗)
    sureBtnFun () {
      if (this.proupBtnstatus) {
        this.proupDis = false;
        return;
      }
      this.proupDis = false;
      this.PickUpFoxFun();
    },
    // 插入卡槽
    insertClick () {
      if(!this.getIstrue){
        this.$common.selectLang("请先连接钱包", "Please connect the wallet", this);
        return
      }
      this.$router.push({
        path: "/insertcard"
      });
    },
    // 解除卡槽
    removeClick (item) {
      // console.log("解除卡槽item: ", item);
      if (item.isloading) return;
      item.isloading = true;
      hnPool()
        .connect(getSigner())
        .withdraw([item.cardID])
        .then(async (res) => {
          // console.log("解除卡槽res: ", res);
          const etReceipt = await res.wait();
          if (etReceipt.status == 1) {
            this.$common.selectLang("解除成功", "Uninserted successfully", this);
            item.isloading = false;
            this.getCardSlotInfo();
            this.$common.newgetUserCardInfoFun(this.getAccount).then(res1 => {
              if(res1 > 1){
                sessionStorage.setItem("count",res1)
              }else{
                sessionStorage.setItem("count",1)
              }
            })
            // this.$common.getUserCardInfoFun(this.getAccount);
          }
          this.proupBtnstatus = true
        })
        .catch((err) => {
          // console.log("解除卡槽err: ", err);
          item.isloading = false;
          this.proupBtnstatus = true
        });
    },
    // 解锁卡槽
    async Unlock (item) {
      // console.log("item: ", item);
      this.datainfo = item;
      // 获取某用户购买新卡槽的HC金额
      this.proupBtnstatus = false;
      this.buyHCMoney = (
        (await hnPool().getUserSlotPrice(this.getAccount)) / 1e18
      ).toString();
      this.$common.selectLang(
        "本次解锁共需" + this.buyHCMoney + "HC",
        "Unlock Slot needs to consume " + this.buyHCMoney + " HC",
        this
      );
    },
    async PickUpFoxFun () {
      this.datainfo.isloading = true;
      let balance = util.formatEther(await hc().balanceOf(this.getAccount));
      if (Number(balance) < Number(this.buyHCMoney)) {
        this.proupBtnstatus = true;
        this.$common.selectLang("余额不足", "Insufficent Balance", this);
        this.datainfo.isloading = false;
        return;
      }
      hnPool()
        .connect(getSigner())
        .buySlot()
        .then(async (res) => {
          const etReceipt = await res.wait();
          this.proupBtnstatus = true;
          if (etReceipt.status == 1) {
            this.$common.selectLang("解锁成功", "Unlock Successful", this);
            this.getCardSlotInfo();
            this.datainfo.isloading = false;
          }
        })
        .catch(() => {
          this.proupBtnstatus = false;
          this.datainfo.isloading = false;
        });
    },
    // 合成
    synthesisClick () {
      this.$router.push("/synthesis");
    },
    // 转账
    transferClick(){
      this.$router.push("/transfer");
    },
    // 我的卡牌跳转
    jumpDetails (item,data) {
      this.$router.push({
        path: "/carddetails",
        query: { level: item.level, num: item.num, serise:data }
      });
    },
    // 链接钱包才能拿到的数据获取方法
    getCardSlotInfo () {
      this.changeAddress = false
      this.promiseGetCardSlotIfo().then(async res => {
        // console.log('封装返回方法res: ', res);
        this.cardsoltArr = res.arr
        // 获取某用户的总卡槽数量cardSlot
        this.cardSlot = (await hnPool().getUserSlots(this.getAccount)).toString();
        // 获取某用户的空卡槽数量(已经质押的卡槽位不算在其中)
        this.emptyCardSlot = (
          await hnPool().getUserLeftSlots(this.getAccount)
        ).toString();
        // 获取最大卡槽数量
        this.maxCardSlot = (await hnPool().maxSlots()).toString();
        let obj1 = {
          src: "",
          btnstatus: 1, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
          isloading: false,
        };
        // 添加空卡槽
        for (let index1 = 0; index1 < this.emptyCardSlot; index1++) {
          this.cardsoltArr.push(obj1);
        }
        // 是否添加解锁卡槽
        if (Number(this.cardSlot) < Number(this.maxCardSlot)) {
          for (
            let index = 0;
            index < Number(this.maxCardSlot) - Number(this.cardSlot);
            index++
          ) {
            let obj3 = {
              src: `${this.$store.state.imgUrl}cardlock.png`,
              btnstatus: 3, //1---插入卡槽  2------已质押卡槽  3-----解锁卡槽
              isloading: false,
            };
            this.cardsoltArr.push(obj3);
          }
        }
        this.changeAddress = true
        this.initSwiper(2);
      })
    },
    promiseGetCardSlotIfo(){
      return new Promise((resolve,reject) => {
        let count = 1;
        hnPool().getUserHnIdsBySize(this.getAccount,0,100000000).then(res => {
          // console.log('直接返回res: ', res);
          if(res[0].length == 0){
            resolve({'arr': [], 'msg':'Success---No Data'});
            return
          }
          let infoarr = []
          res[0].map(async (item) => {
            let obj = {
              src: "",
              cardID: "",
              level: "",
              hc: "",
              btc: "",
              btnstatus: 2, //设置一个状态供需要的地方使用
              isloading: false, //按钮的loading
              type:'',
              series:''
            };
            obj.cardID = item.toString(); // 卡牌的id
            obj.level = (await hn().level(item.toString())).toString(); // 等级
            let race = await hn().getHashrates(item) // 算力数组
            obj.ultra = (await hn().data(item, 'ultra')) >= 1?true:false
            obj.type = (await hn().getRandomNumber(item, "class", 1, 4)).toString();
            obj.series = (await hn().series(item)).toString() // 系列
            obj.src = getHnImg(Number(item),Number(obj.level),race,obj.ultra,obj.series == 1?'':true)

            infoarr.push(obj)
            if (count == res[0].length) {
              infoarr.sort((a, b) => {
                if(a.level == b.level){
                  return Number(a.type) > Number(b.type) ? 1 : -1;
                }
                return Number(a.level) > Number(b.level) ? -1 : 1;
              })
              resolve({'arr': infoarr, 'msg':'Success'});
            }
            count++;
          })
        }).catch(() => {
          reject({'arr': [], 'msg':'error'});
        })
      })
    }
  },
  beforeDestroy(){
    clearInterval(this.timerll)
    clearInterval(this.time_btn)
  }
};
</script>
<style lang="scss" scoped>
.basic_card_page {
  width: 100%;
  display: flex;
  flex-direction: column;
  margin-top: 70px;
  .myCard {
    width: 100%;
    display: flex;
    flex-direction: column;
    padding: 0 0 0 100px;
    .card_left {
      width: 100%;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      .span1 {
        color: #ffffff;
      }
      .card_btnbox{
        display: flex;
        align-items: center;
        .synthesis_btn {
          width: 144px;
          height: 49px;
          line-height: 49px;
          background-image: url(//cdn.hashland.com/images/nft_btn1.png);
          background-size: 100% 100%;
          background-repeat: no-repeat;
          text-align: center;
          color: #ffffff;
          cursor: pointer;
        }
        .btn_margin_left{
          margin-right: 50px;
        }
      }
    }
    .span2 {
      color: #ccbebe;
    }
  }
  .add_mynewCard{
    margin-top: 60px;
  }
  .swiper-container {
    width: 100%;
    height: auto;
    padding: 0 90px;
    .swiper-wrapper {
      .swiper-slide {
        width: 100%;
        height: 100%;
        .content_box {
          width: 100%;
          min-height: 250px;
          display: flex;
          flex-direction: column;
          justify-content: space-between;
          cursor: pointer;
          .swiper_img {
            width: 86%;
            object-fit: contain;
          }
          .grade_box {
            width: 190px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-top: 20px;
            border-radius: 15px;
            box-shadow: -2px 1px 22px 0px rgba(194, 190, 190, 0.52) inset;
            padding: 4px 8px;
            .five_pointed_star {
              display: flex;
              align-items: center;
              .start_img {
                width: 30px;
                object-fit: contain;
              }
              .level_class {
                color: #ffffff;
                margin-left: 4px;
              }
            }
            .num_details {
              display: flex;
              align-items: center;
              .card_grade {
                color: #ffffff;
                margin-right: 5px;
              }
              .card_grade_loading{
                margin-right: 12px;
              }
              .details {
                padding: 0 10px;
                background: #f5b252;
                color: #ffffff;
                border-radius: 25px;
                cursor: pointer;
              }
            }
          }
        }
        .outbox {
          width: 100%;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: space-between;
          margin-top: 120px;
          .second-content_box {
            position: relative;
            width: 100%;
            height: auto;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-end;
            .swiper_img {
              position: absolute;
              top: 6%;
              left: 48%;
              transform: translate(-50%, -50%);
              width: 210px;
              object-fit: contain;
            }
            .serise2Img{
              width:173px
            }
            .lock_swiper_img {
              position: absolute;
              top: 32%;
              left: 48%;
              transform: translate(-50%, -50%);
              width: 100px;
              object-fit: contain;
            }
            .base_img {
              width: 100%;
              object-fit: contain;
            }
          }
          .btnbox {
            width: 160px;
            display: flex;
            align-items: center;
            justify-content: center;
            line-height: 52px;
            background-size: 100% 100%;
            background-repeat: no-repeat;
            color: #ffffff;
            cursor: pointer;
          }
          .remove_btnbox {
            background-image: url("//cdn.hashland.com/images/nft_btn1.png");
          }
          .insert_btnbox {
            background-image: url("//cdn.hashland.com/images/insert.png");
          }
          .lock_btnbox {
            background-image: url("//cdn.hashland.com/images/lock.png");
          }
        }
      }
    }
    .swiper-button-prev {
      width: 70px;
      height: 67px;
      background: url("//cdn.hashland.com/images/swiper_pre.png") no-repeat;
      background-size: 100% 100%;
    }
    .swiper-button-next {
      width: 70px;
      height: 67px;
      background: url("//cdn.hashland.com/images/swiper_next.png") no-repeat;
      background-size: 100% 100%;
    }
  }
  .self_swiper {
    height: auto;
    min-height: 300px;
    margin-top: 70px;
  }
  .margin_top_card {
    margin-top: 81px;
  }
}
@media screen and (max-width: 980px) {
  .basic_card_page {
    width: 100%;
    display: flex;
    flex-direction: column;
    margin-top: 0.3rem;
    .myCard {
      width: 100%;
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      // justify-content: space-between;
      padding: 0 0.2rem;
      .card_left {
        width: 100%;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        .span1 {
          color: #ffffff;
        }
        .card_btnbox{
          display: flex;
          align-items: center;
          .synthesis_btn {
            width: 0.8rem;
            height: 0.3rem;
            line-height: 0.3rem;
            background-image: url("//cdn.hashland.com/images/nft_btn1.png");
            background-size: 100% 100%;
            background-repeat: no-repeat;
            text-align: center;
            color: #ffffff;
            cursor: pointer;
          }
          .btn_margin_left{
            margin-right: 0.1rem;
          }
        }
      }
      .span2 {
        color: #ccbebe;
      }
    }
    .swiper-container {
      width: 100%;
      height: auto;
      min-height: 0.8rem;
      padding: 0 0.2rem;
      .swiper-wrapper {
        .swiper-slide {
          width: 100%;
          height: 100%;
          .content_box {
            width: 100%;
            min-height: 1rem;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            .swiper_img {
              width: 100%;
              object-fit: contain;
            }
            .grade_box {
              width: 1.45rem;
              padding: 0.04rem 0.05rem;
              display: flex;
              align-items: center;
              margin-top: 0.2rem;
              border-radius: 0.1rem;
              box-shadow: -2px 1px 34px 0px rgba(255, 255, 255, 0.22) inset;
              .five_pointed_star {
                display: flex;
                align-items: center;
                .start_img {
                  width: 0.15rem;
                  object-fit: contain;
                }
                .level_class {
                  color: #ffffff;
                  margin-left: 0.05rem;
                }
              }
              .num_details {
                .card_grade {
                  color: #ffffff;
                  margin: 0 0.04rem;
                }
                .card_grade_loading{
                  margin-right: 0.1rem;
                }
                .details {
                  padding: 0 0.05rem;
                  background: #f5b252;
                  color: #ffffff;
                  border-radius: 0.25rem;
                  cursor: pointer;
                  font-size: 0.12rem;
                }
              }
            }
            .btnbox {
              width: 2rem;
              text-align: center;
              background-size: contain;
              background-repeat: no-repeat;
              color: #ffffff;
              cursor: pointer;
            }
            .remove_btnbox {
              background-image: url("//cdn.hashland.com/images/nft_btn1.png");
            }
            .insert_btnbox {
              background-image: url("//cdn.hashland.com/images/insert.png");
            }
            .lock_btnbox {
              background-image: url("//cdn.hashland.com/images/lock.png");
            }
          }
          .outbox {
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            margin-top: 1rem;
            .second-content_box {
              position: relative;
              width: 100%;
              height: auto;
              display: flex;
              flex-direction: column;
              align-items: center;
              justify-content: flex-end;
              margin-bottom: 0.2rem;
              .swiper_img {
                position: absolute;
                top: -3%;
                left: 50%;
                transform: translate(-50%, -50%);
                width: 100%;
                object-fit: contain;
              }
              .serise2Img{
                width: 85%;
              }
              .base_img {
                width: 100%;
                object-fit: contain;
              }
            }
            .btnbox {
              width: 1.2rem;
              text-align: center;
              background-size: 100% 100%;
              background-repeat: no-repeat;
              color: #ffffff;
              cursor: pointer;
              line-height: 0.35rem;
              font-size: 0.12rem;
            }
            .remove_btnbox {
              background-image: url("//cdn.hashland.com/images/nft_btn1.png");
            }
            .insert_btnbox {
              background-image: url("//cdn.hashland.com/images/insert.png");
            }
            .lock_btnbox {
              background-image: url("//cdn.hashland.com/images/lock.png");
            }
          }
        }
      }
      .swiper-button-prev {
        display: none;
      }
      .swiper-button-next {
        display: none;
      }
    }
    .self_swiper {
      height: auto;
      min-height: 3rem;
      margin-top: 0.2rem;
    }
    .margin_top_card {
      margin-top: 0.3rem;
    }
  }
}
</style>
