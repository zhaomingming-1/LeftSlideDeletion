<template>
  <div>
    <div class="cc-calendar">
      <calendarHeader
        :headOptions="headOptions"
        @handlePrevMonth="handlePrevMonth"
        @handleNextMonth="handleNextMonth"
        @handleToday="handleToday"
      />
      <ul class="calendar-week clear">
        <li v-for="(item, index) in calendarTitleArr" :key="index" class="week-item">{{item}}</li>
      </ul>
      <ul class="calendar-view clear">
        <li
          v-for="(item, index) in visibleCalendar"
          :key="index"
          class="date-view"
          :class="[
          {'month-class': !isCurrentMonth(item.date)},
          {todayBg: isCurrentDay(item.date)},
          {handleDay: item.clickDay}
        ]"
          @click="handleClickDay(item)"
        >
          <span
            class="date-day"
            :style="dayStyle"
            :class="[{'opacity-class': !isCurrentMonth(item.date)}]"
          >{{item.day}}</span>
          <span class="calendar-num">{{item.calendarNum}}</span>
        </li>
      </ul>
    </div>
    <div v-if="rlData.length!=0">
      <div class="mn-cont" v-for="(i,index) in rlData" :key="index" v-if="i.rqMonth==xzMonth">
        <div v-if="i.rqDay==xzDay">
          <div class="mn-left">
            <img :src="i.img" alt>
          </div>
          <div class="mn-center">
            <p>{{i.sjd}}</p>
            <p>
              <span class="nam">{{i.name}}</span>
              <span class="bor">{{i.fangshi}}</span>
            </p>
          </div>
          <div class="mn-right">
            <p>调/退课</p>
          </div>
        </div>
      </div>
      <div v-else>
        <img
          src="https://dss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1312059974,1893880587&fm=111&gp=0.jpg"
          alt
        >
      </div>
    </div>
  </div>
</template>

<script>
import "../assets/css/reset.min.css";
import calendarHeader from "./canlendar-head.vue";
import * as utils from "../assets/js/utils.js";

export default {
  name: "cc-calendar",
  componentName: "cc-calendar",
  props: {
    options: Object
  },
  components: {
    calendarHeader
  },
  data() {
    let { year, month, day } = utils.getNewDate(new Date());
    return {
      headOptions: {
        type: this.options.type,
        style: this.options.headStyle,
        date: "",
        month: ""
      },
      calendarTitleArr: ["MON", "TUE", "WED", "THU", "FRI", "SAT", "SUN "],
      time: { year, month, day },
      calendarList: [],
      xzMonth: "",
      xzDay: "",
      
      rlData: [
        {
          rqmont: 4,
          rqDay: 5,
          img:
            "http://img4.imgtn.bdimg.com/it/u=1320076474,223369944&fm=11&gp=0.jpg",
          sjd: "10：00-11：00",
          name: "王",
          fangshi: "线上"
        },
        {
          rqmont: 4,
          rqDay: 5,
          img:
            "http://img4.imgtn.bdimg.com/it/u=1320076474,223369944&fm=11&gp=0.jpg",
          sjd: "10：00-11：00",
          name: "王",
          fangshi: "线上"
        },
        {
          rqmont: 4,
          rqDay: 5,
          img:
            "http://img4.imgtn.bdimg.com/it/u=1320076474,223369944&fm=11&gp=0.jpg",
          sjd: "10：00-11：00",
          name: "王",
          fangshi: "线上"
        },
        {
          rqmont: 4,
          rqDay: 4,
          img:
            "http://img4.imgtn.bdimg.com/it/u=1320076474,223369944&fm=11&gp=0.jpg",
          sjd: "10：00-11：00",
          name: "赵",
          fangshi: "线上"
        },
        {
          rqmont: 4,
          rqDay: 3,
          img:
            "http://img4.imgtn.bdimg.com/it/u=1320076474,223369944&fm=11&gp=0.jpg",
          sjd: "10：00-11：00",
          name: "李",
          fangshi: "线上"
        }
      ]
    };
  },
  computed: {
    dayStyle: function() {
      return {
        textAlign: this.options.viewStyle.day
      };
    },
    visibleCalendar() {
      let calendatArr = [];
      let { year, month, day } = utils.getNewDate(
        utils.getDate(this.time.year, this.time.month, 1)
      );

      let currentFirstDay = utils.getDate(year, month, 1);

      // 获取当前月第一天星期几
      let weekDay = currentFirstDay.getDay();
      let startTime = currentFirstDay - (weekDay - 1) * 24 * 60 * 60 * 1000;

      let monthDayNum;
      if (weekDay == 5 || weekDay == 6) {
        monthDayNum = 42;
      } else {
        monthDayNum = 35;
      }

      for (let i = 0; i < monthDayNum; i++) {
        calendatArr.push({
          date: new Date(startTime + i * 24 * 60 * 60 * 1000),
          year: year,
          month: month + 1,
          day: new Date(startTime + i * 24 * 60 * 60 * 1000).getDate(),
          clickDay: false
        });
      }
      this.headOptions.date = `${utils.englishMonth(month)} ${year}`;
      return calendatArr;
    }
  },
  methods: {
    // 是否是当前月
    isCurrentMonth(date) {
      let { year: currentYear, month: currentMonth } = utils.getNewDate(
        utils.getDate(this.time.year, this.time.month, 1)
      );
      let { year, month } = utils.getNewDate(date);
      return currentYear == year && currentMonth == month;
    },
    // 是否是今天
    isCurrentDay(date) {
      let {
        year: currentYear,
        month: currentMonth,
        day: currentDay
      } = utils.getNewDate(new Date());
      let { year, month, day } = utils.getNewDate(date);
      return currentYear == year && currentMonth == month && currentDay == day;
    },
    // 上一个月
    handlePrevMonth() {
      let prevMonth = utils.getDate(this.time.year, this.time.month, 1);
      prevMonth.setMonth(prevMonth.getMonth() - 1);
      this.time = utils.getNewDate(prevMonth);
      this.headOptions.date = `${utils.englishMonth(this.time.month)} ${
        this.time.year
      }`;
      console.log(`${utils.englishMonth(this.time.month)} ${this.time.year}`);
      this.$emit("handlePrevMonth");
    },
    // 下一个月
    handleNextMonth() {
      let nextMonth = utils.getDate(this.time.year, this.time.month, 1);
      nextMonth.setMonth(nextMonth.getMonth() + 1);
      this.time = utils.getNewDate(nextMonth);
      this.headOptions.date = `${utils.englishMonth(this.time.month)} ${
        this.time.year
      }`;
      this.$emit("handleNextMonth");
    },
    // 点击回到今天
    handleToday() {
      this.time = utils.getNewDate(new Date());
      this.returnDate();
      this.$emit("handleToday");
    },
    // 点击某一天
    handleClickDay(item) {
      this.$forceUpdate();
      this.$emit("handleClickDay", item);
      this.calendarList.map(x => {
        x.clickDay = false;
      });
      this.$set(item, "clickDay", true);
      this.xzMonth = item.month;
      this.xzDay = item.day;
      console.log(this.xzDay);
    }
  },
  created() {
    this.calendarList = this.visibleCalendar;
    this.calendarType = this.options.calendarType;
  }
};
</script>

