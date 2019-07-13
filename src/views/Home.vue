<template>
  <div id="home">
    <section id="tomato-left">
      <div class="tomato-image">
        <div class="sound-block">
          <div>RINTONES</div>
          <div @click="isSong = 'none'">
            <span :class="{'span-active': isSong === 'none'}"></span>
          NONE</div>
          <div @click="isSong = 'ring'">
            <span :class="{'span-active': isSong === 'ring'}"></span>RING
          </div>
          <div @click="isSong = 'beep'">
            <span :class="{'span-active': isSong === 'beep'}"></span>BEEP
          </div>
          <div @click="isSong = 'chicken'">
            <span :class="{'span-active': isSong === 'chicken'}"></span>CHICKEN
          </div>
        </div>
      </div>
      <div class="tomato-chart">
        <bar-chart :width="200" :height="290" style="margin-top:70px"></bar-chart>
      </div>
    </section>
    <section id="tomato-center">
      <div class="tomato-bridge">
        <div>
          <img :src="closeBtn" v-show="!isStart" @click="resetTiming" />
          <div>{{times.minutes}}:{{times.seconds}}</div>
          <img :src="statusButton" @click="statusClick" alt />
        </div>
      </div>
    </section>
    <section id="tomato-right">
      <div class="tomato-image">
        <div class="total-tomato">
          <div>TOTAL</div>
          <div>TODAY</div>
          <div>{{tomatoLength}}</div>
          <div>/TOMATO</div>
          <div></div>
          <div>WEEK</div>
          <div>{{tomatoLength}}</div>
          <div>/TOMATO</div>
        </div>
      </div>
      <div class="tomato-list">
        <div>TO-DO LIST</div>
        <div>
          <input type="text" v-model="listInput" />
          <div @click="addList">＋</div>
        </div>
        <div class="list-wrap">
          <div v-for="(item, index) in listStorage" :key="index">
            <div>{{item.text}}</div>
            <div :class="item.status === 'never' ? 'pt-28' : ''" @click="chooseType(item)">
              <i class="icon-check" v-show="item.status === 'isDone'"></i>
              <i class="icon-cancel" v-show="item.status === 'isFail'"></i>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Vue from 'vue';
import { Bar } from 'vue-chartjs';
import play from '../assets/icon_play.svg';
import close from '../assets/icon_close.svg';
import pause from '../assets/icon_pause.svg';

let interval;

