<template>
  <div class="page" ref="ranking_page" @touchstart="showPoolSelect = showPveSelect = showPvpSelect = false">
    <!-- Reward Vault -->
    <div class="ranking_box ranking_box_box1 pc">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text90") }} </span>
        <span>{{ $t("message.gameFi.text105") }} </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="col1">
              <div class="row select_list" @mouseover="showPoolSelect = true" @mouseleave="showPoolSelect = false">
                <div>
                  <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                  <template v-if="issueList.length > 1">
                    <img class="accrow" :class="{ active: showPoolSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                    <transition name="select-scaleY" appear>
                      <ul class="list" v-show="showPoolSelect">
                        <li v-for="(item, index) in issueList" :key="index" @click="poolSelectSeason(item)">
                          {{ $t("message.gameFi.text91") }} {{ item.issue }}
                        </li>
                      </ul>
                    </transition>
                  </template>
                </div>
              </div>
              <div class="row">
                <div>{{ $t("message.gameFi.text92") }}</div>
              </div>
              <div class="row">
                <div>{{ $t("message.gameFi.text93") }}</div>
              </div>
            </div>
            <div class="col1" v-for="(item, index) in rewardPoolData" :key="index">
              <div class="row">
                <div>
                  {{ item.title }} <br />
                  {{ item.totalR ? "" : $t("message.gameFi.text79") }}
                </div>
              </div>
              <div class="row">
                <div>{{ item.totalR | numToFixed }}</div>
              </div>
              <div class="row">
                <div>
                  {{ item.personalR | numToFixed }}
                  <div
                    class="claim_btn"
                    @click="extractableClick(item)"
                    v-if="item.pool == 1 || item.pool == 2"
                    :class="{ disable: !item.personalR }"
                  >
                    <span>{{ $t("message.gameFi.text98") }}</span>
                    <BtnLoading :isloading="item.loading"></BtnLoading>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Reward Vault -->
    <div class="ranking_box ranking_box_box1 mobile">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text90") }}</span>
        <span>{{ $t("message.gameFi.text105") }} </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="row select_list" @touchstart.stop="showPoolSelect = true">
              <div>
                <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                <template v-if="issueList.length > 1">
                  <img class="accrow" :class="{ active: showPoolSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                  <transition name="select-scaleY" appear>
                    <ul class="list" v-show="showPoolSelect">
                      <li v-for="(item, index) in issueList" :key="index" @click="poolSelectSeason(item)">
                        {{ $t("message.gameFi.text91") }} {{ item.issue }}
                      </li>
                    </ul>
                  </transition>
                </template>
              </div>
            </div>
            <div class="row">
              <ul class="rank_list">
                <li>
                  <div></div>
                  <div>{{ $t("message.gameFi.text92") }}</div>
                  <div>{{ $t("message.gameFi.text93") }}</div>
                </li>
                <li v-for="(item, index) in rewardPoolData" :key="index">
                  <div>
                    {{ item.title }} <br />
                    {{ item.totalR ? "" : $t("message.gameFi.text79") }}
                  </div>
                  <div>{{ item.totalR | numToFixed }}</div>
                  <div>
                    {{ item.personalR | numToFixed }}
                    <div
                      class="claim_btn"
                      @click="extractableClick(item)"
                      v-if="item.pool == 1 || item.pool == 2"
                      :class="{ disable: !item.personalR }"
                    >
                      <span>{{ $t("message.gameFi.text98") }}</span>
                      <BtnLoading :isloading="item.loading"></BtnLoading>
                    </div>
                  </div>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="update_title">
      <span>{{ $t("message.gameFi.text106") }} {{ updateTime }} &nbsp;&nbsp; {{ $t("message.gameFi.text107") }}</span>
    </div>
    <!-- PVE -->
    <div class="ranking_box ranking_box_box2 pc">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text33") }} </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="col1">
              <div class="select_list" @mouseover="showPveSelect = true" @mouseleave="showPveSelect = false">
                <div>
                  <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                  <template v-if="issueList.length > 1">
                    <img class="accrow" :class="{ active: showPveSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                    <transition name="select-scaleY" appear>
                      <ul class="list" v-show="showPveSelect">
                        <li v-for="(item, index) in issueList" :key="index" @click="pveSelectSeason(item)">
                          {{ $t("message.gameFi.text91") }} {{ item.issue }}
                        </li>
                      </ul>
                    </transition>
                  </template>
                </div>
              </div>
              <div>
                <div>
                  <span>{{ $t("message.gameFi.text99") }}</span>
                </div>
              </div>
              <div>
                <div>
                  <span>{{ $t("message.gameFi.text100") }}</span>
                </div>
              </div>
              <div>
                <div>
                  <span>{{ $t("message.gameFi.text94") }}</span>
                </div>
              </div>
              <div>
                <div>
                  <span>{{ $t("message.gameFi.text95") }}</span>
                </div>
              </div>
            </div>
            <div class="col2">
              <div class="row" v-for="(item, index) in PVEData" :key="index">
                <div>
                  <div>
                    <span>{{ $t("message.gameFi.text101") }} {{ item.charpterId }}</span>
                  </div>
                </div>
                <div>
                  <div>
                    <span>{{ item.passCount }}</span>
                  </div>
                </div>
                <div>
                  <div>
                    <span>{{ item.isPass ? "✓" : "x" }}</span>
                  </div>
                </div>
                <div>
                  <div>
                    <span>{{ item.chapterRewards | numToFixed }}</span>
                  </div>
                </div>
                <div>
                  <div>
                    <span>{{ item.totalHc | numToFixed }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- PVE -->
    <div class="ranking_box ranking_box_box2 mobile">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text33") }} </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="row select_list" @touchstart.stop="showPveSelect = true">
              <div>
                <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                <template v-if="issueList.length > 1">
                  <img class="accrow" :class="{ active: showPveSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                  <transition name="select-scaleY" appear>
                    <ul class="list" v-show="showPveSelect">
                      <li v-for="(item, index) in issueList" :key="index" @click="pveSelectSeason(item)">
                        {{ $t("message.gameFi.text91") }} {{ item.issue }}
                      </li>
                    </ul>
                  </transition>
                </template>
              </div>
            </div>
            <div class="row">
              <ul class="rank_list">
                <li>
                  <div></div>
                  <div>{{ $t("message.gameFi.text99") }}</div>
                  <div>{{ $t("message.gameFi.text100") }}</div>
                  <div>{{ $t("message.gameFi.text94") }}</div>
                  <div>{{ $t("message.gameFi.text95") }}</div>
                </li>
                <li v-for="(item, index) in PVEData" :key="index">
                  <div>{{ $t("message.gameFi.text101") }} {{ item.charpterId }}</div>
                  <div>{{ item.passCount }}</div>
                  <div>{{ item.isPass ? "✓" : "x" }}</div>
                  <div>{{ item.chapterRewards | numToFixed }}</div>
                  <div>{{ item.totalHc | numToFixed }}</div>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- PVP -->
    <div class="ranking_box ranking_box_box3 pc">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text35") }}</span>
        <span>
          {{ $t("message.gameFi.text96") }}: {{ PVPPersonalData.totalHc | numToFixed }} &nbsp;&nbsp;&nbsp;&nbsp; {{ $t("message.gameFi.text97") }}:
          {{ PVPPersonalData.rank }}
        </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="col1">
              <div class="select_list" @mouseover="showPvpSelect = true" @mouseleave="showPvpSelect = false">
                <div>
                  <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                  <template v-if="issueList.length > 1">
                    <img class="accrow" :class="{ active: showPvpSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                    <transition name="select-scaleY" appear>
                      <ul class="list" v-show="showPvpSelect">
                        <li v-for="(item, index) in issueList" :key="index" @click="pvpSelectSeason(item)">
                          {{ $t("message.gameFi.text91") }} {{ item.issue }}
                        </li>
                      </ul>
                    </transition>
                  </template>
                </div>
              </div>
              <div>
                <div>
                  <span>{{ $t("message.gameFi.text35") }}</span>
                </div>
              </div>
            </div>
            <div class="col2">
              <div class="row row_col">
                <div>
                  <span>{{ $t("message.gameFi.text102") }}</span>
                </div>
                <div>
                  <span>{{ $t("message.gameFi.text103") }}</span>
                </div>
                <div>
                  <span>{{ $t("message.gameFi.text104") }}</span>
                </div>
              </div>
              <div class="row">
                <ul class="rank_list">
                  <li class="row_col" v-for="(item, index) in PVPData" :key="index">
                    <div>
                      <span>{{ item.rank }}</span>
                    </div>
                    <div>
                      <span>{{ item.walletAddress | ellipsis }}</span>
                    </div>
                    <div>
                      <span>{{ item.totalHc | numToFixed }}</span>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- PVP -->
    <div class="ranking_box ranking_box_box3 mobile">
      <div class="ranking_title">
        <span>{{ $t("message.gameFi.text35") }} </span>
        <span>
          {{ $t("message.gameFi.text96") }}: {{ PVPPersonalData.totalHc | numToFixed }} &nbsp;&nbsp; {{ $t("message.gameFi.text97") }}:
          {{ PVPPersonalData.rank }}
        </span>
      </div>
      <div class="outside_box">
        <div class="in_box">
          <div class="ranking_content">
            <div class="row select_list" @touchstart.stop="showPvpSelect = true">
              <div>
                <span>{{ $t("message.gameFi.text91") }} {{ currentIssue }}</span>
                <template v-if="issueList.length > 1">
                  <img class="accrow" :class="{ active: showPvpSelect }" :src="`${$store.state.imgUrl}accrow.png`" />
                  <transition name="select-scaleY" appear>
                    <ul class="list" v-show="showPvpSelect">
                      <li v-for="item in issueList" :key="item.issue" @click="pvpSelectSeason(item)">
                        {{ $t("message.gameFi.text91") }} {{ item.issue }}
                      </li>
                    </ul>
                  </transition>
                </template>
              </div>
            </div>
            <div class="row">
              <div>
                <span>{{ $t("message.gameFi.text35") }}</span>
              </div>
              <div class="row_col">
                <div>{{ $t("message.gameFi.text102") }}</div>
                <div>{{ $t("message.gameFi.text103") }}</div>
                <div>{{ $t("message.gameFi.text104") }}</div>
              </div>
              <ul class="rank_list">
                <li class="row_col" v-for="(item, index) in PVPData" :key="index">
                  <div>{{ item.rank }}</div>
                  <div>{{ item.walletAddress | ellipsis }}</div>
                  <div>{{ item.totalHc | numToFixed }}</div>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Proup :btntxt="btntxt" :word="word" :proupDis="proupDis" @besurefun="CloseFun" @closedis="CloseFun"></Proup>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import { hwPvPPool, hwPvEPool, hwWEPool, getSigner, hc, contract, util } from "hashland-sdk";
export default {
  data() {
    return {
      btntxt: "", // 弹窗页面的确认按钮
      word: "", //弹窗提示文字
      proupDis: false, // 弹窗展示消失变量
      issueList: [], // 赛季列表
      queryAccount: "",
      currentIssue: "", // 当前赛季
      showPoolSelect: false,
      showPveSelect: false,
      showPvpSelect: false,
      rewardPoolData: [
        { title: `${this.$t("message.gameFi.text33")}`, pool: 1, totalR: 0, personalR: 0, loading: false }, // PVE
        { title: `${this.$t("message.gameFi.text35")}`, pool: 2, totalR: 0, personalR: 0, loading: false }, // PVP
        { title: `${this.$t("message.gameFi.text37")}`, pool: 3, totalR: 0, personalR: 0, loading: false }, // GVE
        { title: `${this.$t("message.gameFi.text39")}`, pool: 4, totalR: 0, personalR: 0, loading: false }, // GVG
        { title: `${this.$t("message.gameFi.text88")}`, pool: 5, totalR: 0, personalR: 0, loading: false }, // World BOSS
      ],
      PVEData: [], // PVE
      PVPData: [], // PVP
      PVPPersonalData: { rank: 0, totalHc: 0 }, // PVP个人
      updateTime: "",
    };
  },
  computed: {
    ...mapGetters(["getIstrue", "getAccount", "getCoinPrice"]),
  },
  watch: {
    getIstrue: {
      handler: function (newValue) {
        if (newValue) {
          this.queryPersonalRewards();
        }
      },
      deep: true,
      immediate: true,
    },
  },
  created() {
    // 只显示UTC 0点12点整点时间
    this.updateTime = new Date(Math.floor(new Date().getTime() / 43200000) * 43200000).toLocaleString("en");
    this.queryAccount = this.queryAccount
      ? this.queryAccount
      : localStorage.getItem("hashlandGameFiInfo")
      ? JSON.parse(localStorage.getItem("hashlandGameFiInfo")).mailAccount
      : "";
    this.queryHWWEPoolTotal();
    this.queryRewardData();
  },
  methods: {
    /**获取赛季列表 */
    queryRewardData() {
      this.$api
        .gameIssueInfo(`queryType=issue_info&queryAccount=${this.queryAccount}&issue=${this.currentIssue}`)
        .then((res) => {
          if (res.data.result == "SUCCESS") {
            this.issueList = res.data.data.issueList;
            this.currentIssue = this.currentIssue ? this.currentIssue : res.data.data.maxIssue;

            this.rewardPoolData.forEach((item) => {
              if (item.pool == 1) {
                item.totalR = res.data.data.totalRewardPveHc; // 奖池
              } else if (item.pool == 2) {
                item.totalR = res.data.data.totalRewardPvpHc;
              }
            });
            this.PVEData = res.data.data.pve; // PVE
            this.PVPData = res.data.data.pvp; // PVP
            this.PVPPersonalData.rank = res.data.data.pvpIndividualRank; // PVP个人
            this.PVPPersonalData.totalHc = res.data.data.pvpIndividualRewardHc; // PVP个人
          }
        })
        .catch((err) => {
          console.warn("rewardRanking", err);
        });
    },
    /**奖池切换赛季 */
    poolSelectSeason(ite) {
      if (this.currentIssue == ite.issue) return;
      this.currentIssue = ite.issue;
      this.showPoolSelect = false;
      this.queryRewardData();
    },
    /**PVE切换赛季 */
    pveSelectSeason(ite) {
      if (this.currentIssue == ite.issue) return;
      this.currentIssue = ite.issue;
      this.showPveSelect = false;
      this.queryRewardData();
    },
    /**PVP切换赛季 */
    pvpSelectSeason(ite) {
      if (this.currentIssue == ite.issue) return;
      this.currentIssue = ite.issue;
      this.showPvpSelect = false;
      this.queryRewardData();
    },
    /**查询世界池余额 */
    queryHWWEPoolTotal() {
      hc()
        .balanceOf(contract().HWWEPool)
        .then((res) => {
          let obj = this.rewardPoolData.find((item) => item.pool == 5);
          if (obj) obj.totalR = Number(util.formatEther(res));
        })
        .catch((err) => {
          console.warn("HWWEPool", err);
        });
    },
    /**奖励池  赛季个人奖励  PVP  PVE  BOSS */
    queryPersonalRewards() {
      if (!this.getAccount || this.getAccount == "no") return this.$common.selectLang("请连接钱包！", "Please connect the wallet!", this);
      this.queryhwPvEPool();
      this.queryhwPvPPool();
      this.queryhwWEPool();
    },
    /**查询PVE奖励池 */
    queryhwPvEPool() {
      hwPvEPool()
        .userStoredToken(this.getAccount)
        .then((res) => {
          let obj = this.rewardPoolData.find((item) => item.pool == 1);
          if (obj) obj.personalR = Number(this.$common.convertBigNumberToNormal(res.toString(), 5));
        })
        .catch((err) => {
          console.warn("PVE Pool", err);
        });
    },
    /**查询PVP奖励池 */
    queryhwPvPPool() {
      hwPvPPool()
        .userStoredToken(this.getAccount)
        .then((res) => {
          let obj = this.rewardPoolData.find((item) => item.pool == 2);
          if (obj) obj.personalR = Number(this.$common.convertBigNumberToNormal(res.toString(), 5));
        })
        .catch((err) => {
          console.warn("PVP Pool", err);
        });
    },

    /**查询BOSS奖励池 */
    queryhwWEPool() {
      hwWEPool()
        .userStoredToken(this.getAccount)
        .then((res) => {
          let obj = this.rewardPoolData.find((item) => item.pool == 5);
          if (obj) obj.personalR = Number(this.$common.convertBigNumberToNormal(res.toString(), 5));
        })
        .catch((err) => {
          console.warn("BOSS Pool", err);
        });
    },
    /**提取个人HC奖励 */
    extractableClick(item) {
      if (!item.personalR) return;
      // this.$common.selectLang("没有可提取余额", "No Remaining Balance to Claim", this);
      if (item.loading) return;
      item.loading = true;
      switch (item.pool) {
        case 1:
          hwPvEPool()
            .connect(getSigner())
            .harvestToken()
            .then(async (res) => {
              const etReceipt = await res.wait();
              if (etReceipt.status == 1) {
                this.$common.selectLang("提取成功", "Claim Successful", this);
                this.queryhwPvEPool();
              }
              item.loading = false;
            })
            .catch((err) => {
              item.loading = false;
              console.warn("PVE提取失败", err);
            });
          break;
        case 2:
          hwPvPPool()
            .connect(getSigner())
            .harvestToken()
            .then(async (res) => {
              const etReceipt = await res.wait();
              if (etReceipt.status == 1) {
                this.$common.selectLang("提取成功", "Claim Successful", this);
                this.queryhwPvPPool();
              }
              item.loading = false;
            })
            .catch((err) => {
              item.loading = false;
              console.warn("PVP提取失败", err);
            });
          break;
        case 3:
          break;
        case 4:
          break;
        case 5:
          hwWEPool()
            .connect(getSigner())
            .harvestToken()
            .then(async (res) => {
              const etReceipt = await res.wait();
              if (etReceipt.status == 1) {
                this.$common.selectLang("提取成功", "Claim Successful", this);
                this.queryhwWEPool();
              }
              item.loading = false;
            })
            .catch((err) => {
              item.loading = false;
              console.warn("BOSS提取失败", err);
            });
          break;
        default:
          break;
      }
    },
    // 取消按钮(关闭弹窗)
    CloseFun() {
      this.proupDis = false;
    },
  },
  filters: {
    ellipsis(value) {
      if (!value) return "";
      const index = value.length;
      return value.slice(0, 6) + "..." + value.slice(index - 4, index);
    },
    mailEllipsis(value) {
      if (!value) return "";
      const index = value.length;
      const index2 = value.indexOf("@");
      return value.slice(0, 2) + "***" + value.slice(index2, index);
    },
    numToFixed(value) {
      if (!value) return "0";
      let val = value.toFixed(4).toString();
      const valEnd = val.substring(val.lastIndexOf("."), val.length);
      valEnd.split("").forEach((element) => {
        if (val.charAt(val.length - 1) == "0" || val.charAt(val.length - 1) == ".") {
          val = val.slice(0, val.length - 1);
        }
      });
      return val;
    },
  },
};
</script>

<style lang="scss" scoped>
// 重置滚动条样式
::-webkit-scrollbar {
  width: 5px;
  height: 5px;
}
::-webkit-scrollbar-thumb {
  background: #68b0c8;
  border-radius: 5px;
}
::-webkit-scrollbar-track {
  background: transparent;
}

.pc {
  display: block;
}
.mobile {
  display: none;
}
.outside_box {
  margin-top: 20px;
  width: 100%;
  background: #68b0c8;
  border-radius: 5px;
  padding: 1px;
  .in_box {
    width: 100%;
    height: 100%;
    border-radius: 5px;
    background: #01162d;
    padding: 10px;
    font-size: 16px;
  }
}
.ranking_box {
  width: 100%;
  background: #021c3b;
  box-shadow: -13px 10px 11px -2px rgba(2, 12, 23, 0.4), -2px -33px 101px 0px rgba(25, 47, 74, 0.5);
  border-radius: 6px;
  padding: 20px;
  margin-top: 30px;
  font-size: 0;
  .ranking_title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    > span {
      &:nth-child(1) {
        font-size: 26px;
      }
      &:nth-child(2) {
        font-size: 18px;
      }
    }
  }
  .ranking_content {
    width: 100%;
    display: flex;
    text-align: center;
  }
}
.claim_btn {
  cursor: pointer;
  background: #29cdda;
  border-radius: 7px;
  padding: 5px;
  font-size: 12px;
  margin-left: 2em;
  display: flex;
  align-items: center;
  &.disable {
    background: #ccc;
    &:hover {
      cursor: no-drop;
    }
  }
  .donut {
    width: 15px;
    height: 15px;
  }
}
.update_title {
  font-size: 20px;
  margin-top: 50px;
}
.ranking_box_box1.pc {
  .ranking_content {
    display: flex;
    text-align: center;
    font-size: 12px;
    .col1 {
      width: calc(100% / 6);
      background: #082545;
      border-radius: 6px;
      padding: 5px;
      margin-right: 5px;
      .row {
        width: 100%;
        height: 50px;
        padding: 5px;
        display: flex;
        align-items: center;
        > div {
          width: 100%;
          height: 100%;
          background: #103763;
          border-radius: 4px;
          padding: 5px 0;
          display: flex;
          align-items: center;
          justify-content: center;
        }
      }
      &:nth-child(1) .row > div {
        background: #3d4f64;
        border-radius: 6px;
        font-size: 16px;
        font-weight: bold;
      }
    }
  }
}
.ranking_box_box2.pc {
  .ranking_content {
    display: flex;
    text-align: center;
    font-size: 12px;
    .col1 {
      width: calc(100% / 6);
      background: #082545;
      border-radius: 6px;
      padding: 5px;
      margin-right: 5px;
      font-size: 16px;
      font-weight: bold;
      > div {
        width: 100%;
        height: 50px;
        padding: 5px;
        > div {
          width: 100%;
          height: 100%;
          background: #3d4f64;
          border-radius: 6px;
          display: flex;
          align-items: center;
          justify-content: center;
        }
      }
    }
    .col2 {
      width: calc(100% / 6 * 5);
      background: #082545;
      border-radius: 6px;
      padding: 5px;
      display: flex;
      overflow-x: scroll;
      .row {
        min-width: 90px;
        width: calc(100% / 9);
        > div {
          height: 50px;
          padding: 5px;
          > div {
            width: 100%;
            height: 100%;
            background: #103763;
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
          }
        }
      }
    }
  }
}
.ranking_box_box3.pc {
  .ranking_content {
    font-size: 12px;
    .col1 {
      width: calc(100% / 6);
      margin-right: 5px;
      font-size: 16px;
      font-weight: bold;
      > div {
        background: #082545;
        border-radius: 6px;
        padding: 5px;
        display: flex;
        align-items: center;
        justify-content: center;
        &:nth-child(1) {
          height: 50px;
          margin-bottom: 5px;
        }
      }
    }
    .col1 div:nth-child(2),
    .col2 .row:nth-child(2) .rank_list {
      height: 350px;
    }
    .col2 {
      width: calc(100% / 6 * 5);
      .row {
        width: 100%;
        display: flex;
        align-items: center;
        border-radius: 6px;
        background: #082545;
        &:nth-child(1) {
          width: 100%;
          height: 50px;
          margin-bottom: 5px;
          display: flex;
          align-items: center;
          > div {
            height: 100%;
            padding: 5px;
          }
        }
        .rank_list {
          width: 100%;
          overflow-y: auto;
          font-size: 15px;
          font-weight: 400;
          li {
            width: 100%;
            display: flex;
            align-items: center;
            &:hover {
              background: #021c3b;
            }
            > div {
              height: 100%;
              padding: 5px 0;
            }
          }
        }
      }
      .row_col {
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        > div {
          display: flex;
          align-items: center;
          justify-content: center;
          &:nth-child(1) {
            width: 30%;
          }
          &:nth-child(2) {
            width: 40%;
          }
          &:nth-child(3) {
            width: 30%;
          }
        }
      }
    }
  }
}
.select_list {
  cursor: pointer;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  > div {
    .accrow {
      margin-left: 10px;
      width: 15px;
      height: auto;
      transform: rotate(-90deg);
      transition: all 0.3s;
      &.active {
        transform: rotate(0);
      }
    }
    .list {
      width: 100%;
      height: auto;
      background: #082545;
      box-shadow: -1px 14px 9px -9px rgba(24, 24, 24, 0.56);
      border-radius: 0 0 6px 6px;
      overflow: hidden;
      position: absolute;
      top: 100%;
      left: 0;
      // transform: scaleY(0);
      transition: transform 0.2s;
      transform-origin: top center;
      li {
        font-size: 16px;
        padding: 10px;
        &:hover {
          color: #00e7f0;
        }
      }
    }
  }
  // &:hover {
  //   .accrow {
  //     transform: rotate(0);
  //   }
  //   .list {
  //     transform: scaleY(1);
  //   }
  // }
}
.select-scaleY-enter,
.select-scaleY-leave-to {
  transform: scaleY(0);
}
.select-scaleY-enter-to,
.select-scaleY-leave {
  transform: scaleY(1);
}
@media screen and (max-width: 980px) {
  // 重置滚动条样式
  ::-webkit-scrollbar {
    width: 0.05rem;
    height: 0.05rem;
  }
  ::-webkit-scrollbar-thumb {
    background: #68b0c8;
    border-radius: 0.05rem;
  }
  ::-webkit-scrollbar-track {
    background: transparent;
  }
  .pc {
    display: none;
  }
  .mobile {
    display: block;
  }
  .ranking_box {
    padding: 0.1rem;
    margin-top: 0.3rem;
    .ranking_title {
      flex-wrap: wrap;
      > span {
        &:nth-child(1) {
          width: 100%;
          font-size: 0.2rem;
        }
        &:nth-child(2) {
          font-size: 0.12rem;
          font-weight: 100;
        }
      }
    }
    .ranking_content {
      display: block;
    }
  }
  .update_title {
    font-size: 0.15rem;
    margin-top: 0.5rem;
  }
  .claim_btn {
    cursor: pointer;
    background: #29cdda;
    border-radius: 0.07rem;
    padding: 0.02rem 0.05rem;
    font-size: 0.12rem;
    margin-left: 1em;
    .donut {
      width: 0.1rem;
      height: 0.1rem;
    }
  }
  .ranking_box_box1.mobile {
    .ranking_content {
      font-size: 12px;
      .row {
        width: 100%;
        background: #082545;
        border-radius: 6px;
        display: flex;
        align-items: center;
        justify-content: center;
        &:nth-child(1) {
          height: 0.3rem;
          margin-bottom: 0.05rem;
        }
      }
      .rank_list {
        width: 100%;
        padding: 0.05rem;
        li {
          width: 100%;
          display: flex;
          align-items: center;
          justify-content: center;
          &:nth-child(1) div {
            height: 0.3rem;
            background: transparent;
            border-radius: 0;
          }
          > div {
            margin: 0 0.05rem 0.05rem 0;
            height: 0.6rem;
            background: #103763;
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
            &:nth-child(1) {
              width: 25%;
            }
            &:nth-child(2) {
              width: 30%;
            }
            &:last-child {
              width: 45%;
              margin-right: 0;
            }
          }
        }
      }
    }
  }
  .ranking_box_box2.mobile {
    .ranking_content {
      font-size: 12px;
      .row {
        width: 100%;
        background: #082545;
        border-radius: 6px;
        padding: 0.05rem;
        display: flex;
        align-items: center;
        justify-content: center;
        &:nth-child(1) {
          height: 0.3rem;
          margin-bottom: 0.05rem;
        }
      }
      .rank_list {
        overflow-x: auto;
        li {
          display: flex;
          align-items: center;
          &:nth-child(1) div {
            height: 0.6rem;
            background: transparent;
            border-radius: 0;
          }
          div {
            min-width: 1rem;
            width: 20%;
            height: 0.3rem;
            margin: 0 0.05rem 0.05rem 0;
            background: #103763;
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
          }
        }
      }
    }
  }

  .ranking_box_box3.mobile {
    font-size: 12px;
    .ranking_content {
      .row {
        width: 100%;
        margin-bottom: 0.05rem;
        background: #082545;
        border-radius: 6px;
        font-size: 12px;
        > div {
          width: 100%;
          height: 0.3rem;
          display: flex;
          align-items: center;
          justify-content: center;
        }
        &:nth-child(2) {
          > div {
            &:nth-child(1) {
              padding: 0 0.1rem;
              justify-content: start;
            }
          }
        }
      }
      .row_col {
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        > div {
          &:nth-child(1) {
            width: 30%;
          }
          &:nth-child(2) {
            width: 40%;
          }
          &:nth-child(3) {
            width: 30%;
          }
        }
      }
      .rank_list {
        width: 100%;
        min-height: 0.5rem;
        max-height: 3.5rem;
        overflow-y: auto;
        font-weight: 100;
        li {
          > div {
            height: 100%;
            padding: 5px 0;
            display: flex;
            align-items: center;
            justify-content: center;
          }
        }
      }
    }
  }
  .select_list {
    > div {
      .accrow {
        margin-left: 0.1rem;
        width: 0.12rem;
      }
      .list {
        li {
          font-size: 12px;
          padding: 0.1rem;
        }
      }
    }
  }
}
</style>
