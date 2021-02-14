<template>
  <div class="w100 h100 workhours">
    <div class="w100 workhours-container">
      <div class="title">
        <span
          ><i class="left" @click="PreMonth"></i
          ><i class="right" @click="NextMonth"></i
          >{{ calendarDateTitle || "-" }}</span
        >
      </div>
      <div class="w100 calendar">
        <div class="calendar-titie">
          <div v-for="(item, index) in calendarTitle" :key="index + 'week'">
            星期{{ item }}
          </div>
        </div>
        <div class="calendar-content">
          <div
            class="calendar-content-item"
            :style="{ height: `calc(100% / ${calendarList.length / 7})` }"
            v-for="(item, index) in calendarList"
            :key="index + 'day'"
          >
            <!-- 今日之前有详情 -->
            <div
              :class="[
                'w100',
                'h100',
                'calendar-container',
                item.isToday ? 'today' : item.isLastWeek ? 'lastWeek' : ''
              ]"
            >
              <span
                :class="[
                  'solarday',
                  item.otherMonth == 'nowMonth' ? 'nowMonth' : ''
                ]"
              >
                {{ item.dayStr }}
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import timeUtil from "@/utils/calendar.js";

export default {
  data() {
    return {
      loading: false,
      //提交loading
      submitLoading: false,
      //日期标题
      calendarDateTitle: "",
      calendarTitle: ["一", "二", "三", "四", "五", "六", "日"],
      //当前日期
      currentDate: [],
      calendarList: []
    };
  },

  methods: {
    // 上个月
    PreMonth: function() {
      this.currentDate = timeUtil.getOtherMonth(this.currentDate, "preMonth");
      this.getList(this.currentDate);
    },
    // 下个月
    NextMonth: function() {
      this.currentDate = timeUtil.getOtherMonth(this.currentDate, "nextMonth");
      this.getList(this.currentDate);
    },
    // 获取日历
    getList: async function(date) {
      let currYear = date.getFullYear(),
        currMonth = date.getMonth() + 1,
        currDay = date.getDate();
      this.calendarDateTitle = `${currYear}年${currMonth}月`;
      // 获取当前要显示的所有日期(上个月末尾+本月+下个月开头)
      let arr = timeUtil.getMonthList(this.currentDate);
      let lastWeek = timeUtil.getLastWeek(this.currentDate);
      arr.map(item => {
        let targetDay = item.id;
        item.currentDay = currDay;
        let targetMonth = item.date.split("-")[1];
        if (targetDay == 1) {
          item.dayStr = `${parseInt(targetMonth)}月${item.id}日`;
        } else {
          item.dayStr = `${item.id}日`;
        }
        //是否是上周
        item.isLastWeek = lastWeek.includes(item.date);
      });
      this.calendarList = arr;
    }
  },
  created() {
    console.log(12);
    this.currentDate = new Date();
  },
  mounted() {
    this.getList(this.currentDate);
  }
};
</script>

<style lang="scss" scoped>
.workhours {
  position: relative;
  .workhours-container {
    padding: 24px;
    height: calc(100% - 60px);
    .title {
      height: 36px;
      text-align: center;
      font-size: 24px;
      color: #999;
      font-weight: bold;
      position: relative;
      margin-bottom: 20px;
      & > span {
        position: relative;
        i {
          cursor: pointer;
          width: 18px;
          height: 33px;
          display: inline-block;
          background: url("../assets/imgs/cost/arrow-left.png") 0 0 no-repeat;
          background-size: 100%;
          position: absolute;
          top: 0;
          &.left {
            left: -50px;
          }
          &.right {
            right: -50px;
            transform: rotate(180deg);
          }
        }
      }

      .tooltip {
        position: absolute;
        right: 0;
        bottom: -20px;
        cursor: pointer;
        @include flex;
        div {
          margin-left: 10px;
          i {
            display: inline-block;
            width: 20px;
            height: 7px;
            margin-right: 4px;
            border-radius: 6px;
            &.submit {
              background: #ffb300;
            }
            &.check {
              background: #ff8761;
            }
            &.success {
              background: #4cc2a5;
            }
            &.error {
              background: #b198dc;
            }
          }
          span {
            font-size: 12px;
            color: #999;
          }
        }
      }
    }
    .calendar {
      height: calc(100% - 60px);
      border: 1px solid #e8e8e8;
      .calendar-titie {
        height: 40px;
        @include flex;
        text-align: center;
        line-height: 40px;
        background: #eceef2;
        div {
          flex: 1;
          color: #103144;
        }
      }
      .calendar-content {
        @include flex;
        flex-wrap: wrap;
        height: calc(100% - 40px);
        & > .calendar-content-item {
          transition: width 0.3s, height 0.3s, border 0.2s,
            background 0.2s ease-out;
          width: calc(100% / 7);
          height: calc(100% / 6);

          &:not(:nth-last-child(-n + 7)) {
            border-bottom: 1px solid #e8e8e8;
          }
          &:not(:nth-child(7n + 0)) {
            border-right: 1px solid #e8e8e8;
          }
          background: #f7f7f7;
          &:hover {
            position: relative;

            z-index: 10;
            border-color: transparent;
            background: rgba(93, 207, 232, 0.4);

            .calendar-container {
              &.lastWeek {
                background: rgba(93, 207, 232, 0.1);
              }
            }
          }
          .calendar-container {
            overflow: hidden;
            position: relative;

            // &.lastWeek {
            //   background: white;
            // }
            // &.today {
            //   background: #5dcfe8;
            //   .solarday {
            //     color: white;
            //   }
            //   .icon.add {
            //     background: url("../../assets/imgs/cost/add-white.png") 0 0
            //       no-repeat;
            //     background-size: 100%;
            //   }
            // }
          }

          .solarday {
            position: absolute;
            top: 15px;
            right: 15px;
            font-size: 16px;
            color: #999;
            &.nowMonth {
              color: #333333;
            }
          }
          .timeState {
            position: absolute;
            top: 15px;
            left: 10px;
            @include flex;
            @include flex-column;
            i {
              &:not(:last-child) {
                margin-bottom: 4px;
              }
              display: inline-block;
              width: 20px;
              height: 2px;
              padding-bottom: 5px;
              border-radius: 6px;
              &.submit {
                background: #ffb300;
              }
              &.check {
                background: #ff8761;
              }
              &.success {
                background: #4cc2a5;
              }
              &.error {
                background: #b198dc;
              }
            }
          }
          // .icon {
          //   cursor: pointer;
          //   width: 24px;
          //   height: 24px;
          //   display: inline-block;
          //   position: absolute;
          //   top: 55%;
          //   left: 50%;
          //   transform: translate(-50%, -50%);
          //   &.success {
          //     background: url("../../assets/imgs/cost/success.png") 0 0
          //       no-repeat;
          //     background-size: 100%;
          //   }
          //   &.add {
          //     background: url("../../assets/imgs/cost/add.png") 0 0 no-repeat;
          //     background-size: 100%;
          //   }
          // }
          // .subscript {
          //   position: absolute;
          //   cursor: pointer;
          //   font-size: 20px;
          //   bottom: 10px;
          //   right: 15px;
          //   color: #333c;
          // }
        }
      }
    }
  }
}
</style>