<style lang="less">
.cc-calendar {
  padding: 23px 30px 30px;
  width: 100%;
  height: 100%;
  background: #f9fafc;
  box-sizing: border-box;
  .calendar-week {
    width: 100%;
    height: 46px;
    line-height: 46px;
    border: 1px solid #e4e7ea;
    border-right: none;
    .week-item {
      float: left;
      width: 14.285%;
      text-align: center;
      font-size: 14px;
      color: #424953;
      border-right: 1px solid #e4e7ea;
      font-weight: 600;
    }
  }
  .calendar-view {
    width: 100%;
    border-left: 1px solid #e4e7ea;
    .date-view {
      float: left;
      width: 14.285%;
      height: 120px;
      border-right: 1px solid #e4e7ea;
      border-bottom: 1px solid #e4e7ea;
      cursor: pointer;
      .date-day {
        padding: 8px 8px 0;
        display: block;
        width: 100%;
        font-size: 14px;
        color: #7f8794;
      }
      .calendar-num {
        margin-top: 6px;
        display: block;
        width: 100%;
        text-align: center;
        font-size: 30px;
        color: #424953;
      }
    }
    .opacity-class {
      opacity: 0.5;
    }
    .month-class {
      background-image: linear-gradient(
        45deg,
        rgba(000, 000, 000, 0.03) 25%,
        transparent 25%,
        transparent 50%,
        rgba(000, 000, 000, 0.03) 50%,
        rgba(000, 000, 000, 0.03) 75%,
        transparent 75%,
        transparent
      );
      background-size: 20px 20px;
    }
    .todayBg {
      background: #fffdef;
    }
    .handleDay {
      background: #2061ff !important;
      .date-day {
        color: #bccfff !important;
      }
      .calendar-num {
        color: #fff !important;
      }
    }
  }
}
.mn-cont {
  overflow: hidden;
}
.mn-left {
  width: 0.58rem;
  height: 0.58rem;
  float: left;
  border-radius: 50%;
}
.mn-left img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  margin-right: 0.14rem;
}
.mn-center {
  float: left;
}
.mn-center p {
  line-height: 0.4rem;
  font-size: 0.3em;
}
.mn-center p .bor {
  width: 0.61rem;
  height: 0.23rem;
  line-height: 0.23rem;
  font-size: 0.2rem;
  vertical-align: middle;
  border: solid 0.01rem #ed4023;
  box-sizing: border-box;
}
.mn-center p .nam {
  font-size: 0.3rem;
}
.mn-right {
  float: right;
}
.mn-right p {
  margin-right: 0.2rem;
  width: 1.2rem;
  height: 0.45rem;
  box-sizing: border-box;
  border: solid 0.01rem transparent;
  background-image: linear-gradient(to right, #fb774e, #ed4023);
  line-height: 0.45rem;
  text-align: center;
  border-radius: 0.1rem;
  color: #fff;
}
</style>