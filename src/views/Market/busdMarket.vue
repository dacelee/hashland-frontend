<template>
  <div class="busd_page">
    <!-- 市场信息 -->
    <div class="info_box">
      <div class="onebox" v-for="(item, index) in infoArr" :key="index">
        <p class="p1 fontsize12">{{ $t(item.title) }}</p>
        <p class="p1 fontsize22" v-if="item.loading">
          <NewLoading></NewLoading>
        </p>
        <p class="p1 fontsize22" v-else-if="(index == 0 || index == 4) && !item.loading">
          {{ item.num }}
        </p>
        <p class="p1 fontsize22" v-else>$ {{ item.num }}</p>
      </div>
    </div>
    <div class="content">
      <!-- 排序  卡牌信息 -->
      <div class="leftboxs">
        <div class="add_leftbox">
          <!-- 系列排序 -->
          <div class="left_content sercise_content" :class="[disablehover ? 'clear_hover' : '']">
            <span class="span1 fontsize14">{{ $t(seriesTxt) }}</span>
            <div class="span2"></div>
            <div class="left_content_hover sercise_content_hover">
              <span class="span1 fontsize14" @click="seriesFun(ele)" v-for="(ele, index) in seriesArr" :key="index">{{ $t(ele.label) }}</span>
            </div>
          </div>
          <!-- 等级排序 -->
          <div class="left_content" :class="[disablehover ? 'clear_hover' : '']">
            <span class="span1 fontsize14">{{ $t("message.synthesis.txt4") }} {{ rank }} ({{ $t("message.synthesis.txt8") }} {{ amount }})</span>
            <div class="span2"></div>
            <div class="left_content_hover">
              <span class="span1 fontsize14" @click="selectRankClik(ele, index)" v-for="(ele, index) in cardLengthArr" :key="index">
                {{ $t("message.synthesis.txt4") }} {{ index + 1 }} ({{ $t("message.synthesis.txt8") }} {{ ele }})
              </span>
            </div>
          </div>
          <!-- 职业排序 -->
          <div class="left_content sercise_content" :class="[disablehover ? 'clear_hover' : '']">
            <span class="span1 fontsize14">{{ $t(occupationTxt) }}</span>
            <div class="span2"></div>
            <div class="left_content_hover sercise_content_hover">
              <span class="span1 fontsize14" @click="occupationFun(ele)" v-for="(ele, index) in occupationArr" :key="index">{{ $t(ele.name) }}</span>
            </div>
          </div>
          <!-- 类型排序 -->
          <div class="left_content" :class="[disablehover ? 'clear_hover' : '']">
            <span class="span1 fontsize14">{{ $t(ultraTxt) }}</span>
            <div class="span2"></div>
            <div class="left_content_hover">
              <span class="span1 fontsize14" @click="ultraFun(ele)" v-for="(ele, index) in ultraArr" :key="index">{{ $t(ele.label) }}</span>
            </div>
          </div>
        </div>
        <div class="add_leftbox pc_add_leftbox">
          <!-- 筛选-->
          <div class="left_content_price" :class="[disablehover ? 'clear_hover' : '']" v-for="(item, index) in orderArr" :key="index">
            <span class="span1 fontsize14">{{ $t(item.name) }}</span>
            <div class="span2"></div>
            <div class="left_content_hover">
              <span class="span1 fontsize14" v-for="(ele, index1) in item.arr" :key="index1" @click="sortPriceClik(item, ele)">
                {{ $t(ele.name) }}
              </span>
            </div>
          </div>
        </div>
      </div>
      <!-- 去挂单 -->
      <div class="right_content">
        <img :src="`${$store.state.imgUrl}record.png`" class="record_img" @click="recordClick" />
        <div class="synthesis_btn fontsize16" @click="goOrder">
          {{ $t("message.market.txt4") }}
        </div>
      </div>
    </div>
    <!-- 移动端筛选 -->
    <div class="mobile_add_leftbox">
      <!-- 筛选-->
      <div class="left_content_price" :class="[disablehover ? 'clear_hover' : '']" v-for="(item, index) in orderArr" :key="index">
        <span class="span1 fontsize14">{{ $t(item.name) }}</span>
        <div class="span2"></div>
        <div class="left_content_hover">
          <span class="span1 fontsize14" v-for="(ele, index1) in item.arr" :key="index1" @click="sortPriceClik(item, ele)">
            {{ $t(ele.name) }}
          </span>
        </div>
      </div>
      <div class="right_content">
        <img :src="`${$store.state.imgUrl}record.png`" class="record_img" @click="recordClick" />
        <div class="synthesis_btn fontsize16" @click="goOrder">
          {{ $t("message.market.txt4") }}
        </div>
      </div>
    </div>
    <!-- 页面展示卡牌盒子 -->
    <div class="show_gameArr" ref="showBoxRef">
      <div class="onebox" :class="{margin0:index % 4 == 3 }" v-for="(item, index) in pageshowarr" :key="index">
        <div class="imgbox">
          <img :src="item.loading ? item.src : `${$store.state.imgUrl}defaultcard.png`" class="img" :class="{serise2Img:item.series == 1}"/>
          <Lottie :options="anmationArr.filter(ele => {return ele.level == item.level && ele.type == item.hnClass})[0].dataJson" :width="getIsMobile?256:''" v-if="item.ultra && item.loading"></Lottie>
        </div>
        <div class="bottom_box">
          <div class="left_price">
            <img :src="`${$store.state.imgUrl}bsc.png`" class="bsc_img" />
            <span class="span1 fontsize16">{{ item.price }} BUSD</span>
          </div>
          <div class="btn fontsize12" :class="{ authorize_class: !isapprove && item.isstatus }">
            <Btn
              :isapprove="isapprove"
              :approveloading="item.isstatus"
              :isloading="item.isstatus"
              :word="'Buy'"
              ref="mychild"
              @sonapprove="sonapprove(item)"
              @dosomething="buyCard(item)"
            />
          </div>
        </div>
      </div>
      <LoadingAnmation v-if="pageshowarr.length == 0 && pageshowLoading"></LoadingAnmation>
      <NoData v-else-if="pageshowarr.length == 0 && !pageshowLoading" :isshow="false"></NoData>
      <div class="bottom_loading" v-if="pageshowarr.length > 10">
        <span class="fontsize16" v-if="!nodata && pulldown">Pull up to load more</span>
        <span class="fontsize16" v-else-if="!nodata && !pulldown">Loading...</span>
        <span class="fontsize16" v-else-if="nodata">End</span>
      </div>
    </div>
    <Proup :btntxt="btntxt" :word="word" @besurefun="CloseFun" :proupDis="proupDis" @closedis="CloseFun"></Proup>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import { hnMarketInfo, hnMarket, getHnImg, erc20, token, contract, getSigner, util } from "hashland-sdk";
