<template>
  <div>
    <div class="box">
      <input
        type="text"
        class="input"
        placeholder="请输入众筹名"
        v-model="searchMeg"
      />
      <input type="button" value="搜索" class="button" @click="init" />
    </div>
    <input
      v-show="!this.schoolOnlyMode"
      type="button"
      value="只看我的学校众筹"
      class="changeButton"
      @click="switchMode"
    />
    <input
      v-show="this.schoolOnlyMode"
      type="button"
      value="所有学校众筹"
      class="changeButton"
      @click="switchMode"
    />
    <div v-for="el in displayList" class="contentView" :key="el.fundid">
      <div class="viewBlock" @click="checkData(el)">
        <img :src="el.ps" /><br />
        <span id="main">{{ el.goal }}</span
        ><br />
        <span>{{ el.userSchool }} </span
        ><span class="red"> 已筹：{{ el.moneystate }}￥</span>
      </div>
    </div>
    <div
      class="returnBack"
      v-if="this.currentPage != 1 && pages != 0"
      @click="back"
    >
      上一页
    </div>
    <div
      class="next"
      v-if="this.currentPage != this.pages && pages != 0"
      @click="next"
    >
      下一页
    </div>
    <div class="pageSwitch">
      <input
        type="button"
        :value="currentPage"
        id="page1"
        v-if="currentPage <= pages"
        :class="{ information: informationActive == currentPage }"
        @click="
          switchPage(currentPage);
          handleChangeInformation(currentPage);
        "
      />
      <input
        type="button"
        :value="currentPage + 1"
        id="page2"
        v-if="currentPage + 1 <= pages"
        :class="{ information: informationActive == currentPage + 1 }"
        @click="
          switchPage(currentPage + 1);
          handleChangeInformation(currentPage);
        "
      />
      <input
        type="button"
        :value="currentPage + 2"
        id="page3"
        v-if="currentPage + 2 <= pages"
        :class="{ information: informationActive == currentPage + 2 }"
        @click="
          switchPage(currentPage + 2);
          handleChangeInformation(currentPage);
        "
      />
      <input
        type="button"
        id="page4"
        :value="currentPage + 3"
        v-if="currentPage + 3 <= pages"
        :class="{ information: informationActive == currentPage + 3 }"
        @click="
          switchPage(currentPage + 3);
          handleChangeInformation(currentPage);
        "
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      school: "",
      schoolOnlyMode: false,
      maxAmount: 8,
      pages: 0,
      informationActive: 1,
      currentPage: 0,
      displayList: [],
      totalFundList: [],
      searchedList: [],
      searchMeg: "",
    };
  },
  mounted() {
    this.getDataList();
    this.currentPage = 1;
    this.$axios({
      url: "/student/Test",
      params: { useCode: this.$route.params.id },
    }).then(
      (res) => {
        this.school = JSON.parse(JSON.stringify(res.data)).userSchool;
        return res;
      },
      (error) => {
        alert("未知错误");
        return error;
      }
    );
  },
  methods: {
    switchPage(value) {
      this.currentPage = value;
      this.init();
    },
    handleChangeInformation(item) {
      this.informationActive = item;
    },
    getDataList() {
      this.$axios({
        url: "/student/isstate",
      }).then(
        (res) => {
          let objData = JSON.parse(JSON.stringify(res.data));
          this.totalFundList = objData;
          this.init();
          return res;
        },
        (error) => {
          alert("无法获取众筹列表！");
          return error;
        }
      );
    },
    checkData(el) {
      this.$router.push({
        name: "fundData",
        query: {
          id: el.fundid,
          user: this.$route.params.id,
        },
      });
    },
    init() {
      this.displayList = [];
      this.searchedList = [];
      this.filterList();
      let that = this.searchedList;
      if (that.length > 0) {
        this.pages = parseInt(that.length / this.maxAmount + 1);
      }
      for (let i = 0; i < that.length; i++) {
        if (parseInt(i / this.maxAmount) + 1 == this.currentPage) {
          let j = i % this.maxAmount;
          this.displayList[j] = that[i];
        }
      }

      this.$forceUpdate();
    },
    switchMode() {
      this.schoolOnlyMode = !this.schoolOnlyMode;
      this.init();
    },
    back() {
      this.currentPage--;
      this.init();
      this.handleChangeInformation(this.currentPage);
    },
    next() {
      this.currentPage++;
      this.init();
      this.handleChangeInformation(this.currentPage);
    },
    filterList() {
      let origin = this.totalFundList;
      let that = [];
      if (this.schoolOnlyMode) {
        for (var key in origin) {
          if (origin[key].userSchool == this.school) {
            that.push(origin[key]);
          }
        }
      } else {
        that = origin;
      }
      for (let i = 0; i < that.length; i++) {
        if (
          that[i].goal.includes(this.searchMeg) ||
          that[i].userSchool.includes(this.searchMeg)
        ) {
          this.searchedList.push(that[i]);
        }
      }
    },
  },
  // watch: {
  //   pages: {
  //     deep: true,
  //     handler() {
  //       this.$forceUpdate();
  //     },
  //   },
  // },
};
</script>

