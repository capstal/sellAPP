<template>
  <div class="ratings" v-el:ratings>
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <span class="title">综合评分</span>
          <span class="rank">高于周边商家{{seller.rankRate}}%</span>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size='36' :score='seller.serviceScore'></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size='36' :score='seller.foodScore'></star>
            <span class="score">{{seller.foodScore}}</span>
            <span class="title no-margin">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type='selectType' :only-content='onlyContent' :desc='desc' :ratings='ratings'></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-show=rateShow(rating.rateType,rating.text) v-for='rating in ratings' class='rating-item'>
            <div class="avatar">
              <img height='28' width='28' :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.name}}</h1>
              <div class="star-wrapper">
                <star :size='24' :score='rating.score'></star>
                <span class="delivery" v-show='rating.deliveryTime'>{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show='rating.recommend.length && rating.recommend'>
                <i class='icon-thumb_up'></i>
                <span v-for='text in rating.recommend' class='item'>{{text}}</span>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type= 'text/ecmascript-6'>
import star from "components/star/star";
import split from "components/split/split";
import ratingselect from "components/ratingselect/ratingselect";
import BScroll from "better-scroll";
import {formatDate} from 'common/js/date'

const ERR_OK = 0;
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
  data() {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: "全部",
        positive: "满意",
        negative: "不满意"
      }
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  filters: {
    formatDate(time) {
      let date = new Date(time);
      return formatDate(date, "yyyy-MM-dd hh:mm");
    }
  },
  created() {
    let _this = this;
    this.$http.get("/api/ratings").then(response => {
      response = response.body;
      if (response.errno === ERR_OK) {
        this.ratings = response.data;
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$els.ratings, {
            click: true
          });
        });
      }
    });
  },
  components: {
    star,
    split,
    ratingselect
  },
  methods: {
    rateShow(type, text) {
      if (this.onlyContent && !text) {
        return false;
      }
      if (this.selectType === ALL) {
        return true;
      } else {
        return type === this.selectType;
      }
    },
    change(item) {
      this.selectType = item;
      this.$nextTick(() => {
        this.scroll.refresh();
      });
    },
    showOnly(onlyContent) {
      this.onlyContent = onlyContent
      this.$nextTick(() => {
        this.scroll.refresh();
      });
    }
  }
};
</script>

<style lang= 'stylus' rel='stylesheet/stylus'>
@import '../../common/stylus/mixin'

.ratings
  position: absolute
  padding: 6px 0
  top: 174px
  bottom: 0
  left: 0
  width: 100%
  overflow: hidden
  .overview
    display: flex
    .overview-left
      flex: 0 0 36.7%
      padding: 18px 0 0 0
      width: 36.7%
      text-align: center
      border-right: 1px solid rgba(7, 17, 27, 0.1)
      font-size: 0
      .score
        margin-bottom: 6px
        line-height: 28px
        font-size: 24px
        color: rgb(255, 153, 0)
      .title
        display: inline-block
        margin-bottom: 8px
        line-height: 12px
        font-size: 12px
        color: rgb(7, 17, 27)
      .rank
        display: inline-block
        line-height: 10px
        font-size: 10px
        color: rgb(147, 153, 159)
    .overview-right
      flex: 1
      padding: 18px 24px 0 24px
      @media only screen and (max-width: 320px)
        padding: 18px 0 0 6px
      .score-wrapper
        font-size: 0
        .title
          display: inline-block
          vertical-align: top
          line-height: 18px
          font-size: 12px
          color: rgb(7, 17, 27)
          margin-bottom: 8px
          &.no-margin
            margin-bottom: 0
        .star
          display: inline-block
          vertical-align: top
          margin: 0 12px
        .score
          display: inline-block
          vertical-align: top
          line-height: 18px
          font-size: 12px
          color: rgb(255, 153, 0)
        .delivery
          display: inline-block
          vertical-align: top
          margin-left: 12px
          font-size: 12px
          line-height: 18px
          color: rgb(147, 153, 159)
  .rating-wrapper
    padding: 0 18px
    .rating-item
      display: flex
      padding: 18px 0
      border-1px(rgba(7, 17, 27, 0.1))
      .avatar
        flex: 0 0 28px
        width: 28px
        margin-right: 12px
        img
          border-radius: 50%
      .content
        position: relative
        flex: 1
        .name
          margin-bottom: 4px
          line-height: 12px
          font-size: 10px
          color: rgb(7, 17, 27)
        .star-wrapper
          margin-bottom: 6px
          font-size: 0
          .star
            display: inline-block
            vertical-align: top
            margin-right: 6px
          .delivery
            display: inline-block
            vertical-align: top
            line-height: 12px
            font-size: 10px
            color: rgb(147, 153, 159)
        .text
          margin-bottom: 12px
          line-height: 18px
          color: rgb(7, 17, 27)
          font-size: 12px
          font-weight: 400
        .recommend
          line-height: 16px
          .icon-thumb_up, .item
            display: inline-block
            margin: 0 8px 4px 0
            font-size: 9px
          .icon-thumb_up
            color: rgb(0, 160, 220)
          .item
            padding: 0 6px
            border: 1px solid rgba(7, 17, 27, 0.1)
            border-radius: 1px
            color: rgb(147, 153, 159)
            background: #fff
        .time
          position: absolute
          top: 0
          right: 0
          line-height: 12px
          font-size: 10px
          color: rgb(147, 153, 159)
</style>