export default {
  data() {
    return {
      anmationArr:[],//动画数组的json
      disablehover: false,
      occupationTxt: "message.market.txt9", //职业排序
      occupationArr: [
        { name: "message.market.txt9", describe: 0 },
        { name: "message.market.txt10", describe: 1 },
        { name: "message.market.txt11", describe: 2 },
        { name: "message.market.txt12", describe: 3 },
        { name: "message.market.txt13", describe: 4 },
      ], //职业排序数组
      cardLengthArr: [0, 0, 0, 0, 0], // 几阶对应的数量数组
      pulldown: true, // 上拉加载变量
      pageshowLoading: true, // 数据没有加载完之前显示loading
      pageshowarr: [], //页面展示数组
      rank: 1, //1阶
      amount: 0, //阶对应的卡牌数量
      selectedNUM: 0, //选中的卡牌数量
      btntxt: "", // 弹窗页面的确认按钮
      word: "", //弹窗提示文字
      proupDis: false, // 弹窗展示消失变量
      isapprove: false, // 是否授权busd
      user_busd_balance: 0, //用户busd余额
      time_btn: null, //判断是否授权按钮的定时器
      infoArr: [
        { title: "message.market.txt5", num: 0, loading: true },
        { title: "message.market.txt6", num: 0, loading: true },
        { title: "message.market.txt7", num: 0, loading: true },
        { title: "message.market.txt24", num: 0, loading: true },
        { title: "message.market.txt8", num: 0, loading: true },
      ],
      timeoutOBJ: null, // 下拉加载定时器对象
      nodata: false, // 没有数据展示字段
      isPull: false, // 是否可以再次上拉字段
      sortObj: {
        first: 40, //查询结果数量，比如填10，就展示前10个结果
        skip: 0, //跳过结果数量，用于分页，比如填50，相当于从第6页开始
        orderBy: "sellTime", // 排序字段，填字段名，所有字段见下文查询结果
        orderDirection: "desc", // 降序or升序，填desc或asc
        level: 1, // 按等级筛选，填1-5
        hnClass: "", // 按职业筛选，填1-4
        seller: "", // 按卖家筛选，填钱包地址
        series: null,
        ultra: null,
      },
      seriesTxt: "message.market.txt36",
      seriesArr: [
        { label: "message.market.txt36", value: null },
        { label: "message.market.txt37", value: 1 },
        { label: "message.market.txt38", value: 2 },
      ],
      ultraTxt: "message.market.txt39",
      ultraArr: [
        { label: "message.market.txt39", value: null },
        { label: "message.market.txt40", value: false },
        { label: "message.market.txt41", value: true },
      ],
    };
  },
  computed: {
    ...mapGetters(["getIstrue", "getAccount", "getCoinPrice","getIsMobile"]),
    orderArr() {
      if (this.rank == 1) {
        return [
          {
            name: "message.market.txt20",
            arr: [
              { name: "message.market.txt17", describe: "btc_desc" },
              { name: "message.market.txt17_1", describe: "btc_asc" },
              { name: "message.market.txt19", describe: "price_desc" },
              { name: "message.market.txt19_1", describe: "price_asc" },
              { name: "message.market.txt20", describe: "time_desc" },
              { name: "message.market.txt20_1", describe: "time_asc" },
            ],
          },
        ];
      } else {
        return [
          {
            name: "message.market.txt20",
            arr: [
              { name: "message.market.txt17", describe: "btc_desc" },
              { name: "message.market.txt17_1", describe: "btc_asc" },
              { name: "message.market.txt18", describe: "hc_desc" },
              { name: "message.market.txt18_1", describe: "hc_asc" },
              { name: "message.market.txt19", describe: "price_desc" },
              { name: "message.market.txt19_1", describe: "price_asc" },
              { name: "message.market.txt20", describe: "time_desc" },
              { name: "message.market.txt20_1", describe: "time_asc" },
            ],
          },
        ];
      }
    },
  },
  watch: {
    getIstrue: {
      handler: function (newValue) {
        if (newValue) {
          this.connectInfo();
          clearInterval(this.time_btn);
          this.time_btn = setInterval(() => {
            if (this.pageshowarr.length > 0) {
              clearInterval(this.time_btn);
              for (let index = 0; index < this.$refs.mychild.length; index++) {
                this.$refs.mychild[index].isApproveFun("BUSD", contract().HNMarket).then((res) => {
                  // console.log("shishou:",res)
                  if (res) {
                    this.isapprove = true;
                  } else {
                    this.isapprove = false;
                  }
                });
              }
            }
            // console.log("链接状态为true时判断是否授权")
          }, 1000);
        }
      },
      deep: true,
      immediate: true,
    },
    pageshowarr: {
      handler: function (newValue) {
        // console.log('pageshowarr----newValue: ', newValue);
        if (newValue.length > 0) {
          newValue.map((item) => {
            let newImg = new Image();
            newImg.src = item.src;
            newImg.onload = () => {
              // 图片加载成功后把地址给原来的img
              item.loading = true;
            };
          });
        }
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    sonapprove(item) {
      if (item.isstatus) return;
      item.isstatus = true;
      // console.log('父组件页面调用子组件的授权方法,授权busd', item)
      for (let index = 0; index < this.pageshowarr.length; index++) {
        const element = this.pageshowarr[index];
        element.isstatus = true;
      }
      this.$refs.mychild[0]
        .goApproveFun("BUSD", contract().HNMarket)
        .then((res) => {
          if (res) {
            for (let index = 0; index < this.pageshowarr.length; index++) {
              const element = this.pageshowarr[index];
              element.isstatus = false;
            }
            this.isapprove = true;
          } else {
            for (let index = 0; index < this.pageshowarr.length; index++) {
              const element = this.pageshowarr[index];
              element.isstatus = false;
            }
            this.isapprove = false;
          }
        })
        .catch(() => {
          for (let index = 0; index < this.pageshowarr.length; index++) {
            const element = this.pageshowarr[index];
            element.isstatus = false;
          }
          this.isapprove = false;
        });
    },
    // 购买卡牌
    async buyCard(item) {
      if (item.isstatus) return
      item.isstatus = true
      let issell = await hnMarket().getSellerHnIdExistence(item.seller, item.hnId)
      if (!issell) {
        item.isstatus = false
        this.$common.selectLang("当前卡牌已售出", "The NFT has been sold. Choose another.", this)
        return;
      } // 判断当前卡是否正在出售
      if (Number(this.user_busd_balance) >= Number(item.price)) {
        let arr = [];
        arr.push(item.hnId);
        hnMarket()
          .connect(getSigner())
          .buy(arr)
          .then(async (ele) => {
            const etReceipt = await ele.wait();
            if (etReceipt.status == 1) {
              this.$common.newgetUserCardInfoFun(this.getAccount).then((res1) => {
                // console.log('重新获取用户卡牌信息res1: ', res1);
                sessionStorage.removeItem("count");
                if (res1 > 1) {
                  sessionStorage.setItem("count", res1);
                } else {
                  sessionStorage.setItem("count", 1);
                }
              });
              this.sortObj.skip = 0;
              this.getDatabaseaFun(this.sortObj).then((data) => {
                if (data.status == 0) {
                  this.pageshowarr = data.arr;
                  this.nodata = false;
                  this.pulldown = true;
                  this.$common.selectLang("购买成功", "Purchase successfully. Check the NFTs on NFT Card later", this);
                  this.getSDKInfo();
                  this.connectInfo();
                  item.isstatus = false;
                } else if (data.status == 1) {
                  this.pageshowarr = [];
                  this.nodata = true;
                }
              });
            } else {
              item.isstatus = false;
            }
          })
          .catch(() => {
            item.isstatus = false;
          });
      } else {
        item.isstatus = false
        this.$common.selectLang("余额不足", "Insufficent Balance", this);
      }
    },
    // 选择阶数
    selectRankClik(ele, index) {
      if (this.pageshowLoading) return;
      this.disablehover = true;
      setTimeout(() => {
        this.disablehover = false;
      }, 600);
      this.rank = index + 1; // 当前几阶
      this.amount = ele;
      this.pulldown = true; // 上拉加载变量
      this.pageshowLoading = true; // 数据秘没有加载玩之前显示loading
      this.pageshowarr = [];
      this.nodata = false;
      this.sortObj.level = this.rank; // 选择当前等级
      this.sortObj.skip = 0; // 当前页数重置为0
      this.encapsulationFun("ccupation");
    },
    // 职业排序
    occupationFun(ele) {
      this.disablehover = true;
      setTimeout(() => {
        this.disablehover = false;
      }, 600);
      // console.log('ele: ', ele);
      this.occupationTxt = ele.name;
      if (ele.describe == 0) {
        this.sortObj.hnClass = "";
      } else {
        this.sortObj.hnClass = ele.describe;
      }
      this.sortObj.skip = 0;
      this.pageshowLoading = true;
      this.encapsulationFun();
    },
    // 筛选全部系列
    seriesFun(ele) {
      this.disablehover = true;
      setTimeout(() => {
        this.disablehover = false;
      }, 600);
      this.seriesTxt = ele.label;
      this.sortObj.series = ele.value;
      this.sortObj.skip = 0;
      this.encapsulationFun();
    },
    // 筛选是否u卡
    ultraFun(ele) {
      // console.log('筛选全部卡牌类型ele: ', ele);
      this.disablehover = true;
      setTimeout(() => {
        this.disablehover = false;
      }, 600);
      this.ultraTxt = ele.label;
      this.sortObj.ultra = ele.value;
      this.sortObj.skip = 0;
      this.encapsulationFun();
    },
    // 筛选
    sortPriceClik(item, data) {
      this.disablehover = true;
      setTimeout(() => {
        this.disablehover = false;
      }, 600);
      // console.log('data: ',item, data);
      item.name = data.name;
      this.sortObj.skip = 0;
      this.pageshowLoading = true;
      switch (data.describe) {
        case "btc_desc":
          this.sortObj.orderBy = "btcHashrate";
          this.sortObj.orderDirection = "desc";
          this.encapsulationFun();
          break;
        case "btc_asc":
          this.sortObj.orderBy = "btcHashrate";
          this.sortObj.orderDirection = "asc";
          this.encapsulationFun();
          break;
        case "hc_desc":
          this.sortObj.orderBy = "hcHashrate";
          this.sortObj.orderDirection = "desc";
          this.encapsulationFun();
          break;
        case "hc_asc":
          this.sortObj.orderBy = "hcHashrate";
          this.sortObj.orderDirection = "asc";
          this.encapsulationFun();
          break;
        case "price_desc":
          this.sortObj.orderBy = "price";
          this.sortObj.orderDirection = "desc";
          this.encapsulationFun();
          break;
        case "price_asc":
          this.sortObj.orderBy = "price";
          this.sortObj.orderDirection = "asc";
          this.encapsulationFun();
          break;
        case "time_desc":
          this.sortObj.orderBy = "sellTime";
          this.sortObj.orderDirection = "desc";
          this.encapsulationFun();
          break;
        case "time_asc":
          this.sortObj.orderBy = "sellTime";
          this.sortObj.orderDirection = "asc";
          this.encapsulationFun();
          break;
        default:
          break;
      }
    },
    // 排序--筛选--封装函数
    encapsulationFun() {
      this.pageshowarr = [];
      this.pageshowLoading = true;
      this.getDatabaseaFun(this.sortObj)
        .then((res) => {
          // 页面加载的时候  查询第一页(0)的数据--每页展示1条数据(测试)
          // if(type == 'ccupation'){
          this.sortObj.skip += this.sortObj.first;
          // }
          if (res.status == 0) {
            this.pageshowarr = res.arr;
            this.pageshowLoading = false;
          } else if (res.status == 1) {
            this.pageshowarr = [];
            this.pageshowLoading = false;
          }
        })
        .catch(() => {
          this.pageshowarr = [];
          this.pageshowLoading = false;
        });
    },
    // 去挂单
    goOrder() {
      this.$router.push("/hangingorder");
    },
    // 挂单记录
    recordClick() {
      // this.$router.push("/pendingrecord");
      this.$router.push({
        path: "/pendingrecord",
        query: { type:'busd' }
      });
    },
    // 取消按钮(关闭弹窗)
    CloseFun() {
      this.proupDis = false;
    },
    //获取sdk信息
    async getSDKInfo() {
      // 获取市场正在出售的各等级的所有HN卡牌数量数组
      hnMarket()
        .getEachLevelHnIdsLength(5)
        .then((res) => {
          // console.log('获取市场正在出售的各等级的所有HN卡牌数量数组res: ', res);
          this.cardLengthArr = res;
          this.amount = res[this.rank - 1];
        });
      // 获取市场正在出售的所有HN卡牌数量
      hnMarket()
        .getHnIdsLength()
        .then((res) => {
          // console.log('获取市场正在出售的所有HN卡牌数量: ', res)
          this.infoArr[4].num = res.toNumber();
          this.infoArr[4].loading = false;
        })
      // 获取总成交次数
      hnMarket()
        .totalSellCount()
        .then((res) => {
          this.infoArr[0].num = res;
          this.infoArr[0].loading = false;
        })
      // 获取总成交额，除1e18
      hnMarket()
        .totalSellAmount()
        .then((res) => {
          let amount = this.$common.convertBigNumberToNormal(res.toString(), 2);
          // console.log('amount: ', amount);
          hnMarket()
            .totalFeeAmount()
            .then((data) => {
              // console.log('手续费金额data: ', data);
              let fee = this.$common.convertBigNumberToNormal(data.toString(), 2);
              this.infoArr[1].num = this.$common.getBit(Number(amount) + Number(fee));
              this.infoArr[1].loading = false;
            });
        })
      // 地板价
      hnMarketInfo
        .getSellInfo(1, 0, "price", "asc")
        .then((res) => {
          // console.log('地板价res: ', res);
          if (res.data.sellInfos.length == 0) {
            this.infoArr[2].num = 0;
          } else {
            this.infoArr[2].num = this.$common.convertBigNumberToNormal(res.data.sellInfos[0].price.toString(), 2);
          }
          this.infoArr[2].loading = false;
        })
      //最新成交价
      hnMarketInfo
        .getBuyInfo(1, 0, "buyTime", "desc")
        .then((res) => {
          // console.log('最新成交价res: ', res);
          this.infoArr[3].num = this.$common.convertBigNumberToNormal(res.data.buyInfos[0].price.toString(), 2);
          this.infoArr[3].loading = false;
        })
        .catch(() => {
          this.infoArr[3].num = 0;
          this.infoArr[3].loading = false;
        });
    },
    connectInfo() {
      // 获取用户余额
      erc20(token().BUSD)
        .balanceOf(this.getAccount)
        .then((res) => {
          this.user_busd_balance = util.formatEther(res);
        });
    },
    // 监听盒子的滚动事件
    listenerBoxScroll() {
      this.$refs.showBoxRef.addEventListener("scroll", () => {
        if (this.pageshowarr.length == 0) return;
        if (this.$refs.showBoxRef.scrollTop + 50 > this.$refs.showBoxRef.scrollHeight - this.$refs.showBoxRef.offsetHeight) {
          if (this.timeoutOBJ) {
            clearTimeout(this.timeoutOBJ);
            if (this.isPull || this.nodata) return;
            this.pulldown = false; // 显示加载中
            this.timeoutOBJ = setTimeout(() => {
              this.pulldownFun(this.sortObj.skip);
            }, 500);
          } else {
            if (this.isPull || this.nodata) return;
            this.pulldown = false; // 显示加载中
            this.timeoutOBJ = setTimeout(() => {
              this.pulldownFun(this.sortObj.skip);
            }, 300);
          }
        }
      });
    },
    pulldownFun(num) {
      // console.log('上拉加载传进来的num: ', num);
      this.isPull = true;
      this.sortObj.skip = num;
      this.getDatabaseaFun(this.sortObj)
        .then((res) => {
          this.isPull = false;
          this.sortObj.skip += this.sortObj.first;
          if (res.status == 0) {
            this.pageshowarr = this.pageshowarr.concat(res.arr);
            this.pulldown = true;
            this.nodata = false;
          } else if (res.status == 1) {
            this.nodata = true;
          }
        })
        .catch(() => {
          this.pageshowarr = [];
          this.nodata = true;
        });
    },
    // 合约数据库信息
    getDatabaseaFun(sortObj) {
      // console.log("sortObj.series-sortObj.ultra", sortObj.series, sortObj.ultra);
      //first?: number,  //查询结果数量，比如填10，就展示前10个结果
      //skip?: number,  //跳过结果数量，用于分页，比如填50，相当于从第6页开始
      //orderBy?: string,  // 排序字段，填字段名，所有字段见下文查询结果
      //orderDirection?: string,  // 降序or升序，填desc或asc
      //level?: number, // 按等级筛选，填1-5
      //hnClass?: number, // 按职业筛选，填1-4
      //seller?: string, // 按卖家筛选，填钱包地址
      // series?: number | undefined, ultra?: boolean | undefined
      // series   series不传就是全部系列，传1就是筛选系列1，传2就是筛选系列2；
      // ultra    ultra不传就是全部卡牌类型，传true就是ultra卡，传false就是普通卡
      return new Promise((resolve, reject) => {
        hnMarketInfo
          .getSellInfo(
            sortObj.first,
            sortObj.skip,
            sortObj.orderBy,
            sortObj.orderDirection,
            sortObj.level,
            sortObj.hnClass,
            sortObj.seller,
            sortObj.series,
            sortObj.ultra
          )
          .then((res) => {
            // console.log("合约数据库返回信息res: ", res);
            if (res.data.sellInfos.length > 0) {
              const arr = JSON.parse(JSON.stringify(res.data.sellInfos));
              arr.forEach((element) => {
                element.loading = false; // 图片加载的loading
                element.isstatus = false; // 授权以及其他按钮操作的loading
                element.src = getHnImg(element.hnId, element.level, [element.hcHashrate, element.btcHashrate],element.ultra,element.series == 1?'':true);
                element.price = this.$common.convertBigNumberToNormal(element.price.toString(), 2);
              });
              resolve({ status: 0, arr: arr, msg: "Success" });
            } else {
              resolve({ status: 1, msg: "No data" });
            }
          })
          .catch(() => {
            reject({ status: 1, msg: "Fail" })
          });
      })
    }
  },
  mounted() {
    let timerObject = setInterval(() => {
      if(localStorage.getItem('Animation')){
        this.anmationArr = JSON.parse(localStorage.getItem('Animation'))
        clearInterval(timerObject)
      }
    },1000)
    this.getSDKInfo()
    this.$nextTick(() => {
      this.listenerBoxScroll()
    })
    this.encapsulationFun("ccupation")
  }
}
</script>

<style lang='scss' scoped>
.busd_page {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  .info_box {
    width: 100%;
    display: flex;
    align-items: center;
    margin-top: 54px;
    justify-content: space-between;
    .onebox {
      width: 190px;
      height: 115px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;
      background: #021e3e;
      box-shadow: 0px 2px 13px 0px rgba(2, 18, 35, 0.68);
      border-radius: 4px;
      padding: 28px 0 25px;
      cursor: pointer;
      .p1 {
        color: #ffffff;
      }
    }
    .onebox:hover {
      box-shadow: 10px 5px 10px 5px rgba(2, 18, 35, 0.68);
    }
  }
  .content {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 71px;
    .leftboxs {
      width: 80%;
      display: flex;
      align-items: center;
      justify-content: space-between;
      .add_leftbox {
        display: flex;
        .left_content {
          position: relative;
          width: 168px;
          height: 44px;
          background: #031224;
          box-shadow: 5px 30px 31px 0px rgba(0, 0, 0, 0.18), -2px 1px 8px 0px rgba(194, 190, 190, 0.52) inset;
          border-radius: 9px;
          padding: 0 10px;
          display: flex;
          align-items: center;
          justify-content: space-between;
          cursor: pointer;
          margin-right: 15px;
          .span1 {
            color: #ffffff;
            font-weight: bold;
          }
          .span2 {
            border-width: 7px;
            border-color: #00e7f0;
            border-bottom-width: 0;
            border-style: dashed;
            border-top-style: solid;
            border-left-color: transparent;
            border-right-color: transparent;
          }
          .left_content_hover {
            position: absolute;
            top: -3px;
            left: 0;
            z-index: 9;
            width: 168px;
            display: none;
            flex-direction: column;
            justify-content: space-between;
            background: rgba(0, 0, 0, 0.9);
            box-shadow: -1px 14px 9px -9px rgba(24, 24, 24, 0.56);
            filter: blur(0px);
            border-radius: 4px;
            padding: 10px;
            margin-top: 47px;
            line-height: 39px;
            .span1 {
              color: #e2dada;
              cursor: pointer;
            }
          }
        }
        .sercise_content{
          width: 120px;
          .sercise_content_hover{
            width: 120px;
          }
        }
        .left_content:hover,
        .left_content_price:hover {
          .left_content_hover {
            display: flex;
            .span1:hover {
              color: #00e7f0;
            }
          }
        }
        .left_content_price {
          margin-right: 15px;
          width: 260px;
          position: relative;
          height: 44px;
          background: #031224;
          box-shadow: 5px 30px 31px 0px rgba(0, 0, 0, 0.18), -2px 1px 8px 0px rgba(194, 190, 190, 0.52) inset;
          border-radius: 9px;
          padding: 0 10px;
          display: flex;
          align-items: center;
          justify-content: space-between;
          cursor: pointer;
          .span1 {
            color: #ffffff;
            margin-right: 10px;
            font-weight: bold;
          }
          .span2 {
            border-width: 7px;
            border-color: #00e7f0;
            border-bottom-width: 0;
            border-style: dashed;
            border-top-style: solid;
            border-left-color: transparent;
            border-right-color: transparent;
          }
          .left_content_hover {
            position: absolute;
            top: -3px;
            left: 0;
            z-index: 9;
            width: 260px;
            display: none;
            flex-direction: column;
            justify-content: space-between;
            background: rgba(0, 0, 0, 0.9);
            box-shadow: -1px 14px 9px -9px rgba(24, 24, 24, 0.56);
            filter: blur(0px);
            border-radius: 4px;
            padding: 10px;
            margin-top: 47px;
            line-height: 39px;
            .span1 {
              color: #e2dada;
              cursor: pointer;
            }
          }
        }
      }
    }
    .right_content {
      display: flex;
      align-items: center;
      cursor: pointer;
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
      .record_img {
        width: 40px;
        object-fit: contain;
        margin-right: 20px;
      }
    }
  }
  .mobile_add_leftbox {
    display: none;
  }
  .show_gameArr {
    margin-top: 40px;
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    overflow-y: scroll;
    max-height: 850px;
    .onebox {
      width: 256px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      align-items: center;
      margin-right: 40px;
      margin-bottom: 30px;
      min-height: 250px;
      .imgbox{
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;
        .img {
          width: 100%;
          object-fit: contain;
        }
        .serise2Img{
          width: 213px;
        }
      }
      .bottom_box {
        width: 90%;
        margin-top: 20px;
        border-radius: 15px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        box-shadow: -2px 1px 22px 0px rgba(194, 190, 190, 0.52) inset;
        padding: 4px 8px;
        .left_price {
          display: flex;
          align-items: center;
          .bsc_img {
            width: 24px;
            object-fit: contain;
            margin-right: 4px;
          }
          .span1 {
            color: #ffffff;
          }
        }
        .btn {
          min-width: 56px;
          height: 22px;
          border-radius: 15px;
          display: flex;
          justify-content: center;
          align-items: center;
          background: #dd9005;
          color: #ffffff;
          cursor: pointer;
        }
      }
    }
    .bottom_loading {
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #ffffff;
      margin-top: 60px;
    }
  }
}
@media screen and (min-width: 1280px) {
  .busd_page {
    max-width: 1162px;
    margin: 0 auto;
  }
}
@media screen and (max-width: 980px) {
  .busd_page {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    .info_box {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
      margin-top: 0.33rem;
      .onebox {
        width: 1.49rem;
        height: 0.84rem;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        background: #021e3e;
        box-shadow: 0px 2px 13px 0px rgba(2, 18, 35, 0.68);
        border-radius: 0.04rem;
        padding: 0.18rem 0;
        margin-right: 0;
        margin-bottom: 0.18rem;
        .p1 {
          color: #ffffff;
        }
      }
    }
    .content {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 0.23rem;
      .leftboxs {
        width: 100%;
        display: flex;
        .add_leftbox {
          width: 100%;
          display: flex;
          align-items: center;
          justify-content: space-between;
          flex-wrap: wrap;

          .left_content {
            position: relative;
            width: 1.62rem;
            height: 0.27rem;
            background: #031224;
            box-shadow: 5px 30px 31px 0px rgba(0, 0, 0, 0.18), -2px 1px 8px 0px rgba(194, 190, 190, 0.52) inset;
            border-radius: 0.06rem;
            padding: 0 0.05rem;
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-right: 0;
            margin-bottom: 0.1rem;
            .span1 {
              color: #ffffff;
              margin-right: 0.1rem;
              font-size: 0.12rem;
            }
            .span2 {
              border-width: 0.07rem;
              border-color: #00e7f0;
              border-bottom-width: 0;
              border-style: dashed;
              border-top-style: solid;
              border-left-color: transparent;
              border-right-color: transparent;
            }
            .left_content_hover {
              position: absolute;
              top: 0;
              left: 0;
              z-index: 9;
              width: 1.62rem;
              display: none;
              flex-direction: column;
              justify-content: space-between;
              background: rgba(0, 0, 0, 0.9);
              box-shadow: -1px 14px 9px -9px rgba(24, 24, 24, 0.56);
              filter: blur(0px);
              border-radius: 0.04rem;
              padding: 0.1rem;
              margin-top: 0.28rem;
              line-height: 0.3rem;
              .span1 {
                color: #e2dada;
                cursor: pointer;
                font-size: 0.12rem;
              }
            }
          }
          .left_content_price {
            display: none;
          }
          .left_content:hover {
            .left_content_hover {
              display: flex;
              .span1:hover {
                color: #00e7f0;
              }
            }
          }
        }
        .pc_add_leftbox {
          display: none;
        }
      }
      .right_content {
        display: none;
      }
    }
    .mobile_add_leftbox {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      .left_content_price:hover {
        .left_content_hover {
          display: flex;
          .span1:hover {
            color: #00e7f0;
          }
        }
      }
      .left_content_price {
        width: 1.62rem;
        position: relative;
        height: 0.27rem;
        background: #031224;
        box-shadow: 5px 30px 31px 0px rgba(0, 0, 0, 0.18), -2px 1px 8px 0px rgba(194, 190, 190, 0.52) inset;
        border-radius: 0.09rem;
        padding: 0 0.05rem;
        display: flex;
        align-items: center;
        justify-content: space-between;
        cursor: pointer;
        .span1 {
          color: #ffffff;
          margin-right: 0;
          font-size: 0.12rem;
        }
        .span2 {
          border-width: 0.07rem;
          border-color: #00e7f0;
          border-bottom-width: 0;
          border-style: dashed;
          border-top-style: solid;
          border-left-color: transparent;
          border-right-color: transparent;
        }
        .left_content_hover {
          position: absolute;
          top: 0;
          left: 0;
          z-index: 9;
          width: 1.62rem;
          display: none;
          flex-direction: column;
          justify-content: space-between;
          background: rgba(0, 0, 0, 0.9);
          box-shadow: -1px 14px 9px -9px rgba(24, 24, 24, 0.56);
          filter: blur(0px);
          border-radius: 0.04rem;
          padding: 0.1rem 0.01rem;
          margin-top: 0.3rem;
          line-height: 0.39rem;
          .span1 {
            color: #e2dada;
            cursor: pointer;
            font-size: 0.12rem;
          }
        }
      }
      .right_content {
        display: flex;
        align-items: center;
        .synthesis_btn {
          width: 1.2rem;
          height: 0.3rem;
          line-height: 0.3rem;
          background-image: url(//cdn.hashland.com/images/nft_btn1.png);
          background-size: 100% 100%;
          background-repeat: no-repeat;
          text-align: center;
          color: #ffffff;
          cursor: pointer;
          font-size: 0.12rem;
        }
        .record_img {
          width: 0.3rem;
          object-fit: contain;
          margin-right: 0.1rem;
        }
      }
    }
    .show_gameArr {
      margin-top: 0.3rem;
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      overflow-y: scroll;
      max-height: 7rem;
      .onebox {
        width: 1.6rem;
        display: flex;
        flex-direction: column;
        margin-right: 0;
        margin-bottom: 0.2rem;
        min-height: 1.5rem;
        .imgbox{
          position: relative;
          display: flex;
          justify-content: center;
          align-items: center;
          .img {
            width: 100%;
            object-fit: contain;
          }
          .serise2Img{
            width: 80%;
          }
        }
        .bottom_box {
          width: 100%;
          margin-top: 0.1rem;
          border-radius: 0.15rem;
          display: flex;
          align-items: center;
          justify-content: space-between;
          box-shadow: -2px 1px 22px 0px rgba(194, 190, 190, 0.52) inset;
          padding: 0.1rem 0.04rem;
          .left_price {
            display: flex;
            align-items: center;
            .bsc_img {
              width: 0.18rem;
              object-fit: contain;
              margin-right: 0.02rem;
            }
            .span1 {
              color: #ffffff;
              font-size: 0.12rem;
            }
          }
          .btn {
            min-width: 0.56rem;
            height: 0.22rem;
            border-radius: 0.15rem;
            display: flex;
            justify-content: center;
            align-items: center;
            background: #dd9005;
            color: #ffffff;
            cursor: pointer;
          }
          .authorize_class {
            min-width: 0.9rem;
          }
        }
      }
      .bottom_loading {
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #ffffff;
      }
    }
  }
}
</style>