export default {
  data() {
    return {
      closeBtn: close,
      isStart: false,
      listInput: '',
      listStorage: [],
      times: {
        minutes: '25',
        seconds: '00',
      },
      local: 0,
      stop: 0,
      obj: {},
      isSong: 'none',
    };
  },
  methods: {
    chooseType(s) {
      if (s.status === 'never' || s.status === 'isFail') {
        /* eslint-disable */
        s.status = 'isDone';
      } else if (s.status === 'isDone') {
        s.status = 'isFail';
      }
    },
    statusClick() {
      this.isStart = !this.isStart;
      let y = 'in';
      y = this.isStart ? 'in' : 'out';
      switch (y) {
        case 'in':
          this.stop = 0;
          this.momentDuration();
          break;
        case 'out':
          localStorage.clear();           
          localStorage.setItem('times', JSON.stringify(this.times));
          this.clear();
          return;
         break;
      }
    },
    addList() {
      this.listStorage.push({ text: this.listInput, status: 'never' });
      this.listInput = '';
    },
    momentDuration() { 
      let local = localStorage.getItem('times');
      let min;
      let sec;
      if (local !== null) {
        let time = JSON.parse(local);
        min = time.minutes;
        sec = time.seconds;  
      } else {
        min = 25;
        sec = 60;
      }
      interval = setInterval(() => {
        if (this.stop > 0) {
          this.clear();
          return;
        }
        sec = sec < 10 ? '0' + sec : sec;
        min = min < 10 ? '0' + min : min;
        min === 25 ? min-- : '';
        if (sec <= 0) {
          min--;
          sec = 60;
        }
        if (min === 0 & sec === 0) {
          this.clear();
          return;
        }
        if (this.local > 0) {
          min = 24;
          sec = 60;
        }
        sec--;
        this.times.minutes = min;
        this.times.seconds = sec;
        this.local = 0;
      }, 1000);
    },
    clear() {
      this.stop++;
      clearInterval(interval);
    },
    resetTiming() {
      localStorage.clear();  
      this.local++;
      this.times.minutes = '25';
      this.times.seconds = '00';  
      this.clear();
    },
  },
  computed: {
    statusButton() {
      return this.isStart ? pause : play;
    },
    tomatoLength() {
      return this.listStorage.filter(i => {
        return i.status === 'isDone';
      }).length;
    },
  },
  mounted() {
    this.resetTiming();  
  },
};
Vue.component('bar-chart', {
  extends: Bar,
  props: {
    today: {
      type: String,
    },
  },
  data() {
    return {
      datacollection: {
        labels: [
          '7/1',
          '7/2',
          '7/3',
          '7/4',
          '7/5',
          '7/6',
          '7/7',
        ],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: ['#FFFFFF', '#FFFFFF', '#FFFFFF', '#FFFFFF', '#FFFFFF', '#FFFFFF', '#FFFFFF'],
            data: [10, 20, 15, 5, 15, 25, 10],
          },
        ],
      },
      options: {
        scales: {
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
                fontSize: 18,
              },
              gridLines: {
                display: false,
                color: '#707070',
                width: 10,
                // lineWidth: 4,
              },
            },
          ],
          xAxes: [
            {
              ticks: {
                beginAtZero: true,
                fontSize: 18,
              },
              gridLines: {
                display: false,
                color: '#707070',
                // lineWidth: 4,
                offsetGridLines: true,
              },
              barPercentage: 0.5,
            },
          ],
        },
        legend: {
          display: false,
        },
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  created() {
    const index = this.datacollection.labels.findIndex(item => item === '7/3');
    if (index) {
      this.datacollection.datasets[0].backgroundColor.forEach((item, j, arr) => {
        if (index === j) {
          /* eslint-disable */
          arr[index] = '#F25044';
        }
      });
    }
  },
  mounted() {
    this.renderChart(this.datacollection, this.options);
  },
});
</script>

<style lang="scss" scoped>
$yellow: #ffc66f;
$white: #ffffff;
$black: #393939;
$black-2: #494949;
$gray: #707070;
$pink: #ffe9dc;
$red: #b72212;

@mixin size($w, $h) {
  width: $w;
  height: $h;
}
@mixin bg($image) {
  background-image: url($image);
}
@mixin flex-col($align) {
  display: flex;
  flex-direction: column;
  align-items: $align;
}
@mixin abs($top, $left) {
  transform: translate(-50%, -50%);
  position: absolute;
  left: $left;
  top: $top;
}