<style scoped>
.box {
  position: absolute;
  right: 6%;
  width: 300px;
  top: 72%;
  height: 40px;
  border: 2px solid cadetblue;
  margin: auto;
}
.input {
  float: left;
  width: 200px; /*宽400*/
  height: 38px; /*高38（因为文本框内外边框要占用1像素所以总体高度减2，其他盒子同理）*/
  outline: none;
  border: none;
  font-size: 20px; /*取消文本框内外边框*/
}
.button {
  float: left;
  width: 96px;
  height: 40px;
  color: white;
  background-color: cadetblue; /*将按钮背景设置为红色，字体设置为白*/
  border: none;
  outline: none; /*取消边框和外边框将按钮边框去掉*/
  cursor: pointer;
}

.viewBlock {
  color: black;
  width: 20%;
  height: 210px;
  border: 3px solid rgb(18, 128, 138);
  float: left;
  margin: 1.5% 2%;
  border-radius: 10px;
  box-sizing: border-box;
  text-align: center;
}
.viewBlock:hover {
  border: 3px solid yellow;
  cursor: pointer;
  background-color: rgb(255, 255, 255);
  filter: opacity(60%);
}
#main {
  font-size: 28px;
  font-weight: bold;
}
img {
  width: 80%;
  height: 70%;
}
.pageSwitch {
  float: left;
  position: absolute;
  top: 1140px;
  left: 40%;
}
input:hover {
  filter: opacity(60%);
}

.returnBack {
  float: left;
  position: absolute;
  top: 1138px;
  left: 32%;
  text-align: center;
  font-size: 20px;
  font-weight: bold;
  color: rgb(223, 233, 32);
  margin: 40px 20px;
  width: 100px;
  height: 30px;
  cursor: pointer;
  border: 2px solid rgb(0, 6, 6);
}
.returnBack:hover {
  background: pink;
}
.next {
  float: left;
  position: absolute;
  top: 1138px;
  left: 58%;
  text-align: center;
  font-size: 20px;
  font-weight: bold;
  color: rgb(223, 233, 32);
  margin: 40px 20px;
  width: 100px;
  height: 30px;
  cursor: pointer;
  border: 2px solid rgb(0, 6, 6);
}
.next:hover {
  background: pink;
}
.information {
  background: red !important;
}
.red {
  background: rgb(255, 213, 213);
  color: red;
}
.pageSwitch input {
  font-size: 20px;
  font-weight: bold;
  color: azure;
  background: cadetblue;
  margin: 40px 20px;
  width: 30px;
  height: 30px;
  cursor: pointer;
  border: 2px solid rgb(0, 6, 6);
}
.changeButton {
  width: 120px;
  height: 40px;
  color: white;
  background-color: cadetblue; /*将按钮背景设置为红色，字体设置为白*/
  border: none; /*取消边框和外边框将按钮边框去掉*/
  cursor: pointer;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}
</style>