#home {
  display: flex;
  justify-content: space-between;
  padding: 20px 20px 0 20px;
  background-color: $pink;
  width: 1280px;
  #tomato-left {
    @include flex-col(flex-end);
    width: calc(100% / 3);
    z-index: 1;
    .tomato-image {
      @include size(339px, 408px);
      @include bg("../assets/tomato_left.svg");
      margin-right: 20px;
      .sound-block {
        text-align: left;
        color: $pink;
        font-weight: bold;
        font-size: 24px;
        width: 160px;
        margin: 106px auto 0 auto;
        & > div {
          margin-bottom: 8px;
          position: relative;
          padding-left: 41.33px;
          font-family: Segoe UI;
          cursor: pointer;
          .span-active {
            background-color: $yellow;
          }
          & > span {
            display: inline-block;
            margin-top: 5px;
            @include size(22.67px, 22.67px);
            border-radius: 50%;
            border: solid 4px $white;
            background-color: $white;
            vertical-align: middle;
            position: absolute;
            left: 8px;
          }
        }
        & > div:nth-child(1) {
          font-size: 32px;
          margin-bottom: 20px;
          padding-left: 0;
          cursor: auto;
          &::before {
            display: none;
          }
        }
      }
    }
    .tomato-chart {
      @include size(380px, 380px);
      border-radius: 45px;
      background-color: $yellow;
      box-shadow: 3px 3px 6px rgba(0, 0, 0, 0.16);
      margin-top: -61px;
    }
  }
  #tomato-right {
    @include flex-col(flex-start);
    width: calc(100% / 3);
    z-index: 1;
    .tomato-image {
      @include size(339px, 408px);
      @include bg("../assets/tomato_right.svg");
      margin-left: 20px;
      .total-tomato {
        width: 294px;
        margin: 106px auto 0 auto;
        text-align: center;
        font-weight: bold;
        color: $white;
        & > div:nth-child(1) {
          font-size: 32px;
          margin-bottom: 7px;
        }
        & > div:nth-child(2) {
          font-size: 18px;
          padding-right: 179.38px;
        }
        & > div:nth-child(3) {
          font-size: 54px;
          margin-top: -23px;
        }
        & > div:nth-child(4) {
          font-size: 18px;
          margin-top: -36px;
          padding-left: 191px;
        }
        & > div:nth-child(5) {
          width: 100%;
          height: 2px;
          background-color: $white;
          margin-top: 11.5px;
        }
        & > div:nth-child(6) {
          font-size: 18px;
          padding-right: 179.38px;
          margin-top: 18.5px;
        }
        & > div:nth-child(7) {
          font-size: 54px;
          margin-top: -23px;
        }
        & > div:nth-child(8) {
          font-size: 18px;
          margin-top: -40px;
          padding-left: 191px;
        }
      }
    }
    .tomato-list {
      @include size(380px, 380px);
      border-radius: 45px;
      background-color: $white;
      box-shadow: 3px 3px 6px rgba(0, 0, 0, 0.16);
      margin-top: -61px;
      padding: 11px 21.5px 25.11px 21.5px;
      & > div:first-child {
        font-size: 32px;
        color: $black;
        text-align: center;
        font-weight: bold;
        margin-bottom: 3px;
      }
      & > div:nth-child(2) {
        position: relative;
        margin-bottom: 15px;
        width: 328px;
        & > input {
          @include size(328px, 48px);
          border: solid 1px $gray;
          border-radius: 15px;
          font-size: 24px;
          padding: 12.75px 14.8px;
          &:focus {
            outline: none;
          }
        }
        & > div:nth-child(2) {
          @include abs(50%, 92.2%);
          font-size: 34px;
          color: $gray;
          cursor: pointer;
          &:active {
            transform: translate(-50%, -50%) scale(0.7);
          }
        }
      }
      .list-wrap {
        width: 100%;
        overflow-y: auto;
        max-height: 250px;
        &::-webkit-scrollbar {
          width: 5px;
          height: 5px;
        }
        &::-webkit-scrollbar-thumb {
          background-color: $gray;
          border-top: 0;
          border-bottom: 0;
          border-radius: 14px;
          border-left: 7px solid $gray;
          border-right: 7px solid $gray;
        }
        &::-webkit-scrollbar-track {
          background-color: rgba(0, 0, 0, 0.16);
          border-left: 11px solid rgba(0, 0, 0, 0.16);
          border-right: 11px solid rgba(0, 0, 0, 0.16);
        }
        & > div {
          display: flex;
          align-items: center;
          margin-bottom: 16px;
          & > div:first-child {
            padding: 0 11.5px 7px 11.5px;
            border-bottom: solid 1px $gray;
            width: 265px;
            font-size: 20px;
            color: $black-2;
            font-style: oblique;
          }
          .pt-28 {
            padding-top: 28px !important;
          }
          & > div:last-child {
            width: 50px;
            padding: 0 11.5px 7px 11.5px;
            border-bottom: solid 1px $gray;
            margin-left: 10px;
            font-size: 20px;
            color: $gray;
            cursor: pointer;
          }
        }
      }
    }
  }
  #tomato-center {
    position: relative;
    width: calc(100% / 3);
    .tomato-bridge {
      @include size(579.43px, 418.81px);
      @include bg("../assets/tomato_center.svg");
      @include abs(48.3%, 50%);
      margin: 0 auto;
      z-index: 0;
      & > div:first-child {
        @include abs(59%, 50.3%);
        @include flex-col(center);
        justify-content: center;
        display: flex;
        position: relative;
        & > img {
          cursor: pointer;
          &:active {
            transform: scale(0.7);
          }
        }
        & > img:first-child {
          position: absolute;
          top: -20px;
        }
        & > div:nth-child(2) {
          font-size: 76px;
          text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.16);
          color: $red;
          margin-bottom: 16px;
        }
      }
    }
  }
}
</style>
